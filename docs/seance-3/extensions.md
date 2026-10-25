# Extensions

*25 minutes.* Claude Code s'étend par plusieurs mécanismes. Ils se ressemblent de loin ; la question utile est toujours : **quand ce mécanisme agit-il, et est-ce garanti ?**

## Choisir le bon mécanisme

| Mécanisme | Ce que c'est | Quand il agit | Garanti ? | Exemple |
|---|---|---|---|---|
| **CLAUDE.md** | Instructions en Markdown | Chargé à chaque session | Non : le modèle essaie de suivre | « Lancer `make test` avant de commiter » |
| **Skill** | Procédure en Markdown, avec fichiers annexes | Sur `/nom`, ou quand Claude la juge pertinente | Non | `/parite`, `/signalement` (séance 2) |
| **Sous-agent** | Un agent spécialisé, avec son contexte, ses outils, son prompt | Quand Claude lui délègue, ou à votre demande | Non | Un relecteur de sécurité en lecture seule |
| **Hook** | Une commande shell déclenchée par un événement | À chaque événement visé : avant ou après un outil, en fin de tour… | **Oui** | Formater chaque fichier modifié ; bloquer l'écriture dans `donnees/` |
| **Serveur MCP** | Un connecteur vers un outil externe | Quand Claude appelle ses outils | Non | GitHub, une base de données, un outil maison |
| **Plugin** | Un paquet qui regroupe les éléments précédents | À l'installation | Selon son contenu | Partager votre configuration avec vos collègues |

!!! tip "À retenir"
    Ce qui **doit** arriver passe par un hook ou une permission. Ce qui **devrait** arriver passe par CLAUDE.md, une skill ou un sous-agent.

## Les sous-agents

Un sous-agent est un fichier Markdown dans `.claude/agents/`. Son en-tête fixe son nom, sa description (qui sert à Claude pour décider de lui déléguer), ses outils et son modèle. Le corps devient son prompt système.

```markdown title=".claude/agents/relecteur-securite.md"
---
name: relecteur-securite
description: Relit un diff à la recherche de failles de sécurité (injection, XSS, secrets, entrées non validées, comportements indéfinis en C++). À utiliser avant toute fusion.
tools: Read, Grep, Glob, Bash
model: sonnet
---

Tu es un relecteur de sécurité exigeant. On te donne un ensemble de changements.

Pour chaque problème trouvé, indique : le fichier et la ligne, la gravité
(critique, majeure, mineure), un scénario d'exploitation concret, et le correctif.

Ne signale que ce que tu peux justifier par un scénario. Ne modifie aucun fichier.
```

Trois raisons d'utiliser un sous-agent :

- **Préserver le contexte** : il lit cinquante fichiers, vous ne récupérez que son résumé.
- **Spécialiser** : un prompt et des outils restreints pour une tâche précise.
- **Paralléliser** : plusieurs sous-agents peuvent travailler en même temps (voir [Parallélisation et revue](parallele-revue.md)).

On l'invoque en le nommant : « Demande au relecteur-securite d'examiner la branche seance-3-contribution. »

Claude Code fournit aussi des sous-agents intégrés, dont **Explore**, rapide et en lecture seule, qu'il utilise de lui-même pour explorer un dépôt.

## Les hooks

Un hook est une commande shell exécutée **à coup sûr** à un moment précis : avant un outil (`PreToolUse`), après un outil (`PostToolUse`), quand Claude a fini (`Stop`), au démarrage d'une session (`SessionStart`), quand Claude attend votre réponse (`Notification`), etc. Le hook reçoit en entrée standard un JSON qui décrit l'événement. Avec le code de sortie **2**, il bloque l'action, et son message d'erreur est renvoyé à Claude.

Exemple pour le dépôt d'exercices : vérifier la syntaxe de chaque fichier PHP modifié et formater chaque fichier C++ modifié.

```json title=".claude/settings.json"
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          { "type": "command", "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/verifier.sh" }
        ]
      }
    ]
  }
}
```

```bash title=".claude/hooks/verifier.sh"
#!/usr/bin/env bash
fichier=$(jq -r '.tool_input.file_path')
case "$fichier" in
  *.php)
    if ! sortie=$(php -l "$fichier" 2>&1); then
      echo "$sortie" >&2
      exit 2          # Claude reçoit l'erreur et doit corriger
    fi ;;
  *.cpp|*.hpp)
    clang-format -i "$fichier" ;;
esac
exit 0
```

Rendez le script exécutable (`chmod +x .claude/hooks/verifier.sh`). La commande `/hooks` liste les hooks chargés.

!!! warning "Piège"
    Un hook est un programme qui s'exécute sur votre machine avec vos droits. Relisez les hooks d'un dépôt que vous clonez, comme vous reliriez un script d'installation.

## Les serveurs MCP

MCP (*Model Context Protocol*) est un protocole ouvert pour brancher des outils externes sur un agent. Un serveur MCP expose des outils ; Claude Code les appelle comme ses outils natifs.

```bash
# Un serveur distant, ici celui de GitHub
claude mcp add --transport http github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer VOTRE_JETON"

# La liste des serveurs et leur état
claude mcp list
```

Dans une session, `/mcp` affiche les serveurs connectés. Une configuration partagée avec le projet se range dans `.mcp.json`, à la racine du dépôt.

Exemples d'usages pour un enseignant-chercheur : interroger une base de données de TP, lire des tickets GitHub ou GitLab, piloter un navigateur pour tester une application web d'étudiant.

!!! warning "Piège"
    Un serveur MCP tiers voit ce que Claude lui envoie, et ses réponses entrent dans le contexte de Claude. N'installez que des serveurs de confiance.

## Exercice

!!! example "Exercice : outiller le dépôt d'exercices"
    1. Créez le sous-agent `relecteur-securite` ci-dessus. Il servira dans la partie [revue de code](parallele-revue.md).
    2. Installez le hook de vérification. Demandez ensuite à Claude d'introduire volontairement une erreur de syntaxe dans un fichier PHP : observez le blocage, puis la correction.
    3. Ajoutez un hook `PreToolUse` qui interdit toute modification dans `donnees/`.

??? success "Solution de la question 3"
    ```json
    {
      "hooks": {
        "PreToolUse": [
          {
            "matcher": "Edit|Write",
            "hooks": [
              {
                "type": "command",
                "command": "jq -r '.tool_input.file_path' | grep -q '/donnees/' && { echo 'Les données de référence sont en lecture seule.' >&2; exit 2; } || exit 0"
              }
            ]
          }
        ]
      }
    }
    ```

    Une règle de permission `"deny": ["Edit(./donnees/**)"]` donne le même résultat plus simplement. Le hook devient utile quand la décision demande un calcul : le contenu du fichier, l'heure, l'état de git…

## Pour aller plus loin

- [Sous-agents](https://code.claude.com/docs/en/sub-agents)
- [Guide des hooks](https://code.claude.com/docs/en/hooks-guide)
- [MCP dans Claude Code](https://code.claude.com/docs/en/mcp)
- [Plugins](https://code.claude.com/docs/en/plugins/overview)
