# Distributed transactions and concurrency control

## 1. Distributed transaction

A distributed transaction accesses data at more than one site but must appear as one transaction.

ACID:

- **Atomicity:** all sites commit or all abort.
- **Consistency:** application/database invariants are preserved.
- **Isolation:** concurrent execution is equivalent to an allowed serial order.
- **Durability:** committed effects survive failures.

Atomic commit and concurrency control solve different problems. Two-phase commit (2PC) decides commit/abort across sites; two-phase locking (2PL) controls conflicting concurrent operations.

## 2. Architecture

Common components:

- Transaction manager/coordinator at the origin
- Local transaction manager at each participating site
- Scheduler/concurrency-control manager
- Recovery/log manager using stable storage
- Communication manager
- Local data/resource managers

The coordinator decomposes a global transaction into subtransactions, sends them to the data-owning sites, and later runs atomic commit.

## 3. Global and local execution plan

Example: print car and home information for every person with salary above 100000.

- `Person(SSN, Name, Profession, Salary)` at site 1
- `Car(Name, Make, Year, SSN)` at site 2
- `Home(Address, Rooms, SSN)` at site 3
- Query submitted at site 0

Efficient plan:

1. Site 0 sends selection/projection to site 1:
   `SELECT SSN,Name FROM Person WHERE Salary > 100000`.
2. Site 1 returns only qualifying `(SSN,Name)` values, not the whole table.
3. Send qualifying SSNs to sites 2 and 3, preferably batched.
4. Site 2 locally selects/projections matching car rows.
5. Site 3 locally selects/projections matching home rows.
6. Ship reduced results to site 0, or ship the smaller relation to one join site.
7. Join on `SSN` and format result.

The global plan chooses sites, operation placement, join order, and data movement. Each local plan chooses access path, scan/index, join implementation, buffering, and local locking.

## 4. Atomic commit and logging

Each participant must force enough information to stable log before sending a vote/decision that another site will rely on. Typical records:

- `START T`
- update before/after information as required by recovery design
- `READY/PREPARED T`
- `COMMIT T` or `ABORT T`
- `END T`

The write-ahead logging rule forces relevant log records before dirty data pages and forces the commit decision before reporting success.

## 5. Two-phase commit

### Phase 1 - prepare/vote

1. Coordinator writes `START-2PC` and sends `PREPARE(T)` to all participants.
2. Each participant checks whether it can commit.
3. If yes, it force-writes `READY(T)` and replies `YES`; it must retain locks/resources and cannot unilaterally abort.
4. If no, it writes `ABORT(T)`, undoes as necessary, and replies `NO`.

### Phase 2 - decision

- If every vote is YES, coordinator force-writes `COMMIT(T)` and sends `COMMIT` to all.
- Otherwise, coordinator writes `ABORT(T)` and sends `ABORT`.
- Participants force-write the received decision, commit/undo, release locks, and acknowledge.
- Coordinator writes `END(T)` after acknowledgements according to the implementation.

Atomicity comes from the rule that a YES voter enters a prepared state and obeys the single durable coordinator decision.

## 6. Failure cases

### Participant fails before voting YES

Coordinator times out and aborts. On recovery, participant follows its local abort/no-prepared state.

### Participant fails after writing READY

On recovery it is **in doubt**. It cannot safely choose because another site may have committed. It asks coordinator/participants for the decision and may block until one is available.

### Coordinator fails before writing COMMIT, after all YES votes

On recovery, absence of a durable commit record means the coordinator must choose/log ABORT and notify participants. It must not invent a previous commit. Prepared participants may block until this recovery/termination decision becomes available.

### Coordinator fails after durable COMMIT but before all ACKs

On recovery it finds `COMMIT`, re-sends `COMMIT` to every participant lacking confirmed completion, and continues until the decision is learned/acknowledged. Duplicate decision messages must be idempotent.

### Network partition

Prepared participants separated from the decision can block. Basic 2PC cannot guarantee both availability and atomic commit during an indefinite partition.

### What basic 2PC does not handle

- Byzantine participants/coordinator
- Permanent loss of all copies of the decision log
- Non-blocking progress through arbitrary partition/coordinator loss

Three-phase commit adds assumptions and another phase to avoid some blocking cases, while consensus-replicated commit protocols tolerate broader crash patterns at additional cost.

## 7. Why 2PC is blocking

Once a participant has voted YES and recorded READY, both global outcomes remain possible from its local view. Unilateral commit could conflict with a global abort; unilateral abort could conflict with a durable global commit. It must learn the decision or use a correct termination protocol.

## 8. Schedules and conflicts

Operations conflict when they are from different transactions, access the same item, and at least one is a write:

- `R1(X)` and `W2(X)` conflict.
- `W1(X)` and `R2(X)` conflict.
- `W1(X)` and `W2(X)` conflict.
- Two reads do not conflict.

A schedule is conflict-serializable iff its precedence/serialization graph is acyclic. Add edge $T_i\to T_j$ when a conflicting operation of $T_i$ occurs before the one of $T_j$. A topological order is the equivalent serial order.

## 9. Two-phase locking

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

Locks are held by data-owning sites or a distributed lock manager. A transaction may wait across sites, creating distributed deadlock; detection/timeout/prevention is needed. Strict 2PL plus 2PC is common: locks remain held while the transaction is prepared, increasing blocking but protecting atomic visibility.

## 10. Timestamp-ordering concurrency control

Assign each transaction timestamp $TS(T)$. Each item $X$ maintains:

- $readTS(X)$: largest timestamp of a successful reader
- $writeTS(X)$: largest timestamp of a successful writer

Basic rules:

- `read_T(X)` is rejected/aborted if $TS(T)<writeTS(X)$; otherwise read and update $readTS$.
- `write_T(X)` is rejected/aborted if $TS(T)<readTS(X)$ or, in the basic rule, if $TS(T)<writeTS(X)$; otherwise write and update $writeTS$.

Thomas's write rule may ignore an obsolete write when $TS(T)<writeTS(X)$ instead of aborting, because a newer value is already installed.

Advantages: no lock waiting and therefore no lock deadlock. Costs: aborts/restarts, timestamp coordination, possible starvation, and distributed metadata/version management.

## 11. Optimistic concurrency control

Assume conflicts are rare. A transaction has:

1. **Read/execution phase:** read database, buffer writes privately.
2. **Validation phase:** test whether committing would violate serializability with overlapping transactions.
3. **Write phase:** if valid, install writes atomically; otherwise abort/restart.

Validation commonly compares read/write sets and transaction phase times. It works well for low contention and read-heavy workloads; under high contention it wastes work through repeated aborts.

Distributed OCC needs coordinated global validation and atomic commit. Passing local validation at every site independently is insufficient if the combined global dependency graph can cycle.

## 12. Concurrency-control comparison

| Method | Waits? | Deadlock? | Main cost |
|---|---|---|---|
| 2PL | Yes | Possible | Lock messages, blocking, deadlock handling |
| Timestamp ordering | Usually abort rather than wait | No lock deadlock | Restarts and timestamp/version metadata |
| Optimistic | No lock wait during read phase | No lock deadlock | Validation and wasted work on abort |
| Serialization graph testing | Depends | Can detect cycles directly | Maintaining distributed dependency graph |

## 13. Agreement vs atomic commit

Consensus chooses one value despite failures under a stated fault/timing model. Atomic commit has validity tied to transaction votes: a NO must force abort, and commit requires the required YES votes. Basic 2PC relies on a single coordinator log and can block; a consensus-backed replicated coordinator can make the decision service more available, but the transaction vote constraints still apply.

### Consensus answer blueprint

Consensus requires:

- **Agreement:** no two correct processes decide different values.
- **Validity:** the decided value satisfies the proposal validity rule.
- **Termination:** every correct process eventually decides under the model's progress assumptions.

Crash-tolerant practical protocols divide time into numbered ballots/terms, use a leader/proposer, and require votes from an intersecting majority. A later leader must first learn the highest previously accepted value from its quorum; because majorities intersect, it cannot safely choose a conflicting value. Log-based protocols apply this rule repeatedly to ordered commands. Pure asynchronous deterministic consensus cannot guarantee termination with even one crash, so practical protocols assume timeouts/eventual synchrony for leader progress while preserving safety during arbitrary delay.

## 14. Common mistakes

- Saying 2PC means two-phase locking.
- Letting a READY participant abort on its own after timeout.
- Forgetting to force the coordinator's commit log before sending COMMIT.
- Claiming 2PC is non-blocking.
- Calling `COMPOUND` file operations a distributed transaction.
- Proving serializability without constructing conflict edges or a lock-point argument.
- Saying 2PL is necessary for all serializable schedules.
- Saying timestamp ordering cannot abort.
- Validating distributed OCC independently at each site without global coordination.
