Here's the `README.md` file for your resilience4j configuration:

# Resilience4j Configuration

This project demonstrates the use of Resilience4j to add fault tolerance to microservices. The configuration includes CircuitBreaker, Retry, Bulkhead, RateLimiter, and TimeLimiter for the `productService` service.

## Configuration Overview

### 1. CircuitBreaker
CircuitBreaker is used to detect failures and prevent system overload by preventing excessive requests to a failing service.

```yaml
circuitbreaker:
  instances:
    productService:
      registerHealthIndicator: true
      slidingWindowSize: 10
      minimumNumberOfCalls: 5
      failureRateThreshold: 50
      waitDurationInOpenState: 5s
      permittedNumberOfCallsInHalfOpenState: 2
      automaticTransitionFromOpenToHalfOpenEnabled: true
      recordExceptions:
        - org.example.exceptions.DownstreamServiceException
        - java.io.IOException
      ignoreExceptions:
        - org.example.exceptions.BusinessValidationException
```

**Parameters:**
- `registerHealthIndicator`: Registers a health indicator for monitoring the circuit breaker state.
- `slidingWindowSize`: Size of the sliding window for tracking requests.
- `minimumNumberOfCalls`: Minimum number of calls to consider before calculating failure rate.
- `failureRateThreshold`: Failure rate threshold to open the circuit breaker.
- `waitDurationInOpenState`: Duration the circuit breaker will stay open before transitioning to half-open state.
- `permittedNumberOfCallsInHalfOpenState`: Number of calls allowed in the half-open state.
- `automaticTransitionFromOpenToHalfOpenEnabled`: Enables automatic transition from open to half-open state.
- `recordExceptions`: List of exceptions that will trigger the circuit breaker.
- `ignoreExceptions`: List of exceptions that will be ignored by the circuit breaker.

### 2. Retry
Retry allows the system to retry failed operations a defined number of times before giving up.

```yaml
retry:
  instances:
    productService:
      maxAttempts: 3
      waitDuration: 1s
      retryExceptions:
        - java.io.IOException
      ignoreExceptions:
        - org.example.exceptions.ValidationException
```

**Parameters:**
- `maxAttempts`: Maximum number of retry attempts.
- `waitDuration`: Wait duration between retry attempts.
- `retryExceptions`: List of exceptions that will trigger a retry.
- `ignoreExceptions`: List of exceptions that will be ignored and not trigger a retry.

### 3. Bulkhead
Bulkhead provides isolation between different parts of the system to prevent one failure from affecting other parts.

```yaml
bulkhead:
  instances:
    productService:
      maxThreadPoolSize: 5
      coreThreadPoolSize: 3
      queueCapacity: 10
      keepAliveDuration: 10s
```

**Parameters:**
- `maxThreadPoolSize`: Maximum number of threads in the pool.
- `coreThreadPoolSize`: Core number of threads in the pool.
- `queueCapacity`: Maximum capacity of the queue.
- `keepAliveDuration`: Duration after which idle threads are terminated.

### 4. RateLimiter
RateLimiter is used to limit the rate of requests to the service to prevent overloading.

```yaml
ratelimiter:
  instances:
    productService:
      limitForPeriod: 10
      limitRefreshPeriod: 1s
      timeoutDuration: 500ms
```

**Parameters:**
- `limitForPeriod`: Maximum number of requests allowed in each period.
- `limitRefreshPeriod`: Duration of each period.
- `timeoutDuration`: Timeout duration for acquiring a permit.

### 5. TimeLimiter
TimeLimiter is used to impose a timeout on operations, and can cancel the running future if the timeout is exceeded.

```yaml
timeLimiter:
  instances:
    productService:
      timeoutDuration: 2s
      cancelRunningFuture: true
```

**Parameters:**
- `timeoutDuration`: Maximum time allowed for the operation.
- `cancelRunningFuture`: Determines whether the future should be canceled if the operation times out.

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

## Monitoring

You can monitor the state of the resilience4j components (CircuitBreaker, Retry, etc.) using health indicators and metrics exposed via Actuator. Ensure the following is added in your configuration:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: "health,metrics"
```

For CircuitBreaker monitoring, you can access the health of all circuit breakers through the health endpoint.