# Sources and supplementary references

## Supplied material

All supplied lecture and question-paper PDFs are linked from the relevant folder README and recorded with checksums in [MOVE_MANIFEST.csv](MOVE_MANIFEST.csv). They remain the primary source for course-specific terminology and past-paper emphasis.

## Why supplementary references were needed

The supplied lecture PDFs do not teach several topics explicitly required by the official syllabus and repeatedly examined: snapshots, termination detection, most mutual-exclusion algorithms, distributed deadlock variants, NFS, and transaction management. The rewritten notes use the following foundational or standards sources for those gaps.

## Foundational references

- Lamport, “Time, Clocks, and the Ordering of Events in a Distributed System,” 1978: <https://doi.org/10.1145/359545.359563>
- Chandy and Lamport, “Distributed Snapshots: Determining Global States of Distributed Systems,” 1985: <https://doi.org/10.1145/214451.214456>
- Dijkstra and Scholten, “Termination Detection for Diffusing Computations,” 1980: <https://www.cs.utexas.edu/~EWD/transcriptions/EWD06xx/EWD687a.html>
- Schiper, Eggli, and Sandoz, “A New Algorithm to Implement Causal Ordering”: <https://doi.org/10.1007/3-540-51687-5_45>
- Korach, Kutten, and Moran, “A Modular Technique for the Design of Efficient Distributed Leader Finding Algorithms,” 1990: <https://doi.org/10.1145/323596.323611>
- Maekawa, “A $\sqrt{N}$ Algorithm for Mutual Exclusion in Decentralized Systems,” 1985: <https://doi.org/10.1145/214438.214445>
- Suzuki and Kasami, “A Distributed Mutual Exclusion Algorithm,” 1985: <https://doi.org/10.1145/6110.214406>
- Raymond, “A Tree-Based Algorithm for Distributed Mutual Exclusion,” 1989: <https://doi.org/10.1145/58564.59295>
- Mitchell and Merritt, “A Distributed Algorithm for Deadlock Detection and Resolution,” 1984: <https://groups.csail.mit.edu/tds/papers/Merritt/podc84.pdf>
- Chandy and Misra, distributed deadlock-detection paper, 1982: <https://doi.org/10.1145/800220.806693>
- Kshemkalyani and Singhal, “Efficient Detection and Resolution of Generalized Distributed Deadlocks,” 1994: <https://doi.org/10.1109/32.263754>
- Ghemawat, Gobioff, and Leung, “The Google File System,” 2003: <https://research.google/pubs/the-google-file-system/>
- Apache ZooKeeper internals, leader activation and quorums: <https://zookeeper.apache.org/doc/current/zookeeperInternals>

## Protocol standards

- NFS version 3, RFC 1813: <https://www.rfc-editor.org/rfc/rfc1813>
- NFS version 4, RFC 7530: <https://www.rfc-editor.org/rfc/rfc7530>

These references are for verification and missing-syllabus coverage. The notes do not copy their full text.
