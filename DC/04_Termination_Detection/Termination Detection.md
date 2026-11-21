# 4. Termination detection

A distributed computation has terminated when **every process is passive and every application channel is empty**. Suppose $P_1$ sends work to $P_2$ and becomes idle. Until the message arrives, both processes may look idle. The in-transit message can still activate $P_2$.

## Model and detection condition

| Term | Meaning |
|---|---|
| Active process | Can compute and send application messages. |
| Passive process | Can resume application work only when a message activates it. |
| Application message | May create more application work. |
| Control message | Used by the detector to establish whether application work remains. |

Under this closed-computation model, termination is a **stable property**. Once the condition holds, the computation cannot restart by itself. The detector is **safe** if it never announces termination too early and **live** if it eventually announces a computation that has terminated, given its delivery and failure assumptions.

## Detecting termination with a snapshot

Take a consistent snapshot. Check that every stored local state is passive and every stored channel state is empty.

$$
\mathrm{Terminated} \iff
(\forall i: P_i\text{ is passive})\land
(\forall C_{ij}: SC_{ij}=\varnothing).
$$

In the opening example, a consistent snapshot that includes the send and excludes the receive records the work in the channel. A later cut may show the receiver still active. Keep taking snapshots if the first one shows outstanding work. Even a report collected after termination can describe an earlier cut with work in it. A snapshot initiated after termination will report all processes passive and all application channels empty, provided the snapshot completes. See the [snapshot chapter](../03_Global_State_and_Snapshots/Global%20State%20and%20Snapshots.md#applications-of-snapshots) for how the cut is recorded.

## Dijkstra–Scholten signalling

Consider a **diffusing computation** started by one root, with all other processes initially passive. Work may spread through arbitrary message paths. The detector tracks unanswered work back to the root. This presentation uses the parent-and-counter form and assumes reliable delivery and no crashes during detection.

1. Each application send increments the sender's outstanding counter and creates one acknowledgement obligation.
2. A non-root process with no current parent adopts the sender of the arriving work message as its parent and retains that acknowledgement. If it already has a parent, it acknowledges the extra incoming message immediately. Its existing parent obligation still accounts for its work. The root also acknowledges incoming messages immediately because it never adopts a parent.
3. Each acknowledgement decrements the original sender's counter. When a non-root process becomes passive or receives an acknowledgement, it checks whether it is passive with counter zero. If so, it returns its retained parent acknowledgement, if any, and clears its parent. A passive process with outstanding work keeps its parent.
4. The root declares termination once it is passive and its own counter reaches zero.

**Why it works.** While a process remains active or awaits outstanding work, it stays connected to the root by retained acknowledgements. A message still in transit also contributes to some sender's outstanding count. A passive root with count zero therefore has no work outstanding anywhere. After a finite computation drains, acknowledgements can travel back from the ends of these chains, so the root eventually detects termination. With one acknowledgement per application message, signalling uses $O(M)$ messages for $M$ application sends.

### Worked example

| Event | Detector state |
|---|---|
| $P_1$ sends work to $P_2$. | Root $P_1$ has 1 outstanding send. $P_2$ attaches to $P_1$ on receipt. |
| $P_2$ sends work to $P_3$. | $P_2$ has 1 outstanding send. $P_3$ attaches to $P_2$ on receipt. |
| $P_1$ becomes passive. | Its count is still 1. It must wait. |
| $P_3$ finishes and acknowledges $P_2$. | $P_2$'s outgoing count reaches 0. |
| $P_2$ becomes passive and acknowledges $P_1$. | $P_1$ is passive with count 0, so it declares termination. |

## Ring-based detection and token counting

A detector token can circulate on a logical ring while application messages travel independently. A pass recording every process as passive is insufficient. Suppose the token visits $P_1$ while it is passive and moves on. An unvisited $P_2$ can send new work to $P_1$ and become passive before its own visit. The token then sees two passive observations even though the message is still in transit or $P_1$ is active again.

### Safra's ring detector

The original receive-colouring variant uses reliable delivery and no crashes. With initially empty application channels, each process starts with a balance of zero and increments it on send or decrements it on delivery. The sum across all processes at a single instant is the number of messages in transit. A token tour samples that sum across different instants, so it checks activity and colour as well.

1. On application delivery, mark the recipient black. To start a probe, the initiator marks itself and the token white and sets the token count to zero.
2. A non-initiator holds the token while active. When passive, it adds its current local balance to the token count, marks the token black if the process is black, resets its own colour to white, and passes the token.
3. On return, the passive initiator declares termination only if both it and the token are white and their counts sum to zero. Otherwise it starts another probe. The process balances are cumulative in this version.

Once all application work drains, balances stop changing and no process turns black again. A complete probe can clear remaining black marks. The next probe then returns white with a zero total balance. [Safra's original rules and invariant](https://www.cs.utexas.edu/~EWD/ewd09xx/EWD998.PDF)

To count **application tokens**, add tokens held by processes and tokens in recorded channel states at one consistent cut. This is a separate problem from using a detector token. See the [worked FIFO ring count](../03_Global_State_and_Snapshots/Global%20State%20and%20Snapshots.md#token-counting-in-a-ring).

| Observation | Interpretation |
|---|---|
| All processes passive, channel has work | The computation may resume on delivery. |
| All processes passive, every channel empty | Termination in the closed-computation model. |
| Processes permanently waiting on each other | Deadlock, which requires a dependency test. |
| No reply arrives from a participant | A timeout alone cannot distinguish a failure from delay in the asynchronous model. |

## Course material

The syllabus and past questions connect termination detection to snapshots. For acknowledgement-based detection, see [Dijkstra and Scholten's original treatment](https://www.cs.utexas.edu/~EWD/transcriptions/EWD06xx/EWD687a.html).
