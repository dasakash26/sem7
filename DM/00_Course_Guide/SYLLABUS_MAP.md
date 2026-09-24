# DM notes scope map

Only material present in the three supplied lecture PDFs is in scope. The PDFs now live inside the corresponding topic folders.

## 1 Introduction and KDD

Source: [`01_Introduction_and_KDD/sources/1_DM_Intro.pdf`](../01_Introduction_and_KDD/sources/1_DM_Intro.pdf)

| Slides | Topics |
|---|---|
| 3-5 | Data mining/KDD; regression, classification, clustering, association and sequential-pattern tasks |
| 6-14 | Motivation, data growth, related fields, applications and mining-system components |
| 15-18 | KDD process: cleaning, integration, selection, transformation, mining, evaluation and presentation |
| 19-26 | Mineable data; descriptive/predictive mining; characterization/discrimination; frequent patterns, classification/regression, clustering and outliers |
| 27-28 | Technologies and major issues: methodology, interaction, scalability, data diversity, privacy and social impact |
| 29-34 | Data-quality dimensions and preprocessing overview |
| 35-44 | Mean, median, mode, skewness, variance, standard deviation, quartiles, IQR, boxplots, histograms, scatter plots, Loess and correlation patterns |
| 45 | Data mining vs machine learning |

## 2 Data preprocessing

Source: [`02_Data_Preprocessing/sources/2_DM_preprocessing.pdf`](../02_Data_Preprocessing/sources/2_DM_preprocessing.pdf)

| Slides | Topics |
|---|---|
| 2-7 | Data objects/datasets; nominal, binary, ordinal, quantitative, discrete and continuous attributes |
| 8-13 | Dirty data, preprocessing need, quality dimensions and major tasks |
| 14-20 | Cleaning; missing values; noisy data; bin-mean/median/boundary smoothing; outlier removal |
| 21-25 | Integration; entity matching, redundancy, correlation/covariance, duplicates and conflicts; transformation; min-max, z-score and decimal-scaling normalization |
| 26-32 | Reduction; feature selection; dimensionality-reduction overview; regression/log-linear models; histograms, clustering and sampling |
| 33-39 | Discretization; concept hierarchies; equal-width/equal-frequency binning; entropy, information gain and best split |
| 40 | Summary |

## 3 Association rule mining

Source: [`03_Association_Rule_Mining/sources/4_Association_Rules.pdf`](../03_Association_Rule_Mining/sources/4_Association_Rules.pdf)

| Slides | Topics |
|---|---|
| 2-16 | Market basket; transactions/itemsets; support; association rules; confidence; frequent/strong rules; anti-monotonicity; horizontal/vertical representation |
| 17-25 | Apriori; join/prune; support counting; hash tree/subset operation; drawbacks |
| 26-34 | FP-growth; frequency order; FP-tree/header table; conditional pattern bases and conditional trees |
| 35-37 | Rule generation from frequent itemsets |
| 38-43 | Maximal and closed frequent itemsets |
| 44-53 | Interestingness; contingency tables; support/confidence limitations; lift/interest factor; correlation, IS/cosine, inversion and null-addition properties |

## Explicitly out of scope

- Data warehousing, ETL, schemas, cubes and OLAP.
- Decision-tree induction, Bayesian classification, KNN, confusion matrices and neural networks.
- k-means, k-medoids/PAM, hierarchical clustering, DBSCAN and other clustering algorithms.
- Bag of words, detailed text mining, biological mining and time-series mining.

Brief mentions do not make a full algorithm examinable. For example, the notes define classification, clustering and sequential patterns but do not teach KNN, k-means or a sequential-pattern algorithm.
