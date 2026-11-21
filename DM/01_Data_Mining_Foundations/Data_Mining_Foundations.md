# Chapter 1. Data Mining Foundations

The course follows one question: how can recorded observations become reliable knowledge for decisions? A retailer records bills, customers, products, branches, and dates. Cleaning makes those records usable; a warehouse organizes their history; mining discovers relationships; evaluation decides whether the relationships deserve action.

## 1. Definition and scope

Database systems store data and retrieve known facts. Data mining identifies patterns, relationships, and predictive regularities that are not explicitly stored in the database. For example, a database query may determine the number of customers who purchased a laptop in a given month, whereas a mining method may identify customers likely to purchase a laptop in a future period.

**Data mining is the process of discovering valid, previously unknown, useful, and understandable patterns from large datasets.**

A result is valuable only when it is:

- **Valid:** it has evidence of reliability beyond an accidental fit to the observed sample.
- **Novel:** it adds knowledge relative to what the analyst already knows.
- **Useful:** it can support a decision or prediction.
- **Understandable:** a human can interpret or communicate it.

## 2. Data mining and KDD are not identical

**Knowledge Discovery in Databases (KDD)** is the complete journey from raw data to usable knowledge. **Data mining** is the central algorithmic step in that journey.

| Stage | Purpose | Supermarket example |
|---|---|---|
| 1. Cleaning | Correct errors and missing values | Remove duplicate bills and invalid codes |
| 2. Integration | Combine sources | Join billing, product, and customer records |
| 3. Selection | Retain task-relevant data | Select recent completed transactions |
| 4. Transformation | Prepare the representation | Convert each bill into a set of items |
| 5. Data mining | Discover patterns or fit models | Find frequently co-purchased items |
| 6. Pattern evaluation | Assess validity and usefulness | Reject weak or obvious rules |
| 7. Knowledge presentation | Communicate results | Present actionable recommendations |

KDD begins by understanding the application domain and ends by using the discovered knowledge in a real task. It is iterative: evaluation can require different data, transformations, or mining methods.

## 3. Why data mining is necessary

Modern systems collect data faster than humans can inspect it. Merely storing this data does not automatically convert it into knowledge.

Data mining is useful because:

- ordinary queries require the user to know what to ask;
- hidden relationships may involve thousands of attributes or millions of records;
- predictions and anomaly detection require generalization beyond stored facts;
- businesses can use discovered patterns for recommendation, retention, fraud detection, and planning.

This situation is commonly described as being **data rich but information poor**.

## 4. Choosing the correct mining task

Choose the mining task from the required output.

| If the required output is… | Use… | Example |
|---|---|---|
| A known category | Classification | Fraudulent or genuine transaction |
| A numerical value | Regression | Expected sales next month |
| Natural groups without labels | Clustering | Customer segments |
| Items that occur together | Association mining | Bread and butter purchases |
| Events occurring in order | Sequential-pattern mining | Laptop, then camera, then memory card |
| An unusual observation | Outlier analysis | Abnormally large card payment |
| A graphical view of structure or change | Data visualization | A plot revealing customer groups or changing sales |

### Classification versus clustering

Classification begins with predefined labels and learns how to assign them. Clustering has no predefined labels; the groups are discovered from similarity.

### Classification versus regression

Both are predictive, but classification predicts a discrete label whereas regression predicts a numerical value.

## 5. Descriptive and predictive mining

**Descriptive mining** explains what is present in the data. Association rules, clustering, characterization, and discrimination are mainly descriptive.

**Predictive mining** uses known data to estimate an unknown outcome. Classification and regression are predictive.

### Characterization versus discrimination

- **Characterization** summarizes one target class. Example: describe the typical premium customer.
- **Discrimination** compares the target class with a contrasting class. Example: compare premium customers with occasional customers.

**Characterization** can be produced by grouping a selected class, generalizing attributes, and calculating summaries. Suppose premium customers are generalized from exact cities to regions and exact ages to bands: a report might show that 60% are in the East region and their median monthly spending is 4,000. **Discrimination** runs compatible summaries for a contrasting group and compares proportions or measures. A difference is descriptive evidence; it does not alone demonstrate causation.

Characterization can use **statistical measures and plots**, **OLAP roll-up**, or **attribute-oriented induction**. Attribute-oriented induction retrieves the target records, removes irrelevant detail, generalizes values through concept hierarchies, and merges identical generalized descriptions with counts. Generalizing city to region and age to age band produces a smaller descriptive summary. Discrimination applies compatible summaries to the target and contrasting classes.

## 6. Frequent patterns and outliers

A pattern may be frequent in several forms:

- A **frequent itemset** contains items that often appear together.
- A **sequential pattern** contains events that often appear in the same order.
- A **frequent substructure** is a recurring graph, tree, or other structure.

An **outlier** is an observation that does not follow the general behavior of the data. An outlier is not automatically an error. In fraud detection, rare observations may constitute the primary objects of interest.

## 7. Data that can be mined

Data mining is not limited to ordinary tables. It can be applied to:

- relational databases and data warehouses;
- transaction databases;
- streams and time-ordered sequences;
- graphs and networks;
- spatial data;
- text, multimedia, and Web data.

Different data forms need different representations. A transaction is naturally a set of items, while a social network is naturally a graph.

## 8. Components of a data-mining system

1. The **database or warehouse server** retrieves the required data.
2. The **knowledge base** supplies domain knowledge and user constraints.
3. The **mining engine** runs algorithms for association, classification, clustering, and other tasks.
4. The **pattern-evaluation module** measures whether the discovered patterns are interesting.
5. The **user interface** allows the user to define the task and understand the result.

## 9. Major issues in data mining

Major issues concern methodology, user interaction, scalability, data diversity, and social impact.

### Mining methodology

The first challenge is deciding **what knowledge to discover and how to discover it**.

- Real datasets contain noise, missing values, and uncertainty.
- A useful pattern may involve many attributes simultaneously.
- Different problems require ideas from databases, statistics, machine learning, and visualization.
- A system may discover millions of patterns, so it needs constraints and interestingness measures to retain the useful ones.

**Example:** a retailer may want rules involving products from different categories but may wish to exclude obvious rules involving different packet sizes of the same product.

### User interaction

The system must let users guide the search and understand the answer.

- Users should be able to ask ad hoc mining questions.
- Domain knowledge should influence the search.
- Results should be presented as understandable rules, plots, or summaries.

**Example:** a doctor may restrict a mining task to medically meaningful attributes and require an interpretable result rather than an unexplained score.

### Efficiency and scalability

An algorithm that works on one thousand records may fail on one billion records. It must scale with:

- the number of records;
- the number of attributes;
- the rate of incoming data;
- the number of candidate patterns.

Large problems may require parallel, distributed, or incremental algorithms.

### Diversity of data

Real repositories may be heterogeneous, dynamic, distributed, or networked. Text, images, graphs, streams, and relational tables cannot all be processed in exactly the same way.

**Example:** a friendship network requires graph relationships, while a sales table uses rows and columns.

### Data mining and society

**Privacy-preserving mining** seeks useful aggregate patterns while limiting disclosure of sensitive records, for example through restricted access, aggregation, or privacy-aware computation. **Invisible data mining** embeds mining within everyday services, such as automatic recommendations, so users may not explicitly initiate the analysis. This makes transparency, consent, and the handling of personal information relevant.

Mining can reveal sensitive information or reinforce unfair historical patterns. Important concerns include privacy, consent, ownership, bias, security, and misuse.

**Example:** a useful credit-risk model may still be unacceptable if it indirectly discriminates against a protected group.

## 10. Data Mining, Machine Learning, and Statistics

The fields overlap, but their emphasis differs.

| Data mining | Machine learning |
|---|---|
| Focuses on discovering useful knowledge from large datasets | Focuses on learning models that generalize to new data |
| Includes selection, cleaning, pattern evaluation, and presentation | Emphasizes training, prediction, and model evaluation |
| May produce rules, summaries, clusters, or predictive models | Commonly produces a trained predictive or decision model |

Machine-learning algorithms can therefore be used **inside** the data-mining stage of KDD. Statistics supplies estimation and uncertainty analysis; pattern recognition supplies methods for identifying regularities. Database and warehouse systems organize large datasets, information retrieval and natural-language processing support text, visualization communicates patterns, and efficient algorithms and high-performance computing make large-scale mining practical.

## 11. How the Field Developed

Data mining grew from the convergence of several developments rather than one invention date. Database systems made large structured collections accessible. Statistics supplied estimation, uncertainty, and experimental reasoning. Machine learning supplied methods that learn patterns from examples. Warehousing integrated historical business data; information retrieval and visualization made complex results accessible. The expansion of digital transactions, scientific measurement, and networked services created the need to combine these capabilities.

KDD emerged as a name for the broader discovery process. Frequent-itemset and association-rule research in the 1990s established scalable transaction mining as a major database problem. Later work extended mining to streams, text, graphs, biological sequences, and high-dimensional data. The field has several roots rather than a single origin year.

## 12. Defining a Mining Task Precisely

A task specification should state five things: the relevant data, the kind of knowledge sought, background knowledge, interestingness criteria, and the desired presentation. These are often called **data-mining task primitives**.

For a retail recommendation task, specify recent completed baskets; association rules; the product-category hierarchy; minimum support and confidence; and a table of actionable cross-category rules. For churn classification, specify customers observed up to a reference date, a later churn label, available predictors, an error metric, and a reporting format. A label defined using the future must not leak into the input attributes.

Mining systems can be classified by their data sources, knowledge discovered, techniques used, and application domain. One system may simultaneously be warehouse-based, association-oriented, database-driven, and used in marketing. These are different classification axes rather than exclusive categories.

## 13. Evolution Analysis and Changing Data

**Evolution analysis** studies regularities and changes over time. A trend is sustained long-term movement; seasonality is recurrence tied to a calendar period; a sequential pattern describes repeated event order; a deviation indicates behavior that departs from the established model.

For the retailer, rising annual revenue is a trend, increased December sales are seasonal, buying a printer after a laptop is a sequence, and a sudden collapse in one branch's sales is a deviation. These require time-aware representations. A basket association ignores the order of items within one basket; sequential mining preserves order across events. A common sequence does not generally require immediate adjacency unless an explicit gap constraint requires it.

The distribution generating the data may also change. If a classification relationship changes, an old model may become unreliable even when its original test results were good. Evaluation and maintenance therefore continue after a model is deployed.

## 14. Probability, Evidence, and Interpretation

A probability lies between 0 and 1. Conditional probability measures an event within a specified subgroup:

$$
P(B\mid A)=\frac{P(A\cap B)}{P(A)},\qquad P(A)>0.
$$

If 20 of 25 bread baskets also contain milk, the empirical estimate of $P(\text{milk}\mid\text{bread})$ is 0.8. Association confidence uses this same conditional-frequency idea. Bayesian classification uses conditional probabilities to reverse the direction of inference. Classification precision and recall also condition on different subsets of a contingency table.

**Independence** means $P(A\cap B)=P(A)P(B)$. It does not mean two observed events never co-occur. **Correlation** describes a statistical relationship; **causation** asserts an effect of changing one factor on another. A rule suggesting a promotion should ideally be tested with an appropriate intervention, since seasonal demand or customer preferences may explain the observed co-occurrence.

A frequent pattern is not automatically statistically significant or useful. Searching many patterns increases the opportunity to find accidental regularities. Holdout evaluation, domain constraints, adequate sample sizes, and appropriate uncertainty analysis support stronger conclusions. There is no universal support count that proves validity for every dataset.

## 15. The Course as One Analytical Workflow

| Stage | Retail question | Representation or result |
|---|---|---|
| Define the task | Which decision needs improvement? | Target, inputs, constraints, evaluation criterion |
| Prepare data | Can branch records be compared? | Clean values, aligned units, meaningful features |
| Organize history | How did sales vary across time and region? | Warehouse facts, dimensions, and OLAP views |
| Discover patterns | Which products occur together? | Frequent itemsets and association rules |
| Predict outcomes | Which customers will buy or leave? | Labeled training data and a tested model |
| Discover groups and exceptions | Which customer groups exist, and which transactions are unusual? | Clusters and outlier scores |
| Adapt to the domain | How should reviews, sequences, and measurements be represented? | Text, biological, or time-series features and patterns |

The stages share data and ideas. A hierarchy used for warehouse roll-up also supports multilevel rules. Entropy first appears when discretizing an attribute and later selects tree splits. Similarity supports KNN, clustering, outlier detection, and text retrieval. Every numerical result depends on the population, representation, and evaluation protocol used to obtain it.

## 16. Sources

Primary source: `sources/1_DM_Intro.pdf`. Quality and descriptive statistics are developed in Chapter 2. Historical context, task specification, and evolution analysis also use *Data Mining: Concepts and Techniques*, second edition, Chapter 1.
