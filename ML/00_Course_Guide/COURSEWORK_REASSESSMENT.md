# Reassessment using mam’s coursework

> **Historical note:** this assessment used the earlier lectures 1–9 summary. The later handwritten CT sheet adds HMM and CRF and is authoritative. Use [CT Scope Map](CT_SCOPE_MAP.md) and [enhanced CT notes](../ct1.md) for current scope.

Primary new source: [ML_Modules_1-4_CT_Notes.md](../ct1.md), supplied by you as a summary of mam’s coursework. It identifies lectures 1–9 and maps them to four modules. I read the complete file and compared it with the earlier plan. I have not independently checked the linked videos or verified that its “Likely CT questions” are actual teacher questions.

## Changes to the earlier advice

| Earlier emphasis | Revised decision from the supplied coursework |
|---|---|
| Unit 1 as a short definitions block | Give it 75 minutes: lectures 1–3 include the workflow, preprocessing, feature engineering/selection, model selection and metrics. |
| Accuracy confidence intervals as a main evaluation block | Teach confusion matrix, precision, recall, F1, cross-validation and leakage first. Keep sampling uncertainty as brief syllabus backup. |
| Generic naive Bayes example | Add document classification, token counts, Laplace smoothing and log scores, emphasized in the new Module 2. |
| Perceptron update and XOR | Also practice AND/OR/NOT/NAND truth tables and weight/threshold choices, emphasized in lectures 6–7. |
| Backpropagation as the dominant numerical | Keep it central, but first practise the explicit computation graph from Module 4; also calculate loss and forward-pass dimensions. |
| Broad optimizer coverage | Prioritize gradient descent, momentum, initialization and early stopping; reduce detailed Adam/Xavier study. |
| HMM/CRF scope uncertain in the earlier summary | Resolved by the later handwritten sheet: HMM and CRF are included. Use the enhanced Modules 5–6 and the specified HMM PDF ranges. |
| Several source PDFs in every block | Use the new CT notes as the main reading, with the corrected supplement for worked practice and small gaps. |

Two questions of 15 marks do not establish their topics or whether they are single essays versus multipart questions. The revised mock deliberately includes several modules. It is practice, not a prediction or official marking scheme.

## Remaining gaps and corrections

- The supplied notes have no fully worked numerical tables for several listed exercises. Use the examples below and the existing [exam supplement](EXAM_PATCH_AND_PRACTICE.md).
- The Module 4 “general differentiable output activation” delta formula using elementwise multiplication applies to **elementwise** activations. For a coupled activation such as softmax, use the activation Jacobian transpose times the loss gradient. With softmax and categorical cross-entropy the combined result simplifies to delta = predicted probabilities minus target, before any batch averaging.
- In the formula sheet, two occurrences of `,qquad` are missing the backslash. Read these as equation spacing; they are not mathematical terms. The source file has been left unchanged.
- Confusion-matrix measures and cross-validation are covered by the new material. The earlier “evaluation gap” now applies specifically to hypothesis-accuracy sampling theory and formal algorithm comparison, not to evaluation generally.
- Formal runtime, noise-free concept learning, Bayes-optimal classification and sampling theory remain more explicit in the official syllabus than in this summary. Keep the brief backup; do not silently declare them out of scope.
- Empirical risk is not developed explicitly in the new notes. The supplement distinguishes it from population risk and individual-example loss.
- Uniform priors make MAP coincide with ML; ML estimation itself does not require choosing a uniform prior. Cross-validation helps select a model; it does not directly change a fitted model's complexity like a regularization penalty does.

## Practice 1: confusion matrix

Given TP=30, TN=50, FP=10, FN=10:

- Accuracy = (30+50)/100 = .80.
- Precision = 30/(30+10) = .75.
- Recall = 30/(30+10) = .75.
- F1 = 2×.75×.75/(.75+.75) = .75.

Explain which denominator refers to predicted positives and which to actual positives. Metrics are not interchangeable with the training loss.

## Practice 2: text naive Bayes

This is a constructed exercise, not a transcribed lecture dataset.

Vocabulary: {free, win, meeting}. Training collection: 2 spam documents and 2 ham documents. Token counts aggregated by class:

| Class | free | win | meeting | Total tokens |
|---|---:|---:|---:|---:|
| Spam | 3 | 2 | 0 | 5 |
| Ham | 0 | 1 | 4 | 5 |

Classify the document **“free win”**, using multinomial naive Bayes and add-one smoothing.

1. Priors: P(spam)=P(ham)=2/4=.5.
2. Vocabulary size is 3; each class likelihood denominator is 5+3=8.
3. Spam: P(free|spam)=4/8; P(win|spam)=3/8.
4. Ham: P(free|ham)=1/8; P(win|ham)=2/8.
5. Unnormalized scores: spam=.5×4/8×3/8=.09375; ham=.5×1/8×2/8=.015625.
6. Predict **spam**. If a normalized model posterior is requested: .09375/(.09375+.015625)=6/7≈.85714.
7. Equivalent log scores are ln(.5)+ln(4/8)+ln(3/8) and ln(.5)+ln(1/8)+ln(2/8). The larger score still selects spam.

Repeat with “meeting meeting”. Count both occurrences; do not reduce the document to unique words under the multinomial model. Answer check: spam score=.5×(1/8)²=.0078125; ham score=.5×(5/8)²=.1953125; predict ham.

## Practice 3: computation graph

Use the expression in the coursework: f=(x1+x2)x3−x4. Set x1=2, x2=3, x3=4, x4=1.

Forward: u=x1+x2=5; v=u×x3=20; f=v−x4=19.

Backward: df/dv=1, df/dx4=−1, df/du=x3=4, df/dx3=u=5. Since du/dx1=du/dx2=1, the final gradient with respect to (x1,x2,x3,x4) is **(4,4,5,−1)**.

The same chain-rule reasoning underlies backpropagation. A gradient is a derivative; a parameter update additionally needs an optimizer and learning rate.

## Practice 4: losses

Regression: targets (1,0), predictions (.8,.2). MSE=[(1−.8)²+(0−.2)²]/2=.04.

Binary classification: y=1 and predicted probability .8. Binary cross-entropy=−ln(.8)≈.22314.

Multiclass classification: predicted probabilities (.1,.7,.2) and true class is the second. Cross-entropy=−ln(.7)≈.35667. These use natural logarithms.

For the complete neural-network gradient and parameter update, use section 6 of [the exam supplement](EXAM_PATCH_AND_PRACTICE.md).
