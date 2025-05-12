### ✅ **1. REST/gRPC vs Kafka/RabbitMQ**

#### 🔄 **Synchronous: REST/gRPC**

> Use when **immediate response is needed** — user-initiated.

- Example use cases:
    - Login, payments, form submission
    - Fetch user profile or transaction history

#### 🔁 **Asynchronous: Kafka/RabbitMQ**

> Use when **response can be delayed**, often for system-to-system.

- Example use cases:
    - Order fulfillment, sending notifications
    - Event streaming, logging, analytics

📌 **Rule**:
> Use **REST/gRPC** for user-facing flows, **Kafka** for internal, decoupled workflows.

---

### ✅ **2. Microservice Communication Resilience**

| Strategy                 | Tool/Concept          | Purpose                    |
|--------------------------|-----------------------|----------------------------|
| Retry + Backoff          | Spring Retry          | Handle transient errors    |
| Circuit Breaker          | Resilience4j, Hystrix | Avoid cascading failures   |
| Idempotency              | Idempotency keys      | Avoid duplicate effects    |
| Async Messaging          | Kafka/RabbitMQ        | Durable, retryable comms   |
| Tracing with Correlation | Sleuth, Zipkin        | End-to-end request tracing |

---

### ✅ **3. Secure Microservice Communication**

| Security Aspect   | Implementation            |
|-------------------|---------------------------|
| Auth              | JWT with Keycloak/Auth0   |
| mTLS              | Istio + SPIRE             |
| Secrets           | Kubernetes Secrets + RBAC |
| Network Isolation | Istio + Network Policies  |

🔐 **Real Example**:  
In a fintech app, internal services used **mTLS + JWT**, enforced by Kong Gateway and Istio.

---

### ✅ **4. Microservices Design Patterns**

| Pattern           | Usage & Tooling                                |
|-------------------|------------------------------------------------|
| API Gateway       | Kong/Istio: Routing, Auth, Rate Limit          |
| Service Discovery | DNS in K8s (`*.svc.cluster.local`)             |
| Circuit Breaker   | Resilience4j: Avoid overload                   |
| Saga Pattern      | Temporal: Orchestrate distributed transactions |
| Bulkhead          | Isolate failure domains                        |
| Strangler Fig     | Gradual monolith-to-microservices migration    |

---

### ✅ **5. Saga Pattern – Real Use Case**

💬 **Your Answer**:
> In `subscription checkout`, we used Saga:
> 1. CreateOrder
> 2. ReserveSlot
> 3. ChargePayment  
     > If payment fails → Cancel reservation and order.

🔍 Used Temporal for orchestration and compensation tracking.

---

### ✅ **6. API Gateway vs Load Balancer**

| Feature          | API Gateway (L7)               | Load Balancer (L4/L7)  |
|------------------|--------------------------------|------------------------|
| Routing          | Path-based (`/orders`, `/pay`) | IP/port-based          |
| Auth, Throttling | Yes                            | No                     |
| Example Tool     | Kong, Istio Gateway            | AWS ALB, NGINX Ingress |

---

### ✅ **7. Reliability Examples**

| Feature         | Tool         | Real Use Case                              |
|-----------------|--------------|--------------------------------------------|
| Retry           | Spring Retry | Retry Kafka publish                        |
| Circuit Breaker | Resilience4j | Avoid dependency crashing `recommendation` |
| Rate Limiting   | Kong Plugin  | 100 req/min per IP                         |
| Fallback        | Cache Layer  | Show stale data if downstream fails        |

---

### ✅ **8. Distributed Tracing**

💬 **Your Answer**:
> We use **Spring Sleuth + Zipkin** for tracing.
> Each log includes `traceId`/`spanId`, visible in ELK or Grafana Loki.

🔍 Example: Traced failed refund request from UI → refund-controller → null exception.

---

### ✅ **9. Trace IDs Usage**

> Injected on incoming request:
> - `X-B3-TraceId`, `X-B3-SpanId`
> - Helps trace flow: `UI → API GW → Service A → B → C`

---