# Chapter 7. Application-Oriented Mining

The earlier chapters developed a common process: define the question, represent observations, prepare data, apply a method, and evaluate the result. Text, biological data, and time series follow this process, but their representations must preserve different kinds of structure. A document has language and context; a DNA sequence has ordered symbols; a time series has temporal dependence. Flattening all three into arbitrary columns can destroy the information being mined.

## 1. A Common Framework for Specialized Data

| Domain | Typical observation | Useful representation | Typical tasks | Main evaluation concern |
|---|---|---|---|---|
| Text | Document, message, or passage | Sparse term vector or contextual representation | Retrieval, classification, clustering, topic discovery | Vocabulary leakage, duplicates, source dependence |
| Biological data | Sequence, specimen, or gene-expression profile | Ordered symbols, molecular features, or expression vector | Alignment, motif discovery, classification, clustering | Biological interpretation, batch effects, related samples |
| Time series | Values observed in time order | Ordered observations, lag features, windows | Forecasting, anomaly detection, similarity search | Future leakage and chronological evaluation |

The choice of method follows the task. Known categories suggest classification. Unknown groups suggest clustering. Repeated co-occurrences suggest association mining. Ordered recurring events suggest sequential pattern mining. Unusual observations suggest outlier detection. A useful result must also make sense in its domain.

## 2. Text Mining

**Text mining** extracts useful patterns and knowledge from unstructured or partly structured text. Information retrieval finds documents relevant to a query; text classification assigns predefined categories; text clustering groups similar documents without predefined labels. Information extraction identifies entities, attributes, or relationships, such as a product name and a reported defect.

### Preparing Documents

A basic document-processing pipeline has the following steps:

1. Define the document unit and remove unwanted markup, duplicate records, or unrelated boilerplate.
2. Tokenize the text into terms. Decide how to treat punctuation, numbers, compound words, and language.
3. Normalize forms when appropriate, for example by lowercasing. Preserve case if it carries useful information.
4. Optionally remove common stop words. Preserve words such as “not” when they affect sentiment or meaning.
5. Optionally stem words to approximate roots, or lemmatize them to dictionary forms. Stemming may produce a form that is not a word; lemmatization uses linguistic information.
6. Learn the vocabulary and weighting statistics from the training documents.
7. Transform training and future documents with that same representation; apply the chosen mining algorithm.

This pipeline is task-dependent. Removing punctuation can damage code or medical abbreviations. Aggressive stemming can merge distinct concepts. Preprocessing decisions should improve the intended signal rather than merely shorten the input.

### Bag-of-Words Representation

A **bag of words** represents a document by term counts and ignores term order. Let the vocabulary be `[bread, butter, milk]`.

| Document | Text | Count vector |
|---|---|---|
| D1 | milk bread bread | (2, 0, 1) |
| D2 | milk butter | (0, 1, 1) |
| D3 | bread butter | (1, 1, 0) |

The document-term matrix has documents as rows and terms as columns. Most entries in a large vocabulary are zero, so sparse storage is important. Binary presence vectors are useful when repeated occurrence should not increase weight.

Bag of words is simple and often effective, but “dog bites man” and “man bites dog” have identical unigram counts. Bigrams preserve some local order; contextual language representations preserve richer relationships but require additional modeling beyond this course's overview.

### TF-IDF and Cosine Similarity

**Term frequency (TF)** measures how strongly a term occurs within a document. **Inverse document frequency (IDF)** reduces the weight of terms occurring in many documents. One explicit convention is

$$
TF(t,d)=\operatorname{count}(t,d),\qquad
IDF(t)=\ln\frac{N}{df(t)},\qquad
w_{t,d}=TF(t,d)IDF(t),
$$

where $N$ is the number of training documents and $df(t)$ counts training documents containing term $t$. Other implementations smooth IDF or normalize TF; numerical answers must state their convention.

In the three-document example, every term occurs in two documents. Each has $IDF=\ln(3/2)=0.405465$. The TF-IDF vectors are therefore $(0.810930,0,0.405465)$, $(0,0.405465,0.405465)$, and $(0.405465,0.405465,0)$.

For nonzero vectors $x,y$, cosine similarity is

$$
\operatorname{cos}(x,y)=\frac{x\cdot y}{\|x\|_2\|y\|_2}.
$$

Using the count vectors gives $\operatorname{cos}(D1,D2)=1/\sqrt{10}=0.316228$ and $\operatorname{cos}(D1,D3)=2/\sqrt{10}=0.632456$. D1 shares its dominant term with D3. Here TF-IDF gives the same cosines because every coordinate receives the same IDF multiplier. In general, IDF changes the direction of vectors and therefore their similarities.

Zero vectors require a declared policy because their cosine is undefined. They may arise when a document contains no vocabulary terms after preprocessing.

### Applying the Earlier Algorithms

A labeled collection of spam and ordinary messages can train a Naive Bayes or other classifier. In multinomial Naive Bayes, the score for class $c$ is proportional to

$$
P(c)\prod_t P(t\mid c)^{\operatorname{count}(t,d)}.
$$

Logarithms turn this product into a sum and reduce numerical underflow. Smoothing prevents an unseen training term from forcing a class score to zero. The multinomial event model differs from the categorical-feature example in Chapter 5: repeated tokens contribute repeatedly.

Unlabeled documents can be clustered using normalized vectors and a similarity-compatible method. Repeated term sets can be mined as itemsets, but a high-confidence co-occurrence rule does not establish a linguistic or causal relationship. Retrieval can rank documents by similarity to a query vector.

Evaluate classification with held-out labels and the metrics in Chapter 5. Evaluate retrieval with relevance judgments, for example precision and recall at a chosen cutoff. Split related or duplicate documents together when their overlap would otherwise make evaluation unrealistically easy. Learn vocabulary and IDF without using the test collection during model selection.

## 3. Biological Data Mining

Biological databases contain DNA and protein sequences, gene-expression measurements, structures, interactions, and annotations. **DNA** sequences use the alphabet A, C, G, T; proteins use an alphabet of amino acids. An expression matrix commonly records specimens as rows and measured genes as columns. These representations answer different questions and should not be interchanged.

Typical tasks include finding conserved sequences, recognizing sequence families, discovering expression-based groups, identifying interaction patterns, and detecting unusual profiles. Sequence similarity can suggest a relationship, but interpreting biological function requires supporting domain evidence.

### Preparing Biological Measurements

Check missing values, measurement quality, identifier consistency, and sample provenance. Expression data may need suitable transformation and normalization before distance-based analysis. A **batch effect** is systematic variation associated with measurement conditions, such as different laboratories or runs, rather than the biological factor of interest.

Feature selection is important when there are far more measured genes than samples. Fit selection, scaling, and learned corrections within the training process. Related specimens or repeated measurements should not be split in a way that leaks specimen-specific information into evaluation. Preserve annotations so that a statistical pattern can be interpreted after discovery.

### Sequence Alignment

**Sequence alignment** inserts gaps to compare ordered symbols. A global alignment compares complete sequences; a local alignment seeks a well-matching region. An alignment score depends on the declared match, mismatch, and gap costs.

For a basic global alignment, let sequences be $X=x_1\ldots x_m$ and $Y=y_1\ldots y_n$. Let $D(i,j)$ be the best score for aligning their first $i$ and $j$ symbols. Use match score +1, mismatch score -1, and gap score -1.

**Inputs:** two sequences and scoring rules. **Output:** an optimal score and an alignment under those rules. Maintain a score table and traceback choices.

1. Initialize $D(0,0)=0$, $D(i,0)=-i$, and $D(0,j)=-j$.
2. Fill the table row by row using

$$
D(i,j)=\max\left\{
D(i-1,j-1)+s(x_i,y_j),\quad
D(i-1,j)-1,\quad D(i,j-1)-1
\right\}.
$$

3. Start traceback at $D(m,n)$. A diagonal step aligns two symbols; an upward or leftward step aligns a symbol with a gap.
4. Stop at $D(0,0)$ and reverse the traceback output. Ties can produce multiple optimal alignments.

For $X=AC$ and $Y=AGC$:

| Prefix of X / prefix of Y | Empty | A | AG | AGC |
|---|---:|---:|---:|---:|
| Empty | 0 | -1 | -2 | -3 |
| A | -1 | 1 | 0 | -1 |
| AC | -2 | 0 | 0 | 1 |

The final score is 1. Traceback gives

```text
A - C
A G C
```

There are two matches and one gap: $1-1+1=1$. The algorithm considers competing alignments instead of greedily aligning each symbol to the next identical symbol. A full table takes $O(mn)$ time and space. Score-only memory can be reduced; complete traceback then needs additional care. Local alignment changes the recurrence to permit a restart at zero and traces back from a maximum-scoring cell.

### Motifs, Classification, and Clustering

A **motif** is a recurring sequence pattern that may be biologically meaningful. For the exact motif `AC`, sequences `ACGT`, `TACG`, and `TTTT` give sequence support $2/3$. Count whether a sequence contains the motif; multiple occurrences inside one sequence do not increase sequence-level support.

Exact substring mining is only a starting point. Real motifs can allow substitutions or variable spacing, so their scoring and significance must reflect the biological question. An amino-acid pattern cannot be treated as an unordered itemset when position matters.

Expression vectors can support classification of known sample categories or clustering of unknown groups. Gene clustering groups genes with similar profiles across specimens, whereas sample clustering groups specimens across selected genes. The orientation of the matrix therefore determines the meaning of a cluster. A cluster is a statistical grouping, not automatically a validated biological category.

## 4. Time-Series Mining

A **time series** is an ordered sequence of values associated with times. Examples include daily sales, hourly temperatures, and sensor readings. Its observations may depend on earlier observations; shuffling the sequence can erase the pattern or leak future information.

Core tasks include forecasting future values, finding similar subsequences, identifying recurring shapes or motifs, discovering change points, and detecting anomalous events. Distinguish these tasks before choosing features and evaluation.

### Components and Preparation

| Component | Meaning | Example |
|---|---|---|
| Trend | Long-term movement | Sales rise over several years |
| Seasonality | Repetition with a known calendar or sampling period | A weekly shopping pattern |
| Cycle | Fluctuation without a fixed seasonal period | A business expansion and contraction |
| Irregular component | Remaining variation | An isolated disruption or measurement noise |

An additive representation is $y_t=T_t+S_t+R_t$, with trend $T_t$, seasonality $S_t$, and residual $R_t$. A multiplicative representation is useful for positive data when the seasonal amplitude grows with the level. These are modeling assumptions, not mandatory properties of every series.

Check timestamp order, sampling frequency, missing intervals, duplicates, units, and changes in measurement. Distinguish an absent observation from a true zero. Smoothing can reveal broad structure but can also hide short anomalies. Differencing, $\Delta y_t=y_t-y_{t-1}$, removes a level and can reduce some trends; it changes the meaning of the variable being modeled.

Window features can include recent values, moving averages, and calendar indicators. A feature used to predict time $t+1$ must be available at prediction time. A centered moving average uses future observations and is unsuitable as an online forecasting feature unless those observations are actually available.

### A Complete Moving-Average Forecast

For a window of length $w$, forecast the next value by the mean of the last $w$ observed values:

$$
\hat y_{t+1}=\frac1w\sum_{j=0}^{w-1}y_{t-j}.
$$

**Inputs:** ordered observations, window length, and forecast horizon. **Output:** successive forecasts. Maintain the most recent window; produce a forecast, reveal the next actual observation, update the window, and continue until the test interval ends.

Take training observations 100, 120, 110 and $w=3$. Hold out the next observations 130 and 125.

| Forecast step | Available window | Forecast | Actual | Actual minus forecast |
|---|---|---:|---:|---:|
| First | 100, 120, 110 | 110 | 130 | 20 |
| Second, after observing 130 | 120, 110, 130 | 120 | 125 | 5 |

Thus $MAE=(20+5)/2=12.5$ and $RMSE=\sqrt{(400+25)/2}=14.577380$. This is **rolling one-step evaluation** because the first actual test observation becomes available before the second forecast. A two-step forecast made entirely at the original cutoff would require a different procedure; it cannot use the future actual 130.

A running sum permits $O(1)$ update work per observation with a fixed window. A moving average is interpretable but lags changing levels and does not automatically model seasonality. Compare forecasts against an appropriate baseline, such as predicting the most recent observation or the same seasonal position.

### Similarity and Temporal Anomalies

Euclidean distance compares aligned time positions and is sensitive to scale and timing shifts. Normalize only when amplitude is irrelevant to the question. **Dynamic time warping (DTW)** permits constrained nonlinear alignment between positions; it can match similar shapes occurring at different speeds. Excessive warping can align unrelated events, so the permitted path and window matter.

A point unusual relative to the whole series is a global anomaly. A value normal in one season but unusual in another is contextual. A sustained unusual run may be a collective anomaly. This connects temporal mining to the outlier distinctions in Chapter 6.

Use chronological holdouts or rolling-origin evaluation. Fit transformations and select parameters using the available past, and keep the final future interval untouched until evaluation. Random row splits generally overstate performance when adjacent or future-dependent observations cross the split.

## 5. Sequential Patterns and Association Rules

An association rule concerns items occurring together in a transaction. A **sequential pattern** concerns events occurring in order across a sequence. Buying bread and milk together differs from buying a phone and later buying a case. Sequence support counts sequences containing the pattern, not the number of embeddings within each sequence.

Consider singleton-event sequences:

| Sequence | Ordered events |
|---|---|
| S1 | a, b, c |
| S2 | a, c |
| S3 | b, c |
| S4 | a, b |

With minimum sequence support count 2, a simple levelwise algorithm works as follows.

1. Scan sequences and count distinct one-event patterns. Here a, b, and c each have support 3.
2. Generate longer ordered candidates from frequent shorter patterns. Prune a candidate when a required subsequence is infrequent.
3. Count each candidate at most once per input sequence.
4. Retain candidates meeting support 2; repeat until no longer frequent patterns exist.

At length two, `ab`, `ac`, and `bc` each occur in two sequences. The reverse orders `ba`, `ca`, and `cb`, and repeated-symbol pairs, have support zero. The length-three candidate `abc` passes subsequence pruning but occurs only in S1, so its support is 1 and it is rejected. Mining terminates with the three frequent singletons and three frequent pairs.

Under ordinary subsequence support, every subsequence of a frequent sequence must be frequent: any sequence containing the longer pattern also contains its subsequences. This is the same downward-closure reasoning used by Apriori, applied to order-preserving patterns. Additional constraints such as maximum gaps require careful treatment because deleting an intermediate event can change whether a constrained occurrence is valid.

General sequential mining can allow an event to contain several simultaneous items and may use projection methods instead of repeated candidate generation. The distinction between simultaneous itemsets and successive events must remain explicit.

## 6. Sources

The shared textbook supplies this syllabus overview: Chapter 8 for time series, sequences, and biological data; Chapter 10 for text mining. There is no separate application lecture deck. The worked examples are teaching examples; the short-note topics also address Q6 of the 2025 and 2026 papers.
