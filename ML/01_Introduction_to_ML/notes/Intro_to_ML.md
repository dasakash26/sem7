# Introduction to Machine Learning

> **CT source range:** `01_ML-UNIT-1-notes.pdf`, PDF pages 1–74 **excluding pages 9–23**, according to the handwritten CT sheet. Use this note for recall; use the specified PDF pages for lecturer-specific wording and diagrams.

## Evolution of Machine Learning
Machine learning (ML) is a branch of artificial intelligence (AI) that allows computers to **learn without explicit programming**. Instead of following rigid instructions, ML algorithms analyze data, identify patterns, and make predictions.

- **1943**: Early ideas emerged but limited by computing power
- **Present**: Explosion of data + computing power has pushed ML to the forefront
- **Applications**: Spam filters, recommendation systems, speech recognition, image recognition

## Learning Paradigms

### 1. Supervised Learning
- Uses **labelled datasets** (each data point paired with a class label)
- Builds a mathematical function mapping input features to desired output
- Common tasks: **Classification** and **Regression**

### 2. Unsupervised Learning
- Works with **unlabeled data** to identify patterns and relationships
- Techniques: **Clustering**, **Association rules**

### 3. Semi-supervised Learning
- Combines **small labelled data + larger unlabeled data**
- Cost-effective when labeled data is limited

### 4. Self-supervised Learning (SSL)
- Pre-trains models using unlabeled data
- Data labels generated automatically during iterations
- Transforms unsupervised into supervised problems

### 5. Reinforcement Learning
- Agents learn through **trial-and-error** interactions
- Goal: Maximize a **reward function**
- No labeled datasets needed
- Examples: Self-driving cars, AlphaGo Zero

---

## The 7 Stages of Machine Learning

1. **Problem Definition** - Define and understand the problem, business goals
2. **Data Collection** - Gather data from various sources (sensors, APIs, surveys, web scraping)
3. **Data Preparation** - Filtering, validation, cleansing, formatting, aggregation (takes 70-90% of project time)
4. **Data Visualization** - EDA using charts, histograms, heat maps, word clouds
5. **ML Modeling** - Apply mathematical/CS knowledge to train algorithms (regression, classification, clustering)
6. **Feature Engineering** - Mathematical/statistical/heuristic procedures to find optimal inputs
7. **Model Deployment** - Put model into production, monitor, and iterate

### Three Phases
1. **Business Value** - Customer benefits & business impact
2. **Proof of Concept (POC)** - Data Collection through Feature Engineering
3. **Production** - Scale and integrate into business process

---

## Data Acquisition (DAQ)
"The process of collecting and storing data for machine learning from a variety of sources."

### Components of a DAQ System
1. **Sensors** - Convert physical parameters → electrical signals
2. **Signal Conditioner** - Amplification, filtering, isolation
3. **Analog-to-Digital Converter (ADC)** - Converts analog → digital
4. **Data Logger** - Manages data, controls acquisition, stores data
5. **Data Processing Unit** - Sampling, buffering, data transfer
6. **Data Storage** - Real-time monitoring memory

### DAQ Sources
- Sensors, IoT devices, network devices
- Manual data entry, experiments, observations
- Simulations, web scraping/compiling
- Institutional data banks, published datasets (Kaggle, GitHub, UCI)
- APIs, surveys

---

## Data Matching

### Probabilistic Data Linkage
- Uses **statistical methods** to determine likelihood that two records refer to same entity
- Compares fields and assigns similarity scores
- **ML algorithms** used for feature extraction and scoring
- **Pros**: Works on unstructured data, handles spelling variants, more accurate
- **Cons**: Harder to configure, needs training data, less interpretable

### Deterministic Data Matching
- Uses **rule-based methods** (e.g. RegEx) for exact matches
- **Pros**: Easy to configure, works with little data
- **Cons**: Less accurate, less flexible, may miss relationships

### Applications
- Data deduplication, data enrichment, data integration
- Fraud detection, building customer 360 profiles

---

## Feature Engineering
"Process of transforming raw data into features suitable for ML models."

### 5 Core Processes
1. **Feature Creation** - Generate new features from domain knowledge or data patterns
2. **Feature Transformation** - Normalize, scale, encode, apply math operations
3. **Feature Extraction** - Dimensionality reduction (PCA, t-SNE), feature combination/aggregation
4. **Feature Selection** - Filter, wrapper, or embedded methods to select relevant subset
5. **Feature Scaling** - Min-Max scaling, Standard scaling, Robust scaling

### Benefits
- Improves model performance and robustness
- Reduces overfitting and computational costs
- Improves interpretability

---

## Learning by Rote
- Learning by **repetition** (facts, dates, multiplication tables)
- Techniques: Read aloud, write down, visualize, free association
- **Pros**: Requires little analysis, helps short-term memory
- **Cons**: Dull, no deep understanding, no connection between new/old information

## Learning by Induction (ILA)
- Iterative algorithm generating **IF-THEN** classification rules
- Overcomes limitations of ID3 and AQ algorithms (better generalization of rules)
- Works even when some attributes are missing
