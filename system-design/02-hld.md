# 🏗️ High-Level Design (HLD) Process

## ❓ Question:
**How do you proceed with High-Level Design (HLD) after gathering requirements?**

---

## ✅ Answer:

Once the requirements are finalized and signed off, I proceed with High-Level Design (HLD) using the following steps:

---

### 1. 🔍 Understand the Scope
- Revisit the BRD/FRD to understand:
    - Functional requirements
    - Non-functional requirements
- Identify key modules, user flows, and boundaries.

---

### 2. 🧱 Define System Architecture
- Choose an architecture style:
    - Monolith / Microservices / Event-Driven
- Identify key components:
    - API Gateway
    - Services or Modules
    - Databases
    - Caching Layer
    - Message Brokers (e.g., Kafka/RabbitMQ)
    - External Integrations

---

### 3. 📊 Create HLD Diagrams
- Visualize:
    - System components
    - Interactions & data flow
    - Third-party integrations
    - Deployment architecture (on-prem / cloud)

**Tools:** Draw.io, Lucidchart, Archimate

---

### 4. 🛠️ Choose Technology Stack
- Select based on requirements and team expertise:
    - Example: Java + Spring Boot, PostgreSQL, Redis, Kafka, Docker, Kubernetes

---

### 5. 🧩 Define Data Models
- Identify core entities and their relationships.
- Create high-level schemas or ER diagrams.

---

### 6. ⚙️ Address Non-Functional Aspects
- Plan for:
    - **Performance:** Caching, async processing
    - **Scalability:** Horizontal/vertical scaling
    - **Security:** OAuth2, input validation
    - **Availability:** Load balancing, failover

---

### 7. 📝 Draft HLD Document
Include:
- System overview
- Module responsibilities
- Architecture & Deployment diagrams
- Technology stack
- Key design decisions with reasoning

---

### 8. 🤝 Review with Stakeholders & Architects
- Present the HLD to:
    - Product Owner
    - Architects
    - Tech Leads
- Gather feedback and refine accordingly.

---

### 9. ✅ Final Sign-Off & Next Steps
- Get final approval on HLD.
- Proceed to **Low-Level Design (LLD)** and implementation planning.