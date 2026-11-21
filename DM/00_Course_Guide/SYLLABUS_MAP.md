# Syllabus Map

Read Chapters 1–7 in order. University module numbers follow a different sequence.

| Module                           | Chapter                                                                                                                                                                                  | Required topics                                                                                                                                                                                                    |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1. Data Warehousing              | [Chapter 3](../03_Data_Warehousing/Data_Warehousing.md#1-data-warehouse-concepts)                                                                                                        | Warehouse characteristics and components; ETL; marts and metadata; dimensional models and schemas; cubes and measures; OLAP; ROLAP/MOLAP/HOLAP; design, implementation, and preliminary cube computation           |
| 2. Foundations and Preprocessing | [Chapter 1](../01_Data_Mining_Foundations/Data_Mining_Foundations.md#1-definition-and-scope) and [Chapter 2](../02_Data_Preprocessing/Data_Preprocessing.md#1-begin-with-the-data-model) | Mining history, KDD, tasks, issues, characterization/discrimination, evolution; attribute types, statistical summaries, quality, cleaning, integration, transformation, reduction, discretization, and hierarchies |
| 3. Association Mining            | [Chapter 4](../04_Association_Rule_Mining/Association_Rule_Mining.md#1-market-basket-model)                                                                                              | Apriori, FP-growth, scalable and vertical methods; rule generation; closed/maximal patterns; multilevel and multidimensional rules; correlation, constraints, and high-dimensional patterns                        |
| 4. Classification and Prediction | [Chapter 5](../05_Classification_and_Prediction/Classification_and_Prediction.md#1-the-classification-problem)                                                                           | KNN, trees and split criteria, Bayes, rules and associative classification, backpropagation, regression, evaluation/model selection, accuracy improvement, multiclass, and semi-supervised learning                |
| 5. Clustering and Outliers       | [Chapter 6](../06_Clustering_and_Outlier_Analysis/Clustering_and_Outlier_Analysis.md#1-clusters-and-types-of-clustering)                                                                 | Data types and distances; k-means, PAM, CLARA/CLARANS; hierarchies, BIRCH, Chameleon; DBSCAN, OPTICS, DENCLUE; STING, CLIQUE; outlier types, challenges, statistical, proximity, and high-dimensional methods      |
| 6. Applications                  | [Chapter 7](../07_Application_Oriented_Mining/Application_Oriented_Mining.md#1-a-common-framework-for-specialized-data)                                                                  | Overview of text, biological, and time-series mining; sequential patterns for the past-paper short note                                                                                                            |

**CT 2 covers Chapters 3, 5, and 6.** Latest exam guidance: warehouse slide theory; all classification algorithms and numericals/metrics; k-means modifications/numericals plus short clustering theory. **PAM is excluded from CT 2** but remains in the full-course syllabus. Follow the [updated 10-hour guide](CT2_10_HOUR_GUIDE.md).

## Lecture Sources

| Lecture PDF | PDF pages checked | Main location |
|---|---:|---|
| `1_DM_Intro.pdf` | 45 | Chapter 1; quality and descriptive statistics are in Chapter 2 |
| `2_DM_preprocessing.pdf` | 40 | Chapter 2 |
| `3_Data_Warehouse.pdf` | 46 | Chapter 3 |
| `4_Association_Rules.pdf` | 54 | Chapter 4 |
| `5_Classification.pdf` | 73 | Chapter 5 |
| `6_Clustering.pdf` | 91 | Chapter 6 |

Original PDFs are in each chapter's `sources/` folder. The lectures do not develop every syllabus topic: selected textbook sections support warehouse implementation, association extensions, additional classifiers, advanced clustering/outliers, and applications. Each chapter records its sources and any corrected formulas or explicit assumptions. Applications are covered at overview depth.

The six decks contain **349 PDF pages**, including covers and repeated illustrations. The coverage check includes definitions, distinctions, formulas, algorithm steps, diagram meanings, examples, and limitations. Important slide content is developed in the chapters; repeated slide builds are consolidated. Incorrect formulas and overgeneralizations are corrected rather than reproduced.
