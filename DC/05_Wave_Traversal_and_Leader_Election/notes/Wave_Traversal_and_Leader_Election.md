# Wave, traversal, leader election, and spanning trees

## 1. Message-passing executions

The network is a graph $G=(V,E)$ with $n=|V|$ processes and $m=|E|$ communication links. A process can inspect only its local state and received messages; it cannot directly read another process's memory.

A **configuration** contains all local states and all messages in transit. A **delivery event** places a message in a receiver's input buffer. A **computation event** lets one process consume inputs, change state, and place messages in output buffers.

![Processes connected by communication channels](../assets/process-channel-model.png)

In a synchronous execution, computation and message delay have known bounds, so algorithms can be analysed in rounds. A fair asynchronous execution normally assumes that every correct process keeps taking steps and every sent message is eventually delivered, but gives no finite delivery deadline. Fairness therefore supports eventual termination; it does **not** make a timeout proof of failure.

## 2. Wave and traversal algorithms

### Wave algorithm

A **wave** is a finite distributed computation with at least one decision event such that every process participates and causally influences that decision. It has two conceptual directions:

1. information diffuses through the network;
2. evidence that the diffusion is complete returns to the decision process.

Broadcast alone is not a complete wave: the initiator does not yet know that every process has participated. An echo/convergecast supplies that completion evidence.

### Echo on an arbitrary graph

Assumptions: one initiator, a connected undirected graph, reliable links, and no crashes during the wave.

```text
initiator sends WAVE on every incident edge

on first WAVE at v from p:
    parent[v] := p
    send WAVE on every other incident edge

record every later WAVE as that edge's response

after a WAVE has arrived on every non-parent edge:
    send WAVE back to parent[v]

initiator decides after every incident edge has replied
```

The first `WAVE` received by each non-initiator selects its parent. Those parent edges form a spanning tree: every node except the initiator has one parent, and following parents moves toward an earlier event, so a cycle is impossible. On a non-tree edge, the two crossing `WAVE` messages account for each other; on a tree edge, the later message is the returning echo. A node replies to its parent only after every other edge is accounted for, so the initiator's final decision implies that the whole connected graph participated.

Under the standard formulation, each undirected edge carries at most one message in each direction, so the wave costs at most $2m$ messages. This is a message bound, not a finite asynchronous time bound.

### Tarry's traversal algorithm

Echo explores concurrently. **Tarry traversal** instead moves one token at a time.

Each process records the edge by which the token first entered and which incident edges have already been used. It forwards the token on an unused edge; a non-initiator uses its entry edge for the return only after its other usable edges are exhausted. The initiator terminates when the token returns and no unused incident edge remains.

Two invariants give correctness:

- the token never crosses the same edge twice in the same direction;
- if an incident edge remains unused, the token cannot permanently finish at that process.

Thus every reachable node is visited, the token returns to the initiator, and each undirected edge is traversed at most once in each direction: at most $2m$ token messages. Tarry is not automatically DFS; a particular local edge-choice rule can make the tour DFS-like.

![Illustrative comparison of echo and Tarry traversal](../assets/echo-tarry-comparison.svg)

| Primitive | Communication pattern | Completion known where? | Messages |
|---|---|---|---:|
| Tree broadcast | Root to children | Not at the root by itself | $n-1$ |
| Tree convergecast | Children to root | At the root | $n-1$ |
| Echo wave | Concurrent exploration, then replies | At the initiator | at most $2m$ |
| Tarry traversal | One serial token | At the initiator | at most $2m$ |

## 3. Rooted spanning-tree primitives

A rooted spanning tree contains all $n$ processes, has $n-1$ edges, and gives every non-root process one parent.

### Broadcast and convergecast

In a **broadcast**, the root sends a value to its children and each non-root forwards it to its children. It uses $n-1$ messages and has causal depth equal to the tree height $h$.

![Broadcast over a rooted spanning tree](../assets/broadcast-example.png)

In a **convergecast**, leaves send toward the root. An internal process waits for all children, combines their values, and sends one result to its parent. It also uses $n-1$ messages and has causal depth $h$.

![Convergecast over a rooted spanning tree](../assets/convergecast-example.png)

Together, broadcast followed by convergecast is a tree wave with $2(n-1)$ messages and causal depth $2h$. Without a message-delay bound, its asynchronous wall-clock time is unbounded.

### Constructing a tree from a known root

The first `EXPLORE` received fixes a process's parent; later arrivals on other links are rejected or acknowledged. `ACCEPT` senders become children. The process finishes after every incident edge is classified. This is the tree-forming part of echo, so it does not require a second independent explanation.

With equal one-edge-per-round propagation, the first arrival follows a shortest-hop path and the result is a BFS tree. In an asynchronous execution, a longer path may arrive first, so the tree need not be BFS or DFS.

![Different spanning trees caused by asynchronous delivery order](../assets/spanning-tree-executions.png)

## 4. Leader-election specification

Election does not merely mean that one process says “I am leader.” Under the stated fault and timing model it must satisfy:

- **Validity:** the winner is an eligible member, such as the highest live ID.
- **Uniqueness/safety:** at most one winner is elected for an election epoch.
- **Agreement:** all correct processes output the same winner.
- **Termination/liveness:** every correct process eventually outputs a winner.

A deterministic algorithm cannot elect a leader in a perfectly symmetric anonymous ring: identical processes receive indistinguishable histories and make identical decisions. Unique IDs, randomness, a distinguished process, or sufficient topological asymmetry can break symmetry.

Useful ring terms:

- **oriented:** processes agree on clockwise/counter-clockwise;
- **anonymous:** processes have no unique IDs;
- **uniform:** the code does not depend on knowing $n$;
- **unidirectional/bidirectional:** messages use one/both ring directions.

Fault-free election is possible in an asynchronous network with reliable eventual delivery. The impossibility arises when termination must be guaranteed despite crashes: silence cannot distinguish a crashed node from an arbitrarily slow node. Timeout-based recovery therefore needs eventual timing assumptions or a failure detector.

## 5. Synchronous complete-graph election — PYQ supplement

For a complete graph $K_n$ with unique IDs, every process sends its ID to all other processes in round 1. At the end of that round, every process has the same set of IDs and independently selects the maximum.

For $K_5$:

- directed sends in round 1: $5(5-1)=20$;
- information rounds: **1**;
- winner: the process with the maximum ID.

A second announcement round is optional and redundant because every process already computed the winner. If a question explicitly requires the elected process to broadcast `LEADER`, count 4 more sends and report 2 rounds. State this convention in the answer.

## 6. Ring election

### LCR on a unidirectional ring

Assumptions: reliable oriented unidirectional ring, unique comparable IDs, no crashes, and every process initially participates.

```text
initially: send own ID clockwise
on candidate x:
    x > ownID  -> forward x
    x < ownID  -> discard x
    x = ownID  -> declare leader and circulate ELECTED(x)
```

**Correctness.** The maximum ID can never meet a larger ID, so every process forwards it and it returns to its owner. Every smaller ID meets a larger ID before completing the ring and is discarded. Hence exactly the maximum declares; the subsequent announcement gives agreement.

The election traffic is $\Theta(n^2)$ in the worst ordering; notification adds $n$ messages. Under this counting convention, the decreasing-ID arrangement gives

$$n+\sum_{i=0}^{n-1}(i+1)=n+\frac{n(n+1)}2$$

transmissions including the final notification. Causal distance to declaration is $O(n)$, but asynchronous elapsed time is not finitely bounded.

![Worst-case candidate traffic in LCR](../assets/lcr-worst-case.png)

### Hirschberg-Sinclair on a bidirectional ring

Assumptions: reliable bidirectional ring, unique comparable IDs, no crashes, and initially unknown ring size.

In phase $k=0,1,2,\ldots$, an active candidate probes distance $2^k$ in both directions. A larger ID suppresses a smaller probe. A probe reaching its radius returns; a candidate receiving both replies survives to the next phase. If its probes meet after covering the ring, it is the maximum and wins.

![Exponentially expanding probes in Hirschberg-Sinclair](../assets/hs-probes.png)

Survivors in phase $k$ are separated by distance at least $2^{k-1}$, so there are only $O(n/2^k)$ of them. Each generates $O(2^k)$ probe/reply traffic; therefore each phase costs $O(n)$ and the $O(\log n)$ phases cost

$$O(n\log n) \text{ messages}.$$

The matching $\Omega(n\log n)$ lower bound is for the relevant comparison-based election model on asynchronous rings of unknown size. It is not an unconditional bound for every stronger model; synchrony and non-comparison operations can change what is achievable.

## 7. Failure-triggered election

### Failure-triggered ring protocol

This is **not LCR**. It starts after a coordinator is suspected. One or more processes circulate `ELECTION(id, attribute)` clockwise. A larger candidate replaces a smaller candidate; an ID that returns to its owner wins and circulates `ELECTED(id)`.

Under this protocol's convention:

- eventual leader starts: $2n$ messages (one election circulation and one announcement);
- worst initiator position: $3n-1$ messages.

Therefore, for the PYQ with $n=10$, the answer is $3(10)-1=29$ **only if the question means this failure-triggered ring protocol**. It is not LCR's worst-case formula, and standard LCR does not have one chosen initiator.

With concurrent initiators, a process caches the highest candidate seen and suppresses lower runs. “Fair” here does not mean equal probability of leadership—the highest eligible ID still wins. It means reliable/fair circulation does not permanently starve a live candidacy, and the result does not depend on which live process first noticed the failure.

If the highest-ID process fails mid-election, no algorithm can infer that from silence alone in pure asynchrony. With eventual synchrony, another process times out, starts a new epoch/run, and the highest remaining live ID eventually wins. Missing a real failure mainly blocks liveness; false suspicion can cause repeated elections and, without epochs or quorum protection, competing coordinators. A network partition is not automatically handled safely by this ring protocol.

### Bully algorithm

Assumptions: known membership, totally ordered IDs, crash-stop failures, and eventually accurate timeouts.

1. A suspecting process sends `ELECTION` to every higher-ID live candidate it knows.
2. A higher process replies `OK` and starts/continues its own election.
3. A process receiving no higher reply declares and sends `COORDINATOR` to lower IDs.
4. A process that received `OK` but no coordinator announcement eventually times out and retries.

The highest live ID eventually wins after failures and timing stabilize. When the lowest ID starts and every higher process successively competes, the number of `ELECTION` sends alone is

$$ (n-1)+(n-2)+\cdots+1=\frac{n(n-1)}2,$$

so total traffic is $O(n^2)$. The often-quoted “five message-transmission times” is one illustrated failure chain, not a universal Bully time bound.

## 8. Election in arbitrary networks — syllabus supplement

### With an existing rooted tree

If a rooted spanning tree already exists, leader selection is easy: convergecast the maximum ID to the root, then broadcast that winner. It costs $2(n-1)$ messages. This is a useful primitive, but it does not solve leader election **from scratch** because the tree already has a distinguished root.

### Korach-Kutten-Moran (KKM): modular traversal-to-election transformation

The syllabus includes KKM; the following is a compact scope rather than reconstructed pseudocode.

Model: a connected asynchronous network with reliable bidirectional links, distinct ordered process IDs, and $k$ possible starters; FIFO channels are not required. Assume a serial traversal algorithm costs at most $f(n)$ messages.

Each starter launches a traversal token labelled by its candidate and a **level**. Competing tokens compare level and then candidate ID. Conceptually, a weaker traversal may be annexed, chase a stronger traversal, or wait so that conflicting traversals do not both finish independently. When same-level competition is resolved, the surviving combined territory advances to the next level; lower-level contenders lose to the stronger level. A traversal that completes the whole network without an undefeated competitor elects its initiator.

The safety idea is that the comparison/absorption rules leave at most one traversal able to complete as winner. The liveness idea is that competition strictly eliminates candidates or raises a level; with $k$ starters there are at most $\log_2 k+1$ levels. The transformation's message bound is

$$\bigl(f(n)+n\bigr)\bigl(\log_2 k+1\bigr),$$

where $f(n)$ is the chosen traversal's message bound and $k$ is the number of starters. The paper also gives a topology-sensitive form using $f(m)$. Exact annexing/chasing state transitions are version-specific and should be learned from the assigned KKM paper if required.

![Conceptual KKM token and level flow](../assets/kkm-level-flow.svg)

KKM is distinct from max-ID flooding and from GHS: it transforms traversal into leader election; GHS constructs a minimum spanning tree.

## 9. Luby's maximal independent set — PYQ supplement

An **independent set** contains no adjacent vertices. It is **maximal** if no additional vertex can be added without breaking independence. Maximal does not mean maximum-cardinality.

Luby's randomized synchronous algorithm maintains active vertices. In each phase:

1. every active vertex chooses a fresh random priority (break ties by unique ID);
2. an active vertex whose priority exceeds every active neighbour joins the MIS;
3. selected vertices and all their neighbours become inactive;
4. the remaining active graph repeats.

Selected neighbours cannot coexist because each would need a greater priority than the other, proving independence. Every non-selected vertex removed in step 3 is adjacent to a selected vertex; when no active vertices remain, the result is maximal. The standard algorithm terminates in $O(\log n)$ phases with high probability (and has the corresponding expected logarithmic behaviour); each phase exchanges priorities/status over active edges.

Topology behaviour depends on the random priorities:

| Graph | Possible/final MIS behaviour |
|---|---|
| Complete graph $K_n$ | The unique global priority maximum joins in phase 1; any singleton is maximal. |
| Star | If the centre beats every leaf, the centre alone wins. If a leaf beats the centre, winning leaves remove the centre and eventually all leaves form the MIS. |
| Ring | Non-adjacent local maxima join, their neighbours leave, and gaps repeat. Many MISs are possible; an alternating set is only one example. |

Do not draw a deterministic Luby trace unless the random priorities for each phase are specified.

## 10. Consensus and system examples

Election chooses a process; consensus chooses a value. They are related but not identical.

In Paxos, proposers use unique proposal numbers; acceptors durably record promises and accepted values; learners discover a value accepted by a majority. A proposer that receives promises from a majority must adopt the value from the highest-numbered previously accepted proposal, if one exists. Any two majorities intersect, so two different values cannot both be chosen. Paxos safety is asynchronous, while progress needs a reachable majority and eventual communication/stable proposing behaviour.

In Chubby, a majority elects one master, with one vote per replica per run. In a ZooKeeper-style example, increasing sequence IDs are assigned, the highest is chosen, and a process monitors the next-higher participant to reduce all-to-all monitoring. Treat this as a simplified illustration, not a full description of ZooKeeper's production Fast Leader Election/Zab protocol.

## 11. Distributed minimum spanning tree

Assumptions: a connected undirected weighted graph, reliable communication, and distinct edge weights or deterministic tie-breaking. A minimum spanning tree (MST) connects all nodes with minimum total edge weight.

The algorithm uses a **GHS-style** fragment construction:

1. every node starts as a one-node level-0 fragment;
2. a fragment leader broadcasts a search;
3. nodes test incident edges and reject internal edges;
4. candidates convergecast so the leader obtains the fragment's minimum-weight outgoing edge (MWOE);
5. fragments merge across the MWOE and broadcast the new fragment identity;
6. repeat until the connected graph has one fragment.

![A fragment identifies its outgoing edges](../assets/mwoe-outgoing-edges.png)

The cut property makes the choice safe: an MWOE crossing a fragment's cut belongs to some MST. Equal-level fragments merge into a fragment one level higher; a lower-level fragment can be absorbed by a higher-level fragment. A level-$L$ fragment contains at least $2^L$ nodes, so there are $O(\log n)$ levels.

![Two fragments merge through an MWOE](../assets/mwoe-merge.png)

Choosing the larger endpoint UID as the merged-fragment leader is a simplification, not the complete classical GHS state machine. Common stated bounds are:

- time: $O(n\log n)$;
- communication: $O((n+m)\log n)$.

Classical GHS is commonly stated as $O(m+n\log n)$ messages. Other presentations cite $O((n+m)\log n)$ or $O(n\log n+m)$ depending on edge classification and testing assumptions.

After the tree exists, a unique tree leader can be chosen with max-ID convergecast plus broadcast in $2(n-1)$ messages. This final election is separate from constructing the MST.

## 12. Election comparison

| Algorithm | Topology/model | Initiators | Failure support | Election messages |
|---|---|---|---|---:|
| $K_n$ all-to-all | synchronous complete graph | all | none | $n(n-1)$ |
| LCR | async unidirectional ring | all | none | $\Theta(n^2)$ worst case |
| Hirschberg-Sinclair | async bidirectional ring | all | none | $O(n\log n)$ |
| Failure-triggered ring | oriented ring + eventual detection | one or more | coordinator crash, after stabilization | $2n$ to $3n-1$ |
| Bully | known complete membership + usable timeouts | one or more | crash-stop, after stabilization | $O(n^2)$ worst case |
| Rooted-tree max | existing rooted tree | root coordinates | none | $2(n-1)$ including announcement |
| KKM | async arbitrary connected graph | $k$ | no crash in base model | $(f(n)+n)(\log_2 k+1)$ |

The table compares only election algorithms. Paxos chooses a value, Luby constructs an MIS, and GHS constructs an MST, so ranking them in the same message-complexity table would be misleading.

## Sources

- [Classical leader-election algorithms](../sources/04_Classical-Leader-Election-Algorithms.pdf)
- [Distributed leader-election protocols](../sources/05_Distributed-Leader-Election-Protocols.pdf)
- [Message-passing algorithms](../sources/06_Message-Passing-Algorithms.pdf)
- [Distributed spanning tree](../sources/07_Distributed-Spanning-Tree.pdf)
- [Korach, Kutten, and Moran, “A Modular Technique for the Design of Efficient Distributed Leader Finding Algorithms”](https://doi.org/10.1145/323596.323611)
- [Luby, “A Simple Parallel Algorithm for the Maximal Independent Set Problem”](https://www.cs.cmu.edu/~guyb/paralg/papers/Luby86.pdf)
