# 🚀 Blue-Green Deployment Strategy (Kubernetes + Spring Boot)

## 📌 Objective

Implement **zero-downtime deployment** for a Spring Boot application using the **Blue-Green strategy** in Kubernetes.

---

## 🔁 Deployment Flow

1. **Blue version (v1)** is running and serving production traffic.
2. Deploy **Green version (v2)** side-by-side (same app, different version).
3. Run **integration and smoke tests** on Green (v2).
4. If stable, switch traffic from Blue to Green.
5. Rollback? Revert the traffic routing to Blue.

---

## 📂 Directory Structure

```

k8s/
├── myapp-blue.yaml
├── myapp-green.yaml
└── myapp-service.yaml

````

---

## 🧩 Kubernetes YAML Files

### 🔹 Blue Deployment

```yaml
# myapp-blue.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-blue
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
      version: blue
  template:
    metadata:
      labels:
        app: myapp
        version: blue
    spec:
      containers:
        - name: myapp
          image: myregistry/myapp:v1
          ports:
            - containerPort: 8080
````

---

### 🔹 Green Deployment

```yaml
# myapp-green.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-green
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
      version: green
  template:
    metadata:
      labels:
        app: myapp
        version: green
    spec:
      containers:
        - name: myapp
          image: myregistry/myapp:v2
          ports:
            - containerPort: 8080
```

---

### 🔹 Traffic Service (Switchable)

```yaml
# myapp-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
    version: blue  # ✅ Switch to "green" for cutover
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: LoadBalancer
```

---

## 🔀 Switching Traffic

To route production traffic from **Blue to Green**:

```bash
kubectl patch svc myapp-service -p '{"spec":{"selector":{"app":"myapp","version":"green"}}}'
```

To rollback (route back to Blue):

```bash
kubectl patch svc myapp-service -p '{"spec":{"selector":{"app":"myapp","version":"blue"}}}'
```

---

## ✅ Health Check Before Cutover

```bash
kubectl port-forward deployment/myapp-green 8081:8080
curl http://localhost:8081/actuator/health
```

Monitor:

* Logs (e.g., via `kubectl logs`)
* Prometheus + Grafana dashboards
* ELK / Loki logs
* App-specific metrics and traces

---

## 🔥 Cleanup (Optional)

Once Green is stable and serving traffic:

```bash
kubectl delete deployment myapp-blue
```

---

## 🔐 Best Practices

* Use **Helm** to template and manage versions.
* Integrate with **CI/CD pipelines** for auto-promotion.
* Use **Istio or NGINX Ingress** for advanced traffic routing.
* Gate production cutover with approvals in GitOps tools (e.g., ArgoCD).
* Monitor rollback safety using feature flags (e.g., Unleash or FF4J).

---

## 📎 References

* [Kubernetes Blue-Green Deployment](https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/)
* [Spring Boot Actuator Health](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html)

---

## 🧠 Summary

| Category   | Blue-Green Deployment         |
| ---------- | ----------------------------- |
| Downtime   | ❌ Zero                        |
| Rollback   | ✅ Instant                     |
| Complexity | ⚠️ Moderate                   |
| Cost       | 💰 Higher (two versions live) |
| Best for   | ✅ Major releases              |

---

## ✅ Interview Preparation for Blue-Green Deployment

### 1. **What is Blue-Green Deployment?**

* **Blue-Green Deployment** involves having two identical environments: one (Blue) that’s live, and the other (Green)
  that hosts the new version. When Green is validated, traffic is switched over.

### 2. **Why would you use Blue-Green Deployment?**

* It provides **zero downtime** during releases.
* It allows for **instant rollback** if something goes wrong with the new version.

### 3. **What is the challenge of Blue-Green in a production environment?**

* Managing **DB schema changes** while ensuring compatibility with both versions.
* Handling the **cost** of maintaining two full environments.
* **Monitoring and observability** are crucial to ensure the Green environment is stable before switching.

### 4. **How do you manage database migrations in Blue-Green?**

* The database schema must be **backward-compatible**. Any schema changes should not break Blue (v1).
* For major schema changes, **use versioning tools like Flyway** or **Liquibase**.

### 5. **How do you test the Green environment before switching?**

* **Canary tests** are a good approach: route a small portion of traffic to Green and monitor it for issues.
* Use **Istio’s observability tools** (like telemetry) along with **Prometheus/Grafana** to monitor performance, errors,
  and latency.

### 6. **What are the risks of Blue-Green Deployment?**

* **Stateful applications** might face issues since both Blue and Green must access the same database state.
* **Infrastructure cost** can be high due to maintaining two identical environments.

---

## 🎤 Real-world Interview Questions

1. **What are the key differences between Blue-Green Deployment and Canary Deployment?**
2. **How would you handle schema changes between Blue and Green in a microservice architecture?**
3. **How does Istio assist in the Blue-Green deployment process?**
4. **Can you implement Blue-Green with CI/CD tools like Jenkins or ArgoCD? How?**
5. **How would you handle rollback if Green fails after 100% traffic has been routed to it?**
6. **What’s the difference between Blue-Green and Rolling Deployment?**

---

## ✅ Conclusion

Blue-Green Deployment is a powerful strategy for zero-downtime releases and fast rollback capabilities. With tools like
**Kubernetes**, **Istio**, and **CI/CD integration**, you can ensure a smooth and safe transition to new application
versions in production.

Always keep in mind:

* **Database compatibility** is critical.
* **Monitoring** is essential for a safe and smooth transition.
* **Automation** via CI/CD pipelines makes this process repeatable and error-free.

---