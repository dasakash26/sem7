# Distributed Computing - exam notes

This is the compact revision file. For proofs, full pseudocode, and worked traces, use the linked topic notes.

## Exam coverage

| Unit | Detailed note |
|---|---|
| Fundamentals | [Models, complexity, correctness](01_Fundamental_Concepts/notes/Fundamental_Concepts.md) |
| Clocks | [Physical/logical/vector clocks and causal delivery](02_Clocks_and_Event_Ordering/notes/Clocks_and_Event_Ordering.md) |
| Snapshots | [FIFO/non-FIFO snapshots and consistent cuts](03_Global_State_and_Snapshots/notes/Global_State_and_Snapshots.md) |
| Termination | [Termination detection](04_Termination_Detection/notes/Termination_Detection.md) |
| Fundamental algorithms | [Wave, traversal, election, spanning tree, MIS](05_Wave_Traversal_and_Leader_Election/notes/Wave_Traversal_and_Leader_Election.md) |
| Mutual exclusion | [Permission, quorum, and token algorithms](06_Distributed_Mutual_Exclusion/notes/Distributed_Mutual_Exclusion.md) |
| Deadlock | [Models and detection algorithms](07_Distributed_Deadlock_Detection/notes/Distributed_Deadlock_Detection.md) |
| File systems | [NFS and GFS](08_Distributed_File_Systems/notes/Distributed_File_Systems.md) |
| Transactions | [2PC and concurrency control](09_Distributed_Transactions_and_Concurrency_Control/notes/Distributed_Transactions_and_Concurrency_Control.md) |

# 1. Foundations

A distributed system consists of autonomous nodes with private state/clocks that coordinate through communication. The hard parts are partial failure, concurrency, variable delay, and lack of instantaneous global knowledge.

Always state the model:

- topology and node IDs;
- message passing or shared memory;
- reliable/unreliable and FIFO/non-FIFO communication;
- synchronous/asynchronous timing;
- crash, omission, or Byzantine failure.

Correctness:

- **Safety:** bad state never occurs.
- **Liveness:** desired progress eventually occurs under stated fairness assumptions.
- **Termination:** every admissible execution eventually finishes.

Complexity:

- messages, bits, rounds/causal hops, local space, response time, synchronization delay.

Do not give a real-time bound in pure asynchrony. Eventual delivery gives progress, not a known deadline.

# 2. Clocks and causality

## Four-timestamp physical synchronization

For client send $t_1$, server receive $t_2$, server send $t_3$, client receive $t_4$:

$$\delta=(t_4-t_1)-(t_3-t_2),$$

$$\theta=\frac{(t_2-t_1)+(t_3-t_4)}{2}.$$

$\delta$ is network round-trip delay excluding server processing; $\theta$ is server-minus-client clock offset under symmetric delay.

## Happened-before

$a\rightarrow b$ if they are ordered in one process, $a$ sends a message received at $b$, or the relation follows transitively. $a\parallel b$ when neither direction holds.

## Lamport clock

```text
before local/send: L := L + 1
on receive timestamp t: L := max(L,t) + 1
```

$$a\rightarrow b\Rightarrow L(a)<L(b),$$

but the converse is false. `(Lamport timestamp, process ID)` gives a total order.

## Vector clock

```text
before local/send at Pi: Vi[i]++
on receive Vmsg: Vi := componentwise_max(Vi,Vmsg); Vi[i]++
```

$$a\rightarrow b\iff V(a)<V(b)$$

using componentwise $\le$ with at least one strict component. Incomparable vectors mean concurrency.

## BSS causal delivery

Deliver message from $P_i$ with vector $T$ at $P_j$ only if:

$$T[i]=V_j[i]+1$$

and

$$T[k]\le V_j[k]\quad\forall k\ne i.$$

First condition preserves sender order; second ensures every cross-process causal predecessor is already delivered. Buffer otherwise.

SES uses vector-time dependency information for causal point-to-point delivery; the receiver waits until all attached destination-relevant dependencies are satisfied.

# 3. Global state and snapshots

A global state is all selected local states plus every channel state. A cut is consistent iff it contains no receive without its send, equivalently it is closed under happened-before.

## Chandy-Lamport

Assume reliable FIFO channels.

1. Initiator records local state and sends marker on every outgoing channel before later application messages.
2. On first marker, a process records local state, records that incoming channel empty, starts recording other incoming channels, and sends its markers.
3. On later marker for channel $c$, stop recording $c$; messages received after local recording and before this marker form $c$'s channel state.

Message classification:

- before local recording: reflected in local state;
- after local recording, before channel marker: channel state;
- after channel marker: post-snapshot.

## Non-FIFO

Ordinary marker separation is unsafe. In a colouring algorithm, messages/processes begin white; a recording process turns red; red sends red messages; a red process records any later-arriving white message as crossing the cut.

## Snapshot termination test

$$Terminated\iff(\text{all processes passive})\land(\text{all channel states empty}).$$

# 4. Termination detection

All processes being passive is insufficient because a message in transit can reactivate one.

In Dijkstra-Scholten, every basic message creates an acknowledgement obligation. The first activation establishes a parent dependency; a process acknowledges its parent only when it is passive and all descendant/outgoing obligations have returned. The root declares when passive with zero outstanding obligations.

Safety comes from the invariant that every active/in-transit piece of work has a chain of outstanding acknowledgement responsibility to the root.

# 5. Wave, traversal, and election

## Tree primitives

- Tree broadcast: $n-1$ messages, causal depth equal to tree height.
- Convergecast: $n-1$ messages.
- Echo on arbitrary graph: $O(m)$ messages; first arrivals form a spanning tree, echoes return after all incident exploration completes.

A wave terminates, contains a decision event, and every process causally influences that decision. A traversal visits nodes sequentially, for example a distributed DFS token.

## LCR

Each ring process sends its ID clockwise; forward larger, discard smaller, declare when own ID returns. Only maximum survives.

- Worst messages: $\Theta(n^2)$
- Causal time: $O(n)$ plus announcement

## Hirschberg-Sinclair

Candidate probes distance $2^k$ in both directions during phase $k$. Candidate survives only if both replies return without a larger ID. Survivors become exponentially separated, so each phase is $O(n)$ messages and there are $O(\log n)$ phases.

$$M(n)=O(n\log n).$$

## Bully

Suspector sends election to higher IDs; if none responds, it declares; otherwise a higher process continues. Highest live process wins under stable membership and eventual failure detection. Worst-case messages $O(n^2)$.

## Arbitrary graph

Echo/max-ID election lets the largest tagged wave cover the graph and return before announcing. KKM is a modular reduction from leader election to serial traversal: candidates run ordered traversals, eliminate weaker candidates, and expand coverage through $O(\log k)$ stages. If traversal costs $f$, the construction is bounded by roughly $(f+n)(\log k+1)$ messages.

## GHS MST

Fragments repeatedly find a minimum-weight outgoing edge and merge. Edge states are basic/branch/rejected; equal-level fragments create the next level. A level-$L$ fragment has at least $2^L$ nodes, hence at most $O(\log n)$ levels. Classical message cost is $O(m+n\log n)$.

## Luby MIS

Active nodes choose random priorities; local maxima join MIS; selected nodes and neighbors leave; repeat. Adjacent selections are impossible, and every removed non-member neighbors a selected node.

# 6. Distributed mutual exclusion

Requirements: mutual exclusion, progress, no starvation, and a stated fairness rule.

| Algorithm | Core rule | Messages per CS |
|---|---|---:|
| Coordinator | request-grant-release | 3 |
| Lamport | replicated timestamp request queues | $3(N-1)$ |
| Ricart-Agrawala | replies deferred by timestamp priority | $2(N-1)$ |
| Roucairol-Carvalho | retain permission until returned | 0 to $2(N-1)$ request/reply traffic |
| Maekawa | collect intersecting quorum votes | about $3\sqrt N$ |
| Agrawal-El Abbadi | tree quorum | $O(\log N)$ best case |
| Suzuki-Kasami | broadcast request, unique token | 0 or at most $N$ |
| Raymond | requests/token routed on tree | distance dependent; typically $O(\log N)$ light load |

## Ricart-Agrawala rule

Reply immediately if not competing or incoming `(timestamp,ID)` has higher priority. Otherwise defer. Enter after replies from all; on exit send all deferred replies.

## Maekawa

Every pair of voting sets intersects and each voter grants one process, proving safety. Basic algorithm can deadlock through circular partial grants; timestamp-priority/yield variants repair this.

## Suzuki-Kasami

Processes store `RN`; token stores last served vector `LN` and queue `Q`. Outstanding request condition:

$$RN[j]=LN[j]+1.$$

Unique token proves safety. On release update `LN`, enqueue outstanding requesters, and send token to queue head.

## Raymond

Tree pointers lead toward token. Requests aggregate along the path; token movement reverses pointers and follows distributed queue state.

# 7. Distributed deadlock

WFG edge $P_i\to P_j$ means $P_i$ waits for $P_j$.

| Model | Unblocking rule | Deadlock structure |
|---|---|---|
| Single-resource | One dependency | Cycle |
| AND | All requested dependencies | Cycle |
| OR | Any dependency | Knot, not merely a cycle |
| P-out-of-Q | Any $P$ of $Q$ | Irreducible generalized blocked set |

Knapp classes: path-pushing, edge-chasing, diffusing computation, and global-state detection.

## CMH AND

Blocked initiator $i$ sends `PROBE(i,i,j)` along each wait edge. Blocked receiver $k$ forwards `PROBE(i,k,m)` along its wait edges. If $i$ receives a probe with initiator $i$, a cycle returns and deadlock is detected.

## CMH OR

A query-reply diffusion explores every reachable dependency. Nodes reply only after their descendant queries are accounted for. Completion at the still-blocked initiator with no escape shows the reachable blocked set is closed: a knot.

## Mitchell-Merritt

Single-resource algorithm with unique private and propagated public labels. Labels move backward through WFG edges; when a process's own private label returns, a cycle exists. Dominant label selection makes one process detect/resolve.

## Kshemkalyani-Singhal

For P-out-of-Q/AND-OR waits, record the reachable WFG and reduce nodes whose unblocking predicate can be satisfied. Recording and reduction overlap in the one-phase algorithm. An initiator remaining irreducible is deadlocked. The classic bound is $4e-2n+2l$ messages and $2d$ hops for the relevant reachable graph parameters.

Phantom deadlock comes from combining wait edges that never coexisted. Use consistent snapshots, timestamps/epochs, or validate current blocked state before resolution.

# 8. Distributed file systems

## NFS

Application file calls pass through VFS/vnode. Local vnodes call a local file system; NFS vnodes issue RPC/XDR requests using opaque file handles.

NFSv3 lookup:

```text
LOOKUP(parent-directory-file-handle, component-name)
    -> object-file-handle + attributes
```

NFSv2/v3 are mostly stateless for ordinary access. Clients cache data, attributes, and names with timeout/open validation; strict cross-client UNIX visibility is not guaranteed by cache protocol alone.

NFSv4 integrates state, locking, leases, callbacks, and `COMPOUND`. A compound such as `LOOKUP -> OPEN -> READ` reduces RPCs but is not a database transaction. Read/write delegations let a client perform compatible operations locally until recall/lease expiry.

Cache consistency options:

- poll/validate by version or attributes;
- server invalidation callback;
- leases/delegations with expiry and recall.

## GFS

- One master for namespace, mapping, versions, leases, and placement.
- Large original 64 MB chunks, usually three replicas.
- Clients ask master for metadata, then transfer data directly with chunkservers.
- Operation log/checkpoints durably preserve namespace/mapping; chunk locations are learned from chunkservers.

Write path:

1. ask master for primary/secondaries;
2. pipeline data to replicas;
3. send mutation request to primary;
4. primary chooses serial order;
5. secondaries apply same order;
6. acknowledgements return.

Record append chooses the offset at primary and can produce duplicate records after retry. Checksums, versions, replication, heartbeat, and re-replication handle common failures.

# 9. Transactions and concurrency control

## Two-phase commit

Phase 1: coordinator sends PREPARE; YES voter force-writes READY and becomes unable to decide alone.

Phase 2: all YES -> coordinator force-writes COMMIT then sends COMMIT; otherwise write/send ABORT. Participants log decision, complete/undo, release, and ACK.

Recovery:

- Coordinator failed after YES votes but before durable COMMIT: recover, log ABORT, notify.
- Coordinator failed after durable COMMIT: re-send COMMIT until learned.
- Participant failed after READY: recover in doubt and ask for decision; may block.

2PC preserves atomicity with stable logs but is blocking under coordinator unavailability/partition. It does not handle Byzantine faults.

## Serializability

Build precedence graph: edge $T_i\to T_j$ when a conflicting operation of $T_i$ precedes one of $T_j$. Schedule is conflict-serializable iff graph is acyclic.

## Two-phase locking

Growing phase acquires; shrinking phase releases and acquires no more. Order transactions by final-lock acquisition point. Every conflict edge follows that order, so a cycle is impossible. 2PL is sufficient, not necessary; a serial schedule may release one lock and later acquire another while remaining serializable.

## Timestamp ordering

For item $X$, track `readTS(X)` and `writeTS(X)`.

- Reject read if $TS(T)<writeTS(X)$.
- Reject write if $TS(T)<readTS(X)$ or, under basic rule, $TS(T)<writeTS(X)$.

No lock deadlock, but transactions may abort/restart.

## Optimistic control

Read/compute privately, validate read/write sets, then install writes or abort. Best under low contention; expensive under high contention. Distributed validation plus atomic commit must enforce one global serialization order.

# Last-minute traps

1. Lamport timestamp order does not prove causality; vector order does.
2. A snapshot includes channel state and does not freeze the system.
3. FIFO Chandy-Lamport cannot be copied unchanged to non-FIFO links.
4. A passive system is not terminated if a basic message is in transit.
5. Anonymous deterministic rings cannot elect without symmetry breaking.
6. Basic Maekawa can deadlock.
7. One token is a safety invariant; timeout recreation can duplicate it.
8. AND deadlock uses a cycle; OR deadlock requires a knot.
9. GFS data bypasses the master.
10. NFSv4 `COMPOUND` is not atomic commit.
11. 2PC and 2PL are unrelated "two phases."
12. A READY participant cannot safely choose abort or commit alone.

# Formula and complexity sheet

| Item | Result |
|---|---|
| Clock delay | $(t_4-t_1)-(t_3-t_2)$ |
| Clock offset | $((t_2-t_1)+(t_3-t_4))/2$ |
| Tree broadcast/convergecast | $n-1$ messages each |
| LCR | $\Theta(n^2)$ messages worst case |
| Hirschberg-Sinclair | $O(n\log n)$ messages |
| GHS | $O(m+n\log n)$ messages |
| Lamport mutex | $3(N-1)$ messages/CS |
| Ricart-Agrawala | $2(N-1)$ messages/CS |
| Maekawa | about $3\sqrt N$ messages/CS |
| Suzuki-Kasami | 0 if token local, else at most $N$ |
| Kshemkalyani-Singhal classic one-phase | $4e-2n+2l$ messages, $2d$ hops |

# Past-paper practice

Use the [question index](00_Course_Guide/PYQ_TOPIC_INDEX.md). Start with the 2025 paper, then 2021, then both 2024 CTs. Do not memorize only the rapid tables; a 10-20 mark answer needs assumptions, numbered steps, correctness, cost, and a trace.

