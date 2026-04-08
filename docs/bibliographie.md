# Bibliographie - Projet HumanForYou : Prédiction d'attrition par apprentissage automatique

---

## 1. Sources méthodologiques et théoriques

**[1] Breiman, L. (2001). Random Forests.**
*Machine Learning*, 45(1), 5-32.
DOI : [10.1023/A:1010933404324](https://link.springer.com/article/10.1023/A:1010933404324)

Reference fondatrice de l'algorithme Random Forest. Breiman y introduit le principe d'ensembles d'arbres de decision entraines sur des sous-echantillons aleatoires de features, et demontre que la generalisation depend de la force individuelle des arbres et de leur faible correlation mutuelle. Cette source justifie le choix du RF comme modele principal du projet et fonde theoriquement l'usage de `class_weight='balanced'` pour compenser le desequilibre de classes.

---

**[2] Cortes, C., & Vapnik, V. (1995). Support-Vector Networks.**
*Machine Learning*, 20(3), 273-297.
DOI : [10.1007/BF00994018](https://link.springer.com/article/10.1007/BF00994018)

Article original introduisant les machines a vecteurs de support (SVM) avec marge souple, permettant la classification sur des donnees non separables lineairement. Utilise pour justifier l'inclusion du SVM dans la comparaison des 5 familles d'algorithmes du notebook 03. Le SVM s'est revele le deuxieme meilleur modele (F1 CV = 0.7363) mais inferieur au RF et plus opaque en termes d'interpretabilite.

---

**[3] Kohavi, R. (1995). A Study of Cross-Validation and Bootstrap for Accuracy Estimation and Model Selection.**
*Proceedings of the 14th International Joint Conference on Artificial Intelligence (IJCAI)*, 1137-1145.
Disponible : [https://www.ijcai.org/Proceedings/95-2/Papers/016.pdf](https://www.ijcai.org/Proceedings/95-2/Papers/016.pdf)

Etude de reference sur la validation croisee. Kohavi demontre que la validation croisee stratifiee a 10 folds est la meilleure methode pour estimer la performance generalisable d'un modele sur des donnees reelles. Cette source justifie l'usage de StratifiedKFold a 5 folds dans les notebooks 03 et 04 pour l'evaluation des modeles et la selection du seuil de decision.

---

**[4] Cramér, H. (1946). Mathematical Methods of Statistics.**
Princeton University Press, p. 282.

Ouvrage de reference en statistiques mathematiques introduisant le V de Cramér, mesure d'association entre deux variables categorielles derivee du chi-deux de Pearson. Varie entre 0 (aucune association) et 1 (association parfaite). Utilise dans le notebook 01 pour quantifier l'association entre chaque variable categorielle (JobRole, MaritalStatus, BusinessTravel...) et la variable cible Attrition.

---

## 2. Sources sur les aspects techniques

**[5] Pedregosa, F., Varoquaux, G., Gramfort, A., et al. (2011). Scikit-learn: Machine Learning in Python.**
*Journal of Machine Learning Research (JMLR)*, 12, 2825-2830.
Disponible : [https://jmlr.org/papers/volume12/pedregosa11a/pedregosa11a.pdf](https://jmlr.org/papers/volume12/pedregosa11a/pedregosa11a.pdf)

Article de reference de la bibliotheque scikit-learn, utilisee tout au long du projet pour l'implementation des modeles (RandomForestClassifier, SVC, LogisticRegression, KNeighborsClassifier, DecisionTreeClassifier), le preprocessing (StandardScaler, LabelEncoder), la selection de features (mutual_info_classif, permutation_importance) et les metriques d'evaluation (f1_score, roc_auc_score, ConfusionMatrixDisplay).

---

**[6] Breiman, L. (2001). Random Forests [section Variable Importance].**
*Machine Learning*, 45(1), 5-32.
DOI : [10.1023/A:1010933404324](https://link.springer.com/article/10.1023/A:1010933404324)

La meme publication introduit egalement l'importance par permutation : en permutant aleatoirement les valeurs d'une feature, on mesure la chute de performance du modele, ce qui donne une estimation de l'importance reelle de la variable. Utilise dans le notebook 04 pour completer l'importance Gini et valider la robustesse des facteurs de depart identifies.

---

**[7] Molnar, C. (2022). Interpretable Machine Learning : A Guide for Making Black Box Models Explainable (2e edition).**
Disponible en ligne : [https://christophm.github.io/interpretable-ml-book/](https://christophm.github.io/interpretable-ml-book/)

Reference pratique sur l'interpretation des modeles de machine learning. Le chapitre sur l'importance par permutation a oriente le choix de cette methode comme complement a l'importance Gini dans le notebook 04 : Molnar y souligne que l'importance Gini peut surestimer les variables numeriques continues, ce qui justifie la verification par permutation.

---

**[8] Johnson, J. M., & Khoshgoftaar, T. M. (2019). Survey on deep learning with class imbalance.**
*Journal of Big Data*, 6(1), 27.
DOI : [10.1186/s40537-019-0192-5](https://journalofbigdata.springeropen.com/articles/10.1186/s40537-018-0151-6)

Survey sur les methodes de gestion du desequilibre de classes en apprentissage automatique. Cette source justifie le choix de `class_weight='balanced'` plutot que SMOTE : dans le contexte du projet (desequilibre modere 84/16, modele d'ensemble), la ponderation des classes est plus stable et evite d'introduire des donnees synthetiques qui pourraient ne pas refleter la realite des profils d'employes.

---

**[9] scikit-learn documentation. (2024). Permutation feature importance.**
Disponible : [https://scikit-learn.org/stable/modules/permutation_importance.html](https://scikit-learn.org/stable/modules/permutation_importance.html)

Documentation officielle de l'implementation de `permutation_importance` dans scikit-learn. Precise que l'importance par permutation doit etre calculee sur des donnees non utilisees pour l'entrainement (jeu de test ou validation) afin d'eviter un biais d'optimisme. Applique dans le notebook 04 sur X_test.

---

## 3. Sources éthiques et sociétales

**[10] High-Level Expert Group on Artificial Intelligence - Commission Européenne. (2019). Ethics Guidelines for Trustworthy AI.**
Publications Office of the European Union.
Disponible : [https://digital-strategy.ec.europa.eu/en/library/ethics-guidelines-trustworthy-ai](https://digital-strategy.ec.europa.eu/en/library/ethics-guidelines-trustworthy-ai)

Document de reference de la Commission Europeenne definissant les 7 exigences d'une IA digne de confiance : autonomie humaine, robustesse technique, confidentialite des donnees, transparence, non-discrimination, bien-etre societal et responsabilite. Ce cadre structure l'analyse ethique du projet (document ethique joint) et justifie les choix de conception : modele interpretable, conservation des donnees anonymisees, supervision humaine des decisions RH.

---

**[11] Bodie, M. T., Cherry, M. A., McCormick, M. L., & Tang, J. (2017). The Law and Policy of People Analytics.**
*University of Colorado Law Review*, 88, 961.
Disponible : [https://pmc.ncbi.nlm.nih.gov/articles/PMC9309597/](https://pmc.ncbi.nlm.nih.gov/articles/PMC9309597/)

Article juridique analysant les enjeux legaux de l'utilisation de l'IA dans les decisions RH (recrutement, retention, licenciement). Souligne les risques de discrimination indirecte lorsqu'un modele predictif utilise des variables correlee au genre, a l'age ou au statut marital. Cette source a oriente la decision de ne pas inclure `Gender` et `MaritalStatus` comme features principales dans les recommandations, meme si elles sont presentes dans le dataset.

---

**[12] Rodolfa, K. T., Lamba, H., & Ghani, R. (2021). Empirical observation of negligible fairness-accuracy tradeoffs in machine learning for public policy.**
*Nature Machine Intelligence*, 3, 896-904.

Etude empirique montrant que les arbitrages entre performance predictive et equite algorithmique sont souvent moins severes que presumes theoriquement. Oriente la reflexion sur la possibilite de deployer un modele performant sans sacrifier l'equite, a condition de surveiller les biais sur des sous-groupes (par departement, niveau hierarchique, genre) lors des predictions.

---

**[13] AIHR - Academy to Innovate HR. (2018). 9 Ways the GDPR Will Impact HR Data & Analytics.**
Disponible : [https://www.aihr.com/blog/general-data-protection-regulation-gdpr-impact-hr-analytics/](https://www.aihr.com/blog/general-data-protection-regulation-gdpr-impact-hr-analytics/)

Article pratique sur les implications du RGPD (Reglement General sur la Protection des Donnees) pour les projets d'analyse RH. Souligne que 81% des projets people analytics sont compromis par des problematiques ethiques et de confidentialite. Bien que les donnees du projet soient indiennes et non soumises au RGPD, ce cadre guide les bonnes pratiques : anonymisation des EmployeeID, non-conservation des scores individuels en clair, droit d'explication des decisions.

---

## 4. Sources spécifiques au projet

**[14] Alduayj, S. S., & Rajpoot, K. (2018). Predicting Employee Attrition using Machine Learning.**
*Proceedings of the 2018 International Conference on Intelligent Systems (IS)*, IEEE.

Etude appliquee sur la prediction d'attrition RH par machine learning sur un dataset IBM similaire au notre. Les auteurs comparent Random Forest, Decision Tree et Logistic Regression et concluent a la superiorite du RF pour ce type de probleme. Confirme la pertinence de notre selection de modele et fournit un point de comparaison pour nos metriques (F1, AUC).

---

**[15] Fallucchi, F., Coladangelo, M., Giuliano, R., & De Luca, E. W. (2020). Predicting Employee Attrition Using Machine Learning Approaches.**
*Applied Sciences*, 12(13), 6424.
DOI : [10.3390/app12136424](https://www.mdpi.com/2076-3417/12/13/6424)

Article recent comparant plusieurs algorithmes de ML sur un dataset d'attrition RH. Identifie les memes facteurs cles que notre analyse (salaire, anciennete, satisfaction) et souligne l'importance du desequilibre de classes comme principale difficulte technique. Valide notre approche methodologique et nos conclusions sur les facteurs de depart chez HumanForYou.

---

**[16] Kaggle / Vijay Choudhary. (2017). HR Analytics Case Study.**
Disponible : [https://www.kaggle.com/vjchoudhary7/hr-analytics-case-study](https://www.kaggle.com/vjchoudhary7/hr-analytics-case-study)

Source originale des donnees du projet. Dataset synthetique base sur des donnees RH reelles, comprenant 4410 employes d'une entreprise pharmaceutique indienne avec 15% de taux d'attrition annuel. Cite pour la transparence sur l'origine des donnees et les conditions de licence (usage academique libre).

---

*Bibliographie etablie dans le cadre du projet IA - HumanForYou - CESI, avril 2026.*
