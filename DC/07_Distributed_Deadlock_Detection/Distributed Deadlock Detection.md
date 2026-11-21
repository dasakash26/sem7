# 7. Distributed deadlock detection

A **deadlock** occurs when a group of processes remains blocked because each needs an action that the group cannot produce. If $P_1$ holds a lock needed by $P_2$ and $P_2$ holds a lock needed by $P_1$, neither can proceed and release its lock. The detector must infer such dependencies across sites while grants, releases, and detection messages are in flight. A correct detector needs **safety** (a reported deadlock is real) and **progress** (a persistent deadlock is eventually reported under the delivery assumptions).

## Wait-for graphs and request models

A **wait-for graph** (WFG) has one vertex per process. An edge $P_i\to P_j$ means that $P_i$ is waiting for a resource or action controlled by $P_j$. The request model determines when those outgoing edges keep a process blocked.

| Model | When a process can resume | What to look for |
|---|---|---|
| Single resource | Its one outstanding request is satisfied. | A directed cycle. |
| AND | Every required request is satisfied. | A directed cycle under the standard exclusive-resource model. |
| OR | Any one of its alternative requests is satisfied. | A closed strongly connected set of blocked processes, called a knot. |
| P-out-of-Q | At least $P$ of its $Q$ outstanding requests are satisfied. | Nodes that remain blocked after recursively reducing all satisfiable waits. |

In the OR model, a cycle can have an escape. If $P_1$ waits for either $P_2$ or active $P_3$, and $P_2$ waits for $P_1$, then $P_3$ can release $P_1$. With the alternative edge removed, $\{P_1,P_2\}$ is a knot. Processes outside a knot can also remain blocked if all their alternatives ultimately depend on deadlocked processes.

### Why a resource-allocation cycle can be harmless

A resource-allocation graph has request arrows from processes to resource types and allocation arrows in the opposite direction. With one instance per type and exclusive, nonpreemptible resources, a cycle means every process on it is blocked by the next one. Conversely, following the required waits in a finite deadlocked set must eventually revisit a process.

Consider one instance of resource $R$ and two instances of $S$:

| Process | Holds | Requests |
|---|---|---|
| $P_1$ | $R$ | One $S$ |
| $P_2$ | One $S$ | $R$ |
| $P_3$ | The other $S$ | Nothing |

The graph has the cycle $P_1\to S\to P_2\to R\to P_1$. However, $P_3$ can finish and release its copy of $S$. This lets $P_1$ finish and release $R$, so $P_2$ can finish too. With multiple resource instances, test actual availability and possible releases before declaring deadlock.

## Knapp's classification

| Detection method | What travels across the network | How it establishes deadlock |
|---|---|---|
| Path pushing | Wait paths or parts of a WFG | A site joins paths and applies a graph test. |
| Edge chasing | Short probes | Probe propagation establishes a dependency path or cycle. |
| Diffusing computation | Queries and replies | An outward search and return messages account for all alternatives. |
| Global-state detection | Local and channel snapshot records | A collector applies the graph test to a consistent view. |

## Mitchell–Merritt for single-resource waits

Each blocked process has at most one outgoing WFG edge. It carries a unique nondecreasing **private label** and a **public label** that other processes can read. Initialize both labels to the same unique value.

1. **Block.** When a process starts waiting, give it a fresh private and public label greater than its previous labels and the holder's public label.
2. **Transmit.** A waiter reads its holder's public label. If larger than its own public label, it copies it. Values therefore travel in the direction opposite the WFG edges.
3. **Detect.** A waiter detects when it sees its own private label at the process it waits for and its own public label still equals that private label.
4. **Activate.** Remove the wait edge when the resource becomes available or the process abandons the wait. A change of holder creates a new wait and uses the block rule again.

For a persistent cycle $A\to B\to C\to A$, suppose $C$ owns its maximum label, 9. First $B$ copies 9 from $C$, then $A$ copies it from $B$. Now $C$ sees 9 at $A$ and detects the cycle after two transmissions. Only the owner of the maximum can detect this persistent cycle. A priority variant chooses the lowest-priority victim. If waits can be removed independently by timeouts, crashes, or resolution, validate an apparent cycle before aborting. [Mitchell and Merritt's original paper](https://groups.csail.mit.edu/tds/papers/Merritt/podc84.pdf)

## Chandy–Misra–Haas for the AND model

The detector chases wait edges using `PROBE(initiator, sender, receiver)`. In a process-level trace, a blocked initiator sends a probe on each outgoing edge. A blocked receiver forwards the probe on its outgoing wait edges once per detection instance. An active receiver discards it. If every dependency on the probe path is valid under the algorithm’s checks, a probe returning to its still-blocked initiator reports a cycle.

For $P_1\to P_2\to P_3\to P_1$:

```text
P1 -> P2   PROBE(1,1,2)
P2 -> P3   PROBE(1,2,3)
P3 -> P1   PROBE(1,3,1)
```

This is the cycle obtained when $P_1$ holds $R_1$ and requests $R_2$, $P_2$ holds $R_2$ and requests $R_3$, and $P_3$ holds $R_3$ and requests $R_1$. Allocation arrows are omitted from the WFG because the resource holders have already been resolved.

In the site-controller algorithm, a probe is accepted if its receiver is still blocked, has not already recorded the initiator's dependency during that blocked interval, and has not already sent positive replies for all the probe sender's outstanding requests. Traverse local dependency paths at the controller and send probes on intersite waits. Reset the dependency flag when the process resumes. These checks are part of the algorithm, even when a textbook trace places one process at each site.

**Release during detection.** If $P_3$ releases $R_3$ before $P_1$ starts, the grant of $R_3$ to $P_2$ breaks the cycle. If $P_2$ already received the grant, it is active and discards the probe. If the grant is still in flight, $P_2$ may send PROBE(1,2,3). Once $P_3$'s controller has sent the positive reply for that request, the receive check rejects the obsolete probe even though $P_3$ itself may still wait for $R_1$. A bare unlock and the protocol’s positive grant are separate events. If an implementation separates them, track the relevant wait across that handoff.

Each reached edge carries at most one probe in the process-level model, so one run takes $O(e)$ messages for $e$ reached wait edges. Detection delay is proportional to the probe path length in message hops. The three-process example uses three probes and three hops when each process is on a separate site. A probe carries three identifiers, whereas path pushing may send entire wait paths or graph fragments. This reduces message payload and avoids collecting the whole WFG at a site. It does not imply fewer messages for every workload. [Chandy, Misra, and Haas, resource model](https://www.cs.utexas.edu/~misra/scannedPdf.dir/DistrDeadlockDetection.pdf)

### Worked trace across two site controllers

In the [2025 semester paper, p. 3](../10_Past_Questions/sources/Distributed%20Computing%20sem%20paper.pdf#page=3), name the left site's top, bottom-right, and bottom-left processes $P_1,P_2,P_3$. Name the right site's left and right processes $P_4,P_5$. The arrows are:

| Edge location | Waits |
|---|---|
| Within the left site | $P_2\to P_1$ and $P_2\to P_3$ |
| Within the right site | $P_4\to P_5$ |
| Between sites | $P_1\to P_4$ and $P_4\to P_2$ |

Start detection at $P_2$. Its controller follows $P_2\to P_1$ locally and sends $\operatorname{probe}(2,1,4)$ to the right site. There $P_4$ has a valid intersite wait on $P_2$, so its controller sends $\operatorname{probe}(2,4,2)$ back. The first field stays 2 even though the probe's sending process changes. The return detects the AND cycle $P_2\to P_1\to P_4\to P_2$. The side waits on $P_3$ and $P_5$ do not require network probes and cannot break a cycle of required waits.

## Chandy–Misra–Haas for the OR model

A returning probe only proves that one dependency path closed. An OR detector must account for **all** possible sources of reactivation. The CMH query–reply diffusion assumes reliable FIFO communication, including the underlying messages.

| Event | Action for this detection instance |
|---|---|
| A blocked initiator starts a run. | Give it an instance ID. Query every alternative and keep an outstanding count. |
| A blocked process receives its first query. | Record the sender as its engager, query all its alternatives, and count the expected replies. |
| A continuously blocked process receives another query for the same run. | Reply immediately to that sender so a cross edge does not create a second engager. |
| An engaged process receives all its replies and has remained blocked. | Send the delayed reply to its engager. The initiator declares when its own count reaches zero. |
| A process becomes active. | Invalidate its blocked status for existing runs. Active processes discard detection messages. |

For $A$ waiting on $B$ or $C$, and both $B$ and $C$ waiting on $A$, queries from $B$ and $C$ back to the already engaged $A$ get immediate replies. Both branches then reply to $A$. All alternatives stayed inside the blocked set. If $C$ can run, its branch gives no valid completion and $A$ cannot declare. One run sends $O(e)$ queries and replies over the reached wait edges. [Chandy, Misra, and Haas, communication model](https://www.cs.mcgill.ca/~carl/distdeadcmh.pdf)

## Kshemkalyani–Singhal for P-out-of-Q waits

A threshold can require more than one alternative but fewer than all of them. The detector models the computation as a distributed WFG with an unblocking condition at each site. Sites have logical channels, and messages can be delayed but eventually arrive. The base analysis assumes no process or channel failure during detection. Sites use Lamport clocks to tag the blocked interval. In the underlying computation, a blocked site sends REQUEST to its alternatives and waits for the required number of grants (REPLY). It sends CANCEL for unanswered requests once enough grants arrive. The detector records the WFG outward and reduces it inward. These two sweeps can overlap.

| Message | Role |
|---|---|
| `FLOOD` | Records a dependency. A newly reached blocked node records its remaining threshold and expands the search along its outgoing waits. |
| `ECHO` | Simulates satisfying a wait. An active or reduced node can return an echo to a dependent. An obsolete wait can also be removed once its release is known. |
| `SHORT` | Returns accounting weight to the initiator when the current message causes no further expansion or reduction. |

1. The initiator records its blocked state, marks the detection instance with its timestamp, and divides weight 1 among its outgoing FLOODs.
2. A newly reached blocked node with a valid incoming request divides the incoming weight among its own FLOODs. A FLOOD reaching an active or already reduced node, or finding that the receiving site has already answered the request, returns an ECHO carrying its weight. When the node is recorded and still blocked, a later FLOOD on another valid incoming edge records that edge and sends a SHORT.
3. An ECHO removes a recorded dependency and reduces the number of grants still needed. When the threshold reaches zero, the reduced node can send ECHOs on recorded incoming dependencies. It divides the triggering weight among those messages. If the threshold was not reached, it returns the weight as a SHORT.
4. The initiator can rule out its deadlock once it reduces. If the snapshot still shows it blocked and the run matches its current blocked interval, it declares deadlock when all weight returns. The initiator starts with weight 1. Splitting or returning weight preserves that total until the decision. Each recorded outgoing edge is expanded at most once, with finite follow-up messages. Eventual delivery either reduces the initiator or returns all weight, at which point no ECHO is left in flight. [Algorithm and worked examples by Kshemkalyani and Singhal](https://www.cs.uic.edu/~ajayk/Chapter10.pdf)

### Worked trace with two outgoing waits at each site

Let $A$ wait on $B,C$, $B$ on $C,D$, $C$ on $D,A$, and $D$ on $A,B$. All are blocked and each needs at least one of its two replies. Start at $A$ and deliver the two initial FLOODs first, then deliver $B$'s FLOOD to $D$ before $C$'s.

| Messages | Weight and effect |
|---|---|
| $A\to B$ and $A\to C$ | $1/2$ each. Both sites record and expand. |
| $B\to D$ | $1/4$. $D$ records and expands to $A$ and $B$ with $1/8$ each. |
| $B\to C$, $C\to D$, $C\to A$ | $1/4$ each. Each reaches a recorded blocked site, so each weight returns as a SHORT. |
| $D\to A$ and $D\to B$ | $1/8$ each. These also return as SHORTs. |

Eight FLOODs and five SHORTs account for this run. The return is $3/4+2/8=1$. With no ECHO to remove an edge, $A$ is still blocked and detects deadlock.

### How a single available site can start reduction

| Process | Needs this many replies | Alternatives |
|---|---:|---|
| $A$ | 2 | $B,D$ |
| $B$ | 1 | $C,D$ |
| $C$ | 2 | $A,B$ |
| $D$ | 0 | Active |

Start with $D$ ready. Its availability reduces $B$, which needs only one reply. Now $A$ has $B$ and $D$, so it reduces. With $A$ and $B$ available, $C$ also reduces. If $B$ instead needs both $C$ and $D$, the first step fails and the other three remain blocked. The same edges give different outcomes because the thresholds changed.

In the original one-phase algorithm, recording and reduction overlap. With $e$ edges, $n$ nodes, $l$ leaves, and diameter $d$ in the recorded graph, the reported worst case is $4e-2n+2l$ messages and $2d$ hops under the paper's unit-hop timing measure. [Kshemkalyani and Singhal's original paper](https://doi.org/10.1109/32.263754)

## Phantom deadlocks and resolution

| Event | What a stale detector might see |
|---|---|
| $P_1$ waits for $P_2$. | The detector adds $P_1\to P_2$. |
| $P_2$ releases the resource and $P_1$ stops waiting. | The removal update is still in transit. |
| $P_2$ then waits for $P_1$ and its update arrives first. | The detector sees a cycle containing an old and a new edge. |

The cycle never existed simultaneously. Consistent snapshots, dependency epochs, or another cycle-validation pass help prevent an abort based on stale information. Once a deadlock is validated, select a victim using cost, priority, or work already completed. Abort or roll back the victim, release resources, and notify dependent sites. Coordinate resolution so multiple detectors do not abort extra victims for the same cycle.

A Banker-style avoidance scheme requires future maximum claims and a timely view of distributed allocations before granting requests. Acquiring that information adds coordination cost and applications may not know their claims in advance. Ordered acquisition is a prevention strategy. Detection and rollback are useful where neither arrangement is practical.

## Course material

The algorithms follow the [syllabus, p. 41](../01_Fundamental_Concepts/sources/01_Introduction-DC.pdf#page=41). See the [past-question index](../00_Course_Guide/PYQ_TOPIC_INDEX.md) for practice.
