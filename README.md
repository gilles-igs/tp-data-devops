# Pipeline de données Bureau&Co

**Auteur :** À compléter

Ce projet nettoie le fichier des ventes et calcule le chiffre d'affaires par ville et par catégorie. Il met en pratique Git, pytest, GitHub Actions, Ruff et Docker.

## Installation et exécution

```bash
pip install -r requirements.txt
python pipeline.py
```

Le pipeline lit `data/ventes.csv`, supprime les doublons et les lignes sans quantité/prix ou avec une quantité non positive, normalise les noms de villes, puis calcule le montant (`quantite * prix_unitaire`). Il écrit trois fichiers dans `output/` :

- `ventes_propres.csv`
- `ca_par_ville.csv`
- `ca_par_categorie.csv`

## Tests et qualité

```bash
pytest -v
ruff check .
```

## Docker

```bash
docker build -t pipeline-data .
docker run --rm -v "$(pwd)/output:/app/output" pipeline-data
```

## Intégration continue

Le workflow `.github/workflows/ci.yml` s'exécute sur chaque push et pull request. Il installe les dépendances, lance Ruff et les cinq tests, exécute le pipeline, construit l'image Docker et publie les CSV comme artefact `resultats`.
