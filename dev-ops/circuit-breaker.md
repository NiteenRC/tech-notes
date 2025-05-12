## 🚨 Circuit Breaker, Retry, and Rate Limiting Patterns — Interview-Ready Explanation

In distributed systems, failure management, load handling, and service resilience are critical. **Circuit Breaker**, *
*Retry**, and **Rate Limiting** are patterns commonly used to achieve fault tolerance, manage load, and ensure the
smooth functioning of the system.

---

### 🔹 Circuit Breaker Pattern

The **Circuit Breaker** pattern is designed to prevent a failing service from being overwhelmed with requests and
propagating the failure to other parts of the system.

#### How it Works:

- **Closed State**: All requests are allowed to pass to the service, and failures are monitored.
- **Open State**: When the failure threshold is exceeded, the circuit breaker opens, and requests are prevented from
  reaching the failing service.
- **Half-Open State**: After a certain period, the circuit breaker allows a few requests to test if the service has
  recovered. If the requests succeed, it closes; if not, it reverts to the open state.

#### When to Use:

- When service failures can cascade across multiple components.
- When interacting with unreliable or third-party services.

---

### 🔹 Retry Pattern

The **Retry** pattern is used to automatically reattempt a failed operation. It can be used in conjunction with other
patterns like Circuit Breaker.

#### How it Works:

- When an operation fails, a **retry** mechanism triggers, attempting the operation again after a certain interval.
- **Exponential Backoff**: In many implementations, retries are done with increasing delays (e.g., retry 1 second after
  the first failure, 4 seconds after the second, etc.).
- A maximum retry limit is usually set to prevent infinite retries.

#### When to Use:

- When network calls or external service requests might intermittently fail.
- When you want to give a failing service some time to recover before retrying.

---

### 🔹 Rate Limiting Pattern

**Rate Limiting** is used to control the rate at which requests are made to a service, ensuring that a service is not
overwhelmed by too many requests in a short period of time.

#### How it Works:

- **Fixed Window**: Limits requests within a fixed time period (e.g., 100 requests per minute).
- **Sliding Window**: A more flexible approach that counts requests over a moving window of time.
- **Token Bucket**: Allows bursts of requests but enforces a limit on the average rate of requests over time.
- **Leaky Bucket**: Similar to Token Bucket but with a steady output rate, allowing bursts but controlling the rate of
  requests.

#### When to Use:

- To prevent overloading your service or third-party services.
- When managing a public API to ensure fair usage across multiple consumers.

---

### 🔹 Use Case Example

Imagine an **e-commerce platform** with a **payment service**:

- **Circuit Breaker**: If the payment service goes down, the circuit breaker will open to stop further requests,
  preventing unnecessary retries and reducing load on the failing service.
- **Retry**: If the payment service fails due to transient errors, the retry pattern would attempt to reprocess the
  payment request after a brief delay, potentially with increasing intervals.
- **Rate Limiting**: To ensure the payment service doesn't get overwhelmed, you apply rate limiting on payment requests,
  allowing only a certain number of requests per minute.

---

### 🔹 Benefits and Trade-Offs

#### Circuit Breaker:

- **Pros**: Prevents cascading failures, enhances system resilience.
- **Cons**: Overuse can lead to unnecessary blocking of requests.

#### Retry:

- **Pros**: Increases the likelihood of success for transient errors.
- **Cons**: Could create unnecessary load on the failing service if not configured properly.

#### Rate Limiting:

- **Pros**: Protects services from overuse, ensures fair usage.
- **Cons**: Could impact user experience if the limits are too restrictive.

---

### 🔹 Interview Questions You Might Face

**Q1. How would you handle retries in combination with circuit breakers?**
> A good approach is to implement retries **before** the circuit breaker. If the retries fail, the circuit breaker opens
> to prevent further strain on the system.

**Q2. How does rate limiting impact user experience?**
> Rate limiting ensures system stability but can frustrate users if not configured well. You can use strategies like *
*Graceful Degradation** or **Rate Limit Headers** to inform users about their rate limits.

**Q3. Can these patterns be combined?**
> Yes, these patterns are often used together. For example, you can use **Circuit Breaker** and **Retry** together,
> while applying **Rate Limiting** to prevent the system from being overwhelmed.

---

### 🧠 Summary Cheat Sheet

| Pattern             | Description                                                | Use Case                                                      |
|---------------------|------------------------------------------------------------|---------------------------------------------------------------|
| **Circuit Breaker** | Monitors service failures and isolates them when necessary | Prevents cascading failures in distributed systems            |
| **Retry**           | Automatically retries failed operations                    | For transient failures, like network or database hiccups      |
| **Rate Limiting**   | Controls the rate of requests to avoid service overload    | Protects services from being overwhelmed by too many requests |

---