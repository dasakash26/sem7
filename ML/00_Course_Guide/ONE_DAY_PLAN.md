# One-day plan — based on mam’s coursework

**Your exam: two questions of 15 marks each. Current CT scope: Introduction, Bayesian learning, ANN, MLP/FFNN, HMM and CRF.**

Use **[the enhanced CT notes](../ct1.md)** as your main book today. Read its six modules in order. The later handwritten CT sheet supersedes the earlier lectures 1–9 scope and explicitly includes HMM and CRF. See the [exact source map](CT_SCOPE_MAP.md).

**The whole day:** ML pipeline and metrics → Bayes → perceptrons and gates → FFNN training → HMM → CRF → long-answer practice. Budget **10 hours 15 minutes of focused study**, plus meals and breaks. Take a short break after roughly each hour and a meal after Step 2 or 3. Start whenever you can; no fixed clock is needed.

The exam may combine subparts from several modules. Prepare all six modules; do not assume one question will be Bayes and the other backpropagation. Internal choice and the actual exam duration are still unknown.

## 1. Learn the ML workflow — 75 minutes

**Read Module 1 of the CT notes.**

Spend 15 minutes on the learning definition, task/experience/performance, supervised/unsupervised/semi-supervised learning, classification and regression. Spend 35 minutes following one example through acquisition → cleaning → scaling/encoding → EDA → feature engineering/selection → model selection → validation → testing. Spend 25 minutes on overfitting and the confusion matrix.

Pay attention to normalization versus standardization, filter/wrapper/embedded feature selection, parameters versus hyperparameters, cross-validation and leakage. These are prominent in the supplied coursework; they should not be skipped as generic introductory material.

**Before moving on:** draw the pipeline from memory and solve this: TP=30, TN=50, FP=10, FN=10. Find accuracy, precision, recall and F1. Answer check: 80%, 75%, 75%, 75%.

**Answer you are preparing:** “Explain the stages of an ML project, including preprocessing, feature selection, model selection and evaluation.”

## 2. Learn Bayesian prediction — 105 minutes

**Read Module 2 of the CT notes.**

Spend 30 minutes on conditional probability, Bayes theorem, prior/likelihood/posterior and MAP versus ML. Spend 30 minutes solving a posterior example and comparing class scores. Spend 30 minutes on naive Bayes text classification, word counts, smoothing and log scores. Finish with 15 minutes drawing a Bayesian network and writing its joint factorization.

For text classification, word likelihood denominators count **tokens within the class**, plus vocabulary size for Laplace smoothing. Class priors count **documents**. Repeated words contribute repeatedly to the document score.

**Before moving on:** reproduce the MAP derivation and solve the short text-classification exercise in [coursework practice](COURSEWORK_REASSESSMENT.md). Explain the conditional-independence assumption aloud.

**Answer you are preparing:** “Explain Bayesian learning, derive MAP, distinguish it from ML, and demonstrate naive Bayes classification.” Also prepare a DAG/CPT explanation for a possible subpart.

## 3. Build a network from perceptrons — 75 minutes

**Read Module 3 of the CT notes.**

Spend 20 minutes on biological-to-artificial neuron mapping, weights, bias/threshold, activations and network topologies. Spend 35 minutes on perceptron output/update and implementing AND, OR, NOT and NAND. Spend 20 minutes on XOR and how a hidden layer makes an MLP more expressive.

**Before moving on:** draw a neuron, perform one weight-and-bias update, and verify every truth-table row for AND and OR. Then explain why one straight boundary cannot represent XOR. Use the gate weights already given in the CT notes.

**Answer you are preparing:** “Explain the perceptron model and learning rule, realize logic gates, and discuss its limitation and the role of an MLP.”

## 4. Train the network — 135 minutes

**Read Module 4 of the CT notes.** Follow its sequence instead of switching between slide decks.

- **30 minutes — predict and measure error:** forward equations, matrix dimensions, output activation choices, MSE and cross-entropy. Calculate a small forward pass and a loss.
- **60 minutes — work backward:** start with the computation graph f=(x1+x2)x3−x4; compute all input derivatives. Then learn output and hidden deltas, weight/bias gradients and gradient-descent updates. Work the small neural-network numerical in section 6 of [the exam supplement](EXAM_PATCH_AND_PRACTICE.md), cover the answer and repeat it.
- **30 minutes — make training work:** initialization, learning rate, batch/SGD/mini-batch, local minima/saddle regions, momentum, vanishing gradients and early stopping. Know regularization and dropout conceptually.
- **15 minutes — explain the whole process:** draw the training loop and write an answer outline from initialization to final evaluation. Explain why backpropagation does not make an FFNN recurrent.

**Before moving on:** solve both a computation-graph derivative and a weight update. Do not just memorize the training-loop arrows.

**Answer you are preparing:** “Explain FFNN forward propagation, loss and backpropagation using the chain rule, and discuss training with momentum and early stopping.”

Read the short correction about elementwise activations versus softmax in [coursework reassessment](COURSEWORK_REASSESSMENT.md) before copying the general output-delta formula.

## 5. Hidden Markov Models — 90 minutes

Read Module 5 of the CT notes and work beside [the detailed HMM/CRF note](../04_Feedforward_Networks_and_Graphical_Models/02_HMM_CRF_and_Entropy/notes/HMM_and_CRF.md). Learn the state/observation diagram, Markov and output-independence assumptions, $\pi/A/B$, and the three main problems. Derive the forward recursion and solve its two-step example. Then distinguish forward–backward, Viterbi and Baum–Welch by purpose.

**Before moving on:** draw an HMM from memory, write the joint probability, and explain why forward sums while Viterbi maximizes.

## 6. Conditional Random Fields — 45 minutes

Read Module 6. Learn the linear-chain diagram, $P(\mathbf y\mid\mathbf x)$, feature functions, weights and partition function. Understand training as empirical feature counts minus model-expected counts, and know that forward–backward and Viterbi-style decoding are reused.

**Before moving on:** explain HMM versus CRF without notes: joint/generative versus conditional/discriminative, emissions versus flexible features, and local versus global normalization.

## 7. Write in the exam format — 90 minutes

Use 60 minutes for two 15-mark practice questions, 30 minutes each. This is a practice allocation, not a claim about your exam duration. Follow the real time limit when it is available.

**Practice question 1 — 15 marks**

(a) Explain the ML pipeline, including preprocessing, feature selection and model evaluation. **5 marks**  
(b) Derive MAP and state its relation to maximum likelihood. **4 marks**  
(c) Demonstrate naive Bayes text classification using class priors and smoothed word likelihoods. **6 marks**

**Practice question 2 — 15 marks**

(a) Explain a perceptron, realize AND and OR, and explain the XOR limitation. **5 marks**  
(b) Explain FFNN forward propagation and derive the backpropagation updates using the chain rule. **7 marks**  
(c) Explain momentum and early stopping. **3 marks**

**Additional practice question 3 — 15 marks**

(a) Define an HMM and explain its parameters and independence assumptions. **5 marks**  
(b) Derive the forward algorithm with initialization, recursion and termination. **6 marks**  
(c) Distinguish forward–backward, Viterbi and Baum–Welch. **4 marks**

**Additional practice question 4 — 15 marks**

(a) Define a linear-chain CRF and explain its feature functions and partition function. **7 marks**  
(b) Describe CRF training and inference. **4 marks**  
(c) Compare HMM and CRF. **4 marks**

Choose two questions for a timed sitting, then outline the other two. These mark allocations are constructed for practice, not supplied by your teacher.

These questions and mark allocations are constructed for practice, not supplied by your teacher. Use your notes' examples as inputs but keep the solutions closed. In numerical answers show substitutions and intermediate values, including biases.

Spend the remaining 30 minutes as follows:

- **15 minutes:** correct your answers and recite all six modules' key formulas.
- **15 minutes:** read the syllabus-only backup below. These topics are named in the official syllabus but are not developed prominently in the new coursework summary.

## Short syllabus backup — included in the final 15 minutes

Use sections 1–3 and 5 of [the exam supplement](EXAM_PATCH_AND_PRACTICE.md) only for: formal model/runtime; accuracy sampling uncertainty; noise-free concept learning; Bayes-optimal versus MAP classification; population versus empirical risk. Learn the definitions and one accuracy example. This is minimum backup coverage, not full preparation for a long sampling-theory question.

If your teacher separately confirmed these topics, promote them into the main study blocks and extend the day or reduce time on already-mastered topics. Their absence from a summary is not proof they cannot be examined.

## What to leave for later

Units 5–8 of the university syllabus are beyond the CT topic sheet. Detailed NLP embedding architectures and detailed Adam/Xavier derivations are lower priority than the six CT modules today. HMM and CRF are included and must not be skipped. Keep PDFs as help for a specific sticking point; do not read every overlapping source.

**Start now:** open the CT notes at Module 1. Keep one sheet for formulas and one for mistakes. Your first task is the ML pipeline and confusion-matrix numerical.
