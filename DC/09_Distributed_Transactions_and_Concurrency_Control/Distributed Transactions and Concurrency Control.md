# 9. Distributed transactions and concurrency control

Suppose a transfer debits an account at site A and credits an account at site B. The sites must decide together whether the transfer takes effect. Other transactions must also see a valid sequence of balance changes. A **distributed transaction** is one logical transaction whose operations execute at multiple sites.

| ACID property | What it means for the transfer |
|---|---|
| Atomicity | Both debit and credit take effect, or both are undone. |
| Consistency | A valid initial state moves to a valid state, preserving rules such as the total balance. |
| Isolation | Concurrent operations observe effects allowed by the isolation level. Serializable isolation is equivalent to some serial order. |
| Durability | A committed transfer survives failures covered by the recovery model. |

The transaction manager at the originating site coordinates the logical transaction and sends operations to participating sites. A distributed query planner chooses where relational operators run. The commit coordinator collects votes and distributes the outcome. The **local transaction managers** execute operations at the participating sites. Each site's **scheduler** handles concurrent accesses and its **recovery manager** maintains a durable log. A communication layer sends operations, votes, and decisions. **Two-phase commit** (2PC) coordinates the outcome. **Two-phase locking** (2PL) constrains concurrent access.

## Global and local execution plan

Suppose a query at site 0 asks for car and home information for every person with salary above 100000.

- `Person(SSN, Name, Profession, Salary)` at site 1
- `Car(Name, Make, Year, SSN)` at site 2
- `Home(Address, Rooms, SSN)` at site 3
- Query submitted at site 0

One plan pushes the salary test and projections to the sites holding the data.

1. Site 0 sends selection/projection to site 1:
   `SELECT SSN,Name FROM Person WHERE Salary > 100000`.
2. Site 1 returns qualifying (SSN,Name) values.
3. Send qualifying SSNs to sites 2 and 3, preferably batched.
4. Site 2 filters car rows by the qualifying SSNs and projects the requested fields.
5. Site 3 does the same for home rows.
6. Ship reduced results to site 0, or ship the smaller relation to one join site.
7. Join on SSN and format the result. If qualifying people must appear even when they have no car or home row, use left joins from the qualifying Person rows.

The global plan chooses sites, operation placement, join order, and data movement. Each local plan chooses scans or indexes, join implementation, buffering, and local locking. Shipping only qualifying join keys to filter remote relations is the semijoin idea.

~~~text
Site 0 -> Site 1   Select salary > 100000, project SSN and name
Site 1 -> Site 0   Qualifying SSN, name
Site 0 -> Site 2   Qualifying SSNs, requested car fields
Site 0 -> Site 3   Qualifying SSNs, requested home fields
Sites 2/3 -> Site 0   Matching rows with SSN for the join
Site 0 -> caller  Joined result
~~~

This transaction runs a read query. The plan shows query traffic. If a transaction also changes data, PREPARE, vote, and decision messages form a separate commit protocol.

## Atomic commit and logging

Each participant must force enough information to stable log before sending a vote/decision that another site will rely on. Typical records:

- `START T`
- update before/after information as required by recovery design
- `READY/PREPARED T`
- `COMMIT T` or `ABORT T`
- `END T`

The write-ahead logging rule forces relevant log records before dirty data pages and forces the commit decision before reporting success.

## Two-phase commit

### Phase 1 - prepare/vote

1. Coordinator writes `START-2PC` and sends `PREPARE(T)` to all participants.
2. Each participant checks whether it can commit.
3. If yes, it force-writes `READY(T)` and replies `YES`; it must retain locks/resources and cannot unilaterally abort.
4. If no, it writes `ABORT(T)`, undoes as necessary, and replies `NO`.

### Phase 2 - decision

- If every vote is YES, coordinator force-writes `COMMIT(T)` and sends `COMMIT` to all.
- If a participant votes NO or a vote is missing when the coordinator decides to abort, the coordinator durably records ABORT(T) before sending ABORT in this basic logging protocol.
- Participants force-write the received decision, commit/undo, release locks, and acknowledge.
- Coordinator writes `END(T)` after acknowledgements according to the implementation.

Atomicity comes from the rule that a YES voter enters a prepared state and obeys the single durable coordinator decision. Votes, decisions, and acknowledgements carry the transaction identifier so retries can be handled without applying an update twice.

### Recovery and blocking

| Failure point | Durable information | Recovery action |
|---|---|---|
| Participant fails without preparing | Participant has no `READY` record. | The coordinator can abort if it did not receive YES. The unprepared participant can undo local work. |
| Participant fails after preparing | Participant has `READY`; it may have voted YES. | Recover its prepared state and learn the decision. It cannot unilaterally abort. |
| Coordinator fails after all YES votes but before logging COMMIT | No durable commit decision exists in the basic protocol. | On recovery it can record and communicate ABORT. Prepared sites may have to wait until the coordinator recovers. |
| Coordinator fails after logging COMMIT | The durable decision is COMMIT. | Resend COMMIT until participants acknowledge. Reapplying a decision message must be safe. |
| A partition separates prepared sites from the decision | READY gives no evidence of the final decision. | Sites can remain blocked even while holding locks. |

A prepared site cannot use a timeout as its decision. It may be in an execution where the coordinator has already committed, or one where another participant voted NO. A reachable participant that knows the decision can relay it. If everyone reachable knows only that they are prepared, the uncertainty remains. A crash that preserves the stable logs can be handled by recovery and retransmission. A permanent partition or permanent loss of the only durable decision record can prevent basic 2PC from completing safely. The protocol also assumes participants follow it. Consensus-backed commit protocols can replicate decision state under additional fault assumptions. [Gray and Lamport on transaction commit](https://arxiv.org/abs/cs/0408036)

## Schedules and conflicts

Operations conflict when they are from different transactions, access the same item, and at least one is a write:

- `R1(X)` and `W2(X)` conflict.
- `W1(X)` and `R2(X)` conflict.
- `W1(X)` and `W2(X)` conflict.
- Two reads do not conflict.

An execution schedule interleaves transaction operations while preserving each transaction’s program order. A serial schedule completes one transaction before beginning the next. A schedule is conflict-serializable exactly when its precedence graph is acyclic. Add edge $T_i\to T_j$ when a conflicting operation of $T_i$ occurs before the one of $T_j$. A topological sort gives an equivalent serial order.

**Worked schedule.** In `W1(X), R2(X), W2(Y), R1(Y)`, the conflict on $X$ gives $T_1\to T_2$. The conflict on $Y$ gives $T_2\to T_1$. The cycle rules out conflict serializability. If $X$ and $Y$ are at different sites, each local graph contains only one edge. The global graph still has the cycle. A precedence edge records the order of two conflicting operations. A wait-for edge records a process that is currently blocked, so the two graphs answer different questions.

## Two-phase locking

Every transaction has:

- **Growing phase:** acquire/upgrade locks; release none.
- **Shrinking phase:** release/downgrade locks; acquire no new lock.

Strict 2PL holds all exclusive locks until commit/abort. Rigorous 2PL holds all locks until commit/abort. These stricter forms simplify recovery and avoid cascading aborts.

### Why 2PL is sufficient for conflict serializability

Define a transaction's lock point as the instant it acquires its final lock. If $T_i$ has a conflicting operation before $T_j$, the required lock held by $T_i$ must be released before $T_j$ acquires its conflicting lock. Under 2PL, $T_i$'s lock point is before that release, while $T_j$'s lock point is after its acquisition. Thus every precedence edge follows lock-point order. A cycle would require a lock point to precede itself, impossible.

### Why 2PL is not necessary

The serial schedule

```text
T1: lock X; W1(X); unlock X; lock Y; W1(Y); unlock Y
T2: lock X; W2(X); unlock X
```

is serial/serializable because all $T_1$ operations precede $T_2$, but $T_1$ acquires `Y` after releasing `X`, violating 2PL. Therefore 2PL is sufficient, not necessary.

### Distributed 2PL

Locks are held by data-owning sites or a distributed lock manager. A transaction may wait across sites, creating distributed deadlock; detection/timeout/prevention is needed. Under distributed 2PL, acquisitions at all sites belong to one growing phase. With strict 2PL and 2PC, each prepared participant retains its exclusive locks until it learns and applies the final decision. This prevents other transactions using the same locking protocol from reading uncommitted writes. It also extends blocking when the decision is delayed.

## Timestamp-ordering concurrency control

Assign each transaction timestamp $TS(T)$. Each item $X$ maintains:

- $readTS(X)$: largest timestamp of a successful reader
- $writeTS(X)$: largest timestamp of a successful writer

Basic rules:

- `read_T(X)` is rejected/aborted if $TS(T)<writeTS(X)$; otherwise read and set $readTS(X)\gets\max(readTS(X),TS(T))$.
- `write_T(X)` is rejected/aborted if $TS(T)<readTS(X)$ or, in the basic rule, if $TS(T)<writeTS(X)$; otherwise write and update $writeTS$.

Thomas's write rule still aborts the write if $TS(T)<readTS(X)$. If that check passes but $TS(T)<writeTS(X)$, it can skip this obsolete write because a newer value is already installed.

**Example.** A transaction with $TS=20$ has read $X$, so $readTS(X)=20$. A transaction with $TS=10$ then attempts to write $X$. Accepting it would require the earlier transaction's write to precede the read in timestamp order. Basic timestamp ordering rejects the write, and the older transaction must restart. The protocol trades lock waits for aborts, retries, and timestamp metadata.

## Optimistic concurrency control

Assume conflicts are rare. A transaction has:

1. **Read/execution phase:** read database, buffer writes privately.
2. **Validation phase:** test whether committing would violate serializability with overlapping transactions.
3. **Write phase:** if valid, install writes atomically; otherwise abort/restart.

Validation commonly compares read/write sets and transaction phase times. It works well for low contention and read-heavy workloads; under high contention it wastes work through repeated aborts.

For example, if $T_1$ reads $X$ and $T_2$ commits a change to $X$ before $T_1$ validates, a validation policy that orders $T_1$ after $T_2$ must reject $T_1$'s stale read. Distributed OCC needs coordinated global validation and atomic commit. Passing local validation at every site independently is insufficient if the combined global dependency graph can cycle. [Kung and Robinson, original OCC paper](https://www.eecs.harvard.edu/~htk/publication/1981-tods-kung-robinson.pdf)

## Concurrency-control comparison

| Method | Waits? | Deadlock? | Main cost |
|---|---|---|---|
| 2PL | Yes | Possible | Lock messages, blocking, deadlock handling |
| Timestamp ordering | Usually abort rather than wait | No lock deadlock | Restarts and timestamp/version metadata |
| Optimistic | Read phase avoids lock waits | Depends on validation and commit implementation | Validation and wasted work on abort |
| Serialization graph testing | Depends | Can detect cycles directly | Maintaining distributed dependency graph |

## Agreement vs atomic commit

Consensus chooses one value despite failures under a stated fault/timing model. Atomic commit has validity tied to transaction votes: a NO must force abort, and commit requires the required YES votes. Basic 2PC relies on a single coordinator log and can block; a consensus-backed replicated coordinator can make the decision service more available, but the transaction vote constraints still apply.

### Consensus properties

Consensus requires:

- **Agreement:** no two correct processes decide different values.
- **Validity:** the decided value satisfies the proposal validity rule.
- **Termination:** every correct process eventually decides under the model's progress assumptions.

Crash-tolerant practical protocols divide time into numbered ballots/terms, use a leader/proposer, and require votes from an intersecting majority. In Paxos, the proposer first gathers promises from a quorum of acceptors. It must use the value attached to the highest-numbered accepted proposal reported by that quorum, if any. It then asks acceptors to accept the new proposal. A quorum of acceptances chooses the value. Intersecting quorums and the rule for carrying forward an accepted value preserve agreement. Multi-Paxos repeats agreement across log positions. [Lamport, Paxos Made Simple](https://systems.cs.columbia.edu/ds2-class/papers/lamport-paxos.pdf) Pure asynchronous deterministic consensus cannot guarantee termination with even one crash, so practical protocols assume timeouts/eventual synchrony for leader progress while preserving safety during arbitrary delay.

## Course material

This chapter follows the transaction and concurrency-control blocks on [syllabus slide 41](../01_Fundamental_Concepts/sources/01_Introduction-DC.pdf#page=41). See the [past-question index](../00_Course_Guide/PYQ_TOPIC_INDEX.md) for the distributed query plan, commit recovery, and serializability questions.
