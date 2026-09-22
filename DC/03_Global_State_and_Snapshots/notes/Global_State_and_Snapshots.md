# Global state and snapshots

## 1. Global state

For processes $P_1,\ldots,P_n$ and directed channels $C_{ij}$:

$$GS=\{LS_1,\ldots,LS_n,SC_{ij}\text{ for every channel}\},$$

where $LS_i$ is a local process state and $SC_{ij}$ is the set/sequence of messages sent on $C_{ij}$ but not yet received at the chosen cut.

Recording only process states is insufficient. If a debit message has been sent but not received, omitting channel state can lose money from the recorded global state.

## 2. Cuts and consistency

A cut chooses a prefix of each process history. Its frontier contains the latest selected event on each process.

A cut $C$ is consistent when it is closed under happened-before:

$$e\in C\land f\rightarrow e \implies f\in C.$$

Equivalent exam statement: a consistent cut never includes a receive event without its corresponding send. Including a send without its receive is allowed; the message is then in transit and belongs to channel state.

## 3. Why snapshots are useful

- Stable-property detection: termination, deadlock, token loss
- Checkpointing and recovery
- Debugging and monitoring
- Distributed garbage collection
- Detecting invariant violations

A **stable property** stays true once it becomes true, for example termination. Snapshot algorithms are especially useful for stable properties because a later consistent cut cannot make the property revert merely due to the observation mechanism.

## 4. Chandy-Lamport snapshot

### Assumptions

- Directed reliable FIFO channels
- No process/channel failure during the snapshot, unless an extension handles it
- The communication graph is strongly connected or markers can reach every relevant process
- Processes continue their normal computation while recording

### Initiator rule

1. Record its local state.
2. Start recording messages arriving on every incoming channel.
3. Send a `MARKER(snapshot-id)` on every outgoing channel before sending any later application message on that channel.

### Marker receiving rule

When process $P$ receives a marker on incoming channel $c$:

- **First marker for this snapshot:**
  1. Record $P$'s local state before processing later application events.
  2. Record $c$ as empty.
  3. Start recording messages on every other incoming channel.
  4. Send a marker on every outgoing channel before any later application message.
- **Later marker:** stop recording channel $c$; the messages recorded after local-state recording and before this marker form $SC_c$.

The process finishes its part after a marker has arrived on every incoming channel.

### Why the first-marker channel is empty

FIFO delivery guarantees that every application message sent before the marker on that channel arrived before the marker. Since the marker caused local-state recording, no earlier message can still be in transit across that cut on this channel.

### Why the snapshot is consistent

Assume a recorded receive exists without its send. The send occurred before the sender recorded its state, so the marker on that FIFO channel was sent after the message. The receiver would receive the application message before that marker. If the receiver state had already been recorded, the message would appear in channel state; otherwise its receive would occur before the receiver state. Either way the send is not lost. Contradiction.

## 5. Three-process ring trace

Channels: $P_1\to P_2$, $P_2\to P_3$, $P_3\to P_1$.

1. $P_1$ records $LS_1$ and sends marker to $P_2$.
2. Before receiving it, $P_2$ sends application message $m$ to $P_3$.
3. $P_2$ receives its first marker, records $LS_2$, records $C_{12}$ empty, and sends marker to $P_3$.
4. If $P_3$ receives $m$ before the marker, $m$ is included in $LS_3$ when it later records; it is not channel state.
5. $P_3$ records $LS_3$, records its first-marker channel empty, and sends marker to $P_1$.
6. $P_1$ receives the marker and completes. The cut includes both send and receive of $m$, so it is consistent.

For a more interesting channel-state example, add a second incoming channel to a process. A message arriving after that process's first marker but before the marker on the second channel is recorded as in transit on the second channel.

## 6. Distinguishing recorded messages

For a process that has recorded its local state:

- Message received before local-state recording: already reflected in local state.
- Message received after local-state recording but before that channel's marker: record in that channel state.
- Message received after that channel's marker: post-snapshot; do not record.

This three-way classification directly answers the 2024 CT2 question.

## 7. Multiple initiators and repeated snapshots

Tag every marker and record with a unique snapshot identifier such as `(initiator, sequence-number)`. A process maintains separate recording state for each active snapshot. The core rules are unchanged; identifiers prevent records from different executions being mixed.

## 8. Non-FIFO channels

The ordinary marker rule fails if an application message sent before a marker can overtake or be overtaken by it. A marker arriving first no longer proves the channel is empty at the cut.

### Colouring approach (Lai-Yang style)

1. Initially processes and messages are **white**.
2. An initiator records its state and becomes **red**.
3. A red process sends only red messages.
4. A white process receiving its first red message records its state before processing that message, then becomes red.
5. If a red process receives a white message, that message crossed the cut: it was sent before the sender recorded but arrived after the receiver recorded. Record it as channel state.

The colour attached to each application message replaces FIFO marker separation. A termination/collection mechanism is still required to know when all local and in-transit records have been gathered.

### Sequence-number alternative

Processes can piggyback sent/received counters or sequence numbers. The snapshot records how many messages the sender had sent and the receiver had received at the cut; the difference identifies in-transit messages. This requires enough logging or identifiers to reconstruct those messages.

## 9. Snapshots with causal delivery

Causal delivery ensures a process cannot deliver a message before its causal predecessors. It does not make channels FIFO for concurrent messages, so it does not by itself justify the ordinary FIFO marker rule.

A snapshot for causal channels can carry a vector-time boundary. A process records a local state when its delivered-event vector reaches the required boundary; messages whose send is inside the cut but receive is outside are channel state. The central proof obligation remains closure under happened-before.

## 10. Monitoring global state

A collector gathers all local and channel records, but collection time is not the snapshot time. The recorded pieces describe a consistent cut that may never have existed as one simultaneous physical instant.

For predicate detection:

- If a stable predicate is true in the recorded snapshot, it was true at some consistent global state and stays true afterward.
- A non-stable predicate may become true and false between snapshots; one snapshot cannot prove it never occurred.

## 11. Necessary and sufficient consistency test

Given a candidate set of local states:

1. Identify all receive events included in those prefixes.
2. Check that each corresponding send is also included.
3. Equivalently, verify closure under happened-before/vector-clock ordering.
4. Put every included send whose receive is excluded into the corresponding channel state.

## 12. Common mistakes

- Saying a snapshot freezes the system.
- Recording only local process states.
- Recording messages that arrive after the marker for their channel.
- Using FIFO Chandy-Lamport unchanged on non-FIFO links.
- Calling every cut consistent.
- Requiring one physical instant common to all recorded states.
- Forgetting a snapshot ID when initiations can overlap.

## Past-paper targets

- Define local state, global state, frontier, and consistent cut.
- State all Chandy-Lamport assumptions.
- Write both marker-receiving cases.
- Trace a three-process cycle.
- Explain how snapshot recording supports termination detection.

