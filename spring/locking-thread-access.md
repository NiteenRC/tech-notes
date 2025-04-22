# Microservices: Data Consistency & Dirty Reads

## ❓ Question
In microservices, two threads try to modify the same resource.  
**How do you maintain data consistency and explain dirty reads?**

---

## ✅ Answer

### 🧩 Problem
- Thread 1 updates data but hasn't committed.
- Thread 2 reads that uncommitted data → **dirty read** → inconsistency.

---

### 🔒 Data Consistency Strategies

#### 1. **Transaction Isolation**
| Level           | Prevents     |
|------------------|--------------|
| READ UNCOMMITTED | ❌ Dirty reads |
| READ COMMITTED   | ✅ Dirty reads |
| REPEATABLE READ  | ✅ Non-repeatable reads |
| SERIALIZABLE     | ✅ All issues |

```java
@Transactional(isolation = Isolation.READ_COMMITTED)
```

---

#### 2. **Optimistic Locking**
- Add `@Version` field.
- Retry if version mismatch.
- ❗ No blocking, fails fast.
- Note: without @Version, we lose the ability to efficiently manage concurrent modifications, increasing the risk of dirty reads, data overwrites, and inconsistency in a distributed microservices environment.

```java
@Version
private int version;
```

**✅ Use Case:**
- When **conflicts are rare**
- High **read-to-write ratio**
- Scalable systems (e.g., inventory updates, profile edits)

---

#### 3. **Pessimistic Locking**
- Lock row during transaction to prevent others from reading/updating.

```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
Product findById(Long id);
```

**✅ Use Case:**
- When **conflicts are frequent**
- Critical data changes (e.g., banking transactions, seat reservations)

---

#### 4. **Distributed Locking**
- Use Redis/Zookeeper to lock across services.

```java
RLock lock = redissonClient.getLock("key");
lock.tryLock();
```

**✅ Use Case:**
- Shared resource updates across microservices
- Preventing race conditions in distributed systems

---

#### 5. **Eventual Consistency**
- Use **Sagas** or **Event Sourcing**.

**✅ Use Case:**
- Complex workflows across services (e.g., order processing)

---

### 😈 Dirty Read Example
Occurs when reading uncommitted data:

```sql
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
```

---

### ✅ Summary

| Strategy            | Dirty Read Safe | Use When                      |
|---------------------|------------------|-------------------------------|
| Isolation Levels    | ✅               | Prevent transactional issues |
| Optimistic Locking  | ✅               | Low conflict, high performance |
| Pessimistic Locking | ✅               | High conflict, critical ops  |
| Redis Locking       | ✅               | Distributed access control   |
| Sagas/Event Sourcing| ⚠️ Eventually    | Distributed long-lived workflows |