# TP 1 : explorer un dépôt inconnu et corriger un bug

*45 minutes.* Vous rejoignez un projet que vous n'avez jamais vu. Un utilisateur signale un bug. Votre mission : comprendre le projet, reproduire le bug, le faire corriger proprement par l'agent, et valider le correctif.

Choisissez **un** langage, C++ ou PHP. Les onglets de cette page s'adaptent à votre choix.

## Le signalement

> **Classement faux pour la saison 2026**
>
> Avec le fichier `donnees/resultats-2026.csv`, Biarritz est classé 2e devant Bayonne. Les deux équipes ont 10 points, mais Bayonne a gagné 3-0 à Anglet et 4-0 à Pau. Sa différence de buts devrait être nettement meilleure que celle de Biarritz. Pourtant le classement affiche **-1** pour Bayonne.

## Préparation (5 min)

1. Ouvrez le dépôt `formation-claude-exercices` dans VS Code, puis **Reopen in Container**.
2. Dans un terminal, vérifiez que tout est vert :

    === "C++"

        ```bash
        make test-cpp
        ```

    === "PHP"

        ```bash
        make test-php
        ```

3. Ouvrez le panneau Claude Code (icône en forme d'étincelle) et passez en mode **Manual**.

!!! question "Première observation"
    Les tests passent, et pourtant il y a un bug. Qu'est-ce que cela vous dit des tests existants ?

## Étape 1 : explorer sans rien modifier (10 min)

Vous ne connaissez pas ce code. Utilisez l'agent comme un collègue qui l'aurait lu pour vous. Exemples de demandes :

```text
Explique-moi ce que fait ce dépôt et comment il est organisé, en dix lignes maximum.
```

```text
Comment est calculé le classement ? Quels sont les critères de départage, et dans quel ordre ?
```

```text
Quels tests couvrent le classement ? Qu'est-ce qui n'est pas testé ?
```

Observez dans le panneau les **outils** utilisés par l'agent : lecture de fichiers, recherche, commandes. Il ne devine pas : il va lire.

!!! warning "Piège"
    Ne demandez pas encore de corriger. Un agent à qui l'on dit « corrige le classement » sans contexte risque de modifier la mauvaise chose avec beaucoup d'assurance.

**Critère de réussite :** vous savez expliquer, sans regarder l'écran, où se trouve le calcul du classement et quels critères il applique.

## Étape 2 : reproduire (5 min)

Avant de corriger, on reproduit. Demandez à l'agent de lancer l'outil sur le fichier de la saison et d'examiner la ligne de Bayonne.

=== "C++"

    ```bash
    cpp/build/tournoi-cli classement donnees/resultats-2026.csv
    ```

=== "PHP"

    ```bash
    php php/bin/tournoi.php classement donnees/resultats-2026.csv
    ```

Une demande possible :

```text
Lance le classement sur donnees/resultats-2026.csv. Recalcule à la main
les buts pour et contre de Bayonne à partir du CSV, et compare avec
ce qu'affiche l'outil.
```

**Critère de réussite :** vous avez constaté vous-même l'écart entre le calcul manuel et la sortie de l'outil.

## Étape 3 : un test qui échoue, puis le correctif (15 min)

La bonne pratique : écrire d'abord un test qui met le bug en évidence, le voir échouer, puis corriger.

```text
Écris un test de non-régression minimal qui met en évidence ce bug.
Lance-le et montre-moi qu'il échoue. Ne corrige rien pour l'instant.
```

Quand le test échoue pour la bonne raison :

```text
Corrige le bug avec la modification la plus petite possible,
puis relance tous les tests.
```

En mode Manual, chaque modification vous est présentée sous forme de diff. **Lisez-le avant d'accepter.**

**Critère de réussite :** un nouveau test, qui échouait avant le correctif, passe après. Tous les autres tests passent toujours.

## Étape 4 : relire le diff (5 min)

Dans le terminal ou l'onglet *Source Control* de VS Code :

```bash
git diff
```

Posez-vous ces questions :

- Le correctif touche-t-il **uniquement** la cause du bug ?
- Le test vérifie-t-il vraiment le comportement attendu, ou seulement la sortie actuelle du code ?
- Le test aurait-il détecté le bug si on l'avait écrit avant ?
- L'agent a-t-il modifié autre chose sans vous le dire ?

## Étape 5 : commiter (5 min)

```text
Commite ces changements avec un message clair qui décrit le bug et sa correction.
```

Vérifiez avec `git log -1 --stat` que le commit contient ce que vous attendez, et rien d'autre.

## Pour aller plus loin

Si vous avez terminé en avance :

1. **L'autre langage.** Demandez : « Le même bug existe-t-il dans l'autre implémentation ? » Puis faites porter le correctif et le test. C'est l'occasion de vérifier que l'agent respecte la consigne de `CLAUDE.md` sur le comportement identique dans les deux langages.
2. **Le signalement suivant.** Demandez à l'agent d'auditer le calcul du classement à la recherche d'autres cas non testés, sans rien corriger. Jugez la pertinence de sa liste.
3. **L'exercice** de la page [CLAUDE.md et permissions](claude-md-permissions.md#exercice).

## Solution

??? success "Solution"
    **La cause.** Pour l'équipe qui joue à l'extérieur, les buts encaissés sont ceux marqués par l'équipe à domicile. Le code ajoute à la place les buts de l'équipe extérieure elle-même : la différence de buts d'une équipe est donc toujours nulle sur ses matchs à l'extérieur. Bayonne, qui a fait ses plus gros scores à l'extérieur, en est la principale victime.

    Les tests existants ne vérifient ni les buts contre, ni le départage par la différence de buts. Le bug passe donc inaperçu.

    **Le correctif** tient en une ligne.

    === "C++"

        ```diff title="cpp/src/classement.cpp"
                 auto& ext = lignes[r.exterieur];
                 ext.equipe = r.exterieur;
                 ext.butsPour += r.butsExterieur;
        -        ext.butsContre += r.butsExterieur;
        +        ext.butsContre += r.butsDomicile;
        ```

        Tests de non-régression, à ajouter dans `cpp/tests/test_classement.cpp` :

        ```cpp
        TEST(Classement, DifferenceDeButsALExterieur) {
            // B gagne 3-0 chez A : +3 pour B, -3 pour A.
            const auto c = calculerClassement({{"A", "B", 0, 3}});
            ASSERT_EQ(c[0].equipe, "B");
            EXPECT_EQ(c[0].butsPour, 3);
            EXPECT_EQ(c[0].butsContre, 0);
            EXPECT_EQ(c[0].difference(), 3);
            EXPECT_EQ(c[1].difference(), -3);
        }

        TEST(Classement, DepartageParDifferenceDeButs) {
            // B et C ont 4 points. B a gagné 3-0 à l'extérieur, C seulement 1-0 à domicile.
            const auto c = calculerClassement({{"A", "B", 0, 3}, {"C", "D", 1, 0}, {"B", "C", 0, 0}});
            EXPECT_EQ(c[0].equipe, "B");
            EXPECT_EQ(c[1].equipe, "C");
        }
        ```

    === "PHP"

        ```diff title="php/src/Classement.php"
                     $ext = $lignes[$r->exterieur] ??= new LigneClassement($r->exterieur);
                     $ext->butsPour += $r->butsExterieur;
        -            $ext->butsContre += $r->butsExterieur;
        +            $ext->butsContre += $r->butsDomicile;
        ```

        Tests de non-régression, à ajouter dans `php/tests/ClassementTest.php` :

        ```php
        public function testDifferenceDeButsALExterieur(): void
        {
            // B gagne 3-0 chez A : +3 pour B, -3 pour A.
            $c = Classement::calculer([new Resultat('A', 'B', 0, 3)]);
            self::assertSame('B', $c[0]->equipe);
            self::assertSame(3, $c[0]->butsPour);
            self::assertSame(0, $c[0]->butsContre);
            self::assertSame(3, $c[0]->difference());
            self::assertSame(-3, $c[1]->difference());
        }

        public function testDepartageParDifferenceDeButs(): void
        {
            // B et C ont 4 points. B a gagné 3-0 à l'extérieur, C seulement 1-0 à domicile.
            $c = Classement::calculer([
                new Resultat('A', 'B', 0, 3),
                new Resultat('C', 'D', 1, 0),
                new Resultat('B', 'C', 0, 0),
            ]);
            self::assertSame('B', $c[0]->equipe);
            self::assertSame('C', $c[1]->equipe);
        }
        ```

    **Le classement corrigé :**

    ```text
     #  Équipe        J   G   N   P   BP  BC  Diff  Pts
     1  Dax           5   4   1   0    9   3    +6   13
     2  Bayonne       5   3   1   1   10   3    +7   10
     3  Biarritz      5   3   1   1    9   5    +4   10
     4  Pau           5   1   1   3    4  11    -7    4
     5  Hendaye       5   0   2   3    5  10    -5    2
     6  Anglet        5   0   2   3    3   8    -5    2
    ```

## Retour collectif

Pour le tour de table de fin de TP :

- Par quel chemin l'agent a-t-il trouvé la cause ? Combien de fichiers a-t-il lus ?
- Qu'auriez-vous dû copier dans un chat pour obtenir le même résultat ?
- Avez-vous refusé une modification proposée ? Pourquoi ?
