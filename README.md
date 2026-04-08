# IA for HumanForYou : Analyse de l'attrition des employés

Projet de data science réalisé dans le cadre du cursus CESI.  
L'entreprise pharmaceutique indienne **HumanForYou** (~4 000 employés) connaît un taux de rotation annuel de ~15 %.  
L'objectif est d'identifier les facteurs d'attrition et de construire des modèles prédictifs pour aider la direction à fidéliser ses équipes.

---

## Structure du projet

```
.
├── data/
│   ├── raw/                  # Données brutes originales (CSV)
│   └── processed/            # Données nettoyées et transformées
├── notebooks/                # Notebooks Jupyter d'exploration et modélisation
├── src/                      # Scripts Python réutilisables (preprocessing, modèles…)
├── outputs/                  # Graphiques, modèles exportés, résultats (.gitignored)
├── docs/                     # Livrables (éthique, bibliographie…)
├── requirements.txt
└── README.md
```

---

## Données sources

| Fichier | Description |
|---|---|
| `general_data.csv` | Données RH générales (âge, genre, salaire, ancienneté…) |
| `employee_survey_data.csv` | Enquête qualité de vie au travail (satisfaction, équilibre pro/perso) |
| `in_time.csv` | Horaires d'entrée badgeuse (janvier–décembre 2015) |
| `out_time.csv` | Horaires de sortie badgeuse (janvier–décembre 2015) |

---

## Installation de l'environnement

```bash
# Cloner le dépôt
git clone https://github.com/Nej-ma/A4-IA-HumanForYou.git
cd A4-IA-HumanForYou

# Créer et activer l'environnement virtuel
python -m venv venv

# Windows
venv\Scripts\activate

# Linux / macOS
source venv/bin/activate

# Installer les dépendances
pip install -r requirements.txt

# Lancer Jupyter
jupyter notebook
```
