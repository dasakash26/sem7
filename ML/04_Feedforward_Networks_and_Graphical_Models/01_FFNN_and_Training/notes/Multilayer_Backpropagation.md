# Multi-Layer Networks & Backpropagation

> **CT sources named on the handwritten sheet:** `13_Multilayer_Perceptron.pdf` pages 1–44; `in3050_lecture_07_ffnn_2025.pdf` pages 1–64; an assigned video corresponding to `03-4-5`; plus unidentified complete Medium/freeCodeCamp resources. Use [CT Scope Map](../../../00_Course_Guide/CT_SCOPE_MAP.md) for the source-level transcription.

> **Sign convention used here:** $\delta^{(l)}=\partial L/\partial z^{(l)}$, so parameters update by **subtracting** the gradient. Some slides define delta with the opposite sign and then add it. Both conventions work; never mix them in one derivation.

## Multi-Layer Perceptron (MLP)

### Architecture
- **Input Layer**: Receives input features, passes them forward
- **Hidden Layer(s)**: One or more intermediate layers with non-linear neurons
- **Output Layer**: Produces final prediction

### Why Multiple Layers?
- Single-layer perceptrons can only solve **linearly separable** problems
- Cannot solve XOR (non-linearly separable)
- Hidden layers create **convex regions** that can separate complex patterns
- **Universal Approximation Theorem**: An MLP with a single hidden layer and non-linear activation can approximate any continuous function to arbitrary accuracy

### Mathematical Representation (2-Layer Network)
$$
\begin{aligned}
h &= \sigma(Wx + b) \\
z &= Uh \\
y &= \text{softmax}(z)
\end{aligned}
$$

Where:
- $x \in \mathbb{R}^{n_0}$: input vector
- $h \in \mathbb{R}^{n_1}$: hidden layer output
- $W \in \mathbb{R}^{n_1 \times n_0}$: input-to-hidden weights
- $U \in \mathbb{R}^{n_2 \times n_1}$: hidden-to-output weights
- $y \in \mathbb{R}^{n_2}$: final output (probability distribution)

---

## The XOR Problem

### Problem
Single-layer perceptron **cannot** compute XOR:
| $x_1$ | $x_2$ | XOR |
|----|----|-----|
| 0  | 0  | 0   |
| 0  | 1  | 1   |
| 1  | 0  | 1   |
| 1  | 1  | 0   |

Points (0,1) and (1,0) cannot be separated from (0,0) and (1,1) by a single line.

### Solution: MLP with Hidden Layer
A 2-layer ReLU network can solve XOR:
- Hidden layer transforms input into a **linearly separable space**
- Inputs [0,1] and [1,0] get mapped to same representation [1,0]
- The output layer can then linearly separate the classes

---

## Deep Networks: Forward Propagation

General form for $n$-layer network:
```
for i in 1,...,n:
    z[i] = W[i] a[i-1] + b[i]
    a[i] = g[i](z[i])
ŷ = a[n]
```

Where:
- $a^{[0]} = x$ (input)
- $z^{[i]}$: pre-activation at layer $i$
- $a^{[i]}$: activation at layer $i$
- $g^{[i]}$: activation function for layer $i$
- $ŷ$: final output prediction

### Why Non-Linearity is Essential
Without non-linear activation, multiple layers collapse to a single linear function:
$$
\begin{aligned}
z^{[2]} &= W^{[2]}(W^{[1]}x + b^{[1]}) + b^{[2]} \\
&= W^{[2]}W^{[1]}x + W^{[2]}b^{[1]} + b^{[2]} \\
&= W'x + b'
\end{aligned}
$$

---

## Backpropagation Algorithm

### Core Idea
1. **Forward pass**: Compute predictions and loss
2. **Backward pass**: Propagate errors from output to input, computing gradients for each weight
3. **Update weights**: Using gradient descent

### Loss Functions
- **Mean Squared Error (MSE)**: $E = \frac{1}{2}\sum_{n}(ŷ_n - y_n)^2$
- **Cross-Entropy Loss**: $L_{CE} = - \sum_{k} y_k \log ŷ_k$

### Backpropagation Derivation

For a sigmoid output with half-squared error, using $z$ for preactivation and $a=\sigma(z)$ for activation:

$$
\delta^{(L)}=(a^{(L)}-y)\odot\sigma'(z^{(L)}).
$$

For sigmoid with binary cross-entropy, or softmax with categorical cross-entropy, the combined output derivative simplifies to:

$$
\delta^{(L)}=a^{(L)}-y.
$$

For hidden layers:
$$
\delta^{(l)}=((W^{(l+1)})^T\delta^{(l+1)})\odot g'^{(l)}(z^{(l)}).
$$

Weight update:
$$\frac{\partial E}{\partial w_{ij}} = \delta_j a_i^{\text{previous layer}}$$
$$w_{ij} \leftarrow w_{ij} - \eta \frac{\partial E}{\partial w_{ij}}$$

### Intuition
- **Output layer**: Error is direct - difference between prediction and target
- **Hidden layers**: Error is the **weighted sum** of errors from the next layer
- "Backpropagation" because errors flow **backwards** through the network

### The Chain Rule
The key mathematical tool enabling backpropagation:
$$\frac{\partial L}{\partial w_{ij}} = \frac{\partial L}{\partial a_j} \cdot \frac{\partial a_j}{\partial w_{ij}}$$

---

## Backpropagation Algorithm (Detailed)

### Steps
1. **Initialize** weights to small random values
2. **Present input** and desired output
3. **Forward propagate**: Compute hidden layer outputs → output layer predictions
4. **Compute error**: Compare actual output with target
5. **Backward propagate errors**: 
   - Calculate $\delta$ for output units
   - Calculate $\delta$ for hidden units using next layer's $\delta$ values
6. **Update weights**: $w_{ij} = w_{ij} + \eta \delta_j x_i$
7. **Repeat** until convergence

### Stochastic Backpropagation Algorithm
```
1. Initialize weights to small random values
2. Choose pattern x_k and apply to input layer
3. Propagate signal through network
4. Compute δ for output layer: δ = f'(net)(t - V)
5. Compute δ for preceding layers (m = M, M-1, ..., 2):
   δ^{m-1} = f'(net^{m-1}) Σ W_{ji}^m δ_j^m
6. Update connections: Δw_ij = η δ_i V_j
7. w_new = w_old + Δw; goto 2 for next pattern
```

---

## Practical Considerations

### Weight Initialization
- Don't initialize all weights to zero (all units become identical)
- Small random values near zero
- **Xavier Initialization**: Maintain mean=0 and variance=1 for activations

### Learning Rate $\eta$
- Too large: Oscillation, no convergence
- Too small: Very slow convergence
- Use momentum: $\Delta w(t+1) = -\eta \frac{\partial E}{\partial w} + \alpha \Delta w(t)$
- $\alpha$ typically 0.9

### Training Modes
- **Batch**: Use entire training set for each update
- **Stochastic/Online**: Update after each training example
- **Mini-batch**: Update after small subset (most common in practice)

### Avoiding Overfitting
- **Early Stopping**: Monitor validation loss; stop when it starts increasing
- **Dropout**: Randomly drop neurons during training (forces robust learning)
- **Regularization**: Limit model complexity

### Vanishing Gradient Problem
- Deep networks: Gradients decrease significantly in early layers
- **Sigmoid/Tanh**: Saturated regions have near-zero derivatives
- **ReLU**: Avoids vanishing gradient (derivative = 1 for positive inputs)
- **Xavier Initialization**: Helps prevent vanishing gradients

---

## Feature Scaling & Batch Normalization

### Feature Scaling
- Bring all features to similar scale (mean=0, variance=1)
- Error surface changes from elongated ellipse → circle
- Faster convergence

### Batch Normalization
- Normalize output of **all layers** (not just input)
- $\gamma$ and $\beta$ are learnable parameters
- Backpropagation can undo if needed

---

## Optimizers

| Method | Description |
|--------|-------------|
| SGD | Basic gradient descent (slow) |
| Momentum | Accumulates past gradients for smoother updates |
| Nesterov | "Look-ahead" gradient at projected next position |
| AdaGrad | Adaptive learning rates per parameter |
| AdaDelta | Extension of AdaGrad using RMS |
| RMSprop | Moving average of squared gradients |
| Adam | Combines momentum + adaptive learning rates |

---

## Network Performance Analysis

### Overfitting vs Underfitting
| Underfitting | Good Fit | Overfitting |
|:---:|:---:|:---:|
| Training error high | Both errors low | Training error low, test error high |
| Model too simple | Right complexity | Model too complex |

### Dropout Regularization
- Randomly drop neurons during training with probability $p$
- Forces remaining neurons to compensate → better generalization
- At test time: All neurons retained

---

## Universal Approximation Theorem
- An MLP with a **single hidden layer** and non-linear activation can represent any continuous function
- However, the hidden layer may need to be **exponentially large**
- **Deep networks** can represent some functions much more compactly than shallow ones (e.g., parity function)
- Depth can trade off for exponential growth of width
