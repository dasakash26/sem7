# Machine Learning CT2 Guide

Prepare **Modules 5, 6 and 7**, following your stated CT2 scope. The attached lecture summary maps these to **Lectures 15–29**. Module 8 and Lecture 30 concern reinforcement learning and fall outside this guide.

The saved teacher chats repeatedly ask for **functionality, simulation, weight learning and interpretation**. Prepare each method by explaining its purpose, drawing its structure, writing its equations, doing a complete numerical example and interpreting the result. The priorities below reflect that emphasis; they are study recommendations, not a prediction of the paper.

## Contents

- [How to use the chapter notes](#using-the-chapter-notes-for-ct2)
- [Module 5 Probabilistic Neural Networks](#module-5-probabilistic-neural-networks)
- [Module 6 Deep Neural Networks](#module-6-deep-neural-networks)
- [Module 7 Classification and Clustering](#module-7-classification-and-clustering)
- [Study order and priorities](#study-order-and-priorities)
- [Readiness checklist](#closed-book-readiness-check)
- [Practice paper and selected answers](#practice-paper)
- [Writing an exam answer](#writing-an-exam-answer)
- [Sources and scope](#sources-and-scope)

**Study files:** [18-hour plan](plan.md) · [Teacher chat index](References/Teacher%20Chat%20Index.md) · [Semester practice](Semester%20Practice.md)

## Using the chapter notes for CT2

The chapter notes now include the worked calculations needed for the supplied outline:

| Chapter | Core sections and practice |
|---|---|
| Module 5 | §§5.1–5.3: full Hopfield matrix/recall, BM probability table and learning, RBM CD-1, sigmoid forward pass and autoencoder gradient; exercises 5A–5E |
| Module 6 | §§6.1–6.4: perspectives/issues, complete GD epochs, optimizers/regularization, CNN pipeline, RNN context and recursive composition; exercises 6A–6F |
| Module 7 | §§7.1–7.6: logistic learning, Fisher derivation, linear SVM and XOR KKT, kernel prediction, KNN, K-means and decision trees; exercises 7A–7F |

Use these as your main CT2 notes. The teacher transcripts explain difficult steps; the broader PDF material can extend a topic after you can solve its core example. Test preparation by solving the chapter questions without the answer checks, rather than by rereading alone.

## Module 5 Probabilistic Neural Networks

### Taught topic outline

#### Lectures 15 and 16 Hopfield nets

**Topics:** Associative memory; recurrent topology; symmetric weights; zero diagonal; bipolar states; Hebbian storage; local fields; asynchronous updates; energy and attractors.

**Practise:** Construct a stored pattern's weight matrix; corrupt one or two bits; update in a stated order; calculate energy change.

#### Lecture 17 Boltzmann machines

**Topics:** Visible and hidden units; stochastic states; configuration energy; temperature; partition function; Boltzmann distribution; sigmoid conditional probability; sampling; correlation-based learning.

**Practise:** Enumerate a three-unit machine's eight states; calculate energies, probabilities and a sampled update; update a weight.

#### Lecture 18 Restricted Boltzmann machines

**Topics:** Bipartite structure; conditional independence; hidden representation; reconstruction; positive/negative phases; contrastive divergence.

**Practise:** Trace two-visible/one-hidden and four-visible/two-hidden RBMs; compute a correlation update.

#### Lecture 19 Autoencoders

**Topics:** Encoder; latent code; decoder; reconstruction loss; backpropagation; compression, denoising and anomaly detection.

**Practise:** Four-input/two-code forward pass, four reconstructed outputs and MSE.

#### Lecture 20 Sigmoid nets

**Topics:** Sigmoid properties; soft activation versus hard threshold; differentiability; layered forward computation.

**Practise:** Two hidden activations and the final output of a 2–2–1 network.

### Questions emphasized in the teacher chats

1. Where does a Hopfield network store a pattern? Why does a neuron flip? How can it recover noisy data without a separate wrong-bit detector?
2. How does a Boltzmann machine turn energy into probability? Why do random draws change the sampled state? How are weights learned?
3. How does an RBM create a hidden representation and reconstruct the visible pattern? What happens when reconstruction differs from the input?
4. How are encoder and decoder weights learned? How does the latent code retain information needed for reconstruction?
5. How does a sigmoid net transform the input layer by layer?

### Essential equations

#### Hopfield networks

For Hopfield states $s_i\in\{-1,+1\}$, use unscaled Hebbian weights unless the question specifies normalization:

$$
\begin{aligned}
w_{ij} &= \sum_\mu s_i^{(\mu)}s_j^{(\mu)}, && i\ne j,\\
w_{ii} &= 0,\\
h_i &= \sum_{j\ne i}w_{ij}s_j+b_i.
\end{aligned}
$$

Set $s_i'=\operatorname{sign}(h_i)$, retaining the old state on a zero field unless instructed otherwise. For symmetric weights and an asynchronous update:

$$
\begin{aligned}
E &= -\frac12s^TWs-b^Ts,\\
\Delta E &= -(s_i'-s_i)h_i.
\end{aligned}
$$

Recall changes **states**, keeping learned weights fixed. A Hopfield network can reach an unwanted attractor; recovery of the nearest stored pattern is not guaranteed.

#### Boltzmann machines

For binary $0/1$ Boltzmann units:

$$
\begin{aligned}
E(x) &= -\sum_{i<j}w_{ij}x_ix_j-\sum_i b_ix_i,\\
P(x) &= \frac{e^{-E(x)/T}}{Z},\\
Z &= \sum_x e^{-E(x)/T}.
\end{aligned}
$$

$$
P(x_i=1\mid x_{-i})=\sigma(h_i/T),\qquad \sigma(z)=\frac1{1+e^{-z}}.
$$

Draw $r$ uniformly from $[0,1)$ and set the state to 1 when $r<p$. For $p=0.881$, draws 0.72 and 0.95 give states 1 and 0 respectively. Do not replace sampling with a deterministic 0.5 threshold.

$$
\Delta w_{ij}=\eta\left(\langle x_ix_j\rangle_{\rm data}-\langle x_ix_j\rangle_{\rm model}\right).
$$

#### Restricted Boltzmann machines

For an RBM at unit temperature, with $W$ shaped visible by hidden:

$$
\begin{aligned}
P(h_j=1\mid v) &= \sigma\left(b_j+\sum_iW_{ij}v_i\right),\\
P(v_i=1\mid h) &= \sigma\left(a_i+\sum_jW_{ij}h_j\right).
\end{aligned}
$$

Trace $v^0\to h^0\to v^1\to h^1$ before updating. A sampled CD-1 update is $\Delta W=\eta[v^0(h^0)^T-v^1(h^1)^T]$. State whether sampled states or probabilities estimate the correlations. CD-1 approximates the model expectation. An RBM need not have fewer hidden units than visible units; the smaller hidden layer is the compression example used here.

#### Autoencoders

For an autoencoder:

$$
\begin{aligned}
z &= f_\theta(x),\\
\hat x &= g_\phi(z),\\
\operatorname{MSE} &= \frac1d\sum_i(x_i-\hat x_i)^2.
\end{aligned}
$$

Both encoder and decoder learn through reconstruction gradients. A standard sigmoid-network activation is a numerical output; sampling a binary state from it is a separate operation.

### Examples to reproduce

- Store $(1,1,-1,1,-1)$. For noisy input $(1,-1,-1,1,-1)$, neuron 2 has field $+4$, flips to $+1$ and changes energy by $-8$ with unscaled weights.
- For a three-unit BM with $w_{12}=2,w_{13}=-1,w_{23}=-1$, zero biases and $T=1$, state 110 has energy $-2$. Enumerating all eight states gives $Z=5+2e^{-1}+e^2\approx13.1248$ and $P(110)\approx0.5630$.
- For RBM weights $(0.5,0.5)$, zero biases and input $(1,1)$, $P(h=1)=\sigma(1)=0.7311$. If $h=1$ is sampled, each reconstructed visible probability is $\sigma(0.5)=0.6225$.
- In the teacher's sigmoid-net example, hidden outputs are approximately $(0.8176,0.2689)$ and final output is $0.7040$.
- Reproduce the autoencoder latent vector $(0.7311,0.6457)$ and the worked MSE of approximately $0.2415$ in the Module 5 notes.

**Further explanations:**

- [Hopfield weights chat](References/Teacher%20Chats/Hopfield%20-%20Weights.md)
- [Boltzmann sampling chat](References/Teacher%20Chats/Boltzmann%20-%20Sampling.md)
- [RBM chat](References/Teacher%20Chats/RBM%20-%20Reconstruction%20and%20Training.md)
- [Autoencoder chat](References/Teacher%20Chats/Autoencoders.md)
- [Sigmoid-net chat](References/Teacher%20Chats/Sigmoid%20Networks.md)

## Module 6 Deep Neural Networks

### Taught topic outline

#### Lecture 21 Deep networks

**Topics:** Deep feedforward architecture; hierarchical features; representation learning; optimization/compute perspectives; applications and transfer learning; data, bias, cost and overfitting issues.

**Practise:** Dataset split; epochs, batch size and update count; loss curves.

#### Lecture 22 Optimization and regularization

**Topics:** Gradients; learning rate; BGD, SGD and mini-batch GD; momentum and Adam; underfitting/overfitting; L1, L2, elastic net; dropout; early stopping; augmentation.

**Practise:** Sequential updates, penalties, dropout scaling and stopping decisions.

#### Lecture 23 CNN

**Topics:** Pixel matrices; filters; sliding operation; stride; feature maps; ReLU, sigmoid and tanh; max/average/min pooling; flattening; dense scores and softmax; learned filters.

**Practise:** Full convolution-to-classification pipeline and output dimensions.

#### Lecture 24 Recurrent and recursive nets

**Topics:** Hidden state; unrolling; shared weights; sequence order; one-hot inputs; long dependencies; bidirectional RNN; BPTT; distinction from tree recursion.

**Practise:** Hidden-state updates; same word in different contexts; next-word probabilities.

### Optimization and training

#### Gradient descent and batches

For $\ell_i=\frac12(wx_i-y_i)^2$:

$$
g_i=(wx_i-y_i)x_i,\qquad w_{\rm new}=w_{\rm old}-\eta g.
$$

BGD averages over the whole training set before one update. SGD updates after each example. Mini-batch GD averages over the current batch. **Recalculate gradients after every update.** If the final partial batch is kept, updates per epoch are $\lceil N/B\rceil$. An epoch is one pass over the training set; it is distinct from an update.

For $(x,y)=(1,2),(2,4),(3,6)$, $w_0=0$, $\eta=0.1$, using mean batch losses:

| Method | Weight after one epoch |
|---|---|
| BGD | $0.9333$ |
| SGD in the listed order | $0\to0.2\to0.92\to1.892$ |
| Mini-batches of sizes 2 then 1 | $0\to0.5\to1.85$ |

#### Momentum and Adam

One momentum convention is $u_t=\beta u_{t-1}+g_t$, then $w_t=w_{t-1}-\eta u_t$. For Adam, know the first/second moment estimates, bias correction and scaled update:

$$
\begin{aligned}
m_t &= \beta_1m_{t-1}+(1-\beta_1)g_t,\\
v_t &= \beta_2v_{t-1}+(1-\beta_2)g_t^2.
\end{aligned}
$$

$$
\begin{aligned}
\hat m_t &= \frac{m_t}{1-\beta_1^t},\\
\hat v_t &= \frac{v_t}{1-\beta_2^t},\\
w_t &= w_{t-1}-\eta\frac{\hat m_t}{\sqrt{\hat v_t}+\epsilon}.
\end{aligned}
$$

Products, squares and division are coordinatewise. Master BGD/SGD numericals before spending time on optimizer variants.

### Regularization

#### L1 and L2 penalties

With the convention used here:

$$
\begin{aligned}
L_{\rm L1} &= L_{\rm data}+\lambda\sum_i|w_i|,\\
L_{\rm L2} &= L_{\rm data}+\frac\lambda2\sum_iw_i^2.
\end{aligned}
$$

L1 encourages sparse weights; L2 shrinks weights. For $(2,-1,0.5)$ and $\lambda=0.1$, penalties are $0.35$ and $0.2625$. Elastic net combines the penalties.

#### Dropout

For inverted dropout with drop probability $p$, use $\tilde a=m\odot a/(1-p)$ during training. With $a=(0.8,0.4,0.9,0.2)$, mask $(1,0,1,0)$ and $p=0.5$, the result is $(1.6,0,1.8,0)$. Inference uses the unmasked activations without this extra scaling.

#### Early stopping and loss curves

Validation losses $0.70,0.60,0.50,0.52,0.57$ identify epoch 3 as the best checkpoint. With patience 2 and checks each epoch, stop after epoch 5 and restore epoch 3. Validation selects the model; test data evaluate the selected model. Explain underfitting, overfitting and label-preserving augmentation in words as well as calculations.

### CNN calculations

#### Output dimensions

For one spatial dimension with unit dilation:

$$
N_{\rm out}=\left\lfloor\frac{N+2P-K}{S}\right\rfloor+1.
$$

State stride and padding for both convolution and pooling. A $5\times5$ image with a $3\times3$ filter, stride 1 and no padding gives a $3\times3$ map. A following $2\times2$ pool gives $1\times1$ at stride 2, or $2\times2$ at stride 1, without padding.

#### Convolution example

For the teacher-chat image and filter:

$$
I=\begin{bmatrix}1&1&1&0&0\\1&1&1&0&0\\1&1&1&0&0\\0&0&0&1&1\\0&0&0&1&1\end{bmatrix}
$$

$$
K=\begin{bmatrix}1&0&-1\\1&0&-1\\1&0&-1\end{bmatrix},
$$

sliding the filter as written at stride 1, with zero bias and no padding, gives

$$
F=\begin{bmatrix}0&3&3\\0&1&1\\0&-1&-1\end{bmatrix}.
$$

#### Classification pipeline

Apply ReLU, pool, flatten in a stated order, calculate dense-layer logits, then softmax: $p_k=e^{z_k}/\sum_je^{z_j}$. Scores $(2,1)$ give probabilities $(0.7311,0.2689)$. Filters learn by backpropagation; pooling and flattening have no learned weights. Flattening changes shape without discarding entries.

### RNN calculations and recursive networks

$$
h_t=\tanh(W_xx_t+W_hh_{t-1}+b_h),\qquad y_t=g(W_yh_t+b_y).
$$

Use the saved chat's consistent toy example: $h_t=\tanh(x_t+0.8h_{t-1})$, $P(+)=\sigma(2h_t)$, “not” $=-1$, “good” $=0.3$, and $h_0=0$. “Good” alone gives $h\approx0.2913$, $P(+)\approx0.6417$; “not good” gives final $h\approx-0.2998$, $P(+)\approx0.3544$. The attachment's value of 0.8 for “good” does not reproduce those outputs.

Time steps reuse the recurrent weights; their number is distinct from the number of stacked recurrent layers. In general, $W_h$ is a learned matrix, not necessarily a scalar retention percentage. BPTT propagates gradients through the unrolled graph. Softmax vocabulary scores are learned from training sequences; the model has no built-in list of words that must follow “a cup of”.

A recursive network combines child representations in a tree, for example $h_{\rm parent}=\tanh(W[h_{\rm left};h_{\rm right}]+b)$. Explain this distinction from a recurrent network's sequence of time steps.

**Further explanations:**

- [training chat](References/Teacher%20Chats/Deep%20Learning%20-%20Training.md)
- [CNN chat](References/Teacher%20Chats/CNN%20-%20Simulation.md)
- [RNN chat](References/Teacher%20Chats/RNN%20-%20Simulation.md)

## Module 7 Classification and Clustering

### Taught topic outline

#### Lecture 25 Linear and logistic models

**Topics:** Linear scores and hyperplanes; separability; numeric regression versus binary probability; sigmoid; log loss; learning by GD.

**Practise:** Scores, probabilities, losses, weight/bias update and boundary.

#### Lecture 26 LDA

**Topics:** Means; within-class/between-class scatter; Fisher criterion; optimal projection and its derivation; threshold; multiclass extension.

**Practise:** Outer-product scatter matrices, inverse, projection and classification.

#### Lecture 27 Linear SVM

**Topics:** Maximum margin; support vectors; canonical planes; hard-margin objective; margin derivation; introduction to slack.

**Practise:** Solve margin equations for weights/bias; width and classification.

#### Lecture 28 Nonlinear SVM

**Topics:** Soft margin and C; feature maps; XOR/concentric data; kernel trick; linear, polynomial, RBF and sigmoid kernels; dual and KKT.

**Practise:** Mapped XOR constraints, parameters and multipliers; kernel score.

#### Lecture 29 KNN, K-means and decision trees

**Topics:** Distances; voting and K; centroid assignment/update and WCSS; entropy, information gain, gain ratio, Gini, pruning.

**Practise:** Sort neighbors; iterate clustering; compare split gains and trace predictions.

### Logistic regression

$$
\begin{aligned}
z &= w^Tx+b,\\
p &= \sigma(z),\\
\ell &= -[y\ln p+(1-y)\ln(1-p)].
\end{aligned}
$$

For one example, $\nabla_w\ell=(p-y)x$ and $\partial\ell/\partial b=p-y$. At threshold 0.5, the boundary is $w^Tx+b=0$. The teacher's example $z=2x-7$ has boundary $x=3.5$.

For $(y,p)=(1,0.9),(0,0.2),(1,0.3)$, losses are approximately $0.1054,0.2231,1.2040$, with mean $0.5108$. Use natural logs for this loss. Linear regression predicts an unrestricted real value; logistic regression estimates a binary class probability.

### LDA

Use $S_A,S_B$ for the two class scatter matrices and $S_{\rm between}$ for between-class scatter, to avoid using $S_B$ for two different objects.

$$
\begin{aligned}
\mu_k &= \frac1{n_k}\sum_{x\in k}x,\\
S_k &= \sum_{x\in k}(x-\mu_k)(x-\mu_k)^T,\\
S_W &= S_A+S_B.
\end{aligned}
$$

For two classes, write $d=\mu_B-\mu_A$. A proportional form of between-class scatter is $dd^T$, giving

$$
J(w)=\frac{(w^Td)^2}{w^TS_Ww},\qquad w\propto S_W^{-1}d.
$$

Prepare the derivation by maximizing $(w^Td)^2$ subject to $w^TS_Ww=1$. Stationarity makes $S_Ww$ proportional to $d$. For multiclass LDA, remember the generalized eigenvector formulation and the limit of $C-1$ discriminant directions.

For $A=\{(1,1),(2,1),(1,2)\}$ and $B=\{(4,4),(5,4),(4,5)\}$:

$$
\begin{aligned}
\mu_A &= (4/3,4/3),\\
\mu_B &= (13/3,13/3),\\
S_A=S_B &= \begin{bmatrix}2/3&-1/3\\-1/3&2/3\end{bmatrix}.
\end{aligned}
$$

Then $S_W^{-1}d=(4.5,4.5)^T$. Use the equivalent direction $(1,1)$, project with $z=x_1+x_2$ and choose midpoint $17/3\approx5.667$ under this example's equal-prior/equal-variance setting. Rescaling the direction requires rescaling the threshold too.

### SVM

#### Hard margin and soft margin

With $y_i\in\{-1,+1\}$:

$$
\min_{w,b}\frac12\|w\|^2\quad\text{subject to }y_i(w^Tx_i+b)\ge1.
$$

The boundary is $w^Tx+b=0$; the canonical margin planes are $w^Tx+b=\pm1$. Each is distance $1/\|w\|$ from the center, giving total width $2/\|w\|$.

For soft margin, minimize $\frac12\|w\|^2+C\sum_i\xi_i$ subject to $y_if(x_i)\ge1-\xi_i$ and $\xi_i\ge0$. Correctly classified points can still lie inside the margin. Larger C makes violations more costly. A kernel or nonlinear feature map allows a nonlinear boundary in the original input space.

#### Linear SVM example

For the fruit example, $w=(0.4,0.4)$, $b=-2.2$, boundary $x_1+x_2=5.5$ and total width $5/\sqrt2\approx3.5355$. The LDA threshold on the same point set differs because its objective differs.

#### Kernel decision function

$$
\begin{aligned}
K(x,z) &= \phi(x)^T\phi(z),\\
K_{\rm RBF}(x,z) &= e^{-\gamma\|x-z\|^2},\\
f(x) &= \sum_i\alpha_i y_iK(x_i,x)+b.
\end{aligned}
$$

Predict by the sign of $f(x)$. The dual maximizes $\sum_i\alpha_i-\frac12\sum_{ij}\alpha_i\alpha_jy_iy_jK(x_i,x_j)$ with $\alpha_i\ge0$, $\sum_i\alpha_iy_i=0$; soft margin also requires $\alpha_i\le C$.

#### Mapped XOR and KKT

**Prepare the mapped XOR derivation.** For points $(0,0),(0,1),(1,0),(1,1)$ with labels $(-1,+1,+1,-1)$, map to $(x_1,x_2,x_1x_2)$. The solution is $w=(2,2,-4)$, $b=-1$, scores $(-1,+1,+1,-1)$, multipliers $(8,6,6,4)$ in that order, and feature-space margin $2/\sqrt{24}\approx0.4082$.

Use the hard-margin Lagrangian $L=\frac12\|w\|^2+\sum_i\alpha_i[1-y_i(w^T\phi(x_i)+b)]$. KKT requires stationarity, primal feasibility, nonnegative multipliers and $\alpha_i[1-y_if(x_i)]=0$. **Correction to the saved kernel chat:** the first XOR constraint contributes $\alpha_1(1+b)$, not $\alpha_1(b-1)$. The final reported parameters satisfy the corrected conditions.

### KNN and K-means

#### K nearest neighbors

KNN is supervised: calculate distances to labeled points, sort, choose K neighbors and vote. Euclidean distance is $\sqrt{\sum_j(x_j-z_j)^2}$; Manhattan is $\sum_j|x_j-z_j|$; Minkowski is $(\sum_j|x_j-z_j|^p)^{1/p}$ for $p\ge1$; Hamming counts mismatched positions. Scale numeric features when units differ. Odd K avoids a binary unweighted voting tie but does not prevent every multiclass tie.

#### K means clustering

K-means is unsupervised. Initialize K centers, assign each point to its nearest center, recompute each center as the assigned points' mean and repeat until assignments stabilize or a stopping condition is met.

$$
\mu_k=\frac1{|C_k|}\sum_{x\in C_k}x,\qquad
\operatorname{WCSS}=\sum_k\sum_{x\in C_k}\|x-\mu_k\|^2.
$$

Practise at least two iterations and calculate final WCSS. Initialization and scaling affect the result. K means number of neighbors in KNN and number of clusters in K-means.

### Decision trees

$$
H(S)=-\sum_cp_c\log_2p_c,\qquad
IG(S,A)=H(S)-\sum_v\frac{|S_v|}{|S|}H(S_v).
$$

For ID3, calculate parent entropy, weighted child entropy for each candidate and information gain. Choose the highest gain, then repeat on mixed branches. Seven Yes and three No labels give entropy $0.8813$ bits; attribute-level counts are needed to calculate a split.

Also know Gini $=1-\sum_cp_c^2$, split information $=-\sum_v(|S_v|/|S|)\log_2(|S_v|/|S|)$ and gain ratio $=IG/\text{split information}$ when its denominator is nonzero. Explain depth limits/pruning and trace a new example from root to leaf.

**Further explanations:**

- [logistic chat](References/Teacher%20Chats/Logistic%20Regression.md)
- [LDA chat](References/Teacher%20Chats/LDA%20-%20Projection.md)
- [SVM margin chat](References/Teacher%20Chats/SVM%20-%20Margin.md)
- [kernel chat](References/Teacher%20Chats/SVM%20-%20Kernels.md)
- [DM classification notes](../DM/05_Classification_and_Prediction/Classification_and_Prediction.md)
- [DM clustering notes](../DM/06_Clustering_and_Outlier_Analysis/Clustering_and_Outlier_Analysis.md)

## Study order and priorities

Follow the [18-hour CT2 plan](plan.md). Complete a full example for every method before adding broader slide topics. Spend extra practice time on Hopfield recall, BM/RBM sampling and learning, GD variants, CNN operations, LDA scatter calculations and SVM derivations because the saved chats develop these in detail. Still complete KNN, K-means and tree numericals from Lecture 29.

After the core examples, revise momentum/Adam equations, multiclass LDA, bidirectional RNN and kernel types. Broader PDF topics such as GANs, named CNN architectures, ART, SOM and GRNN have less support in the supplied Lecture 15–29 outline; study those after the stated topics unless separately assigned.

### Six hour revision schedule

Use this schedule after prior study.

| Time | Revision block |
|---:|---|
| 90 minutes | Module 5 |
| 90 minutes | Training, CNN and RNN |
| 90 minutes | Logistic regression, LDA and SVM |
| 45 minutes | KNN, K-means and decision trees |
| 45 minutes | Closed-book recall |

## Closed book readiness check

### Module 5

- [ ] Build a Hopfield matrix, recall a pattern and calculate energy change.
- [ ] Normalize BM probabilities and sample a neuron using a random draw.
- [ ] Trace an RBM reconstruction and explain both correlation terms in learning.
- [ ] Compute sigmoid-net activations and autoencoder reconstruction loss.

### Module 6

- [ ] Complete one epoch of BGD, SGD and mini-batch GD.
- [ ] Calculate penalties/dropout and select an early-stopping checkpoint.
- [ ] Compute convolution, ReLU, pooling, flattening, dimensions and softmax.
- [ ] Trace RNN states; explain BPTT, context and recursive versus recurrent nets.

### Module 7

- [ ] Calculate logistic loss, a gradient step and a decision boundary.
- [ ] Build LDA scatter matrices, project and classify.
- [ ] Derive SVM margin width and solve linear and mapped-XOR examples.
- [ ] Explain soft margin, kernels, dual coefficients and KKT.
- [ ] Solve KNN voting, K-means iterations and decision-tree root selection.

Use chapter exercises **5A–5C, 6A–6C, 7A–7C** and [Semester Practice sections E–G](Semester%20Practice.md). Add Lecture 29 practice because the older practice set gives those methods little coverage.

## Practice paper

This self-test follows the coverage; it is not a past paper or predicted CT2 format. Allow about 120–150 minutes for the full set, or select questions for a shorter timed session, then mark against the examples.

### Question 1 Hopfield recall

Store $(1,-1,1)$ with unscaled Hopfield weights. Start from $(1,1,1)$, update neuron 2 and find the energy change.

### Question 2 Boltzmann probabilities

Enumerate BM states for $(w_{12},w_{13},w_{23})=(2,-1,-1)$ and zero biases. Compute $P(110)$ at $T=1$. Sample at field +2 with draws 0.72 and 0.95.

### Question 3 RBM learning

Explain an RBM positive/reconstruction phase with a weight update, and contrast it with autoencoder training.

### Question 4 Gradient descent

For $(1,2),(2,4),(3,6)$, $w_0=0$, $\eta=0.1$ and half-squared loss, calculate one epoch of BGD and SGD.

### Question 5 CNN pipeline

Use the image/filter above. Find the feature map, ReLU output and a $2\times2$ max pool at stride 1. Flatten row by row.

### Question 6 RNN context

Compute “good” and “not good” using the specified RNN. Explain their different outputs.

### Question 7 Logistic regression and LDA

For $p=0.75,y=0$, calculate log loss. Reconstruct the full LDA example and threshold.

### Question 8 SVM derivations

Derive $2/\|w\|$, calculate the fruit-example boundary, and verify the mapped XOR parameters and multipliers using KKT.

### Question 9 KNN and K means

Classify $(1,1)$ using KNN with K=3, class A points $(0,0),(0,2),(2,0)$ and B points $(5,5),(6,5),(5,6)$. Run K-means with K=2 on those unlabeled points, starting at $(0,0),(5,5)$, until assignments stabilize.

### Question 10 Decision tree splits

Ten records have seven Yes and three No labels. Attribute A creates groups $(4Y,0N)$ and $(3Y,3N)$; B creates $(5Y,0N)$ and $(2Y,3N)$. Calculate both information gains and choose the ID3 root.

### Selected answers

| Question | Check |
|---|---|
| 1 | Field $-2$, flip to $-1$, energy change $-4$ |
| 2 | $Z\approx13.1248$, $P(110)\approx0.5630$; sampled states 1 and 0 |
| 4 | BGD $0.9333$ after one update; SGD $1.892$ after three |
| 5 | ReLU rows $(0,3,3),(0,1,1),(0,0,0)$; pooled rows $(3,3),(1,1)$; flattened $(3,3,1,1)$ |
| 6 | Final positive probabilities about $0.6417$ and $0.3544$ |
| 7 | Loss $1.3863$; LDA threshold $17/3$ for direction $(1,1)$ |
| 9 | KNN predicts A; centers $(2/3,2/3),(16/3,16/3)$; stable assignments; WCSS $20/3\approx6.6667$ |
| 10 | $IG(A)\approx0.2813$, $IG(B)\approx0.3958$ bits; choose B |

## Writing an exam answer

Give purpose and architecture, define variables, write the governing rule, show a numerical trace and interpret the result. In derivations, state the objective and assumptions before algebra. Retain intermediate precision and name the final class, state, epoch or updated parameter explicitly.

Check state encodings, bias signs, update order, temperature, averaging, log base, stride and padding before calculating. Those conventions explain many apparent disagreements between examples.

## Sources and scope

- [Official syllabus map](References/Syllabus.md).
- [Teacher chat index](References/Teacher%20Chat%20Index.md), with saved prompts and responses for Modules 5–7.
- The attached text beginning “The playlist Machine Learning Classes by Dr. Rohini Basak”. Lecture numbering and Lecture 29 coverage in this guide follow that supplied summary, rather than independent verification of every video.
- [Module 5 notes](05%20-%20Probabilistic%20Networks/Probabilistic%20Networks.md), [Module 6 notes](06%20-%20Deep%20Learning/Deep%20Learning.md) and [Module 7 notes](07%20-%20Classification/Classification.md) provide worked calculations.

**Include KNN, K-means and decision trees.** The official Module 7 list does not name these algorithms, but your attached summary explicitly places them in Lecture 29. The saved Module 7 chats focus on logistic regression, LDA and SVM. Recursive networks appear in the syllabus and lecture heading; the saved RNN chat mainly develops recurrent networks, so learn the distinction and a simple tree example without assuming a long recursive-network derivation was taught.
