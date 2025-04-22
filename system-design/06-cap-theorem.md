# CAP Theorem (Consistency, Availability, Partition Tolerance)

The **CAP Theorem**, also known as **Brewer's Theorem**, is a fundamental concept in distributed systems. It states that it is impossible for a distributed data store to simultaneously achieve all three of the following guarantees:

- **Consistency**
- **Availability**
- **Partition Tolerance**

A system can only provide at most two out of these three guarantees.

## The Three Properties

1. **Consistency (C)**
    - Every read will return the most recent write.
    - All nodes in the system have the same data at any given time.
    - If a node is updated, all subsequent reads from any node will see that updated value.

2. **Availability (A)**
    - Every request (read or write) will get a response, either success or failure.
    - The system is always available for requests, but this does not guarantee that the response will be up-to-date.

3. **Partition Tolerance (P)**
    - The system will continue to function even if a network partition (or communication failure) occurs between nodes.
    - It can tolerate communication breakdowns between nodes and continue working as expected.

## The CAP Theorem Explained

### 1. **CA (Consistency + Availability)**

- Guarantees consistency and availability, but sacrifices partition tolerance.
- In case of network partitions, the system may not be able to handle requests to maintain consistency and availability.

**Example**: Traditional relational databases (e.g., MySQL) often provide CA. They may stop accepting reads or writes in the event of a partition to ensure that the data remains consistent across nodes.

### 2. **CP (Consistency + Partition Tolerance)**

- Guarantees consistency and partition tolerance but sacrifices availability.
- The system will ensure data consistency even during network partitions, but some requests may not be served to maintain these properties.

**Example**: Systems like HBase focus on consistency and partition tolerance, and may reject some reads or writes during a partition to ensure that data remains consistent.

### 3. **AP (Availability + Partition Tolerance)**

- Guarantees availability and partition tolerance, but sacrifices consistency.
- The system will always respond to requests, even during network partitions, but the data may not be the most up-to-date.

**Example**: NoSQL databases like **Cassandra** prioritize availability and partition tolerance. During a partition, they may allow inconsistent data to be returned, but ensure that the system continues operating.

## Real-World Examples

- **Amazon DynamoDB**: Prioritizes **AP** (Availability + Partition Tolerance). DynamoDB allows reads and writes to continue during network partitions, but it might return inconsistent data temporarily. The system reconciles inconsistencies once the partition is resolved.

- **Google Spanner**: Prioritizes **CP** (Consistency + Partition Tolerance). Google Spanner ensures strong consistency across global data but may become unavailable during network partitions to maintain consistency.

## Trade-offs in System Design

- **Consistency vs. Availability**: A system prioritizing consistency may stop responding to requests during network issues to ensure data accuracy, while a system prioritizing availability will always respond, even if the data may be stale.

- **Availability vs. Partition Tolerance**: Systems focusing on availability and partition tolerance may return inconsistent data during network partitions to ensure the system remains operational.

- **Consistency vs. Partition Tolerance**: In systems prioritizing both consistency and partition tolerance, some requests may be rejected during partitions to ensure that data remains consistent.

## Key Takeaways

- **Consistency** ensures that every read sees the most recent write.
- **Availability** ensures that every request gets a response (even if it's not the most recent).
- **Partition Tolerance** allows the system to continue operating in the event of network partitions.

Understanding the CAP theorem is crucial for making informed decisions on how to design a distributed system that balances these trade-offs.

## Interview Questions

You might be asked the following in interviews:
- How does the CAP theorem affect system design decisions?
- In which scenarios would you prioritize consistency over availability or vice versa?
- How would you design a distributed system based on CAP guarantees?