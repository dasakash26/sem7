# CT 2: 10-Hour Guide

## 1. Updated Exam Focus

Based on your latest exam guidance:

| Area | Expected question style | Priority |
|---|---|---|
| Data Warehousing | Slide-based theory, roughly 8/10 marks | Definitions, comparisons, diagrams, explanations |
| Classification | All algorithms; KNN/tree numericals; confusion-matrix and ROC calculations; 8/10/12 marks | Algorithm steps and numerical practice |
| Clustering | K-means and its modifications/numericals, plus a short theory question; 8/10/12 marks | K-means practice; **PAM excluded** |

These are question-level mark ranges, not a confirmed total-paper pattern. Use the latest guidance over the older, broader preparation plan.

## 2. Schedule

**10 hours including 50 minutes of breaks.** Video time is inside each block; keep total watching below two hours.

| Elapsed time | Focus | Finish |
|---|---|---|
| 00:00–00:40 | CT 1 prerequisites | Labels, attribute types, mean/median, distances, scaling, probability, entropy and weighted averages |
| 00:40–02:05 | Warehouse theory | All theory in the warehouse slides; practise diagrams and structured answers |
| 02:05–02:15 | Break | 10 min |
| 02:15–03:55 | Decision trees | Hunt, legal tests, Gini/entropy/gain/gain ratio, Object Table and Buys Computer |
| 03:55–04:05 | Break | 10 min |
| 04:05–05:25 | KNN, metrics and ROC | KNN calculation; confusion metrics; threshold table, ROC plot and AUC |
| 05:25–05:45 | Meal | 20 min |
| 05:45–07:20 | K-means and modifications | Full iterations, changed seeds/data/distance, SSE, bisecting k-means and k-modes |
| 07:20–07:30 | Break | 10 min |
| 07:30–08:35 | Remaining classification algorithms | Review every method's mechanism, steps, example and limitations |
| 08:35–09:10 | Short clustering theory + recall | Types, distances, limitations, method comparisons; revise warehouse headings |
| 09:10–10:00 | Closed-book mini-test | Tasks below; check and repair errors |

For the refresher, use [attribute types](../02_Data_Preprocessing/Data_Preprocessing.md#1-begin-with-the-data-model), [distances](../06_Clustering_and_Outlier_Analysis/Clustering_and_Outlier_Analysis.md#2-distance-similarity-and-data-types), and [split criteria](../05_Classification_and_Prediction/Classification_and_Prediction.md#4-impurity-and-split-selection). Defer Apriori, FP-growth and extensive CT 1 revision.

## 3. English Videos → Notes

Watch only where needed. Videos introduce topics; the notes supply remaining slide details.

### Warehouse Theory — [Chapter 3](../03_Data_Warehousing/Data_Warehousing.md)

| English video                                                                      | Complete from notes                                                                           |
| ---------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| [Warehouse overview — IBM Technology](https://www.youtube.com/watch?v=k4tK2ttdSDg) | §§1–4: characteristics, OLTP/OLAP, three tiers, managers, ETL, marts, metadata                |
| [Star vs snowflake — codebasics](https://www.youtube.com/watch?v=hQvCOBv_-LE)      | §§5–6: also constellation, facts/dimensions, grain, hierarchies, cubes and measure categories |
| [OLAP operations — SmartSlides](https://www.youtube.com/watch?v=GmQmuphvKus)       | §§7–8, 10: operations, ROLAP/MOLAP/HOLAP, tool requirements, benefits and difficulties        |

Prepare each theory answer as **definition → labeled diagram/comparison → explanation → example**. Draw three-tier architecture and all three schemas from memory. Follow the slide theory first; §9 contains additional implementation detail.

### Classification — [Chapter 5](../05_Classification_and_Prediction/Classification_and_Prediction.md)

| English video — StatQuest | Complete from notes |
|---|---|
| [Entropy](https://www.youtube.com/watch?v=YtebGVx-Fxw), if rusty; [Decision trees](https://www.youtube.com/watch?v=_L39rN6gz7Y) | §§1, 3–7: Hunt, all split criteria, both worked trees, stopping |
| [KNN](https://www.youtube.com/watch?v=HVXime0nQeI) | §2: distances, K choice, scaling, voting and ties |
| [Confusion matrix](https://www.youtube.com/watch?v=Kdsp6soqA7o) | §§13–14: accuracy, precision, recall, specificity, FPR, F1, weighted accuracy and evaluation methods |
| [ROC/AUC](https://www.youtube.com/watch?v=4jRBRDbJemM) | §15: threshold counts, tied scores, curve and area |

**Tree video uses Gini:** also calculate entropy, information gain, gain ratio and majority error. **F1/accuracy come from a confusion matrix; ROC plots TPR against FPR across thresholds.**

“All algorithms” means keep §§8–12 in the review: Bayes, rules/associative classification, backpropagation, regression, ensembles, multiclass and semi-supervised learning. Prioritize the detailed methods actually taught; do not silently exclude them because the videos omit them.

### K-Means + Short Theory — [Chapter 6](../06_Clustering_and_Outlier_Analysis/Clustering_and_Outlier_Analysis.md)

| English video | Complete from notes |
|---|---|
| [K-means — StatQuest](https://www.youtube.com/watch?v=4b5d3muPQmA) | §§1–4: types, distances, assignments, updates, SSE, initialization, limitations and variants |

Practise:

- Small dry run, then the **books numerical**, showing every assignment and center update until stable.
- Repeat a small dataset with different initial centers; compare the result and SSE.
- Change/add a point and recompute assignments and means; follow the specified distance and update rule.
- Read **bisecting k-means and k-modes**. “Modifications” was not specified more narrowly, so cover both variants and changed numerical conditions.
- For short theory: cluster types, distances, k-means limitations, and basic hierarchical/density-method comparisons (§§1–3, 6–7, 10). Full hierarchy/DBSCAN numericals are lower priority under this update.

**Skip PAM, medoid-swap practice and the PAM ice-cream numerical for this CT 2.** Keep them in the full-course notes. The books question uses a declared Manhattan-assignment/mean-update convention; standard k-means uses squared Euclidean error.

## 4. Final 50 Minutes

| Minutes | Closed-book task |
|---|---|
| 0–10 | Warehouse theory answer with diagram |
| 10–25 | Decision-tree split calculation and branches |
| 25–35 | Confusion metrics or ROC threshold calculation; recall KNN steps |
| 35–45 | Small k-means iteration/update problem |
| 45–50 | Check and correct errors |

Use the [PYQ index](PYQ_TOPIC_INDEX.md) for datasets, selecting only questions matching this updated scope. Cut optional videos before cutting numerical practice.
