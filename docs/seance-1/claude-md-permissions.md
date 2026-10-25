# CLAUDE.md et permissions

*15 minutes.* Deux mécanismes complémentaires pour encadrer l'agent : `CLAUDE.md` **oriente** ce qu'il fait, les permissions **limitent** ce qu'il peut faire.

## CLAUDE.md : la mémoire du projet

Chaque session démarre avec un contexte vide. `CLAUDE.md` est un fichier Markdown que Claude Code charge automatiquement au début de chaque session. On y écrit ce qu'on devrait sinon réexpliquer à chaque fois.

| Portée | Emplacement | Partagé avec |
|---|---|---|
| Projet | `./CLAUDE.md` ou `./.claude/CLAUDE.md` | L'équipe, via git |
| Personnel pour ce projet | `./CLAUDE.local.md` | Vous seul |
| Personnel, tous projets | `~/.claude/CLAUDE.md` | Vous seul |

Les fichiers se cumulent, ils ne s'écrasent pas. Un `CLAUDE.md` placé dans un sous-dossier est chargé quand Claude travaille dans ce sous-dossier.

### Quoi y mettre

- Les **commandes** : build, tests, lint, lancement.
- Les **conventions** qui ne se devinent pas en lisant le code.
- Les **règles de travail** : « toute correction de bug s'accompagne d'un test de non-régression ».
- Les **pièges connus** du projet.

### Quoi éviter

- Ce que Claude trouve seul en lisant le code : arborescence, liste des dépendances.
- Les consignes vagues. « Écris du code propre » n'aide pas ; « fonctions de moins de 40 lignes, pas de `new` nu en C++ » aide.
- Les fichiers trop longs : visez moins de 200 lignes. Au-delà, l'adhésion baisse.

!!! tip "À retenir"
    Ajoutez une ligne à `CLAUDE.md` chaque fois que vous corrigez Claude deux fois sur le même point.

### Créer et vérifier

- `/init` analyse le dépôt et propose un premier `CLAUDE.md`, à relire et à élaguer.
- `/memory` liste les fichiers d'instructions chargés et permet de les ouvrir.
- `/context` montre ce qui occupe le contexte, y compris ces fichiers.
- Vous pouvez aussi simplement demander : « Ajoute à CLAUDE.md que… »

!!! warning "Piège"
    `CLAUDE.md` est du contexte, pas une contrainte. Claude essaie de le suivre, sans garantie. Pour **interdire** une action, utilisez les permissions.

## Les permissions

Les permissions sont appliquées par Claude Code lui-même, pas par le modèle. Une règle de refus bloque l'action quoi que le modèle décide.

### Les modes

| Mode | Valeur | Ce qui s'exécute sans vous demander |
|---|---|---|
| Manual | `default` | Les lectures. Demande avant chaque modification, commande ou accès réseau |
| Edit automatically | `acceptEdits` | Lectures, modifications de fichiers et commandes de fichiers courantes dans le dossier de travail |
| Plan | `plan` | Lectures et exploration. Aucune modification avant que vous approuviez un plan |
| Auto | `auto` | Tout, un modèle classificateur examinant chaque action pour sa sûreté |

Dans le terminal, ++shift+tab++ fait défiler les modes. Dans VS Code, cliquez sur l'indicateur de mode sous la zone de saisie.

!!! tip "Pour cette séance"
    Travaillez en mode **Manual** : chaque modification passe par un diff que vous acceptez ou refusez. C'est plus lent, mais c'est ainsi qu'on apprend ce que fait l'agent.

### Les règles

Les règles s'écrivent dans un fichier de réglages, sous trois listes : `allow` (autoriser sans demander), `ask` (toujours demander) et `deny` (interdire). L'ordre d'évaluation est fixe : **deny, puis ask, puis allow**. Un refus l'emporte toujours.

| Fichier | Portée |
|---|---|
| `~/.claude/settings.json` | Vous, tous vos projets |
| `.claude/settings.json` | Le projet, partagé via git |
| `.claude/settings.local.json` | Vous, ce projet seulement |

Exemple pour le dépôt d'exercices :

```json title=".claude/settings.json"
{
  "permissions": {
    "allow": [
      "Bash(make test*)",
      "Bash(cmake *)",
      "Bash(ctest *)",
      "Bash(git status)",
      "Bash(git diff *)"
    ],
    "ask": [
      "Bash(git push *)"
    ],
    "deny": [
      "Read(./.env)",
      "Read(./secrets/**)"
    ]
  }
}
```

La commande `/permissions` affiche les règles en vigueur et le fichier d'où vient chacune. Quand Claude vous demande une autorisation, l'option « ne plus demander » ajoute une règle pour vous.

## Exercice

!!! example "Exercice : outiller le dépôt d'exercices"
    *À faire après le TP 1, dans le dépôt d'exercices.*

    1. Demandez à Claude d'ajouter à `CLAUDE.md` la règle suivante : toute correction de bug s'accompagne d'un test de non-régression, dans les deux langages.
    2. Créez `.claude/settings.json` pour autoriser sans confirmation la compilation et les tests, et exiger une confirmation pour `git push`.
    3. Ouvrez une nouvelle session et vérifiez avec `/memory` et `/permissions` que tout est pris en compte.
    4. Demandez à Claude de lancer les tests : il ne doit plus vous demander l'autorisation.

??? success "Solution"
    Ligne ajoutée à la section « Conventions » de `CLAUDE.md` :

    ```markdown
    - Toute correction de bug s'accompagne d'un test de non-régression, en C++ et en PHP.
    ```

    `.claude/settings.json` :

    ```json
    {
      "permissions": {
        "allow": ["Bash(make test*)", "Bash(cmake *)", "Bash(ctest *)"],
        "ask": ["Bash(git push *)"]
      }
    }
    ```

    Committez les deux fichiers : ils font partie du projet et profitent à toute personne qui le clone.

## Pour aller plus loin

- [Mémoire et fichiers CLAUDE.md](https://code.claude.com/docs/en/memory)
- [Modes de permission](https://code.claude.com/docs/en/permission-modes)
- [Règles de permission](https://code.claude.com/docs/en/permissions)
