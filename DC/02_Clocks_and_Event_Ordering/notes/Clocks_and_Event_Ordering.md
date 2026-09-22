# Clocks and event ordering

Replicas can receive the same updates in different orders and finish in different states, making event ordering a correctness concern.

![Replicas applying concurrent updates in different orders](../assets/replica-clock-inconsistency.png)

## 1. Physical clocks

Let $C_i(t)$ be process $i$'s clock reading at real time $t$.

- **Offset or skew:** difference between two clock readings at one instant.
- **Drift rate:** difference between a clock's rate and real time.
- **External synchronization:** $|C_i(t)-UTC(t)|\le D$.
- **Internal synchronization:** $|C_i(t)-C_j(t)|\le D$ for all $i,j$.

External synchronization within $D$ implies pairwise disagreement of at most $2D$. Internal synchronization alone gives no guarantee about UTC.

![Fast, perfect, and slow physical clocks](../assets/clock-drift.png)

*A rate error accumulates, so clock offset grows between synchronizations.*

### Cristian's method

A client sends a request at local time $t_0$, receives the server's timestamp $T_s$ at $t_1$, and assumes roughly equal forward and reverse delays. With negligible server processing, it estimates the server time as

$$T_s+\frac{t_1-t_0}{2}.$$

The estimate is uncertain because the two network delays need not be equal.

The four-timestamp formulation records client send $t_1$, server receive $t_2$, server send $t_3$, and client receive $t_4$:

$$\delta=(t_4-t_1)-(t_3-t_2),$$

$$\theta=\frac{(t_2-t_1)+(t_3-t_4)}{2}.$$

$\delta$ removes server processing time from the observed round trip. The offset $\theta$ averages the forward and reverse estimates under the symmetry assumption.

![Cristian clock-synchronization exchange](../assets/cristian-algorithm.png)

### Berkeley method

A master polls the members, estimates their offsets, removes faulty outliers, computes an average, and sends each member a relative adjustment. Berkeley synchronization aligns the group internally; it does not require UTC.

### Network Time Protocol

NTP servers are arranged in **strata**, or hierarchy levels, according to their distance from an accurate reference clock. Clients exchange timestamps, filter several samples, estimate delay and offset, and adjust their clocks gradually rather than moving time backward abruptly.

### Ordering with synchronised clocks

With synchronised clocks and network delay bounded by $\Delta$, a timestamped message with time $t$ can be held until $t+\Delta$ and then delivered in timestamp order. This works only when the receiver can also know that no lower-timestamp message is still missing.

## 2. Happened-before relation

Lamport's relation $\rightarrow$ is the smallest relation satisfying:

1. If $a$ and $b$ occur in the same process and $a$ occurs first, then $a\rightarrow b$.
2. If $a$ sends a message and $b$ receives it, then $a\rightarrow b$.
3. If $a\rightarrow b$ and $b\rightarrow c$, then $a\rightarrow c$.

Events are **concurrent**, written $a\parallel b$, when neither $a\rightarrow b$ nor $b\rightarrow a$.

- Happened-before is transitive and irreflexive.
- Concurrency is symmetric but not transitive.
- A causal path represents a possible flow of information.

![Causally related and concurrent events](../assets/causal-vs-concurrent.png)

*Events with no directed path in either direction are concurrent.*

## 3. Lamport scalar clocks

Each process $P_i$ stores an integer $L_i$.

```text
Initially: Li := 0
Before each local or send event: Li := Li + 1
Send m with timestamp Li
On receiving m with timestamp t:
    Li := max(Li, t) + 1
```

Lamport clocks satisfy

$$a\rightarrow b\implies L(a)<L(b).$$

The converse is false: two concurrent events can still receive different scalar timestamps. A total order can be formed by sorting $(L(a),processID)$, but the process-ID tie-break does not create causality.

![Lamport scalar-clock trace](../assets/lamport-clock-trace.png)

*In the diagram, $P_2$ is at time 4 and receives timestamp 6 from $P_1$, so it advances to $\max(4,6)+1=7$.*

## 4. Vector clocks

With $n$ processes, $P_i$ stores $V_i[1..n]$. The entry $V_i[j]$ is $P_i$'s knowledge of the progress of $P_j$.

```text
Initially: Vi := [0, ..., 0]
Before each event at Pi: Vi[i] := Vi[i] + 1
Send m with timestamp Vm := Vi
On receiving m at Pi:
    Vi[i] := Vi[i] + 1
    for every k: Vi[k] := max(Vi[k], Vm[k])
```

![Vector-clock update rules](../assets/vector-clock-rules.png)

Vectors are compared componentwise:

$$V<W\iff(\forall k:V[k]\le W[k])\land(\exists j:V[j]<W[j]).$$

For event timestamps,

$$a\rightarrow b\iff V(a)<V(b).$$

Incomparable vectors represent concurrent events.

### Short trace

1. $P_1$ sends $m_1$ with $[1,0,0]$.
2. $P_2$ performs a local event and reaches $[0,1,0]$.
3. On receiving $m_1$, $P_2$ increments to $[0,2,0]$ and merges to $[1,2,0]$.
4. $P_2$ sends $m_2$ with $[1,3,0]$.
5. $P_3$ independently sends $m_3$ with $[0,0,1]$.

$m_2$ and $m_3$ are concurrent because their vectors are incomparable.

![Vector-clock execution trace](../assets/vector-clock-trace.png)

## 5. Efficient and higher-order logical clocks

Exact causality generally requires a vector with $n$ components. The slides present two extensions for controlling overhead or recording deeper knowledge.

### Singhal-Kshemkalyani differential technique

Successive messages to the same destination often differ in only a few vector entries. A sender therefore transmits only changed pairs $(index,value)$ instead of the complete vector.

Process $P_i$ maintains:

- $LS_i[j]$: the value of $V_i[i]$ when it last sent to $P_j$;
- $LU_i[k]$: the value of $V_i[i]$ when component $V_i[k]$ was last updated; in other words, when $P_i$ last learned newer information about $P_k$.

When sending to $P_j$, it includes only components $k$ for which $LS_i[j] < LU_i[k]$: $P_i$ learned something newer about $P_k$ after its last message to $P_j$. The receiver takes the componentwise maximum for the transmitted entries. Storage is $O(n)$ per process, the average timestamp can be much smaller than $n$, and the worst case still carries all $n$ entries. The technique assumes FIFO delivery.

### Matrix time

Process $P_i$ maintains an $n\times n$ matrix $mt_i$:

- $mt_i[i,i]$ is $P_i$'s local logical clock;
- $mt_i[i,*]$ is $P_i$'s ordinary vector-clock view; its entry $mt_i[i,j]$ is the latest time of $P_j$ known directly to $P_i$; and
- $mt_i[j,k]$ means that $P_i$ believes $P_j$ knows about $P_k$ up to time $mt_i[j,k]$.

Vector time records what $P_i$ knows. Matrix time also records what $P_i$ knows that other processes know.

#### Updating the matrix

For every event, $P_i$ advances its own clock (usually $d=1$):

$$
mt_i[i,i] := mt_i[i,i]+d, \qquad d>0.
$$

Every message carries the sender's matrix $mt$. On receiving it from $P_j$, $P_i$ does two things. First, it merges $P_j$'s row into its own row: what $P_j$ knew is now also known to $P_i$.

$$
mt_i[i,k] := \max(mt_i[i,k], mt[j,k]) \qquad (1\le k\le n).
$$

Second, it takes the maximum of every matrix entry, so it also learns what the sender knew about other processes' knowledge:

$$
mt_i[k,l] := \max(mt_i[k,l], mt[k,l]) \qquad (1\le k,l\le n).
$$

It then advances its diagonal entry and delivers the message.

The slide's event $e$ occurs at $P_i$ after messages from $P_j$ and $P_k$ arrive.

![Figure 3.4: evolution of matrix time from the supplied slides](../assets/matrix-time-slide-figure.jpg)

At $e$, row $i$ says that $P_i$ knows $P_k$ up to $e_k^2$ and $P_j$ up to $e_j^2$. The cross entries say more: $mt_e[j,k]=x_k^1$ means $P_i$ knows that $P_j$ has seen $P_k$ only up to $e_k^1$; similarly, $mt_e[k,j]=x_j^1$ describes what $P_k$ knows about $P_j$. Those cross entries are what a vector clock cannot store.

#### Basic property

For a fixed process $P_l$, examine column $l$. If

$$
\min_k(mt_i[k,l])\ge t,
$$

then $P_i$ knows that every process knows $P_l$ has reached time $t$. An algorithm can use this to discard information from $P_l$ with timestamp at most $t$. The cost is $O(n^2)$ space.

## 6. Causal message delivery

Causal delivery requires that if `send(m1) -> send(m2)` and a process delivers both messages, it delivers $m_1$ before $m_2$. Concurrent messages may be delivered in either order.

### Birman-Schiper-Stephenson broadcast

For BSS, $V_i[k]$ counts causal broadcasts from $P_k$ known at $P_i$; it is not the all-events vector used in the previous section. Receiving another process's broadcast does not independently increment the receiver's own component.

The supplied slide shows why buffering is necessary:

![Enforcing causal communication using vector clocks](../assets/causal-communication-slide.jpg)

1. $P_0$ broadcasts $m$ with timestamp $[1,0,0]$.
2. $P_1$ receives $m$, so it knows $P_0$'s first broadcast. It then broadcasts $m^*$ with timestamp $[1,1,0]$. Therefore `send(m) -> send(m*)`.
3. Network delays allow $m^*$ to reach $P_2$ before $m$. At this point $V_2=[0,0,0]$.
4. For $m^*$, the sender condition passes because $T[1]=1=V_2[1]+1$. However, the dependency condition fails because $T[0]=1>V_2[0]=0$: the timestamp says that $m^*$ depends on a broadcast from $P_0$ that $P_2$ has not delivered.
5. $P_2$ buffers $m^*$. When $m$ arrives, it delivers $m$ and reaches $[1,0,0]$. The buffered message now satisfies both conditions, so $P_2$ delivers $m^*$ and reaches $[1,1,0]$.

Before $P_i$ broadcasts $m$, it increments $V_i[i]$ and attaches $T=V_i$. Process $P_j$, where $j\ne i$, may deliver $m$ only when:

1. $T[i]=V_j[i]+1$ - it is the next broadcast expected from $P_i$;
2. $T[k]\le V_j[k]$ for every $k\ne i$ - every other causal predecessor is already known.

Otherwise, $P_j$ buffers the message. After delivery it updates its vector componentwise and rechecks the buffer.

### Schiper-Eggli-Sandoz protocol

It provides causal point-to-point delivery by attaching destination-relevant dependencies and delaying a message until those dependencies have been delivered. Unlike BSS, it is not presented as a broadcast protocol.

## 7. Clock comparison

| Property           | Physical clock                          | Lamport clock                            | Vector clock                                 |
| ------------------ | --------------------------------------- | ---------------------------------------- | -------------------------------------------- |
| Meaning            | Approximate wall time                   | Causality-consistent scalar              | Exact causality in the model                 |
| Size               | One timestamp                           | One integer                              | $n$ integers                                 |
| Detect concurrency | No                                      | No                                       | Yes                                          |
| Ordering           | Numerical order may be wrong under skew | Total order needs a process-ID tie-break | Partial order; total order needs a tie-break |
| Main use           | Deadlines, leases, logs                 | Arbitration and ordering                 | Causal delivery and dependency tracking      |

## Sources

- [Logical clocks and event ordering](../sources/02_Logical-Clocks-and-Event-Ordering.pdf)
- [Synchronization of clocks](../sources/03_Synchronization-of-Clocks.pdf)
