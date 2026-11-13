# Chapter 4, Part 1. Feedforward Networks

[Contents](../../readme.md)

## 4.1 Risk, prediction, and loss

An individual example contributes loss $\ell(f_\theta(x),y)$. **Empirical risk** averages it over training data, $\hat R(\theta)=n^{-1}\sum_i\ell(f_\theta(x_i),y_i)$. **Population risk** is the expected loss on the target distribution. Training may minimize $\hat R(\theta)+\lambda\Omega(\theta)$, where $\Omega$ penalizes model complexity.

| Task | Output | Typical loss |
|---|---|---|
| Regression | Linear output | Squared error |
| Binary classification | Sigmoid | Binary cross-entropy |
| One of several classes | Softmax | Categorical cross-entropy |
| Multiple independent labels | One sigmoid per label | Sum or mean of binary cross-entropies |

For a single binary target, use $L=-[y\ln\hat y+(1-y)\ln(1-\hat y)]$. For a multiclass logit vector $z$, use $\hat y_k=e^{z_k}/\sum_j e^{z_j}$. In numerical calculations, subtract the largest logit before exponentiating. This preserves the probabilities.

With a one-hot target, categorical cross-entropy is $-\ln \hat y_k$ for the true class $k$. Use the loss and reduction stated in the question. Half-squared error simplifies its derivative, while mean squared error divides by the number of terms.

## 4.2 Forward pass and backpropagation

Use column vectors throughout. For one hidden layer,

$$
z_h=W_hx+b_h,\qquad h=g(z_h),\qquad z_o=W_oh+b_o,\qquad \hat y=g_o(z_o).
$$

In backpropagation, $\delta$ means the derivative of the loss with respect to the layer's pre-activation. With sigmoid and binary cross-entropy or softmax and categorical cross-entropy, $\delta_o=\hat y-y$. With sigmoid and half-squared error, $\delta_o=(\hat y-y)\hat y(1-\hat y)$. Softmax has a coupled Jacobian, so the elementwise formula for sigmoid does not transfer to an arbitrary softmax loss.

$$
\delta_h=(W_o^T\delta_o)\odot g'(z_h),\qquad
\nabla_{W_o}L=\delta_oh^T,\qquad
\nabla_{W_h}L=\delta_hx^T.
$$

The bias gradients are the corresponding deltas. Calculate all deltas using the forward-pass weights. Then subtract $\eta$ times each gradient. For a mean batch loss, average the example gradients.

**Worked example.** Take $x=1$, scalar hidden and output weights initially 1, zero biases, sigmoid activations, target 1, and binary cross-entropy.

| Step | Calculation |
|---|---|
| Forward | $h=\sigma(1)\approx0.7311$, $\hat y=\sigma(h)\approx0.6750$, $L=-\ln\hat y\approx0.3930$ |
| Output delta | $\delta_o=0.6750-1\approx-0.3250$ |
| Hidden delta | $\delta_h=1\cdot\delta_o h(1-h)\approx-0.0639$ |
| Weight gradients | $\delta_oh\approx-0.2376$ and $\delta_hx\approx-0.0639$ |
| Update at $\eta=0.1$ | Output and hidden weights become $1.0238$ and $1.0064$. Their biases become $0.0325$ and $0.0064$ |

### The chain rule in a deeper network

For layer $\ell$, let $z^\ell=W^\ell a^{\ell-1}+b^\ell$ and $a^\ell=g_\ell(z^\ell)$. Backpropagation first computes the output derivative, then repeats the same local rule from the final hidden layer back to the first:

$$
\delta^\ell=\big((W^{\ell+1})^T\delta^{\ell+1}\big)\odot g'_\ell(z^\ell).
$$

- **Forward pass:** Store activations and pre-activations that the derivative will need.
- **Backward pass:** Multiply the upstream derivative by the local derivative. At a value used by several branches, add the contributions from those branches.
- **Parameter update:** Use $\delta^\ell(a^{\ell-1})^T$ for the weight gradient and $\delta^\ell$ for the bias gradient. Apply the update after all gradients are computed.

For a sigmoid output with half-squared error, the worked example above has $\delta_o\approx-0.0713$ because the loss derivative includes $\hat y(1-\hat y)$. With binary cross-entropy it is about $-0.3250$. The forward prediction is identical in the two cases.

### From one example to a batch

For layer widths $d_{\mathrm{in}}$ and $d_{\mathrm{out}}$, there are $d_{\mathrm{out}}(d_{\mathrm{in}}+1)$ parameters including biases. With $m$ examples as columns of $X$, the layer computes $Z=WX+b\mathbf{1}^T$. If $D$ contains one pre-activation gradient per example, the mean batch gradients are $DX^T/m$ for $W$ and $D\mathbf{1}/m$ for $b$. All examples in the batch use the same current weights.

Use validation for model selection, including architecture, hyperparameters, and early-stopping checkpoint. Initialize hidden weights to break symmetry. Xavier scaling is a common choice for tanh-like networks and He scaling for ReLU. Chapter 6 develops optimization and regularization with examples.

**Chain-rule check.** For $f=(x_1+x_2)x_3-x_4$ at $(2,3,4,1)$, set $u=x_1+x_2=5$ and $v=ux_3=20$, so $f=19$. The backward pass gives $\partial f/\partial v=1$, $\partial f/\partial u=x_3=4$, and $\partial f/\partial x_3=u=5$. Since $\partial u/\partial x_1=\partial u/\partial x_2=1$, the gradient is $(4,4,5,-1)$.

Automatic differentiation stores a computation graph and applies these rules in reverse. It can compute gradients for a scalar loss with respect to many parameters in one backward pass. A finite-difference check, such as $[L(w+\epsilon)-L(w-\epsilon)]/(2\epsilon)$, can catch an incorrect hand-derived gradient. Check with the same input and model state in both loss evaluations.

### Loss and training choices

| Choice | Effect |
|---|---|
| MSE versus MAE | Squared error emphasizes large residuals and yields the conditional mean as the ideal point prediction. Absolute error emphasizes typical size and yields a conditional median. |
| Learning rate | A small step may be slow. A large step can overshoot or diverge. Plot train and validation loss before adjusting it. |
| Saturating activations | Large positive or negative sigmoid scores give small derivatives. Many such layers can attenuate gradients. ReLU-like units and suitable initialization help. |
| Model selection | Compare depth, width, regularization, and stopping time using validation. Reserve the test set for final evaluation. |

### Feedforward models for text

An **embedding** is a learned vector for a vocabulary item. An embedding lookup selects its row from the embedding matrix. Mean pooling averages token vectors and max pooling keeps the strongest value in each coordinate. Both discard token order. Concatenating $m$ token vectors keeps their positions and gives an input of size $m d_e$ for embedding dimension $d_e$. It requires a fixed window or a padding and truncation rule.

**Worked application.** A next-word model can take *for all the* as input and *fish* as the target. Concatenate the three context embeddings, run the forward pass, and apply softmax to the vocabulary logits. The loss is $-\ln P(\text{fish}\mid\text{for all the})$. Sliding the window supplies more examples. Backpropagation updates the embeddings as well as the network weights.

For text classification, pass a pooled or concatenated embedding through the same hidden layers and predict a document label instead of the next token. The softmax operates over classes in one case and over vocabulary items in the other. Padding and masking matter when batching examples of different lengths.

## Practice

- **4A.** With one input and one sigmoid output, $x=2,w=b=0,y=1$, find the BCE loss and one update at $\eta=0.1$.

## Answer checks

| Exercise | Answer |
|---|---|
| 4A | $\hat y=0.5$, $L=\ln2\approx0.6931$. Gradients are $-1$ and $-0.5$, giving $w'=0.1,b'=0.05$. |

[Chapter sources](../../readme.md#feedforward-networks)

---

[← Chapter 3](../../03%20-%20Neural%20Networks/Neural%20Networks.md) · [Contents](../../readme.md) · [Graphical Models →](../Graphical%20Models/Graphical%20Models.md)
