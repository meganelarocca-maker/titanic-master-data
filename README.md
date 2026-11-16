# Titanic : prédire la survie des passagers

Projet de classification binaire sur le jeu de données Titanic de Kaggle (« Titanic - Machine Learning from Disaster »). Je prédis la colonne `Survived` (0 = décédé, 1 = survivant) et je compare trois modèles : régression logistique, Random Forest et XGBoost.

## Structure du dépôt

```
titanic-master-data/
├── data/                  # jeux de données (non versionnés, voir .gitignore)
├── notebooks/
│   ├── 01_exploration_titanic.ipynb
│   ├── 02_nettoyage_titanic.ipynb
│   └── 03_modelisation_titanic.ipynb
├── requirements.txt
└── README.md
```

## Démarche

| Notebook | Objectif | Ce que j'en retiens |
|---|---|---|
| 01 Exploration | Je décris les données : types, valeurs manquantes, cible, doublons, corrélations | Le sexe, la classe et le prix du billet sont les variables les plus liées à la survie |
| 02 Nettoyage | Je nettoie ce qui est fixe : espaces dans `Name`, extraction du titre, indicateur « cabine renseignée » | Je supprime `Cabin`, `Name` et `Ticket` après en avoir extrait l'information utile |
| 03 Modélisation | Je sépare train et validation, puis j'entraîne et je compare les trois modèles | Les trois modèles dépassent nettement le modèle de référence, sans gagnant net |

Règle appliquée : tout ce qui est appris à partir des données (médiane de l'âge, mode de `Embarked`, encodage, standardisation) l'est sur le jeu d'entraînement uniquement, dans un `Pipeline`, pour éviter la fuite de données.

## Résultats (jeu de validation, 179 passagers)

| Modèle | Accuracy | F1 | Accuracy en validation croisée (5 plis) |
|---|---|---|---|
| Modèle de référence (toujours « décédé ») | 0,615 | non applicable | non applicable |
| Régression logistique | 0,838 | 0,788 | 0,824 |
| Random Forest | 0,804 | 0,741 | 0,819 |
| XGBoost | 0,788 | 0,703 | 0,824 |

- La régression logistique obtient les meilleurs scores sur la validation, mais la validation croisée place les trois modèles à égalité.
- Avec 179 passagers en validation, un passager de plus ou de moins bien classé change l'accuracy d'environ 0,6 point : je ne conclus donc pas à un gagnant net.

## Installation

1. Je clone le dépôt et je crée un environnement virtuel :

```bash
python -m venv venv
source venv/Scripts/activate      # Windows (Git Bash)
python -m pip install -r requirements.txt
```

2. Je télécharge `train.csv` depuis la [compétition Kaggle](https://www.kaggle.com/c/titanic/data) et je le place dans `data/` (le nom du fichier est celui utilisé dans les notebooks).
3. J'ouvre les notebooks dans VS Code, je sélectionne le kernel du `venv` et je les exécute dans l'ordre : 01, 02, puis 03. Le notebook 02 crée `data/titanic_nettoye.csv`, lu par le 03.

## Limites et suites possibles

- Une seule séparation train / validation sur un petit effectif : je m'appuie aussi sur la validation croisée.
- Les hyperparamètres ne sont pas optimisés : je peux tester `GridSearchCV` sur le modèle retenu.
- Je n'ai pas encore appliqué le même nettoyage à `test.csv` pour produire une soumission Kaggle.
- Pour aller vers l'industrialisation : transformer le nettoyage en script réutilisable, sauvegarder le pipeline entraîné et ajouter des contrôles qualité sur les données.
