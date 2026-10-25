# Panorama des outils

*25 minutes.* Claude se présente sous plusieurs formes. Elles reposent sur les mêmes modèles, mais n'ont ni le même accès à votre travail, ni les mêmes usages.

## Les surfaces

| Surface | Ce qu'elle voit | Usage typique |
|---|---|---|
| **Chat** (claude.ai, applications) | Ce que vous lui donnez : messages, fichiers joints, connecteurs | Réfléchir, rédiger, expliquer un concept, analyser un document |
| **Claude Code en CLI** (`claude` dans un terminal) | Le dossier où vous le lancez, le terminal | Développer, automatiser, scripter (`claude -p`) |
| **Extension VS Code** | Le dossier ouvert, l'éditeur, votre sélection | Développer avec des diffs visuels et un panneau de conversation |
| **Application desktop**, onglet Code | Un dossier local, ou un dépôt dans le cloud | Comme l'extension, hors de l'éditeur, plusieurs sessions en parallèle |
| **Claude Code dans le cloud** (claude.ai/code, mobile) | Un dépôt GitHub cloné dans une machine virtuelle distante | Lancer une tâche longue et la suivre depuis n'importe quel appareil |
| **API** | Ce que votre programme lui envoie | Construire vos propres outils et agents (séance 3) |

Les quatre lignes du milieu sont **le même agent**, Claude Code, derrière des interfaces différentes.

!!! tip "À retenir"
    Le chat convient quand le contexte tient dans un message. Dès que la réponse dépend de plusieurs fichiers ou doit être vérifiée en exécutant quelque chose, passez à Claude Code.

## Claude Code et VS Code

C'est la combinaison utilisée dans cette formation.

- **L'extension embarque sa propre copie de la CLI** pour son panneau de conversation. Elle n'installe pas la commande `claude` dans votre terminal : pour cela, il faut [installer la CLI](https://code.claude.com/docs/en/setup) séparément. Dans le Dev Container de la formation, les deux sont déjà installées.
- **Elles partagent la configuration** : `~/.claude/settings.json` (permissions, serveurs MCP, hooks), vos fichiers `CLAUDE.md` et les plugins.
- **Elles partagent l'historique** : une conversation commencée dans le panneau se reprend dans le terminal avec `claude --resume`, et inversement depuis l'historique des sessions de l'extension.
- **La CLI se branche sur l'éditeur** : lancée dans le terminal intégré de VS Code, elle se connecte à un petit serveur local fourni par l'extension. C'est ce qui lui permet d'ouvrir ses diffs dans l'éditeur, de connaître votre sélection et de lire les erreurs du panneau *Problèmes*. Depuis un terminal externe, la commande `/ide` établit ce lien.

| | Extension (panneau) | CLI (terminal) |
|---|---|---|
| Diffs | Visuels, acceptables modification par modification | Dans le terminal, ou dans VS Code si connectée |
| Contexte de l'éditeur | Fichier ouvert et sélection, automatiquement | Via le serveur local de l'extension |
| Commandes `/` | Un sous-ensemble | Toutes |
| Raccourci `!` pour lancer une commande shell | Non | Oui |

## Ce qui suit le projet, ce qui reste sur le poste

C'est la question clé quand on travaille sur plusieurs machines, ou en salle de TP.

| Élément | Emplacement | Voyage avec le dépôt git ? |
|---|---|---|
| Instructions du projet | `CLAUDE.md`, `.claude/rules/` | **Oui** |
| Réglages partagés du projet | `.claude/settings.json` | **Oui** |
| Skills et agents du projet | `.claude/skills/`, `.claude/agents/` | **Oui** |
| Vos instructions personnelles pour ce projet | `CLAUDE.local.md` (à mettre dans `.gitignore`) | Non |
| Vos réglages personnels pour ce projet | `.claude/settings.local.json` | Non |
| Vos instructions et réglages globaux | `~/.claude/CLAUDE.md`, `~/.claude/settings.json` | Non |
| Historique des conversations | `~/.claude/projects/` | Non |
| Mémoire automatique | `~/.claude/projects/<projet>/memory/` | Non |

La **mémoire automatique** mérite une mention : Claude Code y note de lui-même ce qu'il apprend de vos corrections et préférences. Elle est propre à la machine. La commande `/memory` permet de la consulter et de la modifier.

!!! warning "Piège"
    Sur un poste partagé de salle de TP, l'historique et la mémoire automatique restent sur la machine pour le prochain utilisateur. En fin de séance : poussez votre travail et déconnectez-vous (`/logout`).

## Changer de poste sans perdre le fil

Quatre stratégies, de la plus simple à la plus outillée :

1. **Tout ce qui compte passe par git.** Avant de quitter un poste, demandez à Claude de résumer l'état du travail dans un fichier du dépôt (par exemple `NOTES.md` ou un plan dans `docs/`), committez et poussez. Sur l'autre poste, ouvrez une nouvelle session : « Lis NOTES.md et reprends. » C'est la méthode la plus robuste.
2. **Vos réglages personnels dans un dépôt de dotfiles.** Versionnez `~/.claude/CLAUDE.md`, `~/.claude/settings.json` et vos skills personnelles, et clonez-les sur chaque machine.
3. **Travailler dans le cloud.** `claude --cloud "tâche"` lance la tâche dans une machine virtuelle distante, qui clone votre dépôt depuis GitHub. Vous la suivez depuis claude.ai/code ou le mobile. Pour la reprendre dans un terminal, `claude --teleport` récupère la conversation et la branche, à condition d'utiliser le même compte, un clone du même dépôt, une branche poussée et un répertoire de travail propre.
4. **Piloter à distance.** Avec Remote Control (`/remote-control`), la session continue de tourner sur la machine d'origine, et vous la pilotez depuis un autre appareil, navigateur ou téléphone.

!!! tip "À retenir"
    `--resume` reprend une conversation **de cette machine**. `--teleport` reprend une session **du cloud**. Pour passer d'une machine locale à une autre, rien ne remplace un commit et une note de passation.

## Démo

1. Dans le panneau Claude Code de VS Code, demander : « Explique-moi l'organisation de ce dépôt en cinq lignes. »
2. Ouvrir le terminal intégré et lancer `claude --resume` : retrouver la conversation et la poursuivre.
3. Ouvrir `/memory` et montrer où se trouvent `CLAUDE.md` et la mémoire automatique.
4. Montrer `git status` : rien de cette conversation n'est dans le dépôt.

## Pour aller plus loin

- [Claude Code dans VS Code](https://code.claude.com/docs/en/vs-code)
- [Mémoire et fichiers CLAUDE.md](https://code.claude.com/docs/en/memory)
- [Claude Code dans le cloud](https://code.claude.com/docs/en/claude-code-on-the-web)
- [Remote Control](https://code.claude.com/docs/en/remote-control)
