# Corrigé des exercices et questions de réflexion

## Étape 1 — Git

Un message de commit clair permet de comprendre rapidement la nature d'une modification, de retrouver son origine dans l'historique et de faciliter le diagnostic ou le retour à une version antérieure. Six mois plus tard, « Ajout du calcul du chiffre d'affaires par catégorie » est beaucoup plus utile que « modifications ».

## Étape 2 — Anomalies du CSV et agrégation par catégorie

Anomalies repérées dans `data/ventes.csv` :

1. Une ligne dupliquée : la vente du clavier du 5 janvier apparaît deux fois.
2. Une quantité manquante : la vente de la lampe.
3. Une quantité négative : la vente de souris à Marseille.
4. Des villes écrites de façon incohérente : `paris`, `Paris`, ` Lyon`, `LILLE`.

Le pipeline supprime les doublons, retire les lignes sans quantité/prix et celles dont la quantité n'est pas positive, puis normalise les villes. Il calcule ensuite `montant = quantite * prix_unitaire`.

Résultat de l'agrégation par catégorie :

| Catégorie | Chiffre d'affaires |
|---|---:|
| Informatique | 945.0 |
| Mobilier | 850.0 |

Deux avantages du pipeline par rapport au tableur : le traitement est reproductible et il réduit les erreurs manuelles ; il peut aussi être relancé automatiquement sur chaque nouveau fichier. Un inconvénient : il faut développer et maintenir le code, et traiter explicitement les nouveaux formats ou types d'anomalies.

## Étape 3 — Tests

Un petit jeu de données fabriqué permet de connaître précisément le résultat attendu, de couvrir des cas particuliers (doublon, valeur manquante, quantité négative) et d'obtenir des tests rapides et reproductibles. Le test ne dépend pas des changements du fichier réel.

Le cinquième test vérifie l'agrégation par catégorie : il attend 60.0 pour Mobilier sur le jeu d'essai fourni dans `tests/test_pipeline.py`.

## Étape 4 — Intégration continue

Sans CI, une erreur non détectée pourrait produire et faire envoyer un rapport incorrect à la direction. La CI vérifie automatiquement le projet à chaque push et pull request, avant que les changements soient intégrés.

Le serveur repart d'un environnement propre pour vérifier que le projet peut être installé et exécuté avec les seules dépendances déclarées, sans dépendre de fichiers ou réglages présents par hasard sur le poste d'un développeur.

## Étape 5 — Docker

`requirements.txt` liste les bibliothèques Python nécessaires au projet. Le `Dockerfile` décrit la construction d'une image contenant l'environnement d'exécution, Python, les dépendances et le code. Docker apporte donc un environnement plus complet et reproductible, au-delà de la seule liste des bibliothèques Python.

Sans l'option `-v`, le pipeline s'exécute toujours dans le conteneur, mais les fichiers générés restent dans son système de fichiers. Avec `--rm`, le conteneur est supprimé à la fin : les résultats ne sont donc pas conservés sur la machine hôte. Le volume `-v` permet de récupérer les CSV dans le dossier `output/` local.

## Étape 6 — Branche et pull request

Le linter Ruff contrôle automatiquement certains problèmes de qualité et de style. Le lancer dans la CI bloque l'intégration d'un changement qui ne respecte pas ces contrôles. La branche `ajout-linter` permet de préparer le changement séparément ; la pull request déclenche la CI et sert de support à la relecture avant fusion.

## Résultats attendus du pipeline complet

- 12 lignes lues ; 9 lignes conservées.
- Chiffre d'affaires par ville : Paris 525.0 ; Lyon 450.0 ; Bordeaux 330.0 ; Marseille 250.0 ; Lille 240.0.
- Chiffre d'affaires par catégorie : Informatique 945.0 ; Mobilier 850.0.
