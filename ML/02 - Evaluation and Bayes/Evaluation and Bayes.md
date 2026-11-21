# Chapter 2. Evaluating Hypotheses and Bayesian Learning

[Contents](../readme.md)

## 2.1 Estimate performance from a sample

Let $TP$, $TN$, $FP$, and $FN$ denote true positives, true negatives, false positives, and false negatives. Let $n$ be their total. In the F1 formula, $P$ is precision and $R$ is recall.

| Metric | Formula | Question it answers |
|---|---|---|
| Accuracy | $(TP+TN)/n$ | What fraction of predictions are correct? |
| Precision | $TP/(TP+FP)$ | Of the predicted positives, how many are positive? |
| Recall | $TP/(TP+FN)$ | Of the actual positives, how many were found? |
| F1 | $2PR/(P+R)$ | How well do precision and recall balance? |

For a fixed classifier evaluated on $n$ independent, representative test cases, the number correct can be modeled as binomial. If $\hat p$ is the observed accuracy, the estimated standard error is $\sqrt{\hat p(1-\hat p)/n}$. A large-sample approximate 95% interval is $\hat p\pm1.96\,SE$. For small samples or estimates close to 0 or 1, use a Wilson or exact binomial interval. A large sample only reduces sampling variability. A biased test set can still give a poor estimate.

**Worked check.** With $TP=30,TN=50,FP=10,FN=10$, accuracy is $0.80$, and precision, recall, and F1 are each $0.75$. Here $SE=\sqrt{0.8(0.2)/100}=0.04$, giving an approximate accuracy interval of $[0.7216,0.8784]$.

| Actual class | Predicted positive | Predicted negative |
|---|---:|---:|
| Positive | TP = 30 | FN = 10 |
| Negative | FP = 10 | TN = 50 |

Read **down the predicted-positive column** to calculate precision, $30/(30+10)$. Read **across the actual-positive row** to calculate recall, $30/(30+10)$.

**Arithmetic mean and F1.** The confusion matrix above gives equal precision and recall, so both means are 75%. These separate examples show what happens when the scores differ.

| Precision | Recall | Arithmetic mean | Harmonic mean (F1) |
|---:|---:|---:|---:|
| 100% | 25% | 62.5% | 40% |
| 62.5% | 62.5% | 62.5% | 62.5% |

Both pairs have the same arithmetic mean. F1 is lower for the first pair because the model finds only one in four actual positives.

When comparing trained classifiers, test them on the same examples and compare their disagreements. McNemar's test uses the two counts $b$ and $c$ where only one classifier is correct. With enough discordant cases, $(b-c)^2/(b+c)$ is approximately chi-squared with one degree of freedom under equal error rates. To compare **learning algorithms**, use comparable tuning budgets, common data splits, and repeated evaluations where possible. Report variability alongside the score.

### Sampling and comparison design

| Situation                                            | Appropriate comparison                                                                                                                                                                                                                  |
| ---------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| One fixed classifier on a representative test sample | Report the observed proportion, sample size, and uncertainty interval. With independent cases, standard error falls like $1/\sqrt n$.                                                                                                   |
| Two fixed classifiers on the same test cases         | Use paired outcomes. McNemar’s statistic uses the cases on which only one classifier was correct. For few disagreements, use an exact binomial test on those cases.                                                                     |
| Two algorithms that must be trained and tuned        | Train and tune both under the same split protocol, then compare scores on the same held-out data. Repeated splits or nested cross-validation can assess sensitivity to training data. Overlapping folds do not give independent scores. |

For example, if A alone gets 18 cases right and B alone gets 6 right, the uncorrected McNemar statistic is $(18-6)^2/(18+6)=6$. The cases both models agree on do not distinguish them.

## 2.2 Bayes' rule, concept learning, and optimal decisions

$$
P(h\mid D)=\frac{P(D\mid h)P(h)}{P(D)},\qquad
h_{\mathrm{MAP}}=\arg\max_h P(D\mid h)P(h),\qquad
h_{\mathrm{ML}}=\arg\max_h P(D\mid h).
$$

For events $A$ and $B$, $P(A\mid B)=P(A,B)/P(B)$ when $P(B)>0$. Conditional independence means $P(A,B\mid C)=P(A\mid C)P(B\mid C)$.

| Term | Definition | Example |
|---|---|---|
| Prior $P(h)$ | Probability assigned to a hypothesis before observing the data $D$. | The prevalence of a condition before a test result is known. |
| Likelihood $P(D\mid h)$ | Probability of observing the data under a specified hypothesis. | The probability of a positive result given that the condition is present. |
| Evidence $P(D)$ | Overall probability of the observed data, summed over the possible hypotheses. | The probability of a positive test across the entire population. |
| Posterior $P(h\mid D)$ | Updated probability of a hypothesis after observing the data. | The probability of the condition given a positive result. |

### Total probability and a diagnostic test

If $B_1,\ldots,B_k$ are mutually exclusive and together cover all possibilities, the **law of total probability** states

$$
P(A)=\sum_{i=1}^k P(A\mid B_i)P(B_i),
$$

where the conditioning events have positive probability. For a condition $M$ and a symptom $S$, split the population into those with and without the condition: $P(S)=P(S\mid M)P(M)+P(S\mid M^c)P(M^c)$.

**Worked example.** Suppose meningitis causes a stiff neck in 50% of cases, $P(M)=1/5000$, and $P(S)=1/20$. The problem already gives the overall probability of the symptom. Apply Bayes’ rule directly:

$$
P(M\mid S)=\frac{P(S\mid M)P(M)}{P(S)}
=\frac{(1/2)(1/5000)}{1/20}
=\frac{1}{500}=0.2\%.
$$

In an expected 10,000 cases under these assumptions, 500 have a stiff neck and 1 has both the condition and the symptom.

The same split applies when the overall symptom rate is unknown. Then compute it from the two branches, as in the next example.

**Worked example.** Suppose a disease has prevalence $0.008$, test sensitivity $0.98$, and false-positive rate $0.03$. Out of 100,000 people, expect 784 true positives and 2,976 false positives. The probability of disease given a positive result is $784/(784+2976)\approx0.209$. The prevalence affects the posterior even for a sensitive test.

**Follow both positive branches.** Of the 3,760 positive results, 784 come from people with the condition.

```text
100,000 people
|-- Condition present: 800
|   |-- Test positive: 784
|   `-- Test negative: 16
`-- Condition absent: 99,200
    |-- Test positive: 2,976
    `-- Test negative: 96,224
```

### Comparing Bayesian decisions

| Method | Definition | What it chooses |
|---|---|---|
| Maximum likelihood (ML) | Maximizes $P(D\mid h)$. | The hypothesis under which the observed data are most likely. |
| Maximum a posteriori (MAP) | Maximizes $P(h\mid D)$. | The hypothesis with the highest posterior probability. With uniform priors, MAP and ML give the same maximizers. |
| Bayes-optimal classification | Under zero–one loss, maximizes $\sum_h P(c\mid x,h)P(h\mid D)$ over classes $c$. | The class with the highest posterior predictive probability. |
| Gibbs classification | Samples one hypothesis from the posterior and uses its prediction. | A class from the sampled hypothesis. |

For example, three hypotheses have posterior probabilities $0.4,0.3,0.3$ and predict positive, negative, and negative. The MAP hypothesis predicts positive. Bayes-optimal classification predicts negative with probability mass $0.6$. Gibbs classification predicts negative with probability $0.6$ over repeated draws.

In deterministic, noise-free concept learning, hypotheses that disagree with any training label have zero likelihood. The remaining hypotheses form the **version space** and share posterior mass equally under a uniform prior.

**Brute-force concept learning.** Enumerate candidate hypotheses, multiply each prior by its likelihood, normalize by the sum, then choose the largest posterior or average predictions. For priors $(0.6,0.4)$ and likelihoods $(0.2,0.5)$, the unnormalized weights are $(0.12,0.20)$ and the posteriors are $(0.375,0.625)$. With a deterministic noise-free target, consistent hypotheses have likelihood 1 for the labels given the inputs. Each additional example can eliminate hypotheses from the version space.

Enumerating hypotheses to compute the exact posterior can be expensive. In the Bayesian setting where the true hypothesis and the Gibbs hypothesis are independent draws from the same posterior, the expected zero–one error of Gibbs is at most twice the Bayes-optimal error.

**Likelihood and model complexity.** Independent Gaussian errors of fixed variance make maximum likelihood equivalent to minimizing squared error. Independent Bernoulli targets give binary cross-entropy as the negative log-likelihood. With ideal code lengths $-\log_2 p$, MAP minimizes $-\log_2 P(h)-\log_2 P(D\mid h)$. This illustrates minimum description length: a more complex model must explain the data well enough to justify its longer description.

## 2.3 Naive Bayes and belief networks

Naive Bayes assumes features are conditionally independent given the class. It compares $P(c)\prod_j P(x_j\mid c)$. For a categorical feature with $K$ possible values, add-one smoothing gives $(\text{count}+1)/(\text{class count}+K)$. For multinomial text classification, class priors count **documents** and word likelihoods count **tokens**.

**Worked example.** A corpus has equal spam and ham document priors. The three-word vocabulary is *free*, *win*, *meeting*. The spam token counts are $(3,2,0)$ and the ham counts are $(0,1,4)$. With add-one smoothing, each denominator is $5+3=8$.

| Class | Score for “free win” | Result |
|---|---:|---|
| Spam | $0.5(4/8)(3/8)=0.09375$ | Larger score |
| Ham | $0.5(1/8)(2/8)=0.015625$ | Smaller score |

The model predicts spam with normalized posterior $6/7$. For longer documents, sum log probabilities. A word that occurs twice contributes twice in the multinomial model.

### Categorical Naive Bayes by hand

**Worked example.** In the 14-day PlayTennis dataset, 9 days are labeled Yes and 5 are labeled No. Classify a day with Sunny outlook, Cool temperature, High humidity, and Strong wind using the observed frequencies.

| Evidence | Given Yes | Given No |
|---|---:|---:|
| Class prior | $9/14$ | $5/14$ |
| Sunny outlook | $2/9$ | $3/5$ |
| Cool temperature | $3/9$ | $1/5$ |
| High humidity | $3/9$ | $4/5$ |
| Strong wind | $3/9$ | $3/5$ |
| Product | $\approx 0.0053$ | $\approx 0.0206$ |

1. Count each feature value within each class. For example, 3 of the 9 Yes days have Strong wind.
2. Multiply the prior by the four conditional probabilities in each column.
3. Choose **No**, which has the larger product. Dividing its score by the sum of both scores gives a posterior of about $0.795$ under this model.

Without smoothing, a single unseen feature value can zero out an entire score. With add-$\alpha$ smoothing, use $(n_{vc}+\alpha)/(n_c+K\alpha)$ for a categorical feature with $K$ possible values. For text, replace $n_c$ with the number of training tokens in the class and $K$ with the vocabulary size. A more general **$m$-estimate** is $(n_{vc}+m p_v)/(n_c+m)$, where $p_v$ is the prior probability of value $v$ and $m$ is the weight of that prior in equivalent observations. Add-one smoothing uses $p_v=1/K$ and $m=K$.

The bag-of-words model is efficient for sparse text. Correlated features can effectively count the same evidence twice, and its probabilities may be poorly calibrated.

A **Bayesian belief network** is a directed acyclic graph in which $P(X_1,\dots,X_m)=\prod_iP(X_i\mid\operatorname{pa}(X_i))$. If $A\to B$ and $A\to C$, then $P(A,B,C)=P(A)P(B\mid A)P(C\mid A)$. Knowing $A$ makes $B$ and $C$ conditionally independent in this graph.

A node’s conditional probability table gives one distribution for each assignment of its parents. To infer a marginal, clamp any evidence and sum the joint product over unobserved variables. For example, $P(B)=\sum_a P(A=a)P(B\mid A=a)$. Variable elimination organizes these sums to avoid repeated work. With fully observed training data and a fixed graph, estimate each table from counts within each parent configuration. With missing or hidden variables, expected counts can replace observed counts.

When the network structure is known but some variables are hidden, one option is to maximize the observed-data log-likelihood by gradient ascent. For a conditional probability entry $w_{ijk}$, the contribution of one observation to its gradient is the posterior expected count of that entry divided by $w_{ijk}$, for $w_{ijk}>0$. Each conditional probability row must stay nonnegative and sum to 1. If the graph itself is unknown, learning its edges also requires a structure search or a separate procedure.

### Learning with latent variables

**Expectation–maximization (EM)** fits models with hidden variables. The E-step calculates expected assignments or sufficient statistics under the current parameters. The M-step maximizes the expected complete-data log-likelihood. In a Gaussian mixture, the assignments are soft cluster memberships. In an HMM, they include expected state and transition counts. Exact EM does not decrease the observed-data likelihood, but initialization can affect the result.

**Gaussian mixture example.** Suppose two Gaussian components have equal prior weights and the same known variance, but unknown means. In the E-step, compute each component’s responsibility $r_{ik}$ for each point $x_i$ by normalizing its weighted density across both components. In the M-step, update each mean to $\mu_k=\sum_i r_{ik}x_i/\sum_i r_{ik}$. A point can contribute to both means, with greater weight for the component that explains it better. Repeat until improvement is small.

## Practice

- **2A.** A classifier gets 90 out of 100 test cases right. Estimate the standard error and approximate 95% interval. State the assumptions.
- **2B.** Classify “meeting meeting” using the same vocabulary and counts. Explain how repeated tokens affect the score.
- **2C.** In the PlayTennis example, suppose only Sunny outlook and Strong wind are observed. Compare the Yes and No scores using just these two features.

## Answer checks

| Exercise | Answer |
|---|---|
| 2A | $SE=\sqrt{0.9(0.1)/100}=0.03$. Approximate interval $[0.8412,0.9588]$ for a fixed classifier on independent, representative test cases. |
| 2B | Spam score $0.5(1/8)^2=0.0078125$. Ham score $0.5(5/8)^2=0.1953125$. Predict ham. Each occurrence contributes one likelihood factor. |
| 2C | Yes scores $(9/14)(2/9)(3/9)\approx0.0476$. No scores $(5/14)(3/5)(3/5)\approx0.1286$. Predict No. |

[Chapter sources](../readme.md#2-evaluating-hypotheses-and-bayesian-learning)

---

[← Chapter 1](../01%20-%20ML%20Basics/ML%20Basics.md) · [Contents](../readme.md) · [Chapter 3 →](../03%20-%20Neural%20Networks/Neural%20Networks.md)
