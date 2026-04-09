# Plan de présentation - HumanForYou
## Soutenance 20 minutes + questions

---

## Structure générale

| Partie | Durée | Slides |
|---|---|---|
| Introduction et contexte | 2 min | 1-2 |
| Données et preprocessing | 4 min | 3-6 |
| Modélisation et comparaison | 5 min | 7-10 |
| Optimisation et résultats finaux | 4 min | 11-13 |
| Recommandations et mise en production | 3 min | 14-16 |
| Conclusion et limites | 2 min | 17-18 |

---

## Slide 1 - Titre et contexte (1 min)

**Titre** : HumanForYou - Prédire et comprendre l'attrition des employés

**Ce qu'on dit** :
HumanForYou est une entreprise pharmaceutique indienne de 4000 employés avec un taux de rotation de 15% par an. La direction nous demande deux choses : identifier les facteurs qui poussent les employés à partir, et construire un modèle pour détecter les profils à risque avant qu'ils ne partent.

**Visuel** : chiffres clés en grand - 4000 employés / 15% attrition / 5 sources de données

---

## Slide 2 - La question et l'enjeu (1 min)

**Ce qu'on dit** :
Pourquoi c'est important ? Un départ coûte entre 50% et 200% du salaire annuel de l'employé (recrutement, formation, perte de productivité). Sur 4000 employés à 15%, ça représente 600 départs par an. Réduire ce taux de 5 points, c'est 200 départs évités.

On ne cherche pas juste à prédire - on cherche à expliquer pour que les RH puissent agir.

**Visuel** : schéma "prédire qui" + "comprendre pourquoi" + "agir avant"

---

## Slide 3 - Les données disponibles (1 min)

**Ce qu'on dit** :
5 fichiers sources fournis par HumanForYou, fusionnés sur EmployeeID :
- Données RH générales (age, salaire, ancienneté, poste...)
- Enquête satisfaction (réponse volontaire - point important)
- Évaluation manager
- Badgeage entrée/sortie sur toute l'année 2015

**Visuel** : schéma des 5 sources + flèche vers dataset fusionné 4410 lignes / 45 colonnes

---

## Slide 4 - Signal fort découvert dans le badgeage (1.5 min)

**Ce qu'on dit** :
Les 261 colonnes de timestamps ont été condensées en 5 indicateurs. Le plus discriminant : `days_over_8h`. Les employés qui partent font en moyenne **129 jours > 8h** dans l'année, contre **66 jours** pour ceux qui restent. C'est +96%. Ce signal n'était pas visible dans les données RH classiques - il vient uniquement du badgeage.

**Visuel** : graphique barres comparant les 5 features badge entre restants et partants. Mettre `days_over_8h` en évidence.

**Notebook** : NB01, section badge

---

## Slide 5 - Preprocessing : 6 étapes clés (1.5 min)

**Ce qu'on dit** :
1. Suppression de 3 colonnes constantes (aucune info)
2. Calcul des 5 features badge depuis les timestamps
3. Fusion des 5 sources sur EmployeeID - 4410 lignes, 0 doublon
4. Imputation des NA par la médiane (< 1% de NA, biais vérifié)
5. Encodage : LabelEncoder (Gender) + One-Hot (5 variables nominales)
6. Normalisation StandardScaler - **uniquement sur X_train** (bonne pratique anti data leakage)

**Data leakage** : si on fit le scaler sur tout le dataset avant le split, la moyenne et l'ecart-type sont calcules a partir des donnees de test - le modele "voit" indirectement ces donnees pendant l'entrainement, ce qui fausse les metriques. On sauvegarde donc `dataset_encoded.csv` sans normalisation, et le scaler est fit sur `X_train` uniquement en notebook 03.

L'imputation est faite sur le dataset complet, mais on a verifie que les medianes sont identiques sur le dataset complet et sur X_train seul (EnvironmentSatisfaction, JobSatisfaction, WorkLifeBalance : difference = 0.0000). Aucun biais.

**Visuel** : pipeline en 6 etapes avec icones

**Notebook** : NB02

---

## Slide 6 - Déséquilibre des classes (1 min)

**Ce qu'on dit** :
84% restent, 16% partent. Sans traitement, un modèle naïf dirait "tout le monde reste" et aurait 84% d'accuracy - ce qui est inutile. On utilise `class_weight='balanced'` qui donne automatiquement plus de poids aux partants pendant l'entraînement, sans modifier les données. On évalue avec le F1-score et l'AUC, pas l'accuracy.

**Visuel** : camembert 84/16 + formule class_weight + pourquoi F1 > accuracy

---

## Slide 7 - Comparaison des 5 modèles (2 min)

**Ce qu'on dit** :
On a testé 5 familles d'algorithmes avec des logiques fondamentalement différentes. L'évaluation se fait par validation croisée stratifiée à 5 folds sur X_train - pas sur le test set, qu'on garde pour la mesure finale.

| Modèle | F1 CV | Std |
|---|---|---|
| Régression Logistique | 0.490 | 0.014 |
| Arbre de Décision | 0.491 | 0.021 |
| k-NN | 0.427 | 0.033 |
| SVM | 0.736 | 0.021 |
| **Random Forest** | **0.844** | **0.009** |

Le RF est nettement en tête ET a la variance la plus faible - il est stable.

**Visuel** : graphique barres horizontales F1 CV avec barres d'erreur (std)

**Notebook** : NB03, Partie 1

---

## Slide 8 - Pourquoi le Random Forest ? (1 min)

**Ce qu'on dit** :
Trois raisons pour choisir le RF :
1. **Performance** : F1 CV = 0.844, +10 points sur le SVM (2e meilleur)
2. **Stabilité** : std = 0.009, le plus faible des 5 modèles
3. **Interprétabilité** : il répond à "qui va partir" ET à "pourquoi" via les importances de variables. Le SVM est performant mais opaque - il ne donne pas d'explication, ce que HumanForYou demande explicitement.

**Visuel** : tableau comparatif avec les 3 critères cochés pour RF

---

## Slide 9 - Sélection de features (1 min)

**Ce qu'on dit** :
On a testé deux méthodes pour voir si on pouvait réduire le modèle à 20 features sans perte :
- **Filtre** : mutual information (mesure de dépendance statistique)
- **Embarquée** : importances RF directement

Les deux méthodes s'accordent sur 8/10 variables dans leur top 10 - ce sont les signaux robustes. Mais réduire à 20 features fait perdre 3 à 5 points de F1 CV. On conserve les 43 features.

**Visuel** : deux graphiques importances côte à côte, variables communes surlignées

**Notebook** : NB03, Partie 4

---

## Slide 10 - Optimisation : grid search + seuil (2 min)

**Ce qu'on dit** :
Deux niveaux d'optimisation dans le notebook 04 :

**Grid search** : on teste 8 combinaisons (n_estimators, max_depth) par CV sur X_train. Meilleure config : 500 arbres, max_depth=30.

**Seuil de décision** : par défaut le modèle dit "part" si proba > 0.5 → 19 faux négatifs, 0 faux positifs. En contexte RH, rater un partant coûte beaucoup plus cher qu'un faux positif (entretien inutile). On recherche le seuil optimal par CV sur X_train → **seuil 0.40**.

Le test set n'est jamais utilisé pour ces choix - seulement pour la mesure finale.

**Visuel** : courbe F1/FN selon le seuil avec barres d'erreur CV

**Notebook** : NB04

---

## Slide 11 - Résultats finaux (1 min)

**Ce qu'on dit** :
Mesure finale sur le test set (882 employés, touché une seule fois) :

| | Seuil 0.50 | Seuil 0.40 (CV) |
|---|---|---|
| F1 | 0.928 | **0.978** |
| AUC | 0.997 | 0.997 |
| Partants détectés | 123/142 (87%) | **136/142 (96%)** |
| Faux positifs | 0 | 0 |

On capte 96% des départs réels sans aucun faux positif.

**Visuel** : deux matrices de confusion côte à côte (bleue vs verte)

---

## Slide 12 - Les facteurs qui expliquent les départs (2 min)

**Ce qu'on dit** :
Le modèle confirme et quantifie ce que l'exploration avait suggéré :

| Facteur | Restants | Partants | Ecart |
|---|---|---|---|
| Jours > 8h (badgeage) | 66 | 129 | +96% |
| Ancienneté | 7.4 ans | 5.1 ans | -30% |
| Salaire mensuel | 65 673 ₹ | 61 683 ₹ | -6% |
| Satisfaction travail | 2.78/4 | 2.47/4 | -11% |
| Satisfaction environnement | 2.77/4 | 2.47/4 | -11% |

Convergence Gini + permutation : même top 8 avec les deux méthodes.

**Visuel** : graphique importances RF top 15 + tableau chiffres bruts

**Notebook** : NB04, sections 5 et 6

---

## Slide 13 - Recommandations (1.5 min)

**Ce qu'on dit** :
6 recommandations directement fondées sur les données :

1. **Tableau de bord badgeage mensuel** : alerter quand un employé dépasse 3 semaines consécutives > 8h
2. **Focus rétention années 1-3** : renforcer l'onboarding et proposer une première évolution avant 3 ans d'ancienneté
3. **Revalorisation salariale ciblée** : identifier les employés sous la médiane de leur tranche d'expérience
4. **Enquête satisfaction actionnable** : seuil d'alerte à 2.5/4 par département, plan correcteur obligatoire
5. **Formation régulière** : aucun employé sans formation sur une année complète
6. **Système d'alerte RF** : score de probabilité mensuel, entretien RH pour les profils > 40%

**Visuel** : 6 blocs avec icône + chiffre clé pour chacun

---

## Slide 14 - Mise en production (1 min)

**Ce qu'on dit** :
Le notebook 05 simule le déploiement réel. Chaque mois, les RH exportent les 5 fichiers sources depuis leur SIRH, lancent le notebook, et obtiennent un tableau avec :
- Le score de risque de chaque employé (0 à 1)
- Trois niveaux : risque élevé / modéré / faible
- Un flag Alerte RH pour filtrer directement les cas prioritaires

Sur les données 2015 : 705 employés en risque élevé (16%), 0 faux positif.

**Visuel** : extrait du fichier predictions_soutenance.csv + camembert des 3 niveaux

**Notebook** : NB05

---

## Slide 15 - Limites du modèle (1.5 min)

**Ce qu'on dit** :
Quatre limites importantes à connaître avant tout déploiement :

1. **Signal badge rétro-actif** : `days_over_8h` est calculé sur une année complète. En production, ce signal n'est disponible qu'après 6-8 mois d'historique, pas en temps réel.

2. **AUC de 0.997 à relativiser** : ce score exceptionnel est en grande partie porté par ce signal badge. Sans lui, les performances seraient plus modestes.

3. **Données 2015** : le modèle reflète les conditions de travail d'il y a 10 ans. Une mise à jour annuelle est nécessaire.

4. **Corrélation ≠ causalité** : le modèle identifie des corrélations. Augmenter le salaire d'un profil signalé ne garantit pas qu'il reste - d'autres facteurs non mesurés peuvent être déterminants.

**Visuel** : 4 points en icônes d'avertissement

---

## Slide 16 - Éthique (1 min)

**Ce qu'on dit** :
Le projet a suivi les 7 exigences de l'IA de confiance de la Commission Européenne :
- Les données sont anonymisées (EmployeeID sans nom ni contact)
- Le modèle est transparent : chaque prédiction peut être expliquée par les importances
- Le score est un outil d'aide à la décision RH, pas une décision automatique
- Les variables sensibles (Gender, MaritalStatus) sont présentes dans le modèle mais ne figurent pas dans les recommandations finales
- Un document éthique complet a été produit (livrable joint)

**Visuel** : logo Commission Européenne + 7 exigences avec coche sur celles couvertes

---

## Slide 17 - Conclusion (30 sec)

**Ce qu'on dit** :
On a répondu aux deux questions de HumanForYou :
- **Qui** va partir : détecté à 96% avec 0 faux positif
- **Pourquoi** : badgeage (+96% heures sup), ancienneté courte (-30%), salaire (-6%), satisfaction (-11%)

Le modèle est prêt à être déployé avec les précautions identifiées.

**Visuel** : deux cases cochées "qui" et "pourquoi" + chiffre 96%

---

## Questions probables du jury

**"Pourquoi pas SMOTE pour le déséquilibre ?"**
SMOTE génère des données synthétiques qui peuvent ne pas refléter la réalité des profils RH. `class_weight='balanced'` est plus conservateur et suffisant pour un déséquilibre modéré 84/16.

**"L'AUC de 0.997 c'est pas suspect ?"**
C'est un score exceptionnel explique principalement par `days_over_8h` (+96% chez les partants), un signal tres fort issu du badgeage. On le mentionne explicitement dans les limites : ce signal est calcule sur l'annee complete et n'est pas disponible en temps reel en production. Sans lui, les performances seraient plus modestes.

**"Comment vous gérez le biais sur le genre ou l'âge ?"**
Les variables Gender et MaritalStatus sont dans le modèle mais leur importance est faible. Les recommandations finales ne s'appuient pas sur ces variables. Un audit de fairness par sous-groupe (par département, niveau) serait la prochaine étape.

**"Pourquoi pas GridSearchCV automatique ?"**
On a fait la boucle manuellement pour que le jury puisse voir exactement quelles combinaisons ont été testées et comment le F1 CV évolue. GridSearchCV fait la même chose mais de manière opaque dans un projet pédagogique.

**"Le seuil 0.40 a été choisi comment ?"**
Par validation croisée sur X_train uniquement (5 folds). Le test set n'a pas été utilisé pour ce choix - seulement pour la mesure finale. C'est la bonne pratique pour éviter le biais de sélection.

---

*Plan rédigé pour une présentation de 20 minutes - CESI, avril 2026.*
