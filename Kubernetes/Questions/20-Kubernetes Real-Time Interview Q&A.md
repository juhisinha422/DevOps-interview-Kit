# 📘 Kubernetes Real-Time Interview Q&A (L2/L3 DevOps Engineer)

---

## 1. A Pod is running but the Service is not accessible. How do you debug this issue?

* Check Service & Pod labels:

  ```bash
  kubectl get svc
  kubectl get pods --show-labels
  ```
* Verify selector matches labels
* Check endpoints:

  ```bash
  kubectl get endpoints <service-name>
  ```
* Test inside cluster:

  ```bash
  kubectl exec -it <pod> -- curl <service-name>:<port>
  ```
* Validate:

  * Port vs targetPort mismatch
  * Service type (ClusterIP/NodePort/LoadBalancer)
  * Ingress rules
  * Network policies / CNI issues

---

## 2. What is CrashLoopBackOff and how do you resolve it?

**Meaning:** Container repeatedly crashes and Kubernetes backs off restarting.

**Debug:**

```bash
kubectl logs <pod> --previous
kubectl describe pod <pod>
```

**Causes:**

* App crash / wrong command
* Missing config or secret
* Dependency unavailable

**Fix:**

* Correct configuration
* Add proper liveness/readiness probes
* Tune `initialDelaySeconds`

---

## 3. What is OOMKilled and how do you prevent it in Kubernetes?

**Meaning:** Container exceeded memory limit and got killed.

**Check:**

```bash
kubectl describe pod <pod>
```

**Prevention:**

* Set proper requests/limits
* Optimize application memory
* Use monitoring to track usage

```yaml
resources:
  requests:
    memory: "256Mi"
  limits:
    memory: "512Mi"
```

---

## 4. How does Kubernetes handle Pod failures automatically?

* Uses controllers like Deployment/ReplicaSet
* Ensures desired state (replica count)
* Restarts containers based on restartPolicy
* Reschedules Pods on healthy nodes

---

## 5. Explain the difference between Deployment and StatefulSet with a use case?.

* **Deployment:** Stateless apps (web apps, APIs)
* **StatefulSet:** Stateful apps (DBs like MySQL, Kafka)

| Feature  | Deployment | StatefulSet        |
| -------- | ---------- | ------------------ |
| Identity | Random     | Stable             |
| Storage  | Shared     | Persistent per pod |
| Scaling  | Parallel   | Ordered            |

---

## 6. How do you perform rolling updates in Kubernetes?

```bash
kubectl set image deployment/app app=nginx:latest
```

* Controlled via strategy:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1
    maxSurge: 1
```

---

## 7. How do you rollback a Kubernetes deployment?

```bash
kubectl rollout undo deployment <name>
kubectl rollout history deployment <name>
```

---

## 8. Why does a Pod remain in Pending state and how do you fix it?

**Reasons:**

* Insufficient resources
* Taints/Tolerations mismatch
* PVC not bound

**Fix:**

```bash
kubectl describe pod <pod>
```

---

## 9. How do you scale applications in Kubernetes?

**Manual:**

```bash
kubectl scale deployment app --replicas=5
```

**Auto:**

* Horizontal Pod Autoscaler (HPA)
* Vertical Pod Autoscaler (VPA)

---

## 10. How does Horizontal Pod Autoscaler work?

* Uses metrics (CPU/Memory)
* Metrics Server collects data
* Scales pods based on threshold

```bash
kubectl autoscale deployment app --cpu-percent=70 --min=2 --max=10
```

---

## 11. What happens when a Kubernetes node goes down?

* Node marked NotReady
* Pods become unavailable
* Controller reschedules Pods to healthy nodes

---

## 12. How do you check logs of a Pod in Kubernetes?

```bash
kubectl logs <pod>
kubectl logs -f <pod>
kubectl logs <pod> --previous
```

---

## 13. How do you expose a Kubernetes application externally?

* NodePort
* LoadBalancer
* Ingress (preferred in production)

---

## 14. How do you manage configuration changes in Kubernetes?

* ConfigMaps
* Environment variables
* Volume mounts

---

## 15. How do you secure access to Kubernetes cluster?

* RBAC (Role-Based Access Control)
* TLS certificates
* IAM (for cloud like AWS EKS)
* API server access restrictions

---

## 16. How do you manage secrets in Kubernetes?

```bash
kubectl create secret generic my-secret --from-literal=key=value
```

* Mount as env variables or volumes
* Use external tools (Vault, AWS Secrets Manager)

---

## 17. How do you monitor a Kubernetes cluster in production?

* Prometheus + Grafana
* ELK stack (logs)
* Alertmanager

---

## 18. How do you debug DNS issues inside Kubernetes?

```bash
kubectl exec -it <pod> -- nslookup kubernetes.default
```

* Check CoreDNS:

```bash
kubectl get pods -n kube-system
```

---

## 19. What happens if a container crashes repeatedly inside a Pod?

* Pod enters CrashLoopBackOff
* Kubernetes retries with backoff
* Requires log/debug analysis to fix root cause

---

## 20. How do you ensure zero-downtime deployments in Kubernetes?

* Rolling updates
* Readiness probes
* Proper resource allocation
* Blue-Green / Canary deployments

---

# ✅ L2/L3 Pro Tips

* Always define **readiness & liveness probes**
* Use **resource limits/requests**
* Implement **monitoring + alerting**
* Use **namespaces for isolation**
* Practice **failure scenarios (chaos testing)**

---
