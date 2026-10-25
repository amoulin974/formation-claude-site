# Ce qu'est un LLM

*20 minutes.* L'objectif n'est pas de faire un cours d'apprentissage automatique, mais de donner juste assez de mécanique pour anticiper les forces et les échecs de l'outil.

## Un prédicteur du token suivant

Un grand modèle de langage (LLM) est un réseau de neurones de type *transformer*. Il reçoit une séquence de **tokens** et produit une **distribution de probabilité** sur le token suivant. On tire un token dans cette distribution, on l'ajoute à la séquence, et on recommence.

- **Token** : un fragment de texte, souvent un morceau de mot. `genererRoundRobin` fait plusieurs tokens. Le code, les identifiants rares et les langues autres que l'anglais en consomment davantage.
- **Génération probabiliste** : la même question peut produire deux réponses différentes. Ce n'est pas un défaut, c'est le principe.
- **Pas d'exécution** : le modèle ne lance pas le code qu'il écrit. Il produit un texte *plausible*. Pour savoir si ce texte est *juste*, il faut l'exécuter : c'est ce qu'apportera l'agent.

!!! tip "À retenir"
    Un LLM optimise la plausibilité, pas la vérité. Tout ce qu'il produit est une hypothèse tant qu'elle n'a pas été vérifiée.

## Comment il a appris

1. **Pré-entraînement** : prédire le token suivant sur un très grand corpus (web, livres, code). Le modèle y acquiert la langue, les connaissances, les idiomes de programmation.
2. **Post-entraînement** : affinage sur des démonstrations et par apprentissage par renforcement, à partir de retours humains et de tâches vérifiables. Le modèle y apprend à suivre des instructions, à utiliser des outils et à refuser certaines demandes.

Conséquence pratique : les connaissances du modèle s'arrêtent à une **date de coupure**. Une bibliothèque sortie depuis, ou une API qui a changé, lui est inconnue, sauf si on la lui montre.

## La fenêtre de contexte

Le modèle n'a pas de mémoire entre deux appels. Tout ce qu'il « sait » de votre situation tient dans la **fenêtre de contexte** : la séquence de tokens qu'on lui envoie à chaque appel. Elle se compte en centaines de milliers de tokens (de l'ordre de 200 000, jusqu'à un million selon les modèles et les offres).

Dans un agent comme Claude Code, le contexte contient à chaque instant :

- les instructions système de l'outil ;
- vos fichiers d'instructions (`CLAUDE.md`, voir [CLAUDE.md et permissions](claude-md-permissions.md)) ;
- toute la conversation : vos messages, les réponses, les fichiers lus, les sorties des commandes.

Quand le contexte se remplit, l'outil **compacte** la conversation : il la remplace par un résumé. Des détails se perdent.

!!! tip "À retenir"
    Le contexte est **la** ressource à gérer. Un contexte encombré (logs interminables, fichiers inutiles, tâches mélangées) dégrade les réponses. On y reviendra en séance 2 avec `/clear` et `/compact`.

## Les hallucinations

Une hallucination est une production plausible mais fausse : une fonction qui n'existe pas, une option de compilation inventée, un article cité à tort. Elle découle directement de ce qui précède : le modèle complète avec ce qui *ressemble* à la bonne réponse.

Elles sont plus fréquentes quand :

- l'information est rare dans le corpus (bibliothèque peu utilisée, version récente) ;
- l'information n'est pas dans le contexte alors qu'elle devrait y être (le modèle devine au lieu de lire) ;
- la question présuppose quelque chose de faux (« pourquoi la fonction `X` renvoie-t-elle… » alors que `X` n'existe pas).

!!! warning "Piège"
    La fluidité n'est pas un indicateur de fiabilité. Une réponse fausse est rédigée avec la même assurance qu'une réponse juste.

Le remède n'est pas de « mieux demander », mais de **faire vérifier** : compiler, exécuter les tests, lire la documentation réelle. Un chat ne peut pas le faire seul. Un agent, si.

## Pour aller plus loin

- [Comment fonctionne Claude Code](https://code.claude.com/docs/en/how-claude-code-works) : la boucle agentique, les outils, le contexte.
- [La fenêtre de contexte dans Claude Code](https://code.claude.com/docs/en/context-window) : ce qui l'occupe, ce qui survit à la compaction.
