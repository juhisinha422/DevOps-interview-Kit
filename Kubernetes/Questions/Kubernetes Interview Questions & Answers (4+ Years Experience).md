# 📘 Kubernetes Interview Questions & Answers (4+ Years Experience)

---

## 1. What are ResourceQuotas and LimitRanges in Kubernetes, and how do they differ?

**ResourceQuota**: Limits total resource usage (CPU, memory, pods) per namespace.
**LimitRange**: Sets default/min/max limits per container or pod.

👉 Difference:

* ResourceQuota → namespace level
* LimitRange → pod/container level

---

## 2. Suppose a node is running 10 pods and goes into memory pressure. How can we ensure that 3 critical pods are never evicted?

* Assign **higher priorityClass**
* Use **Guaranteed QoS class** (requests = limits)
* Add **pod priority & preemption**

---

## 3. During cluster autoscaling (scale-up / scale-down), how can we make sure that important pods are not terminated or rescheduled unnecessarily?

* Use **PodDisruptionBudget (PDB)**
* Set **priorityClass**
* Use **node affinity / anti-affinity**
* Avoid eviction with proper resource requests

---

## 4. What are taints and tolerations, and how are they used in pod scheduling?

* **Taints** → applied on nodes (restrict scheduling)
* **Tolerations** → applied on pods (allow scheduling)

👉 Used to control which pods can run on specific nodes

---

## 5. If the Kubernetes scheduler goes down, can new pods still be scheduled on nodes using manifests or templates?

No. New pods will remain in **Pending state** until scheduler is back.

---

## 6. What happens when etcd becomes unavailable or goes down in a Kubernetes cluster?

* Cluster state cannot be read/written
* No new deployments/changes
* Existing workloads continue running

---

## 7. Is there any size limitation on ConfigMaps and Secrets in Kubernetes? If yes, why does this limitation exist?

Yes (~1MB).
Reason: Stored in **etcd**, large data affects performance.

---

## 8. How does a pod authenticate with the Kubernetes API server?

Using **Service Account token** mounted inside the pod.

---

## 9. Can you explain the step-by-step process of pod scheduling in Kubernetes?

1. Pod created → Pending
2. Scheduler watches API server
3. Filters nodes (resources, constraints)
4. Scores nodes
5. Selects best node
6. Pod assigned & kubelet starts it

---

## 10. What is the difference between iptables mode and IPVS mode in kube-proxy?

* **iptables** → simple, slower at scale
* **IPVS** → high performance, load balancing optimized

---

## 11. What should be done when the cluster runs out of Pod IP addresses (Pod CIDR exhaustion)?

* Expand CIDR range
* Add new node pools
* Reconfigure cluster networking

---

## 12. What are static pods, and how are they managed?

Pods managed directly by **kubelet**, defined via manifest files on node.

---

## 13. Can static pods be listed using kubectl commands?

Yes, but they appear as **mirror pods**.

---

## 14. Why are pods not scheduled on the control plane (master) node by default?

Because of **taints** to reserve resources for control plane components.

---

## 15. What is a SecurityContext in a pod specification, and what is its purpose?

Defines security settings like:

* runAsUser
* fsGroup
* privileges

---

## 16. How can you rollback a deployment to a previous version in Kubernetes?

```bash
kubectl rollout undo deployment <name>
```

---

## 17. How does scaling work in StatefulSets, and how is it different from Deployments?

* StatefulSet → ordered, unique identity
* Deployment → stateless, random pods

---

## 18. What is a Headless Service, and when would you use it?

Service without ClusterIP.
Used for **direct pod access (DNS-based)** like StatefulSets.

---

## 19. If you run kubectl apply -f pod.yaml -n namespace-A but the pod manifest specifies namespace-B, what will happen?

Namespace in manifest (**namespace-B**) takes precedence.

---

## 20. What are Custom Resource Definitions (CRDs) in Kubernetes?

CRDs allow you to create **custom resources** extending Kubernetes API.

---

