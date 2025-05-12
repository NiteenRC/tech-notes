## ✅ Interview Answer: How Did You Gather Requirements and Create HLD & LLD?
---

### 📌 Requirement Gathering

I started by closely working with Product Owners, Business Analysts, and Architects to understand both **functional and
non-functional requirements**.  
Key activities included:

- Capturing **user flows**, **system constraints**, and **integration points**.
- Documenting use cases, edge scenarios, and SLAs using **JIRA** and **Confluence**.
- Identifying scalability, availability, and performance expectations.

---

### 📌 High-Level Design (HLD)

Once the requirements were frozen, I transitioned into architectural design with focus on **service decomposition** and
**technology choices**.

#### 🔹 Service Identification

- Applied **Domain-Driven Design (DDD)** to split the system into bounded contexts and define independent microservices.

#### 🔹 Communication Strategy

- **Synchronous (REST)** for real-time, user-interactive operations.
- **Asynchronous (Kafka)** for decoupled workflows like event processing, audit trails, and background jobs.

#### 🔹 Database Decisions

- **PostgreSQL** for transactional consistency and relational modeling.
- **MongoDB** for schema-flexible document storage.
- **Cassandra** for high-throughput, write-intensive, time-series data.

#### 🔹 Infrastructure & Components

- **Redis** for caching to reduce DB load and speed up frequent reads.
- **JWT-based authentication** and **API Gateway** for routing, rate-limiting, and observability.
- Deployed services using **Docker + Kubernetes** with autoscaling, readiness, and liveness probes.

---

### 📌 Low-Level Design (LLD)

After HLD, I worked on the detailed design to guide implementation:

#### 🔹 Technical Breakdown

- Defined **class diagrams**, **method-level interactions**, and **module responsibilities**.
- Created **Swagger/OpenAPI contracts** for all APIs.
- Designed **SQL/NoSQL schemas**, along with indexes and partitioning strategies.

#### 🔹 Design Principles

- Followed **SOLID principles** for modularity and maintainability.
- Applied design patterns like:
    - **Strategy Pattern** for dynamic behavior changes
    - **Factory Pattern** for object creation
    - **Observer Pattern** for async event listeners

#### 🔹 Sequence Diagrams

- Illustrated complete flows from:
    - API Layer → Service Layer → Repository → Event Queue (Kafka) → Consumer
- Included **error handling**, **retry logic**, and **circuit breakers** using **Resilience4j**.

---

### ✅ Outcome

This end-to-end process ensured:

- A **scalable, testable, and loosely coupled** system.
- Clear traceability from business requirements to implementation.
- Faster onboarding, better collaboration, and simplified enhancements.

---