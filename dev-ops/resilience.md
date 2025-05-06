
# Resilience4j Configuration
This demonstrates the use of **Resilience4j** to add fault tolerance to microservices. The configuration includes **CircuitBreaker**, **Retry**, **Bulkhead**, **RateLimiter**, and **TimeLimiter** for the `productService`.

### 1. CircuitBreaker
**CircuitBreaker** is used to detect failures and prevent system overload by preventing excessive requests to a failing service.

### ✅ **Resilience4j CircuitBreaker YAML Config (Improved)**

```yaml
resilience4j.circuitbreaker:
  instances:
    productService:
      registerHealthIndicator: true                       # Exposes actuator health
      slidingWindowSize: 10                               # Size of sliding window (count-based)
      minimumNumberOfCalls: 5                             # Needs at least 5 calls to evaluate
      failureRateThreshold: 50                            # 50% failure rate trips the breaker
      waitDurationInOpenState: 5s                         # Stay open for 5 seconds before trying half-open
      permittedNumberOfCallsInHalfOpenState: 2            # Allow 2 trial calls in half-open state
      automaticTransitionFromOpenToHalfOpenEnabled: true  # Automatically move to half-open after wait duration
      recordExceptions:
        - org.example.exceptions.DownstreamServiceException
        - java.io.IOException                             # Count these as failures
      ignoreExceptions:
        - org.example.exceptions.BusinessValidationException  # Ignore these in failure count
```

---

### 🔁 **State Transitions Summary**
| State         | Trigger                                                                 |
|---------------|-------------------------------------------------------------------------|
| **CLOSED**     | Normal flow. Failures < 50%.                                            |
| **OPEN**       | ≥50% of last 10 calls failed (excluding ignored exceptions).            |
| **HALF-OPEN**  | After 5s, allow 2 trial calls. If they succeed, go CLOSED, else OPEN.   |

---

### 🔎 **Usage Example**
```java
@CircuitBreaker(name = "productService", fallbackMethod = "fallback")
public Product getProduct(String id) {
    // call downstream service
}

public Product fallback(String id, Throwable t) {
    // fallback logic
}
```

---

### 2. Retry
**Retry** allows the system to retry failed operations a defined number of times before giving up.

```yaml
resilience4j.retry:
  instances:
    productService:
      maxAttempts: 3                   # Total attempts (1 initial + 2 retries)
      waitDuration: 1s                 # Initial wait (backoff starts from here)
      exponentialBackoff:
        enabled: true
        multiplier: 2.0               # Wait doubles: 1s → 2s → 4s
        maxWaitDuration: 5s           # Cap wait at 5s max
        randomizationFactor: 0.5      # Adds jitter (±50%) to prevent retry storm
      retryExceptions:
        - java.io.IOException         # Retry on this
      ignoreExceptions:
        - org.example.exceptions.ValidationException  # Do not retry these
```

---

### 3. Bulkhead
**Bulkhead** provides isolation between different parts of the system to prevent one failure from affecting other parts.

```yaml
resilience4j.bulkhead:
  instances:
    productService:
      maxThreadPoolSize: 5                       # Maximum threads allowed in the pool
      coreThreadPoolSize: 3                      # Core thread pool size (pre-warmed)
      queueCapacity: 10                          # Queue size for waiting tasks
      keepAliveDuration: 10s                     # Idle thread timeout duration
```

---

### 4. RateLimiter
**RateLimiter** is used to limit the rate of requests to the service to prevent overloading.

```yaml
resilience4j.ratelimiter:
  instances:
    productService:
      limitForPeriod: 10                         # Max number of calls allowed per refresh period
      limitRefreshPeriod: 1s                     # Time window to refresh rate limits
      timeoutDuration: 500ms                     # Max time to wait to acquire permission
```

---

### 5. TimeLimiter
**TimeLimiter** is used to impose a timeout on operations and can cancel the running future if the timeout is exceeded.

```yaml
resilience4j.timeLimiter:
  instances:
    productService:
      timeoutDuration: 2s                        # Timeout for the operation to complete
      cancelRunningFuture: true                  # Cancel future task if timeout is exceeded
```

---

## Dependencies

Make sure to include the following dependencies in your `pom.xml` (for Maven) or `build.gradle` (for Gradle):

### Maven
```xml
<dependency>
  <groupId>io.github.resilience4j</groupId>
  <artifactId>resilience4j-spring-boot2</artifactId>
  <version>1.7.0</version>
</dependency>
```

### Gradle
```gradle
implementation 'io.github.resilience4j:resilience4j-spring-boot2:1.7.0'
```

---

## How to Use

1. Add the configuration to your `application.yml` or `application.properties`.
2. Ensure the Resilience4j dependencies are added to your project.
3. Inject the Resilience4j components (e.g., `CircuitBreaker`, `Retry`, `Bulkhead`, `RateLimiter`, `TimeLimiter`) into your service classes.

Example usage in a service class:

```java
@Autowired
private CircuitBreakerRegistry circuitBreakerRegistry;

@Autowired
private RetryRegistry retryRegistry;

@Autowired
private BulkheadRegistry bulkheadRegistry;

@Autowired
private RateLimiterRegistry rateLimiterRegistry;

@Autowired
private TimeLimiterRegistry timeLimiterRegistry;
```

---

## Monitoring

You can monitor the state of the Resilience4j components (CircuitBreaker, Retry, etc.) using health indicators and metrics exposed via Actuator. Ensure the following is added to your configuration:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: "health,metrics"
```

For CircuitBreaker monitoring, you can access the health of all circuit breakers through the health endpoint.