### 📘 `springboot-2.6-to-3.3-migration

> ⚠️ Spring Boot 3.x requires **Java 17+** and **Jakarta EE 9+**, which introduces namespace changes (
`javax.* ➡️ jakarta.*`).

---

## 🚀 Key Migration Steps

| Step | Description                                                |
|------|------------------------------------------------------------|
| ✅ 1  | Upgrade to Java 17 or above                                |
| ✅ 2  | Update dependencies to Jakarta EE (e.g. `jakarta.servlet`) |
| ✅ 3  | Migrate to Spring Framework 6                              |
| ✅ 4  | Refactor `javax.*` imports to `jakarta.*`                  |
| ✅ 5  | Review removed/deprecated APIs and autoconfigurations      |
| ✅ 6  | Update build plugins (Maven/Gradle)                        |
| ✅ 7  | Adapt security and actuator changes                        |
| ✅ 8  | Run integration tests and fix issues                       |

---

## 📦 Maven/Gradle Upgrade

### Java Version

```xml

<properties>
    <java.version>17</java.version>
</properties>
````

### Spring Boot Version

```xml

<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.3.0</version>
</parent>
```

---

## 💡 Code Changes

### 1. `javax` ➡️ `jakarta`

#### ❌ Spring Boot 2.6

```java
import javax.servlet.http.HttpServletRequest;
```

#### ✅ Spring Boot 3.3

```java
import jakarta.servlet.http.HttpServletRequest;
```

> 🔁 This applies to JPA, Servlet, Validation, and all Jakarta EE modules.

---

### 2. Spring Security Config

#### ❌ Spring Boot 2.6

```java
http
        .authorizeRequests()
  .

antMatchers("/admin").

hasRole("ADMIN")
  .

anyRequest().

authenticated();
```

#### ✅ Spring Boot 3.3

```java
http
        .authorizeHttpRequests(auth ->auth
        .

requestMatchers("/admin").

hasRole("ADMIN")
    .

anyRequest().

authenticated()
  );
```

> `authorizeRequests()` is deprecated in favor of `authorizeHttpRequests()`.

---

### 3. WebSecurityConfigurerAdapter Removed

#### ❌ Spring Boot 2.6

```java

@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) { ...}
}
```

#### ✅ Spring Boot 3.3

```java

@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    return http
            .authorizeHttpRequests(auth -> auth.anyRequest().permitAll())
            .build();
}
```

> 🔁 Use `SecurityFilterChain` beans now; `WebSecurityConfigurerAdapter` is removed.

---

### 4. Validation Changes

#### ❌ Spring Boot 2.6

```java
import javax.validation.constraints.NotNull;
```

#### ✅ Spring Boot 3.3

```java
import jakarta.validation.constraints.NotNull;
```

---

### 5. Spring Boot Admin & Actuator Endpoint Access

#### ❌ 2.6

```properties
management.endpoints.web.exposure.include=*
```

#### ✅ 3.3

```properties
management.endpoints.web.exposure.include=health,info,metrics
```

> Be specific with exposed endpoints; wildcard may not work securely in prod.

---

### 6. Swagger/OpenAPI Compatibility

* Use **springdoc-openapi 2.x** for compatibility with Spring Boot 3.x.

```xml

<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.3.0</version>
</dependency>
```

---

### 7. Jackson Date-Time Changes

#### ❌ 2.6

```java
ObjectMapper mapper = new ObjectMapper();
mapper.

disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
```

#### ✅ 3.3

Ensure you use `JavaTimeModule` with updated behavior.

```java
mapper.registerModule(new JavaTimeModule());
```

---

## 🔥 Removed/Changed Features

| Feature                         | Status                   |
|---------------------------------|--------------------------|
| WebSecurityConfigurerAdapter    | ❌ Removed                |
| `javax.*` imports               | ❌ Removed                |
| `authorizeRequests()`           | ❌ Deprecated             |
| `spring-boot-devtools-remote`   | ❌ Removed                |
| Gradle Plugin (`spring-boot`)   | ✅ Updated to 3.x         |
| Spring Data REST Default Paging | Changed default behavior |

---

## 🔧 Migration Tools

* **[Spring Boot Migrator](https://github.com/spring-projects-experimental/spring-boot-migrator)** (Experimental)
* `jdeps` / `jdeprscan` (Java tools to check dependencies)
* IDE Find/Replace: `javax.` → `jakarta.`

---

## ✅ Final Migration Checklist

| Task                                                                  | Status |
|-----------------------------------------------------------------------|--------|
| 🔲 Upgrade Java to 17+                                                |        |
| 🔲 Upgrade to Spring Boot 3.3                                         |        |
| 🔲 Replace all `javax.*` imports                                      |        |
| 🔲 Replace `WebSecurityConfigurerAdapter`                             |        |
| 🔲 Replace deprecated Spring Security APIs                            |        |
| 🔲 Replace `@Validated`, `@NotNull`, etc. from `jakarta.validation.*` |        |
| 🔲 Upgrade `springdoc-openapi` if using Swagger                       |        |
| 🔲 Run all unit/integration tests                                     |        |

---