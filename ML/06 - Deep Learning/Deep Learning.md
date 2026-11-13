# Chapter 6. Modern Practical Deep Networks

[Contents](../readme.md)

## 6.1 Architecture and training

The feedforward network from Chapter 4 becomes deep as it stacks more hidden layers. Each nonlinear layer transforms features produced by the previous layer. The same forward pass, loss, and backpropagation apply. Depth raises compute costs and can make gradients unstable. Batch normalization commonly uses batch statistics in training and running statistics at inference. Momentum smooths the gradient direction. Adam also tracks squared gradients to scale each parameter's step.

Deep models became practical as larger labeled datasets, faster parallel hardware, and improved optimization supported more layers and parameters. The same design choices create tradeoffs in training cost, interpretability, and the amount of data needed.

### Capacity, initialization, and normalization

| Choice | Why it matters |
|---|---|
| Shallow versus deep | A wide hidden layer can approximate many functions. Depth can represent hierarchies more efficiently when the task naturally composes small features, such as edges into shapes. |
| Xavier and He initialization | Scale weights using layer sizes to keep signals and gradients from shrinking or growing too quickly. Xavier suits many tanh-like layers, and He is designed for ReLU-like layers. |
| Batch normalization | Normalize batch activations, then apply a learned scale and shift. Inference usually uses statistics accumulated during training. This differs from scaling raw inputs once in preprocessing. |
| Hyperparameter search | Grid search tries a fixed grid, random search samples combinations, and validation determines the choice. Tune on validation data and reserve the test set for final evaluation. |

## 6.2 Batch size, loss curves, and regularization

An **epoch** is one pass through the training data. With 1,000 examples and batch size 64 there are 16 updates if the last partial batch is retained. Batch gradient descent uses all training examples for one update. SGD uses one example. Mini-batch descent uses a subset, trading gradient noise against computational efficiency.

**Worked update.** For $\ell_i=\frac12(wx_i-y_i)^2$, the gradient is $(wx_i-y_i)x_i$. At $w=0$, examples $(1,2),(2,2),(1,0)$ give gradients $-2,-4,0$. At learning rate $0.1$:

| Batch for the first update | Mean gradient | New $w$ |
|---|---:|---:|
| All three examples | $-2$ | $0.2$ |
| Second example alone | $-4$ | $0.4$ |
| First and second examples | $-3$ | $0.3$ |

The next update uses the new weights. The factor $\frac12$ cancels the 2 from differentiating a square.

| Method | Exam explanation |
|---|---|
| Early stopping | Select the checkpoint with lowest validation loss. If validation loss is $0.70,0.60,0.50,0.52,0.57$, restore epoch 3 |
| L1 penalty | $\lambda\sum_i\lvert w_i\rvert$. Encourages sparse weights |
| L2 penalty | $\frac{\lambda}{2}\sum_iw_i^2$. Adds $\lambda w_i$ to each weight gradient |
| Inverted dropout | Multiply activations by a random mask and divide by the keep probability during training. At test time, use the unmasked activations |
| Data augmentation | Apply training transformations that preserve the task's label |

For example, with keep probability $0.5$, activation vector $(2,4)$, and mask $(1,0)$, inverted dropout produces $(4,0)$ in that pass. Averaging over masks recovers the original activation in expectation.

### Optimizers and regularization choices

| Optimizer | What changes from plain gradient descent |
|---|---|
| Momentum | A moving average of gradients carries a direction across updates and can damp oscillation. |
| Nesterov momentum | Estimates the gradient at a look-ahead point along the momentum direction before correcting the update. |
| AdaGrad | Accumulated squared gradients shrink steps on frequently updated coordinates. Steps can become too small later in training. |
| RMSProp | A moving average of squared gradients scales each coordinate and gradually forgets old gradients. |
| Adam | Uses moving averages of gradients and squared gradients, with bias correction near initialization. |

Deep-network objectives are generally nonconvex. Initialization, stochastic gradients, and the learning-rate schedule affect training. A saddle can have a small gradient while directions of both ascent and descent still exist. Gradient clipping bounds a large gradient norm and is useful when training recurrent models.

For a gradient $g_t$, AdaGrad uses $s_t=s_{t-1}+g_t^2$, while RMSProp uses $s_t=\beta s_{t-1}+(1-\beta)g_t^2$. Both divide the coordinatewise gradient by $\sqrt{s_t}+\epsilon$. Adam uses a first-moment estimate in the numerator and a second-moment estimate in the denominator, with both moments corrected for their zero initialization.

| Additional slide topic | How to use it |
|---|---|
| Underfitting and overfitting | High training error suggests checking model capacity and optimization. A large validation gap suggests checking regularization, data quality, and the split. |
| Parameter shrinkage and max-norm | L1 and L2 add penalties. Max-norm constrains a weight vector to stay within a specified norm. |
| Dropout conventions | Inverted dropout scales retained activations during training. The original convention leaves training activations unscaled and multiplies them by the keep probability at inference. |
| Data augmentation | Crops, flips, shifts, color changes, or erasing can improve robustness when they preserve labels. Apply random augmentation to training data. |
| Transfer learning | Start from a pretrained representation, replace the task head, then choose which layers to fine-tune using validation. |

**Early stopping in practice.** Keep the checkpoint with the lowest validation loss. A patience setting controls how many successive checks without an improvement trigger the stop. Restore the saved checkpoint after stopping. Shared representations can also act as a constraint in multitask or semi-supervised learning, provided the auxiliary task or unlabeled data is relevant.

## 6.3 Convolutional networks

A convolutional layer slides learned filters across the input, sums elementwise products over all input channels, adds a bias per filter, and produces one feature map per filter. A nonlinearity such as ReLU follows. Pooling reduces spatial size. Flattening turns a stack of maps into a vector for a dense layer. Global average pooling is another possible readout.

**Worked filter.** Take a $3\times3$ image with every row $(1,0,1)$ and a $2\times2$ filter with both rows $(1,-1)$. With stride 1, no padding, and zero bias, the first patch scores $1+1=2$ and the next patch scores $-1-1=-2$. Both feature-map rows are $(2,-2)$. ReLU gives rows $(2,0)$ and a $2\times2$ max pool gives 2.

For input height $H$, padding $P$, kernel height $K_h$, and stride $S$, the output height with unit dilation is $\lfloor(H+2P-K_h)/S\rfloor+1$. Width uses the corresponding dimensions. A layer has $(K_hK_wC_{\mathrm{in}}+1)C_{\mathrm{out}}$ trainable parameters when each filter has a bias. For a $32\times32\times3$ image with 16 filters of size $3\times3$, stride 1 and padding 1, the convolution gives $32\times32\times16$. A $2\times2$ pool with stride 2 gives $16\times16\times16$. Flattening yields 4,096 values. The convolution learns $(3\cdot3\cdot3+1)16=448$ parameters because each filter is reused at every position.

Activation functions also affect the feature map. ReLU clips negative values to zero. Leaky ReLU keeps a small negative-side slope. Sigmoid and tanh can saturate at large input magnitudes. At the classifier output, softmax converts logits into mutually exclusive class probabilities.

### How CNN designs differ

The operation used above is cross-correlation: deep-learning libraries usually slide the kernel without reversing it. Weight sharing lets a filter detect a pattern at different positions. Pooling or strided convolution lowers spatial resolution. Average pooling summarizes a region, while max pooling emphasizes its strongest response.

| Architecture | Design to remember |
|---|---|
| LeNet and AlexNet | LeNet illustrates convolution, subsampling, and a classifier for digits. AlexNet scales the pattern to larger images and uses ReLU and dropout. |
| VGG | Repeats small $3\times3$ kernels to build a larger receptive field through depth. |
| Inception or GoogLeNet | Runs parallel operations at different spatial scales. $1\times1$ convolutions can reduce channel cost. |
| ResNet | Learns a residual $F(x)$ and adds a shortcut, $y=x+F(x)$, when dimensions match. Use a projection if the shortcut needs a different shape. |

A typical image pipeline resizes and normalizes input, uses training augmentation, then trains a stack of filters and a classification head. Validate accuracy and loss with a consistent split. For expensive models, also compare parameter count and inference cost.

## 6.4 Recurrent and recursive networks

A simple recurrent neural network (RNN) reuses $h_t=\tanh(W_xx_t+W_hh_{t-1}+b_h)$ at each time step. The current state carries information from earlier tokens. Unrolling the recurrence gives a computation graph. **Backpropagation through time** sums contributions to shared weights. Repeated Jacobian products can lead to vanishing or exploding gradients.

**Worked RNN update.** Let the current word “good” have input $x_t=0.3$, with $h_t=\tanh(x_t+0.8h_{t-1})$ and output $\sigma(2h_t)$.

| Previous hidden state | New hidden state | Output |
|---|---:|---:|
| $0$ | $0.291$ | $0.642$ |
| $-0.762$ from negative context | $-0.300$ | $0.354$ |

Unroll three steps as $h_1\to h_2\to h_3$. The third prediction depends on the first input through that chain. Backpropagation carries the third-step loss back along the same chain and adds its contributions to the shared weights. In a language model, softmax over vocabulary logits converts the final state into next-token probabilities.

A long short-term memory network (LSTM) controls its cell state with gates and uses $c_t=f_t\odot c_{t-1}+i_t\odot\tilde c_t$. The forget gate can preserve part of an earlier state and the input gate can write new information. A recursive network instead combines child representations to build a parent representation, such as a phrase representation in a parse tree.

### Sequence outputs and gated recurrence

| Output pattern | Example |
|---|---|
| One input to a sequence | Image captioning |
| A sequence to one output | Sentiment classification |
| One output per input step | Token tagging |
| Input and output sequences of different lengths | Translation with an encoder and decoder |

For next-token prediction, apply a softmax to the logits at the relevant step and compare it with the actual next token. Use the observed prefix during training. When generating, feed sampled or selected predictions back as inputs. A bidirectional RNN uses both past and future context for a known sequence, so it suits tagging. Causal generation can only use information available so far.

| Component | Role |
|---|---|
| LSTM forget gate $f_t$ | Retains part of the previous cell state. |
| LSTM input gate $i_t$ and candidate $\tilde c_t$ | Control what new information enters the cell. |
| LSTM output gate $o_t$ | Exposes part of the state as $h_t=o_t\odot\tanh(c_t)$. |
| GRU | Uses reset and update gates with a single recurrent state. |
| Truncated BPTT | Limits how far gradients are propagated through a long sequence, even if state is carried forward. |
| Recursive network | Uses the same composition rule across tree nodes, such as combining child phrases into a parent phrase. |

Character-level language models predict one character at a time. An $n$-gram baseline has a fixed context, whereas an RNN can carry earlier information in its state. Compare models on held-out next-character loss and probe specific long dependencies, such as closing a brace that opened many characters earlier. Plots of cell activations can reveal sensitivity to quotes or positions in a line, but such patterns are qualitative evidence.

### Additional topics in the deep learning sources

The broader lecture PDFs also introduce the following ideas. The course syllabus centers on §§6.1–6.4.

| Topic | Core idea |
|---|---|
| PCA, manifolds, and metric learning | PCA projects onto high-variance axes. Isomap approximates distances along a neighborhood graph, and methods such as LLE preserve local relationships. Metric learning trains a distance or representation to reflect useful similarity. |
| Spatial transformer | Predicts a spatial transform inside a model and resamples a feature map so the network can learn alignment. |
| Generative versus discriminative | A discriminative model predicts labels from inputs. A generative model describes how inputs are distributed and may generate new samples. |
| Generative adversarial network (GAN) | The generator transforms latent noise into samples. The discriminator learns to distinguish generated from real samples. Conditioning can guide generation, while mode collapse occurs when the generator covers too few types of samples. |
| Autoencoder and GAN hybrids | A reconstruction objective links a code to a specific input. An adversarial objective can encourage more realistic outputs or regularize latent codes. |
| Other examples | ImageNet supplies labeled images for benchmarking and pretraining. Word2Vec learns distributional word vectors. WaveNet generates audio with causal convolutions. CNNs can also predict keypoint locations, such as joints. |

## Practice

- **6A.** For 130 examples and batch size 32, find updates per epoch when the partial batch is kept. For validation losses $0.70,0.60,0.50,0.52,0.57$, select a checkpoint.
- **6B.** For a $28\times28\times1$ input and eight $3\times3$ filters with stride 1, no padding, and one bias per filter, find the output shape and parameter count.
- **6C.** Explain how two identical current inputs can produce different RNN outputs, and how a tree-structured recursive model differs.

## Answer checks

| Exercise | Answer |
|---|---|
| 6A | Five updates with the partial batch kept. Restore epoch 3. |
| 6B | The output is $26\times26\times8$. There are $(3\cdot3+1)\cdot8=80$ parameters. |
| 6C | $W_hh_{t-1}$ contributes to the new hidden state. A recursive network combines children of a tree node instead of advancing through one time index. |
[Chapter sources](../readme.md#6-modern-practical-deep-networks)

---

[← Chapter 5](../05%20-%20Probabilistic%20Networks/Probabilistic%20Networks.md) · [Contents](../readme.md) · [Chapter 7 →](../07%20-%20Classification/Classification.md)
