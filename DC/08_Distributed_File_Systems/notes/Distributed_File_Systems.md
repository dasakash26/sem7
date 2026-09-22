# Distributed file systems

## 1. Goals and design questions

A distributed file system (DFS) lets clients access files stored on remote machines through a common naming and file-operation interface.

Design goals:

- Access and location transparency
- Naming independent of physical placement
- Scalability
- Availability and fault tolerance
- Secure authentication/authorization
- Acceptable cache consistency
- Efficient bulk and small-file access
- Replication and recovery

Key tension: strict single-machine UNIX semantics require frequent coordination; caching and replication reduce latency but make immediate visibility expensive.

## 2. Naming and transparency

Naming maps logical names to file-system objects and physical locations. A good DFS separates:

1. Human pathname
2. Stable object/file identifier
3. Current server/storage location

Location transparency means a pathname does not reveal where a file is stored. Migration transparency means the file can move without changing the name or breaking clients. Replication transparency hides multiple copies while the system preserves its chosen consistency rule.

## 3. File-sharing semantics

| Semantics | Visibility rule |
|---|---|
| UNIX semantics | A completed write is immediately visible to later reads by any process |
| Session semantics | Changes are made visible to other clients when the writer closes the file/session |
| Immutable files | File never changes after creation; updates create a new file/version |
| Transaction semantics | Operations are grouped with atomicity/isolation guarantees |

NFS caching approximates close-to-open consistency rather than guaranteeing instantaneous global UNIX visibility in all cases. Applications needing serialized shared writes should use locking or stronger coordination.

# Part A - NFS

## 4. NFS architecture

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

## 5. NFS file handles and mount

The server returns an opaque file handle that identifies an object within an exported file system. The client supplies that handle on later operations; it need not know the server's inode format.

Traditional NFS separates mount/path discovery from ordinary file RPCs. NFSv4 integrates a single server namespace and more stateful operations into the main protocol.

## 6. NFSv2/v3 stateless style

The server does not rely on an `open` session for ordinary file access. Each request contains enough information to retry, typically file handle, offset, count, and credentials.

Advantages:

- Server reboot recovery is simpler.
- Retransmission handles lost request/reply messages.

Costs:

- Requests must be idempotent or carry duplicate-request protection.
- Client caching cannot provide strict global cache consistency by protocol alone.
- Lock management historically required an additional service.

## 7. NFSv3 lookup

To resolve pathname component `x` under a directory:

1. Client already has the parent directory file handle.
2. Client sends `LOOKUP(directory-file-handle, "x")`.
3. Server checks directory permissions and name mapping.
4. On success it returns the target file handle and attributes, plus useful directory attributes.
5. Client caches the name-to-handle and attributes subject to validation/timeout rules.

A multi-component path may require one lookup RPC per uncached component. This round-trip cost motivates caching and NFSv4 `COMPOUND`.

## 8. NFS caching

Clients may cache file data, attributes, and directory/name lookups. Typical validation compares cached attributes/change information after a timeout or on open.

Benefits: fewer RPCs and lower read latency. Problems: stale data, write sharing, false sharing, and server/client failure during dirty-data writeback.

Common mechanisms:

- Attribute-cache timeouts
- Write-through or delayed write with `COMMIT`
- Close-to-open validation
- File/range locks for applications that need serialization
- Delegations in NFSv4

### Cache placement and traffic reduction

- **Client memory cache:** fastest repeated access; lost on reboot and consumes RAM.
- **Client disk cache:** survives process lifetime and can serve larger working sets, but requires stronger validation and secure invalidation.
- **Server memory cache:** shared by clients and easier to keep authoritative, but every miss still crosses the network.
- **Whole-file caching:** few RPCs and good session semantics; poor for huge files and concurrent writers.
- **Block/chunk caching:** fetches only touched regions; needs per-block metadata and validation.

Use read-ahead, write-behind/batching, attribute/name caching, larger transfers, and `COMPOUND` operations to reduce traffic. These change when data crosses the network; they do not eliminate the need for a consistency policy.

### Cache consistency and modification notification

Three standard approaches:

1. **Client polling/validation:** after a timeout or at open, compare server attributes/version/change counter. Simple and robust but permits bounded staleness and creates validation RPCs.
2. **Server callbacks/invalidation:** server records clients caching an object and sends `INVALIDATE`/recall after a conflicting modification. Low read traffic, but server must recover callback state and handle unreachable clients.
3. **Leases/delegations:** callback rights expire unless renewed. A conflicting request triggers recall; expiry bounds damage if client/server communication fails.

For an exam answer on file-modification notification: identify the cached object/version, show server tracking of cache holders, send invalidation or delegation recall on write/open conflict, require acknowledgement or lease expiry before conflicting access proceeds, and make the client discard or revalidate stale blocks/attributes.

## 9. NFSv4

NFSv4 is stateful compared with v3 and integrates locking, stronger security negotiation, leases, a unified namespace, `OPEN`, and callbacks.

### Compound procedure

A client places several ordered operations in one RPC, such as:

```text
PUTROOTFH -> LOOKUP("dir") -> LOOKUP("file") -> OPEN -> READ
```

The server executes in order and stops when an operation fails. `COMPOUND` reduces network round trips; it is not automatically a database transaction or an all-or-nothing group.

### Open delegation

The server may delegate read or write responsibility for a file to a client:

- **Read delegation:** client can serve compatible opens/reads locally while no conflicting writer exists.
- **Write delegation:** client can handle opens, writes, and compatible locks locally while exclusive delegation remains.

If another client requests conflicting access, the server recalls the delegation through a callback. The client flushes/returns state. Delegations reduce open/close and validation traffic but require lease/callback and recovery handling.

## 10. NFS failure behavior

- A lost idempotent RPC can be retransmitted.
- A stale file handle indicates the object/export identity is no longer valid.
- Client cache may temporarily serve stale data under loose validation.
- Stateful NFSv4 uses lease/reclaim rules after failures.
- Exactly-once execution is not obtained merely by using RPC; duplicate suppression and operation design matter.

# Part B - Google File System

## 11. GFS workload assumptions

The original GFS design assumes:

- Commodity failures are routine.
- Files are very large, often multi-GB.
- Workloads favor large streaming reads and large sequential writes/appends.
- Files are often write-once/read-many or append-heavy.
- High sustained aggregate bandwidth matters more than minimum individual-operation latency.
- Applications can use specialized semantics such as atomic record append.

These assumptions justify design choices that would be poor for a general desktop file system.

## 12. GFS architecture

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

## 13. Chunks and metadata

Files are split into fixed-size chunks, 64 MB in the original design. Each chunk has a unique handle and is replicated, normally three copies.

The master maintains:

- Namespace and access-control information
- File-to-chunk mapping
- Chunk version numbers
- Replica locations
- Lease/primary information

Metadata is kept in memory for speed. Namespace and file-to-chunk mapping are durably represented by an operation log/checkpoints replicated on multiple machines. Chunk locations are learned from chunkservers at startup rather than relied on as permanently logged truth.

Large chunks reduce metadata and client-master interaction, allow long sequential transfers, and reduce connection setup. Costs include internal fragmentation for small files and potential hot spots when many clients access one small set of chunks.

## 14. GFS read

1. Client translates file offset to chunk index.
2. Client asks master for the chunk handle and replica locations; it caches the answer.
3. Client selects a nearby replica.
4. Client sends `(chunk-handle, byte-range)` directly to that chunkserver.
5. Chunkserver returns data and verifies checksums.

The master is not involved in the bulk transfer.

## 15. GFS write/mutation

The master grants one replica a lease, making it **primary**; other replicas are secondaries.

1. Client asks master for primary and secondary locations.
2. Client pushes data to all replicas, commonly through a pipeline; data flow is separated from control/order.
3. After replicas acknowledge buffering, client sends the write request to the primary.
4. Primary assigns a serial order to mutations for that chunk and applies the mutation.
5. Primary forwards the ordered request to all secondaries.
6. Secondaries apply in that same order and acknowledge primary.
7. Primary replies to client. Errors may leave replicas inconsistent; client retries, and version/checksum/repair mechanisms identify stale or corrupt copies.

The lease gives one authority for mutation order without sending file data through the master.

## 16. Atomic record append

For concurrent producers, client supplies data but not the final offset. The primary selects an offset, pads/redirects if the record does not fit, orders the append, and makes secondaries use the same location. GFS guarantees at-least-once style record append behavior: a successfully appended record is atomic, but retries can create duplicates, so consumers should tolerate identifiers/padding/duplicates.

## 17. Consistency

GFS distinguishes mutation success and concurrency:

- After a successful serial write, the region is defined and consistent.
- Successful concurrent writes can leave a region consistent in ordering across replicas but undefined in which writer's bytes occupy subranges.
- Record append provides a defined atomic append region but may include duplicate records after retry.

The master uses chunk-version numbers to identify stale replicas. Checksums detect corruption. Re-replication restores desired replica count after failures.

## 18. Master operations and fault tolerance

- Heartbeats collect chunkserver state and send commands.
- Re-replication prioritizes under-replicated/lost chunks.
- Rebalancing distributes storage and load.
- Garbage collection removes orphaned chunks lazily.
- Shadow masters/log replicas improve read availability and recovery, but the original single-master authority simplifies metadata decisions.

## 19. NFS vs GFS

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

## 20. Common mistakes

- Saying NFS client caches always provide strict UNIX semantics.
- Saying NFSv4 `COMPOUND` is an atomic transaction.
- Sending GFS file data through the master in the architecture diagram.
- Saying the master permanently stores every chunk location on disk.
- Forgetting that the primary orders mutations but the client pipelines data to replicas.
- Claiming atomic record append can never duplicate a record.
- Listing replication without explaining stale-replica versions and repair.

## Supplied source

The GFS motivation appears in [Introduction to Distributed Computing](../../01_Fundamental_Concepts/sources/01_Introduction-DC.pdf). Detailed NFS/GFS material is supplementary because no dedicated source PDF was supplied.
