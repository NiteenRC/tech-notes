# 🧠 Memory Leaks in Java

---

## ✅ What is a Memory Leak?

A **memory leak** in Java occurs when objects that are no longer used by the application **are not garbage collected** because they are still **strongly reachable** (held by references). Over time, these unused objects consume more heap memory and can eventually lead to:

- Slower performance  
- Increased GC activity  
- `OutOfMemoryError`

---

## 🔍 How to Recognize a Memory Leak

### ⚠️ Symptoms:
- Continuous **increase in heap usage**
- Frequent **Full GCs with little memory reclaimed**
- App performance degrades over time
- `java.lang.OutOfMemoryError: Java heap space`

### 🛠 Tools to Detect Leaks:
| Tool             | Description                                |
|------------------|--------------------------------------------|
| **New Relic**     | Real-time production-grade JVM monitoring, memory analysis, and alerts |
| **VisualVM**     | Heap usage, GC stats, object tracking (local) |
| **JConsole**     | Real-time monitoring of JVM memory         |
| **JProfiler / YourKit** | Advanced commercial profilers      |

💡 **Pro Tip**:  
Enable automatic heap dump generation on OOM:

```bash
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=./heapdump.hprof
````

---

## 🧰 Detecting Memory Leaks with New Relic

### ✅ Step-by-Step:

1. **Install New Relic Java agent** in your application.
2. Go to the **APM > JVMs** section:

    * Monitor **Heap memory usage**
    * Observe **GC activity & duration**
    * Check **thread count**
3. Set up **alerts and dashboards** for:

    * `Heap Used (%)`
    * GC time per minute
    * Exception rate (look for `OutOfMemoryError`)
4. Use **Distributed Tracing** to pinpoint which transactions are causing object retention.

### 📊 Common Indicators in New Relic:

* JVM Heap memory consistently increasing without dropping
* High GC activity with no substantial memory reclamation
* Long-lived transactions holding references
* Thread count increasing continuously

### ✅ Benefits of New Relic:

* Real-time production monitoring
* No manual heap dump needed
* Auto-detects JVM anomalies
* Alerting and anomaly detection via AI

---

## 🚨 Common Causes of Memory Leaks

| Cause                      | Explanation                                           |
| -------------------------- | ----------------------------------------------------- |
| **Static Collections**     | `static List`, `Map` holding references forever       |
| **Event Listeners**        | Not deregistering listeners leads to memory retention |
| **Unclosed Resources**     | File streams, DB connections not closed properly      |
| **ThreadLocal Misuse**     | Data not removed manually for long-lived threads      |
| **Cache Without Eviction** | Retains unused data indefinitely                      |

---

## 🧪 Example: Static Collection Leak

```java
public class MemoryLeakExample {
    private static List<byte[]> leakyList = new ArrayList<>();

    public static void main(String[] args) {
        while (true) {
            byte[] block = new byte[1024 * 1024]; // 1 MB
            leakyList.add(block); // Retained forever
            System.out.println("Allocated 1MB");
            sleep(100);
        }
    }

    private static void sleep(long ms) {
        try { Thread.sleep(ms); } catch (InterruptedException ignored) {}
    }
}
```

> 🔴 This code will eventually cause an `OutOfMemoryError` because `leakyList` keeps growing and is never cleared.

---

## ✅ How to Solve and Prevent Memory Leaks

### 1. **Use Weak References for Caches**

```java
Map<Object, WeakReference<Object>> cache = new WeakHashMap<>();
```

### 2. **Remove Listeners on Component Destruction**

```java
button.addActionListener(listener);
button.removeActionListener(listener); // Important
```

### 3. **Use `try-with-resources` to Close Streams**

```java
try (FileInputStream in = new FileInputStream("file.txt")) {
    // use the stream safely
}
```

### 4. **Avoid Manual ThreadLocal Misuse**

```java
ThreadLocal<Object> local = new ThreadLocal<>();
local.set(new Object());

// Always clean up
local.remove();
```

### 5. **Use Caching Libraries with Eviction (e.g., Caffeine)**

```java
Cache<String, Object> cache = Caffeine.newBuilder()
    .maximumSize(1000)
    .expireAfterWrite(10, TimeUnit.MINUTES)
    .build();
```

---

## 🧹 Best Practices to Avoid Memory Leaks

| Practice                               | Benefit                      |
| -------------------------------------- | ---------------------------- |
| Avoid long-lived static references     | Prevents heap retention      |
| Use `try-with-resources` for closables | Ensures proper cleanup       |
| Deregister listeners and callbacks     | Avoids hidden references     |
| Clean up `ThreadLocal` variables       | Prevents thread memory leaks |
| Monitor heap usage in staging/prod     | Early detection of leaks     |

---

## ✅ Interview Tips

> ✅ **Interviewer may ask:** How would you detect and fix a memory leak in production?

**Sample Answer:**

> "In production, I’d use New Relic to monitor JVM heap usage, GC patterns, and alert thresholds. If I notice continuous memory growth, I’d inspect which transactions or threads are holding memory using distributed tracing and thread profiling. Based on that, I’d optimize references, deregister listeners, and use weak references or caching with proper eviction strategies."

---