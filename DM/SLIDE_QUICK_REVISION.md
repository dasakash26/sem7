# Data Mining Last-Minute Revision

## Essential process sequences

**KDD**

```text
Cleaning → Integration → Selection → Transformation
→ Mining → Evaluation → Presentation
```

**Preprocessing**

```text
Cleaning → Integration → Transformation → Reduction → Discretization
```

**Apriori**

```text
L₁ → join → prune → count candidates → L₂ → repeat → rules
```

**FP-growth**

```text
Item counts → frequency order → FP-tree
→ conditional pattern bases → conditional trees → patterns
```

## Formula sheet

$$
\operatorname{IQR}=Q_3-Q_1
$$

$$
\text{outlier fences}=Q_1-1.5\operatorname{IQR},\quad Q_3+1.5\operatorname{IQR}
$$

$$
v'_{\text{min-max}}=\frac{v-\min_A}{\max_A-\min_A}(b-a)+a
$$

$$
v'_{z}=\frac{v-\mu_A}{\sigma_A}
$$

$$
v'_{\text{decimal}}=\frac{v}{10^j},\qquad\max|v'|<1
$$

$$
H(S)=-\sum_i p_i\log_2p_i
$$

$$
\operatorname{Gain}=H(\text{parent})-\sum_j\frac{|S_j|}{|S|}H(S_j)
$$

$$
s(X\rightarrow Y)=\frac{\sigma(X\cup Y)}{N}
$$

$$
c(X\rightarrow Y)=\frac{\sigma(X\cup Y)}{\sigma(X)}
$$

$$
\operatorname{lift}(X\rightarrow Y)=\frac{c(X\rightarrow Y)}{s(Y)}
$$

## Essential distinctions

| Pair | Difference |
|---|---|
| Data mining / KDD | Mining discovers patterns; KDD is the complete raw-data-to-knowledge process |
| Classification / clustering | Classification uses known labels; clustering discovers groups |
| Regression / classification | Regression predicts a number; classification predicts a category |
| Nominal / ordinal | Nominal has no order; ordinal has order but unknown gap size |
| Discrete / continuous | Discrete is countable; continuous is measured over a range |
| Equal-width / equal-frequency | Same interval width versus approximately equal observations |
| Closed / maximal | No same-support superset versus no frequent superset |
| Apriori / FP-growth | Candidate generation versus compressed pattern growth |
| Confidence / lift | Conditional frequency versus confidence compared with baseline |

## Numerical cautions

- Values must be sorted before calculating quartiles or forming equal-frequency bins.
- The quartile and rounding conventions must be stated.
- Equal attribute values must not be placed on opposite sides of one entropy threshold.
- Repetition of an item within one transaction does not increase support.
- Every required subset must be frequent before an Apriori candidate survives pruning.
- Rules from one itemset share support but may have different confidence values.
- High confidence may represent negative association when the consequent is already common.

## Self-assessment criteria

Revision is complete when the following tasks can be performed without reference to the detailed notes:

1. State the relationship between data mining and KDD.
2. Classify the five categories of major issues in data mining.
3. Select and justify an appropriate missing-value treatment.
4. Apply bin-mean and bin-boundary smoothing.
5. Apply min-max, z-score, and decimal-scaling normalization.
6. Calculate weighted child entropy and information gain.
7. Trace Apriori from $C_1$ to termination.
8. Construct a conditional pattern base from an FP-tree.
9. Explain the baseline-frequency limitation of confidence and the role of lift.
