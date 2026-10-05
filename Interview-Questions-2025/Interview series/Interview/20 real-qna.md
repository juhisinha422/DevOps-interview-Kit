# Here are 20 real DevOps questions asked in interviews

---

## 1. How does Kubernetes decide which node to schedule a pod on?

* Scheduler checks:

  * Resource availability (CPU/Memory)
  * Node affinity/anti-affinity
  * Taints & tolerations
  * Pod affinity rules
  * Node conditions
* Best suitable node gets selected

---

## 2. What happens internally when you run kubectl apply?

1. Request sent to API Server
2. Object validated
3. Stored in etcd
4. Scheduler assigns node
5. Kubelet creates pod/container
6. Desired state continuously monitored

---

## 3. How does Kubernetes service discovery work?

* Services get DNS entries via CoreDNS
* Pods communicate using:

```text id="u9"
service-name.namespace.svc.cluster.local
```

* kube-proxy routes traffic to matching pods

---

## 4. What is the difference between readiness and liveness probes internally?

| Probe     | Purpose                              |
| --------- | ------------------------------------ |
| Readiness | Checks if pod can receive traffic    |
| Liveness  | Checks if container is healthy/alive |

* Failed readiness → removed from service endpoints
* Failed liveness → container restarted

---

## 5. How does Horizontal Pod Autoscaler (HPA) make scaling decisions?

* Reads metrics from Metrics Server
* Compares against target CPU/memory
* Increases/decreases replicas automatically

---

## 6. What happens internally when you run docker run?

1. Pull image if absent
2. Create container layer
3. Configure namespaces/cgroups
4. Start container process
5. Attach networking/storage

---

## 7. How does Docker layer caching work?

* Each Dockerfile instruction creates layer
* Reuses unchanged layers during rebuild
* Improves build speed

---

## 8. How does Terraform dependency graph (DAG) work internally?

* Terraform builds Directed Acyclic Graph (DAG)
* Determines resource dependency order
* Executes independent resources in parallel

---

## 9. How does Terraform handle state locking and consistency?

* Uses backend locking (e.g., DynamoDB)
* Prevents simultaneous modifications
* Ensures consistent infra state

---

## 10. What happens internally in a CI/CD pipeline from commit → deploy?

1. Code commit triggers webhook
2. CI pipeline starts
3. Build + test executed
4. Docker image built
5. Image pushed to registry
6. CD deploys to environment
7. Monitoring/validation performed

---

## 11. How does a pipeline handle parallel jobs and dependencies?

* Independent stages run in parallel
* Dependent jobs wait for prerequisite completion
* DAG/workflow engine manages execution order

---

## 12. How does AWS Load Balancer route traffic?

* Receives incoming request
* Health checks target instances/pods
* Routes traffic using listeners/rules
* Supports round-robin/path-based routing

---

## 13. What happens internally when you hit a CloudFront URL?

1. DNS resolves to nearest edge location
2. CloudFront checks cache
3. If cached → returns content
4. Else fetches from origin server
5. Caches response for future requests

---

## 14. How does DNS resolution work step by step?

1. Browser cache check
2. OS cache check
3. Resolver query
4. Root DNS server
5. TLD server
6. Authoritative DNS server
7. IP returned to browser

---

## 15. How does Git merge and rebase differ internally?

| Merge                     | Rebase           |
| ------------------------- | ---------------- |
| Preserves history         | Rewrites history |
| Creates merge commit      | Linear history   |
| Safer for shared branches | Cleaner history  |

---

## 16. How does Kubernetes handle pod failures and self-healing?

* Kubelet detects failure
* Controller notices desired vs actual state mismatch
* Pod recreated automatically

---

## 17. What happens during a rolling deployment in Kubernetes?

* New pods created gradually
* Old pods terminated slowly
* Traffic shifted incrementally
* Zero/minimal downtime maintained

---

## 18. How do logs, metrics, and traces work together in observability?

| Component | Purpose                      |
| --------- | ---------------------------- |
| Logs      | Detailed event records       |
| Metrics   | Numerical performance data   |
| Traces    | Request flow across services |

* Together they improve troubleshooting

---

## 19. What happens when your system goes down - how do you approach it?

1. Check alerts/logs
2. Identify blast radius
3. Verify infra/app/network
4. Restore service quickly
5. Perform RCA
6. Add preventive actions

---

## 20. What are the most common production mistakes in DevOps setups?

* No monitoring/alerts
* Hardcoded secrets
* No backups
* Wrong resource sizing
* Missing rollback plan
* Poor access control
* No IaC versioning

---
