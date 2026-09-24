# Data Preprocessing

Data preprocessing converts unreliable, incompatible, or unnecessarily large raw data into a form suitable for mining.

## 1. Begin with the data model

A **data object** is an entity such as a customer, product, patient, or student. In a table, one row normally represents one object and each column represents an **attribute**.

Understanding the attribute type matters because it determines which comparisons and calculations are meaningful.

| Type     | Meaning                                   | Example             | What is meaningful?           |
| -------- | ----------------------------------------- | ------------------- | ----------------------------- |
| Nominal  | Unordered categories                      | Blood group, color  | Equality, counts, mode        |
| Binary   | Two nominal states                        | Smoker/non-smoker   | Equality and presence/absence |
| Ordinal  | Ordered categories with unknown gap sizes | Low, medium, high   | Ordering, median, percentiles |
| Interval | Equal intervals but no true zero          | Celsius temperature | Differences                   |
| Ratio    | Equal intervals and meaningful zero       | Weight, price       | Differences and ratios        |

A binary attribute is **symmetric** when both states are equally important. It is **asymmetric** when the presence of one state matters more, as in whether a patient has a rare disease.

A **discrete** attribute has a finite or countably infinite set of values. A **continuous** attribute can take any measured value within a range.

## 2. What goes wrong with raw data?

Raw data usually has one or more of these problems:

- **Incomplete:** required values are missing.
- **Noisy:** values contain random error, impossible readings, or unusual observations.
- **Inconsistent:** related fields disagree or sources use different conventions.
- **Redundant:** the same information appears more than once.
- **Too large:** the dataset contains more rows or attributes than the mining task needs.

The five preprocessing tasks respond to these problems:

```text
Cleaning       → repair values and records
Integration    → combine sources consistently
Transformation → change representation or scale
Reduction      → reduce size while preserving useful information
Discretization → replace continuous values with intervals
```

## 3. Data cleaning

Cleaning handles missing values, noise, outliers, inconsistencies, and duplicates. The appropriate treatment depends on the underlying cause; no universally optimal replacement method exists.

### 3.1 Missing values

Values may be missing because of equipment failure, misunderstanding, omission during entry, changed requirements, or deletion after a consistency check.

| Treatment | Suitable when | Main limitation |
|---|---|---|
| Ignore the tuple | Very few records are affected | Wastes data and may introduce bias |
| Fill manually | Dataset is tiny and the value is important | Slow and not scalable |
| Use a global constant | Missingness is itself informative | Creates an artificial category |
| Use mean or median | Numeric attribute needs a simple estimate | Reduces natural variation |
| Use class-specific mean | Classes have different distributions | Requires trustworthy class labels |
| Predict the value | Other attributes contain strong information | The prediction may add model bias |

For strongly skewed data or data containing extreme values, the median is generally preferable to the mean.

### 3.2 Noise and smoothing

**Noise** is random error or unwanted variation in a measured value. Binning smooths noise in three steps:

1. Sort the values.
2. Divide them into bins.
3. Replace values using the bin mean, median, or nearest boundary.

For the sorted values

$$
4,8,9,15,21,21,24,25,26,28,29,34,
$$

use equal-frequency bins of depth four:

$$
B_1=(4,8,9,15),\quad B_2=(21,21,24,25),\quad B_3=(26,28,29,34).
$$

The means are

$$
\bar{x}_{B_1}=9,\qquad
\bar{x}_{B_2}=22.75,\qquad
\bar{x}_{B_3}=29.25.
$$

Smoothing by bin means replaces each value in a bin by its mean. When whole-number output is required, the rounding rule should be stated explicitly.

For boundary smoothing, replace a value with the nearer endpoint of its bin. Therefore,

$$
B_1\rightarrow(4,4,4,15),\quad
B_2\rightarrow(21,21,25,25),\quad
B_3\rightarrow(26,26,26,34).
$$

Other ways to handle noise include regression, clustering, and computer-assisted human inspection.

### 3.3 Outliers

An **outlier** is far from the behavior of most observations. It may be:

- a **valid unusual case**, such as a CEO salary; or
- an **error**, such as a recorded age of 200.

Possible detection methods include boxplots, histograms, clustering, curve fitting, and hypothesis tests.

Outliers should not be removed automatically. In anomaly and fraud detection, an outlier may represent the required pattern.

## 4. Data integration

Integration combines several sources into one consistent dataset. The principal considerations are described below.

### Are these fields describing the same concept?

This is the **schema and entity-identification problem**. For example, `customer_id` in one table may be the same as `cust_no` in another.

### Is information repeated?

Attributes may be redundant or strongly correlated. For nominal variables, chi-square can test association. For numerical variables, correlation and covariance show how attributes vary together.

### Are these the same real-world record?

Duplicate tuples may differ slightly because of spelling, abbreviations, or formatting.

### Do the values use the same representation?

Conflicts may involve kilograms versus pounds, rupees versus dollars, or `DD/MM/YYYY` versus `MM/DD/YYYY`.

A complete examination answer should identify all four issues and provide a concrete example.

## 5. Data transformation

Transformation changes the representation without necessarily discarding the underlying information.

- **Smoothing** reduces noise.
- **Aggregation** summarizes detailed records.
- **Generalization** moves from detailed to higher concepts.
- **Attribute construction** derives a new useful feature.
- **Normalization** places numerical attributes on a comparable scale.

### 5.1 Min-max normalization

To map $v$ from $[\min_A,\max_A]$ to $[a,b]$,

$$
v'=\frac{v-\min_A}{\max_A-\min_A}(b-a)+a.
$$

Example: map $70$ from $[50,100]$ to $[0,1]$:

$$
v'=\frac{70-50}{100-50}=0.4.
$$

**Use when:** a fixed output range is required.

**Weakness:** a new extreme value changes the original minimum or maximum and therefore changes the scaling.

### 5.2 Z-score normalization

$$
v'=\frac{v-\mu_A}{\sigma_A}.
$$

If $v=70$, $\mu_A=50$, and $\sigma_A=10$, then

$$
v'=\frac{70-50}{10}=2.
$$

The result means that $70$ lies two standard deviations above the mean.

**Use when:** the range is unknown or attributes need to be compared relative to their distributions.

### 5.3 Decimal scaling

$$
v'=\frac{v}{10^j},
$$

where $j$ is the smallest integer for which

$$
\max |v'|<1.
$$

If the largest absolute value is $987$, then $j=3$ and $987$ becomes $0.987$.

## 6. Data reduction

Reduction produces a smaller representation that gives the same or nearly the same analytical result.

### 6.1 Reduce the number of attributes

**Feature selection** keeps only the attributes needed for the mining task. Exhaustively checking every subset is expensive, so common heuristics are:

- forward selection: begin empty and add useful attributes;
- backward elimination: begin with all attributes and remove weak ones;
- a combination of forward and backward steps.

PCA and wavelet transforms may create a lower-dimensional representation, but their detailed algorithms are not developed in the supplied slides.

### 6.2 Reduce the number of stored values or records

- Regression and log-linear models store model parameters.
- Histograms store bucket summaries rather than individual values.
- Clustering stores cluster representatives.
- Aggregation stores higher-level totals.
- Sampling stores a representative subset.

### 6.3 Sampling

Simple random sampling may miss a small but important class in skewed data. **Stratified sampling** first divides the data into important subpopulations and samples each one, preserving their approximate proportions.

## 7. Discretization

Discretization replaces a continuous attribute with a small number of intervals. This reduces the number of distinct values and supports algorithms that require categorical data.

### 7.1 Equal-width binning

For $k$ bins,

$$
w=\frac{x_{\max}-x_{\min}}{k}.
$$

Every interval has the same width, but the number of observations in each interval may be very different.

### 7.2 Equal-frequency binning

After sorting, place approximately the same number of observations in each bin. The bin widths may therefore differ.

For

$$
0,4,12,16,16,18,24,26,28,
$$

equal-frequency bins of depth three are

$$
(0,4,12),\quad(16,16,18),\quad(24,26,28).
$$

### 7.3 Concept hierarchies

A hierarchy replaces detailed values with higher-level concepts:

$$
\text{street}<\text{city}<\text{state}<\text{country}.
$$

Likewise, exact ages may be replaced by `young`, `middle-aged`, and `senior`.

Discretization forms intervals; a concept hierarchy gives those values or intervals progressively broader meaning.

## 8. Entropy-based discretization

Equal-width and equal-frequency methods ignore the class label. Entropy-based discretization chooses a split that makes the resulting groups as class-pure as possible.

### 8.1 Entropy

For a dataset $S$ with $c$ classes,

$$
H(S)=-\sum_{i=1}^{c}p_i\log_2 p_i.
$$

- $H(S)=0$ when all records belong to one class.
- Entropy increases as the class mixture becomes more even.
- For two equally represented classes, $H(S)=1$.

### 8.2 Evaluating a candidate split

A threshold $v$ divides $S$ into

$$
S_1=\{x:x\le v\},\qquad S_2=\{x:x>v\}.
$$

The weighted entropy after splitting is

$$
I(S_1,S_2)=\frac{|S_1|}{|S|}H(S_1)+\frac{|S_2|}{|S|}H(S_2).
$$

The information gain is

$$
\operatorname{Gain}(v,S)=H(S)-I(S_1,S_2).
$$

Choose the split with **maximum information gain**, which is the same as choosing the minimum weighted child entropy.

### 8.3 Worked example

Consider

$$
(0,P),(4,P),(12,P),(16,N),(16,N),(18,P),(24,N),(26,N),(28,N).
$$

There are four positive and five negative records, so

$$
H(S)=-\frac{4}{9}\log_2\frac{4}{9}-\frac{5}{9}\log_2\frac{5}{9}
\approx 0.991.
$$

Try the split $v=14$:

$$
S_1=\{(0,P),(4,P),(12,P)\},
$$

which is pure, so $H(S_1)=0$. The second group contains one positive and five negatives:

$$
H(S_2)=-\frac{1}{6}\log_2\frac{1}{6}-\frac{5}{6}\log_2\frac{5}{6}
\approx 0.650.
$$

Therefore,

$$
I(S_1,S_2)=\frac{3}{9}(0)+\frac{6}{9}(0.650)\approx0.433,
$$

and

$$
\operatorname{Gain}(14,S)=0.991-0.433\approx0.558.
$$

Repeat this calculation for candidate midpoints between consecutive **distinct** values. Do not place identical values on opposite sides of one threshold.

## 9. Numerical-solution format

### Binning

The solution should state whether the question specifies bin width, number of bins, or bin depth; form the bins after sorting; calculate replacements; and present the final sequence.

### Boxplot outliers

The solution should state the quartile convention, calculate $Q_1$, $Q_3$, IQR, and both fences after sorting, and identify values outside the fences.

### Normalization

The appropriate formula should be selected, all quantities should be substituted with their units or range, and the normalized value should be interpreted.

### Entropy split

The solution should show parent class counts, parent entropy, child class counts, child entropies, weighted entropy, and information gain. A final threshold without the supporting calculation is incomplete.

## 10. Summary

- Cleaning repairs data; transformation changes its representation.
- Integration resolves identities, redundancy, duplicates, and value conflicts.
- Min-max uses the range; z-score uses the mean and standard deviation.
- Reduction makes the representation smaller; discretization specifically turns continuous ranges into intervals.
- Entropy measures impurity; information gain measures the reduction in impurity.

## Source

Primary supplied material: [Data Preprocessing slides](../sources/2_DM_preprocessing.pdf).
