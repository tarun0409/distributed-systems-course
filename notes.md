# Distributed Systems — Study Notes

## What Is a Distributed System?
A **distributed system** is a software system whose components run on multiple networked computers.  
These components **communicate and coordinate** by exchanging messages, working together to appear as a single system to the user.

### Key Characteristics
- Components run on **different machines**
- Operate through **message passing**
- Work together to achieve **common goals**
- Often transparent to users (behave like one unified system)

---

## Benefits of Distributed Systems

### 1. Performance
Distributed systems can parallelize tasks across multiple nodes.  
This reduces latency for large computations and allows heavy workloads to be split efficiently.

### 2. Scalability
You can horizontally scale by **adding more machines**, enabling the system to handle:
- More users  
- Higher data volume  
- Larger computational tasks  

### 3. Availability
Because components are replicated across machines, the system continues operating even when some nodes fail.

> **Note:**  
> “Five-nines availability” (99.999%) means the system can be down for **~5 minutes per year**.  
This is a common target for mission-critical systems.

| Availability Level | Percentage | Maximum Downtime/Year |
|-------------------|------------|------------------------|
| 3-nines           | 99.9%      | ~8.76 hours            |
| 4-nines           | 99.99%     | ~52 minutes            |
| 5-nines           | 99.999%    | ~5 minutes             |

---

## Fallacies of Distributed Computing
These are **common but false assumptions** developers make when designing distributed systems.  
Believing these leads to bugs, failures, and incorrect system architecture.

```mermaid
flowchart TD
    A(Fallacies of Distributed Computing)
    A --> B[1. The network is reliable]
    A --> C[2. Latency is zero]
    A --> D[3. Bandwidth is infinite]
    A --> E[4. The network is secure]
    A --> F[5. Topology does not change]
    A --> G[6. There is one administrator]
    A --> H[7. Transport cost is zero]
    A --> I[8. The network is homogeneous]
```

# Challenges and Core Properties of Distributed Systems

## Why Distributed Systems Are Hard
Distributed systems face complexity due to three fundamental realities:

### 1. Network Asynchrony
Messages may arrive late, out of order, or not at all.  
There is **no global clock**, and components cannot reliably know each other’s timing.

### 2. Partial Failures
In a distributed setup, a component may fail **independently** while others keep running.  
This makes failure detection and recovery extremely difficult.

### 3. Concurrency
Multiple nodes execute simultaneously.  
Their interactions can lead to race conditions, inconsistent states, and coordination challenges.

> **Callout — Key Insight**  
> In distributed systems, **anything that can go wrong will go wrong independently**, and the system must still continue to function.

---

## System Correctness

Correctness in distributed computing is defined using two dimensions:

### **Safety**
A safety property states that **something bad should never happen**.  
Examples:
- No two nodes elect themselves as the leader
- Money should never be deducted twice
- A message should never be delivered in the wrong order

Safety violations are **catastrophic**.

### **Liveness**
A liveness property states that **something good must eventually happen**.  
Examples:
- A leader must eventually be elected
- A client request must eventually receive a response
- A message must eventually be delivered

Liveness violations indicate **stuck or deadlocked systems**.

---

## Properties of Distributed Systems
Every distributed system must define:

### 1. How Nodes Interact
- Message passing protocols  
- Ordering guarantees  
- Synchronization mechanisms  

### 2. How Nodes Can Fail
Failure modes determine what guarantees the system must provide.

---

# Categories of Distributed Systems

## Synchronous Systems
Assume:
- Fixed upper bounds on message delay  
- Known execution speed  
- Reliable clocks  

> Rare in real-world systems; used mostly for theoretical guarantees.

## Asynchronous Systems
Assume:
- **No timing guarantees**  
- Messages may be delayed indefinitely  
- Failures may not be detectable  

> **Real distributed systems are asynchronous**.

---

# Failure Models in Distributed Systems

| Failure Type | Description | Detectable? | Severity |
|--------------|-------------|-------------|----------|
| **Fail-stop** | Node halts with error; others know it failed | Yes | Low |
| **Crash** | Node halts silently | No | Medium |
| **Omission** | Node fails to respond or drops messages | Partially | High |
| **Byzantine** | Node behaves arbitrarily, possibly maliciously | No | Highest |

```mermaid
flowchart TD
    A[Failure Models] --> B[Fail-stop]
    A --> C[Crash]
    A --> D[Omission]
    A --> E[Byzantine]
```
# Partitioning

Partitioning is a technique used in distributed systems to split a large dataset into smaller pieces that can be stored and processed across multiple nodes, improving scalability, performance, and availability.

---

# 1. Overview of Partitioning

Partitioning divides a dataset into smaller, independent datasets to achieve:

- Improved scalability
- Increased read/write throughput
- Distributed storage across multiple machines
- Reduced hotspots
- Improved fault isolation

Types of partitioning:
- Vertical Partitioning
- Horizontal Partitioning

---

# 2. Vertical Partitioning

Vertical partitioning splits a table by columns.

## 2.1 Definition
- A table is divided into multiple tables that contain subsets of columns.
- Frequently accessed columns can be isolated.
- Less frequently accessed or large data fields can be placed separately.
- Full record reconstruction requires JOIN operations.

## 2.2 Advantages
- Improves query performance for column-specific workloads.
- Reduces amount of data scanned.
- Improves cache efficiency.
- Helps normalization.

## 2.3 Disadvantages
- Full-record queries require joins.
- Cross-node joins cause network overhead.
- Increases query complexity.

## 2.4 Mermaid Diagram — Vertical Partitioning

```
flowchart LR
    A[Users Table] --> B["Users_Core<br/>id<br/>name<br/>email"]
    A --> C["Users_Metadata<br/>id<br/>profile_pic<br/>preferences"]
```

---

# 3. Horizontal Partitioning

Horizontal partitioning splits a table by rows, distributing subsets of rows across different partitions or shards.

## 3.1 Definition
- Each partition contains the same columns but different rows.
- Data is assigned to partitions based on a strategy such as range, hash, or consistent hashing.
- Common in large-scale distributed systems such as Cassandra, BigTable, HBase, DynamoDB.

## 3.2 Advantages
- Enables linear scalability by adding nodes.
- Reduces per-node storage and load.
- Supports massive datasets.

## 3.3 Disadvantages
- Range queries may be inefficient depending on the strategy.
- No cross-shard atomic transactions without complex coordination.
- Rebalancing is expensive for some methods.

---

# 4. Types of Horizontal Partitioning

The main strategies are:

- Range Partitioning
- Hash Partitioning
- Consistent Hashing

---

# 4.1 Range Partitioning

## 4.1.1 Definition
Rows are partitioned based on contiguous key ranges.

Example:
- Partition 1 → keys 1–10,000
- Partition 2 → keys 10,001–20,000

## 4.1.2 Used In
- Google BigTable
- Apache HBase

## 4.1.3 Advantages
- Efficient range queries.
- Natural locality for related data.
- Works well for time-series and sequential workloads.

## 4.1.4 Disadvantages
- Leads to hotspots if certain ranges receive more traffic.
- Requires manual or automated splitting and rebalancing.
- Skewed distribution when data is uneven.

## 4.1.5 Mermaid Diagram — Range Partitioning

```mermaid
flowchart LR
    A[Table Rows] --> B[Partition 1\nRange: 1–10k]
    A --> C[Partition 2\nRange: 10k–20k]
    A --> D[Partition 3\nRange: 20k–30k]
```

---

# 4.2 Hash Partitioning

## 4.2.1 Definition
Rows are assigned to partitions using a hash function:

```
partition = hash(key) % N
```

## 4.2.2 Advantages
- Uniform distribution of data.
- Avoids hotspots common in range partitioning.
- Simple implementation.

## 4.2.3 Disadvantages
- Poor performance for range queries because data is scattered.
- Adding or removing nodes requires repartitioning and moving many keys.
- Rebalancing cost increases with cluster size.

## 4.2.4 Mermaid Diagram — Hash Partitioning

```mermaid
flowchart LR
    K[hash\(key\)] --> P0[Partition 0]
    K --> P1[Partition 1]
    K --> P2[Partition 2]
```

---

# 4.3 Consistent Hashing

## 4.3.1 Definition
Consistent hashing maps nodes and data to positions on a logical hash ring.
Each key is assigned to the next clockwise node on the ring.

## 4.3.2 Advantages
- Minimal data movement during scaling.
- Supports dynamic node addition/removal.
- Virtual nodes improve load balancing.

## 4.3.3 Disadvantages
- Without virtual nodes, distribution may be uneven.
- More complex implementation than modulo hashing.

## 4.3.4 Mermaid Diagram — Consistent Hashing Ring

```mermaid
flowchart LR
    subgraph HashRing[Hash Ring]
        A((Node A))
        B((Node B))
        C((Node C))
    end

    K1((Key 1)) --> A
    K2((Key 2)) --> B
    K3((Key 3)) --> C
```

---

# 5. Summary Comparison

| Partitioning Method | Splits By | Pros | Cons | Systems Using It |
|---------------------|-----------|------|------|------------------|
| Vertical Partitioning | Columns | Good cache usage, smaller tables | Joins required for full record | SQL Databases |
| Range Partitioning | Key ranges | Efficient range queries, locality | Hotspots, skew | BigTable, HBase |
| Hash Partitioning | Hash(key) | Even distribution | Poor range queries, expensive rebalancing | Cassandra, Dynamo-like systems |
| Consistent Hashing | Hash ring | Minimal data movement, good scaling | Needs virtual nodes for balance | DynamoDB, Redis Cluster |

---

# 6. When to Use Each Method

## Vertical Partitioning
- When certain columns are accessed much more frequently.
- When optimizing query performance for column subsets.

## Range Partitioning
- When range scans or sequential access are common.
- When keys naturally follow a time- or sequence-based pattern.

## Hash Partitioning
- When uniform distribution is needed.
- When access patterns are random or unpredictable.

## Consistent Hashing
- When cluster size changes often.
- When minimizing rebalance effort is critical.
- When building scalable distributed caches or databases.

---

# Replication

Replication is a technique used in distributed systems to increase data availability and fault tolerance by maintaining multiple copies of the same data across different nodes.

---

# 1. Purpose of Replication
- Increase availability
- Improve read scalability
- Provide fault tolerance
- Reduce latency (geo-distribution)

---

# 2. Types of Replication Strategies

## 2.1 Pessimistic Replication
- All replicas are identical to each other at all times.
- Strong consistency.
- Updates propagate immediately.
- Higher write latency.

## 2.2 Optimistic Replication
- Also called lazy replication.
- Replicas eventually converge to the same state.
- Eventual consistency.
- Faster writes, lower latency.

---

# 3. Primary–Backup Replication

Primary–backup (leader–follower) model:

- A leader (primary) handles all write requests.
- Followers handle read requests.
- Leader propagates updates to followers.

### Advantages
- Scalable for read-heavy workloads.

### Disadvantages
- Not ideal for write-heavy workloads.

---

# 4. Synchronous vs Asynchronous Replication

## 4.1 Synchronous Replication
- Update acknowledgment is sent to the client **only after** all replicas have acknowledged the update.
- High durability.
- Slower writes (wait for all nodes).

## 4.2 Asynchronous Replication
- Client receives acknowledgment immediately after the local write.
- Faster writes.
- Lower consistency & durability guarantees.

---

# 5. Failover

Failover occurs when the leader node crashes.

- A follower takes over as the new leader.
- Can be manual or automatic.

---

# 6. Multi-Primary Replication

- All replicas accept write requests.
- Availability prioritized over consistency.
- Conflicting data may arise due to different execution orders on different nodes.

## 6.1 Eager Conflict Resolution
- Conflicts resolved during the write operation.

## 6.2 Lazy Conflict Resolution
- Conflicts resolved during subsequent reads.

### Approaches to Conflict Resolution
- Exposing conflict to the client.
- Last write wins (latest timestamp wins).
- Causality tracking.

---

# 7. Quorum-Based Replication

Quorum ensures a minimum number of nodes participate in read and write operations to maintain consistency.

Definitions:
- **Vr** = Read quorum size
- **Vw** = Write quorum size
- **V** = Total number of replicas

## Rules
- **Vr + Vw > V** → Ensures no simultaneous conflicting read/write of the same data item.
- **Vw > V/2** → Ensures at least one node receives both write operations and imposes an order.

---

# Consistency Models

Consistency models define the rules for how operations appear and behave in a distributed system.

---

# 1. Consistency Model (General Definition)

A **consistency model** defines the set of guarantees a distributed system provides about the visibility and ordering of read and write operations across nodes.

---

# 2. Types of Consistency Models

## 2.1 Linearizability (Strongest Practical Model)
- Operations appear **instantaneous** to external clients.
- Every operation seems to occur at a single point in time between invocation and response.
- Guarantees real-time ordering.
- Easiest for developers to reason about; hardest to scale.

---

## 2.2 Sequential Consistency
- All clients observe the **same order** of operations.
- Per-client program order is preserved.
- Does **not** guarantee real-time ordering.
- Weaker than linearizability but stronger than causal consistency.

---

## 2.3 Causal Consistency
- Operations with **causal relationships** must be seen in the same order.
- Concurrent operations (no causal link) may be seen in different orders.
- A → B must be preserved if:
  - A happens before B
  - B observes A
  - Client sees A then performs B

---

## 2.4 Eventual Consistency
- System guarantees only that replicas **eventually converge** if no new writes occur.
- Reads may not return the latest writes.
- High availability and low latency.
- Used in Dynamo-style systems, Cassandra, Riak.

---

# 3. Strong vs Weak Consistency

## 3.1 Strong Consistency Model
- Prioritizes consistency over availability.
- During network partition, system chooses **CONSISTENCY** and may reject operations.
- Follows CAP theorem’s CP behavior.
- Example: Linearizability.

## 3.2 Weak Consistency Model
- Preserves availability even during network failures.
- Allows temporary inconsistency.
- Follows CAP theorem’s AP behavior.
- Example: Eventual Consistency.

---

# Isolation Levels

Database isolation levels define how concurrently executing transactions interact with each other, and what anomalies they may or may not allow.

---

# 1. Isolation Levels

## 1.1 Serializability
- Strongest isolation level.
- Concurrent transactions must yield the same result as if executed sequentially.
- Prevents all anomalies.

## 1.2 Repeatable Read
- Once data is read in a transaction, it cannot change during the transaction.
- Prevents non-repeatable reads.
- May allow phantom reads.

## 1.3 Snapshot Isolation
- Each transaction reads from a consistent snapshot of the database taken at its start.
- Commit succeeds only if no other transaction has modified the same data.
- Prevents many anomalies but not all (e.g., write skew).

## 1.4 Read Committed
- A transaction only reads committed data.
- Prevents dirty reads.
- Allows non-repeatable reads and phantom reads.

## 1.5 Read Uncommitted
- Lowest isolation level.
- Allows reading uncommitted data.
- Allows most anomalies.

---

# 2. Anomalies

## 2.1 Dirty Write (DW)
- A transaction overwrites data that another uncommitted transaction has written.

## 2.2 Dirty Read (DR)
- A transaction reads uncommitted data from another transaction.

## 2.3 Non-Repeatable Read / Fuzzy Read (FR)
- A transaction reads the same row twice and sees different values.

## 2.4 Phantom Read (PR)
- A transaction reads rows matching a condition.
- Another transaction inserts/deletes rows matching that condition before the first commits.

## 2.5 Lost Update (LU)
- Two transactions read the same value and both update it.
- One update overwrites the other.

## 2.6 Read Skew (RS)
- A transaction reads logically related data items but sees an inconsistent partial update.

## 2.7 Write Skew (WS)
- Two transactions read the same data but update disjoint parts.
- Can violate database constraints.

---

# 3. Which Isolation Levels Prevent Which Anomalies?

| Isolation Level     | Prevents                                   |
|----------------------|---------------------------------------------|
| Read Uncommitted     | DW                                          |
| Read Committed       | DW, DR                                      |
| Snapshot Isolation   | DW, DR, FR, LU, RS                          |
| Repeatable Read      | DW, DR, FR, LU, RS, WS                      |
| Serializability      | All anomalies                               |

---

## Serializability alone is not sufficient
- T1 - Get balance
- T2 - Withdraw
- T3 - Get balance

The execution is serializable as T1, T3, T2
We need strict serializability = serializability + linearizability
i.e transactions should be serializable and executed in real world order

---

# View Serializability

---

# 1. View Serializability

Two schedules are said to be **view equivalent** if:

1. **First read rule:**  
   The first read of a data item is performed by the **same transaction** in both schedules.

2. **Final write rule:**  
   The **last write** on every data item is performed by the **same transaction** in both schedules.

3. **Producer–consumer rule (W → R):**  
   If a transaction writes a value that another transaction reads, this **producer–consumer relationship must be preserved** in both schedules.

A schedule is **view serializable** if it is view equivalent to some **serial schedule**.

---

# 2. Conflict Serializability

Two schedules **S** and **S′** are **conflict equivalent** if:

- For every pair of **conflicting operations**  
  (RW, WR, WW on the same data item from different transactions),  
  the **order** of these operations is the same in both schedules.

A schedule **S** is **conflict serializable** if it is conflict equivalent to a serial schedule.

---

# 3. Concurrency Control Approaches

## 3.1 Pessimistic Concurrency Control
- Blocks a transaction **before** it can cause a violation of serializability.
- Ensures safety by **acquiring locks**.
- Transaction resumes only when safe to proceed.

## 3.2 Optimistic Concurrency Control
- Assumes conflicts are rare.
- Transactions proceed without locking.
- Serializability is checked **at commit time**.
- If conflict occurs → **transaction aborts and restarts**.

---

# 2-Phase Locking (2PL), Optimistic Concurrency Control, and MVCC

---

# 1. 2-Phase Locking (2PL)

2-Phase Locking is a concurrency control protocol that ensures conflict serializability by structuring how transactions acquire and release locks.

## 1.1 Phases in 2PL

Transactions acquire locks in **two distinct phases**:

1. **Expanding (Growing) Phase**
   - A transaction **only acquires locks**.
   - No locks are released during this phase.

2. **Shrinking Phase**
   - A transaction **only releases locks**.
   - No new locks are acquired during this phase.

Once a transaction releases **any** lock, it cannot obtain new locks afterward.

## 1.2 Property

- Any schedule generated by a **2PL protocol is conflict serializable**.

---

# 2. Optimistic Concurrency Control (OCC)

Optimistic Concurrency Control assumes that conflicts between transactions are rare. Instead of using locks, it validates at commit time whether a transaction can safely commit.

## 2.1 Key Idea
- **No locks** are used during normal execution.
- Transactions execute freely and are validated before commit.

## 2.2 Phases of OCC

Transactions execute in **three phases**:

1. **Begin Phase**
   - The transaction is assigned a unique timestamp or version number (e.g., `start_timestamp`).

2. **Read and Modify Phase**
   - The transaction performs reads and tentative writes.
   - Writes are buffered locally (not yet visible to others).

3. **Validate and Commit/Rollback Phase**
   - At commit time, the system **validates** whether any concurrent transaction has modified the data items read by this transaction.
   - If conflicts are detected (i.e., some data item was modified by another committed transaction after this transaction started), the transaction **rolls back** and may be restarted.
   - If validation passes, the transaction’s writes are applied and it **commits**.

---

# 3. Multiversion Concurrency Control (MVCC)

Multiversion Concurrency Control maintains **multiple versions** of each data item to improve concurrency and reduce read–write conflicts.

## 3.1 Key Idea

- Each write operation **creates a new version** of a data item.
- Old versions are **not overwritten** immediately.
- Each version is typically associated with:
  - A value
  - A version ID or timestamp (e.g., commit timestamp of the writer)

This allows:

- Readers to access a **consistent snapshot** of the data.
- Writers to proceed without blocking readers.

---

# 4. Snapshot Isolation Using MVCC

Snapshot Isolation (SI) is commonly implemented using MVCC.

## 4.1 How Snapshot Isolation Works

1. **Readers Pick a Version**
   - Each transaction reads from the **committed row versions** that existed when the transaction **began**.
   - This provides a **consistent snapshot** of the database at the start time of the transaction.

2. **Writers Create New Versions**
   - When a transaction updates a row, it **creates a new row version** instead of overwriting the existing one.
   - The old version remains available to transactions that started earlier and are still using that snapshot.

3. **Conflicts Resolved at Commit**
   - At commit time, the system checks whether any **other transaction has modified the same rows** since this transaction’s snapshot.
   - If another transaction has modified those rows:
     - The current transaction’s commit is **rejected** (it may be rolled back and retried).
   - If no conflicting modifications are found:
     - The transaction **commits**, and its new versions become visible to future transactions.

---

# Atomicity

Atomicity ensures that a transaction is treated as a single indivisible unit: **either all operations succeed or none do**.

---

# 1. Journaling / Write-Ahead Logging (WAL)

- Before performing an operation, metadata describing the operation is written to a separate log file.
- Markers indicate whether the operation completed successfully.
- If the system crashes, the log is used to either redo or undo operations.
- Ensures atomicity and durability.

---

# 2. Two-Phase Commit (2PC)

2PC is a distributed commit protocol ensuring that all participants in a distributed transaction either commit or abort together.

## 2.1 Components
- **Coordinator**: Orchestrates the protocol.
- **Participants**: Nodes involved in executing the transaction.

## 2.2 Phase 1 — Voting Phase
1. Coordinator sends the transaction to all participants.
2. Participants execute the transaction up to the commit point.
3. Participants vote **YES** (if successful) or **NO** (if some issue occurs).

## 2.3 Phase 2 — Commit Phase
- If **all votes are YES**, coordinator sends **COMMIT** to all.
- If **at least one NO**, coordinator sends **ABORT** to all.
- Participants acknowledge and complete the transaction.

## 2.4 Write-Ahead Log Usage
- Both coordinator and participants log their decisions.
- Allows recovery after failures.

## 2.5 Timeout Handling
- Coordinator times out while waiting for votes.
- If any participant does not respond, it is counted as **NO**.

## 2.6 Limitation — No Liveness Guarantee
- 2PC may block forever if the coordinator fails.
- Does **not** guarantee liveness property.

---

# 3. Three-Phase Commit (3PC)

3PC improves on 2PC by splitting the voting phase into two steps, reducing blocking.

## 3.1 Key Idea
- The *voting phase* is divided into:
  1. Coordinator sends vote result to all nodes.
  2. Coordinator sends commit/abort instruction.

## 3.2 Failure Handling
- If the coordinator fails after the first step, participants can independently decide based on received messages.

## 3.3 Properties
- **Guarantees liveness** (protocol eventually progresses).
- **Does NOT guarantee safety** (may violate atomicity under certain failures).

---

# 4. Quorum-Based Commit

Uses quorum intersection properties to ensure atomicity in distributed commits.

## 4.1 Definitions
- **Vc** = Commit quorum  
- **Va** = Abort quorum  
- **V** = Total number of participants  

Rules:
- Commit only if **Vc** participants vote commit.
- Abort only if **Va** participants vote abort.
- Must satisfy:  
  **Va + Vc > V**  
  Ensures commit and abort decisions cannot both be valid simultaneously.

---

# 5. Commit Protocol (Quorum-Based)

- Coordinator sends **prepare**.
- Waits until it receives **Vc** acknowledgements.
- If quorum is reached → commit.
- Otherwise → abort.

---

# 6. Termination Protocol (After Failure)

1. Participants elect a **new coordinator**.
2. After network partition heals:
   - If any participant has **committed/aborted**, the coordinator finalizes that decision.
   - If at least one participant is in **prepare-to-commit** and at least **Vc** participants await the vote, the coordinator sends **prepare-to-commit**.
   - If none are in that stage but at least **Va** participants await a vote, the coordinator sends **prepare-to-abort**.

---

# 7. Merge Protocol

When partitions merge:

- Leaders of partitions elect a **global coordinator**.
- The termination protocol is executed to ensure all nodes converge to the same final decision.

---

# Sagas

A **Saga** is a sequence of transactions **T₁, T₂, …, Tₙ**, where each transaction **Tᵢ** has a corresponding **compensating transaction Cᵢ**.  
Compensating transactions undo the effects of their forward transactions if failures occur later in the workflow.  
Sagas are used when full ACID transactions are not feasible in long‑running or distributed systems.

---

# 1. Saga Structure

- A saga consists of multiple sub-transactions **T₁ → T₂ → … → Tₙ**.
- If any transaction fails:
  - Previously completed transactions are rolled back by executing compensating operations **Cᵢ** in reverse order.
- Unlike 2PC, sagas do **not block** and allow long-lived workflows.

---

# 2. Compensating Transactions (Cᵢ)

- Each forward transaction **Tᵢ** must have a corresponding **Cᵢ** that semantically reverses its effect.
- Compensation is not always perfect (logical undo instead of physical undo).

Example:  
- T₁: Reserve a seat  
- C₁: Cancel the seat reservation  

---

# 3. Semantic Locks

- Sagas use **semantic locks**, which are high-level locks that restrict access to specific data items that require special handling.
- These locks:
  - Protect data that should not be accessed concurrently.
  - Remain active for the **entire duration of the saga**.
- Prevent anomalies without blocking the entire dataset.

---

# 4. Commutative Updates

- Some updates are **commutative**, meaning:
  ```
  Tᵢ followed by Tⱼ  =  Tⱼ followed by Tᵢ
  ```
- When updates commute, order does not matter.
- Commutativity helps:
  - Reduce conflict.
  - Mitigate **lost update** anomalies.
  - Improve parallelism.

Examples:
- Incrementing a counter  
- Appending to a log  

---

# 5. Reordering Saga Structure

To reduce the chance of expensive rollbacks and improve safety, a saga may be **reordered**.

## 5.1 Pivot Transaction

- A **pivot transaction** divides the saga into two sections:
  - Transactions **before pivot**: allowed to fail and thus compensated.
  - Transactions **after pivot**: must *not* fail because they cannot be undone safely.

## 5.2 Reordering Rules

- Transactions that **cannot fail** or whose rollback has dangerous consequences should be executed **after** the pivot.
- Transactions that **can fail safely** and can be compensated belong **before** the pivot.

This reduces the risk of:
- Cascading failures  
- High-cost compensations  
- Irreversible side effects  

---

# 6. Summary Table

| Concept | Description |
|--------|-------------|
| Saga | Sequence of transactions with compensating operations |
| Compensating Transaction | Reverses the effect of a forward transaction |
| Semantic Lock | High-level lock held until saga completion |
| Commutative Updates | Updates whose order does not affect the final state |
| Pivot Transaction | Splits saga into compensable and non-compensable phases |

---




