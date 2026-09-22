# Your one-day ML plan

**Exam: 2 questions × 15 marks. Scope: everything up to FFNN.**

Study in this order: **ML basics → one neuron → a network → training the network → Bayesian learning → evaluation → answer practice.** This keeps connected ideas together. Prepare all included areas: we do not know which two questions will appear or whether there is internal choice.

Budget **8 hours of study**, plus meals and breaks. Start whenever you can. Take a 10–15 minute break roughly each hour, and a longer meal break after Step 3. The times below are limits to aim for, not a minute-by-minute timetable.

## 1. Get the big picture — 30 minutes

Open [Intro to ML](../01_Introduction_to_ML/notes/Intro_to_ML.md).

Read definitions, applications, supervised versus unsupervised learning, and the learning workflow. Then read **section 1** of [the exam supplement](EXAM_PATCH_AND_PRACTICE.md) for the formal learning model, design issues and runtime.

**Finish by explaining:** “What does a machine learn, from what data, and how do we know it learned?” Use one concrete example. Write a short answer covering task, experience and performance.

Leave detailed history, DAQ hardware and data matching for later unless your teacher emphasized them.

## 2. Understand one neuron, then a network — 45 minutes

Open [Neural Networks Basics](../03_Artificial_Neural_Networks/notes/Neural_Networks_Basics.md). Use **section 4** of [the supplement](EXAM_PATCH_AND_PRACTICE.md) for the corrected formulas and exercises.

Follow this chain:

**Inputs × weights + bias → activation → perceptron prediction → weight update → XOR limitation → hidden layer.**

Draw a neuron and a small MLP. Learn sigmoid, tanh and ReLU with their derivatives. Perform the perceptron update in the supplement.

**You are ready to move on when:** you can explain why a single perceptron cannot solve XOR and why a nonlinear hidden layer helps.

If the hidden-layer idea is unclear, use [L03 Multilayer Perceptrons](../03_Artificial_Neural_Networks/sources/L03%20Multilayer%20Perceptrons.pdf), PDF pages 2–7. This is a fallback, not another compulsory reading.

## 3. Learn how the network trains — 2 hours 45 minutes

Stay in [the exam supplement](EXAM_PATCH_AND_PRACTICE.md), **sections 5–7**. It keeps the backpropagation notation and signs consistent.

Work through three questions in order:

**How does the network predict? — 30 minutes**

Compute a forward pass. Understand what each weight matrix, bias and activation does. Match regression with a linear output, binary classification with sigmoid, and multiclass classification with softmax. Understand loss and empirical risk.

**How does it correct a mistake? — 90 minutes**

Follow the worked backpropagation example once. Then hide the solution and calculate it yourself: forward pass → loss → output delta → hidden delta → gradients → update weights and biases. Explain where the chain rule appears. Finally change the target to 0 and repeat to check that you understand the update direction.

For the derivation, use [Neural Networks and Backpropagation](../04_Feedforward_Networks_and_Graphical_Models/01_FFNN_and_Training/sources/lec21.notes.pdf), PDF pages 4–8. Its notation differs from the supplement, so translate symbols before using its equations.

**How do we train it well? — 45 minutes**

Learn initialization, learning rate, batch/SGD/mini-batch, momentum and Adam at a conceptual level, regularization, dropout, early stopping and validation. Explain what you would change if training loss is low but validation loss is high.

**Your output:** an answer outline for “Explain FFNN training using backpropagation”: labeled architecture, forward equations, loss, output/hidden derivatives, update algorithm, worked calculation and practical training considerations.

Do not open all the FFNN PDFs. They overlap heavily.

## 4. Learn Bayesian prediction — 1 hour 45 minutes

Open [Bayesian Learning](../02_Hypothesis_Evaluation_and_Bayesian_Learning/notes/Bayesian_Learning.md). Read **section 3** of [the supplement](EXAM_PATCH_AND_PRACTICE.md) beside it.

Follow this chain:

**Prior belief → observed evidence → posterior → MAP/ML → naive Bayes prediction.**

Spend about 30 minutes on Bayes theorem, MAP versus ML and noise-free concept learning. Spend 45 minutes calculating a naive Bayes example. Finish with 30 minutes on Bayes-optimal classification and belief networks.

For a full training table, use [Bayesian Learning slides](../02_Hypothesis_Evaluation_and_Bayesian_Learning/sources/lec04-BayesianLearning.pdf), PDF pages 39–41. The short note only shows part of the table.

**Your output:** an answer outline containing Bayes theorem, its terms, the conditional-independence assumption, classifier steps, one solved numerical and limitations. Also explain how MAP differs from Bayes optimal and factorize a small belief network.

Leave EM and detailed MDL derivations until the named syllabus topics are secure, unless your teacher specifically emphasized them.

## 5. Learn how to judge a model — 45 minutes

Read **section 2** of [the supplement](EXAM_PATCH_AND_PRACTICE.md). This fills a gap in your existing notes.

Answer these in order:

- What is training accuracy, and why can it be misleading?
- What are training, validation and test sets used for?
- How do sample size and sampling uncertainty affect measured accuracy?
- How should we compare two learning algorithms fairly?

Work the 80-correct-out-of-100 example without looking at the answer.

**Your output:** a short explanation of generalization, an accuracy/standard-error calculation, and a fair comparison procedure. Connect this to the validation and regularization you learned in Step 3.

## 6. Practise the actual answer format — 90 minutes

Use **60 minutes as a practice window**, not an assumed exam duration: 30 minutes for each mock question. When you know the actual exam duration, use its time budget instead.

**Mock question 1 — 15 marks**

Explain the architecture and training of a multilayer feedforward neural network. Derive the backpropagation updates and illustrate one training step with a numerical example.

**Mock question 2 — 15 marks**

Explain Bayesian learning and distinguish MAP from maximum likelihood. Describe naive Bayes, state its assumption, and demonstrate classification using a numerical example.

These are practice questions, not predictions. Use the supplement's numerical inputs but cover its solutions. Follow any actual question's subparts instead of forcing a memorized answer onto it.

Spend the final **30 minutes** checking signs, bias updates, probabilities, assumptions and missing explanations. Then briefly recall Unit 1, perceptron/XOR, Bayes optimal, belief networks and hypothesis evaluation so the two mock topics do not crowd out the rest of the scope.

For a long answer, include whichever of these the question asks for: **definition → labeled diagram → equations and derivation → algorithm → numerical steps → assumptions or limitations**. There is no fixed page count or verified marking breakdown available.

## If you fall behind

Drop extra PDF reading first. Keep one Bayes numerical, one full backpropagation numerical, hypothesis evaluation and written-answer practice. Avoid spending the final hour collecting more material.

HMMs, CRFs and entropy are outside this plan's interpretation of “up until FFNN”; their inclusion is still unconfirmed because they follow FFNN in the same syllabus block. Units 5–8 are outside the stated scope.

**Start here:** open Intro to ML and spend 30 minutes on Step 1. You only need the linked summary and supplement in front of you.
