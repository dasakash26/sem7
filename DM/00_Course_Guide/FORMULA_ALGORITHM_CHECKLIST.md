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
- Five-number summary and boxplot; $\operatorname{IQR}=Q_3-Q_1$; fences at $Q_1-1.5\operatorname{IQR}$ and $Q_3+1.5\operatorname{IQR}$. State the quartile convention used.
- Explain symmetric, positively skewed and negatively skewed data.
- Identify nominal, binary, ordinal, interval/ratio, discrete and continuous attributes.
- Missing values: ignore tuple, manual fill, global constant, mean/class mean or inferred value.
- Noise: bin means, bin medians, bin boundaries, regression or clustering.
- Integration: entity identification, redundancy/correlation, tuple duplication and value conflicts.
- Transformation: smoothing, aggregation, generalization and attribute/feature construction.
- Min-max normalization: $v'=\frac{v-\min_A}{\max_A-\min_A}(b-a)+a$.
- Z-score normalization: $v'=\frac{v-\mu_A}{\sigma_A}$.
- Decimal scaling: $v'=\frac{v}{10^j}$ with $\max|v'|<1$.
- Reduction: feature selection; wavelet/PCA dimensionality reduction; regression/log-linear models; aggregation, histograms, clustering and sampling.
- Equal-width/equal-frequency bins; discretization and concept hierarchies.
- Entropy: $H(S)=-\sum_i p_i\log_2p_i$.
- Split information and gain: $\operatorname{Gain}=H(S)-\sum_j\frac{|S_j|}{|S|}H(S_j)$.

## Association rules

- For disjoint $X$ and $Y$, $s(X\rightarrow Y)=\frac{\sigma(X\cup Y)}{N}$.
- $c(X\rightarrow Y)=\frac{\sigma(X\cup Y)}{\sigma(X)}$.
- $\operatorname{lift}(X\rightarrow Y)=\frac{c(X\rightarrow Y)}{s(Y)}$.
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
