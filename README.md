# Développer avec Claude : site de la formation

Site MkDocs Material de la formation à Claude Code pour des enseignants en informatique.

## Prévisualiser en local

```bash
python -m venv .venv
source .venv/bin/activate      # Windows : .venv\Scripts\activate
pip install -r requirements.txt
mkdocs serve
```

Le site est alors disponible sur http://127.0.0.1:8000.

## Publication

Chaque push sur `main` publie le site sur GitHub Pages via `.github/workflows/deploy.yml`.
Une seule fois : dans le dépôt GitHub, **Settings > Pages > Source**, choisir **GitHub Actions**.
