# Exam patch and practice: Units 1–3 + FFNN

This is a new study supplement, not text extracted from your lecturer's notes. It fills identified gaps and standardizes formulas; it does not establish the teacher's exact exam questions. For the source audit, see [notes analysis](NOTES_ANALYSIS.md). Work the problems before reading the answer checks.

## 1. Formal learning model and runtime

A learning problem specifies a **task T**, **experience E**, and **performance measure P**. Example: spam classification (T), labeled messages (E), held-out classification accuracy or precision/recall (P).

More formally, let inputs be X, outputs Y, hypothesis class H, and training sample S={(x_i,y_i)} drawn from a distribution D. A learner maps S to a hypothesis h in H. Training fit measures agreement with S; generalization measures performance on new data from the target distribution. Representation controls which functions H can express. Design issues include label noise, missing data, imbalance, leakage, overfitting, compute limits and deployment distribution shift.

Runtime depends on sample count n, feature dimension d, model size and training iterations. For a dense FFNN with layer widths d_0,…,d_L, one forward pass costs approximately O(sum d_(l-1)d_l); backpropagation has the same order of cost. With E epochs over n examples, training is approximately O(E n sum d_(l-1)d_l), ignoring implementation overhead. Parameter count, including biases, is sum (d_(l-1)+1)d_l. Training time and prediction time are different quantities.

Rote learning memorizes cases; inductive learning generalizes from examples. A classification task uses labeled classes; supervised regression predicts a numeric target. Feature preprocessing must be learned on training data and then applied to validation/test data.

## 2. Hypothesis evaluation and sampling theory

For a fixed classifier h tested on n independent examples not used to fit or select it:

$$\hat p = \frac{\text{correct predictions}}{n},\qquad \hat e=1-\hat p.$$

If correctness indicators are independent Bernoulli variables with a common success probability p, their sum is Binomial(n,p). The estimate has variance p(1-p)/n; estimate its standard error as:

$$SE(\hat p)\approx\sqrt{\frac{\hat p(1-\hat p)}{n}}.$$

A simple large-sample approximate 95% interval is hat-p ± 1.96 SE. This normal approximation is unreliable for small samples or proportions near 0 or 1; do not treat it as exact. A confidence interval describes repeated-sampling coverage of the method, not a 95% probability assigned to a fixed unknown parameter after observing this particular interval.

**Exercise:** 80 correct predictions out of 100. Answer: accuracy .80, error .20, SE .04, approximate interval [.7216,.8784]. This assumes a representative independent test sample and no test-driven model selection.

**Train / validation / test:** train learns weights; validation chooses hyperparameters, architecture and stopping epoch; the test set estimates final generalization after these choices. Cross-validation rotates held-out validation folds within the development data. Fit preprocessing separately inside each training fold to avoid leakage.

**Comparing algorithms:** use the same data splits and metric, comparable tuning budgets, and repeated evaluations when feasible. Distinguish comparing two already-trained classifiers from comparing training procedures across datasets/splits. Report variability, not just the better single score. Paired predictions on the same test examples are dependent; do not blindly use an independent-samples formula. A paired test such as McNemar's compares disagreement counts for two classifiers; overlapping cross-validation folds also create dependence. For imbalanced data, accuracy alone can hide failure on the minority class.

## 3. Bayesian essentials

$$P(h|D)=\frac{P(D|h)P(h)}{P(D)},\quad h_{MAP}=\arg\max_h P(D|h)P(h),\quad h_{ML}=\arg\max_h P(D|h).$$

Uniform hypothesis priors make MAP and ML select the same maximizers. In noise-free concept learning with a uniform prior and the target in H, inconsistent hypotheses have posterior zero; consistent hypotheses form the version space and share posterior mass equally.

Naive Bayes assumes features are conditionally independent given class:

$$\hat c=\arg\max_c P(c)\prod_j P(x_j|c).$$

Compute an unnormalized score per class and normalize only when a posterior probability is requested. If a categorical feature has K possible values, Laplace smoothing gives (count+1)/(class count+K).

Bayes optimal predicts by averaging predictions across hypotheses using posterior weights, rather than selecting only the MAP hypothesis. A Bayesian network is a DAG with a factorization:

$$P(X_1,\ldots,X_m)=\prod_i P(X_i|Parents(X_i)).$$

For A→B and A→C, P(A,B,C)=P(A)P(B|A)P(C|A); B and C are conditionally independent given A under this graph.

**Exercise:** P(C1)=.6, P(C2)=.4. For observed binary features A=1,B=1, conditional probabilities are .5,.8 under C1 and .25,.2 under C2. Scores: .6×.5×.8=.24 and .4×.25×.2=.02. Predict C1; normalized posterior .24/.26≈.92308 under the model assumptions.

**Bayes-optimal exercise:** posterior hypothesis weights .4,.3,.3; the first predicts positive, the other two negative. MAP hypothesis predicts positive; Bayes optimal predicts negative with combined mass .6.

## 4. ANN essentials

Neuron: z=w-transpose x+b, a=g(z). With a binary perceptron output a=1 if z≥0, otherwise 0:

$$w\leftarrow w+\eta(t-a)x,\qquad b\leftarrow b+\eta(t-a).$$

This mistake-driven rule is distinct from gradient descent through a sigmoid network. A single perceptron creates one linear decision boundary. XOR is not linearly separable in its original two-dimensional input space.

| Activation | Value | Derivative |
|---|---|---|
| Sigmoid | 1/(1+exp(-z)) | a(1-a) |
| Tanh | tanh(z) | 1-a² |
| ReLU | max(0,z) | 1 for z>0, 0 for z<0; a convention is needed at zero |
| Identity | z | 1 |

One explicit XOR construction uses threshold step(s)=1 if s≥0: h1=step(x1+x2-.5), h2=step(x1+x2-1.5), output=step(h1-2h2-.5). Verify all four binary inputs. This demonstrates representation; hard thresholds are unsuitable for ordinary gradient backpropagation.

**Perceptron exercise:** x=(1,0), t=1, w=(-.2,.1), b=0, eta=.1. Initial z=-.2 and a=0. Updated w=(-.1,.1), b=.1. The model need not classify all samples correctly after one update.

**Parameter exercise:** a fully connected 2–3–1 network has (2+1)×3+(3+1)×1=13 trainable parameters. Input nodes are not trainable neuron layers; state your layer-count convention.

## 5. Risk, outputs and loss

Population risk is expected loss over the unknown data distribution; empirical risk averages loss over the training sample:

$$R(\theta)=\mathbb E_{(x,y)\sim D}[\ell(f_\theta(x),y)],\qquad \hat R(\theta)=\frac1n\sum_i\ell(f_\theta(x_i),y_i).$$

A regularized objective is J=hat-R + lambda/2 × sum of squared weights for L2 regularization. This convention adds lambda W to a weight gradient; biases are often excluded. Low training risk alone does not establish low population risk.

| Task | Output activation | Typical loss |
|---|---|---|
| Real-valued regression | Identity | Squared error |
| Binary classification | Sigmoid | Binary cross entropy |
| One of K classes | Softmax | Categorical cross entropy |
| Multiple independent labels | One sigmoid per label | Sum of binary cross entropies |

$$\ell_{BCE}=-[y\log a+(1-y)\log(1-a)],\qquad \ell_{CE}=-\sum_k y_k\log a_k.$$

Softmax uses a_k=exp(z_k)/sum_j exp(z_j). For stable numerical evaluation, subtract max(z) from every logit first. A loss of half the squared error has derivative a-y; ordinary unscaled squared error has derivative 2(a-y). Follow the loss specified in the question.

## 6. One consistent backpropagation convention

Use column vectors; a^(0)=x. At every layer:

$$z^{(l)}=W^{(l)}a^{(l-1)}+b^{(l)},\qquad a^{(l)}=g_l(z^{(l)}).$$

Define delta^(l)=partial L / partial z^(l). For a sigmoid output and half squared error:

$$\delta^{(L)}=(a^{(L)}-y)\odot a^{(L)}\odot(1-a^{(L)}).$$

For sigmoid with binary cross entropy, or softmax with categorical cross entropy, the combined output derivative simplifies to delta^(L)=a^(L)-y (before batch averaging). Do not multiply by a sigmoid derivative a second time in that case.

Hidden layers and gradients:

$$\delta^{(l)}=((W^{(l+1)})^T\delta^{(l+1)})\odot g_l'(z^{(l)}),$$
$$\frac{\partial L}{\partial W^{(l)}}=\delta^{(l)}(a^{(l-1)})^T,\qquad \frac{\partial L}{\partial b^{(l)}}=\delta^{(l)}.$$

Update W←W-eta×gradient, b←b-eta×gradient. Compute all deltas using the original forward-pass weights before applying any update. For mean mini-batch loss, average sample gradients. Backpropagation computes gradients; an optimizer uses them to change parameters.

### Worked numerical: one input, one hidden sigmoid, one sigmoid output

Given x=1, target y=1, hidden w1=.5, b1=0, output w2=.5, b2=0, eta=.1. Loss L=half×(a2-y)².

1. Forward: z1=.5; a1=sigmoid(.5)=.622459. z2=.5×.622459=.311230; a2=.577185. L≈.089386.
2. Output delta: (a2-1)×a2×(1-a2)≈-.103185.
3. Hidden delta: old w2×delta2×a1×(1-a1)≈-.012124.
4. Gradients: dL/dw2=delta2×a1≈-.064228; dL/db2=delta2; dL/dw1=delta1×x≈-.012124; dL/db1=delta1.
5. Updated parameters: w1≈.501212, b1≈.001212, w2≈.506423, b2≈.010318.

Recompute the forward pass: the output should move toward 1 and this sample's loss should decrease for this step. Then redo the exercise with target y=0 to check the direction of the update. More hidden neurons repeat the same operations; sum all downstream contributions to each hidden delta.

## 7. Training, initialization and model selection

- **Initialization:** identical hidden units initialized identically receive identical updates under the same conditions, preventing useful symmetry breaking. Random weights solve this; zero biases are usually fine. Xavier normal commonly uses variance 2/(fan-in+fan-out); He normal commonly uses 2/fan-in for ReLU. Match the activation and convention used in the question.
- **Batch:** one gradient from all n examples. **SGD:** one example per update. **Mini-batch:** a subset per update. An epoch processes the training dataset once.
- **Learning rate:** too small can slow progress; too large can cause instability. Momentum accumulates a velocity; Adam uses moving averages of gradients and squared gradients with bias correction. Do not assume an optimizer guarantees a global optimum.
- **Overfitting:** low training loss but worse validation performance. Responses include regularization, early stopping, more representative data, or reduced capacity. Underfitting may require more capacity, better features or better optimization.
- **L2:** discourages large weights through the penalty. **Dropout:** randomly masks activations during training; standard inverted dropout rescales retained activations by 1/(1-p), then uses all units without that masking at inference.
- **Early stopping:** monitor validation performance, allow an appropriate patience window, and restore the best validation checkpoint. Do not stop on the first tiny fluctuation or use test loss for stopping.
- **Model selection:** compare candidate architectures/hyperparameters on validation data or cross-validation; evaluate the selected procedure on untouched test data. Standardize inputs using training-set statistics.

## Final recall prompts

Explain without notes: why nonlinearity matters; why naive Bayes needs conditional independence; why train accuracy is optimistic; why a hidden delta sums downstream effects; why subtracting a negative gradient increases a weight; why risk minimization can overfit; why validation and test data have separate roles.
