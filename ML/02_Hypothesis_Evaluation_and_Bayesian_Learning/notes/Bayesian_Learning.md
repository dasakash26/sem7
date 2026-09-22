# Bayesian Learning

> **CT source range:** the handwritten sheet marks `Unit-4.pdf` as complete. This note is the concept guide; the entire assigned PDF remains examinable.

> **Core chain:** prior $P(h)$ → likelihood $P(D\mid h)$ → posterior $P(h\mid D)$ → choose with MAP. For classification, compare $P(x\mid c)P(c)$. Naive Bayes is a restricted Bayesian network whose features are conditionally independent given the class.

## Introduction
Bayesian reasoning provides a **probabilistic approach to inference**. Based on the assumption that quantities of interest are governed by probability distributions, and optimal decisions can be made by reasoning about these probabilities with observed data.

## Features of Bayesian Learning Methods
- Each observed training example can **incrementally decrease/increase** the estimated probability of hypothesis correctness
- **Prior knowledge** combined with observed data
- Accommodates hypotheses that make probabilistic predictions
- New instances classified by combining predictions of **multiple hypotheses, weighted by probabilities**
- Provides a **standard of optimal decision making** even when computationally intractable

## Practical Difficulties
1. Requires initial knowledge of many probabilities
2. Significant computational cost for Bayes optimal hypothesis in general case

---

## Bayes Theorem
$$
P(h|D) = \frac{P(D|h) P(h)}{P(D)}
$$

### Key Notations
| Term      | Meaning                                                        |
| --------- | -------------------------------------------------------------- |
| $P(h)$    | Prior probability of hypothesis h                              |
| $P(D)$    | Prior probability of training data D                           |
| $P(h\|D)$ | Posterior probability - confidence that h holds after seeing D |
| $P(D\|h)$ | Likelihood - probability of observing D given h holds          |

### Properties
- $P(h|D)$ increases with $P(h)$ and $P(D|h)$
- $P(h|D)$ decreases as $P(D)$ increases (less evidence D provides in support of h)

### Example: Medical Diagnosis
- $P(\text{cancer}) = 0.008$
- $P(+|\text{cancer}) = 0.98$, $P(-|\text{cancer}) = 0.02$
- $P(+|\neg\text{cancer}) = 0.03$, $P(-|\neg\text{cancer}) = 0.97$

With positive test result:
- $P(+|\text{cancer})P(\text{cancer}) = 0.0078$
- $P(+|\neg\text{cancer})P(\neg\text{cancer}) = 0.0298$

**hMAP = not cancer** (higher probability)

Exact: $P(\text{cancer}|+) = 0.21$, $P(\neg\text{cancer}|+) = 0.79$

---

## Basic Probability Formulas
- **Product Rule**: $P(A \land B) = P(A|B)P(B) = P(B|A)P(A)$
- **Sum Rule**: $P(A \lor B) = P(A) + P(B) - P(A \land B)$
- **Theorem of Total Probability**: If $A_1, ..., A_n$ are mutually exclusive: $P(B) = \sum_{i=1}^{n} P(B|A_i)P(A_i)$

---

## MAP and ML Hypotheses

### Maximum A Posteriori (MAP)
$$h_{MAP} = \underset{h \in H}{\text{argmax}} P(h|D) = \underset{h \in H}{\text{argmax}} P(D|h)P(h)$$

### Maximum Likelihood (ML)
When all hypotheses equally probable: $P(h_i) = P(h_j)$
$$h_{ML} = \underset{h \in H}{\text{argmax}} P(D|h)$$

---

## Brute-Force MAP Learning Algorithm
1. For each hypothesis $h$ in $H$, calculate $P(h|D)$
2. Output $h_{MAP}$ with highest posterior probability

### Assumptions
- Training data D is **noise free** ($d_i = c(x_i)$)
- Target concept $c$ is contained in hypothesis space $H$
- No a priori reason to favor any hypothesis (uniform prior)

### Result
- Inconsistent hypotheses get $P(h|D) = 0$
- Consistent hypotheses share probability equally: $P(h|D) = 1/|VS_{H,D}|$

---

## Consistent Learners
- A learning algorithm is a **consistent learner** if it outputs a hypothesis with zero errors on training examples
- Under uniform prior and noise-free data, every consistent learner outputs a MAP hypothesis
- **FIND-S** outputs a MAP hypothesis and favors more specific hypotheses

---

## Maximum Likelihood & Least-Squared Error
Under certain assumptions, any learning algorithm that **minimizes squared error** outputs a Maximum Likelihood hypothesis.

$$h_{ML} = \underset{h \in H}{\text{argmin}} \sum_{i=1}^{m} (d_i - h(x_i))^2$$

### Assumptions
- Target value corrupted by random noise drawn from **Normal distribution with zero mean**
- Training examples mutually independent given h
- This provides **Bayesian justification** for neural network/curve fitting methods

### Why Normal Distribution?
1. Mathematically straightforward analysis
2. Good approximation to many types of noise in physical systems
3. Central Limit Theorem: sum of many i.i.d. random variables ≈ Normal

---

## Maximum Likelihood for Predicting Probabilities
For learning nondeterministic (probabilistic) function $f: X \to \{0,1\}$:
- Want $f'(x) = P(f(x)=1)$
- Maximum likelihood hypothesis maximizes:
$$h_{ML} = \underset{h \in H}{\text{argmax}} \sum_{i=1}^{m} [d_i \ln h(x_i) + (1-d_i) \ln(1-h(x_i))]$$

### Gradient Ascent Weight Update (sigmoid unit)
$$w_{jk} \leftarrow w_{jk} + \eta \sum_{i=1}^{m} (d_i - h(x_i)) x_{ijk}$$

---

## Minimum Description Length (MDL) Principle
Motivated by information theory interpretation of hMAP.

$$
\begin{aligned}
h_{MAP} &= \underset{h \in H}{\text{argmax}} P(D|h)P(h) \\
&= \underset{h \in H}{\text{argmin}} [-\log_2 P(D|h) - \log_2 P(h)]
\end{aligned}
$$

### Key Insight
- Optimal code length for event with probability $p$ is $-\log_2 p$ bits
- $-\log_2 P(h)$ = description length of hypothesis $h$
- $-\log_2 P(D|h)$ = description length of data given $h$

**MDL Principle**: Prefer the hypothesis that minimizes:
$$\text{length}(h) + \text{length}(\text{misclassifications})$$

### Application to Decision Trees
- $C_1$: Encoding grows with number of nodes and edges
- $C_2$: If hypothesis predicts correctly, description length is ZERO; otherwise need to transmit misclassified examples' identities and correct classifications

---

## Bayes Optimal Classifier
Instead of asking "most probable hypothesis?", ask "**most probable classification** of new instance?"

$$P(v_j|D) = \sum_{h_i \in H} P(v_j|h_i) P(h_i|D)$$

Bayes optimal classification:
$$\underset{v_j \in V}{\text{argmax}} \sum_{h_i \in H} P(v_j|h_i) P(h_i|D)$$

### Example
- $P(h_1|D)=0.4$: classifies +, $P(-|h_1)=0, P(+|h_1)=1$
- $P(h_2|D)=0.3$: classifies -, $P(-|h_2)=1$
- $P(h_3|D)=0.3$: classifies -, $P(-|h_3)=1$

$\sum P(+|h_i)P(h_i|D) = 0.4$, $\sum P(-|h_i)P(h_i|D) = 0.6$

Most probable classification is **negative** (different from MAP hypothesis!)

### Gibbs Algorithm (Alternative)
1. Choose hypothesis $h$ at random, according to $P(h|D)$
2. Use $h$ to classify

Under conditions: $E[\text{error}_{Gibbs}] \leq 2 \cdot E[\text{error}_{BayesOptimal}]$

---

## Naive Bayes Classifier

### Core Formula
$$v_{NB} = \underset{v_j \in V}{\text{argmax}} P(v_j) \prod_i P(a_i|v_j)$$

### Key Assumption
**Conditional independence** of attributes given the target value:
$$P(a_1, ..., a_n | v_j) = \prod_i P(a_i|v_j)$$

### Example: Play Tennis
| Day | Outlook | Temp | Humidity | Wind | Play |
|-----|---------|------|----------|------|------|
| D1  | Sunny   | Hot  | High     | Weak | No   |
| ... | ...     | ...  | ...      | ...  | ...  |

New instance: `<Outlook=sunny, Temp=cool, Humidity=high, Wind=strong>`

Calculations:
- $P(\text{Yes}) = 9/14$, $P(\text{No}) = 5/14$
- $\prod P(a_i|\text{Yes}) \cdot P(\text{Yes}) = 0.0053$
- $\prod P(a_i|\text{No}) \cdot P(\text{No}) = 0.0206$

**Prediction: No** (with 79.5% confidence)

### m-Estimate for Zero Probabilities
When $n_c = 0$, use:
$$\frac{n_c + mp}{n + m}$$

Where $p = 1/k$ (for $k$ possible values) and $m$ is equivalent sample size.

### Text Classification
```
LEARN_NAIVE_BAYES_TEXT(Examples, V):
  - Collect vocabulary from all documents
  - For each class v_j:
      docs_j = documents with target v_j
      P(v_j) = |docs_j| / |Examples|
      Text_j = concatenate all docs_j
      n = total word positions in Text_j
      for each word w_k:
          n_k = count of w_k in Text_j
          P(w_k|v_j) = (n_k + 1) / (n + |Vocabulary|)
```

### Advantages & Disadvantages
| Pros | Cons |
|------|------|
| Easy to implement | Conditional independence assumption |
| Good results in most cases | Loss of accuracy due to dependencies |
| Fast/efficient | Cannot model attribute dependencies |

---

## Bayesian Belief Networks
Describes **joint probability distribution** for a set of variables by specifying:
1. Conditional independence assumptions
2. Conditional probabilities

### Representation
- **Directed acyclic graph** (DAG)
- Each node = random variable
- Arcs = conditional independence assertions: variable is conditionally independent of non-descendants **given its immediate predecessors**
- **Conditional Probability Table (CPT)** for each variable

### Network Example (Storm/BusTourGroup/...)
Variables: Storm, Lightning, Thunder, ForestFire, Campfire, BusTourGroup

$P(\text{Campfire} = \text{True} | \text{Storm} = \text{True}, \text{BusTourGroup} = \text{True}) = 0.4$

### Joint Probability
$$P(y_1, ..., y_n) = \prod_{i=1}^{n} P(y_i | \text{Parents}(Y_i))$$

### Conditional Independence
$X$ is conditionally independent of $Y$ given $Z$ if:
$$P(X|Y,Z) = P(X|Z)$$

### Gradient Ascent Training
$$w_{ijk} \leftarrow w_{ijk} + \eta \sum_{d \in D} \frac{P_h(y_{ij}, u_{ik}|d)}{w_{ijk}}$$

Then renormalize to ensure probability constraints.

---

## EM Algorithm (Expectation-Maximization)
Used when some variables are never directly observed, provided the general form of the probability distribution is known.

### Gaussian Mixture Example
- Data generated by a mixture of $k$ Normal distributions
- Two-step process: select distribution → generate instance
- Task: Find means $\mu_1, ..., \mu_k$ of the distributions

### Steps
1. **E-Step (Expectation)**: Using observed data, estimate values of missing/unobserved variables
2. **M-Step (Maximization)**: Use complete data to update parameters
3. Repeat until convergence

### Applications
- Filling missing data
- Basis of unsupervised clustering
- Estimating HMM parameters
- Discovering latent variable values

### Pros & Cons
| Pros | Cons |
|------|------|
| Likelihood always increases each iteration | Slow convergence |
| E and M steps often easy to implement | Converges only to local optima |
| Closed-form solutions for M-steps | Requires both forward and backward probabilities |
