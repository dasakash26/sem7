# Feedforward Neural Networks & NLP Applications

> **CT relevance:** use the architecture, forward pass, output-layer, loss, vectorization and training sections. NLP embeddings and language models are supporting applications unless separately named by the teacher. The handwritten CT sheet prioritizes MLP/FFNN, HMM and CRF.

## Feedforward Neural Network Architecture

### Basic Structure
```
Input Layer → Hidden Layer(s) → Output Layer
```

### 2-Layer Feedforward Network
$$h = \sigma(Wx + b)$$
$$z = Uh$$
$$y = \text{softmax}(z)$$

Where:
- $x \in \mathbb{R}^{n_0}$: input features
- $h \in \mathbb{R}^{n_1}$: hidden representation  
- $W \in \mathbb{R}^{n_1 \times n_0}$: weight matrix (input→hidden)
- $U \in \mathbb{R}^{n_2 \times n_1}$: weight matrix (hidden→output)
- $y \in \mathbb{R}^{n_2}$: output probabilities

### Feedforward Networks for NLP Classification

#### Approach 1: Hand-built Features
$$x = [x_1, x_2, ..., x_d] \text{ (each } x_i \text{ is hand-designed)}$$
$$h = \sigma(Wx + b)$$
$$z = Uh$$
$$\hat{y} = \text{softmax}(z)$$

Adding a hidden layer allows the network to model **non-linear interactions between features**.

#### Approach 2: Embeddings as Input
- Instead of hand-built features, use **word embeddings**
- Tokens → vocabulary indices → embedding vectors
- Embedding matrix $E$: shape $[|V| \times d]$

### Selecting Embeddings
- Represent token as **one-hot vector** (length $|V|$, 1 at token's index)
- Multiply one-hot vector by embedding matrix $E$ → get embedding row

### Pooling
Turn $N$ embeddings into a single embedding vector:
- **Mean pooling**: $x = \frac{1}{N}\sum_{i=1}^{N} e(w_i)$
- **Max pooling**: Element-wise max over all $N$ vectors

Used for: Sentiment classification (word order less important)

### Concatenation
- Concatenate all $N$ embeddings into one long vector $[1 \times dN]$
- More information preserved (word order matters)
- Used for: Language modeling

---

## Feedforward Neural Language Model
Approximates $P(w_t|w_{t-N+1}, ..., w_{t-1})$

### Architecture
1. **Embedding layer**: Convert each context word to embedding
2. **Concatenate**: $e = [Ex_{t-3}; Ex_{t-2}; Ex_{t-1}]$
3. **Hidden layer**: $h = \sigma(We + b)$
4. **Output layer**: $z = Uh$, $\hat{y} = \text{softmax}(z)$

### Advantages over n-gram LM
- Uses embeddings → generalizes across similar words
- Handles longer contexts
- Example: Knowing "cat" and "dog" have similar embeddings helps predict "fed" after both

---

## Word Embeddings

### Definition
A dense vector representation of a word:
- Each word $w$ has an embedding $E_{:,w}$ (column/row in embedding matrix $E$)
- Dimensionality $d$ typically 50-300 (much smaller than vocabulary size)

### Properties
- Similar words have similar embeddings
- Analogies can be represented: $e_{king} - e_{man} + e_{woman} \approx e_{queen}$
- Can capture semantic and syntactic information

---

## Output Layer Types

| Task | Output | Activation |
|------|--------|-----------|
| **Regression** | Single real value | Identity (linear) |
| **Binary Classification** | Single value | Sigmoid (→ 0-1) |
| **Multi-label Classification** | Multiple values | Sigmoid each |
| **Multi-class Classification** | K probabilities | Softmax |

---

## Vectorized Computation
Process entire test set at once using matrix operations:

$$H = \sigma(XW^T + b)$$
$$Z = HU^T$$
$$\hat{Y} = \text{softmax}(Z)$$

Where $X$ is $[m \times d]$, each row = one input example.

---

## Training Feedforward Networks

### Gradient-Based Learning
1. Define loss function (Cross-entropy for classification, MSE for regression)
2. Compute gradient of loss w.r.t. each parameter
3. Update parameters: $\theta = \theta - \eta \nabla_\theta L$
4. Use **stochastic gradient descent** (random training examples)

### Backpropagation
Same principle as covered in [[Multilayer_Backpropagation|Multi-Layer & Backpropagation]]:
1. Forward pass: compute predictions
2. Compute gradients starting from output layer
3. Propagate gradients backwards through hidden layers
4. Update all weights

### Practical Tips
- **Learning rate**: Try 0.1, experiment with powers of ten
- **Adagrad**: Automatically chooses different learning rates per parameter
- **Gradient clipping**: Cap large gradients to prevent instability

---

## Why Neural Networks Over Linear Models?

### Limitation of Linear Models
- Linear models can only learn **linearly separable** functions
- Cannot model XOR without feature engineering
- $P(y|x; v) \propto \exp(v(y) \cdot f(x) + \gamma_y)$ is linear in $f(x)$

### Neural Network Advantage
- $P(y|x; \theta, v) \propto \exp(v(y) \cdot \phi(x; \theta) + \gamma_y)$
- $\phi(x; \theta)$ is the **learned representation** (non-linear function of input)
- Automatically discovers useful features from data
- More layers → higher-level, more abstract features

---

## Depth Considerations

### Universal Approximation
- Shallow (1 hidden layer) networks can represent any continuous function
- BUT: may require **exponentially many neurons**

### Depth Efficiency
- Deep networks can represent some functions with far fewer neurons
- Example: Parity function needs exponential size with shallow net, linear with deep net
- Each additional layer learns increasingly abstract features

### Visual Example (MNIST)
- First layer features: Oriented edges, localized patterns
- Higher layers: More abstract, high-level features
- Networks automatically learn useful representations from raw data
