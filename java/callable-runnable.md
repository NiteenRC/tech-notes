# Callable vs Runnable in Java

Both `Callable` and `Runnable` are used to define tasks for concurrent execution, but they differ in return types, exception handling, and use cases.

---

## ✅ Basic Difference

| Feature                 | `Runnable`                         | `Callable<V>`                         |
|-------------------------|------------------------------------|----------------------------------------|
| Return Value            | ❌ Does **not** return a result     | ✅ Returns a result of type `V`         |
| Throws Checked Exception| ❌ No                               | ✅ Yes                                  |
| Method to Override      | `public void run()`                | `public V call() throws Exception`     |
| Introduced In           | Java 1.0                           | Java 5 (`java.util.concurrent`)        |

---

## ✅ Syntax Comparison

### Runnable
```java
Runnable task = () -> {
    System.out.println("Running task...");
};
new Thread(task).start();
````

### Callable

```java
Callable<String> task = () -> {
    return "Task completed";
};

ExecutorService executor = Executors.newSingleThreadExecutor();
Future<String> future = executor.submit(task);
System.out.println(future.get()); // Output: Task completed
executor.shutdown();
```

---

## ✅ When to Use

| Scenario                        | Use `Runnable` | Use `Callable`                 |
| ------------------------------- | -------------- | ------------------------------ |
| Fire-and-forget task            | ✅              | ❌                              |
| Need to return result           | ❌              | ✅                              |
| Need to throw checked exception | ❌              | ✅                              |
| Used directly with `Thread`     | ✅              | ❌ (use with `ExecutorService`) |

---

## ✅ ThreadPool Behavior

* `Runnable` and `Callable` can both be submitted to `ExecutorService`.
* `ExecutorService.submit(Runnable)` → returns `Future<?>`
* `ExecutorService.submit(Callable<V>)` → returns `Future<V>`

---

## ✅ Checked Exception Handling

### Runnable – Can't throw checked exception

```java
Runnable task = () -> {
    // This will cause compile error if you throw checked exception
};
```

### Callable – Can throw checked exception

```java
Callable<String> task = () -> {
    if (true) throw new IOException("Failure");
    return "Success";
};
```

---

## ✅ Summary Cheat Sheet

| Feature             | Runnable   | Callable<V> |
| ------------------- | ---------- | ----------- |
| Return Value        | ❌ No       | ✅ Yes (`V`) |
| Checked Exception   | ❌ No       | ✅ Yes       |
| Method Name         | `run()`    | `call()`    |
| Introduced In       | Java 1.0   | Java 5      |
| Works with Thread   | ✅ Yes      | ❌ No        |
| Works with Executor | ✅ Yes      | ✅ Yes       |
| Future Support      | ✅ Indirect | ✅ Direct    |

---

> ✅ **Tip:** Use `Callable` when your task needs to **return a result** or **throw checked exceptions**. Use `Runnable` for **lightweight, non-blocking** tasks.