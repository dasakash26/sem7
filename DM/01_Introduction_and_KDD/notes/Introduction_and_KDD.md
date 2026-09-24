# Introduction to Data Mining and KDD

## 1. Definition and scope

Database systems store data and retrieve known facts. Data mining identifies patterns, relationships, and predictive regularities that are not explicitly stored in the database. For example, a database query may determine the number of customers who purchased a laptop in a given month, whereas a mining method may identify customers likely to purchase a laptop in a future period.

**Data mining is the process of discovering valid, previously unknown, useful, and understandable patterns from large datasets.**

A result is valuable only when it is:

- **Valid:** it is not a random accident in the data.
- **Novel:** it tells us something not already obvious.
- **Useful:** it can support a decision or prediction.
- **Understandable:** a human can interpret or communicate it.

## 2. Data mining and KDD are not identical

**Knowledge Discovery in Databases (KDD)** is the complete journey from raw data to usable knowledge. **Data mining** is the central algorithmic step in that journey.

```text
Raw data
   ↓
Cleaning → Integration → Selection → Transformation
   ↓
Data mining
   ↓
Pattern evaluation → Knowledge presentation
   ↓
Useful knowledge
```

### The seven KDD stages

1. **Cleaning:** correct errors, handle missing values, and reduce noise.
2. **Integration:** combine data from different sources.
3. **Selection:** retain the records and attributes relevant to the problem.
4. **Transformation:** normalize, aggregate, or otherwise prepare the selected data.
5. **Data mining:** apply an algorithm to discover patterns or build a model.
6. **Pattern evaluation:** reject trivial, invalid, or unhelpful results.
7. **Knowledge presentation:** communicate the useful result through rules, reports, or visualizations.

### Illustrative example

Suppose a supermarket wants to recommend products:

- Cleaning removes duplicate bills and invalid product codes.
- Integration joins billing data with product and customer tables.
- Selection keeps the recent transactions relevant to the campaign.
- Transformation converts each bill into a set of purchased items.
- Mining finds items frequently bought together.
- Evaluation removes weak or obvious rules.
- Presentation shows the final recommendations to the marketing team.

The example demonstrates that data mining is one stage of a larger knowledge-discovery process. The quality of discovered knowledge depends on the quality and suitability of the preceding data-preparation stages.

## 3. Why data mining is necessary

Modern systems collect data faster than humans can inspect it. Merely storing this data does not automatically convert it into knowledge.

Data mining is useful because:

- ordinary queries require the user to know what to ask;
- hidden relationships may involve thousands of attributes or millions of records;
- predictions and anomaly detection require generalization beyond stored facts;
- businesses can use discovered patterns for recommendation, retention, fraud detection, and planning.

This situation is commonly described as being **data rich but information poor**.

## 4. Choosing the correct mining task

Do not memorize the task names in isolation. Identify them from the required output.

| If the required output is… | Use… | Example |
|---|---|---|
| A known category | Classification | Fraudulent or genuine transaction |
| A numerical value | Regression | Expected sales next month |
| Natural groups without labels | Clustering | Customer segments |
| Items that occur together | Association mining | Bread and butter purchases |
| Events occurring in order | Sequential-pattern mining | Laptop, then camera, then memory card |
| An unusual observation | Outlier analysis | Abnormally large card payment |

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

The key distinction is **summary of one class** versus **comparison between classes**.

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

The components form a pipeline rather than an unrelated list:

1. The **database or warehouse server** retrieves the required data.
2. The **knowledge base** supplies domain knowledge and user constraints.
3. The **mining engine** runs algorithms for association, classification, clustering, and other tasks.
4. The **pattern-evaluation module** measures whether the discovered patterns are interesting.
5. The **user interface** allows the user to define the task and understand the result.

## 9. Major issues in data mining

In the context of data mining, **issues** denote the principal technical, computational, usability, and social challenges involved in discovering useful knowledge from data.

### 9.1 Mining methodology

The first challenge is deciding **what knowledge to discover and how to discover it**.

- Real datasets contain noise, missing values, and uncertainty.
- A useful pattern may involve many attributes simultaneously.
- Different problems require ideas from databases, statistics, machine learning, and visualization.
- A system may discover millions of patterns, so it needs constraints and interestingness measures to retain the useful ones.

**Example:** a retailer may want rules involving products from different categories but may wish to exclude obvious rules involving different packet sizes of the same product.

### 9.2 User interaction

The system must let users guide the search and understand the answer.

- Users should be able to ask ad hoc mining questions.
- Domain knowledge should influence the search.
- Results should be presented as understandable rules, plots, or summaries.

**Example:** a doctor may restrict a mining task to medically meaningful attributes and require an interpretable result rather than an unexplained score.

### 9.3 Efficiency and scalability

An algorithm that works on one thousand records may fail on one billion records. It must scale with:

- the number of records;
- the number of attributes;
- the rate of incoming data;
- the number of candidate patterns.

Large problems may require parallel, distributed, or incremental algorithms.

### 9.4 Diversity of data

Real repositories may be heterogeneous, dynamic, distributed, or networked. Text, images, graphs, streams, and relational tables cannot all be processed in exactly the same way.

**Example:** a friendship network requires graph relationships, while a sales table uses rows and columns.

### 9.5 Data mining and society

Mining can reveal sensitive information or reinforce unfair historical patterns. Important concerns include privacy, consent, ownership, bias, security, and misuse.

**Example:** a useful credit-risk model may still be unacceptable if it indirectly discriminates against a protected group.

### Examination note

A complete answer should define major issues as technical, computational, usability, and social challenges. It should then explain mining methodology, user interaction, efficiency and scalability, diversity of data, and social impact, supported by suitable examples.

## 10. Data quality

Mining quality depends on input quality.

| Dimension | Question to ask |
|---|---|
| Accuracy | Does the value correctly represent reality? |
| Completeness | Are the required records and values present? |
| Consistency | Do related values agree? |
| Timeliness | Is the data recent enough? |
| Believability | Is the source trustworthy? |
| Value added | Does the data improve the decision? |
| Interpretability | Are meaning, scale, and units clear? |
| Accessibility | Can authorized users obtain and use it? |

Real-world data is commonly:

- **incomplete**, because values were never recorded;
- **noisy**, because of measurement or entry error;
- **inconsistent**, because sources use conflicting codes, formats, or units.

Consequently, preprocessing is a required component of KDD rather than optional housekeeping.

## 11. Descriptive statistics

Statistics give a compact first description of a numerical attribute.

### Central tendency

For observations $x_1,x_2,\ldots,x_n$, the arithmetic mean is

$$
\bar{x}=\frac{1}{n}\sum_{i=1}^{n}x_i.
$$

With weights $w_i$, the weighted mean is

$$
\bar{x}_w=\frac{\sum_{i=1}^{n}w_i x_i}{\sum_{i=1}^{n}w_i}.
$$

- The **median** is the middle sorted value, or the mean of the two middle values.
- The **mode** is the most frequent value.
- The **midrange** is $\frac{x_{\min}+x_{\max}}{2}$.

For a moderately skewed distribution, the empirical relation is

$$
\text{mean}-\text{mode}=3(\text{mean}-\text{median}).
$$

### Interpreting skewness

- Symmetric distribution: mean $=$ median $=$ mode.
- Positive or right skew: mode $<$ median $<$ mean.
- Negative or left skew: mean $<$ median $<$ mode.

The mean is pulled toward the long tail because it is sensitive to extreme values.

### Variance and standard deviation

For a population of size $N$,

$$
\sigma^2=\frac{1}{N}\sum_{i=1}^{N}(x_i-\mu)^2,
\qquad
\sigma=\sqrt{\sigma^2}.
$$

For a sample of size $n$,

$$
s^2=\frac{1}{n-1}\sum_{i=1}^{n}(x_i-\bar{x})^2,
\qquad
s=\sqrt{s^2}.
$$

Variance measures squared spread; standard deviation expresses the spread in the original unit.

### Quartiles and boxplot outliers

The five-number summary is

$$
x_{\min},\ Q_1,\ \text{median},\ Q_3,\ x_{\max}.
$$

The interquartile range is

$$
\operatorname{IQR}=Q_3-Q_1.
$$

Potential outliers lie outside

$$
\left[Q_1-1.5\operatorname{IQR},\;Q_3+1.5\operatorname{IQR}\right].
$$

Always sort the data and state the quartile convention. Different software may interpolate quartiles differently.

### Choosing a plot

- Use a **histogram** to examine the shape of one numerical distribution.
- Use a **boxplot** to compare spread and identify possible outliers.
- Use a **scatter plot** to inspect the relationship between two variables.
- Add a **Loess curve** when the relationship may be smooth but nonlinear.

## 12. Data mining versus machine learning

The fields overlap, but their emphasis differs.

| Data mining | Machine learning |
|---|---|
| Focuses on discovering useful knowledge from large datasets | Focuses on learning models that generalize to new data |
| Includes selection, cleaning, pattern evaluation, and presentation | Emphasizes training, prediction, and model evaluation |
| May produce rules, summaries, clusters, or predictive models | Commonly produces a trained predictive or decision model |

Machine-learning algorithms can therefore be used **inside** the data-mining stage of KDD.

## 13. Summary

- Data mining discovers patterns; KDD is the complete process.
- Descriptive mining explains existing data; predictive mining estimates unknown outcomes.
- Choose the mining task from the required output.
- Major issues refer to challenges related to methodology, interaction, scalability, data diversity, and society.
- Preprocessing matters because poor-quality input produces poor-quality patterns.

## Source

Primary supplied material: [Data Mining introduction slides](../sources/1_DM_Intro.pdf).
