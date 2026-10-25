# Pièges et prompting

*15 minutes.* Le code généré a des défauts récurrents. Les connaître permet de savoir **où regarder** en relisant un diff.

## Prompting efficace

Il n'y a pas de formule magique. Une bonne demande ressemble à une bonne demande faite à un collègue compétent qui ne connaît pas le projet.

| À faire | À éviter |
|---|---|
| Désigner les fichiers, fonctions ou exemples concernés : `@cpp/src/classement.cpp` | « Améliore le code » |
| Donner un critère vérifiable : une commande qui doit passer, une sortie attendue | « Fais en sorte que ça marche » |
| Énoncer les contraintes : ce qu'il ne faut **pas** toucher | Supposer que l'agent devinera les limites |
| Montrer un exemple du style attendu : « comme dans `Calendrier` » | Décrire le style en termes abstraits |
| Une tâche par demande, `/clear` entre deux tâches | Enchaîner dix sujets dans la même session |
| Demander de s'arrêter en cas de doute | Laisser l'agent trancher seul une ambiguïté |

!!! tip "À retenir"
    Les meilleures demandes sont souvent courtes et précises, pas longues et vagues. La précision vient des références (fichiers, exemples, critères), pas de la longueur.

## Les défauts typiques

| Défaut | À quoi ça ressemble | Parade |
|---|---|---|
| **Plausible mais faux** | Une API qui n'existe pas, un cas limite oublié, une formule presque juste | Tests écrits avant, sur des exemples calculés à la main |
| **Tests complaisants** | Des tests qui recopient l'implémentation, ou qui vérifient la sortie actuelle plutôt que la bonne | Relire les tests comme une spécification ; les écrire avant le code |
| **Dette technique** | Duplication, fonctions qui grossissent, abstraction inutile, code mort conservé « au cas où » | Relecture du diff ; consignes dans `CLAUDE.md` ; demander une simplification |
| **Sécurité** | Requêtes SQL concaténées, sorties HTML non échappées, entrées non validées, comportements indéfinis en C++ | Relecture ciblée ; revue de sécurité dédiée (séance 3) |
| **Secrets** | Une clé ou un mot de passe en dur, un `.env` lu et recopié dans un log | Règles `deny` sur les fichiers sensibles ; ne jamais coller un secret dans une demande |
| **Périmètre** | Des fichiers modifiés que vous n'aviez pas demandé de toucher | `git diff --stat` avant chaque commit |

## Exercice : relire comme un relecteur

!!! example "Exercice"
    Chacun des extraits suivants aurait pu être produit par un agent. Il compile ou s'exécute, et il passe des tests simples. Trouvez le défaut.

**Extrait 1 (PHP)**

```php
public function resultatsDe(string $equipe): array
{
    $sql = "SELECT * FROM resultats WHERE domicile = '$equipe' OR exterieur = '$equipe'";
    return $this->pdo->query($sql)->fetchAll();
}
```

??? success "Réponse"
    **Injection SQL.** Une équipe nommée `x' OR '1'='1` renvoie toute la table ; pire selon le contexte. Il faut une requête préparée :

    ```php
    $stmt = $this->pdo->prepare('SELECT * FROM resultats WHERE domicile = :e OR exterieur = :e');
    $stmt->execute(['e' => $equipe]);
    return $stmt->fetchAll();
    ```

**Extrait 2 (C++)**

```cpp
std::string_view nomDuPremier(std::vector<Ligne> classement) {
    return classement.front().equipe;
}
```

??? success "Réponse"
    **Deux comportements indéfinis.** Le paramètre est une copie locale : la `string_view` renvoyée pointe vers une chaîne détruite à la sortie de la fonction. Et `front()` sur un vecteur vide est indéfini. Il faut renvoyer une `std::string` (ou prendre une référence constante et documenter la durée de vie), et traiter le cas vide. Compiler les tests avec `-DTOURNOI_SANITIZERS=ON` détecte le premier défaut dès qu'un test appelle la fonction.

**Extrait 3 (test, C++)**

```cpp
TEST(Classement, Points) {
    const auto c = calculerClassement(resultats);
    int attendu = 0;
    for (const auto& r : resultats) {
        if (r.domicile == "A" && r.butsDomicile > r.butsExterieur) attendu += kPointsVictoire;
    }
    EXPECT_EQ(c[0].points, attendu);
}
```

??? success "Réponse"
    **Test complaisant.** Il recalcule le résultat avec une logique semblable à celle du code testé, en plus incomplète : il ignore les matchs à l'extérieur et les nuls, et suppose que A est premier. Un test doit comparer à une valeur **connue** : « A a 7 points ».

**Extrait 4 (PHP)**

```php
$niveau = (int) $_GET['niveau'];
$equipes = array_slice($classement, 0, $niveau);
echo "<h2>Les $niveau premiers : " . implode(', ', $equipes) . "</h2>";
```

??? success "Réponse"
    Le transtypage protège `$niveau`, mais **les noms d'équipes sont affichés sans échappement** : une équipe nommée `<script>…</script>` exécute du code dans le navigateur (XSS). Il faut `htmlspecialchars()` sur chaque nom. Et `niveau` négatif ou absent donne un résultat surprenant sans erreur.

## Pour aller plus loin

- [Bonnes pratiques : éviter les échecs courants](https://code.claude.com/docs/en/best-practices)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/) pour la partie web
