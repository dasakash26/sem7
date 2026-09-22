# Termination detection

## 1. Problem

A distributed computation has terminated when:

1. every process is passive, and
2. no computation message is in transit.

The second condition matters. A passive receiver can become active when an in-transit message arrives. Therefore, "all processes look idle" is not sufficient.

## 2. Model and terms

- **Active process:** may perform computation and send basic/application messages.
- **Passive process:** waits for a basic message; it sends no new basic message unless activated.
- **Control message:** used only by the detection algorithm.
- **Diffusing computation:** one initiator activates work that spreads through messages and may create more work.

Termination is a stable property: once all processes are passive and no basic message is in transit, normal computation cannot restart by itself.

## 3. Why local observation fails

Suppose $P_1$ sends work to $P_2$ and immediately becomes passive. A monitor polls $P_2$ before the message arrives and sees both passive. It reports termination, then the delayed message activates $P_2$. The false result comes from ignoring channel state.

## 4. Dijkstra-Scholten signalling

The algorithm superimposes acknowledgements on a diffusing computation.

### State

Each process tracks:

- whether it is active or passive;
- a parent/predecessor edge chosen when it is activated from passive state;
- the number of basic messages it sent that have not yet been acknowledged;
- obligations to acknowledge received basic messages.

The initiator is the root and has no parent.

### Simplified operational view

1. When an active process sends a basic message, increment its outstanding count.
2. A passive process activated by a basic message adopts that sender as its parent for the current diffusing tree.
3. Extra activation messages that do not become the parent obligation are eventually acknowledged separately.
4. A non-root process may acknowledge its parent only when it is passive and has no unacknowledged work below it.
5. Receiving an acknowledgement decrements the sender's outstanding count.
6. The root declares termination exactly when it is passive and its outstanding count is zero.

The actual computation graph may contain cycles; the acknowledgement obligations form a dynamic dependency tree directed toward the initiator.

### Invariant

Every basic message is represented by an outstanding acknowledgement obligation until the work it caused, including descendant work, has drained. Therefore, if the root has no outstanding obligation and is passive, neither a basic message nor active descendant work exists.

### Safety

The root cannot declare while a basic message is in transit or a descendant remains active, because that work leaves an acknowledgement chain outstanding toward the root.

### Liveness

If the basic computation performs finitely many sends, all messages/acknowledgements are eventually delivered, and every process eventually becomes passive, obligations return from leaves to the root and the root eventually declares termination.

### Cost

Each basic message creates a bounded signalling/acknowledgement cost, so control-message complexity is linear in the number of basic messages. Exact batching and signal counts depend on the taught formulation.

## 5. Snapshot-based termination detection

Run a consistent distributed snapshot and evaluate:

$$Terminated \iff (\forall i: P_i\text{ is passive})\land(\forall C_{ij}:SC_{ij}=\varnothing).$$

Because termination is stable, if a consistent snapshot satisfies the predicate, termination is safely detected.

This approach is conceptually simple but a full snapshot may be more expensive than a specialized signalling algorithm.

## 6. Token/ring idea

In a ring, a control token may accumulate activity information or sent-minus-received counts. A complete circulation can prove termination only if the algorithm also prevents activity created "behind" the token from being missed. Colouring schemes commonly mark a process/token when backward work is generated; the initiator declares only after receiving an appropriately clean token while passive.

Do not answer merely "circulate a token and check everyone is idle." Explain how in-transit work and reactivation are captured.

### Counting application tokens in a ring

A correct count at a consistent cut is:

$$\sum_i \text{tokens held in }P_i + \sum_c \text{tokens recorded in channel }c.$$

Counting only process-local holdings misses tokens in transit; counting independent local reports can double-count a token that moves between reports. Use a snapshot marker/colour boundary, unique token identifiers, and duplicate suppression. In a fully asynchronous faulty ring, a process cannot infer from silence whether a missing token is delayed, lost, or behind a failed process; exact termination/counting needs the stated reliable-channel/failure assumptions.

## 7. Termination vs deadlock

| Termination | Deadlock |
|---|---|
| All computation is complete | Processes are permanently waiting |
| Desired final state | Undesired blocked state |
| No work message in transit | Requests/dependencies may remain |
| No process can reactivate | Processes want progress but cannot make it |

Passive processes are not necessarily deadlocked; they may be correctly waiting for new external work.

## 8. Failure considerations

Pure termination-detection algorithms usually assume reliable processes and channels. A lost process or acknowledgement is indistinguishable from an arbitrarily slow one in a pure asynchronous system. Crash-tolerant termination therefore needs stronger assumptions such as failure detectors, replicated state, epochs, or recovery logs.

## 9. Worked example

1. Root $P_1$ sends work to $P_2$ and increments outstanding to 1.
2. $P_2$ becomes active with parent $P_1$, sends work to $P_3$, and has one child obligation.
3. $P_1$ becomes passive but cannot terminate: outstanding is 1.
4. $P_3$ finishes, becomes passive, and acknowledges $P_2$.
5. $P_2$ now has no child obligation, finishes, and acknowledges $P_1$.
6. $P_1$ is passive with outstanding 0, so it declares termination.

## 10. Common mistakes

- Equating passive with terminated.
- Ignoring messages in transit.
- Allowing a child to acknowledge its parent while descendant work remains.
- Claiming timeout proves termination in an asynchronous system.
- Confusing termination detection with deadlock detection.
