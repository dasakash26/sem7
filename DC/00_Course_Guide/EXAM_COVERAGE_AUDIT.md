# Exam coverage audit

## Bottom line

The archived single Markdown file was not enough for the full semester examination. It covered the seven lecture PDFs reasonably well, but compressed roughly half of the official syllabus into one short section. Several repeated 15-20 mark areas had only definitions or answer outlines.

## Major defects repaired

| Area | Old state | Repair |
|---|---|---|
| Snapshots | FIFO Chandy-Lamport only | Added consistent cuts, channel state, FIFO rules, non-FIFO colouring approach, causal-channel case, stable-property use, and worked trace |
| Termination | Mentioned, not taught | Added termination condition, Dijkstra-Scholten signalling, snapshot method, safety/liveness, and pitfalls |
| Causal ordering | BSS only | Added BSS delivery test and a bounded-metadata SES explanation |
| Wave/traversal | Almost absent | Added definitions, echo algorithm, traversal-token scheme, invariants, and costs |
| Arbitrary-network election | Ring-heavy | Added tree election, echo/max-ID election, and syllabus-level KKM modular idea |
| Mutual exclusion | Ricart-Agrawala only | Added all named permission, quorum, and token algorithms with message costs |
| Deadlock | CMH-AND outline only | Added models, Knapp classification, CMH AND/OR, Mitchell-Merritt, and Kshemkalyani-Singhal overview |
| File systems | Mostly GFS constraints | Added NFS architecture/versions/semantics and detailed GFS reads/writes/replication |
| Transactions | 2PC and 2PL outline | Added recovery cases, schedules, serializability graph, timestamp ordering, optimistic control, and architecture |
| PYQs | Mixed into concept notes | Added a separate topic/year/marks index and kept source scans together |

## Corrections made

- Corrected the clock-offset and network-delay formulas used with four timestamps.
- Removed the claim that asynchronous algorithms have an unqualified real-time bound equal to tree depth; asynchronous complexity now distinguishes message count, causal chain length, and wall-clock bounds.
- Standardized mathematical notation using Markdown/LaTeX delimiters.
- Removed stale machine-specific source metadata.
- Replaced unsupported answer stubs with either a usable exam answer or an explicit scope warning.

## Exam priority from supplied papers

| Priority | Topics | Evidence |
|---:|---|---|
| 1 | Logical/vector clocks and causal delivery | Recurs in 2021, 2024 CT1, and 2025 semester paper |
| 1 | Snapshots and consistent cuts | Recurs in CT and semester papers |
| 1 | Mutual exclusion | Full CO block or CT question in several papers |
| 1 | Deadlock models and detection | Full CO block in 2021, 2023, 2024, and 2024 CT2 |
| 1 | NFS/GFS | Full 15-20 mark blocks in 2021 and 2025 |
| 1 | Transactions, 2PC, and serializability | Full 15-20 mark blocks in 2021, 2024, and 2025 |
| 2 | Leader election and message complexity | Repeated in CT and semester papers |
| 2 | Fundamentals, transparency, models | Frequently used as short subquestions |
| 3 | Physical clock synchronization | Appears as concepts/application questions |

## Definition of "exam-ready"

A topic counts as ready only when the notes include its assumptions, operational steps, correctness idea, complexity or performance metrics, one trace/example, and the likely exam traps. A topic name in a checklist does not count as coverage.
