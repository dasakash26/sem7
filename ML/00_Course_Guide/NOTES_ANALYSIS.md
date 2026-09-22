# Notes analysis

> **Updated after the handwritten CT sheet:** use [CT Scope Map](CT_SCOPE_MAP.md), [enhanced CT notes](../ct1.md) and the [revised one-day plan](ONE_DAY_PLAN.md) for current priorities. The audit below describes the original collection before the new CT scope. Its time allocations and conditional HMM/CRF language have been superseded: HMM and CRF are included.

Exam scope confirmed by you: **up until FFNN**. This audit treats Units 1–3 and FFNN training in Unit 4 as core. HMM/CRF/entropy are conditional; Units 5–8 are outside this plan.

## What you have

24 PDFs, 1,252 pages, and six topic summaries. I extracted text from all PDFs, read the six summaries, compared coverage with the official syllabus (PDF pages 1–2), and visually checked the syllabus and the backpropagation derivation. This is a coverage and targeted correctness audit, not a proofread of every slide or diagram. No ML past paper was found; the available question papers belong to DM. Priorities below reflect syllabus coverage and prerequisite value, not predicted marks.

| Syllabus area | Assessment | What to do today |
|---|---|---|
| 1. Introduction | Broad introductory coverage, but the summary overemphasizes DAQ, data matching and generic workflow. Formal learning models and runtime are not adequately explained. | Read Intro_to_ML selectively; use the exam supplement for formal definitions and runtime. |
| 2. Hypothesis evaluation | Major gap: the Bayesian summary and four Bayesian PDFs do not provide a dedicated treatment of estimating accuracy, sampling uncertainty and comparing algorithms. Incidental sampling mentions in other PDFs do not fill this gap. | Reserve 45 minutes for the supplement and its accuracy exercise. |
| 2. Bayesian learning | Good breadth: Bayes, MAP/ML, concept learning, Bayes optimal, naive Bayes and belief networks. Four PDFs substantially overlap. | Use Bayesian_Learning plus lec04-BayesianLearning pages 31–41 for a worked classifier example. |
| 3. ANN | Good coverage of neurons, activation functions, perceptron and XOR. Some topology statements are too restrictive. | Use Neural_Networks_Basics and the 7-page L03 Multilayer Perceptrons. |
| 4. FFNN training | Strong source material, scattered across the original ANN and FFNN folders. The summary mixes notation and delta signs, and is thin on risk, validation and model selection. | Use corrected equations in the supplement; lec21.notes pages 4–8 for the derivation; selected training slides for the rest. |
| 4. HMM/CRF/entropy | Five dedicated PDFs were hidden in the FFNN folder. There is no short study summary for these topics. Entropy coverage is not independently established by merely having HMM/CRF material. | Separate conditional reading, only if the teacher includes the rest of Unit 4. |
| 5. Probabilistic networks | Partial Hopfield coverage and many additional architectures; not a complete treatment of the listed models. | Outside this exam plan. |
| 6–8. Deep networks, classifiers, RL | Some incidental mentions and overlap, but no dedicated complete notes for these units. | Folders explicitly record missing coverage; no need to fill these today. |

## Corrections to keep beside the original notes

1. **Backpropagation sign:** `Multilayer_Backpropagation` first defines delta with prediction minus target, then gives an update with a plus sign. Both conventions exist, but cannot be mixed. With delta = derivative of loss with respect to preactivation, subtract the gradient. The supplement uses this convention throughout.
2. **Notation:** the same note changes the meanings of `a` and `z`. Use `z = Wx+b`, `a = g(z)` consistently; its source `lec21.notes.pdf` uses different names, so translate before copying equations.
3. **Loss scaling:** half the sum of squared errors is not mean squared error. Averaging changes the gradient by the sample-count factor. Always state the chosen loss.
4. **Universal approximation:** say “approximate continuous functions on a compact domain to arbitrary accuracy, given sufficient width and suitable activation.” It does not promise exact representation, successful training or good generalization.
5. **ReLU:** it helps gradient flow for positive inputs; it does not guarantee the absence of vanishing gradients and can produce inactive neurons.
6. **Xavier initialization:** variance depends on layer widths; it does not simply force every activation to mean zero and variance one. For Xavier normal, weight variance is commonly 2/(fan-in + fan-out).
7. **Batch normalization:** it normalizes selected intermediate activations with learned scale/shift. It is not mandatory at every layer. Training uses batch statistics; inference generally uses running statistics.
8. **Feedforward topology:** a feedforward network is acyclic; it may have skip connections. It is not restricted to immediately adjacent layers. A fully connected layer connects every unit to every unit of the next layer, not necessarily every node in the entire network.
9. **Bayesian wording:** marginal evidence P(D) normalizes the posterior and is common across hypotheses for fixed D. Do not interpret it as an independently adjustable measure of support. The total-probability rule needs a mutually exclusive, exhaustive partition. ML estimation is defined regardless of the prior; uniform priors make MAP coincide with ML.
10. **Bayes example:** the diagnosis posterior printed as “Exact: 0.21” is rounded: 0.00784/(0.00784+0.02976) ≈ 0.20851. The PlayTennis table in the summary is incomplete; use the full table in the PDF to reproduce the calculation.
11. **Embedding shapes:** `Feedforward_NN_NLP` defines E as vocabulary-by-dimension but later writes E times a column one-hot vector. With that convention use E-transpose times the column vector, or a row one-hot vector times E.
12. **Introductory claims:** rote learning in ML means storing and reusing cases; inductive learning broadly means generalizing from examples, not just the ILA algorithm. The notes' workflow percentages and claims of universal superiority are not definitions to memorize.
13. **Advanced notes:** unsupported “100x faster” claims should not be repeated as universal facts. Hopfield convergence requires conditions such as symmetric weights and appropriate asynchronous updates; “unconditionally stable” is too broad. These are outside today's scope.

Original PDFs and topic summaries were preserved. Use [the corrected supplement](EXAM_PATCH_AND_PRACTICE.md) alongside them.

## Minimum reading set

- Unit 1: `Intro_to_ML.md`, selectively; original introduction PDF only for clarification.
- Unit 2: `Bayesian_Learning.md`; `lec04-BayesianLearning.pdf` pages 31–41 for Bayes optimal and naive Bayes. Use the evaluation supplement for the missing half of this unit.
- Unit 3: `Neural_Networks_Basics.md`; `L03 Multilayer Perceptrons.pdf` pages 2–7 for MLP, XOR, nonlinearity and representation.
- Unit 4: `Multilayer_Backpropagation.md` with corrections; `lec21.notes.pdf` pages 4–8 for FFNN and derivatives. `lec3.learning.pdf` pages 131–137 explain empirical risk. `13_Multilayer_Perceptron.pdf` covers loss on pages 31–32 and initialization around pages 74–77; use its later sections selectively for dropout and training.
- `Feedforward_NN_NLP.md`: output-layer table and training sections first; embeddings/language models only if taught for this exam.

All page numbers above are PDF viewer page numbers. Do not read the 193-page feedforward deck end-to-end today. `ffnets-note.pdf`, `ff.pdf`, `6.pdf` and the other slide decks are alternatives, not additional compulsory reading.
