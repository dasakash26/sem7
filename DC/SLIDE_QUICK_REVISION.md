# Distributed Computing - slide quick revision

Covered: Unit 1 fundamentals, Unit 2 clocks and event ordering, Unit 5 message passing, leader election, and spanning trees.

## 1. Fundamentals

### Distributed system

Autonomous computers coordinate by communication to provide a common service. In a loosely coupled system, each machine has private memory and communicates by messages. In a tightly coupled system, processors share memory.

| Software layer | Main idea |
|---|---|
| Distributed OS | Multiple machines presented as one system |
| Network OS | Machines remain explicitly separate |
| Middleware | Common services above OSs: RPC, messaging, naming, transactions |

### Why distributed systems?

- Resource sharing
- Parallel processing
- Geographic distribution
- Scalability
- Fault tolerance and availability

### Transparency

| Type | Hides |
|---|---|
| Access | Local versus remote access method |
| Location | Physical location of a resource |
| Replication | Multiple copies |
| Failure | Failure and recovery details |
| Migration | Movement of a resource/service |
| Concurrency | Simultaneous users/operations |
| Performance | Internal load balancing |
| Scaling | Effects of system growth |

### Models

- **Message passing:** private state; processes communicate through messages.
- **Shared memory:** processes communicate through shared registers/objects.
- **Synchronous:** known bounds on processing time and message delay; timeouts and rounds are meaningful.
- **Asynchronous:** no known finite bound; silence may mean crash or delay.
- **Partial synchrony:** unknown bounds initially; useful bounds hold eventually.

### Correctness and cost

- **Safety:** nothing bad happens.
- **Liveness:** something good eventually happens.
- **Termination:** every admissible execution finishes.
- **Message complexity:** number of messages.
- **Bit complexity:** total communicated bits.
- **Round complexity:** synchronous rounds.
- **Space complexity:** local state/storage.

## 2. Clocks and event ordering

### Physical clocks

Let $C_i(t)$ be process $i$'s clock at real time $t$.

- **Offset/skew:** difference between two clock readings at one instant.
- **Drift rate:** difference between a clock's rate and real time.
- **External synchronization:** $|C_i(t)-UTC(t)|\le D$.
- **Internal synchronization:** $|C_i(t)-C_j(t)|\le D$.

External synchronization within $D$ gives pairwise disagreement at most $2D$. Drift accumulates between synchronizations.

### Cristian's method

Client sends at $t_0$, receives server timestamp $T_s$ at $t_1$:

$$T_s+\frac{t_1-t_0}{2}$$

Assumption: forward and reverse network delays are approximately equal.

Four timestamps: client send $t_1$, server receive $t_2$, server send $t_3$, client receive $t_4$.

$$\delta=(t_4-t_1)-(t_3-t_2)$$

$$\theta=\frac{(t_2-t_1)+(t_3-t_4)}2$$

$\delta$ excludes server processing from round-trip delay. $\theta$ estimates server-minus-client offset.

### Berkeley and NTP

- **Berkeley:** master polls clocks, discards outliers, averages offsets, sends relative adjustments; internal synchronization.
- **NTP:** hierarchical strata; filters timestamp samples and adjusts clocks gradually.

### Happened-before

$$a\rightarrow b$$

when:

1. $a$ occurs before $b$ in one process;
2. $a$ sends a message received by $b$;
3. transitivity applies.

$$a\parallel b$$

means neither event happened before the other.

### Lamport scalar clock

```text
Initially: L_i := 0
Before local/send event: L_i := L_i + 1
Send timestamp L_i
Receive timestamp t: L_i := max(L_i, t) + 1
```

$$a\rightarrow b\implies L(a)<L(b)$$

Converse is false. Sorting $(L(a), processID)$ gives a total order, not a causal order.

### Vector clock

Each process $P_i$ stores $V_i[1..n]$.

```text
Before event at P_i: V_i[i] := V_i[i] + 1
Send message with V_i
Receive V_m: increment V_i[i], then V_i := max(V_i,V_m) componentwise
```

$$V<W\iff(\forall k:V[k]\le W[k])\land(\exists j:V[j]<W[j])$$

$$a\rightarrow b\iff V(a)<V(b)$$

Incomparable vectors mean concurrent events.

### Efficient and higher-order clocks

- **Singhal-Kshemkalyani:** send only changed vector entries to the same destination. Send component $k$ to $P_j$ when $LS_i[j]<LU_i[k]$. FIFO required.
- **Matrix time:** $mt_i[i,*]$ is $P_i$'s vector-clock view; $mt_i[j,k]$ records what $P_i$ believes $P_j$ knows about $P_k$. Cost: $O(n^2)$ space.

### Causal delivery: BSS broadcast

Sender $P_i$ increments $V_i[i]$ and attaches $T=V_i$. Receiver $P_j$ delivers only if:

$$T[i]=V_j[i]+1$$

and

$$T[k]\le V_j[k]\quad\forall k\ne i$$

Otherwise buffer. First condition preserves sender order; second ensures every causal predecessor has been delivered.

## 3. Message passing, trees, and election

### Execution model

- **Configuration:** every local process state plus all channel messages.
- **Delivery event:** in-transit message enters a receiver buffer.
- **Computation event:** one process reads state/messages, changes state, and sends messages.
- Asynchronous admissibility: every message is eventually delivered and every process continues taking steps.

### Rooted spanning tree

A rooted spanning tree has all $n$ processes, $n-1$ edges, one root, and one parent for every non-root process.

| Primitive | Direction | Messages | Causal depth |
|---|---|---:|---:|
| Broadcast | root to children | $n-1$ | tree height $h$ |
| Convergecast | children to root | $n-1$ | $h$ |

- **Broadcast:** root sends to children; a process forwards a received message to its children.
- **Convergecast:** leaves send upward; an internal process waits for every child, aggregates, and sends to its parent.
- **Tree construction:** first received `M` fixes `parent`; later arrivals receive `already`; senders of `parent` become children.
- Equal synchronous rounds give BFS; asynchronous first arrival need not give BFS.

### Leader-election specification

- Exactly one non-faulty process becomes leader.
- Every non-faulty process agrees on the leader.
- Election terminates under the model assumptions.
- Anonymous symmetric rings cannot deterministically elect a leader.

### LCR / LeLann-Chang-Roberts

Unidirectional oriented ring with unique IDs.

```text
Send own ID left.
Receive j from right:
    j > id: forward j left
    j = id: elect self
    j < id: discard
```

Largest ID reaches every process and returns to its owner. Time: $O(n)$. Worst-case messages: $\Theta(n^2)$.

### Hirschberg-Sinclair

Bidirectional ring. In phase $k$, a candidate probes distance $2^k$ in both directions.

- Smaller-ID probes are swallowed.
- A surviving probe at radius $2^k$ returns a reply.
- A candidate receiving both replies enters phase $k+1$.
- A process receiving its own probe elects itself.

Each phase costs $O(n)$ messages; there are $O(\log n)$ phases:

$$O(n\log n)$$

### Failure-triggered ring election

A process detecting coordinator failure circulates `ELECTION(id, attribute)` clockwise.

- Larger candidate: forward.
- Smaller candidate: replace with own candidate if not already competing.
- Candidate returning to owner: owner circulates `ELECTED`.

Best case: $2n$ messages. Worst case: $3n-1$ messages. Concurrent initiators suppress lower-ID runs.

### Bully algorithm

Known membership, ordered IDs, usable timeout/failure detector.

1. Suspector sends `ELECTION` to all higher IDs.
2. Higher process replies `OK` and starts its own election.
3. No higher reply: declare coordinator and notify lower IDs.
4. `OK` but no coordinator: timeout and retry.

Highest live ID wins. Worst-case messages: $O(n^2)$. Slide worst-case completion chain: 5 message-transmission times.

### Paxos, Chubby, ZooKeeper

- **Paxos:** proposer sends `PREPARE`; acceptors promise and return accepted values; proposer chooses required value; majority accepts; learners learn result. Intersecting majorities preserve safety.
- **Chubby:** candidate receives votes; a majority elects one master; each server votes at most once per run.
- **ZooKeeper slide example:** highest sequence ID is leader; each process monitors its next-higher process.

### GHS-style distributed MST

1. Each node is a level-0 fragment.
2. Fragment finds its minimum-weight outgoing edge (MWOE).
3. Fragments merge over the MWOE.
4. Equal levels create the next level; lower level joins higher level.
5. No outgoing edge means one MST fragment remains.

Slide bounds: time $O(n\log n)$; communication $O((n+m)\log n)$.

## Last-minute formulas

$$\delta=(t_4-t_1)-(t_3-t_2)$$

$$\theta=\frac{(t_2-t_1)+(t_3-t_4)}2$$

$$a\rightarrow b\implies L(a)<L(b)$$

$$a\rightarrow b\iff V(a)<V(b)$$

$$T[i]=V_j[i]+1,\qquad T[k]\le V_j[k]\;\forall k\ne i$$

$$\text{LCR}=\Theta(n^2),\qquad \text{HS}=O(n\log n),\qquad \text{Bully}=O(n^2)$$
