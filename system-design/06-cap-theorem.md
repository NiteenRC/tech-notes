## ✅ CAP Theorem — Interview-Ready Explanation

### 🔹 What is the CAP Theorem?

The **CAP Theorem**, proposed by **Eric Brewer**, states that in any **distributed system**, you can **only achieve two
out of the following three guarantees simultaneously**:

| Property                    | Description                                                                                                |
|-----------------------------|------------------------------------------------------------------------------------------------------------|
| **Consistency (C)**         | Every read receives the most recent write (or an error). All nodes see the same data at the same time.     |
| **Availability (A)**        | Every request receives a (non-error) response — even if it's not the most recent. System stays responsive. |
| **Partition Tolerance (P)** | The system continues to operate despite network partitions (message delays or losses between nodes).       |

> In the presence of a **network partition**, a system must choose between **Consistency** and **Availability**.

---

### 🔹 Why is Partition Tolerance Non-Negotiable?

- Distributed systems **must** tolerate partitions — network failures and latency are inevitable.
- So, practically, **CAP becomes a choice between Consistency and Availability during a partition**.

---

### 🔹 CAP Trade-offs — Choose Any Two

| Type   | Guarantees                         | Trade-Off                                                                 | Examples                                                   |
|--------|------------------------------------|---------------------------------------------------------------------------|------------------------------------------------------------|
| **CP** | Consistency + Partition Tolerance  | Sacrifices availability if partitioned                                    | HBase, MongoDB (strong consistency), Redis (with Sentinel) |
| **AP** | Availability + Partition Tolerance | Sacrifices consistency (eventual)                                         | Cassandra, Couchbase, DynamoDB                             |
| **CA** | Consistency + Availability         | Sacrifices partition tolerance (not possible in real distributed systems) | Single-node RDBMS (no partitioning)                        |

---

### 🔹 Real-World Analogies

- **CP**: Bank transaction system → Always correct, even if some servers are down.
- **AP**: Twitter feed → Better to show stale data than be unavailable.
- **CA**: Not realistic in a distributed system — assumes perfect networks (no partitions).

---

### 🔹 Interview Questions You Might Face

**Q1. Why can’t a distributed system achieve all three?**
> Because during a partition, to stay available, a system must risk serving stale data. To stay consistent, it may need
> to reject requests. You can’t do both.

**Q2. Which trade-off is better — CP or AP?**
> It depends on the use case:
> - **CP** if correctness matters (e.g., financial systems).
> - **AP** if user experience matters (e.g., social media, shopping cart).

**Q3. Can consistency be relaxed?**
> Yes — many systems adopt **eventual consistency** to stay available and partition-tolerant, eventually syncing data
> across nodes.

**Q4. How does Cassandra balance CAP?**
> Cassandra is **AP** by default but offers tunable consistency via quorum reads/writes, allowing trade-offs per
> operation.

---

### 🔹 Bonus: PACELC Model (Advanced Interview Tip)

While CAP focuses only on what happens **during a partition**, the **PACELC model** extends it:

> **PACELC** = _If there is a Partition (P), choose between Availability (A) and Consistency (C); **Else**, choose
between Latency (L) and Consistency (C)._

This gives a more nuanced view, even when the system is functioning normally.

---

### 🧠 Summary Cheat Sheet

| System    | Partition Tolerance | Availability | Consistency | Notes                                |
|-----------|---------------------|--------------|-------------|--------------------------------------|
| Cassandra | ✅ Yes               | ✅ Yes        | ❌ Eventual  | Tunable consistency via quorum       |
| MongoDB   | ✅ Yes               | ❌ Sometimes  | ✅ Strong    | Primary-secondary with write concern |
| DynamoDB  | ✅ Yes               | ✅ Yes        | ❌ Eventual  | Can be tuned to strong consistency   |
| HBase     | ✅ Yes               | ❌ No         | ✅ Strong    | Designed for consistency             |