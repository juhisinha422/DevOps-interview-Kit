# Kubernetes Interview Questions & Answers (4 Years Experience)

This document contains **detailed, interview-ready answers** for Kubernetes questions commonly asked for **3–5 years experienced DevOps engineers**. Answers focus on **real-world troubleshooting, architecture decisions, and production best practices**.

---

## 1. Pod stuck in CrashLoopBackOff but logs show no errors

**Explanation:**
CrashLoopBackOff means the container starts and crashes repeatedly. Current logs may be empty because the container exits quickly.

**Debugging Approach:**

1. Check previous container logs:

   ```bash
   kubectl logs <pod-name> --previous
   ```
2. Describe the pod to inspect exit codes and events:

   ```bash
   kubectl describe pod <pod-name>
   ```
3. Look for common causes:

   * Liveness probe failures
   * OOMKilled (exit code 137)
   * Misconfigured startup command or entrypoint
   * Missing environment variables or secrets
   * Incorrect ConfigMap mounts
4. Validate resource limits (memory/CPU).
5. Run the image locally to reproduce the issue.

**Resolution:**
Fix probes, increase resources, correct startup logic, or fix configuration issues.

---

## 2. StatefulSet pod not recreating after deletion

**Possible Reasons:**

* PVC is bound but volume is not attaching
* Storage backend issue (EBS / CSI / NFS)
* Pod ordinal mismatch
* Node where volume was attached is unavailable

**Safe Fix (Without Data Loss):**

1. Never delete the PVC.
2. Verify PVC-to-pod binding.
3. Check volume attachment in storage backend.
4. Delete only the pod, not the StatefulSet.
5. Manually detach and reattach volume if required.

**Key Concept:**
StatefulSets guarantee stable identity and storage persistence.

---

## 3. Cluster Autoscaler not scaling with Pending pods

**Investigation Steps:**

1. Describe the pending pod to identify scheduling errors.
2. Check for:

   * NodeSelectors / Affinity rules
   * Missing tolerations
   * Insufficient resources
   * hostPort usage
3. Review cluster autoscaler logs.
4. Verify node group max size and instance types.

**Important Note:**
Autoscaler scales based on **resource requests**, not limits.

---

## 4. NetworkPolicy blocking cross-namespace traffic

**Design Strategy:**

* Default deny traffic per namespace
* Allow only required traffic using podSelector and namespaceSelector
* Restrict ports and protocols

**Debugging:**

* Ensure CNI supports NetworkPolicies
* Describe policies and verify selectors
* Temporarily allow all traffic to isolate the issue
* Test connectivity using debug pods

---

## 5. Connecting to external DB via VPN inside cluster

**Architecture:**

* Deploy VPN gateway pods with HA and anti-affinity
* Route DB traffic through VPN subnet
* Store credentials in Kubernetes Secrets
* Apply NetworkPolicies to restrict access

**Security:**

* TLS encryption
* No public DB exposure
* Least privilege egress rules

---

## 6. Multi-tenant workloads on a single EKS cluster

**Isolation Strategy:**

* Namespace per tenant
* RBAC for access control
* NetworkPolicies for traffic isolation
* ResourceQuotas and LimitRanges
* Pod security standards
* Tenant-level observability using labels

---

## 7. Kubelet restarting continuously on a node

**Troubleshooting Steps:**

1. Check kubelet logs using journalctl.
2. Verify disk, memory, and CPU usage.
3. Check container runtime health.
4. Drain the node safely.
5. Fix root cause and rejoin node if required.

---

## 8. Pod evicted due to node pressure

**Causes:**

* Memory pressure
* Disk pressure
* No eviction priority

**Prevention:**

* Set proper requests and limits
* Use Guaranteed QoS for critical workloads
* Implement PodDisruptionBudgets
* Use dedicated nodes for critical services

---

## 9. TCP and UDP on the same port

**Solution:**

* Create two Services (one TCP, one UDP)
* Use same selectors and ports
* Use LoadBalancer or NodePort (Ingress is HTTP-only)

---

## 10. Downtime during rolling updates

**Advanced Strategies:**

* Correct readiness probes
* PreStop hooks for graceful shutdown
* Increase terminationGracePeriodSeconds
* Blue-Green or Canary deployments
* Traffic shifting via Ingress or service mesh

---

## 11. Service mesh sidecar consuming more resources

**Analysis:**

* Inspect Envoy metrics
* Analyze traffic patterns

**Optimization:**

* Disable unused mesh features
* Tune sidecar resource limits
* Reduce unnecessary mTLS

---

## 12. Designing a Kubernetes Operator

**CRD Design:**

* Spec defines desired state
* Status reflects actual state

**Controller Logic:**

1. Watch CR changes
2. Compare desired vs actual state
3. Reconcile resources
4. Update status
5. Ensure idempotency

---

## 13. High disk IO due to container logs

**Solutions:**

* Enable log rotation
* Limit container log size
* Use centralized logging (ELK / Loki)
* Avoid excessive stdout logging

---

## 14. etcd performance degradation

**Root Causes:**

* High write volume
* Large number of objects
* Slow disk or network latency

**Best Practices:**

* SSD-backed storage
* 3 or 5 node etcd cluster
* Regular defragmentation
* Frequent backups

---

## 15. Enforcing trusted container registries

**Implementation:**

* Admission controllers (OPA Gatekeeper / Kyverno)
* Validate image registry prefixes
* Deny non-compliant pods
* Use private registry and imagePullSecrets

---

## 16. Multi-region deployments with single control plane

**Challenges:**

* API latency
* etcd quorum risks
* Network partitions

**Best Practice:**

* Prefer multi-cluster per region
* Use GitOps for consistency
* Global load balancer
* Disaster recovery planning

---

### End of Document
