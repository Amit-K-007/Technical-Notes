# Common Database Concepts

<br>

## Index
- [Write Ahead Log](#write-ahead-log)

<br>

**1. What is WAL?**
- WAL is a logging mechanism used to provide durability and crash recovery.
- Before a database change is persisted to data files, the change is first written to a log file.
- During recovery, the database replays the log to restore committed changes.
- WAL is often called a Redo Log because it is used to redo committed operations after a crash.

**2. Why is WAL Needed?**
- Database updates are first performed in the buffer pool (RAM) for better performance.
- Modified pages may remain in memory and are not immediately written to disk.
- A crash before page flushing would otherwise result in loss of committed transactions.
- WAL ensures recovery information is safely persisted before data pages are written.

**3. WAL Rule and Commit Process**
- The log record must be persisted before the corresponding data page is written to disk.
- When a transaction modifies data, a WAL record describing the change is generated.
- WAL records are flushed to durable storage (fsync) before the commit succeeds.
- Actual data pages can be written later by background processes.

**4. Why WAL Improves Performance**
- Writing complete data pages on every update would result in expensive random disk I/O.
- WAL records are append-only, producing mostly sequential writes.
- Sequential writes are significantly faster than random page writes.
- Data page flushing can be delayed and batched for efficiency.

**5. Redo Recovery**
- After a crash, committed changes may exist in WAL but not in the data files.
- During startup, the database scans the WAL and re-applies missing changes.
- This process is called redo recovery.
- Redo recovery guarantees durability of committed transactions.

**6. Checkpointing**
- WAL cannot grow indefinitely, so databases periodically create checkpoints.
- During a checkpoint, dirty pages are flushed from memory to data files.
- Once pages are safely persisted, older WAL records can be recycled.
- Checkpoints reduce both WAL size and recovery time.

<br>
