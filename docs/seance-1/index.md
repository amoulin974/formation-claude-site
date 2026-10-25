# Séance 1 : comprendre, puis prendre en main

Cette séance pose le modèle mental : ce qu'est un LLM, ce qu'un agent ajoute, et comment les outils Claude s'articulent. Vous prenez ensuite l'agent en main sur un dépôt que vous ne connaissez pas.

## Déroulé

| Durée | Séquence | Page |
|---|---|---|
| 20 min | LLM, le strict nécessaire | [Ce qu'est un LLM](llm.md) |
| 15 min | Du chat à l'agent, avec démo | [Du chat à l'agent](chat-agent.md) |
| 25 min | Panorama des outils et de leurs interactions | [Panorama des outils](panorama.md) |
| 45 min | TP : explorer un dépôt inconnu et corriger un bug | [TP 1 : premier bug](tp-premier-bug.md) |
| 15 min | CLAUDE.md et permissions | [CLAUDE.md et permissions](claude-md-permissions.md) |

Après la séance : [travail intersession](intersession.md).

## Objectifs

À l'issue de la séance, vous serez capable de :

1. Expliquer le fonctionnement d'un LLM : tokens, fenêtre de contexte, génération probabiliste, origine des hallucinations.
2. Distinguer un assistant conversationnel d'un agent : boucle outil, observation, itération, accès au système de fichiers et au terminal.
3. Situer les différentes surfaces de Claude (chat, CLI, extension VS Code, desktop, cloud, API) et choisir celle qui convient à une tâche.
4. Expliquer comment l'extension VS Code et la CLI partagent configuration, historique et intégration à l'éditeur.
5. Distinguer ce qui est versionné avec le projet de ce qui reste local au poste, et mettre en place une stratégie de continuité entre deux postes.
6. Installer et configurer Claude Code dans VS Code, et en paramétrer les permissions.
7. Utiliser l'agent pour explorer et comprendre une base de code inconnue.
8. Faire corriger un bug par l'agent, puis relire le diff et le valider via git.
9. Rédiger un premier CLAUDE.md décrivant les conventions d'un projet.

## Avant de venir

Vérifiez la [checklist des prérequis](../ressources/prerequis.md). Le jour J, le dépôt d'exercices doit s'ouvrir dans son Dev Container et `make test` doit passer.

??? note "Notes pour l'animateur"
    - Projeter la démo « chat puis agent » en direct : l'effet est plus fort qu'une capture.
    - Pendant le TP, circuler et repérer les participants qui acceptent les modifications sans lire le diff. On en reparlera en séance 2.
    - Garder 5 minutes à la fin du TP pour que deux ou trois personnes montrent comment l'agent a trouvé le bug : les chemins diffèrent souvent.
