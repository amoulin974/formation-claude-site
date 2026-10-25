# Atelier pédagogique

*45 minutes, en groupes de trois ou quatre.* Vous avez apporté un de vos TP. Vous allez mesurer ce qu'un agent en fait, puis le repenser.

Le constat de départ est simple : la plupart des TP de programmation des premières années sont résolus par un agent en quelques minutes, tests compris. Interdire l'outil ne suffit pas à rendre l'exercice formateur ; ignorer l'outil non plus. Il s'agit de décider **ce que l'on veut que l'étudiant apprenne**, et de construire l'activité en conséquence.

## Étape 1 : le diagnostic (10 min)

Chacun soumet son TP à Claude Code, comme le ferait un étudiant pressé : l'énoncé tel quel, sans aide.

Notez dans la grille :

| Question | Votre TP |
|---|---|
| Temps de résolution par l'agent | |
| La solution est-elle correcte ? Complète ? | |
| Qu'aurait **appris** un étudiant qui a seulement relu et rendu cette solution ? | |
| Quelle part de l'énoncé l'agent n'a-t-il **pas** pu faire seul (choix de conception, justification, données absentes, matériel) ? | |
| Quelle compétence visée par le TP est contournée ? | |

!!! tip "À retenir"
    Le bon indicateur n'est pas « l'agent sait-il le faire ? », mais « qu'est-ce que l'étudiant n'a plus besoin de faire, et est-ce précisément ce qu'on voulait lui apprendre ? »

## Étape 2 : concevoir (25 min)

Choisissez **un** TP du groupe et transformez-le à l'aide d'un ou plusieurs leviers.

### Les leviers

| Levier | Principe | Exemple sur le projet tournoi |
|---|---|---|
| **Évaluer le raisonnement** | Le rendu porte sur les choix et leur justification, pas seulement sur le code | « Expliquez pourquoi le départage par confrontation directe pose problème à trois équipes à égalité, et ce que vous avez décidé » |
| **Expliquer un diff** | L'étudiant présente oralement un diff, le sien ou celui d'un agent, et répond aux questions | Soutenance de cinq minutes sur le correctif du TP 1 |
| **Relire du code généré** | L'étudiant reçoit du code produit par un agent, avec des défauts, et doit les trouver | La branche `seance-3-contribution`, adaptée au niveau |
| **Déboguer** | Partir d'un programme qui échoue, plutôt que d'une page blanche | Le signalement du TP 1 |
| **Spécifier avant de coder** | L'étudiant écrit les critères d'acceptation et les tests ; l'agent, éventuellement, le code | TP 2, avec la spécification à écrire par l'étudiant |
| **Contexte que l'agent n'a pas** | L'activité dépend de données, de contraintes ou d'échanges propres au cours | Un « client » joué par l'enseignant, qui répond aux questions de spécification |
| **Progression sans, puis avec** | Les fondamentaux sans outil, puis le même type de tâche avec l'outil, et une comparaison | Écrire le round-robin à la main, puis le faire générer et comparer les deux |
| **Traçabilité** | L'usage de l'agent est autorisé mais documenté : demandes, choix, corrections | Un journal joint au rendu, comme le travail intersession de cette formation |

### La fiche à produire

Remplissez cette fiche pour le TP retenu.

```markdown
# Fiche activité

**TP d'origine :** …
**Ressource ou SAÉ, semestre :** …
**Compétence visée :** …

## Diagnostic
- Ce que l'agent fait seul : …
- Ce qui est contourné : …

## Nouvelle activité
- Leviers utilisés : …
- Déroulé (étapes, durées) : …
- Usage de l'IA : interdit / encadré / libre, pour chaque étape : …

## Évaluation
- Ce qui est évalué : …
- Comment (rendu, oral, revue, test sur table) : …
- Ce qui distingue un étudiant qui a compris d'un étudiant qui a recopié : …

## Risques et limites
- …
```

## Étape 3 : restitution (10 min)

Chaque groupe présente sa fiche en deux minutes. Les autres groupes jouent l'étudiant malin : comment contournerait-on cette activité ?

## Un cadre d'usage pour vos étudiants

Les étudiants ont besoin de règles **claires, explicites et spécifiques à chaque activité**. Un même module peut combiner plusieurs niveaux. Voici une base à adapter, à discuter dans votre équipe pédagogique.

| Niveau | Usage de l'IA | Exemple d'activité | Ce que l'étudiant doit fournir |
|---|---|---|---|
| **0. Sans IA** | Interdit | Contrôle sur table, exercices de fondamentaux | Rien de plus |
| **1. IA comme tuteur** | Explications, questions, pas de code produit pour l'étudiant | TP d'apprentissage d'un concept nouveau | Déclaration d'usage |
| **2. IA comme assistant** | Génération autorisée, l'étudiant reste responsable de chaque ligne | Projet, SAÉ | Journal d'usage et soutenance sur le code |
| **3. IA comme sujet d'étude** | L'usage de l'agent est l'objet même de l'activité | Revue de code généré, comparaison d'approches | Analyse critique |

Points d'attention :

- **Responsabilité** : l'étudiant répond de tout ce qu'il rend, quelle qu'en soit l'origine. Ne pas savoir expliquer une ligne de son propre rendu est un problème, en soi.
- **Transparence plutôt que détection** : les outils de détection de texte ou de code généré ne sont pas fiables. Une déclaration d'usage, assortie d'un oral, l'est davantage.
- **Équité** : tous les étudiants n'ont pas accès aux mêmes outils ou abonnements. Une activité qui suppose un outil payant doit prévoir une alternative.
- **Données** : les étudiants ne doivent pas soumettre de données personnelles, ni de code soumis à un accord de confidentialité (stages, alternance), à un service externe.
- **Cohérence d'équipe** : un même niveau doit vouloir dire la même chose d'un enseignant à l'autre. Le cadre gagne à être discuté et adopté en équipe pédagogique, puis affiché dans chaque énoncé.

!!! example "Pour l'intersession qui suit la formation"
    Proposez votre fiche activité, et un projet de cadre d'usage pour votre ressource, à l'équipe pédagogique de votre département.
