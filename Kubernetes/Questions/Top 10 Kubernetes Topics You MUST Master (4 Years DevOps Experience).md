# 🚀 Top 10 Kubernetes Topics You MUST Master (4 Years DevOps Experience)

---

## 1. Kubernetes Architecture (The Real Internals)

**Core Components:**

* API Server → Entry point (all requests go here)
* etcd → Cluster state (source of truth)
* Scheduler → Decides node placement
* Controller Manager → Maintains desired state
* Kubelet → Node agent (talks to API server)

**Failure Scenarios:**

* API Server down → Cluster unusable (no new changes)
* etcd failure → Data loss risk (must have backups)
* Kubelet down → Node becomes NotReady
* Scheduler down → Pods stuck in Pending

**Interview Tip:**
👉 Always explain **flow: kubectl → API Server → etcd → Scheduler → Kubelet**

---

## 2. Pod Lifecycle & Probes

**Lifecycle Phases:**
Pending → Running → Succeeded/Failed

**Common Issue: CrashLoopBackOff**

* App crashing repeatedly
* Causes:

  * Wrong config
  * Port already in use
  * DB connection failure

**Probes:**

* Liveness → Restart container if unhealthy
* Readiness → Controls traffic routing
* Startup → Used for slow-start apps

**Real Scenario:**

* Pod running but not receiving traffic → Readiness probe failing

---

## 3. Scheduler Logic & Node Selection

**How Scheduling Works:**

1. Filter nodes (resources, taints)
2. Score nodes (affinity, load)
3. Select best node

**Key Concepts:**

* Taints → Restrict nodes
* Tolerations → Allow pods on tainted nodes
* Node Affinity → Preferred/required node selection

**Example:**

* Pod scheduled on wrong node → Check:

  * nodeSelector
  * affinity rules
  * taints

---

## 4. ReplicaSets, Deployments & Rollout Strategy

**Flow after `kubectl apply`:**

* Deployment created
* ReplicaSet created
* Pods created

**Strategies:**

* Rolling Update (default)
* Blue-Green → Two environments
* Canary → Gradual rollout

**Key Fields:**

* maxSurge
* maxUnavailable

**Rollback:**

```bash
kubectl rollout undo deployment <name>
```

---

## 5. Networking Deep Dive

**Core Components:**

* CNI → Handles pod networking
* kube-proxy → Handles service routing

**Traffic Flow:**
Pod → Service → kube-proxy → Pod

**Common Issues:**

* DNS failure → CoreDNS issue
* Packet drops → MTU mismatch
* Cross-node failure → CNI issue

**Debug Commands:**

```bash
kubectl exec -it pod -- nslookup service-name
kubectl get pods -o wide
```

---

## 6. Services & Ingress

**Service Types:**

* ClusterIP → Internal
* NodePort → External via node IP
* LoadBalancer → Cloud LB
* Ingress → HTTP routing

**Traffic Flow:**
LB → Ingress → Service → Pod

**Common Issue:**

* Service works but Ingress fails → Check:

  * Ingress controller
  * DNS mapping

---

## 7. Resource Requests, Limits & Throttling

**Concepts:**

* Requests → Minimum guaranteed
* Limits → Maximum allowed

**Issues:**

* CPU throttling → App slow
* Memory limit exceeded → OOMKilled

**QoS Classes:**

* Guaranteed
* Burstable
* BestEffort

**Real Scenario:**

* Pod restarting → Check:

```bash
kubectl describe pod <name>
```

---

## 8. ConfigMaps, Secrets & Environment Management

**Usage:**

* ConfigMaps → Non-sensitive data
* Secrets → Sensitive data (base64 encoded)

**Ways to Inject:**

* Environment variables
* Volume mounts

**Common Issues:**

* Config change not reflected → Pod restart required

**Best Practice:**

* Use immutable ConfigMaps for stability

---

## 9. Troubleshooting & Observability

**Key Commands:**

```bash
kubectl get events
kubectl logs <pod>
kubectl describe pod <pod>
```

**Debug Flow:**

1. Check pod status
2. Check logs
3. Check events
4. Check node status
5. Check network

**Advanced:**

* Check kubelet logs
* Check CNI logs

**Interview Tip:**
👉 Always follow a structured debugging approach

---

## 10. StatefulSets, PVCs & Storage Internals

**StatefulSets:**

* Stable pod identity
* Ordered deployment

**Storage Concepts:**

* PV (Persistent Volume)
* PVC (Persistent Volume Claim)
* StorageClass

**Binding Flow:**
PVC → StorageClass → PV

**Access Modes:**

* ReadWriteOnce
* ReadOnlyMany
* ReadWriteMany

**Common Issues:**

* PVC stuck in Pending → No matching PV
* Volume mount failure → Permission issue

---

## 🔥 Final Interview Tips (4 Years Experience)

* Always explain **WHY**, not just WHAT
* Use **real-world debugging examples**
* Show **end-to-end understanding (flow thinking)**
* Mention **tools you used (kubectl, Helm, ArgoCD, Prometheus)**

---

✅ If you can explain all these with real scenarios → You’re ready for **Cognizant / Capgemini / TCS interviews**
