# DM formula and algorithm checklist

## Introduction and KDD

- Define data mining and distinguish it from KDD and machine learning.
- Write the KDD sequence from cleaning to presentation.
- Classify a task as association, classification, regression, clustering, sequential pattern or deviation detection.
- List the kinds of data that can be mined.
- Explain descriptive vs predictive mining and class characterization vs discrimination.
- Explain applications, system components and major issues.

## Summaries and preprocessing

- Mean, weighted mean, median, mode, midrange, range, variance and standard deviation.
- Five-number summary and boxplot; `IQR = Q3 - Q1`; fences at `Q1 - 1.5 IQR` and `Q3 + 1.5 IQR`. State the quartile convention used.
- Explain symmetric, positively skewed and negatively skewed data.
- Identify nominal, binary, ordinal, interval/ratio, discrete and continuous attributes.
- Missing values: ignore tuple, manual fill, global constant, mean/class mean or inferred value.
- Noise: bin means, bin medians, bin boundaries, regression or clustering.
- Integration: entity identification, redundancy/correlation, tuple duplication and value conflicts.
- Transformation: smoothing, aggregation, generalization and attribute/feature construction.
- Min-max normalization: `v' = ((v-minA)/(maxA-minA))(newMax-newMin)+newMin`.
- Z-score normalization: `v' = (v-meanA)/sdA`.
- Decimal scaling: `v' = v/10^j` with `max(|v'|) < 1`.
- Reduction: feature selection; wavelet/PCA dimensionality reduction; regression/log-linear models; aggregation, histograms, clustering and sampling.
- Equal-width/equal-frequency bins; discretization and concept hierarchies.
- Entropy: `H(S) = -sum p_i log2(p_i)`.
- Split information and gain: `Gain = H(S) - weighted child entropy`.

## Association rules

- For disjoint `X` and `Y`, `support(X -> Y) = count(X union Y)/N`.
- `confidence(X -> Y) = support(X union Y)/support(X)`.
- `lift(X -> Y) = confidence(X -> Y)/support(Y)`.
- Anti-monotone property: an infrequent itemset has no frequent superset.
- Apriori: find `L1` -> join -> prune -> count -> repeat -> generate rules.
- Explain hash-tree support counting and Apriori drawbacks.
- FP-growth: order items -> build FP-tree/header table -> conditional pattern bases -> conditional trees.
- Distinguish frequent, closed and maximal itemsets.
- Explain contingency tables, subjective/objective measures and limitations of support/confidence/lift.
- Know positive, negative and independent association using the interest factor.

## Proof of readiness

Without notes, complete:

1. One KDD/process answer and one mining-issues answer.
2. Mean/variance and an IQR outlier problem.
3. Missing-value, bin-smoothing and normalization examples.
4. One entropy-based discretization split.
5. One complete Apriori numerical with strong-rule generation.
6. One FP-tree and a closed-vs-maximal explanation.
