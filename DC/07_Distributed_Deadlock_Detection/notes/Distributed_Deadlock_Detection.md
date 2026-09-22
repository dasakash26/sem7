# Distributed deadlock detection

## 1. System model

A wait-for graph (WFG) has one vertex per process. Edge $P_i\to P_j$ means $P_i$ is blocked waiting for a resource/action controlled by $P_j$.

In a distributed system, no site necessarily holds a current global WFG. Messages describing waits and releases may arrive in different orders, producing **phantom deadlocks** from stale or inconsistent graph information.

Correct detection requires:

- **Safety:** a reported deadlock really exists under the model.
- **Liveness:** every persistent deadlock is eventually reported.

## 2. Request models

### Single-resource model

Every blocked process waits for exactly one other process, so outdegree is at most one. A directed cycle is necessary and sufficient for deadlock.

### AND model

A process waits for all requested resources/processes. It can resume only when every required request is satisfied. A directed cycle is necessary and sufficient for a deadlocked set under the standard reusable-resource assumptions.

### OR model

A process waits until any one requested dependency is satisfied. A cycle is necessary but not sufficient.

A **knot** is a nonempty set $K$ such that every process reachable from a process in $K$ is also in $K$; there is no path from $K$ to an outside process that could satisfy a wait. A knot characterizes OR-model deadlock.

### P-out-of-Q model

A process issues $Q$ requests and can resume after any $P$ are granted. AND is $P=Q$; OR is $P=1$. This is a generalized/AND-OR request model.

## 3. Why a cycle may not mean deadlock

In the OR model, let $P_1$ wait for either $P_2$ or active $P_4$, while $P_2$ waits for $P_1$. The edges $P_1\to P_2\to P_1$ form a cycle, but $P_4$ may reply and release $P_1$. The cycle is not closed, so there is no knot.

In a resource-allocation graph with multiple resource instances, a cycle is also only necessary in general; another free instance may satisfy a request.

## 4. Knapp's classification

| Class | Main idea | Typical issue |
|---|---|---|
| Path pushing | Sites send paths/local WFG portions; receivers combine them to find a cycle | Messages can carry large paths; stale paths may create phantoms |
| Edge chasing | Lightweight probes follow wait edges; a return/condition detects a cycle | Model-specific; duplicate probes must be controlled |
| Diffusing computation | Query/flood expands dependencies; replies/echoes contract to initiator | Needs termination bookkeeping |
| Global-state detection | Record a consistent snapshot of WFG/process states, then evaluate it | Snapshot and graph-reduction overhead |

## 5. Chandy-Misra-Haas for the AND model

This is an edge-chasing algorithm.

Probe format: `PROBE(initiator, sender, receiver)`.

### Algorithm

1. Blocked $P_i$ initiates by sending `PROBE(i,i,j)` to every $P_j$ it waits for.
2. When blocked $P_k$ receives `PROBE(i,j,k)` for the first time for initiator $i$, it forwards `PROBE(i,k,m)` to every $P_m$ it waits for.
3. An active process discards probes because it is not part of a persistent wait chain.
4. If $P_i$ receives `PROBE(i,*,i)`, a dependency path has returned to the initiator; declare deadlock involving $P_i$.

Use `(initiator, sequence)` to distinguish overlapping detections and suppress duplicate propagation.

### Trace

For $P_1\to P_2\to P_3\to P_1$:

```text
P1 -> P2: PROBE(1,1,2)
P2 -> P3: PROBE(1,2,3)
P3 -> P1: PROBE(1,3,1)
```

The returning probe detects the cycle. If $P_3$ becomes active and removes its wait before forwarding, it discards the probe and the persistent cycle is not detected.

### Correctness idea

Every forwarded probe corresponds to a real chain of blocked wait edges from the initiator. A return therefore closes a cycle. If a persistent cycle contains the initiator, reliable delivery eventually carries a probe around it.

## 6. Chandy-Misra-Haas for the OR model

Cycle detection is inadequate; the algorithm uses a query-reply diffusing computation to determine whether the initiator's dependency set is closed.

Conceptual rules:

1. Blocked initiator $P_i$ sends `QUERY(i,i,j)` to every process in its current wait set.
2. A blocked process receiving its first query for initiation $i$ becomes engaged in that diffusion, records the parent/sender, and sends queries to every process in its own wait set.
3. A process that is active is a possible escape and does not support a deadlock conclusion.
4. An engaged process sends `REPLY` to its parent only after all queries it issued have been accounted for and none establishes an escape.
5. If the initiator receives replies completing the entire diffusion while it remains blocked, the reachable dependency set is closed: a knot exists.

State variables in textbook versions include an engagement identifier, a count of outstanding queries, and a parent. The correctness argument is closure: every outgoing dependency of the suspected set was explored and remained inside a blocked set.

## 7. Mitchell-Merritt single-resource algorithm

Assume each process waits on at most one other process. Each process has:

- a unique nondecreasing **private label**;
- a readable **public label**, initially equal to its private label.

Labels can be pairs `(counter, processID)` ordered lexicographically.

### Core behavior

- **Block:** when a process begins waiting, it raises both labels to a fresh value greater than its previous labels and the public label of the process it waits for.
- **Transmit:** public labels propagate backward, opposite the direction of WFG edges, through probe/control messages. A process ignores a label smaller than the public label it already knows; a larger one updates/continues.
- **Detect:** if a process receives/observes its own private label after it has propagated around a wait cycle, it detects deadlock.
- **Activate:** when its wait is satisfied, the process leaves the blocked dependency and resets the relevant detection state.

The largest circulating label dominates a cycle, so only one process detects that deadlock. That simplifies resolution: the detecting process can be selected as victim, or the priority variant makes the intended lowest-priority victim detect it.

## 8. Kshemkalyani-Singhal generalized detection

This algorithm targets generalized P-out-of-Q / AND-OR waits. A simple cycle or knot test is not sufficient.

### Record-and-reduce principle

1. **Outward sweep:** control messages record the part of the distributed WFG reachable from the initiator.
2. **Inward sweep:** the recorded WFG is reduced. A node is reduced when enough of its requests can be treated as satisfiable to make its unblocking predicate true.
3. Reduced information flows back and may allow predecessors to reduce.
4. If the initiator cannot be reduced after the sweep completes, it belongs to a generalized deadlock.

In the one-phase Kshemkalyani-Singhal design, recording and reduction overlap instead of waiting for a full snapshot and then starting reduction. The algorithm must preserve a consistent view while the live WFG changes; initiation IDs and timestamps distinguish executions and prevent obsolete edges from being treated as current.

For the classic one-phase algorithm, with $e$ reachable WFG edges, $n$ reachable nodes, $l$ leaves, and diameter $d$, the reported worst-case bounds are:

$$4e-2n+2l\text{ messages},\qquad 2d\text{ hops}.$$

Do not reduce a P-out-of-Q example to ordinary cycle detection. At each node, count/evaluate how many requested dependencies remain capable of satisfying its threshold.

## 9. Distributed reduction intuition

For a blocked node waiting for any 2 of 3 processes:

- If two successors can eventually run/reply, this node is reducible and not deadlocked.
- If at most one successor can reply because the rest remain inside an irreducible blocked set, the node remains blocked.

Repeatedly remove reducible nodes. A nonempty irreducible remainder contains the generalized deadlock.

## 10. WFG-based vs probe-based detection

| WFG/snapshot | Probe/diffusion |
|---|---|
| Builds or records graph information | Follows only relevant dependencies |
| Easy to run a centralized graph test | Avoids a permanent central graph |
| Can suffer stale-edge phantom deadlocks | Requires careful initiation/duplicate state |
| Potentially large graph messages | Often small messages but may traverse many edges |

## 11. False/phantom deadlocks

Example:

1. Detector receives `P1 waits for P2`.
2. $P_2$ releases $P_1$, but that update is delayed.
3. Detector receives a new `P2 waits for P1` edge.
4. Its inconsistent view contains a cycle that never existed simultaneously.

Defenses include consistent snapshots, edge timestamps/epochs, validation before resolution, and algorithms whose probes correspond to current blocked states.

Deadlock resolution itself can change the graph while another detection runs. Sequence numbers and state validation are required to avoid reporting a cycle already broken by an abort/release.

## 12. Why distributed avoidance is uncommon

Banker-style avoidance requires current global allocations, outstanding requests, and future maximum claims to test a safe state before every grant. In a distributed system that information is delayed, expensive to serialize, and fragile under failures. Detection and recovery are often more practical, though prevention by ordered locking is used when feasible.

## 13. Resolution

After detection, select a victim using cost, priority, work completed, resources held, rollback count, or deadline. Abort/rollback it, release resources, and notify affected sites. Resolution must be idempotent and tagged so multiple detectors do not abort excessive processes.

## 14. Common mistakes

- Saying every cycle is an OR-model deadlock.
- Calling a knot merely a cycle.
- Drawing resource-allocation edges and process WFG edges with the same meaning.
- Forwarding CMH-AND probes through active processes.
- Using CMH-AND unchanged for the OR model.
- Describing Kshemkalyani-Singhal as a ring-cycle probe; it is generalized record-and-reduce detection.
- Ignoring phantom deadlocks caused by an inconsistent WFG.

