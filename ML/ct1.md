---
title: Machine Learning — Complete CT Notes
aliases:
  - ML CT Notes
  - Machine Learning CT Scope
tags:
  - machine-learning
  - bayesian-learning
  - artificial-neural-networks
  - feed-forward-neural-network
  - class-test
status: enhanced-from-ct-scope
source: https://www.youtube.com/playlist?list=PLW5Q_JcFMZa8
scope-source: handwritten CT topic sheet supplied 2026-09-20
language: English
---

# Machine Learning — Complete CT Notes

> [!abstract] Scope
> These notes now follow the handwritten CT topic sheet supplied by the student. The assessed material includes **Introduction, Bayesian Learning, Artificial Neural Networks, Multilayer Perceptrons/FFNN, Hidden Markov Models and CRFs**. This replaces the earlier assumption that HMMs and CRFs were outside the CT.
>
> The exact PDF/page mapping is recorded in [[00_Course_Guide/CT_SCOPE_MAP|CT Scope Map]]. Where the handwriting says “video” or names a web source, these notes explain the required concepts without pretending that an unidentified video or page was verified.

## Quick navigation

- [[#Module 1 — Introduction]]
- [[#Module 2 — Bayesian Learning]]
- [[#Module 3 — Artificial Neural Networks]]
- [[#Module 4 — Feed Forward Neural Networks]]
- [[#Module 5 — Hidden Markov Models]]
- [[#Module 6 — Conditional Random Fields]]
- [[#HMM vs CRF]]
- [[#High-yield comparisons]]
- [[#CT formula sheet]]
- [[#Likely CT questions]]
- [[#Final 15-minute revision checklist]]

---

# Module 1 — Introduction

## 1. What is Machine Learning?

Machine Learning (ML) enables a computer system to discover patterns from data and improve its performance without being explicitly programmed with every decision rule.

A standard definition is:

> A program learns from **experience** $E$ with respect to a **task** $T$ and **performance measure** $P$ if its performance on $T$, as measured by $P$, improves with $E$.

Examples:

- **Task:** classify email as spam or not spam.
- **Experience:** previously labelled emails.
- **Performance:** accuracy, precision, recall or F1-score.

The objective is not merely to memorize the training data. A successful model must **generalize**, meaning it must perform well on unseen examples.

## 2. Main types of learning

### 2.1 Supervised learning

The training set contains input-output pairs:

$$
D=\{(x_i,y_i)\}_{i=1}^{n}
$$

The model learns a mapping $f:X\rightarrow Y$ so that $f(x)$ predicts the correct target $y$ for a new input.

#### Classification

The output is a discrete class.

- Binary: spam/not spam, diseased/healthy.
- Multiclass: cat/dog/fox.
- Multilabel: one sample may have several labels simultaneously.

#### Regression

The output is continuous, such as price, temperature or rainfall.

### 2.2 Unsupervised learning

The data have inputs but no known labels. The objective is to discover structure.

- **Clustering:** place similar samples in the same group.
- **Dimensionality reduction:** represent many features using fewer informative dimensions.
- **Pattern/association discovery:** identify relationships that were not supplied as labels.

### 2.3 Semi-supervised learning

Semi-supervised learning combines:

- a small labelled dataset, and
- a much larger unlabelled dataset.

It is useful when obtaining raw data is easy but manual labelling is expensive or slow. It lies between supervised and unsupervised learning.

> [!important] CT distinction
> Supervised learning learns from labelled examples, unsupervised learning finds hidden structure in unlabelled data, and semi-supervised learning uses both.

## 3. Typical machine-learning problems

| Learning setting | Problem | Output |
|---|---|---|
| Supervised | Classification | Discrete class |
| Supervised | Regression | Continuous value |
| Unsupervised | Clustering | Group/cluster assignment |
| Unsupervised | Dimensionality reduction | Lower-dimensional representation |
| Semi-supervised | Classification or regression with few labels | Predicted class/value |

## 4. Stages of a Machine Learning project

### 4.1 Define the problem

Specify:

- the prediction or discovery task;
- the input and expected output;
- whether the problem is classification, regression or clustering;
- constraints such as latency, interpretability and cost;
- the performance measure that determines success.

### 4.2 Acquire data

Data may come from sensors, files, surveys, transaction systems, web sources or existing databases.

Important concerns:

- Is the sample representative of the real population?
- Are the labels reliable?
- Is there enough data?
- Does data collection introduce sampling bias?

A general acquisition pipeline is:

```text
Physical/real-world signal
        ↓
Sensor or collection mechanism
        ↓
Analog/digital conversion when required
        ↓
Preprocessing
        ↓
Storage for later analysis
```

### 4.3 Clean and preprocess data

Common operations include:

- removing duplicates;
- correcting inconsistent values;
- handling missing data;
- detecting or treating outliers;
- converting types and units;
- reducing noise;
- normalization or standardization;
- encoding categorical variables.

#### Normalization

Min-max normalization usually maps values to $[0,1]$:

$$
x'=\frac{x-x_{\min}}{x_{\max}-x_{\min}}
$$

#### Standardization

Standardization produces approximately zero mean and unit variance:

$$
z=\frac{x-\mu}{\sigma}
$$

#### Encoding categories

- **Label encoding:** maps each category to an integer.
- **One-hot encoding:** creates a binary column for each category.

> [!warning] Do not confuse normalization and standardization
> Normalization rescales to a fixed range. Standardization measures distance from the mean in standard-deviation units.

### 4.4 Explore and visualize data

Exploratory Data Analysis (EDA) is used to understand distributions, relationships, trends, anomalies and possible data-quality problems.

Common visualizations:

- bar chart for categories;
- histogram for a numerical distribution;
- scatter plot for relationships;
- line chart for time trends;
- box plot for spread and possible outliers;
- correlation heatmap for relationships among numerical features.

Visualization can reveal structure that is difficult to notice in a table.

### 4.5 Feature engineering

A **feature** is a measurable property supplied to a model. Feature engineering converts raw data into a representation from which useful patterns can be learned.

It includes:

- creating new features from existing variables;
- transforming skewed variables;
- scaling numerical features;
- encoding categorical features;
- extracting components from dates, text or images;
- combining meaningful variables;
- removing irrelevant, redundant or highly noisy features.

Why it matters:

- good features can improve accuracy;
- fewer relevant features reduce time and memory;
- irrelevant features may increase overfitting;
- simpler feature sets often make a model easier to explain.

### 4.6 Feature selection

Feature selection keeps useful variables and removes unnecessary ones.

| Method | Main idea | Example |
|---|---|---|
| Filter | Score features independently of the final model | Correlation, chi-square, mutual information |
| Wrapper | Evaluate different feature subsets using a model | Forward selection, backward elimination |
| Embedded | Selection occurs while the model is trained | L1 regularization, tree importance |

### 4.7 Split the data

- **Training set:** used to learn parameters.
- **Validation set:** used to choose model design and hyperparameters.
- **Test set:** used once for final unbiased evaluation.

The test set must not influence training or model selection. Otherwise, **data leakage** occurs.

### 4.8 Select and train a model

Model selection means choosing the algorithm and configuration that best satisfy the problem requirements.

Consider:

- the nature and size of data;
- linear versus nonlinear relationships;
- predictive quality;
- training and inference cost;
- robustness;
- interpretability;
- probability calibration;
- risk of overfitting.

### 4.9 Tune hyperparameters

- **Parameters** are learned from data: weights, coefficients and biases.
- **Hyperparameters** are selected by the designer: learning rate, number of hidden layers, number of neurons, regularization strength, tree depth, etc.

Cross-validation estimates how well a candidate configuration generalizes.

In $k$-fold cross-validation:

1. Divide the training data into $k$ folds.
2. Train on $k-1$ folds and validate on the remaining fold.
3. Repeat until each fold has served as validation data.
4. Average the validation scores.

### 4.10 Evaluate and deploy

After selecting the final model, evaluate it on the untouched test set. If deployed, continue monitoring it because real-world data may change over time.

## 5. Underfitting, overfitting and generalization

### Underfitting

The model is too simple to capture the pattern.

- high training error;
- high validation/test error;
- associated with high bias.

### Overfitting

The model learns training-specific noise instead of the general pattern.

- very low training error;
- much higher validation/test error;
- associated with high variance.

### Good fit

Training and validation errors are both reasonably low and close to one another.

Common remedies for overfitting:

- obtain more data;
- simplify the model;
- remove irrelevant features;
- use regularization;
- apply early stopping;
- use cross-validation;
- use augmentation where appropriate.

## 6. Evaluation measures

For binary classification:

|                   |  Predicted positive |  Predicted negative |
| ----------------- | ------------------: | ------------------: |
| Actually positive |  True Positive (TP) | False Negative (FN) |
| Actually negative | False Positive (FP) |  True Negative (TN) |

### Accuracy

$$
\text{Accuracy}=\frac{TP+TN}{TP+TN+FP+FN}
$$

Accuracy may be misleading for imbalanced data.

### Precision

$$
\text{Precision}=\frac{TP}{TP+FP}
$$

Of all predicted positives, how many were actually positive?

### Recall / Sensitivity

$$
\text{Recall}=\frac{TP}{TP+FN}
$$

Of all actual positives, how many did the model detect?

### F1-score

$$
F_1=2\cdot\frac{\text{Precision}\cdot\text{Recall}}
{\text{Precision}+\text{Recall}}
$$

F1 is the harmonic mean of precision and recall.

> [!tip] Choosing a metric
> Use recall when missing a positive is especially costly. Use precision when a false alarm is especially costly. Use F1 when both matter and the classes are imbalanced.

---

# Module 2 — Bayesian Learning

## 1. Probability foundations

### Conditional probability

$$
P(A\mid B)=\frac{P(A\cap B)}{P(B)},\qquad P(B)>0
$$

The product rule is:

$$
P(A\cap B)=P(A\mid B)P(B)=P(B\mid A)P(A)
$$

### Bayes' theorem

$$
\boxed{P(A\mid B)=\frac{P(B\mid A)P(A)}{P(B)}}
$$

For hypothesis $h$ and data $D$:

$$
\boxed{P(h\mid D)=\frac{P(D\mid h)P(h)}{P(D)}}
$$

| Term | Meaning |
|---|---|
| $P(h)$ | Prior: belief in $h$ before seeing current data |
| $P(D\mid h)$ | Likelihood: probability of observing $D$ if $h$ is true |
| $P(D)$ | Evidence/marginal likelihood: total probability of the data |
| $P(h\mid D)$ | Posterior: updated belief after observing $D$ |

When hypotheses $h_i$ are mutually exclusive and exhaustive:

$$
P(D)=\sum_i P(D\mid h_i)P(h_i)
$$

## 2. Bayesian learning

Bayesian learning treats uncertainty explicitly. Instead of producing only one unqualified answer, it updates the probability of competing hypotheses using observed data.

Core update:

```text
Prior belief × likelihood of observed data
                    ↓
         normalized posterior belief
```

More evidence can strengthen or weaken a hypothesis depending on how likely that evidence is under the hypothesis.

## 3. MAP and Maximum Likelihood

### Maximum A Posteriori hypothesis

The most probable hypothesis after observing $D$ is:

$$
h_{MAP}=\arg\max_{h\in H}P(h\mid D)
$$

Using Bayes' theorem:

$$
h_{MAP}=\arg\max_{h\in H}\frac{P(D\mid h)P(h)}{P(D)}
$$

Since $P(D)$ is constant with respect to $h$:

$$
\boxed{h_{MAP}=\arg\max_{h\in H}P(D\mid h)P(h)}
$$

### Maximum Likelihood hypothesis

If all hypotheses have equal priors, MAP reduces to Maximum Likelihood (ML):

$$
\boxed{h_{ML}=\arg\max_{h\in H}P(D\mid h)}
$$

> [!important] MAP vs ML
> MAP uses both prior knowledge and observed data. Maximum Likelihood uses only the likelihood of the observed data. They are equivalent under a uniform prior.

## 4. Diagnostic-test example and the base-rate effect

Let:

- $C$: patient has cancer;
- $+$: test result is positive.

The required probability is not $P(+\mid C)$ but:

$$
P(C\mid +)=\frac{P(+\mid C)P(C)}
{P(+\mid C)P(C)+P(+\mid \neg C)P(\neg C)}
$$

Even a highly sensitive test can produce a surprisingly modest $P(C\mid +)$ when the disease is rare, because false positives arise from the much larger healthy population. This is the **base-rate effect**.

Do not reverse a conditional probability:

$$
P(+\mid C)\ne P(C\mid +)
$$

## 5. Bayesian classification

For class $c_j$ and sample $x$, predict the class with the largest posterior:

$$
\hat c=\arg\max_{c_j}P(c_j\mid x)
$$

Using Bayes' theorem and dropping the evidence $P(x)$, which is the same for every class:

$$
\boxed{\hat c=\arg\max_{c_j}P(x\mid c_j)P(c_j)}
$$

This is the Bayes/MAP classifier decision rule.

## 6. Bayesian Belief Networks

A Bayesian Network is a probabilistic graphical model represented by a **Directed Acyclic Graph (DAG)**.

### Components

- **Node:** a random variable.
- **Directed edge:** direct probabilistic influence/dependence.
- **DAG:** directed edges but no directed cycle.
- **Conditional Probability Table (CPT):** probability of a node for each assignment of its parents.

If variables are $X_1,\ldots,X_n$, the joint distribution factorizes as:

$$
\boxed{P(X_1,\ldots,X_n)=\prod_{i=1}^{n}P(X_i\mid Pa(X_i))}
$$

where $Pa(X_i)$ is the set of parents of $X_i$.

The graph compactly represents conditional dependencies. Missing direct links can indicate conditional independence, but independence must be interpreted relative to the graph structure and conditioning variables.

### Why Bayesian Networks are useful

- represent uncertain causal or dependency relationships;
- combine domain knowledge with observed data;
- answer probabilistic queries;
- support prediction and diagnosis;
- represent a large joint distribution compactly.

## 7. Naive Bayes classifier

Naive Bayes assumes features are conditionally independent once the class is known.

For $x=(x_1,\ldots,x_n)$:

$$
P(c\mid x_1,\ldots,x_n)\propto P(c)\prod_{i=1}^{n}P(x_i\mid c)
$$

Therefore:

$$
\boxed{\hat c=\arg\max_c P(c)\prod_{i=1}^{n}P(x_i\mid c)}
$$

The assumption is called “naive” because real features are often dependent. Nevertheless, it can work well, especially for high-dimensional text data.

### Text classification

Typical process:

1. Treat the document as a bag of words.
2. Estimate each class prior $P(c)$.
3. Estimate word likelihoods $P(w\mid c)$.
4. Multiply the prior by word likelihoods for the document.
5. Select the class with the greatest posterior score.

For document $d$:

$$
\hat c=\arg\max_c P(c)\prod_{w\in d}P(w\mid c)
$$

To prevent numerical underflow, use logarithms:

$$
\hat c=\arg\max_c\left[\log P(c)+\sum_{w\in d}\log P(w\mid c)\right]
$$

Laplace smoothing avoids zero probability for an unseen word:

$$
P(w\mid c)=\frac{count(w,c)+1}{\sum_{w'}count(w',c)+|V|}
$$

where $|V|$ is vocabulary size.

Applications include spam filtering, topic classification and sentiment analysis.

## 8. Weather/play example

To decide whether players will play when the weather is overcast, compare posterior scores such as:

$$
P(Play=Yes\mid Overcast)
$$

and

$$
P(Play=No\mid Overcast)
$$

Using the class-decision form, calculate:

$$
P(Overcast\mid Yes)P(Yes)
$$

and

$$
P(Overcast\mid No)P(No)
$$

Then choose the larger score. The common evidence $P(Overcast)$ need not be calculated when only the winning class is required.

---

# Module 3 — Artificial Neural Networks

## 1. Biological inspiration

ANNs are inspired by networks of biological neurons.

| Biological concept | ANN analogue |
|---|---|
| Dendrites | Input channels |
| Synapses | Weighted connections |
| Cell body/soma | Summation and processing unit |
| Axon | Output channel |
| Synaptic strength | Connection weight |
| Firing threshold | Bias/activation threshold |

The analogy motivates the model, but an artificial neuron is a mathematical abstraction rather than a complete simulation of a biological neuron.

## 2. Artificial neuron model

For inputs $x_1,\ldots,x_n$, weights $w_1,\ldots,w_n$, and bias $b$:

$$
z=\sum_{i=1}^{n}w_ix_i+b=\mathbf w^T\mathbf x+b
$$

The output is:

$$
\boxed{y=\phi(z)}
$$

where $\phi$ is the activation function.

Interpretation:

- the sign and magnitude of $w_i$ measure the influence of input $x_i$;
- a positive weight encourages activation;
- a negative weight inhibits activation;
- the bias shifts the decision boundary or effective threshold;
- the activation function determines the final response.

A threshold form is equivalent to a bias form:

$$
\sum_i w_ix_i\ge \theta
\quad\Longleftrightarrow\quad
\sum_i w_ix_i+b\ge 0,
\qquad b=-\theta
$$

## 3. Activation functions

### Binary step

$$
\phi(z)=
\begin{cases}
1,&z\ge0\\
0,&z<0
\end{cases}
$$

Used in the classical perceptron. It is not differentiable at zero and has zero derivative elsewhere.

### Sigmoid

$$
\sigma(z)=\frac{1}{1+e^{-z}}
$$

Range: $(0,1)$. Useful for interpreting a binary output as a probability, but it may suffer from vanishing gradients for large $|z|$.

### Tanh

$$
\tanh(z)=\frac{e^z-e^{-z}}{e^z+e^{-z}}
$$

Range: $(-1,1)$. It is zero-centred but can also saturate.

### ReLU

$$
ReLU(z)=\max(0,z)
$$

Simple and efficient; common in hidden layers. A neuron can become inactive if it remains in the negative region.

### Softmax

For $K$ output scores:

$$
softmax(z_i)=\frac{e^{z_i}}{\sum_{j=1}^{K}e^{z_j}}
$$

The outputs are non-negative and sum to one, making softmax suitable for multiclass classification.

## 4. Neural-network structure

- **Input layer:** receives feature values.
- **Hidden layer(s):** learn intermediate representations.
- **Output layer:** produces the prediction.

The number of hidden layers and neurons is an architectural/hyperparameter choice. It is selected experimentally using validation performance—not arbitrarily from the training score alone.

## 5. Network topologies

### Fully connected network

Every node in one layer connects to every node in the next relevant layer.

### Layered network

Nodes are divided into ordered subsets called layers.

### Feed-forward / acyclic network

Connections move from an earlier layer to a later layer; no directed cycle or feedback connection exists.

### Recurrent / cyclic network

Feedback or recurrent connections create cycles, allowing past state to influence later processing.

### Modular network

Several subnetworks/modules solve parts of the problem and their outputs are combined.

## 6. Learning in an ANN

Training changes the weights and biases so that predicted outputs become closer to targets.

```text
Input → prediction → compare with target → error/loss
                                      ↓
                         update weights and biases
                                      ↓
                                  repeat
```

- **Parameter learning:** learning weights and biases.
- **Architecture/model selection:** deciding layers, units, activations and other hyperparameters.

## 7. The perceptron

A perceptron is a single artificial neuron used as a binary linear classifier.

Prediction:

$$
\hat y=step(\mathbf w^T\mathbf x+b)
$$

Decision boundary:

$$
\mathbf w^T\mathbf x+b=0
$$

In two dimensions, this is a line; in higher dimensions it is a hyperplane.

### Perceptron learning rule

For learning rate $\eta$ and target $y$:

$$
\boxed{w_i\leftarrow w_i+\eta(y-\hat y)x_i}
$$

$$
\boxed{b\leftarrow b+\eta(y-\hat y)}
$$

If the output is correct, $y-\hat y=0$, so no update is needed. If incorrect, the weights and bias move the boundary toward a better classification.

### Algorithm

1. Initialize weights and bias.
2. Compute the weighted sum.
3. Apply the step function.
4. Compare prediction with target.
5. Update weights and bias when incorrect.
6. Repeat over the samples for multiple epochs.

The perceptron convergence theorem applies when the data are linearly separable.

## 8. Realizing logic gates

Using $y=step(w_1x_1+w_2x_2+b)$:

| Gate | One possible setting |
|---|---|
| AND | $w_1=1,w_2=1,b=-1.5$ |
| OR | $w_1=1,w_2=1,b=-0.5$ |
| NOT | $w=-1,b=0.5$ |
| NAND | $w_1=-1,w_2=-1,b=1.5$ |

Different valid weight-bias combinations are possible as long as every truth-table row is classified correctly.

## 9. Limitation of a single perceptron

A single perceptron can represent only linearly separable functions.

- AND and OR are linearly separable.
- XOR is not linearly separable.

No single straight decision boundary can separate the two positive XOR points from the two negative points. XOR therefore requires multiple perceptrons arranged in at least one hidden layer.

## 10. Multilayer Perceptron (MLP)

An MLP contains an input layer, one or more hidden layers and an output layer. Nonlinear activations allow it to learn nonlinear decision boundaries.

For layer $l$:

$$
\mathbf z^{(l)}=W^{(l)}\mathbf a^{(l-1)}+\mathbf b^{(l)}
$$

$$
\mathbf a^{(l)}=\phi^{(l)}(\mathbf z^{(l)})
$$

An MLP can combine simpler boundaries to represent XOR and other nonlinear functions.

---

# Module 4 — Feed Forward Neural Networks

## 1. Definition

A Feed Forward Neural Network (FFNN) is an acyclic neural network in which information travels from input to output without a feedback loop.

For adjacent-layer architecture:

```text
Input layer → Hidden layer 1 → … → Hidden layer L → Output layer
```

During **forward propagation**, activations move toward the output. During **backpropagation**, error derivatives move backward for learning; this backward calculation does not turn the inference graph into a recurrent network.

## 2. Forward propagation

For each layer $l$:

$$
\mathbf z^{(l)}=W^{(l)}\mathbf a^{(l-1)}+\mathbf b^{(l)}
$$

$$
\mathbf a^{(l)}=\phi^{(l)}(\mathbf z^{(l)})
$$

where $\mathbf a^{(0)}=\mathbf x$.

### Matrix dimensions

If layer $l-1$ has $n_{l-1}$ units and layer $l$ has $n_l$ units:

$$
W^{(l)}\in\mathbb R^{n_l\times n_{l-1}},\quad
\mathbf b^{(l)}\in\mathbb R^{n_l},\quad
\mathbf a^{(l)}\in\mathbb R^{n_l}
$$

For a batch, examples are processed together using matrix multiplication, making computation efficient.

## 3. Choosing the output layer

| Task | Output units | Common output activation | Common loss |
|---|---:|---|---|
| Regression | One or more | Linear | MSE |
| Binary classification | 1 | Sigmoid | Binary cross-entropy |
| Multiclass, one class | Number of classes | Softmax | Categorical cross-entropy |
| Multilabel classification | Number of labels | Independent sigmoids | Binary cross-entropy |

## 4. Loss functions

### Mean Squared Error

$$
L=\frac{1}{N}\sum_{i=1}^{N}(y_i-\hat y_i)^2
$$

Common for regression.

### Binary cross-entropy

$$
L=-\frac{1}{N}\sum_{i=1}^{N}
\left[y_i\log\hat y_i+(1-y_i)\log(1-\hat y_i)\right]
$$

### Multiclass cross-entropy

For one-hot target vector $\mathbf y$:

$$
L=-\sum_{k=1}^{K}y_k\log \hat y_k
$$

If the model assigns probability 1 to the correct class, cross-entropy approaches 0. If it assigns a very small probability to the correct class, the loss becomes large.

## 5. Backpropagation

Backpropagation efficiently computes the gradient of loss with respect to every weight and bias by repeatedly applying the chain rule from the output layer toward the input.

For a parameter $w$:

$$
\frac{\partial L}{\partial w}
=
\frac{\partial L}{\partial a}
\frac{\partial a}{\partial z}
\frac{\partial z}{\partial w}
$$

### Output-layer error

For a general differentiable output activation:

$$
\boldsymbol\delta^{(L)}=
\frac{\partial L}{\partial \mathbf a^{(L)}}
\odot \phi'^{(L)}(\mathbf z^{(L)})
$$

### Hidden-layer error

$$
\boxed{
\boldsymbol\delta^{(l)}=
\left(W^{(l+1)T}\boldsymbol\delta^{(l+1)}\right)
\odot\phi'^{(l)}(\mathbf z^{(l)})
}
$$

### Gradients

$$
\frac{\partial L}{\partial W^{(l)}}
=\boldsymbol\delta^{(l)}\mathbf a^{(l-1)T}
$$

$$
\frac{\partial L}{\partial \mathbf b^{(l)}}
=\boldsymbol\delta^{(l)}
$$

## 6. Computation graph intuition

A computation graph breaks a function into simple operations. Example:

$$
f(x_1,x_2,x_3,x_4)=(x_1+x_2)x_3-x_4
$$

Forward pass:

$$
u=x_1+x_2,\qquad v=ux_3,\qquad f=v-x_4
$$

Backward pass starts from $\frac{\partial f}{\partial f}=1$ and applies local derivatives in reverse order. This is reverse-mode automatic differentiation—the core idea used by backpropagation.

## 7. Gradient descent

After gradients are calculated:

$$
\boxed{W^{(l)}\leftarrow W^{(l)}-\eta\frac{\partial L}{\partial W^{(l)}}}
$$

$$
\boxed{\mathbf b^{(l)}\leftarrow \mathbf b^{(l)}-\eta\frac{\partial L}{\partial \mathbf b^{(l)}}}
$$

$\eta$ is the learning rate.

- Too large: updates may overshoot or diverge.
- Too small: learning can be extremely slow.

## 8. Complete FFNN training loop

```text
Initialize parameters
        ↓
Forward propagation
        ↓
Compute loss
        ↓
Backpropagate gradients
        ↓
Update weights and biases
        ↓
Repeat for batches and epochs
        ↓
Check validation performance
```

### Important terms

- **Epoch:** one complete pass through the training set.
- **Batch:** subset processed before one parameter update.
- **Iteration:** one update step.
- **Batch gradient descent:** entire training set per update.
- **Stochastic gradient descent:** one example per update.
- **Mini-batch gradient descent:** a small group per update; most common in practice.

## 9. Practical training issues

### Initialization

If every neuron begins with identical weights, neurons can learn identical features. Random initialization breaks symmetry. Extremely large initial weights can cause unstable or saturated activations.

### Local minima and saddle regions

The loss surface of a neural network is non-convex. Useful responses include:

- different random initializations;
- mini-batch optimization;
- momentum;
- adaptive optimizers;
- proper scaling and initialization.

### Momentum

Momentum accumulates part of the previous update:

$$
v_t=\beta v_{t-1}+\nabla L(W_t)
$$

$$
W_{t+1}=W_t-\eta v_t
$$

It can reduce oscillation and help optimization move through shallow regions.

### Vanishing gradients

Repeated multiplication by small derivatives makes early-layer gradients tiny, especially with saturated sigmoid or tanh units. ReLU-family activations and appropriate initialization help.

### Overfitting and early stopping

Training loss may continue to fall while validation loss starts to rise. Early stopping saves the parameters at or near the lowest validation loss.

```text
Training loss:   usually continues downward
Validation loss: falls, reaches a minimum, then may rise
                                      ↑
                                  stop here
```

Other controls include regularization, simpler architecture, more data and dropout.

## 10. Evaluating the trained network

For classification, calculate the confusion matrix and use accuracy, precision, recall and F1-score as appropriate. Loss and classification metrics answer different questions:

- **Loss** is the differentiable objective optimized during training.
- **Metric** is the interpretable quantity used to evaluate performance.

Do not judge generalization from training loss alone.

---

# Lecture-by-lecture three-part digest

| Lecture | Beginning | Middle | End |
|---|---|---|---|
| 1 — Types of Learning | ML idea and learning problems | Supervised, unsupervised and semi-supervised learning | Labelled examples, prediction flow and comparison of learning types |
| 2 — Stages of ML | Problem definition and the ML workflow | acquisition, preprocessing, storage, EDA and visualization | normalization, scaling, encoding and preparation for modelling |
| 3 — Feature Engineering & Model Selection | meaningful feature construction/selection | selecting models, validation, complexity and regularization | confusion matrix, accuracy, precision, recall and F1-score |
| 4 — Bayesian Learning I | probability and conditional probability | Bayes' theorem examples and Bayesian hypothesis learning | posterior comparison through examples such as weather/play |
| 5 — Bayesian Learning II | prior, likelihood, posterior, MAP and ML | diagnostic-test example and Bayesian networks | Naive Bayes, document/text classification and clustering discussion |
| 6 — ANN & Perceptrons | biological neuron and artificial-neuron mapping | ANN layers and topologies: fully connected, recurrent, acyclic, feed-forward and modular | weighted sum, threshold, learning weights/biases and perceptron introduction |
| 7 — Perceptrons & MLPs | perceptron equation, weights and bias | realizing AND, OR, NOT and related logic using perceptrons | XOR limitation and the need for multiple perceptrons/hidden layers |
| 8 — FFNN I | definition, layers and forward-only connections | matrix representation, batch processing and activation functions | backpropagation motivation and repeated weight correction |
| 9 — FFNN II | network training, optimization, local minima, momentum and early stopping | evaluation measures and cross-entropy loss | computation graphs, chain rule and analytical backpropagation |

---

# Module 5 — Hidden Markov Models

## 1. Why an HMM is needed

Ordinary classifiers treat examples independently. An HMM models a **sequence** in which an unobserved state changes over time and produces visible observations.

Examples:

- hidden weather state → observed umbrella/no umbrella;
- hidden fair/loaded die → observed die value;
- hidden part-of-speech tag → observed word;
- hidden phoneme → observed acoustic signal.

```text
S1  →  S2  →  S3  → … → ST       hidden states
↓      ↓      ↓          ↓
O1     O2     O3         OT       observations
```

The state is **hidden** because $S_t$ is not directly observed. We see $O_t$ and infer the state.

## 2. Markov assumptions

### First-order state assumption

The next state depends only on the current state:

$$
P(S_t\mid S_{1:t-1})=P(S_t\mid S_{t-1}).
$$

This is not the claim that every observation is independent. It is a conditional-independence assumption about the state sequence.

### Output-independence assumption

The current observation depends only on the current hidden state:

$$
P(O_t\mid S_{1:t},O_{1:t-1})=P(O_t\mid S_t).
$$

Together, these assumptions allow efficient dynamic-programming algorithms.

## 3. HMM parameters

An HMM is commonly written as $\lambda=(A,B,\pi)$:

| Parameter | Meaning | Formula |
|---|---|---|
| $\pi_i$ | initial probability of state $i$ | $P(S_1=i)$ |
| $a_{ij}$ | transition from state $i$ to $j$ | $P(S_t=j\mid S_{t-1}=i)$ |
| $b_j(o)$ | state $j$ emits observation $o$ | $P(O_t=o\mid S_t=j)$ |

Every row of $A$ sums to one. For a discrete observation model, every state's emission probabilities also sum to one.

The joint probability of a state path $s_{1:T}$ and observation sequence $o_{1:T}$ is:

$$
P(s_{1:T},o_{1:T})
=\pi_{s_1}b_{s_1}(o_1)
\prod_{t=2}^{T}a_{s_{t-1}s_t}b_{s_t}(o_t).
$$

Read this as: **start probability × first emission × every transition × every later emission**.

## 4. Three fundamental HMM problems

| Problem | Question | Algorithm |
|---|---|---|
| Evaluation | How probable is the observed sequence, $P(O\mid\lambda)$? | Forward algorithm |
| Decoding | What hidden state/path most likely produced the observations? | Forward–backward for state marginals; Viterbi for best complete path |
| Learning | Which parameters best explain the observations? | Baum–Welch, an EM algorithm |

Do not confuse **most probable state at each time** with the **most probable complete state sequence**. Choosing the locally best state at each position need not produce the globally best path.

## 5. Forward algorithm

Define:

$$
\alpha_t(j)=P(o_1,\ldots,o_t,S_t=j\mid\lambda).
$$

### Initialization

$$
\alpha_1(j)=\pi_jb_j(o_1).
$$

### Recursion

$$
\alpha_t(j)=b_j(o_t)\sum_i\alpha_{t-1}(i)a_{ij}.
$$

### Termination

$$
P(O\mid\lambda)=\sum_j\alpha_T(j).
$$

Meaning: add all ways of reaching state $j$, then multiply by the probability that $j$ emits the current observation.

### Tiny numerical example

Suppose states are Rainy ($R$) and Sunny ($S$):

- $\pi_R=0.6,\ \pi_S=0.4$;
- $a_{RR}=0.7,\ a_{RS}=0.3,\ a_{SR}=0.4,\ a_{SS}=0.6$;
- $P(Umbrella\mid R)=0.9,\ P(Umbrella\mid S)=0.2$.

For observations $(U,U)$:

$$
\alpha_1(R)=0.6(0.9)=0.54,\qquad
\alpha_1(S)=0.4(0.2)=0.08.
$$

$$
\alpha_2(R)=0.9[0.54(0.7)+0.08(0.4)]=0.369,
$$

$$
\alpha_2(S)=0.2[0.54(0.3)+0.08(0.6)]=0.042.
$$

Therefore $P(U,U)=0.369+0.042=0.411$.

## 6. Backward and forward–backward

Define the probability of future observations given the present state:

$$
\beta_t(i)=P(o_{t+1},\ldots,o_T\mid S_t=i,\lambda).
$$

Initialize $\beta_T(i)=1$ and recurse backward:

$$
\beta_t(i)=\sum_j a_{ij}b_j(o_{t+1})\beta_{t+1}(j).
$$

The posterior probability of state $i$ at time $t$ is proportional to:

$$
P(S_t=i\mid O,\lambda)\propto\alpha_t(i)\beta_t(i).
$$

Normalize across states to make the values sum to one.

## 7. Viterbi decoding

The forward algorithm **sums** over paths. Viterbi replaces the sum with a **maximum** and stores backpointers.

$$
\delta_1(j)=\pi_jb_j(o_1),
$$

$$
\delta_t(j)=b_j(o_t)\max_i[\delta_{t-1}(i)a_{ij}].
$$

Store:

$$
\psi_t(j)=\arg\max_i[\delta_{t-1}(i)a_{ij}]
$$

and trace backward from the best final state. The result is the single most likely complete state sequence.

## 8. Baum–Welch learning

When the state sequence is unknown, Baum–Welch uses Expectation–Maximization:

1. Initialize $A,B,\pi$.
2. **E-step:** use forward–backward to calculate expected state occupancies and expected transitions.
3. **M-step:** normalize those expected counts to update $\pi$, $A$ and $B$.
4. Repeat until likelihood stops improving significantly.

Likelihood does not decrease at each EM iteration, but the method may converge to a local optimum and depends on initialization.

## 9. Complexity and exam answer structure

For $N$ states and sequence length $T$, forward, backward and Viterbi require $O(N^2T)$ time for a dense transition matrix. Direct enumeration considers exponentially many state paths.

For a long answer: define the model → draw the two-layer chain → state the two independence assumptions → define $A,B,\pi$ → list evaluation/decoding/learning → derive the requested recursion → show initialization, recursion and termination.

---

# Module 6 — Conditional Random Fields

## 1. What problem a CRF solves

A Conditional Random Field directly models the probability of an output-label sequence given an observed input sequence:

$$
P(\mathbf y\mid\mathbf x).
$$

For sentence tagging, $\mathbf x$ is the sequence of words and $\mathbf y$ is the sequence of tags. A linear-chain CRF allows neighboring labels to depend on each other while using rich features of the whole observed input.

```text
y1  —  y2  —  y3  — … — yT      output labels
│      │      │          │
└──────┴──── x1:T ───────┘       observed sequence available to features
```

The undirected label chain encodes dependencies among neighboring labels. The entire input sequence is observed.

## 2. Linear-chain CRF equation

One common form is:

$$
P(\mathbf y\mid\mathbf x)
=\frac{1}{Z(\mathbf x)}
\exp\left(\sum_{t=1}^{T}\sum_{k=1}^{K}
\lambda_k f_k(y_{t-1},y_t,\mathbf x,t)\right).
$$

| Symbol | Meaning |
|---|---|
| $f_k$ | feature function, such as “current word is capitalized and current tag is PERSON” |
| $\lambda_k$ | learned weight of feature $k$ |
| $Z(\mathbf x)$ | partition function that normalizes over all possible label sequences |

The partition function is:

$$
Z(\mathbf x)=\sum_{\mathbf y'}
\exp\left(\sum_{t,k}\lambda_kf_k(y'_{t-1},y'_t,\mathbf x,t)\right).
$$

Without $Z(\mathbf x)$, the sequence scores are not probabilities. After division, probabilities over all possible $\mathbf y$ sum to one.

## 3. Feature functions

CRF features can use:

- the current and previous labels;
- the current word and nearby words;
- prefixes, suffixes, capitalization and digits;
- dictionary membership;
- any observed property of the input sequence.

A positive weight rewards a matching feature; a negative weight penalizes it. The model learns these weights from labelled sequences.

## 4. Training

For labelled examples $(\mathbf x^{(i)},\mathbf y^{(i)})$, maximize conditional log-likelihood:

$$
\ell(\lambda)=
\sum_i\left[
\sum_{t,k}\lambda_k f_k(y_{t-1}^{(i)},y_t^{(i)},\mathbf x^{(i)},t)
-\log Z(\mathbf x^{(i)})
\right].
$$

With L2 regularization, subtract a term proportional to $\sum_k\lambda_k^2$. The gradient has an important interpretation:

$$
\text{gradient}=	ext{empirical feature counts}
-\text{model-expected feature counts}
-\text{regularization term}.
$$

Training repeatedly computes the partition function and label marginals with forward–backward, then updates the weights with a numerical optimizer.

## 5. Inference

- **Partition function and marginals:** forward–backward dynamic programming.
- **Best label sequence:** Viterbi-style dynamic programming.
- For $M$ labels and length $T$, a dense linear chain typically costs $O(TM^2)$ per sequence.

## 6. HMM versus CRF

An HMM models a joint distribution and generates observations:

$$
P(\mathbf x,\mathbf y)=P(\mathbf y)P(\mathbf x\mid\mathbf y).
$$

A CRF directly models:

$$
P(\mathbf y\mid\mathbf x).
$$

Therefore an HMM must specify how states emit observations, while a CRF can use overlapping, dependent input features without modeling how the input itself was generated.

## 7. CRF exam answer structure

Define CRF → draw the linear chain → write $P(\mathbf y\mid\mathbf x)$ → explain feature functions and weights → define $Z(\mathbf x)$ → explain training as conditional likelihood → name forward–backward and Viterbi → compare with HMM.

---

# HMM vs CRF

| Aspect | HMM | Linear-chain CRF |
|---|---|---|
| Type | Generative, directed | Discriminative, conditional and undirected |
| Models | $P(\mathbf x,\mathbf y)$ | $P(\mathbf y\mid\mathbf x)$ |
| Observation assumptions | Current observation depends on current state | Can use many overlapping features of the observed input |
| Parameters | Initial, transition and emission probabilities | Feature weights and label-transition weights |
| Normalization | Local probability tables | Global partition function $Z(\mathbf x)$ |
| Best sequence | Viterbi | Viterbi-style decoding |
| Marginals | Forward–backward | Forward–backward |
| Typical task | Sequence modeling when a generative story is useful | Sequence labeling with rich observed features |

Memory sentence: **HMM explains how hidden states generated the observations; CRF scores label sequences after the observations are already given.**

---

# High-yield comparisons

## Supervised vs unsupervised vs semi-supervised

| Aspect | Supervised | Unsupervised | Semi-supervised |
|---|---|---|---|
| Labels | All/most training samples labelled | No labels | Few labelled, many unlabelled |
| Objective | Predict a target | Discover structure | Improve prediction using both sets |
| Typical task | Classification/regression | Clustering/dimensionality reduction | Classification with costly labels |

## MAP vs Maximum Likelihood

| Aspect | MAP | Maximum Likelihood |
|---|---|---|
| Objective | Maximize $P(D\mid h)P(h)$ | Maximize $P(D\mid h)$ |
| Uses prior? | Yes | No |
| Same result when | Priors are uniform | Priors are uniform |

## Perceptron vs MLP

| Aspect | Single perceptron | MLP |
|---|---|---|
| Hidden layers | None | One or more |
| Boundary | Linear | Nonlinear with nonlinear activations |
| XOR | Cannot represent | Can represent |
| Training | Perceptron rule | Backpropagation + gradient-based optimization |

## Forward propagation vs backpropagation

| Aspect | Forward propagation | Backpropagation |
|---|---|---|
| Direction | Input to output | Output loss toward earlier layers |
| Purpose | Compute prediction and loss | Compute gradients |
| Main operations | Weighted sums and activations | Chain rule and local derivatives |
| Changes parameters? | No | Supplies gradients used by optimizer |

## Precision vs recall

| Metric | Main question | Penalized error |
|---|---|---|
| Precision | How many predicted positives are correct? | False positives |
| Recall | How many actual positives were detected? | False negatives |

---

# CT formula sheet

$$
P(A\mid B)=\frac{P(A\cap B)}{P(B)}
$$

$$
P(h\mid D)=\frac{P(D\mid h)P(h)}{P(D)}
$$

$$
h_{MAP}=\arg\max_h P(D\mid h)P(h)
$$

$$
h_{ML}=\arg\max_h P(D\mid h)
$$

$$
\hat c_{NB}=\arg\max_c P(c)\prod_iP(x_i\mid c)
$$

$$
z=\mathbf w^T\mathbf x+b,\qquad y=\phi(z)
$$

$$
w_i\leftarrow w_i+\eta(y-\hat y)x_i,qquad
b\leftarrow b+\eta(y-\hat y)
$$

$$
\mathbf z^{(l)}=W^{(l)}\mathbf a^{(l-1)}+\mathbf b^{(l)},qquad
\mathbf a^{(l)}=\phi(\mathbf z^{(l)})
$$

$$
W\leftarrow W-\eta\nabla_WL
$$

$$
\text{Accuracy}=\frac{TP+TN}{TP+TN+FP+FN}
$$

$$
\text{Precision}=\frac{TP}{TP+FP},\qquad
\text{Recall}=\frac{TP}{TP+FN}
$$

$$
F_1=\frac{2PR}{P+R}
$$

$$
\alpha_1(j)=\pi_jb_j(o_1),\qquad
\alpha_t(j)=b_j(o_t)\sum_i\alpha_{t-1}(i)a_{ij}
$$

$$
\delta_t(j)=b_j(o_t)\max_i[\delta_{t-1}(i)a_{ij}]
$$

$$
P(\mathbf y\mid\mathbf x)=\frac{1}{Z(\mathbf x)}
\exp\left(\sum_{t,k}\lambda_kf_k(y_{t-1},y_t,\mathbf x,t)\right)
$$

---

# Likely CT questions

## Very short answers

1. **What is generalization?**  
   Good performance on previously unseen data, not merely on training samples.

2. **What is a feature?**  
   A measurable input variable supplied to a learning model.

3. **What is a prior?**  
   Probability assigned to a hypothesis before observing the current evidence.

4. **Why can $P(A\mid B)$ not be replaced with $P(B\mid A)$?**  
   They condition on different events and generally have different values.

5. **Why is Naive Bayes called naive?**  
   It assumes conditional independence of features given the class.

6. **What does a neural-network weight represent?**  
   The strength and direction of one input's influence.

7. **What is the role of bias?**  
   It shifts the activation threshold or decision boundary.

8. **Why can a single perceptron not solve XOR?**  
   XOR is not linearly separable.

9. **Why is an FFNN acyclic?**  
   Its connections move toward later layers and do not form a directed feedback loop.

10. **What does backpropagation calculate?**  
    Gradients of the loss with respect to all trainable parameters.

11. **What is hidden in an HMM?**  
    The state sequence; the observation sequence is visible.

12. **What are the three main HMM problems?**  
    Evaluation, decoding and learning.

13. **Why is the CRF partition function needed?**  
    It normalizes scores over every possible label sequence so they form a conditional probability distribution.

14. **What is the key difference between HMM and CRF?**  
    HMM models the joint distribution $P(\mathbf x,\mathbf y)$; CRF directly models $P(\mathbf y\mid\mathbf x)$.

## Five-mark answer skeletons

### Explain the ML pipeline

Define the problem → acquire data → clean/preprocess → explore/visualize → engineer/select features → split data → select/train model → tune using validation/cross-validation → evaluate on untouched test data → deploy and monitor.

### Derive the MAP rule

Start with $h_{MAP}=\arg\max_hP(h\mid D)$. Apply Bayes' theorem. Remove $P(D)$ because it is constant across hypotheses. Obtain $h_{MAP}=\arg\max_hP(D\mid h)P(h)$. State that uniform priors reduce MAP to Maximum Likelihood.

### Explain a Bayesian Network

Define it as a probabilistic DAG. Explain nodes, directed edges and CPTs. State the joint factorization $\prod_iP(X_i\mid Pa(X_i))$. Mention compact dependency representation, inference and diagnosis.

### Explain the perceptron algorithm

Draw/describe inputs, weights, bias, summation and step activation. Write $\hat y=step(\mathbf w^T\mathbf x+b)$. Give the two update equations. State that convergence requires linear separability and explain the XOR limitation.

### Explain backpropagation

Perform forward propagation, compute loss, start from output error, apply the chain rule backward layer by layer, calculate weight/bias gradients, then update parameters using gradient descent. Repeat across batches and epochs, while checking validation performance.

### Explain an HMM

Define hidden states and observations. Draw the state/observation chain. State the first-order and output-independence assumptions. Define $\pi$, transition matrix $A$ and emission model $B$. Explain evaluation with forward, state marginals with forward–backward, best-path decoding with Viterbi and learning with Baum–Welch.

### Explain a linear-chain CRF

Define $P(\mathbf y\mid\mathbf x)$, draw the label chain, write the globally normalized exponential score, explain feature functions and $Z(\mathbf x)$, then describe conditional-likelihood training and dynamic-programming inference. End with its difference from an HMM.

## Numerical practice checklist

- [ ] Compute a posterior using Bayes' theorem.
- [ ] Compare two hypotheses using MAP scores without unnecessarily calculating $P(D)$.
- [ ] Classify a short document using Naive Bayes and log scores.
- [ ] Calculate a perceptron output from given $x$, $w$ and $b$.
- [ ] Perform one perceptron weight update.
- [ ] Verify AND/OR/NOT truth tables using chosen weights and bias.
- [ ] Perform one small forward pass through an MLP.
- [ ] Calculate accuracy, precision, recall and F1 from a confusion matrix.
- [ ] Compute MSE or cross-entropy for a small example.
- [ ] Compute one two-step HMM forward probability.
- [ ] Distinguish forward summation from Viterbi maximization.
- [ ] Identify $\pi$, transition and emission probabilities from an HMM description.
- [ ] Explain what a CRF feature and partition function do.

---

# Common mistakes

> [!danger] Avoid these in the CT
> - Treating $P(A\mid B)$ and $P(B\mid A)$ as equal.
> - Calling the posterior a likelihood.
> - Forgetting the prior in MAP.
> - Saying ML and MAP are always identical.
> - Assuming accuracy is reliable for every imbalanced dataset.
> - Using the test set to tune hyperparameters.
> - Calling weights hyperparameters; weights are learned parameters.
> - Forgetting the bias term in a neuron or perceptron.
> - Claiming a single perceptron can solve XOR.
> - Saying backpropagation itself updates weights; it calculates gradients, while the optimizer applies the update.
> - Confusing a backward gradient calculation with a backward/recurrent inference connection.
> - Treating an HMM observation as the hidden state.
> - Using a maximum in the forward algorithm; forward sums paths, Viterbi maximizes paths.
> - Choosing the most likely state independently at each time and calling it the Viterbi path.
> - Saying a CRF models $P(\mathbf x,\mathbf y)$; a CRF models $P(\mathbf y\mid\mathbf x)$.
> - Calling $Z(\mathbf x)$ a trainable parameter; it is the input-dependent normalizer.

---

# Final 15-minute revision checklist

- [ ] Define supervised, unsupervised and semi-supervised learning with one example each.
- [ ] Recite the complete ML pipeline in order.
- [ ] Know normalization, standardization and encoding.
- [ ] Explain training, validation and test sets.
- [ ] Draw a confusion matrix and write all four main metrics.
- [ ] Write and label every term in Bayes' theorem.
- [ ] Derive MAP and state when it becomes Maximum Likelihood.
- [ ] Define Bayesian Network, DAG, node, edge and CPT.
- [ ] Write the Naive Bayes decision rule.
- [ ] Draw an artificial neuron and write $y=\phi(\mathbf w^T\mathbf x+b)$.
- [ ] Explain weight, bias, threshold and activation.
- [ ] Write the perceptron learning rule.
- [ ] Explain why XOR requires an MLP.
- [ ] Write the layer-wise FFNN forward equations.
- [ ] Explain the steps of backpropagation and gradient descent.
- [ ] Know MSE, cross-entropy, early stopping and momentum.
- [ ] Draw an HMM and define initial, transition and emission probabilities.
- [ ] State the forward, forward–backward, Viterbi and Baum–Welch purposes.
- [ ] Write the linear-chain CRF probability and explain $f_k$, $\lambda_k$ and $Z(\mathbf x)$.
- [ ] Compare HMM and CRF in one sentence and one table.

---

# Source videos

The URLs below are the original lectures 1–9 recorded in this file. The later handwritten CT sheet also names HMM, freeCodeCamp and CRF video material, but it does not identify the exact external video URLs. The HMM/CRF chapters above are grounded in the local assigned PDFs and standard formulas.

1. [Lecture 1 — Introduction to ML: Types of Learning](https://www.youtube.com/watch?v=Eej9ftP_nRI)
2. [Lecture 2 — Stages of Machine Learning](https://www.youtube.com/watch?v=ETu3dLPsonk)
3. [Lecture 3 — Feature Engineering & Model Selection](https://www.youtube.com/watch?v=Q6G9Qdg_cu0)
4. [Lecture 4 — Bayesian Learning, Part 1](https://www.youtube.com/watch?v=nV55Jh3Eyok)
5. [Lecture 5 — Bayesian Learning, Part 2](https://www.youtube.com/watch?v=UhctVeLMMWs)
6. [Lecture 6 — Artificial Neural Networks & Perceptrons](https://www.youtube.com/watch?v=t04vCl0TcCA)
7. [Lecture 7 — Perceptrons & Multilayer Perceptrons](https://www.youtube.com/watch?v=iYIJjw3HOVA)
8. [Lecture 8 — Feed Forward Neural Network, Part 1](https://www.youtube.com/watch?v=7QIKBMnPXS4)
9. [Lecture 9 — Feed Forward Neural Network, Part 2](https://www.youtube.com/watch?v=8U8dcgCCc6A)
