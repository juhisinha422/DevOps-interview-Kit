# 🚀 Advanced Kubernetes Interview Guide (Architecture + Troubleshooting)

---

## 1. Your pod keeps getting stuck in CrashLoopBackOff, but logs show no errors. How would you approach debugging and resolution?

When logs show no errors, the issue is usually outside application logic.

**Step-by-step approach:**

* Check pod events:
  kubectl describe pod <pod>
  → Look for OOMKilled, probe failures, image issues

* Check previous container logs:
  kubectl logs <pod> --previous

* Validate probes:

  * Liveness/readiness misconfiguration can restart container
  * Test endpoints manually using curl inside container

* Check resource limits:

  * If memory limit is too low → container killed silently

* Check entrypoint/command:

  * Wrong command may exit immediately

* Run container interactively:
  kubectl run debug --rm -it --image=<image> -- /bin/sh

* Verify dependencies:

  * DB/API connectivity failures can cause silent exit

**Resolution:**
Fix probe configs, increase resources, correct startup command, or dependency issues.

---

## 2. You have a StatefulSet deployed with persistent volumes, and one of the pods is not recreating properly after deletion. What could be the reasons, and how do you fix it without data loss?

**Possible causes:**

* PVC is stuck in “Terminating” or not bound
* Storage class issue
* Volume attachment failure
* Pod identity mismatch (StatefulSet uses ordinal index)

**Debug steps:**

* kubectl get pvc
* kubectl describe pvc
* Check events for volume attach errors
* Verify storage backend (EBS/EFS etc.)

**Fix without data loss:**

* Do NOT delete PVC
* Manually delete stuck pod (StatefulSet will recreate)
* Ensure same PVC is reattached
* If needed, rebind PVC using same name

---

## 3. Your cluster autoscaler is not scaling up even though pods are in Pending state. What would you investigate?

**Key checks:**

* kubectl describe pod → check scheduling reason
* Node group limits reached?
* Resource requests too high?
* Incorrect labels/taints preventing scheduling
* Autoscaler logs

**Common issues:**

* Pods requesting more resources than node capacity
* Missing IAM permissions (EKS)
* Node group scaling disabled

---

## 4. A network policy is blocking traffic between services in different namespaces. How would you design and debug the policy to allow only specific communication paths?

**Debug:**

* Check existing network policies
* Use kubectl describe networkpolicy
* Test connectivity using curl or nc

**Design:**

* Allow ingress from specific namespace using labels:

  * namespaceSelector
  * podSelector

**Best practice:**

* Default deny all
* Explicitly allow required traffic only

---

## 5. One of your microservices has to connect to an external database via a VPN inside the cluster. How would you architect this in Kubernetes with HA and security in mind?

**Architecture:**

* Use VPC/VPN gateway
* Private endpoint for DB
* Route traffic via private subnet

**Kubernetes side:**

* Use Service + DNS
* Secure credentials via Secrets
* Use Network Policies to restrict access

**HA:**

* Multi-AZ nodes
* DB failover setup
* Retry logic in application

---

## 6. You're running a multi-tenant platform on a single EKS cluster. How do you isolate workloads and ensure security, quotas, and observability for each tenant?

**Isolation:**

* Separate namespaces per tenant
* Network policies for isolation
* RBAC per tenant

**Resource control:**

* ResourceQuota
* LimitRange

**Security:**

* Pod Security Standards
* IAM roles for service accounts

**Observability:**

* Label-based monitoring
* Separate dashboards/log streams

---

## 7. You notice the kubelet is constantly restarting on a particular node. What steps would you take to isolate the issue and ensure node stability?

**Steps:**

* Check kubelet logs:
  journalctl -u kubelet
* Check node resources (CPU/memory/disk)
* Check disk pressure or inode exhaustion
* Verify container runtime (Docker/containerd)
* Check config issues

**Fix:**

* Restart kubelet
* Fix resource issues
* Drain node if needed:
  kubectl drain <node>

---

## 8. A critical pod in production gets evicted due to node pressure. How would you prevent this from happening again, and how do QoS classes play a role?

**Prevention:**

* Define requests and limits properly
* Use Guaranteed QoS (requests = limits)
* Use dedicated nodes for critical workloads
* Enable autoscaling

**QoS role:**

* Guaranteed → least likely to be evicted
* BestEffort → first to be evicted

---

## 9. You need to deploy a service that requires TCP and UDP on the same port. How would you configure this in Kubernetes using Services and Ingress?

**Approach:**

* Create service with multiple ports:

  * protocol: TCP
  * protocol: UDP

Example:

* Same port with different protocols

**Note:**

* Ingress typically supports HTTP/HTTPS only
* For TCP/UDP → use LoadBalancer or NodePort

---

## 10. An application upgrade caused downtime even though you had rolling updates configured. What advanced strategies would you apply to ensure zero-downtime deployments next time?

**Improvements:**

* Use readiness probes correctly
* Increase maxUnavailable = 0
* Use maxSurge > 0
* Implement blue-green or canary deployments
* Use service mesh for traffic shifting

---

## 11. Your service mesh sidecar (e.g., Istio Envoy) is consuming more resources than the app itself. How do you analyze and optimize this setup?

**Analysis:**

* Check metrics (CPU/memory)
* Review traffic volume
* Check logging verbosity

**Optimization:**

* Reduce logging level
* Tune resource limits
* Disable unnecessary features
* Use sidecar injection selectively

---

## 12. You need to create a Kubernetes operator to automate complex application lifecycle events. How do you design the CRD and controller loop logic?

**CRD Design:**

* Define custom resource schema
* Include spec (desired state) and status

**Controller Logic:**

* Watch resource changes
* Compare desired vs actual state
* Reconcile differences

**Tools:**

* Operator SDK
* Kubebuilder

---

## 13. Multiple nodes are showing high disk IO usage due to container logs. What Kubernetes features or practices can you apply to avoid this scenario?

**Solutions:**

* Enable log rotation
* Limit log size
* Use centralized logging (ELK/CloudWatch)
* Avoid excessive logging in apps
* Use sidecar logging agents

---

## 14. Your Kubernetes cluster's etcd performance is degrading. What are the root causes and how do you ensure etcd high availability and tuning?

**Root causes:**

* High API server load
* Large number of objects
* Disk latency

**Solutions:**

* Use SSD storage
* Regular compaction
* Backup and defragment etcd
* Run etcd in HA (3 or 5 nodes)

---

## 15. You want to enforce that all images used in the cluster must come from a trusted internal registry. How do you implement this at the policy level?

**Approach:**

* Use Admission Controllers (OPA/Gatekeeper)
* Define policy to allow only specific registries
* Use imagePullSecrets

**Example:**

* Reject images not matching internal registry

---

## 🚀 Final Tip

For 4 years experience:

* Focus on **real debugging approach**
* Explain **why + architecture decisions**
* Show **production-level thinking (HA, security, scaling)**

---
