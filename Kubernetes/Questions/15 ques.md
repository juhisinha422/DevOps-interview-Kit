 scheduling constraints
Check for:

nodeSelector
Node affinity
Taints/tolerations
Pod affinity/anti-affinity
Resource requests
Topology constraints
A pod might be Pending because no node group can satisfy its requirements.

3. Node group max size
If:

desired = max

the autoscaler cannot add more nodes.

4. Autoscaler logs
kubectl logs -n kube-system deployment/cluster-autoscaler

Look for messages related to:

NotTriggerScaleUp
Max node group size
Unschedulable pods
IAM/API errors
Node group discovery
5. AWS-specific issues
On EKS, I would verify:

ASG/node group tags
IAM role permissions
EC2 capacity availability
Subnet IP availability
Instance limits
EKS managed node group health
4. NetworkPolicy is blocking traffic between namespaces. How would you design and debug it?
I prefer a default-deny + explicit-allow model.

For example:

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
  namespace: backend
spec:
  podSelector: {}
  policyTypes:
    - Ingress
    - Egress

Then explicitly allow only the required traffic.

For example, allow frontend pods from the frontend namespace to access backend pods on port 8080:

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
  namespace: backend
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
    - Ingress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              kubernetes.io/metadata.name: frontend
          podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080

Debugging
Check policies:

kubectl get networkpolicy -A
kubectl describe networkpolicy -n backend

Check namespace labels:

kubectl get namespace --show-labels

Test connectivity from a pod:

kubectl exec -it <pod> -- curl http://backend.backend.svc.cluster.local:8080

I would also verify whether the CNI actually enforces NetworkPolicy. For EKS, depending on the networking implementation, enforcement may come from the Amazon VPC CNI, Cilium, Calico, etc.

The key principle is:

Allow only the required source namespace + pod labels + destination port, rather than allowing an entire namespace or CIDR unnecessarily.

5. Microservice needs an external database through a VPN inside the cluster. How would you architect it?
I would separate the application workload from the VPN connectivity layer.

A typical architecture:

                 Kubernetes Cluster
                       |
        +--------------+--------------+
        |                             |
   Application                    VPN Gateway
      Pods                             |
        |                              |
        +-----------> Service ---------+
                                       |
                                  VPN Tunnel
                                       |
                              External Database

Depending on the VPN technology, I could use:

Dedicated VPN gateway outside Kubernetes
AWS Site-to-Site VPN
Transit Gateway
Cloud-native routing
Dedicated VPN gateway pods/DaemonSets where appropriate
For production, I generally prefer managing the VPN at the network/VPC level instead of coupling it tightly to application pods.

HA considerations
Use redundant VPN tunnels
Use multiple availability zones
Avoid a single VPN pod as the only gateway
Use health checks and automatic failover
Ensure routes are available from all required nodes/subnets
Security
Store credentials in AWS Secrets Manager/Kubernetes Secrets
Encrypt traffic through the VPN
Restrict database security groups/firewalls
Use NetworkPolicies to restrict which pods can access the DB
Use TLS to the database as an additional layer
Don't expose the database publicly
I would also monitor:

VPN tunnel state
Latency
Packet loss
Database connectivity
Route health
6. Multi-tenant platform on a single EKS cluster. How do you isolate workloads?
I would treat each tenant as a separate security and resource boundary as much as practical.

Namespace isolation
Create a namespace per tenant:

tenant-a
tenant-b
tenant-c

Apply:

ResourceQuota
LimitRange
NetworkPolicy
RBAC
Pod Security standards/policies
Dedicated service accounts
Resource quotas
Example:

apiVersion: v1
kind: ResourceQuota
metadata:
  name: tenant-quota
spec:
  hard:
    requests.cpu: "10"
    requests.memory: 20Gi
    limits.cpu: "20"
    limits.memory: 40Gi
    pods: "50"

Network isolation
Use default-deny NetworkPolicies and explicitly allow required traffic.

RBAC
Tenant users should only access their own namespace:

tenant-a users -> tenant-a namespace
tenant-b users -> tenant-b namespace

Workload isolation
For stronger isolation, I can use:

Dedicated node groups
Node labels
Taints/tolerations
Node affinity
Separate IAM roles using IRSA/EKS Pod Identity
Dedicated security groups where appropriate
Observability
Every tenant's logs and metrics should include a tenant identifier.

For example:

tenant=tenant-a
namespace=tenant-a
app=orders

Then dashboards and alerts can be scoped per tenant.

For highly sensitive tenants, I would consider separate clusters/accounts rather than relying only on namespace isolation.

7. Kubelet constantly restarts on a node. How would you debug it?
First identify the node:

kubectl get nodes
kubectl describe node <node-name>

Check node conditions:

MemoryPressure
DiskPressure
PIDPressure
Ready

Then access the node and inspect kubelet:

systemctl status kubelet
journalctl -u kubelet --since "1 hour ago"

Look for:

Certificate errors
Container runtime failures
Disk problems
Memory exhaustion
CNI issues
API server connectivity
Invalid kubelet configuration
cgroup problems
File descriptor/PID exhaustion
Check container runtime:

systemctl status containerd
journalctl -u containerd

Check resources:

df -h
df -i
free -m
top

Stabilization
If the node is impacting production:

kubectl cordon <node-name>

If appropriate:

kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data

Then investigate/rebuild/replace the node.

For managed EKS node groups, replacing an unhealthy node is often safer than spending excessive time repairing an instance.

8. Critical pod evicted due to node pressure. How would you prevent it?
First determine the eviction reason:

kubectl describe pod <pod-name>
kubectl describe node <node-name>

Typical causes:

Memory pressure
Disk pressure
PID pressure
Ephemeral-storage exhaustion
QoS classes
Kubernetes has three main QoS classes:

Guaranteed
Every container has CPU and memory requests equal to limits.

resources:
  requests:
    cpu: "500m"
    memory: "512Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"

Burstable
At least one container has resource requests/limits but doesn't qualify as Guaranteed.

BestEffort
No CPU/memory requests or limits are defined.

Under resource pressure, Kubernetes generally considers lower-priority/less-protected workloads for eviction first, although actual eviction behavior depends on the pressure signal and node conditions.

Prevention
For critical workloads:

Set accurate CPU/memory requests
Use appropriate limits
Use Guaranteed QoS where justified
Configure PriorityClass
Use PodDisruptionBudget
Add multiple replicas
Use topology spread constraints
Monitor node resources
Configure ephemeral-storage requests/limits where needed
Control container log growth
Keep sufficient node headroom
Example:

resources:
  requests:
    cpu: "1"
    memory: "1Gi"
  limits:
    cpu: "1"
    memory: "1Gi"

A PDB helps with voluntary disruptions, but it does not guarantee protection from node-pressure eviction.

9. Service requires TCP and UDP on the same port. How do you configure it?
Kubernetes Services can expose TCP and UDP on the same numeric port because the protocols are different.

Example:

apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - name: tcp-port
      protocol: TCP
      port: 8080
      targetPort: 8080

    - name: udp-port
      protocol: UDP
      port: 8080
      targetPort: 8080

This means:

TCP :8080 -> application :8080
UDP :8080 -> application :8080

Important Ingress consideration
Traditional Kubernetes HTTP/HTTPS Ingress is designed for HTTP-based traffic and does not generally provide arbitrary TCP/UDP forwarding.

For TCP/UDP workloads, I would usually use:

Service type: LoadBalancer
Cloud load balancer with TCP/UDP support
An ingress/controller that explicitly supports TCP/UDP
Gateway API implementation where supported
For EKS, the exact configuration depends on whether I am using AWS Load Balancer Controller, NLB, or another ingress implementation.

10. Rolling update caused downtime. How would you achieve zero-downtime deployments?
A basic rolling update isn't automatically zero-downtime.

I would use multiple layers of protection.

1. Multiple replicas
replicas: 3

2. Proper readiness probe
Traffic should only go to pods that are actually ready.

readinessProbe:
  httpGet:
    path: /ready
    port: 8080

3. Startup probe
For applications with slow startup:

startupProbe:
  httpGet:
    path: /health
    port: 8080
  failureThreshold: 30
  periodSeconds: 10

4. RollingUpdate strategy
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1

5. PodDisruptionBudget
Ensure enough replicas remain available during voluntary disruptions.

6. Graceful shutdown
The application should handle SIGTERM and stop accepting new requests before exiting.

I would use:

terminationGracePeriodSeconds: 30

and, where needed, a preStop hook.

7. Topology spread
Don't place every replica on the same node/AZ.

8. Advanced deployment strategies
For high-risk releases, I would use:

Blue/green deployment
Canary deployment
Progressive delivery
Automated rollback
Argo Rollouts
Feature flags
A good production deployment should be:

Deploy -> Observe -> Validate -> Gradually increase traffic -> Complete
                         |
                      Failure
                         |
                      Rollback

11. Istio/Envoy sidecar consumes more resources than the application. How do you optimize it?
First I would confirm the resource usage instead of assuming Envoy is the problem.

kubectl top pod <pod-name> --containers

Check Envoy metrics and configuration.

I would investigate:

Request rate
Number of connections
TLS/mTLS overhead
Access logging
Telemetry configuration
Retry policies
Circuit breakers
Large numbers of clusters/endpoints
High-cardinality metrics
Envoy configuration size
Common optimizations
Reduce unnecessary access logging:

DEBUG -> INFO/WARN

Avoid excessive telemetry and high-cardinality labels.

Tune sidecar resources:

resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 500m
    memory: 512Mi

The exact values should come from measurements rather than arbitrary limits.

Use Istio sidecar scoping where appropriate so a workload doesn't receive unnecessary service configuration.

For example, Sidecar resources can limit the set of services/configuration visible to Envoy.

Important
I would not simply reduce Envoy CPU/memory limits because that can cause throttling, OOMKills, or increased latency.

First measure, identify the expensive feature, then optimize.

12. How would you design a Kubernetes Operator?
An operator normally consists of:

CRD
 |
 v
Custom Resource
 |
 v
Controller
 |
 v
Kubernetes APIs / Application

CRD design
Suppose I have a custom resource:

apiVersion: platform.example.com/v1
kind: DatabaseCluster
metadata:
  name: production-db
spec:
  replicas: 3
  version: "15"
  storage: 100Gi

The spec represents the desired state.

The status should represent the observed state:

status:
  phase: Ready
  readyReplicas: 3

I would use:

Schema validation
Defaults
Enum validation where appropriate
Status conditions
Versioning for future CRD changes
Controller reconciliation
The controller follows:

Observe current state
       |
Compare with desired state
       |
Take corrective action
       |
Update status
       |
Wait for next event

Pseudo-logic:

Reconcile():
    fetch DatabaseCluster

    if resource is being deleted:
        perform cleanup
        return

    ensure StatefulSet exists

    ensure Service exists

    ensure PVCs exist

    compare actual replicas with desired replicas

    update resources if required

    update status conditions

    requeue if necessary

The reconciliation loop should be idempotent. Running it multiple times should not cause unintended side effects.

I would also handle:

Finalizers
OwnerReferences
Status conditions
Error retries/backoff
Upgrade logic
Backup/recovery
Leader election for HA
13. High disk I/O because of container logs. How can you prevent it?
Container logs can consume significant disk space, especially with verbose applications.

1. Configure log rotation
For Docker/containerd environments, configure the container runtime/logging mechanism appropriately.

On Kubernetes nodes, kubelet also has container log management settings such as:

containerLogMaxSize
containerLogMaxFiles

Example conceptually:

containerLogMaxSize = 10Mi
containerLogMaxFiles = 5

2. Reduce application log verbosity
Avoid logging large payloads or unnecessary DEBUG logs in production.

3. Centralized logging
Use a logging architecture such as:

Pod
 |
 v
Node logs
 |
 v
Fluent Bit / OpenTelemetry
 |
 v
Central logging system

For EKS, logs can be sent to systems such as CloudWatch or another centralized observability platform.

4. Monitor ephemeral storage
Check:

kubectl describe node <node>
df -h

Configure ephemeral-storage requests/limits for workloads that generate significant local data.

5. Avoid writing unnecessary application data to container filesystem
Use appropriate persistent storage or external storage when the data needs to survive the pod lifecycle.

14. etcd performance is degrading. What are the causes and how do you ensure HA?
etcd is critical because Kubernetes stores cluster state in it.

Possible causes
Excessive API writes
Examples:

Very high pod churn
Controllers continuously updating resources
Excessive status updates
Large numbers of Kubernetes objects
Large objects
Large ConfigMaps, Secrets, CRDs, or excessive object metadata can increase etcd load.

Disk latency
etcd is very sensitive to disk I/O latency.

Resource starvation
Insufficient:

CPU
Memory
Disk IOPS
Fragmentation
Frequent updates/deletes can cause database fragmentation.

Troubleshooting
Check API server and etcd metrics.

For self-managed clusters, etcd health can be checked using:

etcdctl endpoint health
etcdctl endpoint status

Monitor:

Commit latency
Disk WAL fsync latency
Database size
Leader changes
Backend quota
Request latency
HA
Use an odd number of members:

3 members
5 members

For most clusters, 3 members is a common starting point.

etcd uses quorum, so:

3 nodes -> tolerate 1 failure
5 nodes -> tolerate 2 failures

Members should be distributed across failure domains where possible.

Best practices
Use fast SSD storage
Monitor disk latency
Keep etcd database size under control
Compact and defragment when appropriate
Avoid unnecessary API writes
Backup etcd regularly
Test restore procedures
Ensure network latency between members is low
For EKS specifically, AWS manages the Kubernetes control plane and etcd for you, so I would focus on API-server/control-plane metrics and AWS support/health information rather than trying to tune etcd directly.

15. How do you enforce that all images come from a trusted internal registry?
I would enforce this at the admission-control/policy layer, rather than relying only on developer discipline.

For example, if the trusted registry is:

registry.company.internal

I want to reject:

image: docker.io/nginx

and allow:

image: registry.company.internal/nginx:1.27

Policy options
I could use:

Kyverno
OPA Gatekeeper
Kubernetes ValidatingAdmissionPolicy
Admission webhooks
For example, a Kyverno policy can validate that all container images start with the approved registry.

Conceptually:

Pod submitted
      |
      v
Admission Controller
      |
      +---- trusted registry? ----> YES -> Allow
      |
      +---- NO -------------------> DENY

Additional security
I would also implement:

Image vulnerability scanning
Image signing
Signature verification
SBOM generation
Immutable image tags/digests
Least-privilege registry access
Automated image promotion
For production, I prefer image digests where practical:

image: registry.company.internal/payment@sha256:<digest>

This prevents the same tag from silently pointing to a different image.

Example Kyverno-style policy concept
validation:
  message: "Images must come from the internal registry"
  pattern:
    spec:
      containers:
        - image: "registry.company.internal/*"

The exact policy syntax should be validated against the Kyverno version being used.

Quick Interview Summary
#	Topic	Key Interview Point
1	CrashLoopBackOff	Check describe, --previous, exit code, probes, OOM
2	StatefulSet	Protect PVC/PV; investigate CSI, scheduling and volume attachment
3	Cluster Autoscaler	Check why pod is unschedulable, node-group limits, IAM and autoscaler logs
4	NetworkPolicy	Default deny + explicit namespace/pod/port allow rules
5	VPN + DB	Prefer HA network-level VPN architecture with restricted access
6	Multi-tenancy	Namespace + RBAC + NetworkPolicy + quotas + IAM + observability
7	Kubelet	Check kubelet/containerd logs, node pressure, disk, memory and CNI
8	Eviction	Resource requests, QoS, PriorityClass, replicas, PDB and node headroom
9	TCP + UDP	Service can expose both protocols on the same port; Ingress depends on implementation
10	Zero downtime	Readiness/startup probes, graceful shutdown, PDB, topology, canary/blue-green
11	Istio	Measure Envoy, optimize telemetry/logging/configuration before reducing resources
12	Operator	CRD + idempotent reconciliation loop + status + finalizers
13	Disk I/O	Log rotation, centralized logging, lower verbosity, ephemeral-storage controls
14	etcd	Watch I/O, API churn, object size, quorum, backups and latency
15	Image policy	Admission policy + trusted registry + signing/scanning/digests

Useful Commands to Remember
# Pods
kubectl get pods -A
kubectl describe pod <pod>
kubectl logs <pod>
kubectl logs <pod> --previous

# Nodes
kubectl get nodes
kubectl describe node <node>
kubectl top nodes
kubectl top pods --containers

# Scheduling
kubectl get events -A --sort-by=.lastTimestamp
kubectl describe pod <pod>

# Storage
kubectl get pvc,pv
kubectl describe pvc <pvc>

# Networking
kubectl get networkpolicy -A
kubectl describe networkpolicy <policy> -n <namespace>

# StatefulSets
kubectl get sts
kubectl describe sts <statefulset>

# Cluster resources
kubectl top nodes
kubectl top pods -A

# EKS / Autoscaler
kubectl logs -n kube-system deployment/cluster-autoscaler

# Node troubleshooting
systemctl status kubelet
journalctl -u kubelet
systemctl status containerd
journalctl -u containerd

Interview Approach
For scenario-based Kubernetes questions, I would answer using this structure:

1. Identify → 2. Investigate → 3. Find root cause → 4. Fix → 5. Prevent recurrence

For example:

"First, I would check the pod status and events. Then I would inspect the container's previous logs and termination reason. After identifying whether it is an application crash, probe failure, OOM, configuration issue, or dependency problem, I would fix the root cause. Finally, I would add appropriate monitoring, resource settings, probes, or alerting to prevent the issue from happening again."

This approach demonstrates production troubleshooting experience, rather than just knowing Kubernetes commands.
