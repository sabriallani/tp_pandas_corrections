# TP Pandas – Corrigés des exercices et rappels de notions

Ce dépôt contient le notebook **Correction_Pandas.ipynb**, qui propose des solutions détaillées aux exercices sur **Pandas** présents dans le fichier *Exercices pandas.pdf*. Pour accompagner ce notebook, ce document récapitule les notions essentielles de Pandas utilisées dans les exercices et fournit des rappels sur la manipulation de données avec cette bibliothèque.

## 1. Structure des données

Pandas repose sur deux structures de base :

- **Series** : vecteur unidimensionnel indexé. Une `Series` peut être créée à partir d’une liste, d’un tableau NumPy ou d’un dictionnaire. L’index permet de référencer les valeurs facilement (`ser = pd.Series([10,20,30], index=['a','b','c'])`).
- **DataFrame** : tableau bidimensionnel composé de colonnes nommées, chacune étant une `Series`. Un `DataFrame` peut être construit à partir d’un dictionnaire de listes/Series (`df = pd.DataFrame({'col1': ser1, 'col2': ser2})`) ou par concaténation de Series (`pd.concat([ser1, ser2], axis=1)`).

Dans les exercices, ces structures sont utilisées pour représenter des listes de lettres, des entiers aléatoires, des dates ou des valeurs issues de fichiers CSV.

## 2. Indexation et sélection

Pandas propose plusieurs méthodes pour accéder aux données :

- **Positionnelle** : `iloc[]` utilise des indices entiers. Par exemple, `df.iloc[0, 1]` renvoie la valeur de la première ligne, deuxième colonne. Pour inverser l’ordre des lignes ou colonnes, on peut utiliser `df.iloc[::-1, :]`.
- **Label** : `loc[]` sélectionne par étiquette de lignes ou de colonnes. Ex. :`df.loc[:, ['a','b']]` pour deux colonnes nommées `a` et `b`.
- **Attributs** : lorsqu’une colonne a un nom valide (sans espace ni caractère spécial), on peut y accéder via `df.colname`.
- **Méthodes de séries** : `ser.loc[[indices]]`, `ser.take([positions])` permettent d’extraire des sous‐ensembles.

## 3. Opérations courantes

- **Union/intersection/différence** : NumPy fournit `np.union1d`, `np.intersect1d` et `np.setdiff1d` pour combiner des séries numériques.
- **Statistiques descriptives** : `ser.mean()`, `ser.median()`, `ser.std()`, `ser.quantile([0.25,0.5,0.75])` pour les quartiles, et `df.describe()` pour un résumé global.
- **Comptage** : `ser.value_counts()` renvoie la fréquence de chaque valeur ; très utile pour obtenir les 2 valeurs les plus fréquentes ou pour remplacer les autres par “Other”.
- **Découpage en classes** : `pd.qcut()` découpe une série en quantiles et associe une étiquette à chaque quantile.
- **Binning** : `pd.cut()` permet également de discrétiser des variables continues en classes par intervalles fixes.
- **Concaténation / empilement** : `pd.concat()` assemble plusieurs DataFrames ou Series horizontalement (`axis=1`) ou verticalement (`axis=0`). L’ancienne méthode `ser1.append(ser2)` (empilement) est déconseillée à partir de Pandas 2.0.
- **Fusion / jointure** : `df.merge()` n’est pas utilisée dans ces exercices mais constitue la base des jointures sur clés.
- **Groupement** : `df.groupby('col').agg(func)` calcule des agrégats par groupe. Par ex. `weights.groupby(fruit).mean()` renvoie le poids moyen de chaque fruit.
- **Tri et indexation avancée** : `ser.sort_values()` trie une série, `ser.argsort()` retourne les positions triées. L’indexation avancée permet de récupérer la position des valeurs présentes dans une autre série (`np.where(i == ser)[0][0]`).
- **Fenêtres glissantes** : pour créer des sous‐séries de longueur fixe, on peut utiliser des compréhensions ou `rolling()` (non illustré ici) ; la fonction personnalisée `gen_strides` de l’exercice 34 découpe une série en fenêtres de taille et pas donnés.
- **Applications de fonctions** : `map()`, `apply()` et `applymap()` appliquent des fonctions élémentaires respectivement sur chaque élément d’une `Series`, chaque colonne/ligne d’un DataFrame et chaque cellule.

## 4. Dates et séries temporelles

Pandas intègre des outils pour manipuler les dates :

- **Conversion** : `pd.to_datetime()` transforme des chaînes en objets datetime. La bibliothèque `dateutil.parser.parse` est une alternative flexible.
- **Attributs** : une fois convertie, une `DatetimeIndex` permet d’accéder aux attributs `year`, `month`, `day`, `dayofweek`, etc. Ex. :`ser_ts.dt.day` renvoie le jour du mois pour chaque date.
- **Resampling** : `ser.resample('D').ffill()` re‐échantillonne une série temporelle selon une fréquence donnée (ici quotidienne) et comble les valeurs manquantes par la valeur précédente (`ffill`) ou suivante (`bfill`).
- **Autocorrélation** : `ser.autocorr(lag)` calcule le coefficient d’autocorrélation pour un décalage donné. Dans l’exercice 31, on cherche le lag ayant la plus forte corrélation.

## 5. Gestion des valeurs manquantes

- **Détection** : `df.isnull().values.any()` indique si un DataFrame contient des valeurs manquantes. `df.apply(lambda x: x.isnull().sum())` compte les valeurs manquantes par colonne.
- **Remplacement** : pour substituer les valeurs manquantes, on peut utiliser `fillna(value)` ou, comme dans l’exercice 42, appliquer des fonctions différentes selon la colonne via un dictionnaire de fonctions (`np.nanmean`, `np.nanmedian`).

## 6. Affichage et formatage

- **Options d’affichage** : `pd.set_option('display.max_columns', n)` ou `display.max_rows` ajustent respectivement le nombre de colonnes ou de lignes affichées. `pd.set_option('display.float_format', lambda x: '%.2f' % x)` contrôle la représentation des nombres flottants.
- **Style** : l’interface `DataFrame.style` permet un formatage riche, par exemple pour afficher une colonne en pourcentage (`'{0:.2%}'.format`).

## 7. Divers

- **Création d’une clé primaire** : on peut concaténer plusieurs colonnes pour former un index combiné (`df.index = df.Manufacturer + '_' + df.Model + '_' + df.Type`).
- **Remplacement conditionnel** : les méthodes `np.where()` et `Series.where()` appliquent une condition pour sélectionner ou transformer des valeurs.
- **Outliers** : la fonction `cap_outliers()` proposée dans l’exercice 53 tronque les valeurs extrêmes selon des percentiles donnés.
- **One-hot encoding** : `pd.get_dummies()` convertit une variable catégorielle en colonnes binaires (0/1), utile pour le machine learning.
- **Fonctions personnalisées** : beaucoup d’exercices utilisent des compréhensions, `map()` ou `apply()` pour implémenter des règles spécifiques (classement des mots par nombres de voyelles, extraction d’emails valides, calcul d’erreur quadratique moyenne, etc.).

## Contenu du notebook

Le notebook **Correction_Pandas.ipynb** reprend chacun des exercices du PDF et fournit du code Python permettant de :

1. Créer et manipuler des `Series` et des `DataFrames` à partir de différentes sources.
2. Réaliser des opérations d’indexation, de filtrage, de transformation et d’agrégation.
3. Convertir et analyser des données de type date et série temporelle.
4. Importer et nettoyer des jeux de données externes (CSV) en utilisant des techniques de chargement paresseux (`chunksize`) et de remplissage des valeurs manquantes.
5. Appliquer des méthodes statistiques et calculer des métriques (quantiles, autocorrélation, distance euclidienne). 
6. Formater et présenter les résultats de manière lisible (arrondissement des valeurs, format pourcentage).

N’hésitez pas à parcourir les cellules une à une : chaque exercice est précédé d’un en-tête indiquant l’objectif, et la solution est commentée pour faciliter la compréhension.
