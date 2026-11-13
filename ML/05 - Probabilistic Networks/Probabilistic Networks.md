# Chapter 5. Probabilistic Neural Networks

[Contents](../readme.md)

## 5.1 Hopfield networks and associative recall

A Hopfield network stores memories in symmetric weights. Use bipolar units $s_i\in\{-1,1\}$ and zero self-connections. For stored patterns $p^{(\mu)}$, the unscaled Hebbian rule is $w_{ij}=\sum_\mu p_i^{(\mu)}p_j^{(\mu)}$ for $i\ne j$. During recall, initialize the state with the noisy input and update one unit at a time. The local field is $h_i=\sum_jw_{ij}s_j+b_i$. Set the bit to the sign of the field and keep the old bit on a tie.

With symmetric weights and $w_{ii}=0$, the terms in $E=-\frac12s^TWs-b^Ts$ that depend on bit $i$ sum to $-s_i h_i$. Its field stays fixed while that bit changes, so

$$
\Delta E=-(s_i'-s_i)h_i.
$$

A flip changes $s_i$ from the opposite sign of $h_i$ to its sign, giving $\Delta E=-2|h_i|$. With fair asynchronous updates and ties left unchanged, recall reaches a fixed point. Interfering memories can produce a spurious fixed point.

**Worked Hopfield recall.** Store $(1,1,-1,1,-1)$ and start from $(1,-1,-1,1,-1)$. The second neuron's weights from the other four units are $(1,-1,1,-1)$. Their weighted contributions are all $+1$, so the field is $4$ and the second bit flips to $+1$. The network finds the unstable bit through its field. It only needs the current pattern and the learned weights at recall.

Discrete Hopfield units have binary or bipolar states, depending on the formulation. Continuous Hopfield models use continuous activations. The single-bit energy argument above applies to discrete asynchronous updates. Recall capacity is limited because stored patterns can interfere.

### Interference and memory capacity

Each Hebbian outer product favors states that align with its stored pattern. When several patterns contribute to the same weights, their effects add. For two bipolar patterns, their dot product measures overlap. Orthogonal patterns have zero dot product, while highly overlapping patterns can introduce cross-talk. The network can converge to a mixture or another spurious minimum. For a recall question, check the actual fields and the update order instead of assuming that the closest stored pattern will always win.

## 5.2 Boltzmann machines and restricted Boltzmann machines

Use binary $0/1$ units for this section. A Boltzmann machine assigns probability proportional to $e^{-E(s)/T}$, where $T>0$ is the temperature and $E$ is the energy for the binary state. A unit with field $h_i$ turns on with conditional probability $\sigma(h_i/T)$. To sample, draw $r$ uniformly from $[0,1)$ and turn the unit on when $r<p$. If $p=0.85$, $r=0.72$ gives 1 and $r=0.95$ gives 0. A larger field makes 1 more likely. Clamping the known bits and sampling the rest can complete a pattern.

Temperature controls exploration. A high temperature makes the two binary states closer to equally likely. A low temperature favors the lower-energy choice. Simulated annealing starts with more random transitions and cools gradually to search for low-energy states. The outcome can still depend on the schedule and the energy landscape.

| Model | Connections and update | What it gives you |
|---|---|---|
| Discrete Hopfield network | Symmetric recurrent weights and deterministic asynchronous updates | Retrieval of a stable pattern from partial or noisy input |
| Full Boltzmann machine | Symmetric connections among units, with stochastic updates and optional hidden units | A distribution over patterns with hidden units that can model dependencies |
| Restricted Boltzmann machine | Visible-to-hidden connections only | Conditional independence within each layer and efficient alternating Gibbs steps |

For binary units at unit temperature, the RBM energy is $E(v,h)=-a^Tv-b^Th-v^TWh$. Summing the joint distribution over $h$ gives the visible distribution. The log-likelihood gradient for $W$ is the data-conditioned mean of $vh^T$ minus its mean under the model. The first term favors training patterns. The second term offsets patterns the model already generates. In a fully connected Boltzmann machine, both phases can require slow sampling. The RBM structure speeds up each Gibbs step, and contrastive divergence approximates the equilibrium phase with a short chain.

An **RBM** has visible units $v$ and hidden units $h$ with connections only across the layers. Given one layer, the other layer's units can be sampled independently. At unit temperature, with visible bias $a$, hidden bias $b$, and $W$ shaped visible by hidden, use

$$
P(h_j=1\mid v)=\sigma(b_j+(W^Tv)_j),\qquad
P(v_i=1\mid h)=\sigma(a_i+(Wh)_i).
$$

**Worked CD-1 update.** Use two visible bits and one hidden bit, weights $(1,1)$, zero biases, and training pattern $v^0=(1,0)$.

1. Compute $P(h^0=1\mid v^0)=\sigma(1)\approx0.731$. A draw of $0.6$ gives $h^0=1$.
2. Both visible probabilities are now $\sigma(1)$. Draws $0.3,0.4$ give a reconstruction $v^1=(1,1)$.
3. Compute $P(h^1=1\mid v^1)=\sigma(2)\approx0.881$. A draw of $0.8$ gives $h^1=1$.
4. For the second weight, $v_2^0h^0-v_2^1h^1=0-1=-1$. A sampled CD-1 update with $\eta=0.1$ takes $w_2$ from 1 to $0.9$. This reduces the model's tendency to activate the second visible bit with that hidden state.

The two products record whether the visible and hidden units are active together in each sampled pair. The data pair contributes positively and the reconstructed pair negatively. CD-1 uses the reconstruction to approximate the equilibrium model term. This example samples both hidden states. Some implementations use hidden activation probabilities to estimate the corresponding averages. [Hinton’s RBM training guide](https://www.cs.toronto.edu/~hinton/absps/guideTR.pdf)

## 5.3 Sigmoid networks and autoencoders

With the same forward-pass notation as Chapter 4, a feedforward sigmoid network applies $\sigma(Wx+b)$ layer by layer. In this example, the hidden outputs are $(0.8176,0.2689)$ and $\hat y=\sigma(2(0.8176)-0.2689-0.5)\approx0.7040$. With target 1 and half-squared error, $\delta_o\approx-0.0617$. At $\eta=0.1$, the first output weight rises from 2 to about $2.0050$.

An **autoencoder** learns to reproduce its input. The encoder computes $z=f_\theta(x)$ and the decoder computes $\hat x=g_\phi(z)$. Both sets of parameters receive gradients from a reconstruction loss. For real-valued outputs, squared error is common. Binary cross-entropy can be used for Bernoulli targets with sigmoid outputs.

**Worked reconstruction.** For input $x=(1,0,1,0)$, encoder pre-activations $(1,0.6)$ give $z\approx(0.7311,0.6457)$. Apply sigmoid to each decoder score.

| Output | Decoder score | Activation |
|---|---|---:|
| 1 | $0.8z_1+0.2z_2+0.1$ | $0.6930$ |
| 2 | $0.1z_1+0.7z_2-0.1$ | $0.6047$ |
| 3 | $0.6z_1+0.4z_2$ | $0.6675$ |
| 4 | $0.2z_1+0.9z_2-0.2$ | $0.6289$ |

Subtract each target from its reconstruction, square the differences, and average.

$$
\operatorname{MSE}\approx\frac{(-0.3070)^2+0.6047^2+(-0.3325)^2+0.6289^2}{4}\approx0.2415.
$$

The decoder receives the reconstruction gradient first. The encoder receives it through the decoder and the latent code.

| Variant | Constraint |
|---|---|
| Undercomplete | Fewer latent coordinates than input coordinates |
| Sparse | Penalty encourages few active latent units |
| Denoising | Encode a corrupted input and reconstruct the clean input |
| Contractive | Penalize sensitivity of the encoder to small input changes |

Denoising reconstructs the clean input from a corrupted version, such as a vector with randomly masked entries. A contractive autoencoder instead adds $\lambda\|\partial f_\theta(x)/\partial x\|_F^2$, penalizing changes in the code under small changes in the input. A sparse autoencoder can add a penalty on latent activations. These constraints have different effects even if the same reconstruction loss is used.

### What the latent space preserves

An undercomplete linear autoencoder with squared reconstruction error can learn the principal subspace spanned by PCA, although its learned coordinates need not be the PCA axes. Nonlinear encoders can learn curved low-dimensional structure. An overcomplete network can simply learn the identity unless the training objective constrains what it represents.

| Extension | What changes |
|---|---|
| Tied weights and stacked autoencoders | A decoder can use the transpose of the encoder weights. Stacking encoders allows progressively deeper representations, which can be fine-tuned jointly. |
| Stochastic encoder or decoder | The model produces a distribution instead of a single code or reconstruction. A variational autoencoder fits a probabilistic latent model with a reconstruction term and a KL term that regularizes the latent distribution. |
| Predictive sparse decomposition | Train an encoder to predict a sparse code that can reconstruct the input. |
| Compression, retrieval, and metric learning | Use codes for low-dimensional storage or similarity search. Reconstruction preserves useful variation, but labels or a similarity objective may be needed if semantic neighbors matter. |

## 5.4 Related neural architectures

The earlier architecture material also introduced these models. The Chapter 5 syllabus lists the models in §§5.1–5.3.

| Model | Mechanism and use |
|---|---|
| Backpropagation network (BPN) | A feedforward network trained by the gradient procedure in Chapter 4 §4.2. |
| Counterpropagation network (CPN) | A competitive Kohonen layer selects a prototype. A supervised Grossberg layer maps that winner to a target. Too few prototypes give a coarse approximation. |
| Bidirectional associative memory (BAM) | Recalls paired patterns across two layers. With $W$ mapping $X$ to $Y$, alternate $y\gets\operatorname{sign}(Wx)$ and $x\gets\operatorname{sign}(W^Ty)$ until the pair settles. |
| Adaptive resonance theory (ART) | Selects a category and checks its match against a vigilance threshold. A failed check triggers a reset and a search for another category. Higher vigilance produces finer categories. ART-1 uses binary inputs and ART-2 supports continuous inputs. |
| Self-organizing map (SOM) | Finds the closest prototype on a grid. It moves the winner and its neighbors toward the input, $w_j\gets w_j+\eta h_{bj}(x-w_j)$. Shrinking the neighborhood supports a map that preserves local relationships. |
| Learning vector quantization (LVQ) | Uses labeled prototypes for classification. Move the closest prototype toward the input for a correct label and away from it for a wrong label. |
| Cascade-correlation network | Starts with input and output units. It adds a hidden unit trained to correlate with residual errors, freezes its incoming weights, then refits the output weights. |
| Probabilistic neural network (PNN) | Uses Gaussian responses around training examples to estimate class densities. Combine the class density with its prior to choose a label. This is a specific classifier within the broader family of neural models. |
| General regression neural network (GRNN) | Predicts a Gaussian-weighted average of observed targets. Nearby training points carry more weight, and validation can select the kernel width. |

### Applications of the related architectures

BAM recalls paired patterns, such as a noisy code and its associated symbol. Competitive models such as SOM and ART group similar vectors, while LVQ attaches labels to prototypes. In character recognition, a bitmap can be flattened into a binary vector. The model compares or transforms that vector and returns a class code. The reference compilation illustrates this workflow.

## Practice

- **5A.** Store $(1,-1,1)$ with unscaled Hebbian weights. Starting from $(1,1,1)$, update neuron 2 and compute the energy change.
- **5B.** Distinguish what gets updated during Hopfield recall, an RBM Gibbs step, and autoencoder training.
- **5C.** In the CD-1 example, change the second visible draw from $0.4$ to $0.9$. With the same hidden draws, find the new reconstruction and second-weight update.

## Answer checks

| Exercise | Answer |
|---|---|
| 5A | The field is $-2$, so the bit flips from $+1$ to $-1$. $\Delta E=-(-2)(-2)=-4$. |
| 5B | Hopfield recall changes states deterministically. An RBM Gibbs step samples states. Autoencoder training changes weights by backpropagating reconstruction error. |
| 5C | The reconstruction becomes $(1,0)$. The final hidden draw $0.8$ gives 0 since $\sigma(1)\approx0.731$. Both second-bit products are zero, so the sampled update is zero. |
[Chapter sources](../readme.md#5-probabilistic-neural-networks)

---

[← Graphical Models](../04%20-%20Feedforward%20and%20Graphical%20Models/Graphical%20Models/Graphical%20Models.md) · [Contents](../readme.md) · [Chapter 6 →](../06%20-%20Deep%20Learning/Deep%20Learning.md)
