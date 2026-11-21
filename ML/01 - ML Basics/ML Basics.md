# Chapter 1. Introduction to Machine Learning

[Contents](../readme.md)

## 1.1 What a learner learns

A program learns when experience $E$ improves its performance $P$ on task $T$. In spam detection, the task is labeling messages, experience is a set of labeled messages, and performance can be measured by precision and recall on unseen mail.

| Setting | Learning signal | Example |
|---|---|---|
| Supervised learning | Target label or value for each training example | Classify spam or predict a house price |
| Unsupervised learning | Inputs with no target labels | Group customers or find a lower-dimensional representation |
| Reinforcement learning | Rewards from interacting with an environment | Learn a policy for navigating a robot |

Semi-supervised learning combines a small labeled set with unlabeled examples. Self-supervised learning builds targets from the inputs themselves, such as predicting a masked word. A lookup table can memorize observed cases. Inductive learning uses a rule that can also make predictions for unseen cases.

A hypothesis $h$ maps inputs $x\in\mathcal X$ to predictions. The **hypothesis space** $\mathcal H$ contains the candidate rules. In supervised learning, a training algorithm takes labeled pairs $S=\{(x_i,y_i)\}_{i=1}^{n}$ and chooses a rule from $\mathcal H$. The representation affects which rules the model can express. For example, XOR returns 1 exactly when two binary inputs differ. No line separates its two classes in the original plane. Adding the product $x_1x_2$ lets a linear rule in the expanded feature space separate them.

### From examples to rules

In **inductive learning**, a model extracts a rule that applies to new cases. One explicit version is the Inductive Learning Algorithm (ILA), which produces IF–THEN rules from categorical examples.

1. Group training rows by their class and mark all rows as uncovered.
2. Within one class, try conditions on one attribute at a time. Keep conditions that cover uncovered rows of that class and no rows of the other classes.
3. Choose the condition that covers the most uncovered rows. Add it as a rule and mark those rows. If no condition qualifies, try combinations of two attributes, then larger combinations.
4. Repeat for each class. Conflicting rows with identical attributes need a fallback, such as a majority label or leaving them unresolved.

If both rows with **weather = warm** are labeled Yes and no No row is warm, the rule **IF weather = warm THEN Yes** covers those two rows. If **weather = windy** occurs in both classes, try a second attribute such as location. Check the resulting rules on held-out data.

## 1.2 Design a learning system

1. **Specify the prediction.** Define the target, the unit of prediction, and the metric. For rare fraud, evaluate the minority class and choose a threshold based on the cost of missed fraud and false alarms.
2. **Collect and split the data.** Inspect missing values, label quality, duplicates, and class imbalance. Keep related records together when they could leak information across splits. Use time-ordered splits for forecasting.
3. **Prepare the inputs.** Impute, encode, scale, or select features. Fit all data-dependent transformations on training data and apply them to validation and test data. In cross-validation, repeat fitting within each training fold.
4. **Train, select, and evaluate.** Fit parameters on training data, choose hyperparameters on validation data, and report final performance on a held-out test set. After deployment, monitor errors and changes in the input distribution.

The project has three phases. **Define the value** by choosing a decision and a measurable goal. **Build a proof of concept** by collecting, preparing, exploring, and modeling the data. **Operate the model** by deploying it, monitoring input drift and error, and deciding when to retrain. A good validation score supports the proof of concept. Production also requires usable latency and stable input data.

| Goal | Typical output | Example |
|---|---|---|
| Classification | Class label or probability | Flag a transaction for review |
| Regression or forecasting | Numeric estimate | Forecast next week’s demand using past data |
| Clustering | Groups of similar cases | Segment customers by behavior |
| Anomaly detection | Unusualness score | Flag a transaction that departs from an account’s usual activity |
| Ranking | Ordered candidates | Rank search results using relevance signals |

### Data acquisition and representation

A sensor pipeline measures, conditions, digitizes, and logs a signal. Surveys, APIs, and transaction logs also need validation and provenance. Inspect distributions, missingness, outliers, and label balance before settling on features.

**Record matching.** “14 Park Rd.” and “14 Park Road” may identify the same address. Deterministic matching compares fields after a fixed normalization. Probabilistic linkage combines evidence from fields such as name and address, then applies a threshold to the match score. Keep records for the same entity in one split.

A representation can be a **scalar** such as temperature, a **vector** of measurements, a **matrix** with one row per case, or a **tensor** such as a height-by-width-by-channel image. Match the form to the model and preserve ordering when it matters. For example, a bag of words discards token order, while a sequence retains it.

Before modeling, check the source, consent or usage conditions, sampling coverage, labels, missingness, class balance, and duplicates. Collect additional data where the intended population is underrepresented. A large dataset still gives a biased evaluation if it misses the cases the model will face.

Use exploratory plots to decide what to clean or transform. A histogram can reveal skew or extreme values. A scatter plot can reveal a nonlinear relation or duplicate clusters. A grouped count can reveal class imbalance across sources. For a forecasting task, plot measurements over time before selecting a split or deciding whether a rolling feature is appropriate.

### Feature engineering

A feature is a measurable input available when the prediction is made. The aim is to expose useful structure and control noise, computation, and complexity. Methods can overlap. A rolling average, for example, is both a created variable and a summary extracted from a sequence.

| Operation | Decision it makes | Example |
|---|---|---|
| Creation | What new input can we derive from existing data? | Divide a total of ₹600 by 4 items to obtain a price of ₹150 per item. |
| Transformation | How should an existing input be represented? | Encode payment type with indicator columns for card and cash. |
| Extraction | How can we summarize several inputs in fewer values? | Replace seven daily purchase counts with their mean and standard deviation. |
| Selection | Which existing inputs should the model retain? | Keep purchase amount and quantity if validation shows that receipt ID adds no value. |
| Scaling | How can we put numeric inputs on comparable scales? | Express purchase amount and quantity in standard deviations from their respective training means. |

### Why feature engineering matters

The price-per-item feature above helps the model distinguish a large order from an expensive item. When several inputs carry similar information, a smaller representation can also reduce noise and computation.

**Dimensionality reduction** represents each example with fewer input variables while retaining information useful for the task. Feature selection keeps some original columns. Feature extraction can replace several columns with fewer derived ones. For example, principal component analysis (PCA) can turn three correlated measurements into one score by projecting them onto the direction of greatest variance. That score retains the main variation and discards variation in the other directions. Validate whether it also preserves information useful for prediction.

**Interaction features.** Suppose you want to predict delivery time. An extra kilometre might add 2 minutes off-peak but 5 minutes at rush hour. A linear model with separate inputs for distance and rush hour gives distance the same effect in both cases. Add a feature equal to distance × rush-hour flag, where the flag is 1 during rush hour and 0 otherwise. The new feature is zero off-peak and grows with distance during rush hour. This lets the model learn a different rate for each case.

**Worked example.** For a model that flags unusual purchases at checkout, split timestamps into hour and weekday, combine amount and quantity into unit price when quantity is positive, and count purchases in the preceding seven days. For 2, 4, and 6 prior purchases across three successive weeks, a three-week mean is 4. The count and mean must stop before the current purchase. A chargeback posted tomorrow would leak the outcome.

**Read the timeline from left to right.** The cutoff is the moment the model makes its prediction.

```text
Past 7 days           Checkout / predict       Tomorrow
[count past orders] -> [use current price] ----> [chargeback]
   available              available               too late
```

Compare the engineered features with a simple baseline on the same validation split. Keep a transformation only if it improves the relevant metric or yields a useful gain in interpretability or cost. Fit learned bins, imputers, scalers, selectors, and projections inside each training fold, then apply them to held-out data. Use the same transformations at inference. A scikit-learn pipeline, for example, can keep preprocessing and model fitting together during cross-validation.

| Transformation | How it works |
|---|---|
| Min–max scaling | $(x-x_{\min})/(x_{\max}-x_{\min})$ maps the training range to $[0,1]$ when the range is nonzero. |
| Standardization | $(x-\mu)/\sigma$ expresses the value in training standard deviations when $\sigma>0$. |
| Robust scaling | $(x-\operatorname{median})/\operatorname{IQR}$ uses the middle half of the training distribution to reduce the influence of extremes when the interquartile range is positive. |
| One-hot encoding | Creates a binary indicator per nominal category so that arbitrary integer codes do not imply an order. |

For missing values, choose a strategy that fits the data. A median imputation is a simple numeric baseline. A separate indicator can help when missingness carries information. For categorical data, an explicit missing or unknown level can be useful. Tools differ in how they expose these steps, but the same train-only fitting rule applies to code pipelines and automated workflows.

## 1.3 Generalization and runtime

Underfitting can produce high training and validation error. Overfitting often appears as low training error with appreciably worse validation error. Use the training and validation curves to spot these patterns. Check optimization and the data split before diagnosing the cause.

For a dense network with widths $d_0,\dots,d_L$, one forward pass costs about $O(\sum_{\ell=1}^L d_{\ell-1}d_\ell)$. Backpropagation has the same order. Training on $n$ examples for $E$ epochs costs about $O(En\sum_\ell d_{\ell-1}d_\ell)$, apart from implementation overhead. Prediction uses just the forward computation.

**Worked check.** A $3\to4\to2$ dense network has $3\cdot4+4\cdot2=20$ connection weights and $4+2=6$ biases. One prediction uses those 20 weighted connections. Training uses the forward and backward passes for each example.

### Select and compare models

Consider a model that predicts delivery time as $\hat t=wd+b$, where $d$ is the distance.

| Term | Definition | Delivery example | How it is set |
|---|---|---|---|
| **Parameter** | A value learned from training data and used to make predictions. | $w$ is the predicted time added per kilometre, and $b$ is the predicted time at zero distance. | Training fits $w$ and $b$ to past deliveries by minimizing a loss. |
| **Hyperparameter** | A setting that controls the model structure or how it trains. | The learning rate, such as $0.01$, controls the size of the updates to $w$ and $b$. | Set it before a training run. To tune it, train with several values and compare the results on validation data. |

Use a validation set or cross-validation to choose features, hyperparameters, and candidate models. Each fold trains on its training partition and scores its validation partition. Keep groups together for repeated records and keep the future out of training when forecasting. Voting or averaging combines models, but it still needs the same evaluation as any candidate.

| Task | What to inspect |
|---|---|
| Rare positive class | The confusion matrix, precision, recall, and F1. Compare thresholds by their cost or use ROC and precision–recall curves. |
| Numeric prediction | MAE for typical absolute error, or MSE when large errors should carry more weight. |
| Production choice | Validation score alongside latency, robustness, and interpretability. |

The test set provides a final estimate after those choices. Chapter 2 develops the confusion-matrix measures and their sampling uncertainty.

For search and recommendations, feature design can combine text similarity with context and recent behavior. Semantic retrieval compares meaning rather than relying only on exact word matches. A ranking model can use those signals to reorder results, and an experiment can measure whether that improves relevance for users.

## Practice

- **1A.** Define $T$, $E$, and $P$ for medical-image classification. Give one source of leakage and show how to prevent it.
- **1B.** Find the number of weights and biases in a $5\to3\to1$ dense network. Explain why training costs more than predicting one example.
- **1C.** For checkout-time fraud detection, classify one-hot encoding of payment type, a seven-day purchase count, and retaining the five highest-ranked columns by operation. Explain how you would validate the features without leaking future data.

## Answer checks

| Exercise | Answer |
|---|---|
| 1A | Task: predict a diagnosis from an image. Experience: labeled images. Performance: held-out sensitivity, specificity, or a suitable metric. Keep all scans from the same patient in one split. |
| 1B | 18 weights and 4 biases, for 22 parameters. Training repeats forward and backward passes across examples. |
| 1C | One-hot encoding is transformation. A seven-day count creates a feature by aggregation. Keeping the highest-ranked columns is selection. Compute counts from earlier events, fit the encoder and selector on training data, and compare with the baseline on later held-out data. |

[Chapter sources](../readme.md#1-introduction-to-machine-learning)

---

[Contents](../readme.md) · [Chapter 2 →](../02%20-%20Evaluation%20and%20Bayes/Evaluation%20and%20Bayes.md)
