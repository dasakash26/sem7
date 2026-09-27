# Multilayer Perceptrons, Loss Functions, and Backpropagation

*Chapter 4. FFNN means Feedforward Neural Network; MLP means Multilayer Perceptron.*

> **Prerequisite.** Read [Artificial Neurons, Perceptrons, and Hidden Representations](../../../03_Artificial_Neural_Networks/notes/Neural_Networks_Basics.md) first. It explains why hidden layers are necessary. This chapter explains how a multilayer network makes a prediction and learns its parameters.

## Learning objectives

After studying this chapter, you should be able to:

1. write the forward equations for a feedforward neural network;
2. choose a suitable output activation and loss for a task;
3. explain gradient descent and the chain rule;
4. derive the backpropagation equations for a one-hidden-layer network;
5. carry out one numerical forward and backward pass;
6. describe practical choices in neural-network training.

---

## 4.1 The learning problem

Chapter 3 ended with an MLP that can represent a nonlinear function. Representation alone is not enough. The network must still discover useful weights and biases from data.

For one training example, the learning process has four stages:

> **Input → prediction → loss → parameter update**

The prediction is computed by **forward propagation**. The loss measures disagreement between prediction and target. **Backpropagation** calculates how the loss changes with each parameter. An optimizer, such as gradient descent, uses those derivatives to change the parameters.

These terms have different jobs:

| Term | Role |
|---|---|
| Forward propagation | Computes the present prediction |
| Loss function | Measures the quality of that prediction |
| Backpropagation | Computes derivatives of loss with respect to parameters |
| Gradient descent | Updates parameters using the derivatives |

Confusing backpropagation with gradient descent is a common error. Backpropagation computes gradients; gradient descent uses them.

---

## 4.2 A one-hidden-layer feedforward network

Let an input have $d$ features, let the hidden layer contain $h$ units, and let the output layer contain $k$ units. The parameters are

$$
\mathbf W^{(1)}\in\mathbb R^{h\times d},
\qquad
\mathbf b^{(1)}\in\mathbb R^h,
$$

$$
\mathbf W^{(2)}\in\mathbb R^{k\times h},
\qquad
\mathbf b^{(2)}\in\mathbb R^k.
$$

The network computes:

$$
\mathbf z^{(1)}
=\mathbf W^{(1)}\mathbf x+\mathbf b^{(1)},
$$

$$
\mathbf a^{(1)}
=g^{(1)}(\mathbf z^{(1)}),
$$

$$
\mathbf z^{(2)}
=\mathbf W^{(2)}\mathbf a^{(1)}+\mathbf b^{(2)},
$$

$$
\hat{\mathbf y}
=g^{(2)}(\mathbf z^{(2)}).
$$

This is called a feedforward network because values travel from the input layer toward the output layer without a directed cycle.

![Forward propagation and backpropagation through a neural network](https://engines.egr.uh.edu/sites/engines/files/images/page/Backpropagation.jpg)

*Figure 4.1. Reference diagram from the [University of Houston's Engines of Our Ingenuity](https://engines.egr.uh.edu/episode/3357). Read the solid arrows as the forward computation and the returning arrows as the flow of error information used to compute gradients; the notation and update rule are developed below.*

### Reading the equations

We treat individual examples as column vectors. In $\mathbf W^{(1)}$, row $j$ contains all the weights entering hidden neuron $j$. Thus its calculation is

$$
z_j^{(1)}=\sum_{i=1}^{d}W_{ji}^{(1)}x_i+b_j^{(1)}.
$$

The matrix equation performs this same sum for every hidden neuron at once. The input layer holds the features; it has no trainable weights of its own.

The first line calculates how strongly each hidden unit responds to the input. The second line turns those scores into hidden features. The third line combines the hidden features into output scores. The final line turns the scores into a prediction with a meaning appropriate to the task.

The letters have a useful convention:

$$
\mathbf z=\text{value before activation},
\qquad
\mathbf a=\text{value after activation}.
$$

### Parameter count

The first layer has $hd$ weights and $h$ biases. The second layer has $kh$ weights and $k$ biases. Therefore,

$$
\text{number of parameters}=hd+h+kh+k.
$$

For example, a network with $d=4$ inputs, $h=5$ hidden units, and $k=3$ outputs has

$$
4(5)+5+5(3)+3=43
$$

trainable parameters.

### How are the weights and biases obtained?

For a small logic gate, we can derive parameters by solving inequalities, as in Chapter 3. For a general prediction problem, we usually do not know the required hidden features in advance. We choose the architecture, initialize the parameters, and learn them by minimizing a loss over the training data.

Initial weights are therefore starting values, not a hand-derived solution. Hidden units generally need different random initial weights to avoid learning identical features. Biases can often start at zero. The forward calculation tells us what the current parameters predict; the backward calculation tells us how to improve them.

---

## 4.3 Choosing the output layer and loss

The output activation and loss function should be chosen as a pair because they express the type of target being predicted.

| Task | Output activation | Typical loss |
|---|---|---|
| Regression | Linear | Mean squared error |
| Binary classification | Sigmoid | Binary cross-entropy |
| Multiclass classification | Softmax | Categorical cross-entropy |
| Multilabel classification | One sigmoid per label | Binary cross-entropy |

### Regression

For a real-valued target, the output can be left linear:

$$
\hat y=z.
$$

A common loss is the mean squared error (MSE). For $N$ examples with one target value each:

$$
L=\frac{1}{N}\sum_{i=1}^N(y_i-\hat y_i)^2.
$$

### Binary classification

For one yes/no target, sigmoid converts the output score into a value between $0$ and $1$:

$$
\hat y=\sigma(z)=\frac{1}{1+e^{-z}},
$$

where $e\approx 2.71828$ is the base of the natural logarithm. At test time, predict class $1$ when $\hat y\ge 0.5$ and class $0$ otherwise (unless a different threshold is chosen).

Binary cross-entropy is

$$
L=-\left[y\log\hat y+(1-y)\log(1-\hat y)\right].
$$

This loss strongly penalizes a prediction that is both wrong and confident.

For example, when $y=1$, the loss reduces to $-\log\hat y$. Predictions of $0.9$ and $0.1$ give losses of about $0.105$ and $2.303$, respectively. The model is penalized much more for assigning a small probability to the correct answer.

### Multiclass classification

If exactly one of $k$ classes is correct, softmax converts the $k$ output scores into a probability distribution:

$$
\hat y_j=
\frac{e^{z_j}}{\sum_{r=1}^ke^{z_r}}.
$$

The outputs are non-negative and sum to one. A one-hot target has a $1$ at the correct class and $0$ elsewhere: class 2 of three classes is represented by $(0,1,0)$. For such a target vector $\mathbf y$, categorical cross-entropy is

$$
L=-\sum_{j=1}^ky_j\log\hat y_j.
$$

The predicted class is the index of the largest softmax probability (equivalently, the largest score):

$$
\hat c=\arg\max_{j\in\{1,\ldots,k\}}\hat y_j
      =\arg\max_{j\in\{1,\ldots,k\}}z_j.
$$

Softmax is therefore the **probability-producing** step; `argmax` is the final **decision** step. During training, keep the full softmax probabilities so that cross-entropy can measure how confident the model was.

In practice, softmax is implemented with a numerical-stability adjustment: the largest score is subtracted from every score before exponentiation. This leaves the probabilities unchanged.

### Multilabel classification

If an image can contain both a cat and a dog, the labels are not mutually exclusive. Use one sigmoid per label and sum or average the binary cross-entropies. Unlike softmax, these output probabilities need not sum to one.

### One example versus a dataset

From this point through the worked example, $L$ means the loss for one example. For a batch containing $B$ examples, the training objective is usually their average:

$$
J(\theta)=\frac{1}{B}\sum_{i=1}^{B}L_i(\theta).
$$

Its gradient is the average of the individual gradients. This distinction prevents an accidental change in update scale when batch size changes.

---

## 4.4 Gradient descent

The loss $L(\theta)$ depends on all parameters $\theta$, where $\theta$ represents every weight and bias in the network. Its gradient

$$
\nabla_\theta L
$$

points in the direction of steepest local increase in loss. To reduce the loss, gradient descent moves in the opposite direction:

$$
\theta\leftarrow\theta-\eta\nabla_\theta L.
$$

The learning rate $\eta$ determines the size of the step.

If $\partial L/\partial w>0$, increasing $w$ would increase loss locally, so gradient descent decreases $w$. If $\partial L/\partial w<0$, increasing $w$ would decrease loss locally, so gradient descent increases $w$.

The difficulty is not the update rule. The difficulty is calculating $\partial L/\partial w$ for every parameter in a deep network. Backpropagation solves that calculation efficiently.

For a simple numerical illustration, let $w=0.5$, $\partial L/\partial w=-0.2$, and $\eta=0.1$. The update is $w_{\text{new}}=0.5-0.1(-0.2)=0.52$. The negative derivative says that increasing the weight decreases loss locally. It does not guarantee that an arbitrarily large increase would help; that is why the step size matters.

---

## 4.5 The chain rule and the idea of backpropagation

Consider the simple dependency

$$
x\longrightarrow z\longrightarrow a\longrightarrow L.
$$

If $x$ affects the loss only through $z$ and $a$, then

$$
\frac{\partial L}{\partial x}
=
\frac{\partial L}{\partial a}
\frac{\partial a}{\partial z}
\frac{\partial z}{\partial x}.
$$

This is the chain rule. It decomposes one global question, “How does the loss change with $x$?”, into local questions along the computation.

Backpropagation begins at the final loss and applies the chain rule backward through the network. It reuses quantities already computed during the forward pass, which is why it is efficient.

The useful mental picture is:

$$
\text{activations move forward; sensitivities move backward.}
$$

An activation tells the next layer what value it received. A sensitivity tells the previous layer how much the final loss would change if that value were changed slightly.

For a neuron $z=wx+b$, the local derivatives are $\partial z/\partial w=x$ and $\partial z/\partial b=1$. Therefore, once we know $\delta=\partial L/\partial z$, we immediately obtain

$$
\frac{\partial L}{\partial w}=\delta x,
\qquad
\frac{\partial L}{\partial b}=\delta.
$$

This small result is the foundation of the matrix gradient formulas that follow.

---

## 4.6 Backpropagation for one hidden layer

We use the following convention:

$$
\boldsymbol\delta^{(l)}
=
\frac{\partial L}{\partial\mathbf z^{(l)}}.
$$

The delta is the sensitivity of the loss to the pre-activation at layer $l$.

### Output layer

First calculate the output delta:

$$
\boldsymbol\delta^{(2)}
=
\frac{\partial L}{\partial\mathbf z^{(2)}}.
$$

To derive the output delta, begin with one sigmoid output and binary cross-entropy:

$$
\frac{\partial L}{\partial\hat y}
=-\frac{y}{\hat y}+\frac{1-y}{1-\hat y},
\qquad
\frac{\partial\hat y}{\partial z}
=\hat y(1-\hat y).
$$

Multiplying these derivatives gives

$$
\delta
=-y(1-\hat y)+(1-y)\hat y
=\hat y-y.
$$

For sigmoid output with binary cross-entropy, and for softmax output with categorical cross-entropy, the derivative therefore takes the important form

$$
\boxed{\boldsymbol\delta^{(2)}=\hat{\mathbf y}-\mathbf y.}
$$

The output-layer gradients are

$$
\frac{\partial L}{\partial\mathbf W^{(2)}}
=
\boldsymbol\delta^{(2)}(\mathbf a^{(1)})^T,
$$

$$
\frac{\partial L}{\partial\mathbf b^{(2)}}
=
\boldsymbol\delta^{(2)}.
$$

The first equation has an intuitive interpretation: the change assigned to a connection depends on the output unit's error signal and the activation that entered the connection.

For a single output connection $W_{ji}^{(2)}$, the chain rule gives

$$
\frac{\partial L}{\partial W_{ji}^{(2)}}
=\frac{\partial L}{\partial z_j^{(2)}}
\frac{\partial z_j^{(2)}}{\partial W_{ji}^{(2)}}
=\delta_j^{(2)}a_i^{(1)}.
$$

The outer product collects these individual connection gradients into a matrix. Its shape is $(k\times1)(1\times h)=k\times h$, matching $\mathbf W^{(2)}$.

### Hidden layer

The hidden layer does not have a direct target. Each hidden unit can influence several outputs, so we add its contributions along all those paths. For hidden unit $i$:

$$
\frac{\partial L}{\partial a_i^{(1)}}
=\sum_{j=1}^{k}
\frac{\partial L}{\partial z_j^{(2)}}
\frac{\partial z_j^{(2)}}{\partial a_i^{(1)}}
=\sum_{j=1}^{k}\delta_j^{(2)}W_{ji}^{(2)}.
$$

Then apply the hidden activation derivative:

$$
\delta_i^{(1)}
=
\left(\sum_{j=1}^{k}W_{ji}^{(2)}\delta_j^{(2)}\right)
g^{(1)\prime}(z_i^{(1)}).
$$

Writing this calculation for all hidden units at once gives:

$$
\boxed{
\boldsymbol\delta^{(1)}
=
\left((\mathbf W^{(2)})^T\boldsymbol\delta^{(2)}\right)
\odot
g^{(1)\prime}(\mathbf z^{(1)}).
}
$$

Here, $\odot$ denotes elementwise multiplication. The transpose appears because sensitivity is being moved in the reverse direction of the forward map.

The first-layer gradients are

$$
\frac{\partial L}{\partial\mathbf W^{(1)}}
=
\boldsymbol\delta^{(1)}\mathbf x^T,
$$

$$
\frac{\partial L}{\partial\mathbf b^{(1)}}
=
\boldsymbol\delta^{(1)}.
$$

Finally, update every parameter:

$$
\mathbf W^{(l)}
\leftarrow
\mathbf W^{(l)}
-\eta\frac{\partial L}{\partial\mathbf W^{(l)}},
$$

$$
\mathbf b^{(l)}
\leftarrow
\mathbf b^{(l)}
-\eta\frac{\partial L}{\partial\mathbf b^{(l)}}.
$$

Calculate every gradient using the parameter values from the same forward pass, then apply the updates. Updating the output weights before calculating the hidden delta would mix two different parameter states.

For a deeper network, the same hidden-layer rule repeats from the output layer back toward the input.

---

## 4.7 Worked example: one complete update

Consider the smallest network that has a hidden layer. It has one input, one hidden sigmoid unit, and one output sigmoid unit:

$$
x=1,\qquad y=1,
$$

$$
w_1=0.5,\quad b_1=0,\qquad
w_2=0.5,\quad b_2=0.
$$

These weights are chosen initial values for illustrating one training step. They are not the final solution to the task. To show every activation derivative explicitly, use the half squared-error loss

$$
L=\frac12(\hat y-y)^2
$$

and learning rate $\eta=0.1$.

This example deliberately uses squared error rather than binary cross-entropy. Its output delta consequently includes the sigmoid derivative. The shortcut $\delta_2=\hat y-y$ must not be used here.

### Forward pass

At the hidden unit,

$$
z_1=w_1x+b_1=(0.5)(1)+0=0.5,
$$

$$
a_1=\sigma(0.5)\approx0.622459.
$$

At the output unit,

$$
z_2=w_2a_1+b_2=(0.5)(0.622459)=0.311230,
$$

$$
\hat y=\sigma(0.311230)\approx0.577185.
$$

The loss is

$$
L=\frac12(0.577185-1)^2\approx0.089386.
$$

### Backward pass

For sigmoid with squared error,

$$
\frac{\partial L}{\partial\hat y}=\hat y-y,
\qquad
\frac{\partial\hat y}{\partial z_2}=\hat y(1-\hat y).
$$

Multiplying gives

$$
\delta_2
=(\hat y-y)\hat y(1-\hat y)
\approx-0.103185.
$$

Therefore,

$$
\frac{\partial L}{\partial w_2}
=\delta_2a_1
\approx-0.064228,
\qquad
\frac{\partial L}{\partial b_2}
=\delta_2
\approx-0.103185.
$$

For the hidden unit,

$$
\delta_1
=(w_2\delta_2)a_1(1-a_1)
\approx-0.012124.
$$

Thus,

$$
\frac{\partial L}{\partial w_1}
=\delta_1x
\approx-0.012124,
\qquad
\frac{\partial L}{\partial b_1}
=\delta_1
\approx-0.012124.
$$

### Update

$$
w_2\leftarrow0.5-0.1(-0.064228)\approx0.506423,
$$

$$
b_2\leftarrow0-0.1(-0.103185)\approx0.010318,
$$

$$
w_1\leftarrow0.5-0.1(-0.012124)\approx0.501212,
$$

$$
b_1\leftarrow0-0.1(-0.012124)\approx0.001212.
$$

The target is $1$ while the original prediction is $0.577$. The updated parameters give a prediction of approximately $0.581$ and loss $0.088$, smaller than the previous loss $0.089386$. The update improves this example, but repeated training on the full dataset is needed to learn a useful model.

---

## 4.8 Training over a dataset

The preceding example uses one training case. Real training repeats the same logic for many examples.

1. Initialize weights and biases, usually with small random values.
2. Select a batch of training examples.
3. Run forward propagation and calculate the batch loss.
4. Run backpropagation to calculate batch gradients.
5. Apply an optimizer update.
6. Repeat until the chosen stopping criterion is met.

An **epoch** is one complete pass through the training set. An **iteration** is one parameter update. If all examples are used and the final partial batch is kept, a training set with $N$ examples and batch size $B$ has $\lceil N/B\rceil$ iterations per epoch.

For example, with $100$ examples and batch size $20$, one epoch consists of five batch updates. Ten epochs mean ten passes through the data and $50$ updates. An epoch is not one example or necessarily one update.

Three update styles are common:

| Method | Examples used per update | Character |
|---|---:|---|
| Batch gradient descent | All examples | Stable but expensive |
| Stochastic gradient descent | One example | Noisy but frequent |
| Mini-batch gradient descent | A small group | Standard practical compromise |

---

## 4.9 Practical training considerations

### Input scaling

Features on incompatible scales can make optimization unstable or slow. Standardization is common:

$$
x'=\frac{x-\mu}{\sigma}.
$$

The mean $\mu$ and standard deviation $\sigma$ must be calculated on the training set and then reused for validation and test data.

### Initialization

If otherwise interchangeable hidden units begin with identical incoming and outgoing parameters, they receive identical gradients and remain identical. Random weight initialization breaks this symmetry; biases can commonly begin at zero.

Xavier/Glorot initialization is commonly associated with sigmoid or tanh networks. He initialization is commonly associated with Rectified Linear Unit (ReLU) networks. Both aim to keep activations and gradients at reasonable scales across layers.

### Regularization

L2 regularization adds a penalty to the data loss:

$$
J_{\text{reg}}
=J_{\text{data}}+\frac{\lambda}{2}\sum_l\|\mathbf W^{(l)}\|_F^2.
$$

Here $\lambda$ controls the penalty strength and $\|\mathbf W\|_F^2$ is the sum of squared matrix entries. Its additional weight gradient is $\lambda\mathbf W$. The penalty discourages large weights rather than directly rewarding training accuracy.

Dropout randomly removes some hidden activations during training, encouraging the network to avoid depending too heavily on individual units. With inverted dropout, retained activations are rescaled during training and dropout is disabled during inference.

### Overfitting and early stopping

Training performance may improve while validation performance worsens. This is a sign of overfitting: the model is learning idiosyncrasies of the training data rather than a general pattern.

Early stopping retains the parameters from the epoch with the best validation result. Other common controls are L2 regularization, dropout, simpler models, and more training data.

### Vanishing and exploding gradients

Backpropagation multiplies derivatives across layers. Repeated small factors can make early-layer gradients vanish; repeated large factors can make them explode. Suitable activations, careful initialization, normalization, residual connections, and gradient clipping are common remedies.

---

## 4.10 Common examination mistakes

1. Writing a perceptron update in a backpropagation answer without defining a differentiable loss.
2. Calling backpropagation an optimizer. It calculates gradients; it does not itself choose the update.
3. Forgetting bias gradients.
4. Mixing sign conventions. If $\delta=\partial L/\partial z$, then subtract the gradient in the update.
5. Treating softmax outputs as independent binary probabilities. They compete and sum to one.
6. Claiming that more layers automatically improve a model. They increase representational capacity but also make optimization and overfitting more difficult.

---

## Chapter summary

An MLP alternates affine transformations and nonlinear activations to form a prediction. A loss function measures the prediction's quality. Gradient descent updates parameters in the direction that reduces loss. Backpropagation computes those gradients by applying the chain rule from the output layer backward through the network.

The complete training chain is:

> **Forward propagation → loss → output delta → hidden deltas → gradients → parameter update**

---

## Review questions

1. Give the dimensions of the two weight matrices in a network with $d$ inputs, $h$ hidden units, and $k$ outputs.
2. Which output activation and loss would you use for a three-class image classifier? Why?
3. Explain the difference between a gradient, backpropagation, and gradient descent.
4. Why does the hidden-delta equation contain a transpose?
5. Derive the weight gradient for an output unit from the chain rule.
6. What does it mean when training loss falls but validation loss rises?
7. Write a 15-mark answer outline for “Explain backpropagation in an MLP.”

---

## Source guide

- Perceptron limitations, MLP structure, activations, forward propagation, losses, and backpropagation: *13_Multilayer_Perceptron.pdf*, pages 4-44.
- FFNN architecture, output choices, vectorized notation, training, initialization, and early stopping: *in3050_lecture_07_ffnn_2025.pdf*, pages 5-64.
- A compact derivation of forward propagation and backpropagation: *lec21.notes.pdf*, pages 4-8.
