### 📘 java7-to-java17-migration

# ☕ Java 7 to Java 17 Migration Guide

This guide presents a structured and side-by-side comparison of Java 7 code and its Java 17 equivalent. It also
highlights the key language features, syntax enhancements, and API improvements you should leverage when migrating.

---

## ✅ 1. Try-With-Resources (Java 9 Enhancement)

### Java 7

```java
BufferedReader reader = new BufferedReader(new FileReader("file.txt"));
try{
        System.out.

println(reader.readLine());
        }finally{
        reader.

close();
}
````

### Java 17

```java
BufferedReader reader = new BufferedReader(new FileReader("file.txt"));
try(reader){
        System.out.

println(reader.readLine());
        }
```

**✅ Explanation:** Java 9+ allows effectively final variables declared outside to be used in try-with-resources.

---

## ✅ 2. Diamond Operator in Anonymous Classes (Java 9)

### Java 7

```java
Map<String, List<String>> map = new HashMap<String, List<String>>() {
    // implementation
};
```

### Java 17

```java
Map<String, List<String>> map = new HashMap<>() {
    // implementation
};
```

**✅ Explanation:** You can now use `<>` even with anonymous inner classes.

---

## ✅ 3. Private Methods in Interfaces (Java 9)

### Java 7

```java
interface Logger {
    default void log(String message) {
        System.out.println("LOG: " + message);
    }
}
```

### Java 17

```java
interface Logger {
    private void logHelper(String message) {
        System.out.println("LOG: " + message);
    }

    default void log(String message) {
        logHelper(message);
    }
}
```

**✅ Explanation:** Use private methods in interfaces to reduce duplication across default methods.

---

## ✅ 4. Switch Expressions (Java 14-17)

### Java 7

```java
String result;
switch(day){
        case MONDAY:
        case FRIDAY:
result ="Workday";
        break;
        case SATURDAY:
        case SUNDAY:
result ="Weekend";
        break;
default:
result ="Unknown";
        }
```

### Java 17

```java
String result = switch (day) {
    case MONDAY, FRIDAY -> "Workday";
    case SATURDAY, SUNDAY -> "Weekend";
    default -> "Unknown";
};
```

**✅ Explanation:** Concise and expression-based switch blocks introduced in Java 14, finalized in Java 17.

---

## ✅ 5. Text Blocks (Java 15)

### Java 7

```java
String html = "<html>\n" +
        "  <body>Hello</body>\n" +
        "</html>";
```

### Java 17

```java
String html = """
        <html>
          <body>Hello</body>
        </html>
        """;
```

**✅ Explanation:** Triple-quoted strings support multiline content with consistent formatting.

---

## ✅ 6. Pattern Matching for `instanceof` (Java 16)

### Java 7

```java
if(obj instanceof String){
String s = (String) obj;
    System.out.

println(s.length());
        }
```

### Java 17

```java
if(obj instanceof
String s){
        System.out.

println(s.length());
        }
```

**✅ Explanation:** Avoid redundant casting with smarter pattern-based instanceof.

---

## ✅ 7. Records (Java 16)

### Java 7

```java
public class Person {
    private final String name;
    private final int age;

    public Person(String name, int age) { ...}

    public String getName() { ...}

    public int getAge() { ...}

    public boolean equals(Object o) { ...}

    public int hashCode() { ...}

    public String toString() { ...}
}
```

### Java 17

```java
public record Person(String name, int age) {
}
```

**✅ Explanation:** Records are concise classes for data modeling.

---

## ✅ 8. Sealed Classes (Java 17)

```java
public sealed class Shape permits Circle, Square {
}

public final class Circle extends Shape {
}

public final class Square extends Shape {
}
```

**✅ Explanation:** Sealed classes restrict subclassing for better control and pattern matching safety.

---

## ✅ 9. Removed/Deprecated APIs

| Category        | Details                                              |
|-----------------|------------------------------------------------------|
| Java EE Modules | `javax.xml.bind`, `javax.annotation`, etc. — Removed |
| Deprecated APIs | `Thread.stop()`, `Runtime.runFinalizersOnExit()`     |
| SecurityManager | Deprecated in Java 17                                |

🛠 Run these tools:

```bash
jdeps --multi-release 17 -recursive -summary your-jar.jar
jdeprscan --release 17 your-jar.jar
```

---

## ✅ 10. Performance Improvements

* Garbage Collectors:

    * ZGC (JEP 376)
    * Shenandoah GC (low-latency)
* Elastic Metaspace (JEP 387)
* Faster startup, lower memory overhead

---

## 📋 Migration Checklist

| Step | Action                                                        |
|------|---------------------------------------------------------------|
| ✅ 1  | Upgrade to Java 17 JDK                                        |
| ✅ 2  | Migrate build tools (Maven/Gradle plugins)                    |
| ✅ 3  | Replace removed/unsupported Java EE modules                   |
| ✅ 4  | Refactor deprecated APIs (`finalize()`, `Thread.stop()` etc.) |
| ✅ 5  | Adopt new language features: Records, Pattern Matching, etc.  |
| ✅ 6  | Run `jdeps` / `jdeprscan` to analyze dependencies             |
| ✅ 7  | Test thoroughly with `--enable-preview` if needed             |

---