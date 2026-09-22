# Past-question topic index

Use this file to choose questions after studying a topic. The wording is normalized only enough to repair obvious OCR errors; consult the linked scan for diagrams and exact phrasing.

## Topic recurrence

| Topic | Papers in supplied set | Priority |
|---|---:|---:|
| Logical/vector clocks and causal delivery | 2021, 2024 CT1, 2025 | Very high |
| Snapshots/consistent cuts/global state | undated CT1, 2023, 2024 CT2, 2024, 2025 | Very high |
| Leader election/MIS | 2022 re-test, 2023, 2024 CT1, 2024, 2025 | High |
| Mutual exclusion | undated CT1, 2022 CT2, 2023, 2024 CT1, 2024, 2025 | Very high |
| Distributed deadlock | 2021, 2022 CT2/re-test, 2023, 2024 CT2, 2024, 2025 | Very high |
| NFS/GFS/cache/replication | 2021, 2023, 2024, 2025 | Very high |
| Transactions/2PC/serializability | 2021, 2024, 2025 | Very high |

## 2021 semester - 100 marks

[Original scan](../10_Past_Questions/sources/Distributed%20ComputingSemester2021.pdf)

| Q | Marks | Topic |
|---|---:|---|
| 1 | 20 | Vector-clock trace and Birman-Schiper-Stephenson causal broadcast |
| 2(a-b) | 6 | AND/OR wait models and WFG tests |
| 2(c-d) | 14 | Chandy-Misra-Haas in AND and OR models |
| 3(a) | 8 | Ricart-Agrawala optimization under synchronous delay bound |
| 3(b) | 12 | Ricart-Agrawala trace with timestamps 47 and 32 |
| 4 | 20 | NFS architecture/vnode, v3 lookup, v4 open delegation and compound procedures, UNIX/session semantics |
| 5(a) | 6 | 2PC coordinator recovery before/after durable commit record |
| 5(b) | 14 | Prove 2PL sufficient but not necessary for serializability |

## 2022 CT2 - 30 marks

[Original scan](../10_Past_Questions/sources/DC%20CT%202%202022.pdf)

| Q | Marks | Topic |
|---|---:|---|
| 1 | 15 | Kshemkalyani-Singhal assumptions, algorithm, and termination |
| 2 | 15 | Types of asynchronous shared memory, mutual exclusion, progress |

## 2022 CT re-test - 30-mark variants

[Original scan](../10_Past_Questions/sources/DC%20CT%20Re-Test%202022.pdf)

| Variant | Question | Marks | Topic |
|---|---|---:|---|
| CT1 | Complete graph of five nodes: synchronous leader election and rounds | 22 | Leader election |
| CT1 | Global and local clock for a graph | 8 | Clocks |
| CT2 | Kshemkalyani algorithm on four degree-2 nodes; analysis | 23 | Generalized deadlock |
| CT2 | Applications of mutual exclusion | 7 | Mutual exclusion |

## 2023 semester - supplied as `DC SEM 2022.pdf`

[Original scan](../10_Past_Questions/sources/DC%20SEM%202022.pdf)

| CO | Marks | Topic |
|---|---:|---|
| CO1 option A | 23 | Complete-graph election and rounds |
| CO2 option A | 19 | Global/local clock and Chandy-Lamport snapshot |
| CO2 option B | 16 | Local/global timing, asynchrony, snapshot application |
| CO1 option B | 26 | Ring election with correctness and Luby MIS |
| CO3 | 21 | Applications/progress/performance of mutual exclusion |
| CO3 option | 21 | Asynchronous shared memory and mutual exclusion |
| CO4 | 22 | Kshemkalyani(-Singhal) deadlock algorithm |
| Final section | 15 | DFS and mutual-exclusion MCQs |

## Undated CT1 - 30 marks

[Original scan](../10_Past_Questions/sources/Distributed%20Computing%20CT1.pdf)

| Q | Marks | Topic |
|---|---:|---|
| 1 | 10 | Why omitting the second BSS delivery condition breaks causal broadcast |
| 2 | 10 | Chandy-Lamport definition, assumptions, and recording channel state |
| 3 | 10 | Ricart-Agrawala message-complexity optimization in a synchronous system |

## 2024 CT1 - 30 marks

[Original scan](../10_Past_Questions/sources/DC_CT1_2024.pdf)

| Q | Marks | Topic |
|---|---:|---|
| 1(a) | 3 | Logic of sequential and concurrent event relations |
| 1(b) | 2 | Can exact causality use clocks smaller than $N$? |
| 1(c) | 3 | Version vectors and eventual consistency |
| 1(d) | 2 | Vector clocks for causal communication |
| 2(a-b) | 5 | Maekawa order and a deadlock example |
| 2(c) | 2 | Token mutual-exclusion response time under low/high load |
| 2(d) | 3 | Consistent cut and importance |
| 3(a) | 2 | Distributed vs parallel computing |
| 3(b) | 3 | Uniform/non-uniform, anonymous/non-anonymous, oriented/non-oriented rings |
| 3(c) | 3 | Ring election best/worst cases and message complexity |
| 3(d) | 2 | ZooKeeper leader-election failure handling |

## 2024 CT2 - 30 marks

[Original scan](../10_Past_Questions/sources/DC_CT2_2024.pdf)

| Q | Marks | Topic |
|---|---:|---|
| 1 | 10 | Snapshot message classification, algorithm, consistent cut |
| 2(a) | 3 | Cycle necessary but insufficient with multiple-resource/OR behavior |
| 2(b) | 2 | Why distributed deadlock avoidance is uncommon |
| 2(c) | 3 | False deadlock caused by resolution in AND model |
| 2(d) | 2 | Probe-based vs WFG-based detection |
| 3 | 10 | CMH probe trace, release race, correctness/no false positive |

## 2024 semester - 100 marks

[Original scan](../10_Past_Questions/sources/Semester%20paper.pdf)

| CO | Marks | Topic |
|---|---:|---|
| CO1 | 12+3+5 | MIS on star/ring/complete graphs; clocks; ring-election messages |
| CO2 | 10+10 | Local/global state and snapshot-based termination |
| CO3 | 7+8 | Centralized vs distributed mutual exclusion and implementation |
| CO3 option | 8+7 | Types of mutual-exclusion algorithms and clock ordering |
| CO4 | 5+5+5 | WFG, AND/OR, and edge-chasing |
| CO4 option | 7+8 | Cycle/knot and CMH performance |
| CO5 | 8+7 | Cache consistency and file-modification notification |
| CO5 option | 10+5 | Cache placement/traffic reduction and file replication |
| CO6 | 8+7 | Serializable concurrency control and atomic commit |
| CO6 option | 7+8 | Atomic-commit challenges and consensus agreement |

## 2025 semester - 100 marks

[Original scan](../10_Past_Questions/sources/Distributed%20Computing%20sem%20paper.pdf)

| CO | Marks | Topic |
|---|---:|---|
| CO1 option 1 | 20 | Transparencies, logical timestamps, ring token counting, vector-clock diagram |
| CO1 option 2 | 20 | Happened-before/concurrency diagram, causal communication, sync vs async, physical-clock security |
| CO2 set | 20 | Snapshot channels/issues/frontier; election failure; consistent cut; marker rules; ring algorithm |
| CO3 | 20 | Mutual-exclusion metrics, Lamport proof/trace/limitations, Suzuki-Kasami correctness |
| CO4 | 10 | Knot and probe-based deadlock pseudocode, or CMH trace and cycle proof |
| CO5 | 15 | GFS design, scale/faults, metadata, replication, chunk size, write path |
| CO6 | 15 | Global/local transaction plan and 2PC failures/limits |

## Practice order

1. 2025 paper - broadest and most recent supplied coverage.
2. 2021 paper - strongest algorithm traces for clocks, deadlock, NFS, and transactions.
3. 2024 CT1/CT2 - precise conceptual traps.
4. 2024 semester - cache and termination emphasis.
5. 2023/2022 papers - additional election, mutual-exclusion, and generalized-deadlock practice.

