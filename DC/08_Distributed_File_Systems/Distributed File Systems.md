# 8. Distributed file systems

A **distributed file system** lets applications name and access files across machines. Its design determines which details of placement the client sees, how quickly it sees another client's changes, and what happens when a server fails. NFS extends ordinary file access over a network. The original Google File System (GFS) was designed around large, data-intensive jobs and frequent commodity failures.

## Naming and transparency

Naming maps logical names to file-system objects and physical locations. A good DFS separates:

1. Human pathname
2. Stable object/file identifier
3. Current server and storage location

Location transparency means a pathname does not reveal where a file is stored. Migration transparency means the file can move without changing the name or breaking clients. Replication transparency hides multiple copies while the system preserves its chosen consistency rule. A location-transparent name can remain unchanged even when the file’s handle or binding must be refreshed after migration. Migration transparency goes further by keeping the move transparent to clients.

## File-sharing semantics

| Semantics | Visibility rule |
|---|---|
| UNIX semantics | A completed write is immediately visible to later reads by any process |
| Session semantics | Changes are made visible to other clients when the writer closes the file/session |
| Immutable files | File never changes after creation; updates create a new file/version |
| Transaction semantics | Operations are grouped with atomicity/isolation guarantees |

NFS caching approximates close-to-open consistency rather than guaranteeing instantaneous global UNIX visibility in all cases. Applications needing serialized shared writes should use locking or stronger coordination.

## Network File System (NFS)

### NFS architecture

```text
Application
   |
System calls: open/read/write/close
   |
VFS/vnode layer
   | local vnode operations       | NFS vnode operations
Local file system                 NFS client
                                      |
                                  RPC/XDR network
                                      |
                                  NFS server -> server VFS -> disk FS
```

The VFS/vnode layer gives the kernel a uniform interface. A vnode identifies an active file-system object and points to operation methods. For a remote NFS object, vnode operations issue NFS RPCs using an opaque file handle.

### NFS file handles and mount

The server returns an opaque file handle that identifies an object within an exported file system. The client supplies that handle on later operations; it need not know the server's inode format.

In traditional NFS, the mount protocol obtains a handle for an exported directory. Subsequent file RPCs start from that handle. NFSv4 integrates navigation through the server namespace and stateful operations into the main protocol.

### NFSv2/v3 stateless style

The server does not rely on an `open` session for ordinary file access. Each request contains enough information to retry, typically file handle, offset, count, and credentials.

Advantages:

- Server reboot recovery is simpler.
- Retransmission handles lost request/reply messages.

Costs:

- Requests must be idempotent or carry duplicate-request protection.
- Client caching cannot provide strict global cache consistency by protocol alone.
- Lock management historically required an additional service.

### NFSv3 lookup

To resolve pathname component `x` under a directory:

1. Client already has the parent directory file handle.
2. Client sends `LOOKUP(directory-file-handle, "x")`.
3. Server checks directory permissions and name mapping.
4. On success it returns the target file handle and may return target and directory attributes.
5. Client caches the name-to-handle and attributes subject to validation/timeout rules.

For example, resolving /export/docs/a.txt starts with the handle for the export root. LOOKUP for docs returns a directory handle, which the next LOOKUP uses to find a.txt. The client can then READ the file using its handle, offset, and count. A multi-component path may take one RPC per uncached component. This motivates pathname caching and NFSv4 COMPOUND.

### NFS caching

Clients may cache file data, attributes, and directory/name lookups. Typical validation compares cached attributes/change information after a timeout or on open.

Benefits: fewer RPCs and lower read latency. Problems: stale data, write sharing, false sharing, and server/client failure during dirty-data writeback.

Common mechanisms:

- Attribute-cache timeouts
- Write-through or delayed write with `COMMIT`
- Close-to-open validation
- File/range locks for applications that need serialization
- Delegations in NFSv4

#### Cache placement and traffic reduction

- **Client memory cache:** fastest repeated access; lost on reboot and consumes RAM.
- **Client disk cache:** survives process lifetime and can serve larger working sets, but requires stronger validation and secure invalidation.
- **Server memory cache:** shared by clients and easier to keep authoritative, but every miss still crosses the network.
- **Whole-file caching:** few RPCs and good session semantics; poor for huge files and concurrent writers.
- **Block/chunk caching:** fetches only touched regions; needs per-block metadata and validation.

Use read-ahead, write-behind/batching, attribute/name caching, larger transfers, and `COMPOUND` operations to reduce traffic. These change when data crosses the network; they do not eliminate the need for a consistency policy.

#### Cache consistency and modification notification

Three standard approaches:

1. **Client polling/validation:** after a timeout or at open, compare server attributes/version/change counter. Simple and robust, with staleness permitted until the next effective validation. A timeout schedules a check and does not by itself guarantee a freshness bound after failure.
2. **Server callbacks/invalidation:** server records clients caching an object and sends `INVALIDATE`/recall after a conflicting modification. Low read traffic, but server must recover callback state and handle unreachable clients.
3. **Leases/delegations:** callback rights expire unless renewed. A conflicting request triggers recall; expiry bounds damage if client/server communication fails.

**Example.** Client A opens a file and later closes it after writing new data. Client B may still hold old bytes in its cache. On its next open, B revalidates its cached copy and fetches the new version if necessary. With a delegation, the server can recall the delegation on a conflicting open. The holder returns delegated state and flushes pending changes as required, and the server handles an unreachable holder under the protocol's lease and revocation rules.

### NFSv4

NFSv4 is stateful compared with v3. An OPEN can create server-side open state and return a state identifier used with later operations. The protocol integrates locking, security negotiation, leases, a unified namespace, and callbacks.

#### Compound procedure

A client places several ordered operations in one RPC, such as:

```text
PUTROOTFH -> LOOKUP("dir") -> OPEN("file") -> GETFH
```

The server executes in order and stops at the first failure. `OPEN("file")` uses the directory as the current file handle and selects the opened file. `GETFH` returns its handle. The compound reduces RPC round trips. Earlier operations may already have taken effect if a later one fails.

#### Open delegation

The server may delegate read or write responsibility for a file to a client:

- **Read delegation:** client can serve compatible opens/reads locally while no conflicting writer exists.
- **Write delegation:** client can handle opens, writes, and compatible locks locally while exclusive delegation remains.

If another client requests conflicting access, the server recalls the delegation through a callback. The server sends CB_RECALL. The holder flushes pending writes as required, reconstructs any necessary server-side open or lock state, and returns the delegation with DELEGRETURN. The conflicting requester can be told to retry while recall is in progress. Delegations trade fewer open and validation RPCs for recall and recovery work.

### Stable and unstable NFSv3 writes

A stable write requests a specified durability level before the server replies. With an unstable write, the server may acknowledge data still in volatile storage. The client retains the uncommitted data and later issues COMMIT to make it durable. The write verifier in replies lets the client detect a server restart or other loss of unstable state. If the verifier changes, resend the affected uncommitted writes. [RFC 1813, WRITE and COMMIT](https://www.rfc-editor.org/rfc/rfc1813#section-3.3.7)

### NFS failure behavior

- A lost idempotent RPC can be retransmitted.
- A stale file handle indicates the object/export identity is no longer valid.
- Client cache may temporarily serve stale data under loose validation.
- Stateful NFSv4 uses lease/reclaim rules after failures.
- Exactly-once execution is not obtained merely by using RPC; duplicate suppression and operation design matter.

## Google File System (GFS)

### GFS workload assumptions

The original GFS design assumes:

- Commodity failures are routine.
- Files are very large, often multi-GB.
- Workloads favor large streaming reads and large sequential writes/appends.
- Files are often write-once/read-many or append-heavy.
- High sustained aggregate bandwidth matters more than minimum individual-operation latency.
- Applications can use specialized semantics such as atomic record append.

These assumptions justify design choices that would be poor for a general desktop file system.

### GFS architecture

```text
                    metadata request
GFS client  ------------------------------>  Master
    |                                           |
    |  chunk handle + replica locations         | namespace, mapping,
    <--------------------------------------------| leases, placement
    |
    | data directly to/from replicas
    +------------------> Chunkserver A
    +------------------> Chunkserver B
    +------------------> Chunkserver C
```

One master manages metadata; clients transfer file data directly with chunkservers, preventing the master from becoming the data path.

### Chunks and metadata

Files are divided into chunks of up to 64 MB in the original design. The master assigns each chunk a unique 64-bit handle. The default is three replicas, subject to the configured replication policy.

The master maintains:

- Namespace and access-control information
- File-to-chunk mapping
- Chunk version numbers
- Replica locations
- Lease/primary information

Metadata is kept in memory for speed. Namespace and file-to-chunk mapping are durably represented by an operation log/checkpoints replicated on multiple machines. Chunk locations are learned from chunkservers at startup rather than relied on as permanently logged truth.

Large chunks reduce metadata and client–master interaction, allow long sequential transfers, and reduce connection setup. The original design uses lazy allocation to avoid reserving a full chunk on disk for a small file. A small file with many readers can still make its few chunk replicas hot spots.

### GFS read

1. Client translates file offset to chunk index.
2. Client asks master for the chunk handle and replica locations; it caches the answer.
3. Client selects a nearby replica.
4. Client sends `(chunk-handle, byte-range)` directly to that chunkserver.
5. Chunkserver returns data and verifies checksums.

The master handles location lookups. The client caches this metadata but does not maintain a GFS file-data cache. Chunkservers use the operating system’s buffer cache for frequently accessed disk blocks. An offset of 130 MiB, for example, is 2 MiB into chunk index 2 when chunks are 64 MiB and indexing starts at zero.

### GFS write/mutation

The master grants one replica a lease, making it **primary**; other replicas are secondaries.

1. Client asks master for primary and secondary locations.
2. Client pushes data to all replicas, commonly through a pipeline; data flow is separated from control/order.
3. After replicas acknowledge buffering, client sends the write request to the primary.
4. Primary assigns a serial order to mutations for that chunk and applies the mutation.
5. Primary forwards the ordered request to all secondaries.
6. Secondaries apply in that same order and acknowledge primary.
7. The primary reports success if every current replica completes the write. If only some apply it, the region may be inconsistent. The client retries. Version checks and checksums address stale replicas and corrupt blocks respectively. They do not make a failed write appear atomic.

The lease gives one authority for mutation order without sending file data through the master.

### Atomic record append

For concurrent producers, client supplies data but not the final offset. The primary selects an offset, pads/redirects if the record does not fit, orders the append, and makes secondaries use the same location. A successful record append puts the complete record at the same chosen offset on the current replicas at least once. Retries can leave duplicate records, fragments, or padding outside that successful region. Use a record identifier and a checksum or framing if the reader must detect duplicates and reject invalid entries.

### Consistency

A **consistent** region presents the same data to readers regardless of which valid replica serves it. A **defined** region is consistent and contains the complete result of a particular mutation.

| Outcome | What a reader can rely on |
|---|---|
| Successful serial write | The affected region is defined and consistent. |
| Concurrent successful writes | Replicas apply the same order per chunk. Interleaved writes, especially across multiple chunks, can leave a consistent mixture of writes. |
| Failed write | The affected region may differ between replicas and can be undefined. |
| Successful record append | The record’s successful interval is defined. Regions between successful records may contain padding or remnants of failed attempts and may be inconsistent. |

[Original GFS paper, §§2.7 and 3.1–3.3](https://research.google/pubs/the-google-file-system/)

The master uses chunk-version numbers to identify stale replicas. Checksums detect corruption. Re-replication restores desired replica count after failures.

### Master operations and fault tolerance

- Heartbeats collect chunkserver state and send commands.
- Re-replication prioritizes under-replicated/lost chunks.
- Rebalancing distributes storage and load.
- Garbage collection removes orphaned chunks lazily.
- Shadow masters/log replicas improve read availability and recovery, but the original single-master authority simplifies metadata decisions.

## Why replicate files?

Copies on different servers improve availability after failure and let clients read from nearby or less busy servers. They also preserve data if a copy is lost. A replication protocol must specify who orders writes, how it handles a stale copy, and when it considers an update durable. A client cache reduces repeated requests from that client. A server replica can continue serving other clients when the original server fails.

The **Andrew File System (AFS)** is another distributed file system. Its early design cached whole files at the client and used server callbacks to invalidate cached copies. This is a concrete example of callback-based cache consistency. [Original AFS design paper](https://www.andrew.cmu.edu/course/15-440/assets/READINGS/howard1988-tocs.pdf)

## NFS vs GFS

| Dimension | NFS | GFS |
|---|---|---|
| Primary goal | Remote general-purpose UNIX-like files | Huge data-intensive internal workloads |
| Client interface | File RPCs/VFS integration | Specialized client library/API |
| Typical file | General files | Very large streaming/append files |
| Metadata | Distributed server file systems; per-server namespace | Central master metadata |
| Data path | Client to NFS server | Client directly to chunkservers |
| Block/chunk | Conventional FS blocks | Large 64 MB chunks originally |
| Consistency | Cache validation/close-to-open style; locking for sharing | Relaxed mutation semantics and atomic record append |
| Fault strategy | RPC retry, server recovery, protocol state rules | Replication, checksums, versioning, re-replication |

## Course material

- [GFS design constraints in the introduction, pp. 37–39](../01_Fundamental_Concepts/sources/01_Introduction-DC.pdf#page=37)
- [NFSv3 specification](https://www.rfc-editor.org/rfc/rfc1813) and [NFSv4.0 specification](https://www.rfc-editor.org/rfc/rfc7530)
- [The original Google File System paper](https://research.google/pubs/the-google-file-system/)
