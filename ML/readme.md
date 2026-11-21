# Machine Learning

**Semester Study Notes · IT/PC/B/T/411**

## Contents

Open a chapter below or jump to the [chapter sources](#chapter-sources).

1. [ML Basics](01%20-%20ML%20Basics/ML%20Basics.md)
2. [Evaluating Hypotheses and Bayesian Learning](02%20-%20Evaluation%20and%20Bayes/Evaluation%20and%20Bayes.md)
3. [Artificial Neural Networks](03%20-%20Neural%20Networks/Neural%20Networks.md)
4. [Feedforward Networks](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/Feedforward%20Networks.md) · [Graphical Models](04%20-%20Feedforward%20and%20Graphical%20Models/Graphical%20Models/Graphical%20Models.md)
5. [Probabilistic Neural Networks](05%20-%20Probabilistic%20Networks/Probabilistic%20Networks.md)
6. [Modern Practical Deep Networks](06%20-%20Deep%20Learning/Deep%20Learning.md)
7. [Classification and Clustering](07%20-%20Classification/Classification.md)
8. [Reinforcement Learning](08%20-%20Reinforcement%20Learning/Reinforcement%20Learning.md)

For a written answer, define the model and state its assumptions. Draw the architecture or boundary if it helps. Write the governing equation, show one complete calculation, and interpret the result.

[CT2 topic outline and guide](CT2%20Guide.md) · [CT2 study plan](plan.md) · [Semester practice](Semester%20Practice.md) · [References](References/README.md)

## Past questions

[CT1 Set B paper](pyq/ml_26ct1_b.png) · 30 marks, 1 hour. The worked answers are in the relevant chapters.

| Question | Where to read the answer |
|---|---|
| 1(a) Feature engineering and dimensionality reduction | [Why feature engineering matters](01%20-%20ML%20Basics/ML%20Basics.md#why-feature-engineering-matters) |
| 1(b) Total probability and the meningitis calculation | [Total probability and a diagnostic test](02%20-%20Evaluation%20and%20Bayes/Evaluation%20and%20Bayes.md#total-probability-and-a-diagnostic-test) |
| 1(c) Neural network topologies and activation functions | [From a threshold neuron to an MLP](03%20-%20Neural%20Networks/Neural%20Networks.md#31-from-a-threshold-neuron-to-an-mlp) |
| 2(a) NAND using a perceptron | [Worked NAND gate](03%20-%20Neural%20Networks/Neural%20Networks.md#worked-nand-gate) |
| 2(b) Day 11 weather and observable versus hidden states | [Worked weather prediction](04%20-%20Feedforward%20and%20Graphical%20Models/Graphical%20Models/Graphical%20Models.md#worked-weather-prediction-with-a-markov-chain) |
| 2(c) CRF applications and comparison with HMMs | [Linear-chain CRFs](04%20-%20Feedforward%20and%20Graphical%20Models/Graphical%20Models/Graphical%20Models.md#45-linear-chain-crfs) |

## Chapter sources

The notes group repeated material by topic and retain representative worked examples. Chapters 1–7 have local lecture PDFs. Chapter 8 follows the syllabus and the textbook and paper linked below. The shared [SEC1609 compilation](References/SEC1609.pdf) includes neural networks on pp. 2–46 and separate fuzzy logic units on pp. 47–72. The fuzzy logic units remain in the source PDF.

### 1. Introduction to Machine Learning

- [Machine Learning Introduction](01%20-%20ML%20Basics/sources/01_Machine_Learning_Introduction.pdf)

### 2. Evaluating Hypotheses and Bayesian Learning

- [Bayesian Methods](02%20-%20Evaluation%20and%20Bayes/sources/02_Bayesian_Methods.pdf)
- [Bayesian classification, pp. 31–41](02%20-%20Evaluation%20and%20Bayes/sources/01_Bayesian_Learning.pdf#page=31)
- [Bayesian Learning Notes](02%20-%20Evaluation%20and%20Bayes/sources/04_Bayesian_Learning_Notes.pdf)
- [Bayesian Applications](02%20-%20Evaluation%20and%20Bayes/sources/03_Bayesian_Applications.pdf)

### 3. Artificial Neural Networks

- [Perceptron Learning](03%20-%20Neural%20Networks/sources/02_Perceptron_Learning.pdf)
- [Neural Network Foundations](03%20-%20Neural%20Networks/sources/01_Neural_Network_Foundations.pdf)
- [Multilayer Perceptrons](03%20-%20Neural%20Networks/sources/03_Multilayer_Perceptrons.pdf)

### 4. Feedforward Networks and Graphical Models

#### Feedforward Networks

- [Neural Networks for NLP Slides](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/sources/09_Neural_Networks_for_NLP_Slides.pdf)
- [Multilayer Perceptrons](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/sources/02_Multilayer_Perceptrons.pdf)
- [Neural Networks for NLP](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/sources/10_Neural_Networks_for_NLP.pdf)
- [MLP and Backpropagation](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/sources/05_MLP_and_Backpropagation.pdf)
- [Feedforward Networks Chapter](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/sources/08_Feedforward_Networks_Chapter.pdf)
- [Feedforward Networks Collins](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/sources/07_Feedforward_Networks_Collins.pdf)
- [Perceptrons and Feedforward Nets](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/sources/03_Perceptrons_and_Feedforward_Nets.pdf)
- [Feedforward and Backpropagation](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/sources/04_Feedforward_and_Backpropagation.pdf)
- [Chain rule notes, pp. 4–8](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/sources/06_Backpropagation_Derivation.pdf#page=4)
- [Perceptron Learning](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/sources/01_Perceptron_Learning.pdf)

#### Graphical Models

- [Markov Chains and HMMs](04%20-%20Feedforward%20and%20Graphical%20Models/Graphical%20Models/sources/04_Markov_Chains_and_HMMs.pdf)
- [Conditional Random Fields](04%20-%20Feedforward%20and%20Graphical%20Models/Graphical%20Models/sources/05_Conditional_Random_Fields.pdf)
- [HMM Inference and Learning](04%20-%20Feedforward%20and%20Graphical%20Models/Graphical%20Models/sources/02_HMM_Inference_and_Learning.pdf)
- [HMM Algorithms](04%20-%20Feedforward%20and%20Graphical%20Models/Graphical%20Models/sources/03_HMM_Algorithms.pdf)
- [HMM Foundations](04%20-%20Feedforward%20and%20Graphical%20Models/Graphical%20Models/sources/01_HMM_Foundations.pdf)

### 5. Probabilistic Neural Networks

- [Srihari, autoencoders, pp. 9–29](05%20-%20Probabilistic%20Networks/sources/04_Autoencoders_Srihari.pdf#page=9)
- [Hopfield recall, energy, and storage, pp. 23–78](05%20-%20Probabilistic%20Networks/sources/01_Hopfield_Networks.pdf#page=23)
- [Hopfield and Boltzmann machines, pp. 17–81 and 97–192](05%20-%20Probabilistic%20Networks/sources/05_Hopfield_and_Boltzmann_Reference.pdf#page=17)
- [Salakhutdinov, autoencoders, pp. 4–34](05%20-%20Probabilistic%20Networks/sources/03_Autoencoders_Salakhutdinov.pdf#page=4)
- [Boltzmann sampling and RBM training, pp. 41–103](05%20-%20Probabilistic%20Networks/sources/02_Boltzmann_Machines_and_RBMs.pdf#page=41)

### 6. Modern Practical Deep Networks

- [Deep feedforward networks, CNNs, and RNNs, pp. 4–60](06%20-%20Deep%20Learning/sources/01_Deep_Learning_Overview.pdf#page=4)
- [Regularization, pp. 5–40](06%20-%20Deep%20Learning/sources/03_Regularization_Methods.pdf#page=5)
- [Regularization and optimization, pp. 9–27](06%20-%20Deep%20Learning/sources/02_Training_and_Regularization.pdf#page=9)
- [Deep learning notes, pp. 16–30 and 40–50](06%20-%20Deep%20Learning/sources/06_Deep_Learning_Reference.pdf#page=16)
- [MIT sequence modeling, pp. 16–77](06%20-%20Deep%20Learning/sources/04_RNN_Sequence_Modeling.pdf#page=16)
- [Karpathy RNN slides, pp. 49–75](06%20-%20Deep%20Learning/sources/05_RNN_Visualization_Karpathy.pdf#page=49)
- [Feedforward backpropagation refresher](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/Feedforward%20Networks.md#42-forward-pass-and-backpropagation)

### 7. Classification and Clustering

- [LDA tutorial, pp. 7–31](07%20-%20Classification/sources/04_LDA_Tutorial.pdf#page=7)
- [Zisserman, SVMs, pp. 10–19 and 30–39](07%20-%20Classification/sources/06_SVM_Linear_Margins.pdf#page=10)
- [Hoiem, regression and logits, pp. 5–27](07%20-%20Classification/sources/01_Linear_and_Logistic_Regression.pdf#page=5)
- [Harvard CS109A, logistic regression, pp. 17–36](07%20-%20Classification/sources/02_Logistic_Regression_Slides.pdf#page=17)
- [Unit II notes, pp. 6–10](07%20-%20Classification/sources/08_Classification_Methods.pdf#page=6)
- [Fisher discriminants, pp. 3–13 and 21–28](07%20-%20Classification/sources/05_Linear_Discriminant_Functions.pdf#page=3)
- [Rakotomalala, SVMs, pp. 5–33](07%20-%20Classification/sources/07_SVM_Margins_and_Kernels.pdf#page=5)
- [Logistic regression notes, pp. 1–5](07%20-%20Classification/sources/03_Logistic_Regression_Derivation.pdf)

### 8. Reinforcement Learning

- [Sutton and Barto, chapters 2–6 and 13](https://msdnaa.eecs.berkeley.edu/~cs188/fa19/assets/files/SuttonBartoIPRLBook2ndEd.pdf)
- [Original UCT paper](https://sites.ualberta.ca/~szepesva/papers/ecml06.pdf)
