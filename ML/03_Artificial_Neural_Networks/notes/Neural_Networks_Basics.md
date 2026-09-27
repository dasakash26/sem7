# Artificial Neurons, Perceptrons, and Hidden Representations

*Chapter 3*

> **Place in the course.** This chapter develops the basic neural-network model: an artificial neuron, the perceptron learning rule, and the reason a hidden layer is needed. The next chapter, [Multilayer Perceptrons and Backpropagation](../../04_Feedforward_Networks_and_Graphical_Models/01_FFNN_and_Training/notes/Multilayer_Backpropagation.md), explains how multilayer networks are trained.

## Learning objectives

After studying this chapter, you should be able to:

1. describe an artificial neuron mathematically and geometrically;
2. explain the roles of weights, bias, and activation functions;
3. apply the perceptron learning rule to one training example;
4. explain why AND is learnable by one perceptron but XOR is not;
5. explain how hidden units create a useful new representation of the input.

---

## 3.1 From a decision rule to a neuron

Many classification tasks begin with a simple question: *how much evidence is there for one class rather than another?* Consider a model that predicts whether a student will pass a course. It may receive two numerical features:

$$
x_1=\text{hours studied},\qquad x_2=\text{attendance}.
$$

It should regard the two features differently. Attendance may matter less than study time, or its effect may even be negative in an unusual dataset. A useful model therefore assigns a separate coefficient to each input and combines the results into one score.

An **artificial neuron** performs exactly this calculation:

$$
z=\sum_{i=1}^{n}w_ix_i+b
=\mathbf w^T\mathbf x+b.
$$

The quantity $z$ is called the **pre-activation** or **weighted sum**. The neuron then produces an output

$$
a=g(z),
$$

where $g$ is an activation function.

The notation is worth learning carefully:

| Quantity | Meaning |
|---|---|
| $x_i$ | the $i$-th input feature |
| $w_i$ | the weight attached to that feature |
| $b$ | the bias |
| $z$ | weighted evidence before activation |
| $g$ | activation function |
| $a$ | output sent to the next unit or used as a prediction |

The word “neuron” is a historical analogy. The model is not a biological simulation. It is a parameterized mathematical function that can be fitted from examples.

### Example 3.1: Calculating a neuron's output

Let

$$
\mathbf x=
\begin{bmatrix}2\\3\end{bmatrix},
\qquad
\mathbf w=
\begin{bmatrix}0.4\\0.3\end{bmatrix},
\qquad
b=-0.5.
$$

The pre-activation is

$$
z=(0.4)(2)+(0.3)(3)-0.5=1.2.
$$

If the activation is a step function, the output is $1$. If it is a sigmoid, the output is $\sigma(1.2)\approx0.769$. The same weighted evidence can therefore be turned into different kinds of outputs depending on the task.

---

## 3.2 The geometry of one neuron

For binary classification, a single neuron often decides class $1$ when $z\geq0$. The boundary between the two classes is therefore

$$
\mathbf w^T\mathbf x+b=0.
$$

With two inputs, this equation describes a straight line. With three inputs, it describes a plane. In higher dimensions, it describes a hyperplane.

The weights determine the orientation of the boundary. The bias moves the boundary without changing its orientation. This is the geometric meaning of the bias: it allows the classifier to choose a threshold that need not pass through the origin.

For example, with two inputs,

$$
w_1x_1+w_2x_2+b=0
$$

can be rearranged to

$$
x_2=-\frac{w_1}{w_2}x_1-\frac{b}{w_2}.
$$

The ratio $-w_1/w_2$ determines the slope; the bias determines the intercept.

This observation gives us both the power and the limitation of the perceptron:

> A single perceptron can solve a problem whenever one linear boundary separates the classes.

---

## 3.3 Why activation functions matter

The activation function determines what a neuron's score means. It also becomes essential once we stack layers.

### Threshold or step activation

The classical perceptron uses

$$
\operatorname{step}(z)=
\begin{cases}
1,&z\geq0,\\
0,&z<0.
\end{cases}
$$

This is suitable for a hard binary decision. It is not suitable for ordinary gradient-based learning because it is not differentiable at zero and has derivative zero almost everywhere else.

### Sigmoid activation

$$
\sigma(z)=\frac{1}{1+e^{-z}}.
$$

Sigmoid maps every real number into the interval $(0,1)$. It is often used when a binary output is interpreted as a probability. Its derivative has a convenient form:

$$
\sigma'(z)=\sigma(z)\bigl(1-\sigma(z)\bigr).
$$

For very large positive or negative $z$, sigmoid becomes nearly flat. This saturation can make learning slow in deep networks.

### Hyperbolic tangent

$$
\tanh(z)=\frac{e^z-e^{-z}}{e^z+e^{-z}}.
$$

Tanh produces values in $(-1,1)$ and is centered around zero. It can also saturate.

### ReLU (Rectified Linear Unit)

$$
\operatorname{ReLU}(z)=\max(0,z).
$$

ReLU passes positive evidence unchanged and removes negative values. Its simple form makes it a common hidden-layer activation. A ReLU unit that remains negative has zero derivative and may stop updating, a behavior called the *dying ReLU* problem.

### A necessary nonlinearity

Suppose two layers had no activation functions:

$$
\mathbf h=\mathbf W_1\mathbf x+\mathbf b_1,
\qquad
\mathbf y=\mathbf W_2\mathbf h+\mathbf b_2.
$$

Substituting the first equation into the second gives

$$
\mathbf y=
(\mathbf W_2\mathbf W_1)\mathbf x+
(\mathbf W_2\mathbf b_1+\mathbf b_2).
$$

This is still one linear transformation. Thus, simply adding linear layers does not make a model more expressive. Nonlinear activations are what allow multilayer networks to represent nonlinear decision boundaries.

---

## 3.4 The perceptron as a learning machine

A **perceptron** is an artificial neuron with a step activation, used as a binary linear classifier:

$$
\hat y=\operatorname{step}(\mathbf w^T\mathbf x+b).
$$

The perceptron learning rule adjusts the parameters only when the prediction is wrong. For a training pair $(\mathbf x,y)$, define

$$
e=y-\hat y.
$$

Then update

$$
\mathbf w\leftarrow\mathbf w+\eta e\mathbf x,
\qquad
b\leftarrow b+\eta e,
$$

where $\eta>0$ is the learning rate.

The rule has a simple interpretation. If the true answer is $1$ but the model predicts $0$, then $e=1$. The update raises the score for the current input. If the true answer is $0$ but the model predicts $1$, then $e=-1$. The update lowers the score.

### Example 3.2: One perceptron update

Suppose

$$
\mathbf x=(1,1),\quad y=1,\quad
\mathbf w=(0,0),\quad b=-0.5,\quad\eta=0.1.
$$

The current score is $-0.5$, so $\hat y=0$. Hence $e=1-0=1$, and

$$
\mathbf w_{\text{new}}
=(0,0)+0.1(1)(1,1)
=(0.1,0.1),
$$

$$
b_{\text{new}}=-0.5+0.1=-0.4.
$$

The example is not necessarily classified correctly after one update. The point is that the boundary has moved in the useful direction. Training repeatedly applies this correction over the data.

If the training data are linearly separable, the perceptron convergence theorem guarantees that the rule will eventually find a separating boundary. If the data are not linearly separable, the algorithm need not converge.

---

## 3.5 Logic gates as small classification problems

Logic gates give concrete examples of linearly separable and non-separable functions.

For AND, the required truth table is:

| $x_1$ | $x_2$ | AND |
|---:|---:|---:|
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

### Deriving the parameters

Do not guess the parameters. Translate the desired output in each row into an inequality.

The perceptron outputs $1$ when

$$
w_1x_1+w_2x_2+b\geq0,
$$

and outputs $0$ when the score is negative.

For AND, the rows require:

| Input row | Required condition |
|---|---|
| $(0,0)\mapsto0$ | $b<0$ |
| $(1,0)\mapsto0$ | $w_1+b<0$ |
| $(0,1)\mapsto0$ | $w_2+b<0$ |
| $(1,1)\mapsto1$ | $w_1+w_2+b\geq0$ |

Choose equal positive weights to keep the solution simple:

$$
w_1=w_2=1.
$$

The conditions then become

$$
b<0,\qquad 1+b<0,\qquad 2+b\geq0.
$$

Together they require

$$
-2\leq b<-1.
$$

Any bias in this interval works. We choose the midpoint $b=-1.5$. Therefore,

$$
\boxed{w_1=1,\qquad w_2=1,\qquad b=-1.5.}
$$

This is the general method: write one inequality for each truth-table row, choose convenient weights, and find a bias that satisfies all the inequalities.

### Verification of the choice

$$
w_1=1,\qquad w_2=1,\qquad b=-1.5.
$$

The perceptron first calculates

$$
z=w_1x_1+w_2x_2+b=x_1+x_2-1.5,
$$

and then applies the step rule: output $1$ when $z\geq0$, otherwise output $0$.

| $x_1$ | $x_2$ | Score $z=x_1+x_2-1.5$ | Step output | Target |
|---:|---:|---:|---:|---:|
| 0 | 0 | $-1.5$ | 0 | 0 |
| 0 | 1 | $-0.5$ | 0 | 0 |
| 1 | 0 | $-0.5$ | 0 | 0 |
| 1 | 1 | $0.5$ | 1 | 1 |

The output agrees with the target in every row, so this single perceptron realizes the AND gate. Notice that the bias $-1.5$ places the threshold between the score for one active input and the score for two active inputs.

Similarly, OR can use

$$
w_1=1,\qquad w_2=1,\qquad b=-0.5,
$$

because OR requires

$$
b<0,\qquad w_1+b\geq0,\qquad w_2+b\geq0.
$$

With $w_1=w_2=1$, these conditions become $-1\leq b<0$, so $b=-0.5$ is a convenient choice.

For NOT, use one input and require $x=0$ to produce $1$ and $x=1$ to produce $0$:

$$
b\geq0,\qquad w+b<0.
$$

Choose $b=0.5$ and $w=-1$. Then the scores are $0.5$ for $x=0$ and $-0.5$ for $x=1$.

$$
w=-1,\qquad b=0.5.
$$

Different values can work. The solution is not unique; any weights and bias that satisfy all the inequalities implement the same gate.

### A geometric interpretation

For AND, the decision boundary is

$$
x_1+x_2-1.5=0.
$$

The derivation above is equivalent to finding a line that places the positive point $(1,1)$ on one side and the three negative points on the other.

These examples are important because they show that a perceptron is not merely a formula. It represents a geometrical separator.

---

## 3.6 XOR and the need for hidden units

The XOR function is $1$ when its two inputs differ:

| $x_1$ | $x_2$ | XOR |
|---:|---:|---:|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

The positive points $(0,1)$ and $(1,0)$ lie on opposite corners of the input square. No single line can place both positive points on one side while keeping both negative points on the other side.

Therefore:

$$
\boxed{\text{XOR is not linearly separable.}}
$$

The failure is not caused by poor training. It is caused by insufficient model structure. A one-neuron model has only one boundary; the problem requires a more flexible decision rule.

A hidden layer solves this by building intermediate features. One conceptual construction is

$$
h_1=\operatorname{OR}(x_1,x_2),
\qquad
h_2=\operatorname{NAND}(x_1,x_2),
$$

followed by

$$
\operatorname{XOR}(x_1,x_2)=\operatorname{AND}(h_1,h_2).
$$

The hidden layer maps the original input $(x_1,x_2)$ into a new representation $(h_1,h_2)$. In that new space, the final output can be obtained by one linear separator.

This is the central idea of representation learning:

> Hidden units learn features that make the final prediction simpler.

---

## 3.7 From one perceptron to an MLP

A **multilayer perceptron (MLP)** contains an input layer, one or more hidden layers, and an output layer. For a network with one hidden layer,

$$
\mathbf z^{(1)}
=\mathbf W^{(1)}\mathbf x+\mathbf b^{(1)},
\qquad
\mathbf a^{(1)}=g^{(1)}(\mathbf z^{(1)}),
$$

$$
\mathbf z^{(2)}
=\mathbf W^{(2)}\mathbf a^{(1)}+\mathbf b^{(2)},
\qquad
\hat{\mathbf y}=g^{(2)}(\mathbf z^{(2)}).
$$

The superscript identifies a layer rather than a power. The vector $\mathbf a^{(1)}$ contains the hidden features learned from the input.

The following terms should be kept distinct:

- A network is **feedforward** when its directed graph has no cycle. Information moves from input toward output.
- A layer is **fully connected** when every unit in the preceding layer is connected to every unit in that layer.
- A network is **deep** when it has multiple representation-learning layers.

The forward calculation produces a prediction. Training requires another story: measuring how wrong that prediction is and determining how each parameter should change. That is the subject of the next chapter.

---

## Chapter summary

An artificial neuron computes a weighted sum and applies an activation. In classification, the weighted sum defines a decision boundary. A perceptron is a single-neuron linear classifier trained by correcting errors. It can represent linearly separable functions such as AND and OR, but not XOR. A hidden layer creates intermediate features, allowing an MLP to construct nonlinear decision boundaries.

The chapter follows a single conceptual progression:

> **Weighted evidence → linear boundary → perceptron learning → XOR limitation → hidden representation → MLP**

---

## Review questions

1. What is the difference between $z$ and $a$ in an artificial neuron?
2. How does changing the bias affect a two-dimensional decision boundary?
3. Why does a perceptron update only after a mistake?
4. Verify that $w_1=w_2=1,\ b=-0.5$ realizes OR.
5. Explain, without drawing a graph, why XOR cannot be solved by one perceptron.
6. What does it mean to say that a hidden layer “learns a representation”?

---

## Source guide

- Artificial neuron, ANN terminology, layers, and perceptron: *Artificial_Neural_Network.pdf*, pages 7-22.
- Learning paradigms and ANN context: *Artificial_Neural_Network.pdf*, pages 23-38.
- XOR, hidden-layer intuition, and the role of nonlinearity: *L03 Multilayer Perceptrons.pdf*, pages 1-7.
