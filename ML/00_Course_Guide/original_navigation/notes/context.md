# ML Study Context

## Source Documents
This vault contains notes extracted from 20 PDF lecture notes/slides on Machine Learning and Artificial Neural Networks.

## Note Structure

### Topics Covered

#### 1. [[Intro_to_ML|Introduction to Machine Learning]]
- Learning paradigms (Supervised, Unsupervised, Semi-supervised, Self-supervised, Reinforcement)
- 7 Stages of ML (Problem Definition → Deployment)
- Data Acquisition (DAQ) systems and components
- Feature Engineering (Creation, Transformation, Extraction, Selection, Scaling)
- Data Matching (Probabilistic vs Deterministic)
- Learning by Rote and Inductive Learning (ILA)

#### 2. [[Bayesian_Learning|Bayesian Learning]]
- Bayes Theorem fundamentals and derivations
- MAP and ML hypotheses
- Bayes Optimal Classifier vs Gibbs Algorithm
- Naive Bayes Classifier (with PlayTennis & buyComputer examples)
- Bayesian Belief Networks and Conditional Independence
- EM Algorithm (Gaussian mixtures)
- MDL Principle (information theory motivation)
- Text classification with Naive Bayes

#### 3. [[Neural_Networks_Basics|Neural Networks - Fundamentals]]
- Biological neuron → artificial neuron (McCulloch-Pitts)
- Perceptron architecture and decision boundaries
- Activation functions: Step, Sigmoid, Tanh, ReLU
- Perceptron Learning Rule and Convergence Theorem
- ADALINE and Widrow-Hoff LMS learning
- Linearly separable vs non-separable (AND, OR vs XOR)
- Network topologies (fully connected, layered, acyclic, feedforward, recurrent)
- ANN history timeline (1943-present)

#### 4. [[Multilayer_Backpropagation|Multi-Layer Networks & Backpropagation]]
- MLP architecture and universal approximation theorem
- XOR problem solution with hidden layer
- Forward propagation equations (vectorized form)
- Backpropagation: chain rule, error propagation
- Stochastic gradient descent vs batch vs mini-batch
- Optimizers: SGD, Momentum, Nesterov, AdaGrad, AdaDelta, RMSprop, Adam
- Vanishing gradient problem and Xavier initialization
- Dropout regularization, early stopping
- Feature scaling and batch normalization
- Weight initialization strategies

#### 5. [[Feedforward_NN_NLP|Feedforward Networks & NLP]]
- Feedforward networks with hand-built features
- Word embeddings and embedding matrices
- Pooling (mean, max) for sentiment classification
- Concatenation for language modeling
- Neural language models vs n-gram models
- Output layer types: regression, binary, multi-label, multi-class
- Vectorized batch computation
- Training with gradient descent and backpropagation
- Depth vs width - why depth matters

#### 6. [[Advanced_NN_Architectures|Advanced Architectures]]
- Back Propagation Networks (BPN)
- Counter Propagation Networks (CPN) - Kohonen + Grossberg
- Hopfield Networks and Lyapunov energy function
- Bi-Directional Associative Memory (BAM)
- Adaptive Resonance Theory (ART-1, ART-2)
- Self-Organizing Maps (SOM/Kohonen)
- Learning Vector Quantization (LVQ)
- Cascade Correlation Networks (CCNN)
- Probabilistic Neural Networks (PNN)
- General Regression Neural Networks (GRNN)

## Key Formulas Reference

### Bayes Theorem
$P(h|D) = \frac{P(D|h)P(h)}{P(D)}$

### MAP Hypothesis
$h_{MAP} = \underset{h \in H}{\text{argmax}} P(D|h)P(h)$

### Naive Bayes
$v_{NB} = \underset{v_j \in V}{\text{argmax}} P(v_j) \prod_i P(a_i|v_j)$

### Perceptron
$y = f(\sum w_i x_i + b)$

### Perceptron Learning Rule
$w_i \leftarrow w_i + \eta(t-y)x_i$

### MLP (2-layer)
$h = \sigma(Wx + b), \quad y = \text{softmax}(Uh)$

### Cross-Entropy Loss
$L_{CE} = -\sum_k y_k \log \hat{y}_k$

### Backpropagation (weight update)
$w_{ij} \leftarrow w_{ij} - \eta \frac{\partial E}{\partial w_{ij}}$

### Gradient Descent
$\theta \leftarrow \theta - \eta \nabla_\theta L$

## Source Files
Original PDFs were extracted from:
- `/home/voyager/Downloads/ml/Machine Learning - lec04-BayesianLearning.pdf`
- `/home/voyager/Downloads/ml/Machine Learning - module_5.pdf`
- `/home/voyager/Downloads/ml/bayes.pdf`
- `/home/voyager/Downloads/ml/ML-UNIT-1-notes.pdf`
- `/home/voyager/Downloads/ml/Unit-4.pdf`
- `/home/voyager/Downloads/ml/ann/6.pdf`
- `/home/voyager/Downloads/ml/ann/Template - Clean wavy (orange) - Artificial_Neural_Network.pdf`
- `/home/voyager/Downloads/ml/ann/7_BP_21 - 7_BP_21.pdf`
- `/home/voyager/Downloads/ml/ann/neural network/Multilayer Perceptron - 13_Multilayer_Perceptron.pdf`
- `/home/voyager/Downloads/ml/ann/neural network/L03 Multilayer Perceptrons.pdf`
- `/home/voyager/Downloads/ml/ann/neural network/4-nn2-perceptron - 7-nn2-perceptron.pdf`
- `/home/voyager/Downloads/ml/ann/neural network/Microsoft PowerPoint - lec3.learning - lec3.learning.pdf`
- `/home/voyager/Downloads/ml/ann/SEC1609.pdf`
- `/home/voyager/Downloads/ml/ann/ffnn/Microsoft Word - Feedforwardnets-note.DOC - ffnets-note.pdf`
- `/home/voyager/Downloads/ml/ann/ffnn/IN3050 Lecture 7_ feedforward neural networks - in3050_lecture_07_ffnn_2025.pdf`
- `/home/voyager/Downloads/ml/ann/ffnn/4-nn2-perceptron - 7-nn2-perceptron.pdf`
- `/home/voyager/Downloads/ml/ann/ffnn/chapter4v2.pdf`
- `/home/voyager/Downloads/ml/ann/ffnn/lec21.notes.pdf`
- `/home/voyager/Downloads/ml/ann/ffnn/ff.pdf`
- `/home/voyager/Downloads/ml/ann/ffnn/03-4-5.feedforward-nets.pdf`

### How to view files

**View the notes I have generated - [[Intro_to_ML]], [[Bayesian_Learning]], [[Neural_Networks_Basics]], [[Multilayer_Backpropagation]], [[Feedforward_NN_NLP]], [[Advanced_NN_Architectures]]**