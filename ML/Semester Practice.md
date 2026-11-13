# ML semester · closed-book practice

Try the questions before opening the answer checks. Use the [chapter contents](readme.md) to review anything you miss.

## A · ML basics and Bayes

1. Explain the ML pipeline from raw data to final evaluation. Give one example of leakage and how to prevent it.
2. Derive the MAP decision from Bayes’ rule and explain when it agrees with maximum likelihood.
3. For two equally likely classes, vocab size 2, and word counts $(3,1)$ and $(1,3)$ for “offer” and “meeting”, use Laplace smoothing to classify “offer offer meeting”.

## B · ANN and backpropagation

1. Give perceptron parameters for AND and OR. Explain why XOR needs a nonlinear network.
2. For $x=1$, two scalar weights initially 1, zero biases, sigmoid hidden and output activations, target 1 and BCE, compute the forward pass, both weight gradients, and both updated weights at $\eta=0.1$.
3. Explain how momentum and early stopping affect training. State which split selects the checkpoint.

## C · HMM

1. Draw a two-state HMM and define $\pi,A,B$. State the two conditional independence assumptions.
2. With $\pi=(0.6,0.4)$, transition rows $(0.7,0.3),(0.4,0.6)$ and emission probabilities for symbol $a$ of $(0.5,0.2)$, compute $P(a,a)$ by the forward algorithm.
3. Explain what Viterbi, forward–backward, and Baum–Welch each return or estimate.

## D · CRF

1. Write the linear-chain CRF score, probability, and partition function. Give one token feature and one transition feature.
2. For the sequences PP, PL, LP, LL with total scores $0,1,0,0$, compute $Z$, the best sequence, its probability, and the marginal probability that the second label is L.
3. If PL is the gold sequence, calculate the unregularized gradient for the P→L transition weight. Explain how to obtain the expected count for longer sequences.

## E · Hopfield, RBM, and autoencoders

1. Store $(1,-1,1)$ with unscaled Hebbian weights. Starting from $(1,1,1)$, update neuron 2 and calculate the energy change.
2. Compare Hopfield recall, an RBM Gibbs step, and an autoencoder training step. Name what gets updated in each.

## F · Training, CNNs, and RNNs

1. Given 130 examples and batch size 32, find the updates per epoch when the partial batch is kept. Training loss falls for five epochs and validation loss is $(0.7,0.6,0.5,0.52,0.57)$. Which checkpoint would you restore?
2. For a $28\times28\times1$ input, eight $3\times3$ filters, stride 1, no padding, and one bias per filter, find the output shape and parameter count. Explain how changing the previous hidden state affects an RNN with the same current input.

## G · Logistic regression, LDA, and SVM

1. For $z=\ln 3$, compute the logistic probability and log loss when $y=0$.
2. State Fisher’s criterion and explain what happens to a threshold when you rescale its projection direction by a positive constant.
3. For $f(x)=3x_1+4x_2-2$ in canonical SVM scaling, give the unit normal, both margin equations, and the total width.

## H · Reinforcement learning

1. Contrast a contextual bandit and an MDP. Give an example where the choice affects future states.
2. Update $Q(s,a)=1$ after reward 2 with $\gamma=0.9$, $\max_{a'}Q(s',a')=3$, and $\alpha=0.25$. Explain how the target would change if the next state were terminal.

## Answer checks

| Question | Check your result |
|---|---|
| A | Define the target and metric, split the data, fit preprocessing and model parameters on training data, select using validation, and evaluate on the held-out test set. Fitting transforms before the split can leak test information. Under uniform priors MAP equals ML. The class scores are $0.5(4/6)^2(2/6)\approx0.0741$ and $0.5(2/6)^2(4/6)\approx0.0370$, so choose class 1. |
| B | AND uses bias $-1.5$ and OR $-0.5$ with both weights 1. XOR has no single linear boundary. OR and NAND hidden units followed by AND can represent it. Hidden output $\approx0.7311$, predicted output $\approx0.6750$, gradients $\nabla_{w_o}\approx-0.2376$ and $\nabla_{w_h}\approx-0.0639$. Updated weights are about $1.0238$ and $1.0064$. Momentum accumulates a running direction from past gradients. Early stopping selects the lowest validation-loss checkpoint. |
| C | Draw arrows from each state to the next state and to the current observation. $\pi$ gives initial probabilities, $A$ transitions, and $B$ emissions. The next state depends only on the current state, and each observation depends only on its own state. $\alpha_1=(0.3,0.08)$ and $\alpha_2=(0.121,0.0276)$ give $P(a,a)=0.1486$. Viterbi returns the best complete state path, forward–backward gives posterior marginals, and Baum–Welch estimates parameters from expected counts. |
| D | Sum token and transition scores to get $S(x,y)$. The probability is $e^{S(x,y)}/Z(x)$, where $Z(x)$ sums $e^{S(x,y)}$ over all paths. A capitalized word tagged as a person is a token feature. P followed by L is a transition feature. $Z=e+3\approx5.7183$. Best is PL with probability $\approx0.4754$. The node marginal is $(e+1)/(e+3)\approx0.6502$. The P→L gradient is $1-e/(e+3)\approx0.5246$. Sum pairwise marginals over edges for longer sequences. |
| E | Weights into neuron 2 are both $-1$, so its field is $-2$. The flip is $+1\to-1$ and $\Delta E=-(-2)(-2)=-4$. Recall changes Hopfield states, an RBM Gibbs step samples states, and autoencoder training changes parameters by backpropagating reconstruction loss. |
| F | Five updates if you retain the partial batch. Restore epoch 3. CNN output $26\times26\times8$, with $(3\cdot3+1)\cdot8=80$ parameters. The term $W_hh_{t-1}$ changes the next hidden activation. |
| G | $p=0.75$ and loss $-\ln(0.25)\approx1.3863$. Fisher maximizes projected between-class separation relative to within-class scatter. Scale the threshold by the same positive factor as the direction. The unit normal is $(3/5,4/5)$, margins satisfy $3x_1+4x_2-2=\pm1$, and full width is $2/5$. |
| H | A contextual bandit chooses an action for its immediate expected reward given the current context. In an MDP an action can change the next state, such as moving a robot. The target is $4.7$ and the updated value is $1.925$. If terminal, the target is 2 and the new value is $1.25$. |

For a short session, choose two calculations from different units. Check your work, then redo both from memory.

[Worked CRF calculations](04%20-%20Feedforward%20and%20Graphical%20Models/Graphical%20Models/Graphical%20Models.md#45-linear-chain-crfs)
