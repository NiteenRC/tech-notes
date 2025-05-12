#### 🔹 JVM Memory Areas

```text
+------------------------------+
|      JVM Memory Areas       |
+------------------------------+
| Heap                        |
| - Young Gen (Eden + Survivor)|
| - Old Gen (Tenured)         |
+------------------------------+
| Stack (per thread)          |
+------------------------------+
| Program Counter (PC)        |
+------------------------------+
| Native Method Stack         |
+------------------------------+
| Metaspace (Java 8+)         |
+------------------------------+
```

---

#### 🔹 Heap Memory

* **Stores**: Objects and class instances
* **GC Managed**: ✅ Yes
* **Generations**:

    * **Young Generation**: short-lived objects
    * **Old Generation**: long-lived objects
    * **Metaspace** (post Java 8): class metadata

---

#### 🔹 Stack Memory

* **Per-thread memory**: Stores local variables, method calls
* **Lifecycle**: Allocated on method call, deallocated on return
* **GC Managed**: ❌ No

---

#### 🔹 Program Counter (PC) Register

* **Purpose**: Keeps track of the instruction being executed per thread

---

#### 🔹 Native Method Stack

* Used for **native (C/C++)** code via JNI

---

#### 🔹 Metaspace (Java 8+)

* Stores **class metadata** and **runtime constant pool**
* Can **grow dynamically**, unlike PermGen

---

### 🔹 Garbage Collection (GC)

| GC Type        | Description                       |
|----------------|-----------------------------------|
| Serial GC      | Single-threaded, small apps       |
| Parallel GC    | Multi-threaded, better throughput |
| CMS GC         | Low pause, partially concurrent   |
| G1 GC          | Balanced, default from Java 9+    |
| ZGC/Shenandoah | Ultra-low latency (Java 11+)      |

#### GC Phases

1. **Mark** – find live objects
2. **Sweep** – remove dead ones
3. **Compact** – clean memory fragments

---

### 🔹 JVM Memory Tuning Parameters

| Flag                   | Description              |
|------------------------|--------------------------|
| `-Xms`                 | Initial heap size        |
| `-Xmx`                 | Maximum heap size        |
| `-Xss`                 | Thread stack size        |
| `-XX:MaxMetaspaceSize` | Max metaspace size       |
| `-XX:+UseG1GC`         | Use G1 Garbage Collector |

---

### 🔹 Common Memory Leak Causes

* Static collections holding object references
* Unclosed resources (streams, sockets)
* ThreadLocal not cleared
* Event listeners not removed