# Revision plan

## Midterm - seven supplied slide decks

Use the [midterm scope map](MIDTERM_SLIDE_SCOPE.md) and study in this order:

1. Fundamentals: coupling, transparency, models, assumptions, and correctness.
2. Physical clocks: drift, synchronization formulas, Berkeley, and NTP.
3. Logical clocks: happened-before, Lamport, vectors, differential vectors, and matrix time.
4. Causal delivery: both BSS conditions and the supplied SES scope.
5. Message passing and rooted trees: executions, broadcast, convergecast, and tree construction.
6. Leader election: impossibility, LCR, Hirschberg-Sinclair, ring failures, and Bully.
7. Consensus/MST: Paxos safety rule and the GHS-style MWOE construction.

For each major algorithm, recall its assumptions, mechanism, correctness idea, and complexity. The sections below are for the full-semester examination.

## Full semester - if you have one day

1. Read the rapid revision tables in [DC_EXAM_NOTES.md](../DC_EXAM_NOTES.md).
2. Study clocks/causality, snapshots, mutual exclusion, deadlock, file systems, and transactions in that order.
3. For every algorithm, write from memory: assumptions, message types, steps, correctness, and cost.
4. Solve the 2025 semester paper using the [PYQ index](PYQ_TOPIC_INDEX.md).
5. Finish with leader election and physical-clock formulas.

## Full semester - if you have three days

- **Day 1:** Fundamentals; clocks; causal delivery; snapshots; termination.
- **Day 2:** Wave/traversal/election; all mutual-exclusion families; deadlock models and algorithms.
- **Day 3:** NFS; GFS; 2PC; 2PL; timestamp and optimistic concurrency; two full papers.

## Answer template for a 10-mark algorithm

1. Model and assumptions - 1 mark.
2. State maintained and message types - 2 marks.
3. Numbered algorithm - 3 marks.
4. Correctness: safety and liveness - 2 marks.
5. Complexity and limitation - 1 mark.
6. Small trace/diagram - 1 mark.

## Non-negotiable comparisons

- Lamport scalar clock vs vector clock.
- FIFO vs non-FIFO snapshot.
- Lamport vs Ricart-Agrawala vs Maekawa mutual exclusion.
- Permission vs token mutual exclusion.
- AND cycle vs OR knot deadlock.
- NFS vs GFS.
- 2PC vs 2PL.
- Timestamp ordering vs optimistic concurrency control.
