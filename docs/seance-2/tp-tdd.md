# TP 2 : phase finale en TDD, avec passation

*45 minutes, en binôme.* Vous ajoutez au projet une phase finale à élimination directe, en appliquant toute la méthode : plan, critères, tests d'abord, petits incréments. À mi-parcours, le travail **change de poste** : votre binôme reprend sur sa machine, dans une nouvelle session, sans rien d'autre que ce que vous aurez laissé dans le dépôt.

## Organisation

- Binôme A et B. Choisissez **un** langage pour le binôme.
- Vous travaillez sur le fork de A, qui contient le correctif du TP 1. A ajoute B comme collaborateur (*Settings > Collaborators* sur GitHub).
- A pilote la première partie sur son poste, B relit les diffs avec lui. Les rôles s'inversent pour la seconde partie, sur le poste de B.

## La spécification

On veut générer le **premier tour** d'une phase finale à élimination directe à partir du classement.

- Entrée : la liste des équipes **dans l'ordre du classement**, et le nombre de qualifiés.
- Le nombre de qualifiés est une puissance de 2, au moins 2, et pas plus que le nombre d'équipes. Sinon : erreur.
- Les meilleures équipes sont protégées : la tête de série 1 affronte la dernière qualifiée, et les têtes de série 1 et 2 ne peuvent se rencontrer qu'en finale.
- L'équipe la mieux classée reçoit.
- Les rencontres sont rendues **dans l'ordre du tableau**, de haut en bas.

Critères d'acceptation, avec `T1` à `T9` classées dans cet ordre :

| Qualifiés | Premier tour attendu |
|---|---|
| 2 | `T1-T2` |
| 4 | `T1-T4`, `T2-T3` |
| 8 | `T1-T8`, `T4-T5`, `T2-T7`, `T3-T6` |
| 6, 1 ou 0 | erreur |
| 4 qualifiés parmi 3 équipes | erreur |

=== "C++"

    Fonction attendue, dans un nouvel en-tête `cpp/include/tournoi/phase_finale.hpp` :

    ```cpp
    std::vector<Match> genererPremierTour(const std::vector<std::string>& equipesClassees,
                                          std::size_t nbQualifies);
    ```

    Erreur : `std::invalid_argument`.

=== "PHP"

    Méthode attendue, dans une nouvelle classe `php/src/PhaseFinale.php` :

    ```php
    /** @return list<Rencontre> */
    public static function genererPremierTour(array $equipesClassees, int $nbQualifies): array
    ```

    Erreur : `InvalidArgumentException`.

## Partie 1, sur le poste de A (20 min)

### Planifier (5 min)

Passez en **plan mode** et donnez la spécification complète : copiez la section ci-dessus dans votre demande, en ajoutant :

```text
Propose un plan en incréments. Les tests sont écrits avant le code à chaque incrément.
Ne modifie pas le calcul du classement.
```

Relisez et annotez le plan. Vérifiez qu'il prévoit les tests en premier et qu'il ne touche pas aux fichiers existants plus que nécessaire.

### Incrément 1 : le tableau (15 min)

1. Faites écrire **uniquement les tests** des critères d'acceptation. Lancez-les : ils doivent échouer (ou ne pas compiler, en C++, faute de fonction).
2. Relisez les tests. Correspondent-ils exactement au tableau ci-dessus ?
3. Faites écrire l'implémentation, **sans modifier les tests**.
4. `make test-cpp` ou `make test-php` : tout doit être vert.
5. Commitez.

**Critère de réussite :** les cinq critères d'acceptation sont couverts par des tests, qui passent.

## Passation (5 min)

C'est l'étape clé du TP. A demande à l'agent :

```text
Rédige NOTES.md pour qu'une autre personne reprenne ce travail dans une
nouvelle session, sur un autre poste : ce qui est fait, les décisions prises
et pourquoi, ce qui reste à faire (l'incrément suivant est décrit ci-dessous),
et comment vérifier que tout fonctionne. Sois concis.
```

Relisez `NOTES.md`, corrigez-le si besoin, puis commitez et poussez :

```bash
git push origin HEAD
```

!!! warning "Piège"
    Tout ce qui n'est pas dans le dépôt est perdu pour B : la conversation, le plan approuvé, la mémoire automatique de l'agent sur le poste de A. C'est voulu.

## Partie 2, sur le poste de B (15 min)

B clone le fork de A (ou fait un `git pull`), ouvre le Dev Container et démarre une **nouvelle** session :

```text
Lis NOTES.md et CLAUDE.md, puis reprends le travail.
```

### Incrément 2 : la commande en ligne

Ajouter une commande `phase-finale FICHIER.csv N` à l'outil en ligne de commande. Elle calcule le classement à partir du fichier, retient les `N` premiers et affiche le premier tour.

=== "C++"

    ```bash
    cpp/build/tournoi-cli phase-finale donnees/resultats-2026.csv 4
    ```

=== "PHP"

    ```bash
    php php/bin/tournoi.php phase-finale donnees/resultats-2026.csv 4
    ```

Sortie attendue, avec le correctif du TP 1 :

```text
Phase finale (4 qualifiés)
  Dax - Pau
  Bayonne - Biarritz
```

Même discipline : critères d'acceptation, tests d'abord quand c'est possible, commit.

**Critère de réussite :** la commande produit la sortie attendue, et un nombre invalide affiche une erreur claire au lieu de planter.

## Retour collectif (5 min)

- B a-t-il pu reprendre sans poser de question à A ? Qu'est-ce qui manquait dans `NOTES.md` ?
- Le plan initial a-t-il tenu ? Où l'agent a-t-il dévié ?
- Les tests écrits en premier ont-ils attrapé une erreur de l'implémentation ?

## Pour aller plus loin

- **Les exemptions.** Si le nombre de qualifiés n'est pas une puissance de 2, les meilleures équipes sont exemptées du premier tour. Avec 6 qualifiés : T1 et T2 exemptées, puis `T3-T6` et `T4-T5`. Spécifiez, testez, implémentez.
- **L'autre langage.** Faites porter la fonctionnalité dans l'autre implémentation, en exigeant les mêmes tests.

## Solution

??? success "Solution de l'incrément 1"
    L'idée : on construit l'ordre des têtes de série dans le tableau par doublements successifs. À chaque étape, chaque tête de série `s` est suivie de son adversaire `n + 1 - s`, où `n` est la nouvelle taille.

    ```text
    2 : 1 2
    4 : 1 4 2 3
    8 : 1 8 4 5 2 7 3 6
    ```

    Il suffit ensuite de lire cette liste deux par deux.

    === "C++"

        ```cpp title="cpp/src/phase_finale.cpp"
        #include "tournoi/phase_finale.hpp"

        #include <stdexcept>

        namespace tournoi {

        namespace {

        // Ordre des têtes de série dans le tableau : 1, 2 -> 1, 4, 2, 3 -> 1, 8, 4, 5, 2, 7, 3, 6...
        std::vector<std::size_t> ordreDuTableau(std::size_t taille) {
            std::vector<std::size_t> ordre{1, 2};
            while (ordre.size() < taille) {
                const std::size_t n = ordre.size() * 2;
                std::vector<std::size_t> suivant;
                for (std::size_t tete : ordre) {
                    suivant.push_back(tete);
                    suivant.push_back(n + 1 - tete);
                }
                ordre = suivant;
            }
            return ordre;
        }

        bool estPuissanceDeDeux(std::size_t n) { return n != 0 && (n & (n - 1)) == 0; }

        }  // namespace

        std::vector<Match> genererPremierTour(const std::vector<std::string>& equipesClassees,
                                              std::size_t nbQualifies) {
            if (nbQualifies < 2 || !estPuissanceDeDeux(nbQualifies)) {
                throw std::invalid_argument("Le nombre de qualifiés doit être une puissance de 2, au moins 2");
            }
            if (nbQualifies > equipesClassees.size()) {
                throw std::invalid_argument("Pas assez d'équipes pour ce nombre de qualifiés");
            }

            const auto ordre = ordreDuTableau(nbQualifies);
            std::vector<Match> tour;
            for (std::size_t i = 0; i < ordre.size(); i += 2) {
                tour.push_back({equipesClassees[ordre[i] - 1], equipesClassees[ordre[i + 1] - 1]});
            }
            return tour;
        }

        }  // namespace tournoi
        ```

        ```cpp title="cpp/tests/test_phase_finale.cpp (extrait)"
        TEST(PhaseFinale, HuitQualifiesDansLOrdreDuTableau) {
            EXPECT_EQ(enLignes(genererPremierTour(kClassement, 8)),
                      (std::vector<std::string>{"T1-T8", "T4-T5", "T2-T7", "T3-T6"}));
        }

        TEST(PhaseFinale, RefuseUnNombreQuiNEstPasUnePuissanceDeDeux) {
            EXPECT_THROW(genererPremierTour(kClassement, 6), std::invalid_argument);
            EXPECT_THROW(genererPremierTour(kClassement, 1), std::invalid_argument);
            EXPECT_THROW(genererPremierTour(kClassement, 0), std::invalid_argument);
        }
        ```

        N'oubliez pas d'ajouter `src/phase_finale.cpp` et le fichier de tests dans `cpp/CMakeLists.txt`.

    === "PHP"

        ```php title="php/src/PhaseFinale.php"
        <?php

        declare(strict_types=1);

        namespace Tournoi;

        use InvalidArgumentException;

        final class PhaseFinale
        {
            /**
             * @param list<string> $equipesClassees équipes dans l'ordre du classement
             * @return list<Rencontre>
             */
            public static function genererPremierTour(array $equipesClassees, int $nbQualifies): array
            {
                if ($nbQualifies < 2 || ($nbQualifies & ($nbQualifies - 1)) !== 0) {
                    throw new InvalidArgumentException('Le nombre de qualifiés doit être une puissance de 2, au moins 2');
                }
                if ($nbQualifies > count($equipesClassees)) {
                    throw new InvalidArgumentException("Pas assez d'équipes pour ce nombre de qualifiés");
                }

                $ordre = self::ordreDuTableau($nbQualifies);
                $tour = [];
                for ($i = 0; $i < count($ordre); $i += 2) {
                    $tour[] = new Rencontre($equipesClassees[$ordre[$i] - 1], $equipesClassees[$ordre[$i + 1] - 1]);
                }

                return $tour;
            }

            /** @return list<int> */
            private static function ordreDuTableau(int $taille): array
            {
                $ordre = [1, 2];
                while (count($ordre) < $taille) {
                    $n = count($ordre) * 2;
                    $suivant = [];
                    foreach ($ordre as $tete) {
                        $suivant[] = $tete;
                        $suivant[] = $n + 1 - $tete;
                    }
                    $ordre = $suivant;
                }

                return $ordre;
            }
        }
        ```

        ```php title="php/tests/PhaseFinaleTest.php (extrait)"
        public function testHuitQualifiesDansLOrdreDuTableau(): void
        {
            self::assertSame(
                ['T1-T8', 'T4-T5', 'T2-T7', 'T3-T6'],
                self::enLignes(PhaseFinale::genererPremierTour(self::CLASSEMENT, 8)),
            );
        }
        ```

??? success "Exemple de NOTES.md"
    ```markdown
    # Phase finale : notes de passation

    ## Fait
    - Incrément 1 : `genererPremierTour` (C++), tests dans `tests/test_phase_finale.cpp`.
      Les 5 critères d'acceptation sont couverts, `make test-cpp` est vert.

    ## Décisions
    - Ordre du tableau construit par doublements (1,2 -> 1,4,2,3 -> ...),
      pour que les têtes de série 1 et 2 ne se croisent qu'en finale.
    - L'équipe la mieux classée reçoit.
    - Pas d'exemptions : le nombre de qualifiés doit être une puissance de 2.

    ## À faire
    - Incrément 2 : commande `phase-finale FICHIER.csv N` dans `tournoi-cli`
      (calcul du classement, N premiers, affichage du premier tour).

    ## Vérifier
    - `make test-cpp`
    - `cpp/build/tournoi-cli phase-finale donnees/resultats-2026.csv 4`
      doit afficher Dax - Pau puis Bayonne - Biarritz.
    ```
