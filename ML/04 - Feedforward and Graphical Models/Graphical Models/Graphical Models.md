# Chapter 4, Part 2. Graphical Models

[Contents](../../readme.md)

## 4.3 Hidden Markov models

An HMM has hidden states $y_t$, observations $x_t$, initial state probabilities $\pi$, transitions $A$, and emissions $B$. The next state depends on the current state, and the observation at time $t$ depends on $y_t$. Thus

$$
P(x,y)=\pi_{y_1}B_{y_1}(x_1)\prod_{t=2}^n A_{y_{t-1}y_t}B_{y_t}(x_t).
$$

| Question | Method | What it computes |
|---|---|---|
| How likely is the observation sequence? | Forward | Sum of joint probabilities over state paths |
| How likely is a particular state at a position? | Forward–backward | State or edge posterior marginal |
| Which complete state path is best? | Viterbi | Maximize over paths, then trace predecessors |
| How do we estimate parameters from unlabeled observations? | Baum–Welch | Use expected counts, then renormalize the probability parameters |

Initialize $\alpha_1(j)=\pi_j B_j(x_1)$. Recur with $\alpha_t(j)=B_j(x_t)\sum_i\alpha_{t-1}(i)A_{ij}$, and sum the last row for $P(x)$. Backward values start at $\beta_n(i)=1$ and use $\beta_t(i)=\sum_j A_{ij}B_j(x_{t+1})\beta_{t+1}(j)$. Then $P(y_t=i\mid x)=\alpha_t(i)\beta_t(i)/P(x)$. For Viterbi, initialize $v_1(j)=\pi_jB_j(x_1)$ and compute $v_t(j)=B_j(x_t)\max_i[v_{t-1}(i)A_{ij}]$. Store the maximizing predecessor for each $t,j$. Start at the largest final score and trace those pointers backward. Dense transitions cost $O(nK^2)$ for $K$ states.

**Worked example.** For two states use $\pi=(0.6,0.4)$, transition rows $(0.7,0.3)$ and $(0.4,0.6)$, and emissions for symbol $a$ of $(0.5,0.2)$. For $aa$:

- $\alpha_1=(0.30,0.08)$.
- $\alpha_2(1)=0.5(0.30\cdot0.7+0.08\cdot0.4)=0.121$.
- $\alpha_2(2)=0.2(0.30\cdot0.3+0.08\cdot0.6)=0.0276$.
- Therefore $P(aa)=0.1486$. The best single path is $1\to1$ with joint probability $0.105$.

### Markov chains and tagging

A Markov chain has observable states and transition matrix $A$. If $\mu_t$ is a row vector of state probabilities, $\mu_{t+1}=\mu_t A$. A stationary distribution $\mu$ satisfies $\mu=\mu A$ and $\sum_i\mu_i=1$. A finite irreducible, aperiodic chain converges to its unique stationary distribution. Estimate transition rows by dividing each outgoing transition count by the total count out of that state.

In an HMM part-of-speech tagger, words are observed and tags are hidden. A bigram tagger uses one previous tag. A trigram tagger can use a pair of previous tags as its state. Add start and end transitions when the model includes them. Sparse transition and emission counts often need smoothing or an unknown-word rule.

### Backward probabilities, decoding, and learning

In the same example, $\beta_1(1)=0.7(0.5)+0.3(0.2)=0.41$. Therefore $P(y_1=1\mid aa)=0.30(0.41)/0.1486\approx0.828$. This is a probability for one position. The Viterbi probability $0.105$ above is the joint probability of the best path and the observations.

For labeled sequences, estimate transitions and emissions by normalizing observed counts. With hidden labels, Baum–Welch uses forward–backward to estimate the counts, then normalizes them to update $\pi$, $A$, and $B$. This is the HMM form of EM. For long sequences, rescale messages or work in log space to avoid underflow. The forward calculation uses log-sum-exp, while Viterbi takes a maximum.

For the transition from $i$ at $t$ to $j$ at $t+1$, the expected count at that edge is

$$
\xi_t(i,j)=\frac{\alpha_t(i)A_{ij}B_j(x_{t+1})\beta_{t+1}(j)}{P(x)}.
$$

Sum this over positions and training sequences, then normalize over destinations $j$ to update the transition row. For emissions, sum state marginals where the matching symbol was observed and normalize over symbols.

## 4.4 Markov networks, belief propagation, and entropy

A Markov network is an undirected graph. Its joint distribution can be written as $P(x)=Z^{-1}\prod_c\psi_c(x_c)$ using positive clique potentials. The neighbors of a node separate it from the rest of the graph. The partition function $Z$ sums the unnormalized weights over all configurations.

**Belief propagation** passes summaries along graph edges. A sum-product message multiplies the appropriate local factor by incoming messages, then sums out variables that the receiving node does not need. For a pairwise model with node potential $\phi_i$ and edge potential $\psi_{ij}$, the message is $m_{i\to j}(x_j)=\sum_{x_i}\phi_i(x_i)\psi_{ij}(x_i,x_j)\prod_{k\in N(i)\setminus\{j\}}m_{k\to i}(x_i)$. Normalize the resulting beliefs to get probabilities. On trees this gives exact marginals. A chain needs a left-to-right and a right-to-left pass. Replacing sums by maxima gives max-product, which recovers the best chain assignment with backpointers.

Entropy is $H(p)=-\sum_xp(x)\log p(x)$. A fair binary variable has one bit of entropy with base-2 logs, or $\ln 2$ nats with natural logs. Use the same base throughout an entropy, cross-entropy, or KL calculation. Cross-entropy $H(p,q)=-\sum_xp(x)\log q(x)$ measures the cost of predicting with $q$ when outcomes follow $p$. The identity $H(p,q)=H(p)+D_{\mathrm{KL}}(p\Vert q)$ explains why cross-entropy minimization fits the predictive distribution.

## 4.5 Linear-chain CRFs

A linear-chain CRF models $P(y\mid x)$ directly. A token feature can fire when the word is *Kolkata* and its tag is **B-LOC**. A transition feature can fire when **O** is followed by **B-LOC**. In BIO tagging, B begins an entity, I continues it, and O is outside an entity. Each feature has a learned weight, and the total score sums the weights of features that fire.

With token scores $E_t(b)$ and adjacent-label scores $T(a,b)$, and zero start/end scores,

$$
S(x,y)=\sum_t E_t(y_t)+\sum_{t\ge2}T(y_{t-1},y_t),\qquad
P(y\mid x)=\frac{e^{S(x,y)}}{Z(x)},\qquad
Z(x)=\sum_{y'}e^{S(x,y')}.
$$

| Model | Distribution | Observation features |
|---|---|---|
| HMM | Joint $P(x,y)$ | Emission probabilities model how each hidden state generates an observation. |
| Linear-chain CRF | Conditional $P(y\mid x)$ | Label scores can use overlapping clues such as capitalization, suffixes, and surrounding words. |

Both can decode with Viterbi. An HMM scores transitions and emissions with probabilities. A CRF uses feature scores and normalizes over label sequences for the given input. Dense linear-chain inference costs $O(nK^2)$ for $n$ positions and $K$ labels.

### Worked decoding. “Riya lives in Kolkata”

Use P for person, O for outside, and L for location. The table gives the token scores and best prefix scores in P, O, L order. Transition rows in the same order are $(-0.5,1.5,-0.5)$, $(-0.5,0.7,1.2)$, and $(-0.5,0.3,-0.5)$.

| Token | Token scores | Best prefix scores |
|---|---|---|
| Riya | $(2.5,0.2,-0.5)$ | $(2.5,0.2,-0.5)$ |
| lives | $(-0.5,1.0,-0.5)$ | $(1.5,5.0,1.5)$ |
| in | $(-0.6,0.8,-0.5)$ | $(3.9,6.5,5.7)$ |
| Kolkata | $(-1.0,0.1,2.8)$ | $(5.0,7.3,10.5)$ |

Initialize $V_1=E_1$. For each next token, compute $V_t(b)=E_t(b)+\max_a[V_{t-1}(a)+T(a,b)]$. At “in”, the L entry is $5.0+1.2-0.5=5.7$, from the preceding O. For the final L entry, the best predecessor is O, giving $6.5+1.2+2.8=10.5$. Following the stored predecessors gives **P, O, O, L**. Since $Z(x)$ is shared by all labelings of this sentence, decoding can maximize the score directly.

### Worked training step. From scores to expected counts

In the two-token example “Sohan Kolkata”, the first-token scores are $(2,0)$ and the second-token scores are $(0,2)$ for labels P and L. Start and end scores are zero. The only nonzero transition is $T(P,L)=1$.

| Path | PP | PL | LP | LL |
|---|---:|---:|---:|---:|
| Score | 2 | 5 | 0 | 2 |
| Unnormalized weight | $e^2$ | $e^5$ | $1$ | $e^2$ |

1. **Normalize.** $Z=2e^2+e^5+1\approx164.19$, so $P(PL\mid x)\approx0.904$.
2. **Compute the node marginal.** The “Kolkata tagged L” feature fires for PL and LL. Its expected count is $(e^5+e^2)/Z\approx0.949$.
3. **Compute the edge marginal.** P→L occurs only in PL. Its expected count is $e^5/Z\approx0.904$. If the gold path is PL, observed minus expected is $1-0.904\approx0.096$. A learning rate of $0.1$ increases this transition weight by about $0.0096$.

These token and transition scores describe a model at one moment. Training sets the scores by updating feature weights. For longer chains, let $\psi_t(a,b)=e^{T(a,b)+E_t(b)}$. The forward pass sums prefix weights and the backward pass sums suffix weights.

$$
\begin{aligned}
\alpha_1(b)&=e^{E_1(b)}, & \alpha_t(b)&=\sum_a\alpha_{t-1}(a)\psi_t(a,b),\\
\beta_n(b)&=1, & \beta_{t-1}(a)&=\sum_b\psi_t(a,b)\beta_t(b),\\
Z(x)&=\sum_b\alpha_n(b).
\end{aligned}
$$

In the two-token example, $\alpha_2(P)=e^2+1$ and $\alpha_2(L)=e^5+e^2$. To find expected feature counts, sum node marginals $\alpha_t(b)\beta_t(b)/Z$ where a token feature fires, or edge marginals $\alpha_{t-1}(a)\psi_t(a,b)\beta_t(b)/Z$ where a transition feature fires. For an L2-regularized log-likelihood, the ascent gradient is observed minus expected minus $\lambda w$.

**Count the edges before updating.** For the gold sequences POL, POL, LOO, LOO, each of P→O, O→L, L→O, and O→O occurs twice. The total is eight edges.

### Extensions beyond a linear chain

| Model or inference choice | Consequence |
|---|---|
| Maximum entropy Markov model (MEMM) | Normalizes each next-label distribution locally. This can produce label bias because states with few possible exits redistribute all their probability among those exits. |
| Linear-chain CRF | Normalizes over whole label paths. Forward–backward and Viterbi use the chain to compute exact marginals and decoding. |
| Skip-chain CRF | Adds edges between related nonadjacent positions, such as repeated capitalized names. Loops can make exact inference expensive, so loopy belief propagation is one approximation. |
| Pseudolikelihood | Fits local conditional distributions of labels given neighbors. It avoids computing the full partition function during fitting. |

These extensions show why the graph matters for training. The CRF tutorial in the chapter sources develops the general factorized model and the skip-chain example.

## Practice

- **4B.** For the HMM example, compute $P(a)$ and explain the difference between forward and Viterbi.
- **4C.** Four CRF paths PP, PL, LP, LL have scores $0,1,0,0$. Compute $Z$, the probability of PL, and the expected count for the second label being L.

## Answer checks

| Exercise | Answer |
|---|---|
| 4B | $P(a)=0.30+0.08=0.38$. Forward sums all path probabilities. Viterbi retains the best path and its predecessors. |
| 4C | $Z=e+3\approx5.7183$, $P(PL\mid x)\approx0.4754$, and $P(y_2=L\mid x)=P(PL\mid x)+P(LL\mid x)\approx0.6502$. |
[Chapter sources](../../readme.md#graphical-models)

---

[← Feedforward Networks](../Feedforward%20Networks/Feedforward%20Networks.md) · [Contents](../../readme.md) · [Chapter 5 →](../../05%20-%20Probabilistic%20Networks/Probabilistic%20Networks.md)
