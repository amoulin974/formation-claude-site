# Développer avec Claude

Formation à l'usage d'un agent de développement (Claude Code) pour des enseignants en informatique.

## Présentation

| | |
|---|---|
| **Public** | Enseignants en informatique, docteurs, très à l'aise avec le terminal et git |
| **Point de départ** | Usage de l'IA limité au chat (copier-coller de code) |
| **Format** | 3 séances de 2 h, en présentiel, avec travail intersession |
| **Environnement** | VS Code, Dev Container, extension Claude Code |
| **Langages des exercices** | C++ et PHP (un même énoncé dans les deux langages) |

**Fil rouge :** passer du chat, où l'IA ne voit que ce qu'on lui colle, à l'agent, qui lit le code, le modifie, exécute les tests et itère, tout en gardant un regard critique d'enseignant et de chercheur.

## Objectifs généraux

À l'issue de la formation, le participant sera capable de :

- comprendre suffisamment le fonctionnement d'un LLM et d'un agent pour en anticiper les forces et les échecs ;
- utiliser Claude Code au quotidien sur ses propres projets, avec une méthode de travail rigoureuse ;
- configurer et étendre l'agent pour ses besoins ;
- adapter ses enseignements et ses évaluations à un monde où les étudiants disposent de ces outils.

**Objectif transversal :** porter un regard critique et argumenté sur ce qu'un agent peut ou ne peut pas faire de manière fiable.

## Séance 1 : comprendre, puis prendre en main

| Durée | Séquence |
|---|---|
| 20 min | LLM, le strict nécessaire : tokens, fenêtre de contexte, génération probabiliste, hallucinations |
| 15 min | Du chat à l'agent : la boucle lire, agir, observer, itérer. Démo comparative |
| 25 min | Panorama des outils et de leurs interactions (CLI, VS Code, desktop, cloud, API) |
| 45 min | TP : explorer un dépôt inconnu, corriger un bug, relire le diff, commiter |
| 15 min | CLAUDE.md et permissions |

À l'issue de la séance, le participant sera capable de :

1. Expliquer le fonctionnement d'un LLM : tokens, fenêtre de contexte, génération probabiliste, origine des hallucinations.
2. Distinguer un assistant conversationnel d'un agent : boucle outil, observation, itération, accès au système de fichiers et au terminal.
3. Situer les différentes surfaces de Claude (chat, CLI, extension VS Code, desktop, cloud, API) et choisir celle qui convient à une tâche.
4. Expliquer comment l'extension VS Code et la CLI partagent configuration, historique et intégration à l'éditeur.
5. Distinguer ce qui est versionné avec le projet de ce qui reste local au poste, et mettre en place une stratégie de continuité entre deux postes.
6. Installer et configurer Claude Code dans VS Code, et en paramétrer les permissions.
7. Utiliser l'agent pour explorer et comprendre une base de code inconnue.
8. Faire corriger un bug par l'agent, puis relire le diff et le valider via git.
9. Rédiger un premier CLAUDE.md décrivant les conventions d'un projet.

**Travail intersession :** utiliser Claude Code sur un de ses vrais projets (cours, recherche, outil personnel) et noter ce qui a marché ou échoué.

## Séance 2 : développer sérieusement avec Claude

| Durée | Séquence |
|---|---|
| 15 min | Retours d'expérience sur le travail intersession |
| 45 min | Méthode : plan mode, critères d'acceptation, tests d'abord, petits incréments, gestion du contexte |
| 45 min | TP : fonctionnalité en TDD sur une base existante, puis passation à un binôme sur un autre poste |
| 15 min | Prompting efficace et pièges du code généré |

À l'issue de la séance, le participant sera capable de :

1. Faire valider une approche par le plan mode avant toute écriture de code.
2. Formuler une demande avec contexte, contraintes et critères d'acceptation vérifiables.
3. Conduire un développement en TDD : faire écrire les tests d'abord, avancer par petits incréments.
4. Gérer le contexte d'une session : `/clear`, `/compact`, reprise de session.
5. Identifier les défauts typiques du code généré : code plausible mais faux, dette technique, failles de sécurité, fuite de secrets.
6. Créer une commande personnalisée ou une skill pour une tâche récurrente.

**Travail intersession :** écrire un CLAUDE.md réel et une skill pour un de ses projets.

## Séance 3 : aller plus loin et enseigner avec

| Durée | Séquence |
|---|---|
| 45 min | Extensions : skills, sous-agents, MCP, hooks ; un agent minimal avec l'API |
| 20 min | Parallélisation et revue de code par agents |
| 45 min | Atelier pédagogique : TP, évaluations, cadre d'usage pour les étudiants |
| 10 min | Synthèse et ressources pour continuer |

À l'issue de la séance, le participant sera capable de :

1. Décrire le rôle des skills, sous-agents, serveurs MCP et hooks, et choisir le mécanisme adapté à un besoin.
2. Construire un agent minimal avec l'API et l'usage d'outils.
3. Paralléliser des tâches et organiser une revue de code par agents.
4. Analyser l'effet des agents sur ses propres TP et évaluations.
5. Concevoir une activité ou une évaluation qui porte sur le raisonnement plutôt que sur le code produit.
6. Définir un cadre d'usage de l'IA pour ses étudiants, intégrité académique comprise.

## Supports d'exercices

Un même domaine métier, la **gestion de tournoi** (équipes, poules, calendrier des matchs, classement), implémenté en C++ et en PHP. Les deux projets partagent un cœur algorithmique commun, mais chacun garde les enjeux propres à son langage.

**Partie commune** (énoncés identiques dans les deux langages)

- Génération du calendrier en round-robin
- Classement avec critères de départage
- Phase finale à élimination directe

**Spécificités par langage**

| | C++ | PHP |
|---|---|---|
| Forme | Bibliothèque et outil en ligne de commande | Application web PHP 8 sans framework, base de données via PDO |
| Build et tests | CMake, GoogleTest, sanitizers (ASan, UBSan) | Composer, PHPUnit |
| Enjeux du langage | Gestion mémoire et RAII, comportement indéfini, performance sur un tournoi de 10 000 équipes, templates | Inscriptions et résultats via une page ou une API, validation des entrées, sécurité web |
| Pièges volontaires | Fuite mémoire, comportement indéfini caché, algorithme quadratique | Injection SQL, faille XSS, entrée non validée |
| Communs aux deux | Code dupliqué, fonction trop longue, cas limite non testé | Idem |
| Taille visée | 1 500 à 2 500 lignes | 1 500 à 2 500 lignes |

Les deux projets vivent dans un dépôt unique, cloné une fois et ouvert dans un Dev Container.

## Prérequis

**Effectif :** 8 à 15 participants, sur postes personnels ou en salle de TP.

**Participants**

- Un abonnement Claude personnel donnant accès à Claude Code (à souscrire avant la première séance)
- Un compte GitHub
- Sur poste personnel : VS Code et Docker installés, ou Codespaces à défaut
- Le dépôt d'exercices cloné et le Dev Container testé avant la première séance

**Salle de TP**

- Postes équipés de Docker et de VS Code avec l'extension Dev Containers
- Images Docker du Dev Container préchargées, pour ne pas saturer le réseau en début de séance
- Connexion réseau fiable (accès à l'API Claude et à GitHub)
- Vidéoprojecteur pour les démos

!!! tip "Postes partagés de la salle de TP"
    L'historique des sessions et la mémoire automatique de Claude Code restent sur le poste. Sur un poste de salle, chacun pousse son travail et ses notes sur GitHub en fin de séance, et se déconnecte de son compte Claude.

## Évaluation

- Retour à chaud à la fin de chaque séance
- Livrable final : un dépôt personnel contenant un CLAUDE.md, une skill, et une note courte sur ce qui a marché, ce qui a échoué et une idée d'intégration dans ses enseignements
- Questionnaire de satisfaction après la séance 3

!!! warning "À compléter avant publication"
    - Dates et lieu des trois séances
    - Validation du domaine métier et de la répartition commun / spécifique des exercices
