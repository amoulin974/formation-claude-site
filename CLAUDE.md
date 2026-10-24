# Site de la formation « Développer avec Claude »

Site MkDocs Material, contenu en français, dans `docs/`.

## Commandes

- Prévisualiser : `mkdocs serve`
- Vérifier avant commit : `mkdocs build --strict` (doit passer sans avertissement)

## Conventions

- Une page Markdown par notion, rangée dans le dossier de sa séance (`docs/seance-N/`).
- Toute nouvelle page est ajoutée à `nav` dans `mkdocs.yml`.
- Exercices : un onglet C++ et un onglet PHP (`=== "C++"` / `=== "PHP"`), solution dans un bloc repliable `??? success "Solution"`.
- Encadrés : `!!! tip "À retenir"`, `!!! warning "Piège"`, `!!! example "Exercice"`.
- Phrases courtes, pas de jargon inutile, vouvoiement des participants.
