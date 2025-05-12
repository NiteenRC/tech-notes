### ✅ **1. Deployment vs Service**

| Component  | Purpose                                     |
|------------|---------------------------------------------|
| Deployment | Manages replicas, rolling updates           |
| Service    | Stable networking (ClusterIP, NodePort, LB) |

🔍 **Real Case**:  
Pods changed IPs; external system broke. Switched to using Service DNS: `billing-service.default.svc.cluster.local`.

---

### ✅ **2. Debugging Misbehaving Pods**

🛠️ **Checklist**:

1. `kubectl get/describe pod` – Pod health (`CrashLoopBackOff`, `OOMKilled`)
2. `kubectl logs` – Check logs
3. `kubectl exec` – Inspect pod internals
4. Monitor CPU/Memory – Prometheus/Grafana
5. Validate ConfigMaps/Secrets – Common silent errors

🔍 Example:  
`pdf-generator` failed due to wrong volumeMount → fixed in YAML.

---

### ✅ **3. Zero-Downtime Deployment**

#### 🌀 Rolling Update Strategy

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

#### 📡 Readiness & Liveness Probes

```yaml
readinessProbe:
  httpGet:
    path: /health
    port: 8080

livenessProbe:
  httpGet:
    path: /health
    port: 8080
```

#### 🛑 Graceful Shutdown

```yaml
lifecycle:
  preStop:
    exec:
      command: ["/bin/sh", "-c", "sleep 10"]
```

---

### ✅ **4. Auto-scaling in Kubernetes**

#### 📈 Horizontal Pod Autoscaler (HPA)

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
spec:
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

🔍 **Use Case**: Scale app based on CPU/memory.

---

#### 🧠 Vertical Pod Autoscaler (VPA)

- Adjusts `resources.requests/limits`
- Best for: batch/memory-intensive jobs

🛠 Needs VPA components to be installed.

---

#### 🧱 Cluster Autoscaler (CA)

- Adds/removes nodes based on pod needs
- Works with AWS, GCP, Azure clusters

---

🔄 **Real Example**:

- `pdf-service` scaled from 3 → 12 pods via HPA (CPU > 80%)
- Cluster Autoscaler added 2 nodes
- Readiness probe ensured only healthy pods served traffic

---

### ✅ **5. Canary vs Blue-Green Deployment**

| Strategy | Canary                           | Blue-Green                              |
|----------|----------------------------------|-----------------------------------------|
| Rollout  | Gradual (10%, 50%, 100%)         | Full switch to green after verification |
| Risk     | Low – only a portion is impacted | Low – instant rollback                  |
| Rollback | Reduce traffic %                 | Switch DNS or service label             |
| Best For | Frequent small releases          | Major stable updates                    |

🔍 Example:  
Rolled out `pricing-engine` with 10% traffic, validated, then scaled to 100%.