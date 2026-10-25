# Séance 2 : développer sérieusement avec Claude

En séance 1, vous avez vu ce que l'agent sait faire. Cette séance porte sur la **méthode** : comment obtenir un travail juste, vérifiable et maintenable, plutôt qu'un code plausible.

## Déroulé

| Durée | Séquence | Page |
|---|---|---|
| 15 min | Retours sur le travail intersession | ci-dessous |
| 45 min | La méthode : planifier, spécifier, tester, avancer par petits pas, gérer le contexte | [Méthode de travail](methode.md) |
| 45 min | TP : une fonctionnalité en TDD, puis passation à un binôme | [TP 2 : phase finale en TDD](tp-tdd.md) |
| 15 min | Prompting efficace et pièges du code généré | [Pièges et prompting](pieges.md) |

À faire après la séance : [Commandes et skills](skills.md) et le [travail intersession](intersession.md).

## Objectifs

À l'issue de la séance, vous serez capable de :

1. Faire valider une approche par le plan mode avant toute écriture de code.
2. Formuler une demande avec contexte, contraintes et critères d'acceptation vérifiables.
3. Conduire un développement en TDD : faire écrire les tests d'abord, avancer par petits incréments.
4. Gérer le contexte d'une session : `/clear`, `/compact`, reprise de session.
5. Identifier les défauts typiques du code généré : code plausible mais faux, dette technique, failles de sécurité, fuite de secrets.
6. Créer une commande personnalisée ou une skill pour une tâche récurrente.

## Retours sur le travail intersession (15 min)

Tour de table rapide, deux minutes par personne au plus, à partir de votre journal :

- **Une réussite** : la tâche où l'agent vous a fait gagner le plus de temps.
- **Un échec** : ce qui s'est passé, et votre hypothèse sur la cause.

L'animateur classe les échecs au tableau dans quatre colonnes, qui serviront de fil conducteur pour la suite :

| Contexte insuffisant | Demande ambiguë | Pas de moyen de vérifier | Tâche trop grosse |
|---|---|---|---|
| L'agent ne connaissait pas une convention, une version, une contrainte | Il a fait autre chose que ce que vous aviez en tête | Il a affirmé que c'était juste, sans pouvoir le prouver | Il s'est perdu, a tout réécrit, ou a oublié une partie |

!!! tip "À retenir"
    La plupart des échecs relèvent de ces quatre causes. La méthode de cette séance répond à chacune : CLAUDE.md et exploration pour le contexte, plan mode et critères d'acceptation pour l'ambiguïté, tests pour la vérification, petits incréments pour la taille.

??? note "Notes pour l'animateur"
    - Ne pas laisser le tour de table déborder : il nourrit la suite, il ne la remplace pas.
    - Si un échec relève d'une vraie limite du modèle (raisonnement, connaissance absente), le dire franchement : tout ne se règle pas par la méthode.
    - Pour le TP, constituer les binômes à l'avance et vérifier que chacun a son fork du dépôt d'exercices, avec le correctif du TP 1.
