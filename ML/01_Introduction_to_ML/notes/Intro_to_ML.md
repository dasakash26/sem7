# Introduction to Machine Learning

*Chapter 1*

> **Place in the course.** This chapter establishes the language used in every later unit: data, task, target, feature, hypothesis, training, evaluation, and generalization. It is based on the assessed portions of *01_ML-UNIT-1-notes.pdf*.

## Learning objectives

After studying this chapter, you should be able to:

1. state what it means for a program to learn from experience;
2. distinguish the main learning paradigms;
3. describe a machine-learning project from problem definition to deployment;
4. explain how acquisition, preparation, exploration, and feature engineering affect a model;
5. distinguish training, validation, and test data;
6. explain underfitting, overfitting, generalization, and data leakage.

---

## 1.1 What does it mean to learn?

Suppose we want a computer to recognize whether an email is spam. We could write a long list of rules:

- reject messages containing certain words;
- reject messages from certain addresses;
- accept messages from known contacts.

Such rules work until the environment changes. New words appear, legitimate messages contain suspicious words, and spammers change their behavior.

Machine learning takes a different approach. We provide examples and a procedure that adjusts a model so that its predictions improve.

The standard formulation is:

> A program learns from experience $E$ with respect to a task $T$ and a performance measure $P$ if its performance on $T$, measured by $P$, improves with experience $E$.

For the spam example:

- **Task $T$:** classify an email as spam or not spam;
- **Experience $E$:** previously labelled emails;
- **Performance measure $P$:** perhaps precision, recall, F1-score, or a cost-sensitive metric.

The purpose of learning is not to reproduce the training examples. It is to **generalize**: to make useful predictions for examples that were not seen during training.

### A model is a controlled compromise

A machine-learning model must be expressive enough to capture the useful pattern, but constrained enough not to memorize accidental details. This tension explains the later topics of model selection, regularization, and validation.

---

## 1.2 The objects in a learning problem

An **instance** is one example, such as one email or one patient's record. A **feature** is a measurable description of an instance, such as message length, word frequency, age, or blood pressure. A **target** or **label** is the desired answer.

For supervised learning, a dataset can be written as

$$
D=\{(\mathbf x_i,y_i)\}_{i=1}^{n},
$$

where $\mathbf x_i$ is the feature vector and $y_i$ is its target.

A **hypothesis** is a candidate function that maps features to predictions:

$$
h:\mathcal X\rightarrow\mathcal Y.
$$

The set of candidates available to a learning algorithm is the **hypothesis space** $H$. Training chooses, estimates, or updates a hypothesis using the observed data.

The choice of representation matters. The same real-world problem can be easy with informative features and difficult with a poor representation. This is why feature engineering appears before model selection in a practical workflow.

---

## 1.3 Learning paradigms

### Supervised learning

In supervised learning, each training input is paired with a desired output. The learner approximates an unknown relationship between inputs and targets.

**Classification** predicts a discrete value. Binary classification has two classes; multiclass classification has one of several mutually exclusive classes; multilabel classification allows several labels at once.

**Regression** predicts a continuous value such as price, temperature, or demand.

### Unsupervised learning

Unsupervised learning receives inputs without target labels. The aim is to discover structure rather than reproduce a supplied answer.

- **Clustering** groups similar instances.
- **Dimensionality reduction** represents data using fewer dimensions while preserving useful structure.
- **Association discovery** finds recurring relationships among variables or events.

### Semi-supervised learning

Semi-supervised learning uses a small labelled dataset together with a larger unlabelled dataset. It is useful when collecting raw data is inexpensive but assigning labels requires expert time.

### Self-supervised learning

In self-supervised learning, the data itself supplies a temporary learning signal. A model may hide part of an input and learn to reconstruct it, or predict one part from another. The resulting representation can later be adapted to a supervised task.

### Reinforcement learning

Reinforcement learning is organized around an agent, an environment, actions, states, and rewards. The agent learns through interaction and seeks a policy that maximizes long-term reward. It is different from supervised learning because the correct action is not supplied for every state.

The paradigms answer different questions:

| Available feedback | Natural learning setting |
|---|---|
| A target is supplied for each example | Supervised learning |
| No target is supplied | Unsupervised learning |
| A few targets and many raw examples | Semi-supervised learning |
| Feedback is generated from the input itself | Self-supervised learning |
| Feedback arrives as rewards after actions | Reinforcement learning |

---

## 1.4 The machine-learning project

A useful project is not “choose an algorithm and fit it.” It is a sequence of decisions in which errors early in the sequence can invalidate later results.

### Stage 1: define the problem

State the decision to be made, the population affected, the available inputs, the prediction horizon, and what counts as success. Ask whether a traditional rule-based solution is sufficient. If the outcome is probabilistic, ask whether the available data can support a reliable model.

The performance measure must be chosen before training. Optimizing accuracy is inappropriate if missing a positive case is much more costly than generating a false alarm.

### Stage 2: acquire data

Data may come from sensors, experiments, surveys, transaction systems, public datasets, APIs, or carefully governed web collection. Acquisition is part of modelling because the sampling process determines what the model can learn.

Ask:

- Does the data represent the population on which the model will be used?
- Are the labels accurate and consistently defined?
- Are important groups or conditions absent?
- Can the data be stored and accessed reliably?

### Stage 3: prepare the data

Preparation includes filtering, validation, cleansing, formatting, aggregation, and reconciliation. Missing values, duplicated records, inconsistent units, impossible values, and timestamp errors should be investigated before model training.

Data preparation often consumes more project time than fitting the model. A sophisticated algorithm cannot compensate for systematically incorrect data.

### Stage 4: explore and visualize

Exploratory data analysis asks what the data actually look like. Histograms reveal distributions; scatter plots reveal relationships; box plots expose spread and possible outliers; bar charts show category counts; heatmaps summarize correlations.

Visualization is not decoration. It can reveal leakage, imbalance, measurement errors, or a subgroup for which the model will behave differently.

### Stage 5: model

Choose a family of hypotheses appropriate to the representation and task. A linear model may be preferable when the relationship is simple and interpretability matters. A neural network or tree ensemble may be justified when the relationship is more complex and sufficient data are available.

### Stage 6: engineer features

Feature engineering turns raw observations into a representation that makes the desired relationship easier to learn. It can include:

- creating domain-informed variables;
- extracting date, text, image, or signal properties;
- transforming skewed variables;
- encoding categorical variables;
- scaling numerical variables;
- combining or aggregating measurements;
- removing irrelevant or redundant variables.

Good features represent the phenomenon clearly, preserve relevant context, and make important relationships easier for the chosen model to express.

### Stage 7: deploy and monitor

Deployment makes predictions available to a user or another system. A deployable model must be compatible with its environment, sufficiently fast, robust to missing or unusual inputs, and monitored after release.

The data distribution may change. Labels may arrive later. User behavior may adapt to the model. Deployment is therefore part of an iteration loop rather than the final page of the project.

### Three phases

The seven stages can be grouped into three practical phases:

1. **Business value:** define the problem and why it matters.
2. **Proof of concept:** acquire, prepare, explore, model, and engineer features.
3. **Production:** deploy, monitor, interpret, and improve.

---

## 1.5 Data acquisition and matching

In a data-acquisition system, sensors measure a physical quantity, a signal conditioner amplifies or filters it, an analogue-to-digital converter represents it numerically, and a logger or storage system preserves the measurements for analysis.

The same logic applies to non-physical sources. A survey response, API record, or transaction must be collected, validated, identified, and stored with enough context to be useful.

### Record matching

Suppose two databases contain customer records. We may need to decide whether “A. Das, 14 Park Road” and “Akash Das, 14 Park Rd.” refer to the same person.

**Deterministic matching** uses explicit rules, such as exact equality after a fixed normalization. It is easy to inspect and requires little training data, but it can miss spelling variations and unusual cases.

**Probabilistic linkage** extracts comparison features and assigns a score representing how likely two records are to refer to the same entity. It is more flexible but requires labelled matches, careful threshold selection, and more explanation of why a match was accepted.

Typical applications include deduplication, data integration, fraud detection, and building a unified customer profile.

---

## 1.6 Feature engineering in more detail

Feature engineering is best understood as a sequence of questions:

1. **Creation:** can domain knowledge produce a more meaningful variable?
2. **Transformation:** should a skewed, bounded, or nonlinear quantity be transformed?
3. **Extraction:** can a large object such as text, an image, or a signal be summarized?
4. **Selection:** which variables carry useful information?
5. **Scaling:** are numerical variables on comparable scales?

### Scaling

Min-max normalization maps a variable to a chosen interval, commonly $[0,1]$:

$$
x'=\frac{x-x_{\min}}{x_{\max}-x_{\min}}.
$$

Standardization expresses a value in standard-deviation units:

$$
z=\frac{x-\mu}{\sigma}.
$$

Normalization fixes a range; standardization centers and rescales using the mean and standard deviation. Neither automatically makes a feature useful.

### Categorical variables

Label encoding replaces categories by integers. This is appropriate only when the integers do not introduce a false ordering, or when the model treats the values as categories. One-hot encoding creates one binary indicator per category and is often safer for nominal variables.

### Selection methods

Filter methods score features without fitting the final model, using criteria such as correlation, chi-square, or mutual information. Wrapper methods evaluate candidate subsets using a model. Embedded methods perform selection while fitting, as L1 regularization and tree-based importance can do.

---

## 1.7 Training, validation, and test data

The training set estimates parameters. The validation set helps choose hyperparameters, features, architecture, and stopping time. The test set is reserved for a final estimate after those decisions have been made.

Using test performance to choose a model quietly turns the test set into validation data. The resulting estimate is optimistic because information from the test set has influenced the selection process. This is **data leakage**.

Cross-validation is useful when the training data are limited. In $k$-fold cross-validation, the data are divided into $k$ folds; each fold serves once as validation data while the remaining folds are used for training. The scores are then averaged.

### Parameters and hyperparameters

Parameters are learned from data: weights, coefficients, and biases. Hyperparameters are chosen by the designer: learning rate, regularization strength, tree depth, number of hidden units, and so on.

### Runtime and complexity

Training cost includes repeated passes through the data and the cost of calculating updates. Inference cost is the cost of producing one prediction after training. A model can be accurate yet unsuitable for a real-time system if its inference cost, memory use, or latency is too high.

Model selection therefore considers predictive quality, training cost, inference cost, interpretability, reliability, and the cost of errors.

---

## 1.8 Generalization, underfitting, and overfitting

**Underfitting** occurs when the hypothesis space or training process is too limited to capture the useful pattern. Training and validation errors are both high.

**Overfitting** occurs when the model fits noise or accidental details of the training data. Training error is low, while validation or test error is substantially higher.

A good model has low enough error on unseen data and a manageable gap between training and validation performance.

Common ways to reduce overfitting include collecting more representative data, simplifying the model, removing irrelevant features, regularization, early stopping, cross-validation, and data augmentation.

---

## 1.9 Learning by rote and learning by induction

Learning by rote stores repeated facts without constructing a general rule. It is useful for memorizing fixed information, but it does not by itself support generalization.

Learning by induction constructs a rule from examples. In machine learning, an induction algorithm searches a hypothesis space for a rule that explains the observed examples and can be applied to new instances.

This contrast clarifies the purpose of the entire subject: a model is valuable when it extracts a reusable pattern rather than merely storing examples.

---

## Chapter summary

Machine learning learns a hypothesis from experience so that performance on a task improves. The quality of the result depends on the problem definition, data acquisition, preparation, representation, model, evaluation design, and deployment environment.

The project can be remembered as:

> **Define → acquire → prepare → explore → represent → model → evaluate → deploy → monitor**

The next chapter studies a principled way to compare hypotheses when uncertainty matters: Bayesian learning.

---

## Review questions

1. State the roles of task $T$, experience $E$, and performance measure $P$.
2. Distinguish classification, regression, clustering, and reinforcement learning.
3. Why can a model with excellent training accuracy still be poor in practice?
4. What information belongs in the problem-definition stage?
5. Explain deterministic and probabilistic record linkage.
6. Distinguish normalization from standardization.
7. Why must the test set be protected during model selection?
8. Give one example of underfitting and one example of overfitting.

---

## Source guide

- Learning paradigms and machine-learning stages: *01_ML-UNIT-1-notes.pdf*, pages 1-8 and 24-74.
- The CT sheet excludes pages 9-23 from the Unit 1 reading range.
