# Parallélisation et revue de code

*20 minutes.* Quand l'agent travaille seul, vous pouvez en lancer plusieurs. Et un agent qui relit le code d'un autre, ou le vôtre, est l'un des usages les plus rentables.

## Paralléliser

| Moyen | Ce qui est isolé | Usage typique |
|---|---|---|
| **Sous-agents** dans une session | Le contexte de chacun | Investigations ou revues en parallèle : « Examine en parallèle, avec trois sous-agents, la sécurité, la performance et les tests. » |
| **Worktrees** : `claude --worktree nom` | Les fichiers et la branche | Deux tâches de développement en même temps sur le même dépôt, sans qu'elles se marchent dessus |
| **Sessions dans le cloud** : `claude --cloud "tâche"` | Une machine distante entière | Tâches longues, à suivre depuis le navigateur ou le téléphone |
| **Application desktop** | Une session par onglet, en worktree si besoin | Piloter plusieurs sessions visuellement |

Un worktree git est un second répertoire de travail, sur sa propre branche, qui partage l'historique du dépôt. `claude --worktree phase-finale` crée `.claude/worktrees/phase-finale/` sur une nouvelle branche et y démarre Claude. Lancez la même commande avec un autre nom dans un second terminal : vous avez deux agents qui travaillent en parallèle.

!!! warning "Piège"
    Paralléliser multiplie aussi le travail de **relecture**. Deux agents qui produisent chacun trois cents lignes, c'est six cents lignes à relire. Commencez par paralléliser ce qui ne produit pas de code : recherche, analyse, revue.

## Faire relire du code par des agents

### La commande intégrée

`/code-review` lance une relecture des changements de votre branche par rapport à sa branche amont, dans un sous-agent en arrière-plan. Les résultats arrivent dans la conversation.

```text
/code-review                               # branche courante et changements en cours
/code-review main...seance-3-contribution  # une branche par rapport à une autre
/code-review php/src                       # un dossier
```

Par défaut, elle ne fait que signaler. Avec `--fix`, elle applique aussi ses corrections, en respectant vos permissions.

### Des relecteurs spécialisés

Vos propres sous-agents (voir [Extensions](extensions.md#les-sous-agents)) permettent d'orienter la relecture : sécurité, performance, qualité des tests… et de les lancer en parallèle.

## Exercice : la contribution à relire

!!! example "Exercice (15 min)"
    La branche `seance-3-contribution` du dépôt d'exercices contient une contribution : des statistiques de saison en C++ et une page web des résultats en PHP. Ses tests passent. Elle contient **au moins dix défauts**.

    Récupérez d'abord la branche :

    ```bash
    git fetch origin
    git switch seance-3-contribution
    ```

    1. **Vous d'abord (5 min).** Sans agent, lisez le diff (`git diff main...seance-3-contribution`) et notez les défauts que vous trouvez.
    2. **Les agents ensuite (5 min).** Dans une nouvelle session :

        ```text
        Relis la contribution de la branche seance-3-contribution par rapport à main.
        Lance en parallèle trois sous-agents : sécurité, justesse et comportements
        indéfinis, performance et maintenabilité. Fusionne leurs résultats dans un
        tableau trié par gravité. Ne modifie rien.
        ```

        Puis comparez avec `/code-review main...seance-3-contribution`.
    3. **Le bilan (5 min).** Comparez votre liste, celle des agents et la liste de référence ci-dessous. Comptez :
        - les défauts trouvés par vous seul, par les agents seuls, par les deux ;
        - les **faux positifs** des agents : des problèmes signalés qui n'en sont pas.

??? success "Liste de référence des défauts"
    **C++ : `cpp/src/statistiques.cpp`**

    | # | Défaut | Conséquence |
    |---|---|---|
    | 1 | `butsParMatch = total / static_cast<int>(resultats.size())` : division **entière** | 7 buts en 2 matchs donnent 3 au lieu de 3,5. Le test passe car il utilise un total divisible |
    | 2 | Même ligne, avec une liste vide : division entière par zéro | Comportement indéfini, en pratique un plantage |
    | 3 | `classement[0]`, `butsMarques[0]` sur un classement vide | Comportement indéfini (UBSan le signale) |
    | 4 | `new int[]` puis `throw` sur score négatif, avant les `delete[]` | **Fuite mémoire** (LeakSanitizer la signale). Un `std::vector` règle la question |
    | 5 | `equipeLaPlusProlifique` prend le classement **par valeur** et renvoie une `string_view` sur ses éléments | **Vue pendante**, lecture après libération dès que le résultat est conservé dans une variable (AddressSanitizer la signale) |
    | 6 | `contientDoublons` : double boucle | **Quadratique** : 10 000 équipes, environ 50 millions de matchs, donc de l'ordre de 10<sup>15</sup> comparaisons. Un `std::set` de paires suffit |
    | 7 | Tableaux `butsMarques` et `butsEncaisses` qui recopient le classement | Duplication inutile : les lignes du classement contiennent déjà ces valeurs |
    | 8 | Tests sans cas limites (liste vide, score négatif, moyenne non entière) | Les défauts 1 à 5 passent inaperçus |

    **PHP : `php/src/DepotResultats.php` et `php/public/index.php`**

    | # | Défaut | Conséquence |
    |---|---|---|
    | 9 | `deLEquipe` concatène `$equipe` dans la requête SQL | **Injection SQL** : `x' OR '1'='1` renvoie toute la table |
    | 10 | `$equipe` affiché tel quel dans le titre et le champ de recherche | **XSS réfléchie** |
    | 11 | Noms d'équipes affichés sans `htmlspecialchars`, dans les liens, le tableau et le classement | **XSS stockée** : une équipe nommée `<script>…</script>` attaque chaque visiteur |
    | 12 | Jeton administrateur écrit en dur dans le code | **Secret versionné** : quiconque lit le dépôt peut vider la saison |
    | 13 | Aucune validation du formulaire : champs absents (erreur 500), score négatif, `"abc"` converti silencieusement en 0, équipe contre elle-même, nom vide | Données corrompues |
    | 14 | Formulaires POST sans protection CSRF | Un site tiers peut ajouter des résultats ou vider la saison au nom d'un administrateur connecté |
    | 15 | Base SQLite créée dans `donnees/`, à l'intérieur du dépôt | Risque de committer la base, et `donnees/` sert de référence aux TP |
    | 16 | Tests sans aucun cas hostile ou invalide | Les défauts 9 à 13 passent inaperçus |

## Pour aller plus loin

- [Exécuter des agents en parallèle](https://code.claude.com/docs/en/agents)
- [Worktrees](https://code.claude.com/docs/en/worktrees)
- [Relecture locale avec /code-review](https://code.claude.com/docs/en/code-review-local)
