# Chapter 5. Classification and Prediction

The preceding chapter discovered relationships without designating one target attribute. Classification instead learns from examples whose correct categories are already known. The learned relationship is then used to label previously unseen objects. A bank can learn from transactions labeled legitimate or fraudulent; a news service can learn to assign stories to sports, finance, or weather.

## 1. The Classification Problem

A **training record** contains predictor attributes and a known **class label**. A classifier learns a model $f$ that maps an attribute vector $x$ to a class:

$$
\hat y=f(x).
$$

The hat indicates a predicted value. Learning from known labels is **supervised learning**. **Classification** predicts a discrete category. **Numeric prediction**, commonly formulated as regression, predicts a numerical value. Predicting “buys a computer: yes/no” is classification; predicting the amount spent is numeric prediction. Clustering differs because the groups are discovered without supplied class labels.

Model construction and model evaluation serve different purposes:

1. Select the predictor attributes and target class; clean and encode the data.
2. Separate data for training and evaluation.
3. Fit preprocessing parameters and the classifier using training data.
4. Choose model settings using validation data or cross-validation within the training data.
5. Evaluate the selected model on an untouched test set.
6. Apply the model to genuinely new records.

The test set estimates generalization. Training accuracy only measures how well the model fits observations it has already seen. Repeatedly selecting settings using the final test set makes that estimate optimistic.

Missing values, noise, irrelevant attributes, incompatible scales, imbalance, and the cost of different errors influence model quality. The class label must not be used as an input feature when predicting an unknown class.

### Families of Classification Methods

| Family | Main idea | Typical representation |
|---|---|---|
| Decision trees | Recursively split records using attribute tests | Tree with tests and class-labeled leaves |
| Rule-based methods | Predict through conditions and conclusions | IF conditions THEN class rules |
| Memory-based reasoning | Compare a new object with stored examples | Labeled instances, as in KNN |
| Neural networks | Learn weighted transformations from features to outputs | Layers of connected computational units |
| Naive Bayes and Bayesian belief networks | Relate features and classes through probabilities | Conditional probability models |
| Support vector machines | Learn a decision boundary with a large separating margin | Boundary defined using support vectors |

## 2. Distance-Based Classification and KNN

Distance-based classification assigns an object according to nearby labeled examples or representatives. A class can be represented by its mean vector (**centroid**), an observed representative minimizing total within-class dissimilarity (**medoid**), or its individual records.

For $d$ numerical attributes, Euclidean distance is

$$
\operatorname{dist}(x,z)=\sqrt{\sum_{f=1}^{d}(x_f-z_f)^2}.
$$

If income is measured in rupees and age in years, income can dominate this calculation. Scaling makes the dimensions comparable when equal influence is appropriate. Learn the scaling values from the training data, then apply the same transformation to validation, test, and new records.

### K-Nearest Neighbors Algorithm

**KNN** is a nonparametric, instance-based method: it stores labeled training examples and postpones most prediction work until a query arrives. It does not fit a fixed-form boundary during ordinary training.

**Inputs:** training vectors and labels, query $x$, integer $K$ with $1\le K\le n$, distance function, and a tie rule. **Output:** predicted class and optionally the neighborhood vote fractions.

1. Store the prepared training records.
2. Calculate the distance from $x$ to every training record.
3. Select the $K$ records with the smallest distances.
4. Count $C_i$, the selected neighbors from class $i$.
5. Compute the vote fraction $C_i/K$ for each class.
6. Return the class with the largest vote fraction. Apply the declared tie rule if necessary.

The maintained information during prediction is the set of nearest records and the counts for their classes. The process terminates after the nearest $K$ have been selected and voted. A vote fraction is local class support, not automatically a calibrated probability.

### Worked KNN Example

Use two already comparable numerical features. The stored training data are:

| Record | Coordinates | Class | Squared distance to $x=(3,2)$ | Distance |
|---|---|---|---:|---:|
| A | $(1,1)$ | Red | $(3-1)^2+(2-1)^2=5$ | $\sqrt5=2.236$ |
| B | $(2,2)$ | Red | $1$ | $1$ |
| C | $(4,2)$ | Blue | $1$ | $1$ |
| D | $(6,5)$ | Blue | $18$ | $4.243$ |
| E | $(1,3)$ | Red | $5$ | $2.236$ |

Let $K=3$. Break equal distances by record order. The nearest neighbors are B, C, and A. Red has two votes and Blue has one:

$$
\operatorname{vote}(\text{Red})=2/3,\qquad
\operatorname{vote}(\text{Blue})=1/3.
$$

The prediction is **Red**. The A/E tie does not affect this result because both are Red. These are stored training observations and one new query; the vote alone is not a test accuracy estimate.

### Choosing K and Understanding the Tradeoffs

For $K=1$, one nearby example determines the label, which makes the classifier sensitive to noise. Increasing $K$ smooths decisions but can include distant observations and favor a large class. Select $K$ by validation rather than choosing it after inspecting test results.

Use $K=1$ as a baseline. The heuristic $K<\sqrt n$ suggests starting candidates, not an optimal value. An odd $K$ avoids a two-class vote tie when all selected votes have equal weight, but does not resolve all multiclass or distance ties.

Advantages include a simple mechanism, flexible decision boundaries, and little conventional fitting. Limitations include memory use, slow prediction, sensitivity to irrelevant dimensions and scale, and performance loss in high-dimensional spaces. Data compression can reduce storage but may remove useful neighborhood information.

With brute-force distance computation, one query needs $O(nd)$ arithmetic work. Fully sorting the distances adds $O(n\log n)$; maintaining only the closest $K$ can avoid a full sort. Storing the data requires $O(nd)$ space. “No training time” means no substantial parameter fitting in the basic method; storing, preprocessing, and indexing data still require work.

## 3. Decision Trees and Recursive Partitioning

A **decision tree** predicts by applying tests from a root to a leaf. Internal nodes test attributes, edges represent test outcomes, and leaves supply class labels. Each root-to-leaf path can be expressed as a rule.

For example, the following small model predicts house ownership from monthly income in thousands of rupees and age in years:

```text
Income >= 101?
  Yes -> Owns a house
  No  -> Age >= 54?
           Yes -> Owns a house
           No  -> Does not own a house
```

A prediction follows only one path. Each test partitions the records reaching that node. Recursive partitioning seeks children whose class distributions are purer than the parent's distribution.

Different trees may fit the same data. A greedy induction method chooses a locally good split at each node; it does not search all possible trees for a globally best one.

### Tracing the Refund Example

The Refund/Marital Status/Taxable Income tree makes these decisions:

```text
Refund = Yes -> Cheat = No
Refund = No:
  Marital Status = Married -> Cheat = No
  Marital Status = Single or Divorced:
    Income < 80K  -> Cheat = No
    Income >= 80K -> Cheat = Yes
```

For (No, Married, 80K), follow Refund=No and then Married, reaching **No**; the income test is not visited. For (No, Single, 85K), follow the final income branch and predict **Yes**. The complementary inequalities cover equality at 80K. Reordering the first two tests can give another tree that fits the same training records.

### Hunt's Algorithm

Let $D_t$ denote the training records that reach node $t$. The algorithm maintains the tree, the current subset at each node, and the remaining candidate tests.

**Inputs:** labeled training set, legal attribute tests, split-selection measure, stopping rules, and default/tie policy. **Output:** a class-labeled decision tree.

1. Create a root containing the training data.
2. If $D_t$ contains only one class, make $t$ a leaf with that class.
3. If $D_t$ is empty, use a default class, normally the parent's majority class.
4. If records cannot be usefully distinguished by the available attributes, stop and use the majority class of $D_t$.
5. Otherwise evaluate candidate tests and select the best permitted split.
6. Partition $D_t$ according to the test, create children, and recursively apply the same procedure to each child.
7. Stop when every active branch has reached a terminal condition.

When identical predictor values have different labels, no attribute test can separate those examples. Majority labeling is needed rather than infinite recursion.

Hunt's framework underlies several tree methods. ID3 uses information gain; C4.5 uses gain ratio in its selection procedure; CART commonly uses Gini for classification and binary splits. SLIQ and SPRINT are also named as scalable tree approaches using Gini.

### Attribute Test Conditions

| Attribute type | Legal test forms | Example |
|---|---|---|
| Nominal | One branch per value, or a binary partition of categories | CarType: Family, Sports, Luxury; or Family versus Sports/Luxury |
| Ordinal | Ordered branches or a threshold separating consecutive rank groups | Small/Medium versus Large |
| Continuous | A threshold, or discretization followed by categorical tests | Income $<v$ versus Income $\ge v$ |

For an order-preserving ordinal threshold, Small/Large versus Medium is inappropriate because it breaks the natural ordering. For nominal values there is no such order restriction.

For $r$ distinct sorted numerical values, there are $r-1$ meaningful internal boundaries. Midpoints between consecutive distinct values provide convenient thresholds. Equal values must stay on the same side. The inequality pair must cover every value exactly once.

Static discretization creates intervals before induction. Node-specific threshold selection adapts splits to the subset at the current node. The choice of test condition and the choice of best split are separate decisions.

## 4. Impurity and Split Selection

Let $p(j\mid t)$ be the proportion of records at node $t$ belonging to class $j$. A pure node contains one class. An impure node mixes classes.

### Gini Index

$$
\operatorname{Gini}(t)=1-\sum_{j=1}^{c}p(j\mid t)^2,
$$

where $c$ is the number of classes. Gini is 0 at a pure node and reaches $1-1/c$ for equal class frequencies.

### Entropy and Information Gain

$$
H(t)=-\sum_{j=1}^{c}p(j\mid t)\log_2p(j\mid t).
$$

Use $0\log_2 0=0$ by continuity. Entropy is 0 at a pure node and reaches $\log_2c$ for equal class frequencies. It measures uncertainty about the class before seeing more information.

For children $D_1,\ldots,D_k$ formed from $D_t$, the weighted child impurity is

$$
M_{\mathrm{split}}=\sum_{i=1}^{k}\frac{|D_i|}{|D_t|}M(D_i),
$$

where $M$ is Gini, entropy, or another impurity. The split improvement is

$$
\Delta M=M(D_t)-M_{\mathrm{split}}.
$$

When $M=H$, the improvement is **information gain**. The best split maximizes gain, equivalently minimizes weighted child entropy. The child weights are essential: a nearly pure child with two records should not have the same influence as a child with two hundred records.

### Misclassification Error

If a node predicts its majority class, its error fraction is

$$
\operatorname{Error}(t)=1-\max_jp(j\mid t).
$$

For a binary node, the measures compare as follows:

| Class counts | Gini | Entropy in bits | Misclassification error |
|---|---:|---:|---:|
| $(0,6)$ | 0 | 0 | 0 |
| $(1,5)$ | 0.2778 | 0.6500 | 0.1667 |
| $(2,4)$ | 0.4444 | 0.9183 | 0.3333 |
| $(3,3)$ | 0.5000 | 1.0000 | 0.5000 |

Entropy and Gini respond more smoothly to changes in class distribution than majority error. Suppose a parent has counts $(7,3)$ and is split into $(3,0)$ and $(4,3)$:

$$
\operatorname{Gini}(\text{parent})=1-0.7^2-0.3^2=0.42,
$$

$$
\operatorname{Gini}_{\mathrm{split}}
=\frac3{10}(0)+\frac7{10}\left(1-\left(\frac47\right)^2-\left(\frac37\right)^2\right)
=0.342857.
$$

Gini decreases by 0.077143. Parent majority error is 0.3, and weighted child majority error is also $0+\frac7{10}\frac37=0.3$. Error therefore misses this improvement in purity.

### Gain Ratio

Information gain tends to favor attributes with many distinct values. A unique identifier can create pure one-record leaves without providing a useful general rule.

Define partition information by

$$
\operatorname{SplitInfo}=-\sum_{i=1}^{k}\frac{|D_i|}{|D_t|}
\log_2\frac{|D_i|}{|D_t|}.
$$

Then

$$
\operatorname{GainRatio}=\frac{\operatorname{Gain}}{\operatorname{SplitInfo}}.
$$

The denominator measures how extensively the records are partitioned, regardless of class. It penalizes fragmentation into many small groups. A split with SplitInfo = 0 is noninformative and must not be divided by zero. Full C4.5 applies selection safeguards; simply maximizing any ratio, including ratios from negligible gains, is not a complete description of the implementation.

For a balanced binary split of eight records, SplitInfo = 1. If the parent entropy is 1 and both children are pure, information gain and gain ratio are both 1.

### Categorical and Continuous Split Calculations

For categorical CarType counts Family $(1,4)$, Sports $(2,1)$, Luxury $(1,1)$, the multiway weighted Gini is

$$
\frac5{10}\left(1-\left(\frac15\right)^2-\left(\frac45\right)^2\right)
+\frac3{10}\left(1-\left(\frac23\right)^2-\left(\frac13\right)^2\right)
+\frac2{10}\left(1-2\left(\frac12\right)^2\right)
=0.393333.
$$

Grouping Sports/Luxury against Family gives counts $(3,2)$ and $(1,4)$ and weighted Gini 0.400000. Grouping Sports against Family/Luxury gives $(2,1)$ and $(2,5)$ and weighted Gini 0.419048. Under this comparison, the multiway split has the lowest child Gini.

For a numerical attribute, sorting once and moving a boundary across the ordered values avoids rescanning all records for every threshold. Maintain left and right class counts, move the next value group, update the counts, and calculate weighted impurity.

The income example has ordered values and labels:

```text
Income: 60  70  75  85  90  95  100 120 125 220
Cheat:  No  No  No Yes Yes Yes No  No  No  No
```

At threshold $v=97.5$, the left counts are Yes = 3, No = 3; the right counts are Yes = 0, No = 4:

$$
\operatorname{Gini}_{\mathrm{split}}=\frac6{10}(0.5)+\frac4{10}(0)=0.3.
$$

Scanning all internal midpoints gives the complete comparison. Counts are ordered (Yes,No).

| Threshold | Left counts | Right counts | Weighted Gini |
|---|---|---|---:|
| 65 | (0,1) | (3,6) | 0.400000 |
| 72.5 | (0,2) | (3,5) | 0.375000 |
| 80 | (0,3) | (3,4) | 0.342857 |
| 87.5 | (1,3) | (2,4) | 0.416667 |
| 92.5 | (2,3) | (1,4) | 0.400000 |
| 97.5 | (3,3) | (0,4) | 0.300000 |
| 110 | (3,4) | (0,3) | 0.342857 |
| 122.5 | (3,5) | (0,2) | 0.375000 |
| 172.5 | (3,6) | (0,1) | 0.400000 |

The parent has counts (3,7) and Gini 0.42. Threshold 97.5 minimizes weighted Gini and gives gain 0.12. Boundaries outside the observed range leave an empty child and give no improvement. Threshold 80 can still be useful deeper in a tree on a different subset.

## 5. Complete Decision Tree Example: Object Table

The 2025 paper gives ten records and asks for class Target Location for the query $(\text{Triangle},\text{Medium},\text{Light},\text{Blue})$.

| ID | Shape | Size | Weight | Color | Target Location |
|---|---|---|---|---|---|
| 1 | Square | Small | Light | Red | Shelf 1 |
| 2 | Circle | Medium | Heavy | Blue | Shelf 2 |
| 3 | Rectangle | Small | Light | Green | Shelf 3 |
| 4 | Triangle | Large | Heavy | Red | Shelf 4 |
| 5 | Circle | Small | Light | Blue | Shelf 2 |
| 6 | Square | Medium | Heavy | Green | Shelf 3 |
| 7 | Rectangle | Small | Heavy | Red | Shelf 4 |
| 8 | Triangle | Large | Light | Blue | Shelf 2 |
| 9 | Circle | Small | Heavy | Green | Shelf 3 |
| 10 | Square | Medium | Light | Red | Shelf 1 |

All ten records form the training set. There is no separate labeled test set in this question, so construction of a fitting tree does not establish unseen-data accuracy.

### Step 1: Parent Entropy

The class counts are Shelf 1 = 2, Shelf 2 = 3, Shelf 3 = 3, Shelf 4 = 2:

$$
H(D)=-2(0.2\log_2 0.2)-2(0.3\log_2 0.3)=1.970951.
$$

### Step 2: Evaluate All Candidate Attributes

| Attribute | Child groups: counts by class | Weighted child entropy | Gain |
|---|---|---:|---:|
| Shape | Square: S1=2,S3=1; Circle: S2=2,S3=1; Rectangle: S3=1,S4=1; Triangle: S2=1,S4=1 | 0.950978 | 1.019973 |
| Size | Small: S1=1,S2=1,S3=2,S4=1; Medium: S1=1,S2=1,S3=1; Large: S2=1,S4=1 | 1.636453 | 0.334498 |
| Weight | Light: S1=2,S2=2,S3=1; Heavy: S2=1,S3=2,S4=2 | 1.521928 | 0.449022 |
| Color | Red: S1=2,S4=2; Blue: S2=3; Green: S3=3 | 0.400000 | 1.570951 |

Here S1-S4 abbreviate Shelf 1-Shelf 4 only inside the count table. For example,

$$
H(D\mid\text{Shape})=\frac3{10}H(2/3,1/3)
+\frac3{10}H(2/3,1/3)+\frac2{10}(1)+\frac2{10}(1).
$$

Color gives the largest information gain and becomes the root.

### Step 3: Complete Each Branch

Blue contains three Shelf 2 records: make a Shelf 2 leaf. Green contains three Shelf 3 records: make a Shelf 3 leaf.

Red contains four records: two Shelf 1 and two Shelf 4. Its entropy is 1. Weight separates Light into Shelf 1 and Heavy into Shelf 4, producing entropy 0 and gain 1. Shape also gives gain 1 on Red; choose Weight as the declared tie decision because it produces two branches.

```text
Color
  Blue  -> Shelf 2
  Green -> Shelf 3
  Red   -> Weight
             Light -> Shelf 1
             Heavy -> Shelf 4
```

All leaves are pure, so induction terminates. For the query, Color = Blue immediately reaches **Shelf 2**. Shape, Size, and Weight are not examined on this path.

The parent-entropy calculation also answers 2025 Q4(c). The reason Color is best is its maximum gain among all evaluated attributes, not simply that its name appears in the final tree.

## 6. Complete Decision Tree Example: Buys Computer

Use the exact eight-row training table printed in 2026 Q4(a):

| ID | Age | Income | Student | Credit rating | Buys computer |
|---|---|---|---|---|---|
| 1 | Senior | High | Yes | Fair | Yes |
| 2 | Youth | Medium | No | Excellent | Yes |
| 3 | Middle | High | No | Excellent | No |
| 4 | Senior | Low | No | Fair | Yes |
| 5 | Youth | Low | Yes | Fair | No |
| 6 | Senior | Medium | Yes | Excellent | Yes |
| 7 | Middle | Low | No | Excellent | No |
| 8 | Youth | High | Yes | Fair | No |

There are four Yes and four No labels, so $H(D)=1$.

| Attribute | Class counts in children (Yes, No) | Weighted child entropy | Gain |
|---|---|---:|---:|
| Age | Senior $(3,0)$; Youth $(1,2)$; Middle $(0,2)$ | $\frac38(0)+\frac38(0.918296)+\frac28(0)=0.344361$ | 0.655639 |
| Income | High $(1,2)$; Medium $(2,0)$; Low $(1,2)$ | $\frac38(0.918296)+\frac28(0)+\frac38(0.918296)=0.688722$ | 0.311278 |
| Student | Yes $(2,2)$; No $(2,2)$ | 1.000000 | 0 |
| Credit rating | Fair $(2,2)$; Excellent $(2,2)$ | 1.000000 | 0 |

Age has maximum gain. Senior becomes Yes and Middle becomes No. Youth contains IDs 2, 5, and 8. On that subset, Student = No contains the single Yes record; Student = Yes contains both No records. Its gain is 0.918296, making both children pure. Income and Credit rating also perfectly split this subset; choose Student as the stated tie rule.

```text
Age
  Senior -> Yes
  Middle -> No
  Youth  -> Student
              No  -> Yes
              Yes -> No
```

The tree fits all eight training records. This is a construction result, not an independent accuracy estimate. Attribute usefulness is conditional: Student has zero gain at the root but is useful after restricting the data to Youth.

## 7. Stopping, Interpretation, and Limitations

Stop expanding a node when it is pure, when available predictors cannot distinguish its records, or when an early stopping condition is met. Possible early controls include a depth limit or a minimum number of records required for a split. These prevent excessively detailed fitting of noise; a node stopped while mixed predicts its majority class.

Small trees are interpretable, and prediction is fast because only one path is followed. A path with $h$ tests takes $O(h)$ test evaluations. Construction cost depends on candidate tests, sorting, and tree shape; efficient counting is much cheaper than independently rescanning every candidate threshold.

Decision trees can overfit, may change with small changes in training data, and can prefer certain split structures. Greedy selection does not guarantee the globally smallest or most accurate tree. A large tree can be hard to interpret despite the simplicity of each test.

## 8. Bayesian Classification

A classifier can compare the probabilities of possible classes. **Bayes' theorem** relates a prior class probability, the likelihood of observed features, and the posterior class probability:

$$
P(C_j\mid x)=\frac{P(x\mid C_j)P(C_j)}{P(x)}.
$$

$P(C_j)$ is the prior, $P(x\mid C_j)$ the likelihood, and $P(C_j\mid x)$ the posterior. For choosing a class for the same $x$, the evidence $P(x)$ is common to all classes, so maximize the numerator. Posterior probabilities are obtained by normalizing across the classes.

### Naive Bayes: Algorithm and Assumption

**Naive Bayes** assumes features are conditionally independent **given the class**:

$$
P(x\mid C_j)=\prod_{f=1}^{d}P(x_f\mid C_j).
$$

It does not assume that all features are independent without conditioning. The approximation is useful because estimating one joint distribution over many features requires far more observations than estimating separate class-conditional feature distributions.

1. From labeled training data, estimate the class priors.
2. For each class and feature, estimate the conditional distribution.
3. For categorical values, use frequencies, with smoothing when appropriate.
4. For a new record, calculate each class's prior multiplied by its feature likelihoods.
5. Select the maximum score; normalize scores if posterior estimates are required.

Maintain class counts and class-feature counts, or numerical distribution parameters. Training ends after parameter estimation; prediction ends after all class scores have been compared. For categorical inputs, basic counting takes $O(nd)$ work and one prediction takes $O(cd)$ for $c$ classes. Correlated features can double-count evidence, and smoothing does not fix a wrong independence assumption.

### Complete Categorical Example

Suppose eight training baskets have features Bread and Milk and target HighValue:

| Class | Number of training baskets | Bread=Yes count | Milk=Yes count |
|---|---:|---:|---:|
| High | 4 | 3 | 3 |
| Low | 4 | 1 | 2 |

For a new Bread=Yes,Milk=Yes basket, use unsmoothed counts in this nonzero example. Scores are

$$
s_H=\frac48\frac34\frac34=\frac9{32},\qquad
s_L=\frac48\frac14\frac24=\frac2{32}.
$$

Normalize: $P(H\mid x)=9/11$ and $P(L\mid x)=2/11$. Predict High. These are model-based estimates from training counts; an independent test set is still needed to measure accuracy.

For a categorical feature with $v$ possible values, **Laplace smoothing** uses

$$
P(X_f=a\mid C_j)=\frac{N_{jfa}+\alpha}{N_j+\alpha v},\qquad\alpha>0.
$$

With four High records and an unobserved binary value, $\alpha=1$ changes zero to $1/(4+2)=1/6$. Use the same vocabulary and smoothing convention for all classes. Unseen categories need an explicitly defined representation.

For a numeric attribute, **Gaussian naive Bayes** estimates a class mean $\mu_{jf}$ and variance $\sigma^2_{jf}$ and evaluates

$$
p(x_f\mid C_j)=\frac1{\sqrt{2\pi\sigma^2_{jf}}}
\exp\left[-\frac{(x_f-\mu_{jf})^2}{2\sigma^2_{jf}}\right].
$$

This is a density, not a discrete probability at exactly one real value. A zero variance needs regularization or a separate convention. In implementation, add log likelihoods to avoid multiplying many tiny numbers.

### Bayesian Belief Networks

A **Bayesian belief network** represents conditional dependencies by a directed acyclic graph. Each node stores a distribution conditioned on its parents:

$$
P(X_1,\ldots,X_d)=\prod_fP(X_f\mid\operatorname{Parents}(X_f)).
$$

For Rain→WetRoad and Rain→Umbrella, the factorization is $P(R)P(W\mid R)P(U\mid R)$. The graph states that WetRoad and Umbrella are independent conditional on Rain, not necessarily independent marginally. Naive Bayes is a special structure with Class as the parent of each feature.

To classify, enter observed feature values, combine the factors, sum over hidden variables, and compare target posteriors. With a known graph and fully observed data, conditional tables can be estimated by counts. Hidden values can require iterative estimation, and learning the graph is a separate search problem. More expressive dependency structures need more parameters and can make inference expensive.

## 9. Rule-Based and Frequent-Pattern Classification

A class rule has the form IF condition THEN class. A record **covers** the rule's antecedent when it meets all its conditions. An ordered rule list uses the first matching rule; an unordered rule set needs a conflict-resolution policy. A default class handles records matching no rule.

For the earlier Object Table tree, one rule is Color=Red ∧ Weight=Light → Shelf 1. Its training coverage is 2/10 and its conditional accuracy on covered records is 2/2. This coverage differs from the fraction correctly predicted among all records. An empty antecedent default rule covers every remaining case.

### Sequential Covering

A basic separate-and-conquer rule learner operates as follows:

1. Select a target class and initialize the uncovered training examples.
2. Grow a rule by adding conditions that improve a declared quality measure.
3. Stop growing when the rule is sufficiently accurate or no useful refinement remains.
4. Prune conditions using validation evidence to reduce overfitting.
5. Add the rule and remove the target-class examples it correctly covers, under the learner's declared covering policy.
6. Repeat while enough uncovered positives remain; then add a default prediction.

Different algorithms use different quality measures and removal policies, so these details must be specified for a numerical trace. On the Object Table, Color=Blue → Shelf 2 covers IDs 2,5,8; Color=Green → Shelf 3 covers 3,6,9; Red/Light → Shelf 1 covers 1,10; Red/Heavy → Shelf 4 covers 4,7. These four rules account for every record and terminate. They fit training examples; their validation quality must be assessed separately.

### Classification by Association

**Class association rules** restrict the consequent of an association rule to a designated class. Mine frequent condition-class combinations, test support and confidence, rank or combine the surviving rules, and supply a default class.

For ten customers, suppose four satisfy Bread=Yes and three of those are HighValue. Bread=Yes → HighValue has class-rule support $3/10$ and confidence $3/4$. If Milk=Yes → LowValue also matches a query, the classifier needs an explicit precedence or voting scheme. Merely generating both rules does not define a classifier.

A typical CBA-style workflow mines class rules, orders them by decreasing confidence then support with a stable tie policy, builds a decision list while accounting for covered training cases, and chooses a useful prefix/default by observed training or validation error. Related methods can combine several matching rules; their voting details are not interchangeable with a decision list.

**Classification using frequent patterns** can also construct features rather than direct class rules. Encode $z_P(x)=1$ when pattern $P$ occurs in record $x$, and train a classifier on the resulting pattern indicators. For pattern {Bread,Milk}, a basket containing both has $z_P=1$, even if it contains additional items. Mine and select these patterns inside the training partition to avoid leaking test-label information. Too many correlated pattern features can increase cost and overfitting.

## 10. Neural Classification by Backpropagation

A feedforward network maps an input vector through weighted layers. For a hidden unit $j$,

$$
a_j=\sum_iw_{ji}x_i+b_j,\qquad h_j=\sigma(a_j),
\qquad\sigma(a)=\frac1{1+e^{-a}}.
$$

An output unit similarly combines hidden activations. A sigmoid output supports a binary class score; multiple class outputs can use a softmax. Weights and biases are learned from labeled examples. A hidden layer creates features that need not be simple rules in the original variables.

**Backpropagation** calculates the loss gradient by applying the chain rule backward through the network; an optimization rule then changes the parameters. For sigmoid units and squared loss $E=\frac12\sum_k(t_k-o_k)^2$, a convenient negative-gradient error signal is

$$
\delta_k=(t_k-o_k)o_k(1-o_k),
\qquad
\delta_j=h_j(1-h_j)\sum_kw_{kj}\delta_k.
$$

Update a weight into a unit with $w\leftarrow w+\eta\delta\times\text{input}$ and its bias with $b\leftarrow b+\eta\delta$. Here $\eta>0$ is the learning rate. With this sign convention, the plus update descends the squared loss; other loss functions require their corresponding derivatives.

### Training Procedure

1. Choose input features, hidden topology, activation, output encoding, loss, and learning rate.
2. Initialize weights to small nonidentical values and biases according to a declared policy.
3. Forward-propagate a training example to compute every activation.
4. Compare the output with its target and calculate output error signals.
5. Propagate signals backward using the **pre-update downstream weights**.
6. Update weights and biases, either per example or using accumulated batch gradients.
7. Repeat epochs until a validation stopping criterion, convergence tolerance, or iteration limit is reached.

Maintain weights, biases, activations, and gradients. Each epoch costs roughly $O(nW)$ for $W$ network connections in a basic dense implementation. Gradient descent can be sensitive to scaling, learning rate, initialization, local structure, and overfitting. Training accuracy alone does not justify a network's use.

### One Complete Forward/Backward Update

Use one input $x=1$, one sigmoid hidden unit, one sigmoid output, target $t=1$, weights $w_1=w_2=0$, biases $b_h=b_o=0$, and learning rate 1. The example uses zero weights only to make the arithmetic transparent; general multiunit networks need symmetry-breaking initialization.

Forward pass: $a_h=0$, $h=0.5$, $a_o=0$, $o=0.5$, and $E=0.125$.

Backward pass: $\delta_o=(1-0.5)(0.5)(0.5)=0.125$. Using old $w_2=0$, $\delta_h=0.5(0.5)(0)(0.125)=0$.

Updates: $w_2'=0+1(0.125)(0.5)=0.0625$, $b_o'=0.125$, while $w_1'$ and $b_h'$ remain 0. The new hidden activation remains 0.5; output preactivation becomes $0.0625(0.5)+0.125=0.15625$. New output is approximately 0.538983 and loss approximately 0.106268, lower than 0.125. Later examples and epochs continue the learning process; one update is not a trained classifier.

## 11. Numeric Prediction and Regression

Numeric prediction uses a continuous-valued target rather than a category. **Simple linear regression** fits

$$
\hat y=b_0+b_1x,
\quad b_1=\frac{\sum_i(x_i-\bar x)(y_i-\bar y)}{\sum_i(x_i-\bar x)^2},
\quad b_0=\bar y-b_1\bar x.
$$

These coefficients minimize training squared residuals when the predictor has nonzero spread. A residual is $e_i=y_i-\hat y_i$. The model approximates conditional behavior; the existence of a fitted line does not prove a causal effect.

For training pairs $(1,2),(2,3),(3,5)$, $\bar x=2$, $\bar y=10/3$, numerator is 3, denominator 2, slope is 1.5, and intercept is $1/3$. Predictions are $11/6,10/3,29/6$, with residuals $1/6,-1/3,1/6$ and training SSE $1/6$. At $x=4$, the prediction is $19/3\approx6.333333$, which extrapolates beyond the observed range.

Multiple linear regression uses $\hat y=b_0+\sum_fb_fx_f$. Polynomial regression adds powers or interactions of predictors but can remain linear in its coefficients. Logarithmic transformations or nonlinear models can represent other relationships. More flexible forms need enough data and appropriate validation; extrapolation can be especially unreliable.

For held-out targets and predictions, define

$$
MAE=\frac1n\sum_i|y_i-\hat y_i|,\qquad
MSE=\frac1n\sum_i(y_i-\hat y_i)^2,\qquad RMSE=\sqrt{MSE}.
$$

On independent test targets $[10,20]$ and predictions $[12,17]$, absolute errors are 2,3: MAE = 2.5, MSE = 6.5, and RMSE = 2.549510. RMSE emphasizes large errors more strongly. Percentage errors require care when actual values are zero or near zero. Classification accuracy is not a regression metric.

## 12. Improving Models, Multiclass Learning, and Unlabeled Data

Improve accuracy by fixing data quality, selecting meaningful features, tuning model settings using validation, regularizing or pruning, addressing imbalance appropriately, and considering ensembles. A technique can improve one dataset while harming another; compare under a fixed protocol.

### Pruning and Ensemble Methods

**Pre-pruning** stops tree growth early; **post-pruning** grows a tree and replaces subtrees with leaves when the replacement improves an estimate of generalization or an error/complexity criterion. A subtree with lower training error can still be pruned if its extra detail fits noise. Validation records used to choose pruning must not later be represented as an untouched final test set.

**Bagging** draws bootstrap training samples, fits one model per sample, and combines predictions by majority vote or numerical averaging. Bootstrap samples contain replacement duplicates. For three class predictions Yes,No,Yes, the combined vote is Yes. Bagging can reduce variance, especially for unstable learners; heavily correlated model errors reduce the benefit.

**Random forests** add random feature-subset selection at tree splits to the bootstrap/ensemble idea, encouraging diversity among trees. They combine many trees' predictions while reducing dependence on one unstable tree.

**Boosting** trains learners sequentially so that later learners emphasize cases poorly handled by earlier ones. In binary AdaBoost with labels $y_i\in\{-1,+1\}$, normalized sample weights $D_i$, and classifier $h_t$:

$$
\epsilon_t=\sum_iD_i\mathbf1[h_t(x_i)\ne y_i],
\quad\alpha_t=\frac12\ln\frac{1-\epsilon_t}{\epsilon_t},
$$

$$
D_i'\propto D_i\exp[-\alpha_ty_ih_t(x_i)].
$$

Initialize $D_i=1/n$, train a weighted learner, compute its error, update and normalize the weights, and repeat for a fixed number of rounds or a stopping criterion. Predict the sign of $\sum_t\alpha_th_t(x)$. Require a better-than-chance learner under these binary formulas; error 0 or at least 0.5 needs explicit stopping or handling.

With four equal-weight records and one mistake, $\epsilon=0.25$ and $\alpha=\frac12\ln3$. After reweighting, the mistaken record has normalized weight 0.5 and each correct record has weight $1/6$. The next learner therefore pays more attention to that error. Boosting can fit mislabeled outliers aggressively; it does not guarantee better accuracy.

### Multiclass Classification

Multiclass learning assigns one of more than two mutually exclusive labels. Decision trees and naive Bayes naturally extend their class counts or scores. Binary learners can be combined:

- **One versus rest:** train $c$ classifiers, each comparing one class with all others; choose the largest suitably comparable class score.
- **One versus one:** train $c(c-1)/2$ pairwise classifiers and combine their votes or scores using a declared tie rule.

For three classes A,B,C, one-versus-one trains A/B,A/C,B/C. If A beats B, C beats A, and C beats B, C receives two votes and wins. Multilabel learning is different: one record may legitimately receive several labels.

A multiclass confusion matrix has one row per actual class and one column per predicted class. Compute per-class precision/recall by treating that class as Positive. Macro averaging gives each class equal weight; micro averaging pools the decision counts. For per-class recalls 0.9,0.5,0.1, macro recall is 0.5 even if one class is much larger.

### Semi-Supervised Classification

Semi-supervised learning uses a small labeled set $L$ and an additional unlabeled set $U$. It requires assumptions relating unlabeled structure to labels; unlabeled data do not supply ground-truth labels automatically.

A **self-training** procedure fits a classifier on $L$, predicts $U$, accepts only pseudo-labels meeting a confidence policy, adds them to the training set, and repeats until no eligible records remain or a round limit is reached. If two unlabeled records receive estimated probabilities 0.96 and 0.60 for a class with threshold 0.9, only the first is added. The second remains unlabeled. Refit and reconsider it in later rounds. Confident mistakes can reinforce themselves, so independent labeled validation is essential.

**Label propagation** constructs a similarity graph, fixes known labels, and repeatedly distributes class information along edges until stable. It assumes that sufficiently connected nearby observations tend to share labels. If a nearest-neighbor chain has A-labeled points at one end and B-labeled points at the other, unlabeled middle points receive competing information; graph weights determine the boundary. A graph connecting unlike populations can spread wrong labels.

These methods connect prediction to the similarity and cluster assumptions in Chapter 6. External unlabeled test records should not be included during fitting unless the evaluation is explicitly transductive and reports that protocol.

## 13. Confusion Matrices and Evaluation Measures

Declare the **positive class** before calculating metrics. For a fraud classifier, suppose Positive means Fraud.

| Actual class | Predicted Positive | Predicted Negative |
|---|---:|---:|
| Positive | $a=TP$ | $b=FN$ |
| Negative | $c=FP$ | $d=TN$ |

A true positive is a positive case correctly predicted positive. A false negative is a missed positive. A false positive is a negative case incorrectly flagged positive. A true negative is a negative case correctly predicted negative.

| Measure | Formula | Interpretation |
|---|---|---|
| Accuracy | $(TP+TN)/(TP+TN+FP+FN)$ | Fraction of all predictions that are correct |
| Error rate | $(FP+FN)/N$ | Fraction of all predictions that are wrong |
| Precision | $TP/(TP+FP)$ | Fraction of positive predictions that are truly positive |
| Recall, sensitivity, or TPR | $TP/(TP+FN)$ | Fraction of actual positives detected |
| Specificity, or TNR | $TN/(TN+FP)$ | Fraction of actual negatives rejected correctly |
| False positive rate, FPR | $FP/(FP+TN)$ | Fraction of actual negatives incorrectly flagged |
| F1 score | $2TP/(2TP+FP+FN)$ | Harmonic combination of precision and recall |

$FPR=1-\text{specificity}$. Precision conditions on **predictions**; recall conditions on the **actual positive class**. When a denominator is zero, the metric needs an explicitly declared convention rather than an unexplained division.

### Worked Evaluation Example

Suppose a classifier is trained on a separate labeled training set and evaluated on 100 untouched test records:

| Actual class | Predicted Fraud | Predicted Legitimate |
|---|---:|---:|
| Fraud | 30 | 10 |
| Legitimate | 5 | 55 |

Thus $TP=30$, $FN=10$, $FP=5$, $TN=55$:

$$
\text{Accuracy}=85/100=85\%,\qquad
\text{Precision}=30/35=85.71\%,
$$

$$
\text{Recall}=30/40=75\%,\qquad
\text{Specificity}=55/60=91.67\%,
$$

$$
FPR=5/60=8.33\%,\qquad F_1=60/75=80\%.
$$

The model misses 25% of fraud cases even though its overall accuracy is 85%. A false negative and a false positive may have very different business costs.

### Imbalance and Weighted Accuracy

If 9,990 of 10,000 records are negative, always predicting Negative gives 99.9% accuracy while detecting zero positive cases. Accuracy alone hides this failure.

The course also gives a weighted accuracy, assigning nonnegative weights to the four cells:

$$
\operatorname{WeightedAccuracy}
=\frac{w_1a+w_4d}{w_1a+w_2b+w_3c+w_4d}.
$$

Its interpretation depends on the supplied weights. Equal weights give ordinary accuracy. It is not automatically balanced accuracy or a universal cost metric. With the preceding confusion matrix, $w_1=w_2=2$ and $w_3=w_4=1$ give

$$
\frac{2(30)+55}{2(30)+2(10)+5+55}=\frac{115}{140}=82.14\%.
$$

## 14. Estimating Generalization Performance

| Method | Procedure | Main consideration |
|---|---|---|
| Holdout | Reserve separate training and test portions; the course uses about two-thirds and one-third as an example | One split can have substantial sampling variability |
| Random subsampling | Repeat random holdout splits and summarize performance | A record can appear in several test sets across repetitions |
| K-fold cross-validation | Divide records into $k$ disjoint folds; train on $k-1$, evaluate on the remaining fold, and rotate | Each record is tested once across the folds |
| Leave-one-out | Use $k=n$; train on all but one record at a time | Many fitting runs; a special case of cross-validation |
| Stratified splitting | Preserve approximately the class proportions in each partition | Helps prevent small classes from disappearing from a fold |
| Bootstrap | Draw training samples with replacement and evaluate on suitable held-out or out-of-bag data | A bootstrap training sample contains duplicates and omits some original records |

For 90 records in three-fold cross-validation, each round trains on 60 and evaluates on 30. If the held-out correct counts are 24, 27, and 25, pooled accuracy is $(24+27+25)/90=84.44\%$. No record is used to train the model that evaluates it in that round.

Stratification and class rebalancing are distinct. Oversampling increases minority-class training observations; undersampling reduces majority-class training observations. Apply these operations inside the training partition, keeping the evaluation protocol representative of the target problem. Fit normalization and choose $K$ inside the training/validation workflow as well.

Performance estimates depend on class distribution, error costs, and train/test size as well as the learning algorithm. Compare models using the same evaluation conditions and a metric suited to the task.

## 15. ROC Curves and AUC

A score-based classifier predicts Positive when its score is at least a threshold $t$. Lowering $t$ normally labels more records positive, increasing both detected positives and false alarms.

A **receiver operating characteristic (ROC) curve** plots **FPR on the horizontal axis** and **TPR on the vertical axis** as the threshold varies. Use coordinate order $(FPR,TPR)$ consistently.

- $(0,0)$ predicts everything Negative.
- $(1,1)$ predicts everything Positive.
- $(0,1)$ is ideal: no false positives and all positives detected.
- The diagonal corresponds to chance-level ranking in expectation.
- A curve below the diagonal indicates systematically poor ranking; it does not imply every individual prediction is opposite to truth.

The **area under the curve (AUC)** summarizes ranking discrimination. A perfect ranking has AUC 1; chance-level ranking has expected AUC 0.5. Crossing ROC curves mean one model can be preferable at low FPR while another is preferable at higher FPR. A single AUC does not determine the appropriate operating threshold.

### Constructing the Course ROC Example Correctly

Treat these ten scored observations as held-out test data from a previously trained model:

| Score | 0.95 | 0.93 | 0.87 | 0.85 | 0.85 | 0.85 | 0.76 | 0.53 | 0.43 | 0.25 |
|---|---|---|---|---|---|---|---|---|---|---|
| Actual class | + | - | + | - | - | - | + | - | + | + |

There are five positives and five negatives. Start above the largest score, then lower the threshold through each distinct score. **All records with equal scores cross together.**

| Threshold, predict + when score $\ge t$ | TP | FP | TN | FN | FPR | TPR |
|---|---:|---:|---:|---:|---:|---:|
| 1.00 | 0 | 0 | 5 | 5 | 0.0 | 0.0 |
| 0.95 | 1 | 0 | 5 | 4 | 0.0 | 0.2 |
| 0.93 | 1 | 1 | 4 | 4 | 0.2 | 0.2 |
| 0.87 | 2 | 1 | 4 | 3 | 0.2 | 0.4 |
| 0.85 | 2 | 4 | 1 | 3 | 0.8 | 0.4 |
| 0.76 | 3 | 4 | 1 | 2 | 0.8 | 0.6 |
| 0.53 | 3 | 5 | 0 | 2 | 1.0 | 0.6 |
| 0.43 | 4 | 5 | 0 | 1 | 1.0 | 0.8 |
| 0.25 | 5 | 5 | 0 | 0 | 1.0 | 1.0 |

At threshold 0.85, the selected records are the first six: two positives and four negatives. Thus the point is $(0.8,0.4)$. The three tied negative scores cannot generate three different confusion matrices at the same threshold under a fixed score rule.

Using trapezoids between these points gives AUC $0.2(0.2)+0.6(0.4)+0.2(0.6)=0.40$. The table and the resulting AUC follow from the supplied scores and labels, independently of the plotted curve in the PDF.

## 16. Sources and Lecture Corrections

Primary source: `sources/5_Classification.pdf`; additional classifier families and regression use the shared textbook, Chapter 6. Worked trees use the actual 2025 and 2026 paper tables.

- PDF page 46: counts (5,2) and (1,4) give weighted Gini 0.371429 and gain 0.128571; the printed 0.333 is inconsistent.
- PDF page 51: the second entropy term for counts (1,5) is $-(5/6)\log_2(5/6)$; entropy is 0.650022.
- PDF pages 71–73: use ROC coordinates (FPR,TPR), apply score≥threshold, and move tied scores together. The corrected score table gives AUC 0.40.

Further definitions: official references for [tree criteria](https://scikit-learn.org/stable/modules/tree.html#classification-criteria), [ROC](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_curve.html), [multiclass strategies](https://scikit-learn.org/stable/modules/multiclass.html), [self-training](https://scikit-learn.org/stable/modules/generated/sklearn.semi_supervised.SelfTrainingClassifier.html), and [label propagation](https://scikit-learn.org/stable/modules/semi_supervised.html).
