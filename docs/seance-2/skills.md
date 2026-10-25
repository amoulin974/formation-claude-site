# Commandes et skills

*À faire en fin de séance ou en intersession.* Quand vous tapez trois fois la même demande, transformez-la en **skill**.

## Ce qu'est une skill

Une skill est un dossier contenant un fichier `SKILL.md` : des instructions en Markdown, précédées d'un en-tête YAML. Elle s'utilise de deux façons :

- **par vous**, comme une commande : `/nom-de-la-skill` ;
- **par Claude**, qui la charge de lui-même quand votre demande correspond à sa description.

Contrairement à `CLAUDE.md`, chargé à chaque session, une skill n'occupe le contexte que lorsqu'elle sert. C'est la bonne place pour une **procédure** en plusieurs étapes.

| Emplacement | Portée |
|---|---|
| `.claude/skills/<nom>/SKILL.md` | Le projet, partagé via git |
| `~/.claude/skills/<nom>/SKILL.md` | Vous, tous vos projets |

Les anciennes commandes personnalisées (`.claude/commands/<nom>.md`) fonctionnent toujours et produisent aussi `/<nom>`. Les skills sont recommandées pour tout nouveau travail.

## L'en-tête

| Champ | Rôle |
|---|---|
| `name` | Nom de la commande (par défaut, le nom du dossier) |
| `description` | Ce que fait la skill et quand l'utiliser. C'est ce que Claude lit pour décider de la charger : soignez-la |
| `argument-hint` | Indication affichée pour les arguments, par exemple `[fichier.csv]` |
| `disable-model-invocation` | `true` : seule vous pouvez la lancer. Utile pour les actions à effets de bord |
| `allowed-tools` | Outils utilisables sans demander la permission pendant la skill |

Dans le corps, `$ARGUMENTS` est remplacé par ce que vous tapez après le nom de la commande.

## Exemple : vérifier la parité C++/PHP

Dans le dépôt d'exercices, le comportement doit rester identique dans les deux langages. Une skill peut encoder cette vérification.

```markdown title=".claude/skills/parite/SKILL.md"
---
name: parite
description: Vérifie qu'une fonctionnalité se comporte de la même façon en C++ et en PHP. À utiliser après toute modification de comportement dans cpp/ ou php/.
argument-hint: "[fonctionnalité]"
allowed-tools: Bash(make test*) Bash(cmake *) Bash(ctest *)
---

Vérifie la parité entre `cpp/` et `php/` pour : $ARGUMENTS

1. Repère l'implémentation et les tests de cette fonctionnalité dans les deux langages.
2. Compare les cas testés. Dresse la liste des cas présents d'un côté et absents de l'autre.
3. Compare les messages d'erreur et les cas limites (liste vide, doublons, valeurs négatives).
4. Lance `make test`.
5. Si l'outil en ligne de commande expose la fonctionnalité, exécute la même commande
   dans les deux langages sur `donnees/resultats-2026.csv` et compare les sorties.

Termine par un tableau : cas, C++, PHP, écart. Ne modifie aucun fichier :
propose les corrections, sans les appliquer.
```

Utilisation :

```text
/parite classement
```

## Exercice

!!! example "Exercice : votre première skill"
    1. Créez la skill `parite` ci-dessus dans votre fork du dépôt d'exercices, et lancez `/parite phase finale` sur votre travail du TP 2.
    2. Créez une seconde skill, `/signalement`, qui transforme un signalement de bug (passé en argument) en un test de non-régression qui échoue, **sans corriger** le bug. Testez-la sur le signalement du TP 1, à partir d'un état antérieur au correctif.
    3. Committez vos skills : elles font désormais partie du projet.

??? success "Une version possible de /signalement"
    ```markdown title=".claude/skills/signalement/SKILL.md"
    ---
    name: signalement
    description: Transforme un signalement de bug en test de non-régression qui échoue, sans corriger le bug.
    argument-hint: "[description du bug]"
    disable-model-invocation: true
    ---

    Signalement : $ARGUMENTS

    1. Reproduis le problème avec l'outil en ligne de commande ou un appel direct.
       Si tu n'arrives pas à le reproduire, arrête-toi et dis-le.
    2. Identifie le plus petit jeu de données qui montre le problème.
    3. Écris un test qui exprime le comportement **attendu** sur ce jeu de données,
       en C++ et en PHP, en suivant les conventions des tests existants.
    4. Lance les tests et montre qu'ils échouent pour la bonne raison.
    5. Ne modifie pas le code de production. Termine par ton hypothèse sur la cause.
    ```

    `disable-model-invocation: true` évite que Claude déclenche cette skill de lui-même au milieu d'une autre tâche.

## Pour aller plus loin

- [Documentation des skills](https://code.claude.com/docs/en/skills)
