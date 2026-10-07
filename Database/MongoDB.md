# MongoDB Revision Notes

> Legend: ✅ notes ready, ⏳ pending (placeholder, to be filled as we discuss it)

## Index

- [Sessions](#sessions) ✅
- [Transactions](#transactions) ✅
- [MongoDB Transactions vs SQL Transactions](#mongodb-transactions-vs-sql-transactions) ✅
- [Causal Consistency](#causal-consistency) ✅
- [Read Concern and Write Concern](#read-concern-and-write-concern) ✅
- [Replica Sets](#replica-sets) ✅
- [Oplog, WAL and Journal](#oplog-wal-and-journal) ✅
- [Sharding](#sharding) ✅
- [Two-Phase Commit in Sharded Transactions](#two-phase-commit-in-sharded-transactions) ✅
- [Outbox Pattern](#outbox-pattern) ✅
- [Indexes and Query Performance](#indexes-and-query-performance) ⏳
- [Data Modeling](#data-modeling) ⏳
- [Aggregation Framework](#aggregation-framework) ⏳
- [Replication Internals](#replication-internals) ⏳
- [Shard Key Design and Chunk Management](#shard-key-design-and-chunk-management) ⏳
- [WiredTiger Storage Engine](#wiredtiger-storage-engine) ⏳
- [Change Streams](#change-streams) ⏳
- [Mongoose Essentials](#mongoose-essentials) ⏳
- [Core Fundamentals](#core-fundamentals) ⏳

<br>

---

### Sessions

#### What it is

- A session (`ClientSession`) is a logical grouping of operations sent from the **client** (your driver, i.e. the backend app) to the **server** (`mongod` / Atlas).
- Identified by a **logical session ID** (`lsid`) attached to every command.
- It is **not a connection**. It is an ID plus some state. Many sessions share one connection pool.
- One session should run **one transaction at a time** and should not run operations in parallel.

#### What a session carries

- `lsid`: identifies the session on the server.
- `txnNumber` and transaction state: used by transactions and retryable writes.
- `operationTime` / `clusterTime`: used for causal consistency.

#### Implicit vs explicit sessions

| | Implicit | Explicit |
|---|---|---|
| Created by | Driver, automatically | You, via `startSession()` |
| Lifetime | One operation | Until `endSession()` |
| Causal consistency across operations | No | Yes (default `true`) |
| Transactions | No | Yes |
| Retryable writes | Yes | Yes |

- Since MongoDB 3.6, **every** operation runs in a session.
- Implicit sessions are drawn from a driver-side **server session pool**, so they are cheap.
- Idle sessions expire on the server after about 30 minutes.

#### Why implicit sessions exist

- **Retryable writes**: `lsid` + `txnNumber` lets the server detect a retried write that was already applied.
- **Tracking and cleanup**: the server can identify, expire and kill operations by session.
- **Uniform protocol**: every command carries an `lsid`.

#### Code

```js
const session = await mongoose.startSession();
try {
  // pass { session } to every operation that belongs together
  await Order.updateOne({ _id }, { status: 'paid' }, { session });
} finally {
  await session.endSession();
}
```

#### Interview Qs

- **Is a session a connection?** No. It is an ID + state; sessions are multiplexed over the connection pool.
- **Do normal operations use sessions?** Yes, implicit single-use ones. Only explicit sessions carry state across operations.
- **What breaks if I forget to pass `session`?** The operation silently runs outside the transaction / causal chain.

---

### Transactions

#### Atomicity levels

- **Single document** (including embedded docs and arrays): always atomic, no transaction needed.
- **Multi-document ACID transactions**: 4.0 on replica sets, 4.2 on sharded clusters.
- Needs a **replica set** (even single-node) or sharded cluster. A standalone `mongod` does not support them.

#### How it works

- `startTransaction()` sets a **snapshot** read point (`readConcern: "snapshot"`).
- Every operation must receive the same `session` to join the transaction.
- Writes are held in the transaction's own view; document-level write locks are taken as you go.
- A conflicting writer gets a **WriteConflict** error immediately (fail-fast, no waiting, no deadlocks).
- `commitTransaction()` applies everything atomically with the configured `writeConcern`.
- `abortTransaction()` discards everything.

#### Limits and behaviour

- Default lifetime: **60 seconds** (`transactionLifetimeLimitSeconds`).
- Isolation: effectively **snapshot isolation**.
- Oplog: before 4.2 a transaction had to fit in one 16MB oplog entry; 4.2+ splits large ones across multiple entries.
- Creating collections inside a transaction: allowed from **4.4**.
- `readConcern` and `writeConcern` are set **on the transaction**, not per operation.
- Cost is higher than normal writes, especially across shards.

#### Mongoose: manual control

```js
const session = await mongoose.startSession();
session.startTransaction({
  readConcern: { level: 'snapshot' },
  writeConcern: { w: 'majority' },
});

try {
  await Account.updateOne({ _id: from }, { $inc: { balance: -100 } }, { session });
  await Account.updateOne({ _id: to },   { $inc: { balance:  100 } }, { session });
  await session.commitTransaction();
} catch (err) {
  await session.abortTransaction();
  throw err;
} finally {
  session.endSession();
}
```

#### Mongoose: `withTransaction` (preferred)

```js
const session = await mongoose.startSession();
try {
  await session.withTransaction(async () => {
    await Account.updateOne({ _id: from }, { $inc: { balance: -100 } }, { session });
    await Account.updateOne({ _id: to },   { $inc: { balance:  100 } }, { session });
  });
} finally {
  await session.endSession();
}
```

- Automatically retries the callback on `TransientTransactionError`.
- Automatically retries the commit on `UnknownTransactionCommitResult`.
- So the callback must be **idempotent** with **no external side effects** (no emails, no SQS publishes).
- Mongoose also has `connection.transaction(fn)`, which wraps this and resets document state on retry.

#### Gotchas

- Forgetting `{ session }` on an operation: it runs outside the transaction, no error. The #1 bug.
- `save()`: use `doc.$session(session)`.
- `create()` with options needs the **array form**: `Model.create([doc], { session })`.
- Queries: `Model.find().session(session)`.
- No `Promise.all` inside one transaction; run operations sequentially.
- Make sure collections and indexes exist beforehand on older server versions (`Model.init()` / `createCollection()`).

#### Practical guidance

- **Model to avoid transactions**: embed data that changes together in one document.
- Use transactions only for real cross-document invariants (transfers, inventory + order, ledger).
- Keep them short, touching few documents.
- Use `w: "majority"` and `readConcern: "snapshot"`.
- Do side effects **after** commit, or use the [outbox pattern](#outbox-pattern).

#### Interview Qs

- **Why does a standalone `mongod` not support transactions?** Transactions rely on the oplog and replication machinery, which only exists on replica set members.
- **What does `withTransaction` retry, and what does that imply?** Transient errors and unknown commit results; the callback must be idempotent.
- **What happens on a write conflict?** The second transaction fails immediately with `WriteConflict` and is retried.

---

### MongoDB Transactions vs SQL Transactions

| Aspect | SQL (e.g. PostgreSQL) | MongoDB |
|---|---|---|
| Default scope | Every statement is in a transaction (autocommit) | Single-document ops atomic; multi-doc is opt-in |
| Typical need | Always, data is normalized across tables | Often avoidable via embedding |
| Context object | Connection / `BEGIN` | Explicit `ClientSession` passed to each op |
| Isolation levels | Read Uncommitted, Read Committed, Repeatable Read, Serializable | Snapshot isolation + read/write concern tuning |
| Conflicts | Row locks, blocking (depending on MVCC and level) | Fail-fast `WriteConflict`, retry |
| Duration | Can be long-lived | Short by design (60s default) |
| Cost | Cheap, core feature | More overhead, especially cross-shard |
| Deadlocks | Possible, detected by DB | Avoided via fail-fast conflicts |
| Retries | Application-level | Built into driver (`withTransaction`) |
| Constraints | Foreign keys, CHECK enforced | No FKs; app or schema validation |
| Savepoints | Supported | Not supported |
| DDL | Often transactional (Postgres) | Limited; mostly avoid |

#### One-liner

- SQL: transactions are the default, schema is normalized. MongoDB: atomic documents are the default, transactions are the exception.

---

### Causal Consistency

*(Spelled **causal**, not "casual": about cause and effect.)*

#### Problem it solves

- Replica set with reads from **secondaries**. Replication lag means a secondary may not yet have your write.
- Without it: you post a comment, reload, and your comment is missing.

#### Guarantees

| Guarantee | Prevents |
|---|---|
| Read your writes | Updating a profile, then seeing the old value |
| Monotonic reads | Seeing 10 comments, then 8 on the next read |
| Monotonic writes | `x=1` then `x=2` applied as 2 then 1 |
| Writes follow reads | A write based on a read being ordered before that read |

#### How it works

- Server returns `operationTime` (e.g. `T=100`) for a write.
- The session remembers it and attaches `afterClusterTime: T=100` to the next read.
- A secondary at `T=95` **waits** until it reaches `T=100`, then answers.
- The read is slightly slower but correct.

#### Code

```js
const session = await mongoose.startSession({ causalConsistency: true }); // default for explicit sessions

await Comment.create([{ text: 'Nice post!' }], { session });

const comments = await Comment.find({})
  .read('secondary')
  .session(session); // the session carries the timestamp
```

#### Key points

- **Only exists inside a session.** No session (or a different one) means no guarantee.
- Per session, not global: two users are not causally linked.
- Needed only when reads can hit different nodes. Reading from the primary gives it for free.
- For full guarantees use `readConcern: "majority"` and `writeConcern: "majority"`.
- Different from transactions: it is about **ordering/visibility**, transactions are about **atomicity**.
- Across app servers: carry timestamps with `session.advanceOperationTime()` / `advanceClusterTime()`, or just read "I just wrote this" data from the primary.

#### Interview Qs

- **What does causal consistency add over normal secondary reads?** Reads wait until the node has caught up to the session's last operation time.
- **Do implicit sessions give it?** No, they live for one operation only.

---

### Read Concern and Write Concern

#### Write concern: how many acknowledgements before success

- `w: 1`: primary only. Fast, but can be lost if the primary fails before replicating.
- `w: "majority"`: majority of voting members have it. Survives failover. The safe choice.
- `j: true`: also flushed to the on-disk **journal**.
- `wtimeout`: stop waiting after N ms (the write may still succeed).

#### Read concern: what kind of data a read may return

| Level | Meaning |
|---|---|
| `local` | Whatever this node has; may later be rolled back |
| `majority` | Only data acknowledged by a majority; won't be rolled back |
| `snapshot` | Consistent point-in-time view (used in transactions) |
| `linearizable` | Reflects all majority-acknowledged writes before the read; primary only, slow |

#### Key points

- In a transaction, set both on `startTransaction({ readConcern, writeConcern })`.
- `majority` read + `majority` write is the combination that protects against **rollback** after failover.
- Read **preference** (which node) is a separate setting from read **concern** (what data).

#### Interview Qs

- **`w: 1` vs `w: "majority"`?** Speed vs durability across failover.
- **Read concern vs read preference?** Concern = data guarantee; preference = which member serves the read.

---

### Replica Sets

#### What it is

- A group of `mongod` servers holding copies of the same data.
- One **primary** takes all writes; **secondaries** copy changes from the primary's [oplog](#oplog-wal-and-journal).
- If the primary fails, the remaining members hold an **election** and promote a new primary.
- Gives **high availability** and **durability**.

#### Single-node replica set

- One `mongod` started with a replica set name and initialised with `rs.initiate()`.
- No redundancy, but enables the oplog-based features: **transactions** and **change streams**.
- The usual local development setup. Atlas clusters are always replica sets.

```js
// in mongosh, after starting mongod with --replSet rs0
rs.initiate()
```

#### Key points

- Secondaries may lag (replication lag), which is why [causal consistency](#causal-consistency) exists.
- Writes always go to the primary; reads can go to secondaries via read preference.
- Elections, priorities, arbiters, hidden/delayed members and rollback are covered in [Replication Internals](#replication-internals).

#### Interview Qs

- **Why use a replica set?** Failover, durability and read scaling.
- **Why a single-node replica set in dev?** To enable transactions and change streams.

---

### Oplog, WAL and Journal

#### Oplog

- A special **capped collection**, `local.oplog.rs`, recording every write applied to a replica set member.
- Maintained automatically by `mongod`. Your app never writes to it.
- **Primary** appends an entry for every write (data change and oplog entry are written atomically).
- **Secondaries** pull entries from the primary (or another secondary, "chained replication") and apply them.
- Does not exist on a standalone `mongod`.

#### Entry shape

```js
{
  ts: Timestamp(1728300000, 1),   // time + counter, gives ordering
  op: "u",                        // i=insert, u=update, d=delete, c=command, n=no-op
  ns: "shop.orders",              // db.collection
  o:  { $v: 2, diff: { u: { status: "paid" } } },
  o2: { _id: ObjectId("...") }
}
```

#### Key properties

- **Capped**: fixed size; oldest entries are overwritten. Default is about 5% of free disk (with min/max bounds). Resize with `replSetResizeOplog`.
- **Idempotent entries**: `$inc` is stored as the resulting value, so replaying is safe.
- **Oplog window**: time span between oldest and newest entry. A secondary offline longer than this needs a **full resync**. Check with `rs.printReplicationInfo()`.
- Big bulk writes shrink the window quickly.

#### What depends on it

- Replication, elections and rollbacks.
- Transactions (commit is written as one or a few linked oplog entries).
- Change streams.
- Point-in-time backup and recovery.

#### Oplog vs WAL

| | WAL (e.g. PostgreSQL) | MongoDB oplog |
|---|---|---|
| Level | Physical (pages/bytes) | Logical (document operations) |
| Purpose | Crash recovery on one node | Replication between nodes |
| Storage | Segment files | Regular capped collection you can query |
| Retention | Removed when no longer needed | Kept while space allows (oplog window) |

#### Journal (the real WAL equivalent)

- WiredTiger has its own write-ahead log, the **journal**, for single-node crash recovery and durability.
- `j: true` in write concern means "wait for the journal flush".
- A write effectively produces both: a **journal** record (local durability) and an **oplog** entry (replication).
- PostgreSQL uses one WAL for both jobs; MongoDB splits them. Closest analogy to the oplog is MySQL's **binlog**.

#### Interview Qs

- **Is the oplog a WAL?** WAL-like for replication, but the real WAL is the WiredTiger journal.
- **What happens if a secondary falls behind the oplog window?** It cannot catch up and needs a full resync.

---

### Sharding

#### What it is

- Splitting a collection across multiple machines (**horizontal scaling**) so no single server holds or serves everything.
- A **shard** is one slice of the data, and in MongoDB each shard is itself a **replica set**.

#### Components

- **Shards**: replica sets storing the data.
- **mongos**: router your app connects to; sends queries to the right shard(s) and merges results.
- **Config servers**: replica set storing metadata of which chunks live where.

#### How data is placed

- You pick a **shard key** (field or fields, e.g. `customerId`).
- Key ranges are split into **chunks**, and chunks are assigned to shards.
- A background **balancer** migrates chunks to keep shards even.

#### Ranged vs hashed

| | Ranged | Hashed |
|---|---|---|
| Distribution | Contiguous key ranges per shard | Hash of key spreads evenly |
| Range queries | Efficient | Hit many shards |
| Risk | Hotspots on monotonic keys (timestamp, ObjectId) | Poor range query performance |

#### Query routing

- **Targeted**: query includes the shard key, so `mongos` hits one shard.
- **Scatter-gather**: no shard key, so every shard is queried. Slower as shards grow.

#### Key points

- Shard key choice is one of the most important design decisions and is painful to change.
- Bad keys: low cardinality, monotonically increasing.
- Related documents sharing a shard key value stay on one shard, so transactions avoid [2PC](#two-phase-commit-in-sharded-transactions).
- Most apps do **not** need sharding; a single replica set (e.g. Atlas) scales far.
- Deeper design topics: [Shard Key Design and Chunk Management](#shard-key-design-and-chunk-management).

#### Interview Qs

- **What is a shard?** A replica set holding a subset of the data.
- **Targeted vs scatter-gather?** Whether the query includes the shard key.
- **Why is `createdAt` a poor shard key?** Monotonic, so all new writes hit one shard.

---

### Two-Phase Commit in Sharded Transactions

#### When 2PC is used

| Transaction type | Commit mechanism |
|---|---|
| Single document | Plain atomic write |
| Multi-doc, one replica set | Single atomic oplog write, no 2PC |
| Multi-doc, one shard | Same as above, router skips 2PC |
| Multi-doc, **multiple shards** | **2PC**, coordinated by a participating shard |

- Used internally inside `commitTransaction()`. You never call it yourself.

#### Flow (4.2+)

1. Operations go through `mongos`, which tracks the shards touched.
2. On commit, one participant shard becomes the **coordinator**.
3. **Prepare phase**: each participant durably records a "prepared" state, keeps its locks and returns a `prepareTimestamp`. Any failure aborts everything.
4. **Decision**: coordinator picks `commitTimestamp` (max of prepare timestamps) and durably records it (`config.transaction_coordinators`).
5. **Commit phase**: coordinator tells participants to commit at that timestamp; locks are released.

#### Failure handling

- Classic 2PC weakness: coordinator crash between phases.
- Mitigated because prepared state and the decision are **replicated to a majority**; a newly elected primary reads the state and finishes the job.

#### Consequences

- Extra latency (more round trips and durable writes).
- Locks held longer on prepared documents.
- Design shard keys so related writes stay on one shard.
- A bulk write or `updateMany` across shards is **not** atomic outside a transaction.
- No 2PC with external systems (SQS, other databases); use the [outbox pattern](#outbox-pattern).

#### Interview Qs

- **Does MongoDB use 2PC?** Yes, only for cross-shard transactions, hidden behind the same API.
- **How does it survive coordinator failure?** Decision and prepared state are majority-replicated; the new primary completes the commit.

---

### Outbox Pattern

#### Problem

- You must update the DB **and** publish a message (e.g. SQS).
- Publish inside the transaction: commit may fail, message already sent.
- Publish after commit: process may crash in between, message lost.

#### Solution

- In **one transaction**, write the business data and an **outbox** document.
- A separate **relay** publishes pending outbox entries and marks them sent.

#### Code

```js
const OutboxSchema = new mongoose.Schema({
  topic: String,
  payload: Object,
  status: { type: String, enum: ['pending', 'sent'], default: 'pending', index: true },
  createdAt: { type: Date, default: Date.now },
});
const Outbox = mongoose.model('Outbox', OutboxSchema);

// 1. Business write + outbox write in ONE transaction
async function createOrder(data) {
  const session = await mongoose.startSession();
  try {
    let order;
    await session.withTransaction(async () => {
      [order] = await Order.create([data], { session });
      await Outbox.create(
        [{ topic: 'order.created', payload: { orderId: order._id } }],
        { session }
      );
    });
    return order;
  } finally {
    await session.endSession();
  }
}

// 2. Relay worker (separate process)
async function relayOutbox() {
  const batch = await Outbox.find({ status: 'pending' }).sort({ createdAt: 1 }).limit(50);
  for (const msg of batch) {
    await sqs.sendMessage({
      QueueUrl: QUEUE_URL,
      MessageBody: JSON.stringify({ id: msg._id, ...msg.payload }),
    }).promise();
    await Outbox.updateOne({ _id: msg._id }, { status: 'sent' });
  }
}
```

#### Key points

- Delivery is **at-least-once**; consumers must be **idempotent** (dedupe on outbox `_id`).
- With multiple relay workers, **claim** rows atomically (`findOneAndUpdate` from `pending` to `processing`).
- Clean up old `sent` entries with a TTL index or job.
- The relay can use a **change stream** instead of polling.

#### Interview Qs

- **Why not publish inside the transaction?** The commit can still fail or be retried by `withTransaction`.
- **Exactly-once?** No; at-least-once plus idempotent consumers.

---

### Indexes and Query Performance

_Notes to be added as we discuss this topic._

#### To cover

- B-tree basics; single, compound, multikey, partial, sparse, TTL, text, unique indexes
- ESR rule (Equality, Sort, Range) for compound index order
- `explain("executionStats")`: IXSCAN vs COLLSCAN, `totalDocsExamined` vs `nReturned`
- Covered queries, index intersection, profiler and slow query log
- Index cost on writes; building indexes on live collections

---

### Data Modeling

_Notes to be added as we discuss this topic._

#### To cover

- Embed vs reference; one-to-few, one-to-many, one-to-squillions
- 16MB document limit, unbounded array growth
- Patterns: bucket, subset, extended reference, computed, polymorphic, schema versioning
- Denormalization and keeping copies consistent

---

### Aggregation Framework

_Notes to be added as we discuss this topic._

#### To cover

- Stages: `$match`, `$group`, `$project`, `$lookup`, `$unwind`, `$sort`, `$facet`, `$addFields`
- Stage ordering and index use; memory limits and `allowDiskUse`
- `$lookup` vs application-side joins; cost on sharded collections

---

### Replication Internals

_Notes to be added as we discuss this topic._

#### To cover

- Election mechanics (Raft-like), priority, votes, arbiters, hidden/delayed members
- Rollback and replication lag
- Read preference modes: `primary`, `primaryPreferred`, `secondary`, `nearest`
- Failover behaviour from the app's perspective; retryable reads/writes

---

### Shard Key Design and Chunk Management

_Notes to be added as we discuss this topic._

#### To cover

- Cardinality, frequency, monotonic change, hotspots
- Chunk splitting and migration, jumbo chunks
- Zone sharding, resharding (5.0+)
- Which queries and operations get expensive across shards

---

### WiredTiger Storage Engine

_Notes to be added as we discuss this topic._

#### To cover

- Document-level concurrency and MVCC
- Cache, checkpoints and journal, and how `j: true` relates
- Compression
- How snapshot isolation falls out of the engine design

---

### Change Streams

_Notes to be added as we discuss this topic._

#### To cover

- Built on the oplog; requires a replica set
- Resume tokens and failure recovery
- Use cases: event-driven sync, outbox relay

---

### Mongoose Essentials

_Notes to be added as we discuss this topic._

#### To cover

- Schemas, validation, middleware/hooks, virtuals
- `populate` vs `$lookup`
- `lean()`, discriminators
- `versionKey` and optimistic concurrency
- Connection pooling and `bufferCommands`

---

### Core Fundamentals

_Notes to be added as we discuss this topic._

#### To cover

- ObjectId structure, BSON types
- Atomic update operators: `$set`, `$inc`, `$push`, `$addToSet`
- Pagination: skip/limit vs range-based
- `bulkWrite`, TTL and capped collections
- Schema validation (`$jsonSchema`)
- CAP theorem positioning, basic auth/RBAC, backups and Atlas basics
