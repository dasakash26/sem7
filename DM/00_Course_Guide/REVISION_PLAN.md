# DM revision plan

This plan covers only the three supplied lecture-note PDFs.

## Study order

1. Introduction and KDD.
2. Data types and descriptive statistics.
3. Cleaning, integration, transformation and reduction.
4. Discretization and entropy-based splitting.
5. Association basics and Apriori.
6. FP-growth, condensed patterns and rule evaluation.

## Two-day plan

### Day 1 - introduction and preprocessing, 5 hours 30 minutes

1. **Introduction - 60 min:** slides 3-14 of `1_DM_Intro.pdf`; definitions, tasks, motivation, applications and system components.
2. **KDD, patterns and issues - 60 min:** slides 15-28; reproduce the KDD flow and classify mining functions.
3. **Statistics and quality - 60 min:** slides 29-44; central tendency, dispersion, boxplots, histograms, scatter plots and correlation patterns.
4. **Cleaning and transformation - 75 min:** slides 2-25 of `2_DM_preprocessing.pdf`; attributes, missing/noisy data, integration and normalization.
5. **Reduction and discretization - 75 min:** slides 26-40; reduction, sampling, bins, hierarchies, entropy and information gain.

**Exit test:** solve one IQR outlier, one normalization, one bin-smoothing and one entropy-split problem.

### Day 2 - association rules and PYQs, 6 hours

1. **Association foundations - 60 min:** slides 2-16 of `4_Association_Rules.pdf`; support, confidence, frequent/strong rules and anti-monotonicity.
2. **Apriori - 90 min:** slides 17-25; join/prune, support counting, hash tree and drawbacks. Solve the 2026 Apriori question.
3. **FP-growth - 75 min:** slides 26-34; build the 2025 FP-tree and conditional pattern bases.
4. **Rules and condensed patterns - 45 min:** slides 35-43; rule generation, maximal and closed sets.
5. **Rule evaluation - 45 min:** slides 44-53; contingency tables, lift, correlation and measure limitations.
6. **Recall - 45 min:** answer only the fully covered questions in the [PYQ index](PYQ_TOPIC_INDEX.md).

**Exit test:** complete one Apriori numerical and one FP-tree without notes; explain why confidence alone can mislead.

## One-day plan - 10 focused hours

| Block | Time | Target |
|---|---:|---|
| DM, KDD, tasks and issues | 2 h | Definitions, process, applications and pattern types |
| Statistics and preprocessing | 2 h | Summaries, quality, missing/noisy data, integration and normalization |
| Reduction and discretization | 1 h 30 min | Reduction, sampling, bins, hierarchies and entropy split |
| Association foundations and Apriori | 2 h | Support/confidence, anti-monotonicity and one full numerical |
| FP-growth and condensed patterns | 1 h 30 min | FP-tree, rules, maximal and closed sets |
| Interestingness and PYQ recall | 1 h | Lift/correlation plus covered PYQ outlines |

## PYQ practice

1. 2026 Q3 in full.
2. 2026 Q2(b-c) and Q1(a).
3. 2025 Q3 in full, Q2(b), Q1(b) and Q1(d).
4. Practice only the covered portion of mixed questions; do not study their out-of-scope remainder from the textbook.

## Answer format

- **Five marks:** definition, three precise points, one example or diagram.
- **Ten-mark algorithm:** purpose, inputs, numbered steps, trace/table, result and limitation.
- In calculations, show sorted data, candidate sets or conditional pattern bases; do not jump directly to the answer.

## Final check

- Every row in the [notes scope map](SYLLABUS_MAP.md) has been revised.
- Every task in the [formula and algorithm checklist](FORMULA_ALGORITHM_CHECKLIST.md) works without notes.
- The [PYQ index](PYQ_TOPIC_INDEX.md) is used as an overlap filter, not as an expanded syllabus.
