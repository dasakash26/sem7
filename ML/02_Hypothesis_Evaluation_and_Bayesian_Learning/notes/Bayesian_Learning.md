# Evaluating Hypotheses and Bayesian Learning

*Chapter 2*

> **Place in the course.** Chapter 1 introduced hypotheses, training data, validation, and generalization. This chapter asks a more precise question: how should a learner compare competing hypotheses when the data and the target are uncertain?

## Learning objectives

After studying this chapter, you should be able to:

1. use conditional probability, the product rule, and the total-probability rule;
2. derive and interpret Bayes' theorem;
3. distinguish prior, likelihood, evidence, and posterior;
4. explain MAP, maximum likelihood, version spaces, consistent learners, and MDL;
5. distinguish a MAP hypothesis from a Bayes-optimal classification;
6. derive and apply the Naive Bayes classifier;
7. explain Bayesian networks and their factorization;
8. describe the role of expectation-maximization.

---

## 2.1 Why probability belongs in learning

Data rarely determine one hypothesis with certainty. Measurements contain noise, examples may be incomplete, and several explanations may agree with the observations.

A deterministic learner may discard every hypothesis that makes one error. A Bayesian learner retains uncertainty by assigning probabilities to hypotheses and updating those probabilities as evidence arrives.

The central question is:

> Given a hypothesis and observed data, how should our belief in that hypothesis change?

Bayes' theorem supplies the answer.

---

## 2.2 Probability tools

For events $A$ and $B$, the conditional probability of $A$ given $B$ is

$$
P(A\mid B)=\frac{P(A\cap B)}{P(B)},
\qquad P(B)>0.
$$

Rearranging gives the product rule:

$$
P(A\cap B)=P(A\mid B)P(B)=P(B\mid A)P(A).
$$

For mutually exclusive events $A_1,\ldots,A_k$ that cover all possibilities,

$$
P(B)=\sum_{i=1}^{k}P(B\mid A_i)P(A_i).
$$

This is the **law of total probability**. It says that an event's probability can be obtained by partitioning the possible worlds and adding their contributions.

The distinction between $P(A\mid B)$ and $P(B\mid A)$ is crucial. They answer different questions and are generally not equal.

---

## 2.3 Bayes' theorem

Apply the product rule in two ways:

$$
P(A\cap B)=P(A\mid B)P(B)
$$

and

$$
P(A\cap B)=P(B\mid A)P(A).
$$

Equating them and dividing by $P(B)$ gives

$$
\boxed{P(A\mid B)=\frac{P(B\mid A)P(A)}{P(B)}}.
$$

For a hypothesis $h$ and observed data $D$:

$$
\boxed{P(h\mid D)=\frac{P(D\mid h)P(h)}{P(D)}}.
$$

The four terms have different roles:

| Term | Interpretation |
|---|---|
| $P(h)$ | prior belief in the hypothesis before seeing $D$ |
| $P(D\mid h)$ | likelihood of observing $D$ if $h$ were true |
| $P(D)$ | evidence, or total probability of the observed data |
| $P(h\mid D)$ | posterior belief after observing $D$ |

In words:

> Posterior belief is proportional to prior belief multiplied by how well the hypothesis predicts the data.

### Example 2.1: A cloudy picnic

Suppose $R$ means “it rains” and $C$ means “the morning is cloudy.” Let

$$
P(R)=0.10,\qquad P(C\mid R)=0.50,\qquad P(C)=0.40.
$$

Then

$$
P(R\mid C)
=\frac{P(C\mid R)P(R)}{P(C)}
=\frac{0.50\times0.10}{0.40}
=0.125.
$$

The probability of rain given a cloudy morning is $12.5\%$. The conditional probability $P(C\mid R)=50\%$ was not itself the answer.

### Example 2.2: A rare disease

Let $C$ denote cancer and $+$ a positive test. Suppose

$$
P(C)=0.008,\quad
P(+\mid C)=0.98,\quad
P(+\mid\neg C)=0.03.
$$

The posterior probability is

$$
P(C\mid +)
=\frac{P(+\mid C)P(C)}
{P(+\mid C)P(C)+P(+\mid\neg C)P(\neg C)}.
$$

Substituting:

$$
P(C\mid +)
=\frac{0.98(0.008)}
{0.98(0.008)+0.03(0.992)}
\approx0.21.
$$

Although the test is sensitive, a positive result implies only about a $21\%$ probability of disease because the disease is rare. This is the **base-rate effect**.

---

## 2.4 Bayesian concept learning

Let $H$ be a hypothesis space and $D$ the training data. Bayesian concept learning assigns a posterior probability to every $h\in H$:

$$
P(h\mid D)\propto P(D\mid h)P(h).
$$

The learner can then:

- select one hypothesis;
- retain a posterior distribution over hypotheses;
- average predictions from several hypotheses.

This is more informative than simply declaring one hypothesis “true” when the data do not justify that certainty.

### Brute-force Bayes learning

For a finite hypothesis space:

1. calculate $P(h\mid D)$ for every $h\in H$;
2. normalize the values if actual probabilities are required;
3. choose the hypothesis with the largest posterior, if a single hypothesis is needed.

This algorithm is conceptually important but often computationally expensive because the hypothesis space may be enormous.

### Version spaces and consistent learners

Under a noise-free setting, assume:

1. the target concept is contained in $H$;
2. examples are labelled correctly;
3. all hypotheses have the same prior probability.

The **version space** is the set of hypotheses consistent with every observed example:

$$
VS_{H,D}=\{h\in H:h(x_i)=y_i\text{ for every training example}\}.
$$

An inconsistent hypothesis has $P(D\mid h)=0$. Each consistent hypothesis has the same likelihood. Under a uniform prior, the posterior probability is therefore shared equally among the hypotheses in the version space:

$$
P(h\mid D)=
\begin{cases}
\frac{1}{|VS_{H,D}|},&h\in VS_{H,D},\\
0,&h\notin VS_{H,D}.
\end{cases}
$$

A **consistent learner** returns a hypothesis with zero training error. Under these assumptions, any consistent learner returns a MAP hypothesis. FIND-S is an example: it returns a maximally specific consistent hypothesis.

The assumptions matter. With noisy labels or unequal priors, consistency alone does not determine the best posterior hypothesis.

---

## 2.5 MAP and maximum likelihood

The **maximum a posteriori** hypothesis is

$$
h_{MAP}
=\arg\max_{h\in H}P(h\mid D).
$$

Using Bayes' theorem and observing that $P(D)$ is constant with respect to $h$:

$$
\boxed{
h_{MAP}
=\arg\max_{h\in H}P(D\mid h)P(h)
}.
$$

The **maximum likelihood** hypothesis ignores the prior:

$$
\boxed{
h_{ML}
=\arg\max_{h\in H}P(D\mid h)
}.
$$

MAP and ML agree when all hypotheses have equal prior probability. Otherwise, MAP rewards both data fit and prior plausibility.

### Likelihood and least-squares error

Suppose the target is a real value and the observation noise is independent Gaussian noise with mean zero and fixed variance. Maximizing the likelihood is then equivalent to minimizing the sum of squared errors:

$$
h_{ML}
=\arg\min_{h\in H}
\sum_{i=1}^{m}\bigl(y_i-h(x_i)\bigr)^2.
$$

This result gives a probabilistic interpretation to least-squares fitting. It is not a universal identity; it depends on the noise assumptions.

For probabilistic binary predictions, the likelihood becomes

$$
\prod_{i=1}^{m}
h(x_i)^{y_i}\bigl(1-h(x_i)\bigr)^{1-y_i}.
$$

Taking logarithms turns the product into a sum:

$$
\sum_{i=1}^{m}
\left[
y_i\log h(x_i)
+(1-y_i)\log(1-h(x_i))
\right].
$$

Maximizing this log-likelihood is the origin of binary cross-entropy.

---

## 2.6 Minimum Description Length

The **Minimum Description Length (MDL)** principle chooses a hypothesis that gives the shortest total description of:

1. the hypothesis itself;
2. the data not already explained by that hypothesis.

Because an event with probability $p$ has ideal code length $-\log_2p$,

$$
\begin{aligned}
h_{MAP}
&=\arg\max_h P(D\mid h)P(h)\\
&=\arg\min_h
\left[-\log_2P(D\mid h)-\log_2P(h)\right].
\end{aligned}
$$

Thus, under suitable coding choices,

$$
\text{description length}
=
\text{length of hypothesis}
+
\text{length of unexplained data}.
$$

MDL expresses a version of Occam's razor: a complicated hypothesis must earn its extra complexity by explaining the data substantially better.

For a decision tree, one code can describe the tree structure and another can describe the misclassified examples. A tree that is larger but makes fewer errors is preferred only when the reduction in error description outweighs the additional tree description.

---

## 2.7 MAP hypothesis versus Bayes-optimal classification

MAP selects the single most probable hypothesis. Bayes-optimal classification asks a different question:

> Which class has the highest posterior probability after averaging over all hypotheses?

For a new instance $x$ and possible class $v$:

$$
P(v\mid x,D)
=
\sum_{h\in H}P(v\mid x,h)P(h\mid D).
$$

The Bayes-optimal prediction is

$$
\boxed{
\hat v
=
\arg\max_v
\sum_{h\in H}P(v\mid x,h)P(h\mid D)
}.
$$

The MAP hypothesis can disagree with the Bayes-optimal class because several less-probable hypotheses may collectively support another class.

### Gibbs classification

The Gibbs algorithm samples one hypothesis according to $P(h\mid D)$ and uses it to classify the new instance. It is cheaper than averaging over all hypotheses. Under the standard assumptions from the course, its expected error is at most twice the expected error of the Bayes-optimal classifier:

$$
E[\operatorname{error}_{Gibbs}]
\leq
2E[\operatorname{error}_{Bayes}].
$$

---

## 2.8 Bayesian classification

For class $c_j$ and input $x$, the Bayes/MAP decision rule is

$$
\hat c
=
\arg\max_{c_j}P(c_j\mid x).
$$

Applying Bayes' theorem:

$$
\hat c
=
\arg\max_{c_j}P(x\mid c_j)P(c_j),
$$

because $P(x)$ is the same for every candidate class.

The prior $P(c_j)$ expresses how common the class is. The likelihood $P(x\mid c_j)$ expresses how compatible the observed features are with that class.

---

## 2.9 Naive Bayes

Naive Bayes makes one strong simplifying assumption: the features are conditionally independent given the class.

For $x=(x_1,\ldots,x_n)$:

$$
P(x_1,\ldots,x_n\mid c)
=
\prod_{i=1}^{n}P(x_i\mid c).
$$

The decision rule becomes

$$
\boxed{
\hat c
=
\arg\max_c
P(c)\prod_{i=1}^{n}P(x_i\mid c)
}.
$$

The assumption is often false, but the classifier can still perform well, especially with high-dimensional sparse text features.

### The four-step calculation

For each possible class:

1. estimate the prior $P(c)$;
2. estimate one likelihood $P(x_i\mid c)$ for each feature;
3. multiply the prior and likelihoods;
4. choose the class with the largest score.

The common evidence term $P(x)$ is unnecessary when only the winning class is required.

### Example 2.3: Play Tennis

Suppose a training table contains $14$ days, with $9$ “Yes” outcomes and $5$ “No” outcomes. For the new observation

$$
x=(\text{Sunny},\text{Cool},\text{High},\text{Strong}),
$$

the two unnormalized Naive Bayes scores are

$$
S_{Yes}
=P(Yes)
P(Sunny\mid Yes)
P(Cool\mid Yes)
P(High\mid Yes)
P(Strong\mid Yes),
$$

and

$$
S_{No}
=P(No)
P(Sunny\mid No)
P(Cool\mid No)
P(High\mid No)
P(Strong\mid No).
$$

Compute both scores using the frequency table and select the larger one. The calculation is a comparison of class scores; normalization is needed only if calibrated posterior probabilities are requested.

### Zero-frequency problem and smoothing

If a feature value never appears with class $c$, its estimated likelihood is zero. Multiplication then makes the entire class score zero.

Laplace smoothing replaces a raw frequency estimate with

$$
P(w\mid c)
=
\frac{count(w,c)+1}
{\sum_{w'}count(w',c)+|V|},
$$

where $|V|$ is the number of possible values or vocabulary items. More generally, the m-estimate is

$$
\frac{n_c+mp}{n+m},
$$

where $p$ is a prior estimate and $m$ is an equivalent sample size.

### Text classification

For a document represented as a bag of words,

$$
\hat c
=
\arg\max_c
\left[
\log P(c)+
\sum_{w\in d}\log P(w\mid c)
\right].
$$

Logarithms prevent numerical underflow and turn a product into a sum. This approach is used in spam filtering, topic classification, and sentiment analysis.

---

## 2.10 Bayesian belief networks

A Bayesian Network represents a joint probability distribution with a directed acyclic graph.

- Each node represents a random variable.
- A directed edge represents a direct dependency in the model.
- A conditional probability table specifies a node's distribution for each assignment of its parents.
- The graph contains no directed cycle.

If $Pa(X_i)$ denotes the parents of $X_i$, the joint distribution factorizes as

$$
\boxed{
P(X_1,\ldots,X_n)
=
\prod_{i=1}^{n}
P(X_i\mid Pa(X_i))
}.
$$

This factorization is valuable because it replaces one large joint table with smaller local conditional tables.

Conditional independence is a statement about distributions. $X$ is conditionally independent of $Y$ given $Z$ when

$$
P(X\mid Y,Z)=P(X\mid Z).
$$

The graph encodes such assumptions, but the meaning of an independence depends on what variables have been observed or conditioned on.

### Naive Bayes as a restricted network

Naive Bayes can be drawn as one class node pointing to every feature node. The features have no edges between them. This graph expresses conditional independence of the features given the class. Naive Bayes is therefore a special, highly constrained Bayesian network.

---

## 2.11 Expectation-maximization

Expectation-maximization (EM) is used when some variables are unobserved or latent, but the form of the probability model is known.

Suppose data were generated by a mixture of $k$ Gaussian distributions, but the identity of the generating component is hidden. EM alternates:

1. **E-step:** using the current parameters, estimate the probability that each observation belongs to each hidden component;
2. **M-step:** treat those probabilities as responsibilities and update the model parameters to maximize the expected complete-data likelihood.

Repeat the two steps until the likelihood or parameter changes become sufficiently small.

EM can increase likelihood at each iteration, but it may converge to a local optimum and depends on its initialization. Applications include mixture models, missing-data estimation, clustering, and hidden Markov models.

---

## Chapter summary

Bayesian learning keeps uncertainty over competing hypotheses. Bayes' theorem updates a prior using the likelihood of observed data. MAP chooses the most probable hypothesis; maximum likelihood chooses the hypothesis that best explains the data without a prior. Version spaces describe hypotheses consistent with noise-free examples. MDL interprets model selection as a trade-off between describing the model and describing its errors.

Bayes-optimal classification averages predictions across hypotheses. Naive Bayes makes that calculation practical by assuming conditional independence of features given the class. Bayesian networks represent more general dependency structures, while EM estimates models containing hidden variables.

The chapter's central chain is:

> **Prior → likelihood → posterior → model or class decision**

---

## Review questions

1. Derive Bayes' theorem from the product rule.
2. Explain why $P(+\mid C)$ is not the same as $P(C\mid +)$.
3. State the assumptions under which every consistent learner is MAP.
4. Compare MAP and maximum likelihood.
5. Explain the relation between least-squares error and Gaussian noise.
6. What does MDL penalize?
7. Why can Bayes-optimal classification disagree with the MAP hypothesis?
8. Derive the Naive Bayes decision rule.
9. Why is Laplace smoothing needed?
10. Explain how a Bayesian network factorizes a joint distribution.
11. Describe the E-step and M-step of EM.

---

## Source guide

- Conditional probability, Bayes' theorem, MAP, ML, and Naive Bayes: *Unit-4.pdf* and *bayes.pdf*.
- Concept learning, version spaces, consistent learners, squared-error likelihood, MDL, Bayes-optimal classification, Gibbs, and Bayesian networks: *lec04-BayesianLearning.pdf*, especially the sections corresponding to the course's Unit 2 scope.
