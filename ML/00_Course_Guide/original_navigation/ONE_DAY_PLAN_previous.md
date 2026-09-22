# One-day ML plan: through FFNN

**Goal:** prepare exam answers and solve standard numerical problems for Units 1–3 plus FFNN training. This is a focused revision/first-pass plan, not a promise of mastery from zero in one day. Assume 10 hours 15 minutes of focused work spread across 12 hours 15 minutes. Start at any time and shift the example clock below. Preserve a normal night's sleep before the exam.

Read [corrections and missing basics](EXAM_PATCH_AND_PRACTICE.md) alongside your notes. Use [the syllabus map](SYLLABUS_MAP.md) to track scope. There is no ML past paper here, so these time allocations are study priorities, not a marks forecast.

| Example time | Duration | Work                                                                                                                                                                                                                     | Required output before moving on                                                                                        |
| ------------ | -------: | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------- |
| 08:00–08:15  |   15 min | Setup and diagnostic: attempt to state Bayes, perceptron update, forward pass and gradient update without notes.                                                                                                         | Mark each green/amber/red; open only the next block's files.                                                            |
| 08:15–09:00  |   45 min | Unit 1: Intro_to_ML; formal model/runtime section in the supplement. Focus learning paradigms, applications, design issues, task/experience/performance and generalization.                                              | One page of definitions; explain supervised vs unsupervised with examples.                                              |
| 09:00–10:30  |   90 min | Unit 2 Bayes: Bayesian_Learning; lec04-BayesianLearning PDF pp. 31–41. First Bayes and MAP/ML; then concept learning, Bayes optimal, naive Bayes and belief-network factorization.                                       | Solve one posterior, one naive Bayes problem and one Bayes-optimal vote. Draw one belief network.                       |
| 10:30–10:45  |   15 min | Break.                                                                                                                                                                                                                   | Leave the desk.                                                                                                         |
| 10:45–11:30  |   45 min | Unit 2 evaluation: supplement on test accuracy, binomial sampling, standard error and fair algorithm comparisons.                                                                                                        | Solve the 80/100 accuracy exercise; explain train/validation/test roles.                                                |
| 11:30–12:30  |   60 min | Unit 3: Neural_Networks_Basics; L03 Multilayer Perceptrons pp. 2–7. Activation derivatives, perceptron rule, separability, XOR and MLP architecture.                                                                     | Draw a neuron and MLP; perform one perceptron update; explain why XOR needs a nonlinear hidden representation.          |
| 12:30–13:15  |   45 min | Lunch.                                                                                                                                                                                                                   | Rest.                                                                                                                   |
| 13:15–14:00  |   45 min | Unit 4 forward computation, output/loss choices and empirical risk. Multilayer_Backpropagation with supplement; lec3.learning pp. 131–137 if needed.                                                                     | Write z=Wa+b and a=g(z); compute a forward pass; distinguish population risk, empirical risk and regularized objective. |
| 14:00–15:30  |   90 min | Backpropagation: lec21.notes pp. 4–8; supplement's worked example. First follow it, then cover the answer and redo it.                                                                                                   | Derive output and hidden deltas; calculate gradients and update weights and biases without mixing signs.                |
| 15:30–15:45  |   15 min | Break.                                                                                                                                                                                                                   | Leave the desk.                                                                                                         |
| 15:45–16:45  |   60 min | Training practice: initialization, batch/SGD/mini-batch, learning rate, momentum, regularization, early stopping, validation and model selection. Use the training summary and selected 13_Multilayer_Perceptron slides. | Explain zero-weight symmetry; compare SGD/momentum/Adam at a high level; diagnose underfitting/overfitting.             |
| 16:45–17:30  |   45 min | Mixed numerical practice from the supplement. Redo Bayes, perceptron, parameter counting and backprop with no notes.                                                                                                     | Identify and correct every sign, bias and normalization mistake.                                                        |
| 17:30–18:00  |   30 min | Meal/break.                                                                                                                                                                                                              | Rest.                                                                                                                   |
| 18:00–19:00  |   60 min | Closed-book mock using questions below.                                                                                                                                                                                  | Finish within 60 minutes; do not pause to read.                                                                         |
| 19:00–19:15  |   15 min | Break.                                                                                                                                                                                                                   | Rest.                                                                                                                   |
| 19:15–20:00  |   45 min | Repair only mock errors.                                                                                                                                                                                                 | One handwritten formula sheet and one list of remaining weaknesses.                                                     |
| 20:00–20:15  |   15 min | Final recall: explain the whole training pipeline aloud.                                                                                                                                                                 | Stop and prepare for sleep; no new long PDF.                                                                            |

**Totals:** 615 minutes focused study and 120 minutes meals/breaks = 12 hours 15 minutes elapsed. Within longer blocks, use short stand-up pauses as needed; trim reading, not the closed-book practice.

## Closed-book mock: 60 minutes

1. 5 min: define a learning problem using task, experience and performance; contrast supervised and unsupervised learning.
2. 10 min: calculate the Bayes/naive Bayes exercise from the supplement; explain MAP versus Bayes optimal; factorize a three-node belief network.
3. 5 min: estimate accuracy and its approximate interval; explain why the test set must not select the model.
4. 10 min: perceptron update, XOR limitation and activation derivatives; count parameters in a 2–3–1 MLP.
5. 20 min: perform the supplement's complete forward/backward/update calculation from the stated inputs; explain the chain rule and loss-dependent output delta.
6. 10 min: explain empirical risk, initialization, regularization and early stopping; compare batch, stochastic and mini-batch training.

For each answer mark: correct / incomplete / wrong. Repair wrong numerical answers first, then missing syllabus definitions. Reusing exercises tests recall and procedure; change one input or weight afterward to check understanding.

## If only six focused hours remain

Use: Unit 1 25 min; Bayes 55 min; evaluation 35 min; ANN 40 min; forward pass/loss/risk 35 min; backprop 80 min; training/regularization 40 min; closed-book mixed practice 50 min. Total 360 min, plus breaks. Skip long history, detailed DAQ, EM/MDL derivations, NLP embeddings and optimizer derivations unless specifically emphasized by your teacher.

## Scope boundary

“Up until FFNN” is taken to include FFNN training through optimization. HMMs, CRFs and entropy follow that material in the same printed syllabus block, so their inclusion remains unconfirmed. They are separated under Unit 4 / `02_HMM_CRF_and_Entropy`. If your teacher confirms all of Unit 4, this plan needs extra time or reduced depth elsewhere: reserve 45 minutes for HMM states/transitions/emissions and forward/Viterbi, 45 minutes for CRF conditional modeling/partition function/belief propagation/training, and 15 minutes for entropy. Treat that as a minimal survey, not complete coverage.

## Ready-to-stop checklist

- [ ] Define ML and compare learning paradigms with examples.
- [ ] Explain formal learning setup, generalization and basic training/inference cost.
- [ ] Calculate Bayes, MAP, naive Bayes and Bayes-optimal classification.
- [ ] Factorize a belief network; explain conditional independence and concept learning.
- [ ] Estimate test accuracy and sampling uncertainty; compare algorithms fairly.
- [ ] Apply a perceptron update; explain XOR and nonlinear hidden layers.
- [ ] Choose output activation and loss; compute a forward pass.
- [ ] Derive hidden/output gradients and perform one full update, including biases.
- [ ] Explain risk, initialization, optimizers, regularization and validation.

Start now with the 15-minute diagnostic, then Unit 1. Read each topic briefly, close the notes and produce the required output.
