# Distributed mutual exclusion

## 1. Problem and requirements

Processes repeatedly execute:

```text
request section -> critical section (CS) -> release section -> remainder section
```

Required properties:

- **Mutual exclusion:** at most one process is in CS.
- **Progress/deadlock freedom:** if CS is free and requests exist, some requester enters.
- **Starvation freedom:** every correct requester eventually enters.
- **Fairness:** often timestamp/FCFS order for causally observable requests.

Usual model for the basic algorithms: reliable message delivery, distinct process IDs, and no failures during a request. Failure handling is a separate property, not automatic.

## 2. Performance metrics

- Messages per CS execution
- Response time from request to entry
- Synchronization delay between consecutive CS executions
- Throughput under high load
- Local storage
- Fault tolerance and recovery

Let $T$ be one maximum message-transfer delay in a synchronous analysis.

## 3. Central coordinator

1. Requester sends `REQUEST` to coordinator.
2. Coordinator sends `GRANT` if free or queues request.
3. Exiting process sends `RELEASE`; coordinator grants next.

Cost: 3 messages per CS (request, grant, release). Synchronization delay: about $2T$ from release to next grant if measured as two one-way transfers. It is simple and fair with a FIFO queue, but the coordinator is a bottleneck and single failure point unless replicated/elected.

## 4. Lamport permission algorithm

Every process maintains the same logical-timestamp-ordered request queue.

### Request

1. Increment Lamport clock.
2. Insert own `REQUEST(ts,i)` in local queue.
3. Send it to all $N-1$ other processes.

### Receive request

1. Update clock.
2. Insert request in local queue ordered by `(timestamp, processID)`.
3. Send timestamped `ACK`.

### Enter

Process $P_i$ enters when:

1. its request is at the head of its local queue; and
2. it has received a later message/ACK from every other process.

### Release

Remove own request and send `RELEASE` to all. Receivers delete it.

### Correctness

All processes order requests identically. If two processes were in CS, each own request would have to be first in its queue, impossible under one total order after acknowledgements establish that all processes know the request.

### Cost

$3(N-1)$ messages per CS: request, acknowledgement, release.

## 5. Ricart-Agrawala

It removes explicit release broadcasts by deferring replies.

### Request

Send `REQUEST(ts,i)` to all other processes and wait for all $N-1$ replies.

### On receiving `REQUEST(ts,j)` at $P_i$

Reply immediately if:

- $P_i$ is not requesting/in CS; or
- $(ts,j)$ has higher priority (smaller lexicographic value) than $P_i$'s request.

Otherwise defer the reply.

### Release

Exit CS and send replies to every deferred requester.

### Cost and behavior

- Messages: $2(N-1)$ per CS
- FCFS according to Lamport timestamp for competing known requests
- A crashed/nonresponding process can block every requester unless membership/failure handling is added

### Trace

$P_1$ requests at timestamp 47 and $P_2$ at 32. Each sends to all. Since `(32,P2)` has priority, $P_1$ replies to $P_2$ while $P_2$ defers its reply to $P_1$. After receiving all replies, $P_2$ enters. On exit it sends the deferred reply; then $P_1$ enters.

## 6. Roucairol-Carvalho optimization

This optimization observes that a permission already received from process $P_j$ can be retained across later CS requests until $P_i$ sends a reply/permission back to $P_j$.

- First request may still need up to $2(N-1)$ request/reply messages.
- Repeated low-contention entries can reuse permissions and send fewer messages, potentially none when all required permissions are retained.
- When $P_i$ yields permission to another requester, it must reacquire that permission before a later entry.

The safety argument is ownership of permission: a process cannot treat permission from $P_j$ as retained after granting the conflicting right back. The optimization improves average cost, not the same fixed worst-case bound under contention.

## 7. Maekawa quorum algorithm

Each process $P_i$ has a voting set $R_i$ such that:

- $P_i\in R_i$;
- $R_i\cap R_j\ne\varnothing$ for all $i\ne j$;
- balanced constructions have $|R_i|\approx\sqrt N$.

Each voter grants at most one request at a time.

### Basic protocol

1. $P_i$ sends `REQUEST` to all voters in $R_i$.
2. A free voter sends `GRANT`; otherwise it queues the request.
3. $P_i$ enters after every voter in $R_i$ grants.
4. On exit, it sends `RELEASE` to all voters, which grant queued requests.

### Why mutual exclusion holds

Any two request quorums intersect. Their common voter cannot grant both simultaneously, so both requesters cannot enter together.

### Cost

Approximately $3\sqrt N$ messages for request, grant, and release in the basic design.

### Deadlock

Maekawa's basic algorithm can deadlock. Example: $P_i$ holds one vote and waits for another held by $P_j$, while $P_j$ waits for $P_i$'s vote. Timestamp-priority variants use messages such as `FAILED`, `INQUIRE`, `RELINQUISH`, and `YIELD` to break circular waiting.

Unlike Ricart-Agrawala, basic Maekawa entry order is not simply global increasing timestamp order because different requesters collect different intersecting voter sets and can obtain partial grants in different orders.

## 8. Agrawal-El Abbadi tree quorum

Processes are arranged as a logical complete binary tree. A quorum is built recursively:

- if a node is available, include it and continue through an appropriate child path;
- if it is unavailable, obtain quorums from both subtrees.

Any two valid tree quorums intersect. In the failure-free best case a root-to-leaf path gives $O(\log N)$ quorum size/messages; failures force branching into both subtrees, increasing cost while preserving availability when a quorum can still be formed.

The mutual-exclusion protocol is then request/grant/release over the selected quorum. The point of the algorithm is the cost-availability tradeoff: very small best-case quorum, graceful growth when nodes fail, and intersection for safety.

## 9. Suzuki-Kasami broadcast token algorithm

Exactly one token exists. Each process $P_i$ stores request vector `RN[1..N]`; the token stores:

- `LN[j]`: sequence number of $P_j$'s last completed request;
- queue `Q` of pending requesters.

### Request

If $P_i$ does not hold the token:

1. increment `RN[i]`;
2. broadcast `REQUEST(i,RN[i])` to all $N-1$ processes.

Receiver updates `RN[i] := max(RN[i], sequence)`.

### Token handling

The token holder knows $P_j$ has an outstanding request when

$$RN[j]=LN[j]+1.$$

If not in CS and such a request exists, it sends the token to an eligible queued requester.

### Release

After $P_i$ exits:

1. set `LN[i] := RN[i]` in the token;
2. append each process $P_j$ with `RN[j]=LN[j]+1` that is not already in `Q`;
3. if `Q` is nonempty, remove its head and send the token.

### Cost

- If requester lacks token: $N-1$ request messages plus one token transfer, at most $N$.
- If it already holds token: zero messages.
- Synchronization delay under load: one token-transfer time.

Safety follows from uniqueness of the token. Token loss or duplication requires explicit recovery that preserves exactly-one-token safety.

## 10. Raymond tree-based token algorithm

Processes form a logical tree. Every process stores:

- `holder`: neighbor on the path toward the token, or self if holding it;
- FIFO request queue;
- `asked`: whether a request has already been forwarded toward `holder`.

### Request flow

1. A requester enqueues itself.
2. If it lacks token and has not asked, it sends one `REQUEST` to `holder` and sets `asked`.
3. Intermediate nodes enqueue the requester direction and forward at most one outstanding request toward their holder.
4. Token follows queued directions, reversing `holder` pointers along the path.
5. A node holding token enters if it is at the head; otherwise it sends token toward the queued neighbor.

Typical light-load cost is proportional to the tree distance to the token, often $O(\log N)$ in a balanced tree. Under saturated demand the published algorithm approaches a small constant number of messages per CS. Worst-case movement depends on tree diameter.

## 11. Response time under low and high load

- Permission algorithms: low load pays a full request/reply round; high load additionally waits behind earlier requests.
- Suzuki-Kasami: zero messages if token local; otherwise broadcast plus token. Under high load the token passes directly between queued requesters.
- Raymond: low load follows path to token and back; under high load requests form a distributed queue and token moves directly among queued nodes.

Always specify whether response time counts message hops, one-way delay $T$, queueing, or CS execution time.

## 12. Comparison

| Algorithm | Type | Messages/CS | Main weakness |
|---|---|---:|---|
| Central coordinator | Coordinator | 3 | Bottleneck/single failure point |
| Lamport | Permission | $3(N-1)$ | Every process involved |
| Ricart-Agrawala | Permission | $2(N-1)$ | One failed member can block |
| Roucairol-Carvalho | Permission optimization | 0 to $2(N-1)$ request/reply traffic | Retained-permission bookkeeping |
| Maekawa | Quorum permission | about $3\sqrt N$ | Basic version can deadlock |
| Agrawal-El Abbadi | Tree quorum | $O(\log N)$ best; grows with failures | More complex quorum generation |
| Suzuki-Kasami | Broadcast token | 0 or at most $N$ | Token recovery |
| Raymond | Tree token | Distance-dependent; typically $O(\log N)$ light load | Tree/token recovery |

## 13. Common mistakes

- Omitting release messages from Lamport's message count.
- Saying Ricart-Agrawala uses a release broadcast.
- Claiming Maekawa is automatically deadlock-free.
- Claiming timestamp order in Maekawa is identical to Ricart-Agrawala.
- Forgetting the token queue or `LN` vector in Suzuki-Kasami.
- Assuming a timeout safely recreates a lost token; that may duplicate it.
- Giving message complexity without saying whether the token is already local.

