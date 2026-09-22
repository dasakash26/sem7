# Hidden Markov Models and Conditional Random Fields

## CT sources

- [HMM.pdf](../sources/HMM.pdf): PDF pages 1–29.
- [7bf751479f644ef0785964a0151a1180cb48.pdf](../sources/7bf751479f644ef0785964a0151a1180cb48.pdf): PDF pages 1–50.
- CRF is marked as video-based on the handwritten CT sheet; no exact video URL was supplied. [crf-tutorial.pdf](../sources/crf-tutorial.pdf) supports the concepts below but is not being represented as the assigned video.

## One mental model

For sequence labeling, think in three levels:

1. A **Markov chain** has visible states that depend on the previous state.
2. An **HMM** hides those states and lets each hidden state generate an observation.
3. A **CRF** is given the observations and directly scores possible label sequences using flexible features.

```text
Markov chain:  S1 → S2 → S3

HMM:           S1 → S2 → S3       hidden
                ↓    ↓    ↓
                O1   O2   O3       observed

CRF:           Y1 — Y2 — Y3       labels to infer
                 \   |   /
                   X1:T            observed input available to features
```

## Hidden Markov Model

### Assumptions

First-order state dependence:

$$P(S_t\mid S_{1:t-1})=P(S_t\mid S_{t-1}).$$

Current observation depends only on current state:

$$P(O_t\mid S_{1:t},O_{1:t-1})=P(O_t\mid S_t).$$

### Parameters

- Initial distribution: $\pi_i=P(S_1=i)$.
- Transition: $a_{ij}=P(S_t=j\mid S_{t-1}=i)$.
- Emission: $b_j(o)=P(O_t=o\mid S_t=j)$.

Joint path and observation probability:

$$P(s_{1:T},o_{1:T})=\pi_{s_1}b_{s_1}(o_1)\prod_{t=2}^Ta_{s_{t-1}s_t}b_{s_t}(o_t).$$

### Three questions and four algorithms

| Goal | Algorithm | Operation over incoming paths |
|---|---|---|
| Probability of observations | Forward | Sum |
| Probability of a state at a time | Forward–backward | Combine $\alpha_t$ and $\beta_t$, then normalize |
| Most likely complete state path | Viterbi | Maximum plus backpointers |
| Learn unknown parameters | Baum–Welch/EM | Expected counts then normalized updates |

### Forward

$$\alpha_1(j)=\pi_jb_j(o_1)$$

$$\alpha_t(j)=b_j(o_t)\sum_i\alpha_{t-1}(i)a_{ij}$$

$$P(O\mid\lambda)=\sum_j\alpha_T(j)$$

### Backward and smoothing

$$\beta_T(i)=1$$

$$\beta_t(i)=\sum_j a_{ij}b_j(o_{t+1})\beta_{t+1}(j)$$

$$P(S_t=i\mid O,\lambda)=\frac{\alpha_t(i)\beta_t(i)}{\sum_j\alpha_t(j)\beta_t(j)}$$

### Viterbi

$$\delta_1(j)=\pi_jb_j(o_1)$$

$$\delta_t(j)=b_j(o_t)\max_i[\delta_{t-1}(i)a_{ij}]$$

$$\psi_t(j)=\arg\max_i[\delta_{t-1}(i)a_{ij}]$$

Trace the backpointers from the best final state. Forward and Viterbi look similar, but forward sums every path while Viterbi retains the best path.

### Baum–Welch

Baum–Welch is EM for an HMM:

1. Guess parameters.
2. E-step: use forward–backward to obtain expected state and transition counts.
3. M-step: normalize expected counts to update $\pi$, $A$ and $B$.
4. Repeat until convergence.

Its likelihood is non-decreasing per iteration, but it can converge to a local optimum.

### Complexity

For $N$ states and sequence length $T$, forward, backward and Viterbi take $O(N^2T)$ time with dense transitions.

## Conditional Random Field

### Definition

A linear-chain CRF directly models a label sequence given an observed input sequence:

$$P(\mathbf y\mid\mathbf x)=\frac{1}{Z(\mathbf x)}\exp\left(\sum_{t,k}\lambda_kf_k(y_{t-1},y_t,\mathbf x,t)\right).$$

- $f_k$ is a feature function.
- $\lambda_k$ is its learned weight.
- $Z(\mathbf x)$ sums the unnormalized scores of all possible label sequences.

$$Z(\mathbf x)=\sum_{\mathbf y'}\exp\left(\sum_{t,k}\lambda_kf_k(y'_{t-1},y'_t,\mathbf x,t)\right).$$

The partition function makes all $P(\mathbf y\mid\mathbf x)$ values sum to one for the given input.

### Features

A CRF feature may check the current/previous label together with the current word, neighboring words, capitalization, suffixes, digits or dictionary membership. The model does not need to generate the words.

### Training and inference

Training maximizes regularized conditional log-likelihood. Its gradient is:

```text
observed feature counts − model-expected feature counts − regularization
```

Forward–backward calculates $Z(\mathbf x)$ and marginals. Viterbi-style decoding finds the highest-scoring label sequence. Dense linear-chain inference costs $O(TM^2)$ for $M$ labels.

## HMM versus CRF

| HMM | CRF |
|---|---|
| Models $P(\mathbf x,\mathbf y)$ | Models $P(\mathbf y\mid\mathbf x)$ |
| Generative, directed | Discriminative, conditional, undirected |
| Needs transition and emission probabilities | Uses learned feature weights |
| Restrictive observation independence assumption | Allows rich, overlapping observed features |
| Local probability tables normalize | Global partition function normalizes |

Memory sentence: **HMM tells a story of states generating observations; CRF labels observations that are already given.**

## Practice prompts

1. Draw an HMM and label $\pi$, $A$ and $B$.
2. Explain why $P(S_t\mid S_{1:t-1})=P(S_t\mid S_{t-1})$ saves computation.
3. Calculate the forward probability for a two-state, two-observation example.
4. Explain why forward uses a sum and Viterbi uses a maximum.
5. Define the CRF partition function and explain why it is necessary.
6. Compare HMM and CRF in five points.

