# Distributed Computing syllabus map

Source: official final-year IT syllabus, course **IT/PC/B/T/413**. The printed syllabus does not assign unit numbers; the folders below number its topic blocks in printed order.

## 1. Fundamental concepts

Models, issues, complexity measures, and proving correctness.

[Material](../01_Fundamental_Concepts/README.md)

## 2. Clocks and event ordering

Clock concepts and limitations; clock synchronization; Lamport logical clocks; vector clocks; causal ordering; Birman-Schiper-Stephenson and Schiper-Eggli-Sandoz protocols.

[Material](../02_Clocks_and_Event_Ordering/README.md)

## 3. Global state and snapshot recording

System model; FIFO and non-FIFO snapshot algorithms; Chandy-Lamport variations; snapshots with causal delivery; monitoring global state; consistent-global-snapshot conditions; finding consistent snapshots.

[Material](../03_Global_State_and_Snapshots/README.md)

## 4. Termination detection

Introduction and issues.

[Material](../04_Termination_Detection/README.md)

## 5. Fundamental algorithms

Wave algorithms; traversal algorithms; leader election in rings and arbitrary networks; Korach-Kutten-Moran algorithm.

[Material](../05_Wave_Traversal_and_Leader_Election/README.md)

## 6. Distributed mutual exclusion

Permission based: Lamport, Ricart-Agrawala, Roucairol-Carvalho, and Maekawa. Quorum based: Maekawa and Agrawal-El Abbadi. Token based: Suzuki-Kasami and Raymond tree based.

[Material](../06_Distributed_Mutual_Exclusion/README.md)

## 7. Deadlock detection

System model; deadlock models; Knapp classification; Mitchell-Merritt single-resource algorithm; Chandy-Misra-Haas for AND and OR models; Kshemkalyani-Singhal for the P-out-of-Q model.

[Material](../07_Distributed_Deadlock_Detection/README.md)

## 8. Distributed file systems

NFS and Google File System.

[Material](../08_Distributed_File_Systems/README.md)

## 9. Distributed transactions and concurrency control

Transaction-management framework; atomicity; concurrency control; architectural issues; distributed deadlocks; timestamp ordering; optimistic concurrency control; execution schedules.

[Material](../09_Distributed_Transactions_and_Concurrency_Control/README.md)

## Source availability

| Syllabus block | Dedicated supplied lecture material | Status |
|---|---|---|
| Fundamentals | Introduction PDF | Good base |
| Clocks/event ordering | Logical-clock and synchronization PDFs | Good base; SES needs supplementation |
| Snapshots | None | Rebuilt from syllabus and PYQs |
| Termination | None | Rebuilt from syllabus and PYQs |
| Fundamental algorithms/election | Four PDFs | Strong coverage of message passing, trees, ring election; KKM needs supplementation |
| Mutual exclusion | None | Rebuilt from syllabus and PYQs |
| Deadlock | None | Rebuilt from syllabus and PYQs |
| File systems | Short GFS introduction only | NFS and GFS details supplemented |
| Transactions/concurrency | None | Rebuilt from syllabus and PYQs |

