# 🚀 Kubernetes + AWS + DevOps Interview Guide (4 Years Experience - Detailed)

---

## What is a pod and why is it the smallest unit in Kubernetes?

A Pod is the smallest deployable and manageable unit in Kubernetes. It represents a single instance of a running process in the cluster and can contain one or more containers.

Containers inside a pod share:

* Same network namespace (same IP and port space)
* Shared storage volumes
* Same lifecycle

Kubernetes schedules workloads at the pod level, not container level. This is why it is the smallest unit. If a container fails, Kubernetes restarts the pod (depending on restart policy). Pods are ephemeral by nature and are usually managed by higher-level controllers like Deployments.

---

## Difference between Deployment, StatefulSet, and DaemonSet?

**Deployment:**
Used for stateless applications. It manages ReplicaSets and ensures desired number of pods are running. Supports rolling updates and rollbacks.

**StatefulSet:**
Used for stateful applications like databases. Provides:

* Stable network identity (fixed hostname)
* Persistent storage (PVC per pod)
* Ordered deployment and scaling

**DaemonSet:**
Ensures one pod runs on every node. Common use cases include:

* Logging agents (Fluentd)
* Monitoring agents (Node Exporter)

---

## What is CrashLoopBackOff and how do you debug it?

CrashLoopBackOff means the container inside the pod is repeatedly crashing and Kubernetes is backing off before restarting it.

Common causes:

* Application crash
* Misconfiguration
* Missing dependencies
* Port conflicts

Debug steps:

1. Check logs:
   kubectl logs <pod> --previous
2. Describe pod:
   kubectl describe pod <pod>
3. Check exit code and events
4. Verify environment variables and configs
5. Check resource limits
6. Run container locally if needed

---

## What happens when a pod goes into Evicted state?

A pod goes into Evicted state when the node is under resource pressure (memory, disk, or inode). Kubernetes removes the pod to maintain node stability.

Evicted pods are not restarted automatically unless controlled by a controller (Deployment/ReplicaSet).

---

## Difference between OOMKilled and Evicted?

**OOMKilled:**

* Container exceeds its memory limit
* Killed by Linux OOM killer
* Happens at container level

**Evicted:**

* Node-level resource pressure
* Pod removed entirely from node

---

## How does Kubernetes decide which pod to evict?

Kubernetes uses QoS classes:

* BestEffort → highest eviction priority
* Burstable → medium
* Guaranteed → lowest

Also considers:

* Resource usage vs requests
* Pod priority
* Node pressure conditions

---

## What are QoS classes (BestEffort, Burstable, Guaranteed)?

**BestEffort:**
No requests or limits defined. Lowest priority.

**Burstable:**
Requests defined but limits may be higher. Medium priority.

**Guaranteed:**
Requests = limits for all containers. Highest priority and least likely to be evicted.

---

## How do liveness and readiness probes work?

Liveness probe checks if application is alive. If it fails, Kubernetes restarts the container.

Readiness probe checks if application is ready to serve traffic. If it fails, the pod is removed from service endpoints but not restarted.

---

## What happens if readiness probe fails?

When readiness probe fails:

* Pod is marked “Not Ready”
* Removed from service load balancing
* Traffic is not routed to it
* Application continues running internally

---

## How do you debug a pod that is not starting?

Steps:

* kubectl describe pod → check events
* Check image pull issues
* Verify configmaps/secrets
* Check node resources
* Look for scheduling issues
* Inspect logs if container starts briefly

---

# 🌐 Networking & DNS

## How does DNS work inside Kubernetes?

Kubernetes uses CoreDNS. Each service gets a DNS entry:
service-name.namespace.svc.cluster.local

Pods resolve DNS using /etc/resolv.conf configured to point to CoreDNS service.

---

## What is CoreDNS and how do you troubleshoot it?

CoreDNS is the DNS server for Kubernetes cluster.

Troubleshooting:

* Check pods: kubectl get pods -n kube-system
* Check logs: kubectl logs <coredns-pod>
* Verify configmap
* Restart if needed

---

## How do you debug DNS issues in a pod?

* Exec into pod: kubectl exec -it <pod> -- sh
* Run nslookup or dig
* Check resolv.conf
* Verify CoreDNS health

---

## What is ClusterIP, NodePort, LoadBalancer?

ClusterIP: Internal communication within cluster
NodePort: Exposes service on node IP and port
LoadBalancer: Uses cloud provider LB for external access

---

## How does service discovery work in Kubernetes?

Services are discovered using DNS. kube-proxy manages routing rules and forwards traffic to backend pods.

---

## What is CNI and how does networking happen between pods?

CNI plugin assigns IPs to pods and enables communication across nodes using overlay or VPC networking.

---

## How do you debug intermittent packet loss?

* Check node CPU/network usage
* Verify network policies
* Use ping/traceroute
* Check CNI logs

---

# ☁️ AWS / Cloud (Very Important)

## Difference between Internet Gateway and NAT Gateway?

Internet Gateway allows inbound and outbound traffic for public instances.

NAT Gateway allows outbound traffic from private instances but blocks inbound traffic.

---

## Why NAT Gateway is placed in a public subnet?

Because NAT Gateway itself needs access to Internet Gateway to route traffic externally.

---

## How does traffic flow from private subnet to internet?

Private EC2 → Route Table → NAT Gateway → Internet Gateway → Internet

---

## What is VPC and how does routing work?

VPC is a logically isolated network. Routing is controlled via route tables associated with subnets.

---

## Difference between Security Groups and NACLs?

Security Groups are stateful and applied at instance level.
NACLs are stateless and applied at subnet level.

---

## What happens if route table is misconfigured?

Traffic will not reach destination:

* No internet access
* Internal communication failure

---

# 🔥 Scenario-Based (High Value)

## Your pod is in CrashLoopBackOff — how do you debug?

Check logs, configs, env variables, dependencies, and resource limits. Identify root cause and fix application or configuration.

---

## Pods are getting evicted frequently — what will you check?

* Node resource usage
* Pod resource requests/limits
* Disk/inode usage
* Scaling requirements

---

## Application is slow intermittently — how do you troubleshoot?

* Check CPU/memory spikes
* Analyze logs
* Check DB/network latency
* Use monitoring dashboards

---

## DNS resolution is failing in cluster — steps?

* Check CoreDNS pods
* Test DNS resolution inside pod
* Restart DNS

---

## Private EC2 cannot access internet — what could be wrong?

* Missing NAT Gateway
* Wrong route table
* Security group blocking

---

## Pod is running but not accessible — what will you check?

* Service config
* Endpoints
* Network policy
* Port mismatch

---

## High latency between services — how do you debug?

* Check network path
* Check pod resource usage
* Analyze logs and metrics

---

# ⚙️ Resources & Performance

## What are requests and limits?

Requests define guaranteed resources. Limits define maximum allowed usage.

---

## What happens if limits are not defined?

Pod may consume excessive resources and impact other workloads.

---

## How do you monitor resource usage?

Use Prometheus, Grafana, kubectl top, CloudWatch.

---

## What causes OOMKilled?

Application exceeds memory limit and is killed by kernel.

---

## How to prevent memory issues in production?

* Set proper limits
* Optimize application
* Use autoscaling

---

# 🔁 CI/CD & DevOps

## How do you design a CI/CD pipeline?

Stages:

* Code commit
* Build
* Test
* Scan
* Store artifact
* Deploy

---

## What is blue-green deployment?

Two environments are maintained. Traffic is switched after validation.

---

## What is rolling update in Kubernetes?

Pods are updated gradually without downtime.

---

## How do you rollback a failed deployment?

kubectl rollout undo deployment <name>

---

## How do you integrate SonarQube in pipeline?

Add scanning stage and enforce quality gate before deployment.

---

# 🧠 Pro-Level / Deep Questions

## How does Kubernetes scheduler work internally?

Scheduler filters nodes based on constraints, scores them, and assigns the best node.

---

## What happens inside kubelet when a pod is created?

Kubelet pulls image, starts container, and monitors health.

---

## How does service routing work at iptables level?

kube-proxy updates iptables rules to route traffic to pods.

---

## What happens when node goes NotReady?

Pods are rescheduled on other nodes after timeout.

---

## How does autoscaling work (HPA)?

HPA monitors metrics and adjusts replica count automatically.

---

# 🎯 Rapid-Fire (Interview Style)

## What is Exit Code 137?

Indicates container killed due to OOM.

---

## What is the default DNS policy in Kubernetes?

ClusterFirst

---

## Can a pod move to another node automatically?

No, new pod is created instead.

---

## What is the difference between pod restart and pod recreation?

Restart = same pod, recreation = new pod instance.

---

## What is the role of kube-proxy?

Handles networking and routes traffic to pods.

---

## 🚀 Final Tip

At 4 years experience:

* Always explain **why + how + debug steps**
* Mention **real production scenarios**
* Use **commands + troubleshooting approach**

---
