# Advanced Neural Network Architectures

## 1. Back Propagation Networks (BPN)

### Overview
- Introduced by Rumelhart, Hinton & Williams (1986)
- Multi-layer feedforward network
- Uses **supervised training** with error backpropagation
- Employs **Generalized Delta Law / Continuous Perceptron Law / Gradient Descent Law**

### Architecture
- Input layer → Hidden layer(s) → Output layer
- Weights: $W_{ij}$ (input→hidden), $V_{jk}$ (hidden→output)

### Training (3 Stages)
1. **Feedforward** of input training pair
2. **Calculation and backpropagation** of associated error
3. **Adjustment of weights**

### Merits
- Smooth weight correction
- 100x faster than perceptron model
- Systematic weight updating procedure

### Demerits
- Intensive calculations in learning phase
- Selection of hidden layer size is challenging
- Can get trapped in **local minima**
- Training time is long for complex problems

---

## 2. Counter Propagation Network (CPN)

### Overview
- Proposed by Hecht-Nielsen (1987)
- Combines **unsupervised** (Kohonen layer) + **supervised** (Grossberg layer) learning
- Functions like a **Look-up Table Generalization**
- Correct output even when input is partially incomplete

### Architecture
- **Kohonen Layer**: Competitive/unsupervised, winner-takes-all
- **Grossberg Layer**: Supervised, similar to BPN forward pass

### Types
- **Full CPN**: Complete bidirectional propagation
- **Forward-only CPN**: Simplified, used for approximation problems

### Training
- **Kohonen**: Self-organizing unsupervised, winner-takes-all weight update
- **Grossberg**: Supervised, weights updated based on Delta Law

### Merits
- 100x faster than BPN
- Fast and coarse approximation

### Demerits
- Intensive calculations
- Hidden layer selection issues
- Can get trapped in local minima

---

## 3. Hopfield Networks

### Overview
- Fully interconnected network with **symmetric weights**
- $W_{ij} = W_{ji}$, $W_{ii} = 0$
- Uses **Lyapunov Energy Function**
- Content-addressable memory

### Types
| Type | Activation |
|------|-----------|
| **Discrete Hopfield** | Bipolar: +1/-1 |
| **Continuous Hopfield** | Continuous activation |

### Energy Function
$$\text{Change in Energy} = -(\text{net}_i) \Delta Y_i$$

- Energy always decreases or stays constant
- Network converges to **stable minimum energy state**
- Stable states correspond to stored memory patterns

### Algorithm
1. Initialize weights to store patterns
2. Set initial activations = external input vector
3. For each unit: compute net input, update activation
4. Broadcast to all other units
5. Test for convergence

### Applications
- Pattern recognition and restoration
- Optimization problems (TSP)
- Constraint satisfaction

### Merits & Demerits
| Merits | Demerits |
|--------|----------|
| Unconditionally stable | Incorrect convergence possible |
| Best for content-addressable memory | Limited memory capacity |
| Best recall | May learn unintended patterns |

---

## 4. Bi-Directional Associative Memory (BAM)

### Overview
- Developed by Kosko (1988)
- Hetero-associative, two-layer network
- Signals transmitted **back and forth** between layers
- Stored patterns can be recalled from noisy/corrupted inputs

### Types
1. **Binary BAM** - Binary activation functions
2. **Bipolar BAM** - Bipolar activation functions
3. **Continuous BAM** - Log-sigmoidal activation

### Architecture
- $n$ neurons in $X$ layer, $m$ neurons in $Y$ layer
- Both layers can act as input or output
- Weights: $W_{ij}$ for X→Y, $W_{ij}^T$ for Y→X (bidirectional)

### Algorithm
```
1. Initialize weights to store patterns
2. Present input pattern x to X layer, y to Y layer
3. While not converged:
   - Update Y layer activations, send to X
   - Update X layer activations, send to Y
4. Stop when activations reach equilibrium
```

### Applications
- Fault detection, pattern association
- Medical diagnosis, pattern mapping
- Optimization problems

---

## 5. Adaptive Resonance Theory (ART)

### Overview
- Invented by Grossberg (1976)
- Solves **plasticity-stability dilemma**
- Unsupervised learning model
- Resonance = target vector matches input vector closely enough

### Types
| Type | Input Type |
|------|-----------|
| ART-1 | Binary input vectors |
| ART-2 | Real-valued (continuous) vectors |

### Architecture Components
1. **F1 Layer** (Comparison layer) - Input processing
2. **F2 Layer** (Recognition layer) - Clustering/competition
3. **Reset Mechanism** - Vigilance control

### Vigilance Parameter
- Higher → finer, more detailed memories
- Lower → more general memories

### Learning
- **Fast learning** (ART-1): Weight changes rapid, during resonance
- **Slow learning** (ART-2): Gradual weight change, requires more memory

### Algorithm
```
1. Initialize parameters
2. For each input:
   - Process F1 layer
   - Find matching candidate in F2
   - Test reset condition against vigilance
   - If accepted, learn; else reject and try next candidate
```

### Applications
- Pattern recognition, restoration, generalization
- Speech recognition
- Image enhancement/restoration
- Facial recognition

---

## 6. Self-Organizing Maps (SOM/Kohonen)

### Overview
- Developed by Teuvo Kohonen (1980s)
- **Topology-preserving maps**
- Unsupervised, dimensionality reduction
- Converts arbitrary dimensions → 1D or 2D map

### Grid Types
- Rectangular
- Hexagonal

### Algorithm
```
1. Initialize weights, learning rate, neighborhood parameters
2. For each input vector x:
   - Find winning neuron j with minimum D(j) = Σ (W_ij - x_i)²
   - Update weights for all units in neighborhood:
     W_ij(new) = W_ij(old) + α[x_i - W_ij(old)]
3. Update learning rate, reduce neighborhood radius
4. Test for stop condition
```

### Process Phases
- **Competition**: Find best-matching neuron (smallest distance)
- **Cooperation**: Winning neuron determines excited neighborhood
- **Adaptation**: Weights adjusted to better match inputs

### Merits
- Dimensionality reduction
- Easy to interpret and implement
- Can handle large complex input sets

### Applications
- Character, speech, texture recognition
- Image and data clustering
- Seismic analysis, failure analysis

---

## 7. Learning Vector Quantization (LVQ)

### Overview
- Multi-class classification
- Uses **codebook vectors** created from training data
- Similar to K-Nearest Neighbor procedure

### Algorithm
```
1. Initialize weight vectors to m training vectors
2. For each input X:
   - Find closest codebook vector (minimum Euclidean distance)
   - If class matches: W(new) = W(old) + α[X - W(old)]
   - If class differs: W(new) = W(old) - α[X - W(old)]
3. Reduce learning rate α
4. Test stop condition
```

---

## 8. Cascade Correlation Networks (CCNN)

### Overview
- Supervised feedforward network
- Starts minimal → **automatically adds** hidden units
- Determines its own size and topology

### Process
1. Start with only input and output units
2. Train until no improvement
3. Add new hidden unit (candidate):
   - Connect to all inputs
   - Train to maximize correlation with residual error
   - Freeze weights, connect to output units
4. Retrain output connections
5. Repeat

### Merits
- Learns 10x faster than standard backprop
- Network determines own topology
- Useful for incremental learning

---

## 9. Probabilistic Neural Network (PNN)

### Overview
- Three-layer network
- Hidden layer nodes correspond to **Gaussian functions** centered on feature vectors
- Related to Parzen Window PDF Estimator

### Architecture
- Input layer → Hidden layer (Gaussian nodes grouped by class) → Output (summation per class)

### Classification
1. Feed input to Gaussian functions in each class group
2. Sum Gaussian outputs per class
3. Find class with maximum summed value

---

## 10. General Regression Neural Network (GRNN)

### Overview
- Proposed by D.F. Specht (1991)
- Single-pass learning
- Function approximation based on weighted average

### Architecture (4 layers)
1. Input layer
2. Hidden (pattern) layer - Gaussian activation
3. Summation layer
4. Output (division) layer

### Key Parameter: Spread $\sigma$
- Selected when MSE is minimum (through validation)
- Weights calculated using Euclidean distance between training and test data