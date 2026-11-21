# Chapter 2. Data Preprocessing

Chapter 1 defined the intended knowledge. Data preprocessing now converts unreliable, incompatible, or unnecessarily large raw observations into a representation suitable for discovering it. The same cleaned retail records can later support warehouse summaries, basket rules, customer models, or customer clusters; the preparation choices depend on which task is intended.

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

Nominal, binary, and ordinal attributes are **qualitative/categorical**; interval and ratio measurements are **quantitative/numerical**. A numerical code does not make a category quantitative: postal codes identify locations, and satisfaction scores encode ordered categories rather than measured equal gaps. A Boolean attribute is binary with true/false states.

A binary attribute is **symmetric** when both states are equally important. It is **asymmetric** when the presence of one state matters more, as in whether a patient has a rare disease.

A **discrete** attribute has a finite or countably infinite set of values. A **continuous** attribute can take any measured value within a range.

The distinction between interval and ratio attributes is determined by the zero point. A zero Celsius reading does not indicate the absence of temperature; consequently, ratios such as $20^\circ\mathrm{C}/10^\circ\mathrm{C}$ are not meaningful. A zero weight indicates the absence of weight, so ratio comparisons are meaningful for ratio-scaled attributes.

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

Cleaning primarily improves quality, whereas reduction primarily improves storage and computational efficiency. Transformation may improve either compatibility or suitability for a specific algorithm.

## 3. Data Quality

Mining quality depends on input quality.

| Dimension | Question to ask |
|---|---|
| Accuracy | Does the value correctly represent reality? |
| Completeness | Are the required records and values present? |
| Consistency | Do related values agree? |
| Timeliness | Is the data recent enough? |
| Believability | Is the source trustworthy? |
| Value added | Does the data improve the decision? |
| Interpretability | Are meaning, scale, and units clear? |
| Accessibility | Can authorized users obtain and use it? |

## 4. Descriptive Statistics

### Central tendency

For observations $x_1,x_2,\ldots,x_n$, the arithmetic mean is

$$
\bar{x}=\frac{1}{n}\sum_{i=1}^{n}x_i.
$$

With weights $w_i$, the weighted mean is

$$
\bar{x}_w=\frac{\sum_{i=1}^{n}w_i x_i}{\sum_{i=1}^{n}w_i}.
$$

- The **median** is the middle sorted value, or the mean of the two middle values.
- The **mode** is the most frequent value.
- The **midrange** is $\frac{x_{\min}+x_{\max}}{2}$.

For some moderately skewed unimodal distributions, the following rough empirical approximation is used; it is not an identity:

$$
\text{mean}-\text{mode}\approx3(\text{mean}-\text{median}).
$$

### Interpreting skewness

- A symmetric unimodal distribution often has coincident mean, median, and mode; symmetry alone does not guarantee a unique central mode.
- A typical unimodal right-skewed distribution has mode $<$ median $<$ mean.
- A typical unimodal left-skewed distribution has mean $<$ median $<$ mode.

The mean is pulled toward the long tail because it is sensitive to extreme values.

### Variance and standard deviation

For a population of size $N$,

$$
\sigma^2=\frac{1}{N}\sum_{i=1}^{N}(x_i-\mu)^2,
\qquad
\sigma=\sqrt{\sigma^2}.
$$

For a sample of size $n$,

$$
s^2=\frac{1}{n-1}\sum_{i=1}^{n}(x_i-\bar{x})^2,
\qquad
s=\sqrt{s^2}.
$$

Variance measures squared spread; standard deviation expresses the spread in the original unit. The algebraically equivalent computational forms are

$$
s^2=\frac{\sum_i x_i^2-(\sum_i x_i)^2/n}{n-1},
\qquad
\sigma^2=\frac{\sum_i x_i^2}{N}-\mu^2.
$$

Keeping count, sum, and sum of squares lets partitions combine their summaries without storing every observation. For $[2,4,4,6,9]$, these are 5, 25, and 153, giving sample variance $(153-625/5)/4=7$. This is why variance and standard deviation are algebraic cube measures. Subtraction of nearly equal large numbers can lose numerical precision; stable incremental implementations are preferable for computation.

### Quartiles and boxplot outliers

The five-number summary is

$$
x_{\min},\ Q_1,\ \text{median},\ Q_3,\ x_{\max}.
$$

The interquartile range is

$$
\operatorname{IQR}=Q_3-Q_1.
$$

Potential outliers lie outside

$$
\left[Q_1-1.5\operatorname{IQR},\;Q_3+1.5\operatorname{IQR}\right].
$$

Always sort the data and state the quartile convention. Different software may interpolate quartiles differently.

### Choosing a plot

- Use a **histogram** to examine the shape of one numerical distribution.
- Use a **boxplot** to compare spread and identify possible outliers.
- Use a **scatter plot** to inspect the relationship between two variables.
- Add a **Loess curve** when the relationship may be smooth but nonlinear.

### Trimmed Means and Grouped Medians

A trimmed mean sorts the observations, removes a declared fraction from each tail, and averages the remainder. For $[1,2,3,4,100]$, removing one value from each end gives mean $(2+3+4)/3=3$, compared with the untrimmed mean 22. Trimming reduces sensitivity to extremes but discards observations and needs a stated trimming rule.

For grouped data, estimate the median within the median class by interpolation:

$$
\widetilde{x}=L+\frac{N/2-F}{f_m}h.
$$

$L$ is the lower boundary of the median class, $F$ is cumulative frequency before it, $f_m$ its frequency, and $h$ its width. The approximation assumes a roughly uniform distribution inside that class. For classes $[0,10),[10,20),[20,30)$ with frequencies 2,6,2, the median class is $[10,20)$ and the estimate is $10+(5-2)10/6=15$.

A distribution can be unimodal, bimodal, or multimodal according to the number of peaks. A unique mode need not exist. For unequal-width histogram bins, use frequency density as height when the rectangle area is intended to represent frequency; raw heights alone would distort comparisons.

### Complete Summary Example

For sorted data $[2,4,4,6,9]$, $n=5$, mean $=25/5=5$, median $=4$, mode $=4$, midrange $=(2+9)/2=5.5$, and range $=7$. Squared deviations from the mean sum to $9+1+1+1+16=28$. Population variance is $28/5=5.6$; sample variance is $28/4=7$. Their square roots have the original measurement unit.

A Tukey boxplot marks the median within a box from $Q_1$ to $Q_3$, extends whiskers to the most extreme observations inside the 1.5-IQR fences, and plots outside values individually. Whiskers need not reach the overall minimum and maximum when outliers exist.

LOESS fits low-degree local polynomials to neighboring observations with distance-dependent weights. Its span controls how much data enter each local fit; its degree controls local flexibility. A small span can follow noise; a large span smooths more strongly. A curved scatter pattern can have weak linear correlation while still showing a strong relationship.

## 5. Data cleaning

Cleaning handles missing values, noise, outliers, inconsistencies, and duplicates. The appropriate treatment depends on the underlying cause; no universally optimal replacement method exists.

### Missing values

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

### Noise and smoothing

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

For median smoothing, the three bin medians are 8.5, 22.5, and 28.5. Replace each of a bin's four values by that median. For boundary ties, state whether the lower or upper endpoint is chosen.

Other ways to handle noise include regression, clustering, and computer-assisted human inspection.

Regression smoothing replaces a value with that predicted by a fitted relationship. Clustering identifies values that are far from their nearest cluster as possible noise or outliers. These methods are more suitable than simple binning when the data contains a clear trend or natural groups.

### Outliers

An **outlier** is far from the behavior of most observations. It may be:

- a **valid unusual case**, such as a CEO salary; or
- an **error**, such as a recorded age of 200.

Possible detection methods include boxplots, histograms, clustering, curve fitting, and hypothesis tests.

Outliers should not be removed automatically. In anomaly and fraud detection, an outlier may represent the required pattern.

## 6. Data integration

Integration combines several sources into one consistent dataset. 

### Are these fields describing the same concept?

This is the **schema and entity-identification problem**. For example, `customer_id` in one table may be the same as `cust_no` in another.

### Correlation and Covariance

For paired numerical observations, sample covariance and Pearson correlation are

$$
\operatorname{cov}(X,Y)=\frac{\sum_i(x_i-\bar x)(y_i-\bar y)}{n-1},
\qquad
r=\frac{\sum_i(x_i-\bar x)(y_i-\bar y)}
{\sqrt{\sum_i(x_i-\bar x)^2\sum_i(y_i-\bar y)^2}}.
$$

Covariance has units equal to the product of the variables' units. Correlation is dimensionless and lies in $[-1,1]$ when both variables have nonzero variance. For $X=[1,2,3]$ and $Y=[2,4,6]$, the sample covariance is 2 and $r=1$. Positive correlation means larger values tend to occur together; negative correlation means larger values of one variable tend to accompany smaller values of the other. The sign gives direction, while the magnitude measures linear strength. A constant attribute makes Pearson correlation undefined. Zero correlation excludes a linear trend, not every kind of dependence.

For categorical counts, the chi-square statistic compares observed and independence-expected frequencies:

$$
\chi^2=\sum_{i,j}\frac{(O_{ij}-E_{ij})^2}{E_{ij}},
\qquad E_{ij}=\frac{O_{i+}O_{+j}}N.
$$

In a two-by-two table $[[30,10],[10,50]]$, row totals are 40,60, column totals 40,60, and $N=100$. Expected counts are $[[16,24],[24,36]]$. Thus $\chi^2=196(1/16+1/24+1/24+1/36)=34.027778$. A large statistic is evidence against independence under the test assumptions; it is not an effect-size measure or a causal explanation. Degrees of freedom are $(r-1)(c-1)$ for an $r$-by-$c$ table, and sparse expected cells require care.

### Is information repeated?

Redundant attributes repeat information and can distort analysis or increase computation. Age and date of birth may be redundant when the reference date is known. Correlation and covariance help identify related numerical attributes; chi-square assesses association between categorical attributes.

### Are these the same real-world record?

Duplicate tuples may differ slightly because of spelling, abbreviations, or formatting. Check integrity rules as well: a **functional dependency** such as CustomerID → DateOfBirth requires records with the same ID to agree on date of birth. Conflicting values need reconciliation rather than arbitrary averaging.

### Do the values use the same representation?

Conflicts may involve kilograms versus pounds, rupees versus dollars, or `DD/MM/YYYY` versus `MM/DD/YYYY`.

## 7. Data transformation

Transformation changes the representation without necessarily discarding the underlying information.

- **Smoothing** reduces noise.
- **Aggregation** summarizes detailed records.
- **Generalization** moves from detailed to higher concepts.
- **Attribute construction** derives a new useful feature.
- **Normalization** places numerical attributes on a comparable scale.

For example, daily sales may be aggregated into monthly sales, individual cities may be generalized to states, and a new attribute such as body-mass index may be constructed from height and weight.

### Min-max normalization

To map $v$ from $[\min_A,\max_A]$ to $[a,b]$,

$$
v'=\frac{v-\min_A}{\max_A-\min_A}(b-a)+a.
$$

Example: map $70$ from $[50,100]$ to $[0,1]$:

$$
v'=\frac{70-50}{100-50}=0.4.
$$

**Use when:** a fixed output range is required.

**Weakness:** extreme training values compress the rest of the range. A new value outside the fitted training range maps outside the target interval unless an explicit clipping policy is used; do not silently refit the transform using test data.

### Z-score normalization

$$
v'=\frac{v-\mu_A}{\sigma_A}.
$$

If $v=70$, $\mu_A=50$, and $\sigma_A=10$, then

$$
v'=\frac{70-50}{10}=2.
$$

The result means that $70$ lies two standard deviations above the mean.

**Use when:** the range is unknown or attributes need to be compared relative to their distributions.

### Decimal scaling

$$
v'=\frac{v}{10^j},
$$

where $j$ is the smallest integer for which

$$
\max |v'|<1.
$$

If the largest absolute value is $987$, then $j=3$ and $987$ becomes $0.987$.

## 8. Data reduction

Reduction produces a smaller representation that gives the same or nearly the same analytical result.

### Reduce the number of attributes

**Feature selection** keeps only the attributes needed for the mining task. Exhaustively checking every subset is expensive, so common heuristics are:

- forward selection: begin empty and add useful attributes;
- backward elimination: begin with all attributes and remove weak ones;
- a combination of forward and backward steps.

Feature selection retains original attributes. Dimensionality-reduction methods such as PCA may instead construct new reduced attributes from combinations of the original attributes.

### Reduce the number of stored values or records

- Regression and log-linear models store model parameters.
- Histograms divide a numerical range into buckets and store counts, sums, or averages rather than individual values.
- Clustering stores representatives and, where needed, sizes or spreads. It compresses well when groups are compact; a diffuse dataset may require many representatives or lose substantial detail.
- Aggregation stores higher-level totals.
- Sampling stores a representative subset.

The objective is not exact reconstruction of every record. The objective is preservation of the information required for the intended mining result.

### Sampling

Sampling selects a representative subset when processing the complete dataset is expensive.

| Method | Procedure | Suitability |
|---|---|---|
| Simple random sampling | Every record has equal selection probability | Uniform data; may miss rare classes |
| Stratified sampling | Partition into groups, then sample each group separately | Skewed data or important minority classes |
| Cluster sampling | Select some groups and retain their records, or sample within selected groups | Accessing complete groups is cheaper; differs from sampling every stratum |

For example, a dataset with 900 regular and 100 fraudulent transactions produces approximately 90 regular and 10 fraudulent transactions in a proportional stratified sample of 100 records. Thus, the original class distribution is preserved.

When a minority class is deliberately over-sampled, the changed class distribution must be considered while interpreting probabilities or support counts.

### The Curse of Dimensionality

As the number of attributes grows, space becomes sparse, neighborhoods become less informative, and the number of possible attribute subsets grows exponentially. A grid with ten intervals per dimension has $10^d$ cells in $d$ dimensions: two dimensions give 100 cells, ten dimensions give ten billion. Keeping average occupancy fixed requires rapidly increasing data.

Reduce irrelevant/redundant features, construct useful lower-dimensional representations, use domain-informed distances, or search for clusters in selected subspaces. More features can add useful signal, but collecting them without adequate examples or regularization can degrade results. This explains both KNN's distance problems and the later need for subspace clustering.

### Principal Components and Other Reduced Representations

**Principal component analysis (PCA)** constructs orthogonal directions that preserve maximum sample variance in successive components.

1. Select numerical features and decide whether different units require scaling.
2. Center the training matrix by subtracting each feature mean.
3. Form its covariance matrix, or use a singular-value decomposition.
4. Order directions by decreasing eigenvalue (explained variance).
5. Keep a declared number of components and project observations onto those directions.
6. Apply the fitted centering, scaling, and directions unchanged to new data.

For $(1,1),(2,2),(3,3)$, the mean is $(2,2)$ and the sample covariance matrix is $[[1,1],[1,1]]$. Its eigenvalues are 2 and 0. Keeping direction $(1,1)/\sqrt2$ gives scores $-\sqrt2,0,\sqrt2$ and retains all sample variance in one dimension. Generally, discarded components lose information. High variance need not mean high predictive relevance, and PCA changes feature interpretation.

A simple Haar-style transform replaces pairs by their averages and half-differences. For $[2,4,6,8]$, pair averages are 3,7 and details are -1,-1; transforming the averages gives overall average 5 and detail -2. The four coefficients $[5,-2,-1,-1]$ reconstruct the original values exactly under this convention. Dropping small details gives compression with approximation error. Orthonormal wavelet conventions use different scaling; state the convention used.

Numerosity reduction instead replaces observations by model parameters or summaries. A regression stores a fitted relation; a log-linear model approximates multidimensional categorical counts through selected interactions. Histograms, samples, and cluster representatives store less detailed information. Their suitability depends on preserving the analysis of interest, not merely reducing bytes.

## 9. Discretization

Discretization replaces a continuous attribute with a small number of intervals. This reduces the number of distinct values and supports algorithms that require categorical data.

### Equal-width binning

For $k$ bins,

$$
w=\frac{x_{\max}-x_{\min}}{k}.
$$

Every interval has the same width, but the number of observations in each interval may be very different.

For example, when most values lie in a narrow range, equal-width binning may produce one crowded bin and several nearly empty bins. Equal-frequency binning avoids this imbalance but can produce intervals of very unequal widths.

### Equal-frequency binning

After sorting, place approximately the same number of observations in each bin. The bin widths may therefore differ.

For

$$
0,4,12,16,16,18,24,26,28,
$$

equal-frequency bins of depth three are

$$
(0,4,12),\quad(16,16,18),\quad(24,26,28).
$$

### Concept hierarchies

A hierarchy replaces detailed values with higher-level concepts:

$$
\text{street}<\text{city}<\text{state}<\text{country}.
$$

Likewise, exact ages may be replaced by `young`, `middle-aged`, and `senior`.

Discretization forms intervals; a concept hierarchy gives those values or intervals progressively broader meaning.

Concept hierarchies support analysis at different levels of abstraction. For example, a rule concerning a city can be generalized to a state or country when a broader pattern is required.

## 10. Entropy-based discretization

Equal-width and equal-frequency methods ignore the class label. Entropy-based discretization chooses a split that makes the resulting groups as class-pure as possible.

### Entropy

For a dataset $S$ with $c$ classes,

$$
H(S)=-\sum_{i=1}^{c}p_i\log_2 p_i.
$$

- $H(S)=0$ when all records belong to one class.
- Entropy increases as the class mixture becomes more even.
- For two equally represented classes, $H(S)=1$.

### Evaluating a candidate split

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

The weighting is essential: a very pure child group containing only one record should not dominate the choice of split. Each child entropy is weighted by the fraction of records assigned to that child.

### Worked example

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

### Complete Threshold Comparison and Termination

For the nine labeled values above, the candidate midpoints and gains are:

| Threshold | Left counts (P,N) | Right counts (P,N) | Weighted entropy | Information gain |
|---|---|---|---:|---:|
| 2 | (1,0) | (3,5) | 0.848386 | 0.142690 |
| 8 | (2,0) | (2,5) | 0.671316 | 0.319760 |
| 14 | (3,0) | (1,5) | 0.433348 | 0.557728 |
| 17 | (3,2) | (1,3) | 0.899985 | 0.091091 |
| 21 | (4,2) | (0,3) | 0.612197 | 0.378879 |
| 25 | (4,3) | (0,2) | 0.766289 | 0.224788 |
| 27 | (4,4) | (0,1) | 0.888889 | 0.102187 |

The best first split is 14. Recursive discretization repeats the search inside mixed intervals. Stop using a declared gain, minimum-bin-size, or statistical criterion; otherwise repeated splitting can encode noise. Entropy-based binning uses the labels, so learn its thresholds from training data only. This same weighted-uncertainty calculation will later choose decision-tree branches.

For categorical hierarchies, domain/schema knowledge can specify street-city-state-country. Ordering attributes by their numbers of distinct values can suggest this hierarchy, but counts alone do not prove valid containment: cities can share names and postal regions need not nest cleanly in administrative regions.

## 11. Worked Semester Questions

### 2026 Q2(b): Bin-Mean Smoothing

Given

$$
7,12,5,8,5,9,13,12,19,7,12,12,13,3,4,5,13,8,7,6,
$$

the first compulsory step is sorting:

$$
3,4,5,5,5,6,7,7,7,8,8,9,12,12,12,12,13,13,13,19.
$$

A bin depth of 3 means three observations per bin. The final bin contains the remaining two observations.

| Bin | Original values | Mean | Smoothed bin |
|---|---|---:|---|
| $B_1$ | $3,4,5$ | $4$ | $4,4,4$ |
| $B_2$ | $5,5,6$ | $5.33$ | $5.33,5.33,5.33$ |
| $B_3$ | $7,7,7$ | $7$ | $7,7,7$ |
| $B_4$ | $8,8,9$ | $8.33$ | $8.33,8.33,8.33$ |
| $B_5$ | $12,12,12$ | $12$ | $12,12,12$ |
| $B_6$ | $12,13,13$ | $12.67$ | $12.67,12.67,12.67$ |
| $B_7$ | $13,19$ | $16$ | $16,16$ |

Hence the smoothed sequence is

$$
4,4,4,5.33,5.33,5.33,7,7,7,8.33,8.33,8.33,
12,12,12,12.67,12.67,12.67,16,16.
$$

Where integer output is required, the rounding rule should be stated explicitly.

### 2026 Q2(c): Boxplot Outliers

After sorting, the data is

$$
\begin{aligned}
&0.11,0.18,0.23,0.51,1.19,1.30,1.32,1.73,2.06,2.16,2.37,\\
&2.91,4.50,4.51,4.66,14.68,14.82,27.44,39.41,41.04,49.09,60.05.
\end{aligned}
$$

Using the median-of-halves convention,

$$
\text{median}=\frac{2.37+2.91}{2}=2.64,
$$

$$
Q_1=1.30,\qquad Q_3=14.82.
$$

Therefore,

$$
\operatorname{IQR}=Q_3-Q_1=14.82-1.30=13.52.
$$

The fences are

$$
Q_1-1.5\operatorname{IQR}
=1.30-1.5(13.52)
=-18.98,
$$

$$
Q_3+1.5\operatorname{IQR}
=14.82+1.5(13.52)
=35.10.
$$

Thus the potential outliers are

$$
\boxed{39.41,\ 41.04,\ 49.09,\ 60.05}.
$$

A boxplot identifies unusual observations but does not establish that they are erroneous.

## 12. Sources

Primary sources: `sources/2_DM_preprocessing.pdf` and the introduction lecture's quality/statistics material. Additional reduction, correlation, and hierarchy explanations use the shared textbook, Chapter 2. PCA and Haar examples extend the lecture overview. Bin means are kept exact before any stated rounding.
