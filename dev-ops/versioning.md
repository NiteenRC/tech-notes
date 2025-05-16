# Versioning in AWS Microservices with EKS & GitLab CI/CD

Versioning is a critical practice to ensure reliable deployments, backward compatibility, traceability, and smooth evolution of microservices. Below is a comprehensive guide tailored for AWS EKS deployments using GitLab CI/CD pipelines.

---

## 1. Code & Artifact Versioning

- **Semantic Versioning (SemVer):**  
  Follow the `MAJOR.MINOR.PATCH` format (e.g., `1.4.2`):  
  - **MAJOR:** Incompatible API changes  
  - **MINOR:** Backward-compatible new features  
  - **PATCH:** Backward-compatible bug fixes  

- **Git Tags:**  
  Tag release commits with versions like `v1.4.2` to mark stable points in code history.

- **Docker Image Tags:**  
  Tag Docker images with explicit versions and optionally Git commit hashes, e.g.:  
```

myrepo/myservice:1.4.2
myrepo/myservice:1.4.2-<commit-sha>

```
Avoid using `latest` tag for production deployments to ensure immutability.

- **GitLab CI/CD Integration:**  
Extract version from `pom.xml` or `build.gradle` during the pipeline or rely on Git tags. Use versioned tags for builds and deployments.

---

## 2. API Versioning Strategies

- **URI Versioning (Most Common):**  
Explicitly version your API in the URL path:  
```

GET /api/v1/products
GET /api/v2/products

```

- **Header Versioning:**  
Specify API version via HTTP headers, e.g.,  
```

Accept: application/vnd.myapp.v2+json

````

- **Content Negotiation or Query Parameter Versioning:**  
Use headers or query parameters to specify API versions without changing the URL path.

- **Backward Compatibility & Deprecation:**  
Support multiple API versions simultaneously and plan a clear deprecation strategy with client communication.

---

## 3. Kubernetes Resource Versioning

- **Immutable Docker Image Tags:**  
Always specify fixed image tags in your Kubernetes manifests, e.g.,  
```yaml
spec:
  containers:
    - name: product-service
      image: myrepo/product-service:1.4.2
````

* **Helm Chart Versioning:**
  Maintain `Chart.yaml` version and separate application version.
  Use `values.yaml` to manage image tags per environment and version.

* **GitOps / Manifest Version Control:**
  Store Kubernetes manifests in Git with proper versioning to enable rollbacks and audit trails.

---

## 4. Database Versioning

* Use schema migration tools like **Flyway** or **Liquibase** to version database changes alongside application releases.

* Each app version corresponds to a specific migration version, ensuring schema and app compatibility.

---

## 5. Versioning in GitLab CI/CD Pipelines

Example snippet to handle versioning in CI/CD:

```yaml
variables:
  VERSION: "1.4.2"

build:
  stage: build
  script:
    - ./gradlew clean build

dockerize:
  stage: dockerize
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$VERSION .
    - docker push $CI_REGISTRY_IMAGE:$VERSION

deploy:
  stage: deploy
  script:
    - kubectl set image deployment/product-service product-service=$CI_REGISTRY_IMAGE:$VERSION
  only:
    - main
```

* Extract the `VERSION` dynamically from Git tags or project files during the pipeline.

* Use versioned Docker images for deployments to allow easy rollback by redeploying previous tags.

---

## 6. Best Practices Summary

| Aspect              | Best Practice                                      |
| ------------------- | -------------------------------------------------- |
| **Code versioning** | Semantic versioning + Git tags                     |
| **Docker images**   | Use versioned tags, avoid `latest` in production   |
| **API versioning**  | URI or header-based versioning                     |
| **Kubernetes**      | Immutable image tags, Helm chart versioning        |
| **Database**        | Use Flyway/Liquibase migrations linked to versions |
| **CI/CD pipelines** | Parameterize versions, enable easy rollback        |
| **Deprecation**     | Communicate version changes and retire old APIs    |

---

# Interview Talking Points & Model Answers: Versioning in AWS Microservices

---

### 1. Why is semantic versioning important in microservices?

**Answer:**
Semantic Versioning (SemVer) provides a clear and standardized way to indicate the nature of changes in a microservice version. It helps developers and consumers understand whether a release introduces breaking changes (MAJOR), adds new features without breaking compatibility (MINOR), or just fixes bugs (PATCH). This clarity is crucial in microservices architecture, where many independent services evolve rapidly and need to coordinate changes without breaking dependent services.

---

### 2. How do you handle Docker image versioning and why avoid using the `latest` tag in production?

**Answer:**
Docker images should be tagged with explicit versions (e.g., `myservice:1.4.2`) or commit hashes to ensure immutability and traceability. Using the `latest` tag in production is risky because it can lead to unpredictability in deployments; the image labeled `latest` can change over time, making it hard to track exactly which version is running and complicating rollbacks. Explicit version tags allow precise deployment and rollback to known stable states.

---

### 3. What are the common API versioning strategies? Which one do you prefer and why?

**Answer:**
Common API versioning strategies include:

* **URI versioning:** Embedding the version in the API path, e.g., `/api/v1/products`. This is straightforward and visible to clients.
* **Header versioning:** Using HTTP headers to specify the version, e.g., `Accept: application/vnd.myapp.v2+json`. This keeps URLs clean but requires clients to set headers correctly.
* **Query parameter versioning:** Clients specify version as a query parameter like `?version=2`.

I prefer **URI versioning** for its simplicity and clarity, especially in REST APIs, as it makes versions explicit in URLs, which helps in routing and documentation. Header versioning can be useful for advanced scenarios but adds complexity.

---

### 4. How do you manage Kubernetes manifests and ensure versioned deployments?

**Answer:**
In Kubernetes, deployments should always reference immutable Docker image tags that correspond to a specific service version (e.g., `myservice:1.4.2`). This ensures consistency and repeatability of deployments. We store manifests (or Helm charts) in Git with proper versioning, enabling GitOps workflows and easy rollback by redeploying a previous manifest version. Helm charts have their own versioning, which can be used to package app versions and manage environment-specific configurations via `values.yaml`.

---

### 5. How do database versioning and migrations fit into your deployment process?

**Answer:**
Database schema changes are versioned and managed via tools like **Flyway** or **Liquibase**. These tools maintain a history of migrations, each tied to a version number or timestamp. Application deployments are coordinated with schema migrations to ensure that the database structure is compatible with the app version being deployed. This prevents runtime errors and data inconsistencies. Migration scripts are part of the CI/CD pipeline, applied before or during app rollout.

---

### 6. How do you automate versioning and deployments in your GitLab CI/CD pipelines?

**Answer:**
In GitLab CI/CD, the pipeline extracts the application version either from code metadata (like `pom.xml`) or from Git tags. It uses this version to build, tag, and push Docker images. Deployment stages then deploy the specific versioned image to Kubernetes by updating the deployment manifest with that image tag. This automation ensures that each deployment is traceable to a particular code version and allows easy rollback by redeploying an earlier image tag.

---

### 7. How do you handle version deprecation and client communication?

**Answer:**
When introducing new API versions, I maintain backward compatibility by supporting older versions for a defined period. Clients are informed in advance about deprecation timelines through documentation and release notes. Gradual migration plans and sunset policies help minimize disruption. Monitoring usage of old API versions helps determine when it is safe to retire them.

---