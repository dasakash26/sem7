# Machine Learning CT2 Guide

**Scope:** Modules 5–7, corresponding to Lectures 15–29 in the attached lecture summary. Module 8 and Lecture 30 are outside the stated scope.

The chapters contain the theory, derivations, diagrams, examples and exercises. Use this guide to choose what to study and assess your preparation; use the [18-hour plan](plan.md) to allocate time.

## Contents

- [Coverage](#coverage)
- [Study priorities](#study-priorities)
- [Practice selection](#practice-selection)
- [Readiness checklist](#readiness-checklist)
- [Timed revision](#timed-revision)
- [Answer presentation](#answer-presentation)
- [Scope references](#scope-references)

## Coverage

### Module 5 Probabilistic Neural Networks

[Read Chapter 5](05%20-%20Probabilistic%20Networks/Probabilistic%20Networks.md), §§5.1–5.3.

| Lectures | Topics | Required preparation |
|---|---|---|
| 15–16 | Hopfield networks | Weight construction, asynchronous recall and energy change |
| 17 | Boltzmann machines | Configuration probabilities, sampling and correlation-based learning |
| 18 | RBMs | Hidden representation, reconstruction and CD-1 update |
| 19 | Autoencoders | Encoding, decoding, reconstruction loss and parameter learning |
| 20 | Sigmoid networks | Forward pass and activation properties |

Chapter 5 §5.4 covers related architectures beyond this attached lecture outline. Complete the core sections before that reference material.

### Module 6 Deep Neural Networks

[Read Chapter 6](06%20-%20Deep%20Learning/Deep%20Learning.md), §§6.1–6.4.

| Lecture | Topics | Required preparation |
|---|---|---|
| 21 | Deep-network foundations | Hierarchical features, perspectives, issues and data splits |
| 22 | Optimization and regularization | Complete BGD/SGD/mini-batch updates; momentum, Adam, penalties, dropout and early stopping |
| 23 | CNNs | Convolution through prediction, pooling and output dimensions |
| 24 | Recurrent and recursive networks | Context-dependent states, sequence modeling, BPTT and tree composition |

The saved RNN chat mainly develops recurrence. Prepare the recursive/recurrent distinction and the chapter's tree example; a long recursive-network derivation is not established by that chat. Named CNN architectures and broader generative-model topics are lower priority than the listed core calculations.

### Module 7 Classification and Clustering

[Read Chapter 7](07%20-%20Classification/Classification.md), §§7.1–7.6.

| Lecture | Topics | Required preparation |
|---|---|---|
| 25 | Linear and logistic models | Decision boundary, probability, log loss and learning |
| 26 | LDA | Scatter matrices, Fisher derivation, projection and threshold |
| 27 | Linear SVM | Support vectors, margin derivation and parameter calculation |
| 28 | Nonlinear SVM | Soft margin, kernels, mapped XOR, dual and KKT |
| 29 | KNN, K-means and decision trees | Neighbor voting, clustering iterations, WCSS and split selection |

**Include Lecture 29.** These algorithms appear in the attached summary, although the official Module 7 topic list does not enumerate them and the saved teacher chats focus on logistic regression, LDA and SVM.

## Study priorities

The teacher chats repeatedly request functionality, numerical simulation, weight learning and interpretation. For every model, prepare one explanation and one complete calculation.

1. Complete the worked examples in Chapters 5–7.
2. Solve the chapter exercises without the answers.
3. Revisit the first incorrect step in each failed calculation and solve it again.
4. Use the teacher chats for explanations of difficult steps and the PDFs for further detail.

Give extra practice time to Hopfield recall, BM/RBM learning, GD variants, CNN operations, LDA scatter matrices and SVM derivations. Still finish the Lecture 29 numericals. These priorities reflect the supplied material, rather than a prediction of the paper.

## Practice selection

| Chapter | Exercises | What they check |
|---|---|---|
| 5 | 5A–5E | Recall, stochastic learning, reconstruction and gradients |
| 6 | 6A–6F | Training decisions, optimization, CNNs and recurrent/recursive computation |
| 7 | 7A–7F | Classifier calculations, clustering, trees and KKT |

For more mixed questions, use [Semester Practice sections E–G](Semester%20Practice.md). The chapter exercises provide the additional KNN, K-means and decision-tree coverage.

## Readiness checklist

### Module 5

- [ ] Construct a Hopfield matrix and recover corrupted bits in a stated order.
- [ ] Calculate energies, normalized probabilities and sampled states in a BM.
- [ ] Explain both learning phases and complete an RBM update.
- [ ] Calculate sigmoid activations, an autoencoder code, reconstruction loss and a gradient.

### Module 6

- [ ] Explain deep-network perspectives and practical training issues.
- [ ] Complete one epoch of BGD, SGD and mini-batch GD.
- [ ] Explain momentum and Adam; calculate penalties, dropout and early stopping.
- [ ] Trace convolution, activation, pooling, flattening and classification.
- [ ] Trace RNN states and distinguish time recurrence from tree recursion.
- [ ] Explain BPTT, sequence prediction and bidirectional context.

### Module 7

- [ ] Calculate logistic probability, loss, gradient and boundary.
- [ ] Derive Fisher's direction and solve a complete LDA example.
- [ ] Derive SVM margin width and solve the linear and mapped-XOR examples.
- [ ] Explain soft margin, kernels, dual coefficients and KKT conditions.
- [ ] Complete a KNN vote, K-means iterations and a tree split calculation.

## Timed revision

### Mixed practice session

Allow 75–90 minutes for a selection of chapter questions: one Module 5 learning calculation, one Module 6 training or CNN calculation, one LDA/SVM derivation, and one Lecture 29 problem. Rotate the selection in the next session so every method is tested.

### Six hour revision schedule

Use this after prior study; it is not a first-learning schedule.

| Time | Revision block |
|---:|---|
| 90 minutes | Module 5 |
| 90 minutes | Training, CNN and RNN |
| 90 minutes | Logistic regression, LDA and SVM |
| 45 minutes | KNN, K-means and decision trees |
| 45 minutes | Closed-book recall and corrections |

## Answer presentation

For an explanation, state the purpose, draw the structure where useful, define the variables and explain the governing rule. For a derivation, state assumptions and the objective before algebra. For a numerical problem, show intermediate steps and identify the final prediction, state, parameter or checkpoint.

Before calculating, check state encodings, bias signs, update order, temperature, batch averaging, log base, stride and padding. Retain intermediate precision and round the final result.

## Scope references

- [Official syllabus map](References/Syllabus.md).
- [Saved teacher chats](References/Teacher%20Chat%20Index.md).
- The attached summary beginning “The playlist Machine Learning Classes by Dr. Rohini Basak”. Lecture numbering and Lecture 29 coverage follow that summary; every video has not been independently checked.

The chapter calculations use consistent conventions and corrected arithmetic. Teacher chats remain explanatory source copies rather than the answer key.
