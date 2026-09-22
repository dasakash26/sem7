# Message passing, leader election, and spanning trees

## 1. Message-passing executions

The communication network is a graph $G=(V,E)$ with $n=|V|$ processes and $m=|E|$ links. Each process can access only its local variables and incoming-message buffers.

A **configuration** contains every process's local state and every message currently in transit. A **delivery event** moves a message to a receiver's buffer; a **computation event** lets one process read inputs, change state, and send messages. The synchronous and asynchronous timing assumptions are defined in [Fundamental concepts](../../01_Fundamental_Concepts/notes/Fundamental_Concepts.md#synchronous-model).

![Processors connected by directed communication channels](../assets/process-channel-model.png)

## 2. Rooted spanning trees

A rooted spanning tree includes all $n$ processes, contains $n-1$ edges, and gives every non-root process one parent.

### Broadcast

The root sends a value to its children. Each non-root forwards the first value received from its parent to its own children.

- Messages: $n-1$
- Synchronous rounds or causal depth: tree height $h$
- Asynchronous wall-clock time: unbounded without a delay bound

![Broadcast over a rooted spanning tree](../assets/broadcast-example.png)

### Convergecast

Leaves send values toward the root. An internal process waits for all children, combines their values, and sends one result to its parent.

- Messages: $n-1$
- Causal depth: $h$
- Uses: aggregation, acknowledgement, termination, and election

![Convergecast over a rooted spanning tree](../assets/convergecast-example.png)

### Constructing a tree from a known root

```text
root sends EXPLORE to every neighbour
on first EXPLORE from q:
    parent := q
    send ACCEPT to q
    send EXPLORE to every other neighbour
on later EXPLORE from q:
    send REJECT to q
record ACCEPT senders as children
terminate after every incident edge is classified
```

The first-arrival rule produces a BFS tree under synchronous equal-round propagation. In an asynchronous execution, message delays can produce a tree that is DFS-like or neither BFS nor DFS.

![Different spanning trees caused by asynchronous delivery order](../assets/spanning-tree-executions.png)

## 3. Leader-election problem

Leader election requires every correct process to identify the same unique leader.

- **Safety:** at most one process is leader.
- **Agreement:** all correct processes identify the same leader.
- **Liveness:** an election eventually terminates with a correct leader under the stated assumptions.

A deterministic uniform algorithm cannot elect a leader in a perfectly symmetric anonymous ring. Every process starts in the same state, receives the same messages, and remains indistinguishable. Unique IDs, randomness, a distinguished process, or asymmetric topology is required.

Crash-tolerant election algorithms that use timeouts require synchrony or an eventually useful failure detector. In pure asynchrony, silence cannot distinguish a failed process from a delayed one.

Ring terminology:

- **Oriented:** all processes agree on clockwise and counter-clockwise directions.
- **Anonymous:** processes have no unique identifiers.
- **Uniform:** the same algorithm works without knowing the ring size.
- **Non-uniform:** the algorithm may depend on the ring size.

## 4. LCR unidirectional-ring election

Assumptions: reliable oriented unidirectional ring and unique comparable IDs.

```text
each process sends its own ID clockwise
on receiving candidate x:
    if x > ownID: forward x
    if x < ownID: discard x
    if x = ownID: declare leader and circulate ELECTED(ownID)
```

The maximum ID is never discarded and eventually returns to its owner. Every smaller ID encounters a larger one before returning, so only the maximum declares.

- Worst-case election messages: $\Theta(n^2)$
- Leader notification: $n$ additional messages
- Causal distance until declaration: $O(n)$

The worst arrangement places IDs in decreasing order along the message direction, causing many candidates to travel far before being discarded.

![Worst-case candidate traffic in LCR](../assets/lcr-worst-case.png)

## 5. Hirschberg-Sinclair election

Assumptions: reliable bidirectional ring and unique comparable IDs. Candidates probe exponentially larger neighborhoods.

In phase $k$, an active candidate sends an outward probe with hop limit $2^k$ in both directions.

1. A process suppresses a probe carrying an ID smaller than its own.
2. A surviving probe is relayed until it reaches its hop limit.
3. The last process returns a reply along the reverse path.
4. A candidate receiving both replies survives and doubles its radius.
5. A candidate receiving its own probe has covered the ring and becomes leader.

For example, in phase $k=1$, candidate 12 probes two hops in each direction. Encountering a larger ID eliminates it; two returning replies let it enter the next phase.

![Exponentially expanding probes in Hirschberg-Sinclair](../assets/hs-probes.png)

At phase $k$, winners are far enough apart that only $O(n/2^k)$ candidates remain, and each creates $O(2^k)$ probe/reply traffic. Each phase therefore costs $O(n)$ and there are $O(\log n)$ phases:

$$M(n)=O(n\log n).$$

This is asymptotically optimal for asynchronous rings of unknown size: every comparison-based election algorithm requires $\Omega(n\log n)$ messages in the worst case.

## 6. Ring election with failures and concurrent initiators

Unlike LCR, this protocol begins when one or more processes detect that an existing coordinator has failed. It circulates `ELECTION(id,attribute)` clockwise. A process forwards a larger candidate; if the candidate is smaller and the process has not already competed, it replaces the candidate with its own. When an ID returns to its owner, that process sends `ELECTED(id)` around the ring.

- Best case: the eventual leader initiates, giving $2n$ messages.
- Worst case: the initiator is immediately after the eventual leader, giving $3n-1$ messages.

With concurrent initiators, each process caches the highest initiator ID observed and suppresses messages from lower-ID election runs. Only the highest surviving run completes.

If the proposed leader fails during the election, a predecessor or another detector must start a new run.

## 7. Bully algorithm

Assumptions: known membership, totally ordered IDs, crash failures, and usable timeouts.

1. A process suspecting the coordinator sends `ELECTION` to all higher-ID processes.
2. A recipient replies `OK` and starts its own election unless one is already running.
3. If no higher process replies, the initiator declares and sends `COORDINATOR` to lower IDs.
4. If a higher process replies but no coordinator is announced, the waiting process times out and starts again.

The highest live process eventually wins when failures stop and timing assumptions hold. The worst case uses $O(n^2)$ messages when a low-ID process starts; the slide gives a worst-case completion chain of five message-transmission times.

## 8. Consensus connection and practical systems

Election chooses a process; consensus chooses a value. A replicated system can run consensus on a proposed process ID and treat the chosen value as its leader.

### Paxos

Paxos uses proposers, acceptors, learners, and majority quorums.

1. A proposer sends `PREPARE(n)`.
2. Acceptors promise not to accept smaller proposal numbers and report any value already accepted.
3. After a majority of promises, the proposer must use the value from the highest-numbered previously accepted proposal, if one exists; otherwise it may use its own value.
4. A value is chosen when a majority accepts it.

The value-selection rule together with intersecting majorities prevents two different values from being chosen.

### Chubby and ZooKeeper examples

The slides present Chubby as a replica group in which a candidate collects votes and a majority elects one master. Each server votes for at most one candidate in a run.

The ZooKeeper case study uses increasing sequence IDs: the highest current ID is leader, and each process monitors the next-higher participant. If that participant was the leader and fails, the monitor takes over; otherwise it waits and checks again. This is a simplified slide-level illustration of avoiding an all-to-all election flood.

## 9. Distributed minimum spanning tree

The supplied spanning-tree deck presents a GHS-style construction. A **fragment** is a component of the growing MST, and each fragment has a leader UID.

1. **Initialize:** every node begins as a one-node level-0 fragment.
2. **Search:** the leader broadcasts a search within its fragment.
3. **Test edges:** neighboring nodes exchange fragment UIDs. Equal UIDs identify internal edges; different UIDs identify outgoing edges.
4. **Select the MWOE:** each node reports its lightest outgoing candidate toward the leader, which chooses the fragment's minimum-weight outgoing edge.
5. **Merge:** the two fragments join across the selected edge. The larger endpoint UID becomes the new fragment leader and is broadcast through the merged fragment.
6. **Repeat:** if no outgoing edge remains, the single fragment is the MST.

![A fragment identifies its outgoing edges](../assets/mwoe-outgoing-edges.png)

The cut property makes each merge safe: a minimum-weight edge crossing a fragment boundary belongs to some MST.

![Two fragments merge through an MWOE](../assets/mwoe-merge.png)

Levels coordinate concurrent merges. Equal-level fragments form a fragment one level higher; when levels differ, the lower-level fragment joins the higher-level one. Fragment sizes grow geometrically, so there are $O(\log n)$ levels.

The lecture slides give:

- Time complexity: $O(n\log n)$
- Communication complexity: $O((n+m)\log n)$

The standard tighter classical GHS message bound is $O(m+n\log n)$; the slide bound is the expected classroom analysis for this deck.

## Sources

- [Classical leader-election algorithms](../sources/04_Classical-Leader-Election-Algorithms.pdf)
- [Distributed leader-election protocols](../sources/05_Distributed-Leader-Election-Protocols.pdf)
- [Message-passing algorithms](../sources/06_Message-Passing-Algorithms.pdf)
- [Distributed spanning tree](../sources/07_Distributed-Spanning-Tree.pdf)
