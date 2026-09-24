# Data Mining Complete Exam Notes

This document provides a compact examination-oriented summary of the three detailed topic notes.

## 1. Priority topics

The recurring high-value areas are:

1. Apriori numerical and strong-rule generation
2. FP-growth and Apriori comparison
3. Missing/noisy data, bin smoothing, normalization, and outliers
4. Discretization, entropy, and information gain
5. KDD stages, mining tasks, applications, and major challenges

## 2. Data mining and KDD

**Data mining** discovers valid, novel, useful, and understandable patterns from large datasets. **KDD** is the complete process surrounding it.

```text
Cleaning → Integration → Selection → Transformation
       → Data mining → Pattern evaluation → Presentation
```

The simplest distinction is:

> KDD is the journey from raw data to knowledge; data mining is the pattern-discovery stage.

### Recognizing the mining task

| Required result | Task |
|---|---|
| Known discrete label | Classification |
| Numerical value | Regression |
| Unknown natural groups | Clustering |
| Items occurring together | Association mining |
| Events occurring in order | Sequential-pattern mining |
| Unusual cases | Outlier analysis |

### Major issues in data mining

- **Methodology:** noise, missing data, uncertainty, too many patterns
- **User interaction:** domain knowledge, queries, understandable presentation
- **Scalability:** large records, dimensions, streams, and candidate spaces
- **Data diversity:** tables, text, images, graphs, and distributed data
- **Society:** privacy, consent, bias, security, and misuse

Detailed note: [Introduction and KDD](01_Introduction_and_KDD/notes/Introduction_and_KDD.md).

## 3. Descriptive statistics

For $x_1,x_2,\ldots,x_n$,

$$
\bar{x}=\frac{1}{n}\sum_{i=1}^{n}x_i,
\qquad
\bar{x}_w=\frac{\sum_iw_ix_i}{\sum_iw_i}.
$$

Population and sample variance are

$$
\sigma^2=\frac{1}{N}\sum_{i=1}^{N}(x_i-\mu)^2,
$$

$$
s^2=\frac{1}{n-1}\sum_{i=1}^{n}(x_i-\bar{x})^2.
$$

For boxplot outliers,

$$
\operatorname{IQR}=Q_3-Q_1,
$$

$$
\text{fences}=Q_1-1.5\operatorname{IQR},\quad Q_3+1.5\operatorname{IQR}.
$$

Positive skew usually gives mode $<$ median $<$ mean. Negative skew gives mean $<$ median $<$ mode.

## 4. Data preprocessing

```text
Cleaning → Integration → Transformation → Reduction → Discretization
```

### Missing values, noise, and outliers

- Missing value: ignore, manually fill, use a constant, use mean/median, use class mean, or predict.
- Noise: smooth by bin mean/median/boundary, regression, clustering, or human inspection.
- Outlier: investigate whether it is an error or a valid rare event before removal.

### Integration checks

Resolve schema/entity matching, redundant attributes, duplicate tuples, and conflicting units or codes.

### Normalization methods

Min-max normalization to $[a,b]$:

$$
v'=\frac{v-\min_A}{\max_A-\min_A}(b-a)+a.
$$

Z-score normalization:

$$
v'=\frac{v-\mu_A}{\sigma_A}.
$$

Decimal scaling:

$$
v'=\frac{v}{10^j},
\qquad \max|v'|<1.
$$

### Reduction and discretization

- Feature selection reduces attributes.
- Sampling reduces records; stratification protects small classes.
- Equal-width binning fixes interval width.
- Equal-frequency binning fixes approximate observations per bin.
- A concept hierarchy replaces detailed values with broader concepts.

### Entropy-based discretization

$$
H(S)=-\sum_i p_i\log_2p_i.
$$

For child sets $S_j$,

$$
I=\sum_j\frac{|S_j|}{|S|}H(S_j),
$$

$$
\operatorname{Gain}=H(S)-I.
$$

Choose maximum gain, equivalently minimum weighted child entropy.

Detailed note: [Data preprocessing](02_Data_Preprocessing/notes/Data_Preprocessing.md).

## 5. Association-rule mining

For $X\cap Y=\varnothing$,

$$
s(X\rightarrow Y)=\frac{\sigma(X\cup Y)}{N},
$$

$$
c(X\rightarrow Y)=\frac{\sigma(X\cup Y)}{\sigma(X)}.
$$

Support measures how common the complete combination is. Confidence measures how often $Y$ occurs when $X$ occurs.

### Anti-monotonicity

If $X\subseteq Y$, then

$$
s(X)\ge s(Y).
$$

Therefore, every superset of an infrequent itemset is infrequent.

### Apriori

```text
Count L₁ → join → prune → count C₂ → retain L₂ → repeat
```

Show each candidate set, support count, prune decision, and frequent set. Convert percentage support to

$$
\left\lceil\frac{pN}{100}\right\rceil.
$$

### FP-growth

```text
Count items → remove infrequent items → fix frequency order
→ build FP-tree → conditional pattern bases
→ conditional FP-trees → frequent patterns
```

Apriori repeatedly scans the database and explicitly generates candidates. FP-growth normally scans twice, compresses shared prefixes, and avoids candidate generation.

### Closed and maximal

- **Closed:** no proper superset has the same support.
- **Maximal:** no proper superset is frequent.

Closed patterns preserve exact support information; maximal patterns are smaller but do not.

### Lift

Confidence can look high merely because $Y$ is common. Lift compares confidence with that baseline:

$$
\operatorname{lift}(X\rightarrow Y)
=\frac{c(X\rightarrow Y)}{s(Y)}
=\frac{s(X\cup Y)}{s(X)s(Y)}.
$$

- lift $>1$: positive association
- lift $=1$: independence
- lift $<1$: negative association

Detailed note: [Association rule mining](03_Association_Rule_Mining/notes/Association_Rule_Mining.md).

## 6. Examination-answer format

### Five-mark theory answer

A complete answer contains a precise definition, three to five connected explanatory points, an example or diagram, and the principal interpretation or limitation.

### Ten-mark algorithm or numerical

The answer should state the purpose and inputs, define notation, present ordered steps, show the complete trace, report the result, and mention the principal limitation or comparison.

The required intermediate work includes sorted values, bins, quartiles, candidate itemsets, class counts, or conditional pattern bases, as applicable.

## 7. PYQ boundary

Study first:

- 2026 Q3, Q2(b-c), and Q1(a)
- 2025 Q3, Q2(b), and Q1(b,d)

The supplied lecture notes do not develop full warehouse, decision-tree, KNN, clustering, text-mining, or time-series algorithms. Mixed past questions should not be used to expand the defined scope.

Use the [covered PYQ index](00_Course_Guide/PYQ_TOPIC_INDEX.md) and [worked examples](04_Past_Questions/COVERED_WORKED_EXAMPLES.md).
