# DC CT2 plan

| Order | Deck                                                                                                          | Study and practise                                                                                                                            |
| ----- | ------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| 1     | [Global Snapshot Part I](../03_Global_State_and_Snapshots/sources/Global-Snapshot-Part-I.pdf)                 | Local and channel states, cuts, frontiers, and consistency. Draw a cut with a message in transit.                                             |
| 2     | [Global Snapshot Part II](../03_Global_State_and_Snapshots/sources/Global-Snapshot-Part-II.pdf)               | Linearization, the two recording issues, Chandy–Lamport rules and proof, trading and token-ring traces, and stable predicates.                                   |
| 3     | [Distributed Mutual Exclusion](../06_Distributed_Mutual_Exclusion/sources/Distributed-Mutual%20Exclusion.pdf) | Requirements and metrics. Trace Lamport, Ricart–Agrawala, Maekawa including its deadlock remedy, and Suzuki–Kasami. Derive each message cost. |

In the [snapshot chapter](../03_Global_State_and_Snapshots/Global%20State%20and%20Snapshots.md), read from Global state through Applications of snapshots. In the [mutual exclusion chapter](../06_Distributed_Mutual_Exclusion/Distributed%20Mutual%20Exclusion.md), read the requirements and the four algorithms in the table. For CT2, study termination and deadlock as snapshot applications and deadlock as a failure mode of basic Maekawa.

## YouTube lectures

| Watch | Practise |
|---|---|
| [Consistent cuts and Chandy–Lamport](https://www.youtube.com/watch?v=moSWS1Gha2g) | Start from basics, then trace the marker example. |
| [Lamport](https://www.youtube.com/watch?v=1502Ol30WCw) | Update each request queue. |
| [Ricart–Agrawala](https://www.youtube.com/watch?v=7ZSn6--8DZM) | Decide who defers a reply. |
| [Maekawa](https://www.youtube.com/watch?v=eTF9FdVws58) | Check quorum intersections and trace [how a voter gets its vote back](../06_Distributed_Mutual_Exclusion/Distributed%20Mutual%20Exclusion.md#resolving-partial-grants). |
| [Suzuki–Kasami](https://www.youtube.com/watch?v=9vPAtfk1ufY) | Update the request numbers and token queue. |

**Revision:** [Consistent global state from 3:05](https://www.youtube.com/watch?v=moSWS1Gha2g&t=185s) and [IIT Delhi’s mutual exclusion recap](https://www.youtube.com/watch?v=90DChePW5wI).

## Past-paper practice within scope

- [2024 CT2 Q1](../10_Past_Questions/sources/DC_CT2_2024.pdf) — classify messages and explain the marker rules.
- [2021 semester Q3(b)](../10_Past_Questions/sources/Distributed%20ComputingSemester2021.pdf) — trace Ricart–Agrawala with timestamps 47 and 32.
- [2025 semester CO3 (i), (ii), and (vi)](../10_Past_Questions/sources/Distributed%20Computing%20sem%20paper.pdf#page=2) — metrics, Lamport fairness, and Suzuki–Kasami correctness.
