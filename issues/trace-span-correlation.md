# Distributed Tracing: `traceId`, `spanId`, and `correlationId`

## 🔍 Key Differences

| Concept         | `traceId`                                             | `spanId`                                            | `correlationId`                                            |
|-----------------|------------------------------------------------------|----------------------------------------------------|-----------------------------------------------------------|
| **Scope**       | Entire request across multiple services              | Single operation/service call within the request    | Generally used for logging and correlation across logs    |
| **Uniqueness**  | Same across all services involved in the request     | Unique for each operation or service call in the trace | Unique per request but may be used across multiple systems |
| **Purpose**     | Ties together all activities in a distributed request | Tracks the performance, status, and health of one specific operation | Links logs and data between different services/systems |
| **Example**     | API Gateway → Auth → Orders → Payments               | A specific call within the `OrdersService`          | A unique ID used for tracking requests in logs across systems |
| **Used For**    | End-to-end tracking of the request lifecycle         | Profiling a single service call or operation       | Cross-system correlation to trace a single logical flow (not tied to distributed traces) |
| **Parent-Child?** | No (Root identifier for all spans)                  | Yes (Parent-Child relationship for service calls)   | No (Typically independent of the span relationship)       |

## 🧵 Visual Representation

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

correlationId: req-001234 (Used across logs for related activities)
```

- **`traceId`** links all these spans (A1, B1, C1, etc.).
- **`spanId`** uniquely identifies each operation.
- **`correlationId`** is often used across different logs to correlate activities that might span multiple microservices or subsystems.

## 🧠 Detailed Use Cases:

### 1. **`traceId`** — Full Request Journey (Distributed Tracing)
- Tied to the lifecycle of a single request across all microservices.
- All services involved will share the same `traceId`, which helps track the entire journey from entry to response.
- **Example**: A user request starts at the API Gateway → goes to Auth → then Orders → Payment → Response.

### 2. **`spanId`** — Single Operation within the Trace (Service-Level)
- Each individual service call or operation is a **span** with a **`spanId`**.
- Every service or operation within the trace can have a parent-child relationship.
- **Example**: Within `OrderService`, there are spans for connecting to the database, validating the order, etc.

### 3. **`correlationId`** — Cross-System or Cross-Service Correlation (Log-Level)
- **Used across logs or systems** to correlate logs or requests that may not be connected through `traceId` directly.
- Can be passed from one service to another or within multiple systems to tie related logs together.
- **Example**: When different microservices log events related to a specific user or request, the same `correlationId` can be used across logs even if those services aren’t directly traceable via `traceId`.

## 🧰 Practical Example

- **A user makes a request to the API Gateway**. The request is logged with a `traceId` (let’s say `abc123`).
- The **API Gateway** logs the request with a `traceId: abc123` and `spanId: A1`.
- The **Auth Service** receives the request and logs with the same `traceId: abc123`, but its own `spanId: B1`.
- During the request to **Order Service**, we see logs with the same `traceId: abc123` but a new `spanId: C1` and a **`correlationId: req-001234`** passed along to connect logs across different services.
- Now, **Payment Service** logs with `traceId: abc123` and `spanId: D1`.

## 🌐 Where Do They Appear?

- **`traceId` & `spanId`**:
  - Appears in **distributed tracing** systems like Jaeger, Zipkin, and OpenTelemetry.
  - Used in logs and dashboards for tracing performance, errors, and bottlenecks.
  
- **`correlationId`**:
  - Appears in **logs**, especially in systems where logs are manually aggregated or correlated.
  - Essential in **logging frameworks** like ELK Stack (Elasticsearch, Logstash, Kibana) or Splunk, where logs from different systems are stitched together.

## 🛠 Implementation Example (Spring Boot)

In a **Spring Boot** application, using **Spring Cloud Sleuth** to implement `traceId` and `spanId`:

```properties
spring.sleuth.sampler.probability=1.0
spring.cloud.sleuth.traceId128=true
```

**In your logs**:

```json
{
  "traceId": "abc123",
  "spanId": "a1f2",
  "correlationId": "req-001234",
  "serviceName": "order-service",
  "message": "Order request received"
}
```

- **`traceId`** and **`spanId`** will be automatically managed by Spring Cloud Sleuth.
- You can manually log **`correlationId`** based on your application logic and use it to tie related logs across different systems.

## 🧠 Summary Table

| **Concept**     | **Use Case**                                               | **Where It’s Found**                          |
|-----------------|------------------------------------------------------------|----------------------------------------------|
| **`traceId`**   | Tracks entire request journey across services              | Distributed tracing systems (Jaeger, Zipkin) |
| **`spanId`**    | Represents a single operation or service call              | In tracing and logging of microservices      |
| **`correlationId`** | Cross-system or log correlation across services/systems   | Centralized logging platforms (ELK, Splunk) |
```
