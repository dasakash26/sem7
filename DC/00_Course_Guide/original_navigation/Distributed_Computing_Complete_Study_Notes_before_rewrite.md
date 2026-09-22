---
title: Distributed Computing - Complete Study and Exam Notes
aliases: [DC Complete Notes, Distributed Computing Revision]
tags: [distributed-computing, exam-prep, leader-election, clocks, message-passing, spanning-trees]
created: 2026-09-21
source-directory: /Users/somnathchattaraj/Desktop/DC
---

# Distributed Computing - Complete Study and Exam Notes

> [!important]
> These notes integrate the seven lecture PDFs by topic, then attach supplied-paper questions after their prerequisite material. A source citation gives the original slide/page. Pages that are title/diagram-only are represented in the surrounding explanation. Question-paper scans with illegible figures are explicitly flagged rather than reconstructed by guesswork.

## Contents

- [[#1. Distributed systems foundations]]
- [[#2. Message-passing model and distributed execution]]
- [[#3. Physical-clock synchronization]]
- [[#4. Logical clocks, causality, and causal delivery]]
- [[#5. Spanning trees, broadcast, convergecast, and MST]]
- [[#6. Leader election]]
- [[#7. Exam extensions: snapshots, mutual exclusion, deadlock, files, and transactions]]
- [[#Rapid Revision Sheet]]
- [[#Comprehensive Practice Question Bank]]
- [[#Question-Paper Index]]
- [[#Coverage Checklist]]

# 1. Distributed systems foundations

## Overview

A distributed system is a set of intercommunicating components on different networked computers. Components coordinate by message passing to reach a common goal; it should appear to users as one coherent system although processing and storage are spread over independent machines. **Source:** [[01_Introduction-DC]], pp. 2-3.

## Key Concepts and Definitions

> [!definition]
> **Distributed computing** studies algorithms, programming models, and techniques for solving a problem by concurrent work on multiple computers. **Distributed systems** studies the design, implementation, management, infrastructure, and middleware that make such cooperation possible. **Source:** [[01_Introduction-DC]], pp. 4-5.

| Term | Meaning / contrast |
|---|---|
| Tightly coupled system (TCS) | Multiple processors share one system-wide primary-memory address space; a shared memory can simulate IPC. Also called parallel-processing system in the slides. |
| Loosely coupled system (LCS) | Networked independent machines; communication is message passing. Also called distributed system in the slides. |
| DOS | Tightly coupled OS that tries to present a virtual uniprocessor/global resource view and automatically allocates jobs; used for multiprocessors and homogeneous multicomputers. |
| NOS | Loosely coupled OS that exposes local services to remote clients; manages heterogeneous multicomputers. Middleware improves distribution transparency. |
| Middleware | Software layer between applications/users and OSs that supports heterogeneous machines/networks while presenting a single-system view. |

**Source:** [[01_Introduction-DC]], pp. 11-15.

## Why distributed computing

- **Resource/information sharing:** A may use B's printer; B may use C's free disk; workstations can exploit idle computation elsewhere.
- **Throughput/economy:** divide a problem into subproblems executed concurrently; this can be simpler and cheaper than one very fast uniprocessor.
- **Fault tolerance/availability:** redundancy avoids the total collapse caused by one central processor's failure.
- **Scalability:** add resources incrementally to improve performance, independent of eventual scale.
- **Inherently distributed applications:** e.g., geographically distributed bank accounts, inter-bank transactions, and ATM transfers.
- Examples named: Google Cloud, AWS, banking networks, distributed process control, edge computing/Industrial IoT/smart cities, distributed ML/AI. **Source:** [[01_Introduction-DC]], pp. 6-8.

## Properties and Assumptions

Desired characteristics are fault tolerance (recover without incorrect actions), high availability, recoverability after repaired failure, consistency despite concurrency/failure, scalability, predictable responsiveness, and authentication/security. **Source:** [[01_Introduction-DC]], pp. 9-10.

An algorithm must explicitly state its model: topology (complete/ring/tree), shared-memory or message-passing communication and its reliability/order/broadcast properties, synchronous or asynchronous timing, and failure model (fail-stop, crash, omission, Byzantine). Complexity is measured as message count, bit/communication count, and rounds in synchronous systems; asynchronous time needs another stated definition. **Source:** [[01_Introduction-DC]], pp. 34-36.

> [!warning]
> Do not silently assume the network is reliable, instantaneous, infinitely wide, secure, free, static, or homogeneous. These are the “eight fallacies” (the supplied slide lists seven explicitly; the usual eighth is “there is one administrator”). **Source:** [[01_Introduction-DC]], p. 30.

## Transparency, openness, scalability

Goals are connecting users/resources, transparency, openness, and scalability. Transparency hides the multiplicity and physical distribution of computers. **Source:** [[01_Introduction-DC]], pp. 16-17.

| Transparency | What is hidden / required |
|---|---|
| Access | Differences in data representation and local vs remote access. |
| Location (name) | Name reveals no location; resources can move and names are unique system-wide. Supports user mobility. |
| Replication | Multiple copies used for availability/performance; generally implies location transparency. |
| Failure | Partial failures (link, machine, store); difficult because dead and very slow are hard to distinguish. |
| Migration | Automatic movement without name change; messages must reach a moving process without sender retransmission. |
| Concurrency | Correct ordering plus mutual exclusion, no starvation, and no deadlock. |
| Performance | Automatic reconfiguration to improve performance. |
| Scaling | Growth must not disrupt users; needs an open architecture and scalable algorithms. |

**Source:** [[01_Introduction-DC]], pp. 18-25.

Scaling is challenged by size (central server overload), geography (LAN assumptions, broadcast/reliability vs WAN delay/unreliability/point-to-point links), and administrative domains (policies, payment, management, security). Decentralized algorithms have no machine with complete state, decide locally, tolerate one machine's failure, and assume no global clock. Avoid centralized entities/algorithms and do most work at clients. **Source:** [[01_Introduction-DC]], pp. 26-29.

## Connections to Other Topics

The fundamental obstacles are arbitrary interleaving, no shared global memory/clock, and unpredictable communication delay. They lead directly to [[#4. Logical clocks, causality, and causal delivery]], snapshots, leader election, mutual exclusion, replication, recovery, and termination detection. **Source:** [[01_Introduction-DC]], pp. 31-32.

## Test Your Understanding

1. Why is a “single system image” harder to provide after a WAN partition than on one machine?

> [!hint]- Answer hint
> Contrast transparency with failure indistinguishability and communication delay.

2. Compare DOS and NOS in coupling, resource view, and heterogeneity.

> [!hint]- Answer hint
> Use the table above; middleware is the bridge in the NOS case.

## Previous-Year / Supplied Question-Paper Questions

### Question: transparencies and token counting

> **Original question:** “Mention the types of transparencies present in distributed systems.” **Source:** [[Distributed Computing sem paper]], 2025, Q1(i), 4 marks.

#### Answer

List access, location/name, replication, failure, migration, concurrency, performance, and scaling transparency. State one purpose for each as in the table above; for full marks, note that replication hides multiple copies and generally requires location transparency, while failure transparency is intrinsically difficult because slow and failed resources cannot be reliably distinguished.

> **Original question:** “A distributed system is charged with the responsibility of counting number of tokens rotating in a ring… Informally describe what each process will do, what inter-process messages will be exchanged? Is it possible to get a correct result? If not why?” **Source:** [[Distributed Computing sem paper]], 2025, Q1(i), 5 marks.

#### Answer

Give every token a unique token ID and send `TOKEN(id)` clockwise. Each process keeps a set/count of IDs seen in a defined round; the initiator circulates a `ROUND-END` control token and gathers counts by a convergecast. With reliable FIFO links, unique IDs, a known start/termination condition, and no process/link failure, the result is correct: each token is counted once. Without such assumptions, a process cannot in general know whether an absent token is delayed, lost, duplicated, or held behind a failed process; asynchronous termination detection is therefore not guaranteed merely from local silence. This follows from the absence of a global clock and unreliable communication concerns. **Related:** [[#2. Message-passing model and distributed execution]].

> [!summary]
> A distributed system gains sharing, throughput, resilience, and scale, but must replace global knowledge/time with stated assumptions, messages, and decentralized protocols.

# 2. Message-passing model and distributed execution

## Overview

In a message-passing system, processors communicate over bidirectional channels between specific processors. The channel pattern is the network topology. Formally, a system has (n) processors (p_0,ldots,p_{n-1}); (i) indexes (p_i). Each processor labels its incident channels and may not know the identity at the other end. **Source:** [[Message Passing Algorithms]], pp. 2-4.

## Detailed Explanation

A processor is a state machine with local state and channel mechanisms. A directed channel (p_i\to p_j) is represented by `outbuf` of (p_i) and `inbuf` of (p_j): the former is the physical outgoing channel, the latter an incoming-message queue. A **configuration** is the vector of accessible processor states (local variables plus incoming queues), together with channels. **Source:** [[Message Passing Algorithms]], pp. 5-7.

Two events define execution:

1. **Delivery:** moves a specific message from the sender's `outbuf` to the receiver's `inbuf`; receiver does not take a computational step.
2. **Computation:** one processor starts with old accessible state, applies its transition function (handling all incoming messages), and ends with a new accessible state: empty `inbuf`s and possible new outgoing messages.

An execution is `config, event, config, …`; it starts with all processes initial and all in-buffers empty. Each next configuration differs only by a delivery or one processor's transition. **Source:** [[Message Passing Algorithms]], pp. 8-12.

## Synchronous and asynchronous models

| Model | Admissibility and time |
|---|---|
| Asynchronous | No fixed upper bound on delivery time or time between processor steps; every message eventually delivers and every process takes infinitely many steps; arbitrary relative speed/delay, no loss/stop. |
| Synchronous | Lockstep rounds: every process sends to neighbours, all messages arrive, then each computes from just-received messages. Every message delivers and every processor takes infinitely many steps; time is rounds to termination. |

**Source:** [[Message Passing Algorithms]], pp. 13-15.

## Algorithms / Procedure: broadcast and convergecast

> [!definition]
> A rooted spanning tree is connected, acyclic, contains all processors, and has one distinguished root. Each process stores its parent/children incident channels. **Source:** [[Message Passing Algorithms]], pp. 17-18.

**Broadcast:** root sends (M) to children. A non-root, when receiving (M) from its parent, forwards (M) to all children and terminates. Each tree edge carries exactly one message, so messages are (n-1), and time is tree depth (d) (at most (n-1) for a chain) in both stated models. **Source:** [[Message Passing Algorithms]], pp. 18-21.

**Convergecast:** reverse the direction. Leaves send values to parent; every internal node waits for all child messages, aggregates them, and sends one combined value to parent. The diagram shows solid arrows as parent-child tree edges and dotted lines as non-tree edges. **Source:** [[Message Passing Algorithms]], pp. 22-25.

## Building a rooted spanning tree (known root)

Root sends `M` to every neighbour. A non-root's first `M` sender becomes its parent; it sends `parent` to that sender and `M` to all other neighbours. On later `M`, it sends `reject`; it records senders of `parent` as children and `reject` senders as non-children. It terminates when every neighbour except parent is classified. In a synchronous execution this flooding produces a BFS tree; in asynchronous execution it need not be BFS and can be DFS-like or neither. **Source:** [[Message Passing Algorithms]], pp. 27-31.

```text
root: parent := ⊥; send M to every neighbour
non-root on first M from q: parent := q; send parent to q; send M to all neighbours except q
on later M from q: send reject to q
on parent from q: add q to children
on reject from q: add q to other
terminate when children ∪ other = neighbours \ {parent}
```

## Test Your Understanding

1. In the asynchronous model, why is “eventually delivered” not a round bound?

> [!hint]- Answer hint
> Event order is arbitrary even though every message is fair.

2. Trace rooted flooding on a triangle plus a leaf; identify each `parent` and `reject`.

> [!hint]- Answer hint
> The first arriving `M` fixes a node's parent.

3. Why can asynchronous flooding fail to construct a BFS tree?

> [!hint]- Answer hint
> Parent selection is determined by arrival schedule, not shortest-hop distance.

## Previous-Year / Supplied Question-Paper Questions

### Question: synchronous vs asynchronous DS

> **Original question:** “What is the difference between synchronous and asynchronous DS?” **Source:** [[Distributed Computing sem paper]], 2025, Q1(ii), 4 marks.

#### Answer

In a synchronous system there is a known upper bound on message delivery and local processing; computation is organised into rounds, so an algorithm can use the round number and measure time in rounds. In an asynchronous system there is no known finite upper bound on either delay or relative processor speed; only eventual delivery/progress is assumed. Hence timeout-based conclusions safe in the former can violate liveness in the latter.

> [!summary]
> The execution model makes channels part of state. A spanning tree reduces all-network dissemination/aggregation to (n-1) tree messages, but timing guarantees depend on the model.

# 3. Physical-clock synchronization

## Overview

Physical clocks support real-time interpretation but different machines have skew and drift. A quartz oscillator converts mechanical vibration of piezoelectric quartz into a voltage signal; it is part of a self-feedback loop, typically oscillates at 32 kHz, and divides frequency to generate clock time. A typical quartz clock drifts about (pm15) seconds/month (6 ppm), unsuitable for large distributed systems alone. **Source:** [[03_Synchronization-of-Clocks]], pp. 4-5.

## Definitions, formulas, and variables

- **Clock skew/offset:** difference between readings of two clocks.
- **Clock drift:** clocks count time at different rates. Ordinary quartz drift is stated as about 1 second in 11-12 days ((10^{-6}) s/s); high-precision quartz (10^{-7}) or (10^{-8}) s/s. **Source:** [[03_Synchronization-of-Clocks]], p. 6.
- (C_i(t)): process (i)'s clock at real time (t). External synchronization bounds (|C_i(t)-C(t)|<D) against a known clock (C); internal synchronization bounds (|C_i(t)-C_j(t)|<D) for every pair. **Source:** [[03_Synchronization-of-Clocks]], p. 14.

Atomic clocks use Caesium-133 and are stated to have (10^{-8}) ppm accuracy. GPS satellites broadcast position ((x_i,y_i,z_i)) and time (t_i); a receiver needs four equations for unknowns (x,y,z,d), using

$$\sqrt{(x-x_i)^2+(y-y_i)^2+(z-z_i)^2}=(t_r-t_i+d)c,$$

where (t_r) is receive time, (d) receiver-clock drift, and (c) light speed. **Source:** [[03_Synchronization-of-Clocks]], pp. 8-10.

## Algorithms / Procedure

**NTP:** a hierarchy of accurate stratum-1 time servers; a client contacts a server, estimates clock difference, and applies a clock-synchronization method. **Source:** [[03_Synchronization-of-Clocks]], p. 11.

**Cristian's algorithm:** client sends at (t_1), server receives (t_2), replies at (t_3), client receives at (t_4). Assuming zero server/network processing and equal request/reply transit time,

$$t_2-(t_1+\Delta)=t_4-\Delta-t_3,\qquad \Delta=\frac{(t_2-t_1)+(t_4-t_3)}{2}.$$

Here (Delta) is the estimated one-way delay; use server time adjusted by this delay. **Source:** [[03_Synchronization-of-Clocks]], p. 12.

**Berkeley algorithm:** choose a master; master uses Cristian-style exchanges to find each slave's drift, computes mean drift, sends each slave an adjustment, and also adjusts itself to minimise adjustments. **Source:** [[03_Synchronization-of-Clocks]], p. 13.

## Limitations and edge cases

No global clock exists: physical observers can see “simultaneous” events differently because of propagation delay, and local clocks have technological limitations and drift. This complicates scheduling, debugging/event reasoning, and collecting an up-to-date global state. Unsynchronised replicas can each accept updates in incompatible orders; banking transfers are an example where causal order matters. **Source:** [[03_Synchronization-of-Clocks]], pp. 19-26.

With synchronized clocks, total-order multicast stamps messages; receiver queues each message until timestamp (t+\Delta) and delivers in timestamp order. But a missing message can violate exact same-order delivery. A receiver may deliver only when all lower-timestamp messages are already present and none will arrive. **Source:** [[03_Synchronization-of-Clocks]], pp. 15-16.

## Test Your Understanding

1. Derive Cristian's (Delta) from the four timestamps.

> [!hint]- Answer hint
> Equate request and reply one-way delay under the stated symmetry assumption.

2. Give an application where clock skew is a security problem.

> [!hint]- Answer hint
> Think certificate/credential expiration or replay-window checks.

> [!summary]
> Physical synchronization bounds real-time disagreement; it cannot remove network-delay assumptions. Logical clocks handle causal, not wall-clock, order.

# 4. Logical clocks, causality, and causal delivery

## Overview and definitions

The **happened-before** relation (a\to b) captures causal influence. Events are **concurrent**, (a\parallel b), when neither (a\to b) nor (b\to a); for distinct events, exactly one of (a\to b,b\to a,a\parallel b) applies. **Source:** [[02_Logical-Clocks-and Event Ordering]], p. 4.

## Lamport scalar clocks

Each process (P_i) has a monotonically increasing clock (C_i) that maps event (a) to timestamp (C_i(a)); it has no physical-time meaning. Required conditions are:

$$a\to b\implies C(a)<C(b).$$

This follows if (C1) successive events in a process increase clock and (C2) a message's receive timestamp exceeds its send timestamp. Implementation, with (d>0), usually (d=1):

```text
before each local/send/receive event at Pi: Ci := Ci + d
on send m: ts(m) := Ci
on receive m with timestamp tm: Cj := max(Cj, tm + d)
```

Apply IR1 before IR2. **Source:** [[02_Logical-Clocks-and Event Ordering]], pp. 8, 12-13.

Scalar clocks are monotone and can impose a total order (break equal timestamps with process ID). A total order compares every pair and gives one sequence; a partial order compares only some pairs and permits multiple valid sequences. **Source:** [[02_Logical-Clocks-and Event Ordering]], pp. 16-19.

> [!warning]
> Lamport clocks are not strongly consistent: (C(e_i)<C(e_j)\not\Rightarrow e_i\to e_j). A scalar collapses local time and global causal knowledge, losing dependencies among remote events. **Source:** [[02_Logical-Clocks-and Event Ordering]], pp. 21-22.

## Vector clocks

For (n) processes, (P_i) keeps (VC_i[1..n]). (VC_i[i]) is its number of events; (VC_i[j]=x) means (P_i) knows (P_j)'s local time progressed to (x). The whole vector is (P_i)'s global logical-time view. **Source:** [[02_Logical-Clocks-and Event Ordering]], pp. 27-28.

On every local/send/receive event increment own component. A send carries the current vector. On receiving timestamp (t_m), take componentwise maximum:

$$VC_j[k]:=\max(VC_j[k],t_m[k])\quad\forall k,$$

after applying the local increment. Define (V<W) iff every component of (V) is no greater than the corresponding component of (W), and at least one is strictly smaller. Then (a\to b\) iff (VC(a)<VC(b)); incomparable vectors represent concurrency. **Source:** [[02_Logical-Clocks-and Event Ordering]], pp. 29, 32.

## Causal communication

If (P_j) receives (m) from (P_i), delay delivery to the application until

$$ts(m)[i]=VC_j[i]+1\quad\text{and}\quad ts(m)[k]\le VC_j[k]\;\forall k\ne i.$$

The first condition makes (m) the next expected message from (P_i); the second says (P_j) has seen everything (P_i) had seen when it sent (m). **Source:** [[02_Logical-Clocks-and Event Ordering]], pp. 34-35.

## Test Your Understanding

1. Give vectors (V,W) that prove two events concurrent.

> [!hint]- Answer hint
> Make one component larger in (V) and another larger in (W).

2. Why does process ID tie-breaking create a total order without proving causality?

> [!hint]- Answer hint
> It resolves equal timestamps by convention, not information.

## Previous-Year / Supplied Question-Paper Questions

### Question: logical/vector clocks and causal delivery

> **Original question:** “Explain how logical clocks can be used to mark a unique time-stamp for each event across different nodes in a distributed system.” **Source:** [[Distributed Computing sem paper]], 2025, Q1(i), 5 marks.

#### Answer

Run Lamport IR1 and IR2 above. Make the timestamp unique as the ordered pair ((C_i(e),i)), ordered lexicographically: counter first, process ID second. IR1 makes local order increase; IR2 makes send precede its receive; transitivity gives (a\to b\Rightarrow T(a)<T(b)). The tie-breaker is only for total ordering; it does not make the converse causal.

> **Original question:** “Describe all the events that are happening in the given diagram below. How this type of clock has overcome the limitations of Lamport's Clock.” **Source:** [[Distributed Computing sem paper]], 2025, Q1(i), 6 marks.

#### Answer

The supplied diagram is a three-process vector-clock space-time diagram (labels visibly include ((0,1,0),(0,1,1),(0,2,1))); read each dot as a local/send/receive event and each sloping arrow as a send-to-receive causal edge. At every event increment the owning vector component; at receive take componentwise max with the carried timestamp. Unlike Lamport scalar timestamps, vectors retain what every process knows about every process. Thus (V<W) exactly establishes causality and incomparable vectors establish concurrency, whereas scalar (C(a)<C(b)) alone cannot.

> **Original question:** “Explain the mechanism how causal communication has been enforced in vector clocks.” **Source:** [[Distributed Computing sem paper]], 2025, Q1(ii), 4 marks.

#### Answer

Buffer a received message from (P_i) at (P_j) until its sender component is exactly the next expected value, (ts(m)[i]=VC_j[i]+1), and all other components are no newer than what (P_j) already knows. Then deliver and update. This prevents delivery before any causal predecessor, while allowing concurrent messages in either order.

> **Original question:** 2021 Q1 asks for all vector-clock updates for the three-site broadcast schedule using Birman-Schiper-Stephenson. **Source:** [[Distributed ComputingSemester2021]], Q1, 20 marks.

#### Answer

Use the event table in the paper and initial ((0,0,0)). At each broadcast increment sender's own component and attach the vector; buffer an arrival until its immediate causal predecessor from that sender is delivered, as stated in the paper. The essential marking procedure is: (i) calculate each broadcast vector in local event order; (ii) apply componentwise maximum at a deliverable arrival; (iii) retain any arrival whose predecessor is absent in the buffer; and (iv) release buffered messages when the two causal-delivery inequalities become true. The paper's line diagram is sufficient to enter each exact value, but its arrows/relative positions must be read from the original figure; no invented values are supplied here.

> [!summary]
> Scalar clocks preserve “cause implies smaller timestamp”; vectors preserve causality in both directions and therefore implement causal delivery with buffering.

# 5. Spanning trees, broadcast, convergecast, and MST

## Overview

The rooted-tree procedures of [[#2. Message-passing model and distributed execution]] make global work tractable. The separate spanning-tree lecture develops a distributed minimum-spanning-tree construction (presented as GHS-style component merging). **Source:** [[Distributed-Spannig Tree]], pp. 10-22.

## Distributed MST construction

Start with (n) one-node components (level 0). Every node knows its UID, incident edges, and weights, and initially leads itself. Repeatedly find every component's **minimum-weight outgoing edge (MWOE)** and merge components through selected MWOEs until one component remains. **Source:** [[Distributed-Spannig Tree]], pp. 11-13.

1. Broadcast a search within the component (the slides call this intra-component BFS).
2. Test neighbours for component leader UID. Same UID means internal edge; different UID means outgoing. Each node retains its smallest-weight outgoing candidate.
3. Convergecast candidates to the component leader; it chooses the component's global MWOE.
4. Inform the two endpoints; mark this edge as spanning structure and merge components.
5. Select larger of the two endpoint UIDs as new leader; broadcast new leader identity.

Levels impose controlled merging: two level-0 components make level 1; a level-0 plus level-1 merge remains level 1; equal levels advance. This prevents conflicting activity. Termination is detected when a new MWOE search finds every neighbour has the same component UID. **Source:** [[Distributed-Spannig Tree]], pp. 14-19.

## Complexity and uses

At each level, propagation/processing costs (O(n)) time. Component sizes grow geometrically, so at most (O(\log n)) levels:

$$T=O(n\log n),\qquad M=O((n+e)\log n),$$

where (n) is nodes and (e) edges. The resulting MST is a communication backbone; leaf-to-root convergecast aggregates global values, and the higher-UID candidate can be elected through the tree. **Source:** [[Distributed-Spannig Tree]], pp. 20-22.

## Test Your Understanding

1. Why is an MWOE safe to add when building an MST?

> [!hint]- Answer hint
> Use the cut defined by one component.

2. Explain why levels yield (O(\log n)) phases.

> [!hint]- Answer hint
> Equal-level component merging grows component size geometrically.

> [!summary]
> A tree turns arbitrary-network communication into parent/child operations. MWOE merging creates a minimum-weight backbone with stated (O(n\log n)) time and (O((n+e)\log n)) messages.

# 6. Leader election

## Overview and specification

Leader election is a symmetry-breaking problem: every processor irreversibly reaches `elected` or `not-elected`; in every admissible execution all eventually decide and exactly one enters `elected`. A leader coordinates activities, can root a spanning tree, and can help reconstruct a lost token. **Source:** [[04_Classical Leader Election Algorithms]], pp. 2-4.

System assumptions in the protocol lecture: (N) processes, unique IDs, eventual message delivery; failures can occur. Any process may call at most one election at a time; simultaneous initiators must yield one leader and the result must not depend on initiator. Desired safety elects exactly one nonfaulty best/highest-attribute process; liveness says each run terminates and all nonfaulty processes learn a non-null elected process. **Source:** [[05_Distributed Leader Election Protocols]], pp. 4-7.

## Ring concepts and impossibility

An oriented ring has consistent left/right; forwarding on one labelled channel sends clockwise. A **uniform** algorithm does not use ring size; a non-uniform one does. In anonymous rings, processors lack unique IDs. There is no election algorithm for anonymous rings even if size is known and the model is synchronous: identical initial states/messages lead every processor through the same transitions; if one elects, all elect (safety violation), and if none do, liveness fails. The result holds for weaker uniform/asynchronous models. **Source:** [[04_Classical Leader Election Algorithms]], pp. 5-10.

## Algorithms / Procedure

### LCR / (O(n^2)) unidirectional-ring algorithm

Every process sends own ID left. On receiving ID (j): forward if (j>id) (this process loses); elect self if (j=id); discard if (j<id). The maximum ID traverses the ring and returns to itself, so it wins. Time (O(n)). Worst decreasing-ID arrangement sends

$$n+(n-1)+\cdots+1=\Theta(n^2)$$

messages, including termination messages. **Source:** [[04_Classical Leader Election Algorithms]], pp. 11-15.

### Hirschberg-Sinclair (HS) / (O(n\log n)) bidirectional-ring algorithm

Phase (k) probes distance (2^k) in both directions. A probe encountering a larger ID is swallowed; otherwise boundary sends a reply. A process receiving replies from both directions is phase winner and enters phase (k+1). Receiving its own probe elects it, then it sends termination. A phase-(k) winner has largest ID in its (2^{k-1})-neighbourhood; number of winners is at most (n/(2^{k-1}+1)). There are at most (lceil\log(n-1)\rceil+1) phases and total messages (O(n\log n)). Correctness follows as for LCR: a smaller ID cannot eliminate larger; only maximum can survive all neighbourhoods. **Source:** [[04_Classical Leader Election Algorithms]], pp. 17-30.

> [!important]
> In unknown-size asynchronous comparison-based rings, (Omega(n\log n)) is a lower bound. In synchronous rings, (O(n)) is possible under conditions permitting general arithmetic/non-comparison operations and unbounded time. **Source:** [[04_Classical Leader Election Algorithms]], p. 30.

### Classical ring protocol (failure-free case)

Initiator sends `Election(own id:attr)` clockwise. Receiver forwards a larger carried attribute; if its own is larger and it has not forwarded an election before, it overwrites and forwards; receipt of its own ID means it is highest and it sends `Elected(id)`. A recipient records the elected ID and forwards unless it is the new coordinator. Best case (highest initiates): (2N) messages, completion (2N) message transmission times. Worst case (initiator successor of leader): (3N-1) messages/times. **Source:** [[05_Distributed Leader Election Protocols]], pp. 8-18.

Concurrent initiators cache their initiator ID, suppress lower-ID election/elected messages, update on a higher-ID initiator, leaving only the highest-ID run. **Source:** [[05_Distributed Leader Election Protocols]], p. 19.

### Bully algorithm

All know IDs. On detecting coordinator failure: if a process is highest, declare itself and send `Coordinator` to lower IDs; otherwise send `Election` only to higher IDs. A receiver replies `OK` and starts its own election unless already doing so. If caller gets no answer before timeout, it declares coordinator; if it gets an answer, it awaits `Coordinator` then restarts after timeout if absent. Worst-case election messages are ((N-1)+(N-2)+\cdots+1=(N-1)N/2=O(N^2)); in the shown failure-free timing analysis, worst completion is five transmission intervals and best case is second-highest detector sending coordinator messages. **Source:** [[05_Distributed Leader Election Protocols]], pp. 25-33.

## Failures, consensus, and industry systems

If elected process fails before/while announcement circulates, a ring can violate liveness. Reinitiation by predecessor/successor can itself fail; failure detectors may be incomplete (missed failure, safety issue) or inaccurate (false suspicion, endless election/liveness issue). In a purely asynchronous model, timeout protocols may never terminate. Election is consensus-related: elected process ID's last bit could encode a consensus decision; because consensus is impossible under the stated asynchronous setting, so is guaranteed leader election. **Source:** [[05_Distributed Leader Election Protocols]], pp. 20-24, 31-33.

**Paxos overview:** proposers propose values; acceptors accept/reject under rules; learners learn agreed value; a quorum (usually majority) is required. Example: A sends `Prepare(1)` to B,C, receives `Promise(1)`, sends `Propose(X)`, receives `Accepted(X)`; 2/3 acceptance agrees (X). Advantages stated: tolerate some failures while majority operates, no single point of failure, eventual same value. **Source:** [[05_Distributed Leader Election Protocols]], pp. 38-41.

**Chubby/ZooKeeper:** Chubby uses replicas; potential leader gains votes, each server votes at most once, majority becomes master. ZooKeeper uses Zab, gives each server a sequence number/ID and selects highest ID; monitoring only next-higher ID avoids everyone monitoring master, and successor becomes leader when its higher successor fails. **Source:** [[05_Distributed Leader Election Protocols]], pp. 36-45.

## Comparison

| Algorithm | Topology/model | Messages | Core trade-off |
|---|---|---:|---|
| LCR | Unidirectional ring, unique IDs | (Theta(n^2)) worst | simplest, works sync/async |
| HS | Bidirectional ring, IDs | (O(n\log n)) | phased, more complicated |
| Classical ring | clockwise ring, failure-free analysis | (2N) to (3N-1) | need separate failure handling |
| Bully | known all IDs / timeouts | (O(N^2)) worst | quick under timing assumptions; timeout-sensitive |
| Paxos-style | quorum replicas | not stated in source | consensus-backed fault tolerance |

## Test Your Understanding

1. Prove that LCR's maximum ID is never discarded.

> [!hint]- Answer hint
> A receiver forwards every ID larger than its own.

2. Trace HS phases for IDs 3, 12, 5, 80, 6, 32.

> [!hint]- Answer hint
> At each phase, a candidate must be locally maximal within doubled radius.

3. Why does the anonymous-ring impossibility proof violate safety or liveness?

> [!hint]- Answer hint
> Symmetry makes every process take the same transition.

## Previous-Year / Supplied Question-Paper Questions

### Question: election algorithms

> **Original question:** “Consider a Complete Graph of 5 nodes. Describe a Leader Election Algorithm considering synchronous rounds. Count the No of rounds.” **Source:** [[DC CT Re-Test 2022]], Class Test 1 Q1, 15+7 marks; also [[Distributed ComputingSemester2021]], 2023 Q1.

#### Answer

Let each node broadcast its UID to all four neighbours in round 1. At end of that round every node knows all UIDs; the maximum UID declares itself leader in round 2 and broadcasts `LEADER(maxUID)`. All nodes decide at the end of round 2. Thus two synchronous rounds (one if election is considered locally complete once every node receives all UIDs and deterministic-max is implicit). Message count is (5\cdot4=20) directed UID messages plus 4 or 20 announcement messages depending whether only leader announces to all or every node echoes. Correctness: all nodes receive identical UID set, deterministically choose the unique maximum, hence agreement and uniqueness; termination occurs after bounded two rounds.

> **Original question:** “Develop and analysis a distributed algorithm for Leader Election in a ring network of n nodes. Give a brief outline of correctness.” **Source:** [[DC SEM 2022]], 2023 CO1, 10+6 marks.

#### Answer

Use LCR. Each node sends its UID clockwise; forward only larger IDs; receiving own UID elects self and circulates leader announcement. Maximum UID is never discarded and completes a ring, so it elects; any smaller UID meets the maximum and is discarded, so no second leader exists. It terminates after at most (n) hops for the maximum plus announcement, time (O(n)), worst messages (Theta(n^2)). For fewer messages use HS, (O(n\log n)).

> **Original question:** “During an election, if the highest-ID process fails before declaring itself the leader, how does the algorithm proceed to ensure a new leader is elected?” **Source:** [[Distributed Computing sem paper]], 2025 CO2 Q1(d), 3 marks.

#### Answer

Timeout/failure detector detects missing announcement or response; an eligible nonfaulty process starts/restarts an election excluding the suspected failed candidate. In Bully, it contacts higher IDs and either hears `OK` or times out and declares itself; in a ring, the relevant process reinitiates/circulates a new election. This only guarantees eventual success when failures stop and failure detection/timing assumptions are adequate; pure asynchrony cannot promise a finite timeout-based completion.

> [!summary]
> Election requires unique decision and eventual agreement. Ring algorithms trade message complexity for sophistication; failure-tolerant election depends on detector/quorum assumptions and is tightly related to consensus.

# 7. Exam extensions: snapshots, mutual exclusion, deadlock, files, and transactions

> [!warning]
> The seven lecture PDFs only name many of these as syllabus items; the detailed content below is included because supplied papers explicitly examine it. It is a concise exam-answer framework, not a claim that all details appeared in the seven lecture decks. **Source for syllabus scope:** [[01_Introduction-DC]], pp. 40-42.

## Global snapshots and consistent cuts

> [!definition]
> A global state consists of each process's local state plus each channel state. A cut/frontier selects one point per process history. It is **consistent** if it contains no receive event without the corresponding send; equivalently, it is closed under happened-before.

**Chandy-Lamport (FIFO channels):** initiator records local state and sends a marker on every outgoing channel. On first marker at process (p), record (p)'s state, record the incoming channel carrying marker as empty, send markers on outgoing channels, and record messages arriving on other incoming channels until marker arrives on each. On a later marker for channel (c), stop recording (c); its recorded messages are (c)'s state. Markers separate pre-snapshot from post-snapshot messages, so the cut is consistent. It requires reliable FIFO channels and a way to initiate/collect recorded states. **Source:** question-paper topic, especially [[Distributed Computing sem paper]], 2025 CO2 Q2.

## Mutual exclusion

Mutual exclusion protects a critical section (CS). Correctness: **safety** (at most one in CS), **liveness/progress** (a request is eventually granted under assumptions), no starvation, and no deadlock. Performance commonly measures messages/CS, response delay, and synchronization delay.

**Ricart-Agrawala:** requester timestamps and sends `REQUEST(ts,id)` to all (N-1) peers; enter after replies from all; defer a reply if own request has lower lexicographic ((ts,id)), otherwise reply immediately; on release send all deferred replies. It uses (2(N-1)) messages/CS. In the supplied scenario, (P_2)'s timestamp 32 precedes (P_1)'s 47, so (P_2) gets replies and enters first; (P_1)'s reply from (P_2) is deferred until (P_2) exits, then (P_1) enters. **Source:** [[Distributed ComputingSemester2021]], Q3.

## Distributed deadlock

In a wait-for graph (WFG), (P_i\to P_j) means (P_i) waits for resource/action of (P_j). In the **AND** model a process waits for all requested resources; a directed cycle is necessary and sufficient for deadlock. In the **OR** model a process can proceed if any requested resource is granted; a cycle alone is necessary but not sufficient, and a knot (a nonempty set whose outgoing wait edges stay within set) characterizes deadlock.

**Chandy-Misra-Haas probe, AND model:** a blocked initiator (i) sends `probe(i,i,j)` to each (j) it waits for. When blocked (k) receives `probe(i,j,k)`, it forwards `probe(i,k,l)` to every (l) it waits for. If (i) receives `probe(i,*,i)`, it declares deadlock. For (P_1\to P_2\to P_3\to P_1): `probe(1,1,2)`, `probe(1,2,3)`, `probe(1,3,1)`; final return proves a cycle. If (P_3) releases (R_3) before forwarding, it is no longer blocked, forwards no probe, and no detection occurs - correctly avoiding a stale false positive. **Source:** [[DC_CT2_2024]], Q3.

Probe approaches distribute work and need not construct/maintain a global WFG; they detect a real dependency cycle under their model, while WFG approaches can suffer inconsistent, obsolete views. Deadlock avoidance is generally unsuitable in DS because it needs complete current global allocation/request knowledge and safe-state prediction despite delay/failure. **Source:** [[DC_CT2_2024]], Q2.

## Distributed file systems and GFS

A distributed file system disperses clients, servers, and storage across machines. Naming maps logical to physical objects; location transparency lets file name remain unchanged when physical storage migrates. A file may be identified by host+local name in a traditional scheme; an immutable file cannot be changed after creation. Examples named in papers include Andrew FS, NFS, Novell network. **Source:** [[DC SEM 2022]], pp. 2-3.

GFS constraints: component failures are normal across thousands of inexpensive components; continuous monitoring/error detection/fault tolerance/recovery are integral. Files are multi-GB, may contain billions of objects, are mostly append/write-once then sequentially read; workloads include large streaming reads and forward small random reads, where sustained bandwidth dominates latency. It therefore revisits I/O/block sizes and supports sequential reads and atomic mutations. **Source:** [[01_Introduction-DC]], pp. 37-39.

## Distributed transactions

**Two-phase commit (2PC):** coordinator asks participants to vote/prepare. Each durably logs `READY/AGREED` and replies YES only if it can commit; any NO leads coordinator to log/announce ABORT. If all YES, coordinator logs COMMIT, sends COMMIT, receives acknowledgements, and completes. This provides atomic all-or-nothing decision under recoverable crash logging, but can block if coordinator fails and participants are uncertain.

Recovery: if coordinator failed before commit log after all YES, it must consult durable state/participants; without durable commit it must not invent commit, and may abort if safe or wait for a known decision. If it failed after durable COMMIT but before all ACKs, it re-sends COMMIT after recovery until ACKs arrive. 2PC handles many crash/recovery cases via stable logs but not permanent partition/coordinator-loss blocking without an additional termination protocol/consensus. **Source:** [[Distributed ComputingSemester2021]], Q5; [[Distributed Computing sem paper]], 2025 CO6.

**Two-phase locking (2PL):** transaction has a growing phase acquiring locks and shrinking phase releasing; it acquires no lock after first release. It is sufficient for conflict serializability because serialise transactions by their lock point (time each acquired its final lock). It is not necessary: a serial schedule that releases then later acquires locks can still be serializable but violates 2PL.

## Test Your Understanding

1. Why does a snapshot record channel state rather than just process states?

> [!hint]- Answer hint
> A message can have been sent but not received at the cut.

2. Trace the CMH probes for a four-node WFG with one cycle and one outgoing edge.

> [!hint]- Answer hint
> Track initiator, sender, and receiver tuple fields.

3. Why can 2PC block even when every participant is correct?

> [!hint]- Answer hint
> Consider a prepared participant after coordinator crash.

## Previous-Year / Supplied Question-Paper Questions

### Snapshot questions

> **Original question:** “How we can distinguish between the message to be recorded in the snapshot from those not to be recorded? Describe the snapshot algorithm and explain how it records consistent cuts.” **Source:** [[DC_CT2_2024]], Q1(a,b), 3+7 marks.

#### Answer

For each incoming FIFO channel, record messages received after local state is recorded but before its marker arrives; do not record messages received before local-state recording or after marker. Execute the Chandy-Lamport rules above. The marker creates a boundary on every channel: a recorded message was sent before sender recorded state but received after receiver recorded state, so it belongs to channel state; no receive is included without its send. Hence the union is a consistent cut.

### Mutual exclusion questions

> **Original question:** “Write the Applications of Mutual Exclusion Methods. Analyze…” **Source:** [[DC CT Re-Test 2022]], Class Test 2 Q2 (scan cuts off after “Analyze”).

#### Answer

Applications include exclusive update of replicated metadata/configuration, distributed printer/device access, one-at-a-time transaction/record update, leader-only administration, file locking, and protection of a shared resource/token. Analyze using safety, liveness/no starvation, no deadlock, message complexity, response delay, synchronization delay, fault tolerance, and scalability. The remainder of the scanned question is not legible; no wording is inferred.

### Deadlock questions

> **Original question:** “Describe Kshemkalyani Algorithm for dead lock detection. Run the algorithm for 4 nodes with all having degree 2. Analyze the algorithm.” **Source:** [[DC CT Re-Test 2022]], Class Test 2 Q1, 10+8+5 marks.

#### Answer

The requested algorithm is the Kshemkalyani-Singhal distributed deadlock-detection approach (detailed algorithm not present in the seven teaching PDFs). An exam answer should state its assumed resource/wait model, propagate dependency information/probes to construct a consistent detection view, identify the deadlocked set, and terminate detection when all relevant dependent paths have been explored. For a 4-node degree-2 ring (P_1\to P_2\to P_3\to P_4\to P_1), an initiator's dependency/probe eventually returns, showing a cycle in an AND model; report complexity in terms of message/path traversals from the exact version taught. The source materials supplied here do not state that version's pseudocode or complexity, so a more precise claim would be unsupported.

### File-system / transaction questions

> **Original question:** “What are the primary design principles of the Google File System… [and seven listed subquestions on scalability, failures, metadata, replication/consistency, chunk size, and write]?” **Source:** [[Distributed Computing sem paper]], 2025 CO5, 15 marks.

#### Answer

Answer from the GFS constraints above: commodity component failures are normal, huge files and append/sequential workloads dominate, sustained bandwidth matters, and APIs/block/I/O design are deliberately adapted. Fault tolerance uses monitoring, error detection, recovery and replication; metadata is master-managed in conventional GFS architecture; large chunks reduce metadata/load but can create hot spots and waste small-file space; replicated chunks trade storage/network cost for availability/durability. A write selects/coordinates replicas and applies a defined mutation order so replicas converge; use atomic record append where required. The supplied intro slides state the design constraints, but not a full GFS write-message sequence or metadata-storage implementation detail, so those implementation specifics should be checked against the course's GFS reference before reproducing them verbatim.

> **Original question:** “Consider the 2-phase commit protocol… coordinator recovers [before commit record after AGREED; after commit record and some ACKs]. Prove two phase locking is sufficient for serializability; show it is not necessary.” **Source:** [[Distributed ComputingSemester2021]], Q5, 20 marks.

#### Answer

Use the 2PC recovery and 2PL proof above. For non-necessity, give a serial schedule (T_1;T_2) whose transactions release a lock then later acquire a different lock; it is conflict-serializable by being serial, but violates the “no acquire after first release” 2PL rule.

# Rapid Revision Sheet

| Item | Revision fact |
|---|---|
| Happened-before | (a\to b) means causal influence; concurrency means neither direction. |
| Lamport | (a\to b\Rightarrow C(a)<C(b)), not converse; receive (C_j:=\max(C_j,t_m+d)). |
| Vector | componentwise max on receive; (V<W) exactly represents causality. |
| Causal delivery | sender component is next expected; all other components already known. |
| Cristian | (Delta=((t_2-t_1)+(t_4-t_3))/2) under symmetric delay. |
| Broadcast tree | (n-1) messages, time depth (d). |
| LCR | (O(n)) time, (Theta(n^2)) worst messages. |
| HS | (O(n\log n)) messages; radius doubles each phase. |
| GHS-style MST | (O(n\log n)) time, (O((n+e)\log n)) messages. |
| Snapshot | record process state plus in-transit channel messages; marker boundary yields consistent cut. |
| RA mutual exclusion | (2(N-1)) messages/CS; lower timestamp gets priority. |
| CMH | probe returning to initiator detects AND-model cycle. |
| 2PC | stable logs provide recovery; prepared participants can block after coordinator failure. |

> [!warning]
> Common exam mistakes: claiming scalar timestamp order proves causality; omitting channel state in a snapshot; confusing a cycle (AND) with a deadlock proof in OR; or promising timeout liveness in a fully asynchronous model.

# Comprehensive Practice Question Bank

1. Explain why location transparency and migration transparency are related but not identical.

> [!hint]- Answer hint
> One hides naming/location; the other governs movement and in-flight messages.

2. A message has timestamp ([4,2,7]) and receiver clock is ([4,1,7]). May it be causally delivered if sender is process 2?

> [!hint]- Answer hint
> Check sender component first, then all other components.

3. Compare LCR and HS on topology, message count, and their proof that only maximum wins.

> [!hint]- Answer hint
> HS eliminates candidates by expanding neighbourhoods.

4. Give a schedule under which asynchronous flooding does not make a BFS tree.

> [!hint]- Answer hint
> Delay a short-path message until a longer-path path reaches the node.

5. A component's MWOE is selected. Explain why broadcasting new component UID matters before next-level testing.

> [!hint]- Answer hint
> Nodes need consistent internal/outgoing-edge classification.

6. Compare external and internal clock synchronization.

> [!hint]- Answer hint
> One references UTC/known clock; the other bounds pairwise disagreement.

7. Design a causal multicast receiver buffer using vector clocks.

> [!hint]- Answer hint
> Re-test buffered messages whenever a delivery advances the vector.

8. Why does a false failure suspicion endanger liveness in repeated election?

> [!hint]- Answer hint
> It can restart elections indefinitely.

9. Trace CMH probes for (P_1\to P_2\to P_3\to P_1), then remove the last wait edge.

> [!hint]- Answer hint
> The return probe disappears after the edge removal.

10. Explain how snapshot markers and causal-delivery conditions both distinguish “before” from “after” across channels.

> [!hint]- Answer hint
> Both impose a boundary using transmitted control/timestamp information.

# Question-Paper Index

| Question paper | Question number | Topic | Marks | Link to solution |
|---|---:|---|---:|---|
| Distributed Computing sem paper (2025) | 1(i) | Transparency, clocks, ring counting, vectors | 20 | [[#1. Distributed systems foundations]], [[#4. Logical clocks, causality, and causal delivery]] |
| Distributed Computing sem paper (2025) | 1(ii) | Causality, causal delivery, physical clocks | 20 | [[#2. Message-passing model and distributed execution]], [[#3. Physical-clock synchronization]], [[#4. Logical clocks, causality, and causal delivery]] |
| Distributed Computing sem paper (2025) | CO2 Q1-Q3 | Snapshots, election, ME | 20+20 | [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]], [[#6. Leader election]] |
| Distributed Computing sem paper (2025) | CO4 Q1-Q2 | Deadlock | 10+10 | [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]] |
| Distributed Computing sem paper (2025) | CO5 Q1-Q7 | GFS | 15 | [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]] |
| Distributed Computing sem paper (2025) | CO6 Q1-Q2 | Transactions/2PC | 15 | [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]] |
| Distributed ComputingSemester2021 | Q1 | Vector clocks / BSS | 20 | [[#4. Logical clocks, causality, and causal delivery]] |
| Distributed ComputingSemester2021 | Q2 | AND/OR deadlock, CMH | 20 | [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]] |
| Distributed ComputingSemester2021 | Q3 | Ricart-Agrawala | 20 | [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]] |
| Distributed ComputingSemester2021 | Q4 | NFS | 20 | [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]] |
| Distributed ComputingSemester2021 | Q5 | 2PC, 2PL | 20 | [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]] |
| DC SEM 2022 (2023) | Q1 | Election/snapshots/MIS | 20 | [[#6. Leader election]], [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]] |
| DC SEM 2022 (2023) | Q2-Q4 | ME, deadlock, DFS MCQs | 80 | [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]] |
| DC_CT2_2024 | Q1-Q3 | Snapshots, deadlock | 30 | [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]] |
| DC_CT1_2024 | Q1-Q3 | Clock/ME/election (scan rotated) | 30 | [[#4. Logical clocks, causality, and causal delivery]], [[#6. Leader election]] |
| DC CT Re-Test 2022 | CT1 Q1-Q2; CT2 Q1-Q2 | Election, clocks, deadlock, ME | 30 | [[#3. Physical-clock synchronization]], [[#6. Leader election]], [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]] |
| Semester paper (2024) | CO1-CO6 | Algorithms, clocks, snapshots, ME, deadlock, caching, transactions | 100 | [[#1. Distributed systems foundations]], [[#7. Exam extensions snapshots mutual exclusion deadlock files and transactions]] |

> [!warning]
> Several question papers contain questions on NFS, mutual exclusion variants, deadlock variants, and transactions that the seven lecture PDFs only list in the syllabus. They are indexed and given answer frameworks above; where an exact source diagram/pseudocode is unreadable or absent, this note labels that limitation rather than fabricating it.

# Coverage Checklist

| PDF | Pages checked | Topics covered | Tables/diagrams/equations included | Status |
|---|---:|---|---|---|
| 01_Introduction-DC | 43/43 | foundations, transparency, models, scaling, GFS constraints | transparency table; complexity measures; GFS constraints | Complete visual/text pass |
| 02_Logical-Clocks-and Event Ordering | 49/49 | causality, Lamport, vectors, causal delivery | IR rules; causal-delivery inequalities; scalar limitation diagram described | Complete visual/text pass |
| 03_Synchronization-of-Clocks | 26/26 | quartz/atomic/GPS, NTP, Cristian, Berkeley, ordering | GPS and Cristian equations; timing diagrams described | Complete visual pass |
| 04_Classical Leader Election Algorithms | 31/31 | anonymity, LCR, HS, lower bound | ring/probe diagrams described; complexity derivation | Complete visual pass |
| 05_Distributed Leader Election Protocols | 47/47 | ring, Bully, failures, Paxos, Chubby, ZooKeeper | message/count comparisons, Paxos flow | Complete visual pass |
| Distributed-Spannig Tree | 22/22 | GHS-style MST and tree leader election | MWOE/levels/complexity | Complete text/visual pass |
| Message Passing Algorithms | 31/31 | model, events, sync/async, broadcast/convergecast, flooding tree | state/channel and tree diagrams described; pseudocode | Complete visual pass |

## Question-paper completion checklist

- [x] All nine supplied question-paper PDFs were opened page-by-page (16 pages total).
- [x] Readable question wording was indexed by topic and supported questions were answered after concepts.
- [x] Scan extraction failures/illegible diagrams were called out instead of guessed.
- [x] Duplicate election, clock, snapshot, deadlock, ME, file-system, and transaction questions were consolidated with cross-links.

