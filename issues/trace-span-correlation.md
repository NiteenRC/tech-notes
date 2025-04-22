# Distributed Tracing: `traceId`, `spanId`, and `correlationId`

## Key Differences

| Concept         | `traceId`                                             | `spanId`                                            | `correlationId`                                            |
|-----------------|------------------------------------------------------|----------------------------------------------------|-----------------------------------------------------------|
| **Scope**       | Entire request across services                       | Single service operation                           | Cross-service log correlation                            |
| **Uniqueness**  | Same across services involved in the request         | Unique per operation                                | Unique per request, used across systems                   |
| **Purpose**     | Links activities across microservices                 | Tracks performance of individual operations         | Correlates logs across services                          |

## Visual Representation

```
traceId: abc123         (Global Request ID)

┌───────────────┐
│ Span 1        │ spanId: A1 (API Gateway)
├───────────────┤
│ → Span 2      │ spanId: B1 (Auth Service)
│ → Span 3      │ spanId: C1 (Order Service)
│   → Span 4    │ spanId: C2 (Order → DB)
│ → Span 5      │ spanId: D1 (Payment Service)
└───────────────┘

correlationId: req-001234
```

## Use Cases

- **`traceId`**: Tracks the entire request journey across services.
- **`spanId`**: Represents a single operation within the request (service-level).
- **`correlationId`**: Used to correlate logs across different services, not tied to tracing.

## Example

- A request flows from **API Gateway** → **Auth Service** → **Order Service** → **Payment Service**.
- Each service logs with `traceId`, and each operation within a service has its own `spanId`. The `correlationId` ties logs across systems for easy traceability.

## Where to Use

- **`traceId` & `spanId`**: Distributed tracing systems like Jaeger, Zipkin.
- **`correlationId`**: In centralized logging systems (ELK Stack, Splunk) for cross-service log correlation.

## Spring Boot Example

```properties
spring.sleuth.sampler.probability=1.0
spring.cloud.sleuth.traceId128=true
```

Logs:

```json
{
  "traceId": "abc123",
  "spanId": "a1f2",
  "correlationId": "req-001234",
  "serviceName": "order-service",
  "message": "Order request received"
}
```

## Summary

| **Concept**     | **Use Case**                         | **Where It’s Found**           |
|-----------------|--------------------------------------|--------------------------------|
| **`traceId`**   | Tracks the entire request journey    | Distributed tracing systems    |
| **`spanId`**    | Tracks a single service operation    | In service logs                |
| **`correlationId`** | Correlates logs across services      | Centralized logging platforms |
```