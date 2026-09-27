# Feedforward Networks for Text and Batched Computation

*A companion to Chapter 4*

> Read [Multilayer Perceptrons, Loss Functions, and Backpropagation](Multilayer_Backpropagation.md) first. This chapter applies the same model to text and explains how multiple examples are processed together. Text applications provide context; follow the CT scope map for assessed topics.

## 1. From a text document to a prediction

A Feedforward Neural Network (FFNN) receives a fixed-size numerical vector. A sentence is a variable-length sequence of symbols. Before a network can classify text, we must therefore decide how to represent it numerically.

Consider sentiment classification: predict whether a review is positive or negative. One representation uses hand-designed features such as word counts, review length, or the frequency of positive and negative words. Another uses learned word embeddings and combines them into a document vector.

In either case, the classifier follows the same equations as Chapter 4:

$$
\mathbf z^{(1)}=\mathbf W^{(1)}\mathbf x+\mathbf b^{(1)},
\qquad
\mathbf a^{(1)}=g(\mathbf z^{(1)}),
$$

$$
\mathbf z^{(2)}=\mathbf W^{(2)}\mathbf a^{(1)}+\mathbf b^{(2)}.
$$

For two-class sentiment, one sigmoid output is sufficient. For exactly one of several document topics, use a softmax output. The hidden layer learns combinations of input features that help distinguish the classes.

There are two distinct uses of an FFNN in this chapter:

| Task | What enters the network? | What is predicted? | Suitable combination |
|---|---|---|---|
| Document classification | All tokens in a document | One label for the document | Pooling when exact order is not essential |
| Fixed-window language modeling | A fixed number of preceding tokens | The next token | Concatenation to retain position and order |

In both cases the FFNN requires one fixed-size input vector. The difference is how the token vectors are combined to create it.

![Feedforward neural network for text classification](https://www.wasilzafar.com/images/series/nlp/feedforward-mlp-text-classification-layers.webp)

*Figure 1.1. Reference architecture from [Wasil Zafar's NLP notes](https://www.wasilzafar.com/pages/series/nlp/nlp-neural-networks.html): a vector representation enters one or more hidden layers and the final layer produces class scores. The surrounding sections explain how the text vector is constructed and how the network is trained.*

## 2. Word embeddings and lookup

A word embedding is a dense numerical vector associated with a vocabulary item. Let the vocabulary contain $V$ words and each embedding have $d_e$ components. Use the convention

$$
\mathbf E\in\mathbb R^{V\times d_e}.
$$

Each row stores one word vector. If word $w$ has vocabulary index $r$, its embedding is the transpose of row $r$, viewed as a column vector:

$$
\mathbf e(w)=\mathbf E^T\mathbf q_r,
$$

where $\mathbf q_r$ is the $V$-dimensional one-hot vector with a $1$ at position $r$. The dimensions are $(d_e\times V)(V\times1)=d_e\times1$.

The multiplication is simply a mathematical description of selecting a row. For a four-word vocabulary

$$
(\text{cat},\text{dog},\text{car},\text{tree})
$$

with three-dimensional embeddings, suppose

$$
\mathbf E=
\begin{bmatrix}
0.2&0.1&-0.4\\
0.7&-0.2&0.4\\
-0.3&0.8&0.1\\
0.5&0.2&0.6
\end{bmatrix}.
$$

The word `dog` has index $2$, so $\mathbf q_2=(0,1,0,0)^T$. Therefore

$$
\mathbf e(\text{dog})=\mathbf E^T\mathbf q_2
=
\begin{bmatrix}0.7\\-0.2\\0.4\end{bmatrix}.
$$

In code, constructing the mostly-zero one-hot vector would be wasteful; an embedding lookup directly retrieves the row stored for that vocabulary index. The route is

$$
\text{token}\longrightarrow\text{vocabulary index}
\longrightarrow\text{row of }\mathbf E
\longrightarrow\text{embedding vector}.
$$

The same matrix $\mathbf E$ is used at every token position. It may be initialized from a pretrained model or learned with the classifier. If it is trainable, backpropagation updates the rows used by the current batch along with the FFNN weights.

### Vocabulary details

Before lookup, a tokenizer maps every token to an integer index. An unknown-token entry handles tokens outside the chosen vocabulary. Variable-length examples are commonly padded to a shared length; a mask excludes padding from pooling and from any position-wise loss. Padding is a batching device, not meaningful text.

An embedding can capture useful similarities between words, but those similarities depend on its training data and objective. Semantic similarity and analogy behavior are learned properties, not mathematical guarantees.

## 3. Combining word vectors

### Mean pooling

For a document containing $m$ tokens, mean pooling produces

$$
\mathbf x=\frac{1}{m}\sum_{i=1}^{m}\mathbf e(w_i).
$$

The result has $d_e$ components regardless of document length.

For example, if two word vectors are $(1,2)^T$ and $(3,0)^T$, the pooled document vector is $(2,1)^T$. This vector can be passed to an MLP.

Pooling loses word order: “dog bites man” and “man bites dog” have the same mean if they contain the same words. It is therefore useful when an approximate bag-of-words representation is adequate, but insufficient when order determines meaning.

If a sequence contains padding, use a mask $m_i\in\{0,1\}$ and average only real tokens:

$$
\mathbf x=
\frac{\sum_{i=1}^{L}m_i\mathbf e(w_i)}{\sum_{i=1}^{L}m_i}.
$$

### Max pooling

Max pooling takes the largest value in each embedding coordinate across the document. It retains strong coordinate responses and also loses word order.

### Concatenation

For a fixed context of $m$ words, concatenate the embeddings:

$$
\mathbf x=
\begin{bmatrix}
\mathbf e(w_1)\\
\mathbf e(w_2)\\
\vdots\\
\mathbf e(w_m)
\end{bmatrix}
\in\mathbb R^{m d_e}.
$$

Unlike pooling, concatenation preserves the positions because each word occupies its own block. It requires a fixed number of positions, or a specified truncation and padding rule.

The choice is a trade-off. Pooling is compact and accepts different document lengths but discards order. Concatenation retains order within its window but increases the input size from $d_e$ to $m d_e$ and cannot directly accept a different window length.

## 4. A feedforward language model

A language model predicts the probability of a next word given a preceding context. A feedforward model uses a fixed window:

$$
P(w_t\mid w_{t-m},\ldots,w_{t-1}).
$$

It looks up the $m$ context embeddings, concatenates them, processes that vector through hidden layers, and produces $V$ scores. Softmax converts those scores into probabilities over the vocabulary.

$$
\mathbf a=g(\mathbf W\mathbf x+\mathbf b),
\qquad
\mathbf z=\mathbf U\mathbf a+\mathbf c,
\qquad
\hat{\mathbf y}=\operatorname{softmax}(\mathbf z).
$$

If the actual next word has vocabulary index $r$, the training loss is $-\log\hat y_r$. Training adjusts the embeddings and network weights to assign more probability to observed next words.

For example, with a context window of three tokens, the sentence fragment

$$
\text{``for all the fish''}
$$

creates the training pair

$$
(\text{for},\text{all},\text{the})\longrightarrow\text{fish}.
$$

The input is

$$
\mathbf x=
[\mathbf e(\text{for});\mathbf e(\text{all});\mathbf e(\text{the})]
\in\mathbb R^{3d_e}.
$$

The network produces one logit for every vocabulary item, and softmax converts those $V$ logits into $P(w_t=j\mid w_{t-3:t-1})$. If `fish` has index $r$, cross-entropy selects $-\log\hat y_r$. At inference time, $\arg\max_j\hat y_j$ returns the most probable next token; the full distribution is retained when alternatives must be sampled.

A corpus supplies many such examples by sliding the window one position at a time. The target word never enters the input for its own prediction.

Embeddings allow related contexts to share statistical strength. A model that learned “cat gets fed” may assign useful probability to “dog gets fed” because `cat` and `dog` can have similar vectors. The model nevertheless remains limited to its fixed window; tokens before that window have no influence on the prediction.

## 5. Processing a batch with matrices

Chapter 4 treats an individual example as a column vector. For a batch of $B$ examples, place each example in a column:

$$
\mathbf X\in\mathbb R^{d\times B}.
$$

For text, this matrix is obtained in stages. Under this chapter's column-example convention, padding $B$ sequences to length $L$ gives a token-index array of shape $L\times B$. Embedding lookup produces a tensor with shape $d_e\times L\times B$. Pooling across the $L$ token positions gives $\mathbf X\in\mathbb R^{d_e\times B}$; concatenating a fixed window gives $\mathbf X\in\mathbb R^{Ld_e\times B}$. A padding mask ensures that artificial padding positions do not affect the representation. Libraries often store the same tensor as $B\times L\times d_e$; the data is equivalent, but the axis order differs.

The batch forward pass is

$$
\mathbf Z^{(1)}
=\mathbf W^{(1)}\mathbf X
+\mathbf b^{(1)}\mathbf 1_B^T,
\qquad
\mathbf A^{(1)}=g(\mathbf Z^{(1)}),
$$

$$
\mathbf Z^{(2)}
=\mathbf W^{(2)}\mathbf A^{(1)}
+\mathbf b^{(2)}\mathbf 1_B^T.
$$

The vector $\mathbf 1_B$ contains $B$ ones. Multiplying a bias column by its transpose repeats the bias for every example. Libraries normally perform this operation through broadcasting.

| Object | Shape |
|---|---|
| Inputs $\mathbf X$ | $d\times B$ |
| Hidden scores and activations | $h\times B$ |
| Output scores and predictions | $k\times B$ |

For multiclass classification, softmax is applied to each output column separately. Examples must not compete with one another in the normalization.

Some sources place examples in rows instead. That convention is equally valid, but all multiplications must be transposed consistently. A formula written for row examples cannot be inserted unchanged into the column convention.

### Batch gradients

Let $\boldsymbol\Delta^{(l)}$ collect the individual-example deltas in columns, before division by $B$. For the mean batch loss,

$$
\frac{\partial J}{\partial\mathbf W^{(l)}}
=\frac{1}{B}\boldsymbol\Delta^{(l)}
(\mathbf A^{(l-1)})^T,
$$

$$
\frac{\partial J}{\partial\mathbf b^{(l)}}
=\frac{1}{B}\boldsymbol\Delta^{(l)}\mathbf 1_B.
$$

The weight multiplication sums the contribution from every example; the bias gradient sums each unit's deltas. Division by $B$ makes both gradients averages. If deltas already include that division, do not divide again.

## 6. What hidden layers add

A linear classifier forms a boundary in the supplied feature space. It cannot separate XOR in the original two input coordinates, but it can do so if an appropriate nonlinear feature is supplied. An MLP learns such transformations internally.

In a text classifier, this means a hidden unit can respond to a learned combination of embedding coordinates rather than to one manually named feature. Individual coordinates or neurons should not automatically be interpreted as complete linguistic concepts; their meaning arises from how the network uses the whole representation.

Depth can allow some functions to be represented more efficiently through successive transformations. It does not guarantee meaningful abstraction, successful optimization, or better test accuracy.

Under appropriate activation assumptions, a sufficiently wide hidden layer can approximate a continuous function arbitrarily closely on a compact domain. This universal-approximation result concerns representational capacity. It does not say that every function can be represented exactly, or that a training algorithm will discover the required weights.

## Chapter summary

Text classification requires a numerical representation before an FFNN can operate. Embeddings provide word vectors; pooling creates a fixed-size document vector, while concatenation preserves positions in a fixed context. The resulting network is trained with the same forward propagation, loss, backpropagation, and optimizer steps developed in Chapter 4.

Batch vectorization carries out those calculations for many examples together. Clear conventions for example orientation, bias broadcasting, and loss averaging keep the matrix equations correct.

## Review questions

1. With $V=1000$ and $d_e=20$, what is the shape of the embedding matrix? Show how a one-hot vector selects one embedding.
2. Why does mean pooling lose word order?
3. What is the input dimension when five embeddings of dimension ten are concatenated?
4. Why does a feedforward language model need a specified context window?
5. For $d=4$, $h=3$, $k=2$, and $B=8$, give the shapes of the batch inputs, hidden activations, output predictions, and weight gradients.
6. Explain why a batch bias gradient must combine the deltas of all examples.
7. Trace `for all the` through a three-word feedforward language model, giving the input dimension and the number of output probabilities.

## Source guide

- Embedding lookup, pooling, text classification, and the fixed-window language model: *6.pdf*, Sections 6.4-6.5.
- Feedforward architecture, training, representation capacity, and NLP batching: *03-4-5.feedforward-nets.pdf*.
- Batch notation and task-dependent outputs: *in3050_lecture_07_ffnn_2025.pdf*, pages 5-23.
- The derivation of individual-example gradients is in [Chapter 4](Multilayer_Backpropagation.md).
