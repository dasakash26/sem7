# DM PYQ overlap index

Use the 2025 and 2026 papers only for questions supported by the lecture notes.

## Highest-priority overlaps

1. Apriori numerical, support/confidence and strong rules.
2. FP-growth, maximal/closed itemsets and Apriori limitations.
3. Missing/noisy data, bin smoothing and outlier analysis.
4. Discretization, entropy and information gain.
5. KDD steps, mining issues, applications and mining functions.

## 2025 paper

### Fully covered

- 1(b): KDD steps.
- 1(d): business applications and required mining functions.
- 2(b): discretization and numerical concept hierarchy.
- 3(a): association task, confidence and anti-monotone support.
- 3(b): maximal itemsets and FP-growth.
- 3(c): Apriori and its limitations.

### Partly covered

- 1(a): data mining vs machine learning only; warehouse vs DBMS is out of scope.
- 2(a): missing values and mineable patterns only; cube measures are out of scope.
- 4(a): only the definition/context of classification is covered; the requested classification techniques and KNN are out of scope.
- 4(c): entropy/information gain only in the context of discretization, not decision trees.
- 5(a): clustering goal and outlier meaning only; k-means is out of scope.
- 6(b): sequential patterns are briefly defined, but no mining algorithm is taught.

### Out of scope

1(c), 2(c), 4(b), 5(b), 5(c) and 6(a), plus the uncovered portions listed above.

## 2026 paper

### Fully covered

- 1(a): mining issues and nominal/ordinal attributes.
- 2(b): entropy-based binning and smoothing by bin means.
- 2(c): noisy data, outliers and boxplot/histogram analysis.
- 3(a-d): market basket, confidence, Apriori numerical and Apriori vs FP-growth.
- 4(b)(ii): discretization.
- 4(b)(iii): entropy and information gain.

### Partly covered

- 2(a): integration and dimensionality reduction are covered; the curse of dimensionality is not explicitly taught.
- 4(a): classification is introduced, but decision-tree construction and split selection are not taught.

### Out of scope

1(b-d), 4(b)(i), 4(b)(iv), 5(a-c) and 6(a-b), plus the uncovered portions listed above.

## Paper cautions

- In the 2026 Apriori data, the last transaction repeats `O`; treat a transaction as a set, so it counts once.
- The 2026 entropy/information-gain material is in scope for discretization only. Do not expand it into decision-tree learning.
- If the teacher later supplies additional notes, update this boundary before using the excluded PYQ sections.
