# Midterm scope from the supplied slides

This path covers only the seven supplied lecture decks. Full-semester topics without dedicated slides remain in their own folders and are not part of this revision sequence.

## Study order

1. [Fundamental concepts](../01_Fundamental_Concepts/notes/Fundamental_Concepts.md)
2. [Clocks and event ordering](../02_Clocks_and_Event_Ordering/notes/Clocks_and_Event_Ordering.md)
3. [Message passing, leader election, and spanning trees](../05_Wave_Traversal_and_Leader_Election/notes/Wave_Traversal_and_Leader_Election.md)

## Deck-to-note map

| Supplied deck | Main note coverage |
|---|---|
| Introduction to Distributed Computing | Definition, motivation, coupling, system software, transparency, models, assumptions, complexity, correctness, scalability |
| Logical Clocks and Event Ordering | Happened-before, Lamport clocks, vector clocks, differential vector clocks, matrix time, causal delivery |
| Synchronization of Clocks | Drift/skew, Cristian, Berkeley, NTP, timestamp-based ordering |
| Classical Leader Election Algorithms | Ring terminology, impossibility, LCR, Hirschberg-Sinclair, lower bound |
| Distributed Leader Election Protocols | Ring election with failures, Bully, consensus/Paxos connection, Chubby and ZooKeeper examples |
| Message Passing Algorithms | Execution model, broadcast, convergecast, rooted spanning-tree construction |
| Distributed Spanning Tree | Fragment/MWOE construction, merging, levels, termination, complexity |

## Named but not developed in the decks

The syllabus names SES causal delivery, wave/traversal algorithms, and KKM arbitrary-network election, but the supplied decks do not teach their full algorithms. The slide-backed notes either give only a short scope statement or omit implementation detail rather than presenting unsupported reconstructions as lecture content.

## Outside this midterm path

Snapshots, termination detection, mutual exclusion, deadlock detection, file systems, and distributed transactions belong to the full-semester material unless the instructor extends the announced midterm scope.
