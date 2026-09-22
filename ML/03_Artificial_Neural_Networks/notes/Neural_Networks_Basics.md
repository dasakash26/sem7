# Neural Networks - Fundamentals

> **CT source range:** the handwritten sheet marks `Artificial_Neural_Network.pdf` as complete and separately names multilayer perceptrons. Study the full assigned source, then use this note for active recall.

## Biological Inspiration
- Neural networks inspired by the brain but are NOT models of the brain
- Biological neuron: Dendrites → Soma (cell body) → Axon → Synapses
- **McCulloch-Pitts (1943)**: First mathematical model of a neuron
- Brain: Slow, unreliable processors acting in parallel, learns by experience
- Computer (Von Neumann): Single processing unit, explicit programming needed

## Artificial Neuron Model (Perceptron)

### Components
1. **Inputs** $(x_1, x_2, ..., x_n)$
2. **Weights** $(w_1, w_2, ..., w_n)$
3. **Bias** $b$
4. **Weighted Sum** (net input): $z = \sum w_i x_i + b = \mathbf{w} \cdot \mathbf{x} + b$
5. **Activation Function** $f(z)$ - introduces non-linearity

### Mathematical Foundation
$$y = f\left(\sum_{i=1}^{n} w_i x_i + b\right)$$

In vector form:
$$y = f(\mathbf{w} \cdot \mathbf{x} + b)$$

---

## Activation Functions

### 1. Binary Step (Threshold/Heaviside)
$$f(x) = \begin{cases} 1 & \text{if } x \geq \theta \\ 0 & \text{if } x < \theta \end{cases}$$

### 2. Sigmoid (Logistic)
$$f(x) = \frac{1}{1 + e^{-x}}$$
- Output range: $(0, 1)$
- Smooth, differentiable
- Derivative: $f'(x) = f(x)(1-f(x))$

### 3. Hyperbolic Tangent (tanh)
$$f(x) = \frac{e^x - e^{-x}}{e^x + e^{-x}}$$
- Output range: $(-1, 1)$
- Zero-centered (better than sigmoid)
- Derivative: $f'(x) = 1 - f(x)^2$

### 4. ReLU (Rectified Linear Unit)
$$f(x) = \max(0, x)$$
- Most commonly used today
- Computationally efficient
- Avoids vanishing gradient problem (derivative = 1 for $x > 0$)
- Derivative: $f'(x) = 1$ for $x > 0$, $0$ for $x < 0$, undefined at $x=0$

### 5. Leaky ReLU, ELU, Maxout
- Variants to address ReLU's "dying ReLU" problem

---

## Perceptron and Decision Boundary

### Linear Classifier
The perceptron defines a **hyperplane** (decision boundary):
$$\mathbf{w}^T \mathbf{x} + b = 0$$

- **Weight vector** $\mathbf{w}$ is orthogonal to the decision boundary
- Weight vector points in direction of positive class
- **Bias** determines position of the boundary

### Linearly Separable vs Non-Separable
| Separable                                    | Inseparable                        |
| -------------------------------------------- | ---------------------------------- |
| AND, OR                                      | XOR                                |
| Can be separated by a single line/hyperplane | Cannot be separated by single line |

### Logic-gate examples

With $y=step(w_1x_1+w_2x_2+b)$ and $step(z)=1$ for $z\ge0$:

| Gate | One valid choice |
|---|---|
| AND | $w_1=w_2=1,\ b=-1.5$ |
| OR | $w_1=w_2=1,\ b=-0.5$ |
| NAND | $w_1=w_2=-1,\ b=1.5$ |
| NOT | $w=-1,\ b=0.5$ |

Verify all truth-table rows rather than memorizing only the weights. XOR cannot be implemented by one perceptron because no single straight line separates its positive points from its negative points.

### From perceptron to multilayer perceptron

An MLP connects neurons in layers. A hidden layer creates a learned nonlinear representation, and the output layer classifies that representation:

$$
\mathbf z^{(l)}=W^{(l)}\mathbf a^{(l-1)}+\mathbf b^{(l)},\qquad
\mathbf a^{(l)}=g^{(l)}(\mathbf z^{(l)}).
$$

Nonlinear activations are essential. If every layer is linear, their composition collapses into one linear transformation and still cannot solve XOR.

---

## Perceptron Learning Rule

### Algorithm
1. Initialize weights to small random values
2. For each training example $(x, t)$:
   - Compute output $y = f(\mathbf{w} \cdot \mathbf{x} + b)$
   - If misclassified, update:
$$\mathbf{w} \leftarrow \mathbf{w} + \eta (t - y) \mathbf{x}$$

### Delta Rule
$$w_{ij}(t+1) = w_{ij}(t) + \eta \cdot (target_i - output_i) \cdot x_j$$

### Perceptron Convergence Theorem
- Guaranteed to converge to correct weights if training data is **linearly separable**
- Converges in finite number of steps
- Does NOT converge if data is not linearly separable

---

## ADALINE (Adaptive Linear Element)
- Proposed by Widrow & Hoff (1960)
- Minimizes **squared error** rather than classification error
- Uses linear activation during training, threshold for classification
- **Widrow-Hoff/LMS/Delta Rule**:
$$w_i \leftarrow w_i + \eta(d-y)x_i$$

---

## Neural Network Topologies

### Types
1. **Fully connected** - Every node connected to every other node
2. **Layered** - Nodes partitioned into layers, no connections from layer $j$ to $k$ if $j > k$
3. **Acyclic** - Subclass of layered, no intra-layer connections
4. **Feedforward** - Subclass of acyclic, connections only from layer $i$ to layer $i+1$
5. **Modular** - Several modules with sparse interconnections
6. **Recurrent** - Feedback connections (cycles)

---

## Learning Paradigms

### Supervised Learning
- Network provided with input-target pairs $\{p_1, t_1\}, ..., \{p_Q, t_Q\}$
- Teacher present; error computed and used to adjust weights

### Unsupervised Learning
- Only network inputs available
- Network learns to categorize/cluster inputs

### Reinforcement Learning
- Network receives grade/score indicating performance
- Not exact target values

---

## Different Learning Rules

| Rule | Description |
|------|-------------|
| **Hebb's Law** | Weight strengthening based on correlated neuron activity |
| **Perceptron Law** | Updates based on binary error |
| **Delta Law** | Gradient descent, minimizes squared error |
| **Widrow-Hoff LMS** | Least mean squares |
| **Correlation Law** | Correlative weight adjustment |
| **Winner-take-all** | Only winning neuron updates weights |

---

## History of Neural Networks
1. **1943**: McCulloch-Pitts neuron model
2. **1949**: Hebb's learning mechanism
3. **1958**: Rosenblatt's Perceptron + learning rule
4. **1960**: Widrow & Hoff's ADALINE
5. **1969**: Minsky & Papert - limitations of single-layer perceptrons → NN research declines
6. **1970s-80s**: Anderson, Kohonen, Grossberg, Hopfield continue work
7. **1986**: Rumelhart, Hinton, Williams - **Backpropagation** → NN resurgence
8. **1995-2010**: Second decline (overfitting issues, slow computers)
9. **2010-present**: **Deep Learning Revolution** (CNNs, AlexNet 2012, AlphaGo)
