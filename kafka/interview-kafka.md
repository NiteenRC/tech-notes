### **1. How does Kafka ensure that a message is delivered and processed exactly once?**

**Answer:**
Kafka guarantees at least once delivery by default. To achieve **exactly once** semantics, Kafka uses the following
mechanisms:

- **Idempotence:** Kafka producers can be configured with `acks=all` and `enable.idempotence=true` to ensure that
  messages are sent only once, even if retries happen.
- **Transactions:** Kafka allows producers to send a batch of messages in a transaction to ensure atomicity. Consumers
  can then commit offsets atomically as well, ensuring that messages are processed exactly once.
- **Consumer Offset Tracking:** Consumers track their offsets to know which messages they’ve processed, and if a failure
  occurs, they can resume from the last committed offset.

---

### **2. How do you prevent a message from being reprocessed by Kafka consumers?**

**Answer:**
To prevent reprocessing of messages, Kafka uses **consumer offsets**. Each consumer maintains its offset in a partition,
and once a message is processed, the consumer commits the offset. The key methods are:

- **Auto-commit:** Kafka automatically commits the consumer offset after processing a message.
- **Manual commit:** The consumer can control when to commit offsets, ensuring that it only commits after successful
  message processing.
- **Idempotent Processing:** Even if a message is consumed again due to an offset issue, idempotent consumers ensure
  that the processing doesn’t have negative side effects.

---

### **3. What are the advantages of Kafka over other message brokers like RabbitMQ or ActiveMQ?**

**Answer:**
Kafka offers several advantages:

- **High Throughput & Scalability:** Kafka is optimized for high throughput and can handle millions of messages per
  second. It can easily scale horizontally by adding more brokers.
- **Durability & Fault Tolerance:** Kafka replicates messages across brokers, ensuring fault tolerance and data
  durability. Even if a broker fails, messages can still be recovered.
- **Stream Processing:** Kafka allows real-time stream processing with its integration to tools like Kafka Streams and
  ksqlDB.
- **Consumer Groups:** Kafka allows multiple consumers to share the load of consuming messages from a topic, which
  enhances scalability.
- **Persistence:** Kafka persists messages on disk, which is not always the case for other message brokers like
  RabbitMQ.

---

### **4. How are multiple consumers coordinated within a Kafka consumer group?**

**Answer:**
In Kafka, a **consumer group** allows multiple consumers to share the workload of consuming messages from a topic. Each
partition of a topic is assigned to a single consumer in a group, ensuring that each message is processed only once.
Coordination is done via:

- **Partition Assignment:** Kafka assigns partitions to consumers in the group. If there are more consumers than
  partitions, some consumers will remain idle. If there are more partitions than consumers, Kafka distributes the
  partitions among the consumers.
- **Rebalancing:** When consumers join or leave a group, Kafka performs a **rebalance**, redistributing the partitions.

---

### **5. What are Kafka consumer offsets and how are they managed?**

**Answer:**
Kafka consumer offsets represent the last message a consumer has successfully processed in a partition. These offsets
are stored in Kafka’s internal topic (`__consumer_offsets`). The offset management can be done in two ways:

- **Automatic Offset Committing:** Kafka can automatically commit the offsets after every message is processed.
- **Manual Offset Committing:** Consumers can manually control when and what offset to commit, offering finer control,
  especially in cases where message processing fails.
  To **roll back** or **replay** messages, consumers can reset their offset to an earlier point in time or a specific
  offset value, enabling reprocessing of messages from that point onward.

---

### **6. What happens if data is lost during consumption, and how can you recover failed messages in Kafka?**

**Answer:**
Kafka ensures durability via **replication**, but if data is lost (due to issues like broker failure or
misconfiguration), the recovery process involves:

- **Dead Letter Queues (DLQs):** Failed messages are redirected to DLQs for later inspection and recovery.
- **Retry Mechanism:** Messages that fail to process can be retried with an exponential backoff or by placing them on a
  retry topic.
- **Log Compaction:** Kafka can retain the latest version of a message in a topic even after the message is deleted or
  processed.
- **Replication:** Kafka’s replication ensures data is available even if one or more brokers fail. The replicas of
  messages in other brokers provide high availability.
- **Exactly Once Semantics (EOS):** Kafka supports transactions that ensure that messages are neither lost nor
  duplicated, even if a failure happens.