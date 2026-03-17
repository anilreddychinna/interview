## Kafka Interview Questions & Answers

Core, repeated Kafka questions with clear, short answers.

---

### 1. What is Apache Kafka?

**Answer:** Apache Kafka is a **distributed, fault-tolerant, high-throughput messaging and streaming platform**. It is used to build real-time data pipelines and streaming applications.

**Key points (interview lines):**
- Stores streams of records in **topics**.
- Scales horizontally with **partitions** across brokers.
- Provides **durability** (data on disk, replicated).

---

### 2. What is a Topic, Partition, and Offset?

**Answer:**
- **Topic**: A named stream of data (like a table in a database).
- **Partition**: A topic is split into multiple partitions for **scalability and parallelism**. Each partition is an ordered, immutable sequence of records.
- **Offset**: A unique position (long number) of a record within a partition.

**Interview tip:** “Order is guaranteed only **within a single partition**, not across all partitions.”

---

### 3. What is a Producer and a Consumer?

**Answer:**
- **Producer**: Application that writes (publishes) messages to Kafka topics.
- **Consumer**: Application that reads (subscribes) messages from Kafka topics.

Producers choose which partition to send to (e.g., by key). Consumers read from partitions using offsets.

---

### 4. What is a Consumer Group?

**Answer:** A **consumer group** is a group of consumers that work together to consume data from a set of topics.

**Key points:**
- Each partition is consumed by **only one** consumer **within the same group**.
- Multiple groups can read the same data independently.
- Used for **parallelism** and **scaling** consumers.

---

### 5. How does Kafka guarantee message ordering?

**Answer:** Kafka guarantees **ordering within a partition**. Messages are appended to a partition in order and read in that same order.

If you need strict order per key (e.g., per customer ID), you should **use the same key** so all messages for that key go to the **same partition**.

---

### 6. What is replication in Kafka? Why is it needed?

**Answer:** Replication means each partition has **one leader** and **one or more followers** (replicas) on different brokers.

**Why:**
- **Fault tolerance**: If leader broker fails, a follower can become the new leader.
- **High availability**: Data is not lost when a single broker goes down.

Replication factor is set per topic (e.g., 3).

---

### 7. What is ISR (In-Sync Replica)?

**Answer:** ISR is the set of replicas that are **fully caught up** with the leader for a partition.

**Key points:**
- Kafka only considers ISR members for leader election.
- This ensures **no data loss** when a leader fails (if `acks=all` and `min.insync.replicas` are configured).

---

### 8. What is the difference between `acks=0`, `acks=1`, and `acks=all`?

**Answer:** These are producer acknowledgment settings:

- `acks=0`: Producer does **not wait** for any acknowledgment. Fast but can lose messages.
- `acks=1`: Producer waits for **leader** to write the message. Better, but data can be lost if leader fails before followers replicate.
- `acks=all` (or `-1`): Producer waits until **all in-sync replicas** store the message. Safest but slightly slower.

**Interview line:** “For strong durability, use `acks=all` with `min.insync.replicas` > 1.”

---

### 9. How are messages stored in Kafka? Is it a queue or a database?

**Answer:** Messages are stored on disk in append-only **logs** per partition.

- Kafka behaves like a **distributed commit log**, not a typical queue.
- Data is kept for a **configurable retention** (time or size), not removed when consumed.

Because of this, **multiple consumers** can read the same data at different speeds and times.

---

### 10. What is the difference between Kafka and a traditional message queue (like RabbitMQ)? (high-level)

**Answer (short, interview style):**
- Kafka: **Distributed log**, horizontal scalability, high throughput, persistent storage, multiple consumers, used for **stream processing** and event-driven architectures.
- Traditional queues: Often focus on **work queues**, messages removed once consumed, simpler routing patterns, but may not handle the same scale.

---

### 11. What is Kafka Streams?

**Answer:** Kafka Streams is a **Java library** for building stream processing applications on top of Kafka.

- Provides **high-level DSL** for operations like `map`, `filter`, `join`, `aggregate`.
- Runs as part of your application (no separate cluster like Spark).
- Uses Kafka topics as input and output.

---

### 12. What is the role of ZooKeeper in Kafka (classic architecture)?

**Answer:** In older Kafka versions, ZooKeeper is used for:

- Storing metadata (brokers, topics, partitions).
- Leader election for partitions.
- Cluster coordination.

**Note:** Newer Kafka versions (KIP-500) aim for **Kafka without ZooKeeper** (self-managed quorum).

---

### 13. How do you commit offsets in Kafka? Why is it important?

**Answer:** Offsets track how far a consumer has read in a partition.

- **Auto-commit**: Kafka auto-commits offsets at intervals.
- **Manual commit**: Application commits offsets after processing messages.

**Why important:** If you commit before processing and the app crashes, you may **lose messages**. If you commit after processing, you might **reprocess** messages after restart (at-least-once).

---

### 14. Delivery semantics: at-most-once, at-least-once, exactly-once?

**Answer:**
- **At-most-once**: Messages may be lost but are never processed more than once.
- **At-least-once**: Messages are never lost but may be processed more than once (duplicates).
- **Exactly-once**: Messages are processed once and only once (hardest to achieve).

Kafka supports **at-least-once** by default, and with idempotent producers + transactions, you can approach **exactly-once** semantics.

---

### 15. What is idempotent producer in Kafka?

**Answer:** An idempotent producer ensures that **retries do not create duplicate messages**.

- Broker assigns a **producer ID (PID)** and sequence numbers.
- If the same message is retried, Kafka can detect it and avoid writing duplicates.

Enable by setting `enable.idempotence=true` (in client config).

---

## Handling 1 million+ records (scale & throughput)

Interview questions focused on **high volume**: producing/consuming millions of records, APIs, and uploading to S3 or similar.

---

### 16. How would you design Kafka to handle 1 million messages per second?

**Answer:**

- **Partitions**: Use **enough partitions** so multiple consumers can read in parallel (e.g., 10–50+ per topic). Throughput per partition is limited; more partitions = more parallelism.
- **Producer**: Use **batching** (`batch.size`, `linger.ms`) so the producer sends batches instead of one-by-one. Use `acks=1` or `acks=all` depending on durability needs.
- **Brokers**: Scale **horizontally** (add brokers). Spread partitions across brokers; ensure disk and network can handle the load.
- **Consumers**: Scale **consumer group** size up to the number of partitions (one consumer per partition for max parallelism). Tune `fetch.min.bytes` / `max.partition.fetch.bytes` for throughput vs latency.

**Interview line:** “For 1M msg/s I’d increase partitions, use producer batching, scale brokers and consumers, and monitor consumer lag.”

---

### 17. How do you consume 1 million records from Kafka without falling behind (consumer lag)?

**Answer:**

- **Parallelism**: Run at least as many consumers as partitions (same consumer group). Each partition has one consumer; more partitions = more parallel consumers.
- **Batch processing**: Process in **batches** (e.g., poll 500–5000 records, process, then commit). Avoid processing one-by-one if the workload allows.
- **Tuning**: Increase `max.poll.records`, `fetch.min.bytes`, and `max.partition.fetch.bytes` so each poll gets more data and you do less round-trips.
- **Commit strategy**: Commit offsets **after** processing a batch (manual commit) to avoid reprocessing on crash, while keeping batch size reasonable so you don’t reprocess too much.
- **Monitoring**: Watch **consumer lag**; add more consumers or scale partitions if lag grows.

---

### 18. What is consumer lag? Why does it matter for high volume?

**Answer:** **Consumer lag** is the difference between the **latest offset** in a partition and the **offset the consumer group has committed** for that partition.

- If producers write faster than consumers read, lag **increases**.
- High lag means **delay** in processing and more data at risk if you need to replay or recover.
- For 1M+ records, you must **monitor lag** and scale consumers/partitions or optimize processing to keep lag under control.

---

### 19. How would you process 1 million records from Kafka and upload results to S3?

**Answer:**

- **Consume in batches** from Kafka (chunk size e.g. 1000–10000 per batch).
- **Process** each batch in memory (transform, aggregate, filter).
- **Write to S3** per batch:
  - Use **multipart upload** for large single objects (e.g., one big file per batch).
  - Or write **many small objects** (e.g., one object per record or per sub-batch) with meaningful keys (e.g., date/partition prefix).
- **Commit Kafka offsets** only after the batch is successfully written to S3 (at-least-once; duplicates possible on retry – design for idempotency if needed).
- **Scale**: Run multiple consumer instances (same group) so multiple partitions are processed in parallel; each instance can upload to S3 independently.

**Interview line:** “I consume in batches, process each batch, use S3 multipart or many small objects per batch, commit offsets after S3 write, and scale with more consumers/partitions.”

