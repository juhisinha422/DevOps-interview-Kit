# Kubernetes Interview Questions & Answers — 4 Years Experience

This README contains practical, scenario-based Kubernetes interview questions and answers targeted at a **4-year DevOps / Kubernetes Engineer**.

---

## Table of Contents

1. [CrashLoopBackOff](#1-pod-stuck-in-crashloopbackoff-but-logs-show-no-errors)
2. [StatefulSet Pod Recreation](#2-statefulset-pod-is-not-recreating-properly-after-deletion)
3. [Cluster Autoscaler](#3-cluster-autoscaler-is-not-scaling-up-even-though-pods-are-pending)
4. [NetworkPolicy](#4-networkpolicy-is-blocking-traffic-between-namespaces)
5. [VPN and External Database](#5-microservice-needs-to-connect-to-an-external-database-through-a-vpn)
6. [Multi-Tenant EKS](#6-multi-tenant-platform-on-a-single-eks-cluster)
7. [Kubelet Restarting](#7-kubelet-is-constantly-restarting-on-a-node)
8. [Pod Eviction](#8-critical-pod-evicted-due-to-node-pressure)
9. [TCP and UDP Same Port](#9-service-requires-tcp-and-udp-on-the-same-port)
10. [Zero-Downtime Deployment](#10-rolling-update-caused-downtime)
11. [Istio/Envoy Resource Usage](#11-istio-envoy-sidecar-is-consuming-more-resources-than-the-application)
12. [Kubernetes Operator](#12-designing-a-kubernetes-operator)
13. [Container Log Disk Usage](#13-high-disk-io-due-to-container-logs)
14. [etcd Performance](#14-etcd-performance-is-degrading)
15. [Trusted Container Registry](#15-enforcing-images-from-a-trusted-internal-registry)

---

# 1. Pod Stuck in CrashLoopBackOff but Logs Show No Errors

### Question

Your pod keeps getting stuck in `CrashLoopBackOff`, but logs show no errors. How would you approach debugging and resolution?

### Answer

`CrashLoopBackOff` means the container starts, exits, and Kubernetes repeatedly restarts it with an increasing backoff delay.

I would troubleshoot it step by step instead of immediately restarting the pod.

### Step 1: Check Pod Status

```bash
kubectl get pod <pod-name> -n <namespace>


Check:

STATUS
RESTARTS
Age
Step 2: Describe the Pod
kubectl describe pod <pod-name> -n <namespace>


I would check:

Container state
Exit code
Termination reason
Events
Liveness probe
Readiness probe
Startup probe
Mount failures
Scheduling issues
Step 3: Check Previous Container Logs

This is especially important when the container crashes very quickly.

kubectl logs <pod-name> -n <namespace> --previous


Sometimes the current container has no logs because it has already restarted, while --previous contains the actual error.

Step 4: Check Container Exit Reason
kubectl get pod <pod-name> -n <namespace> \
  -o jsonpath='{.status.containerStatuses[*].state}'


Look for:

OOMKilled
Error
Completed

Common Causes
Application is crashing
Incorrect command or entrypoint
Missing environment variables
Missing Secret or ConfigMap
Incorrect configuration
Liveness probe is killing the container
Container is getting OOMKilled
Permission problems
Missing volume
Dependency such as database/API is unavailable
Application exits immediately after startup
Example: Probe Problem

If the application takes 60 seconds to start but the liveness probe starts checking after only 10 seconds, Kubernetes may repeatedly kill it.

A startupProbe can be used:

startupProbe:
  httpGet:
    path: /health
    port: 8080
  failureThreshold: 30
  periodSeconds: 10

Resolution

I would first identify the termination reason and then fix the root cause.

For example:

CrashLoopBackOff
      |
      v
kubectl describe pod
      |
      v
Check exit code/events
      |
      v
kubectl logs --previous
      |
      v
Check probes/config/resources
      |
      v
Fix root cause
      |
      v
Monitor pod

2. StatefulSet Pod Is Not Recreating Properly After Deletion
Question

You have a StatefulSet deployed with persistent volumes, and one pod is not recreating properly after deletion. What could be the reasons, and how do you fix it without data loss?

Answer

StatefulSets provide stable pod identities and persistent storage. I would be very careful not to delete the PVC/PV because that can cause data loss.

First check:

kubectl get statefulset -n <namespace>
kubectl get pods -n <namespace>


Then:

kubectl describe statefulset <statefulset-name> -n <namespace>

Check PVC and PV
kubectl get pvc -n <namespace>
kubectl get pv


Then:

kubectl describe pvc <pvc-name> -n <namespace>

Possible Reasons
PVC is stuck in Pending
PV is unavailable
StorageClass problem
CSI driver issue
Volume attachment problem
Volume cannot be mounted
Node doesn't satisfy scheduling requirements
Resource requests cannot be satisfied
Node affinity/topology issue
Taints and tolerations
Pod security policy/admission policy
Previous pod is stuck terminating
Storage backend is unavailable
Check Volume Attachments
kubectl get volumeattachments


If the volume is still attached to an unhealthy node, I would investigate the CSI driver and attachment state.

Important: Avoid Data Loss

Do not blindly run:

kubectl delete pvc
kubectl delete pv


The PVC contains the persistent storage reference.

Normally, deleting a StatefulSet pod does not delete its PVC.

Safe Recovery

Delete only the problematic pod if required:

kubectl delete pod <pod-name> -n <namespace>


Then verify:

kubectl get pod -n <namespace> -w


The StatefulSet controller should recreate the same ordinal, for example:

mysql-0
mysql-1
mysql-2


and mysql-1 should reconnect to its existing PVC.

Interview Point

The important point is:

StatefulSet pods have stable identities and persistent volumes. When troubleshooting, protect the PVC/PV first and investigate scheduling, CSI, attachment, and mount issues before considering any destructive storage operation.

3. Cluster Autoscaler Is Not Scaling Up Even Though Pods Are Pending
Question

Your Cluster Autoscaler is not scaling up even though pods are in Pending state. What would you investigate?

Answer

A pod being Pending does not automatically mean Cluster Autoscaler will scale up.

First, I check why the pod is unschedulable.

kubectl get pods -A
kubectl describe pod <pod-name> -n <namespace>


Look for messages such as:

0/5 nodes are available

Check Resource Requests

For example, if the pod requires:

resources:
  requests:
    cpu: "8"
    memory: "16Gi"


but the node groups don't have an instance that can satisfy the request, autoscaling may not help.

Check Node Constraints

I would check:

nodeSelector
Node affinity
Pod affinity
Pod anti-affinity
Taints
Tolerations
Topology constraints
Resource requests
Check Cluster Autoscaler
kubectl -n kube-system get deployment cluster-autoscaler


Then:

kubectl logs -n kube-system deployment/cluster-autoscaler


Look for:

NotTriggerScaleUp


or messages related to:

Node group maximum size
Unschedulable pods
IAM permissions
AWS API errors
Node group discovery
Check Node Group Limits

Verify:

minimum nodes
desired nodes
maximum nodes


If:

desired = max


Cluster Autoscaler cannot add more nodes.

EKS-Specific Checks

For EKS, I would also check:

Auto Scaling Group / managed node group configuration
Cluster Autoscaler IAM permissions
Node group tags/discovery
EC2 capacity
Subnet IP availability
Instance limits
Availability Zone capacity
Interview Summary

My approach would be:

Pending Pod
    |
    v
kubectl describe pod
    |
    v
Why is it unschedulable?
    |
    +--> Resource issue
    +--> Affinity issue
    +--> Taint issue
    +--> Node group max reached
    +--> Autoscaler configuration/IAM
    |
    v
Check Cluster Autoscaler logs
    |
    v
Fix root cause

4. NetworkPolicy Is Blocking Traffic Between Namespaces
Question

A NetworkPolicy is blocking traffic between services in different namespaces. How would you design and debug the policy to allow only specific communication paths?

Answer

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


This denies traffic unless another policy allows it.

Allow Specific Namespace and Pod

Suppose:

frontend namespace
      |
      | TCP 8080
      v
backend namespace


Policy:

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


This allows only:

frontend namespace
+
frontend pods
+
TCP 8080


to reach the backend pods.

Debugging

Check policies:

kubectl get networkpolicy -A
kubectl describe networkpolicy -n backend


Check namespace labels:

kubectl get namespace --show-labels


Check pod labels:

kubectl get pods -n frontend --show-labels
kubectl get pods -n backend --show-labels


Test connectivity:

kubectl exec -it <frontend-pod> -n frontend -- \
  curl http://backend.backend.svc.cluster.local:8080

Important

I would also verify that the Kubernetes CNI supports and enforces NetworkPolicy.

Examples include:

Amazon VPC CNI
Calico
Cilium
Interview Point

Avoid broad policies such as:

allow all namespaces
allow all pods
allow all ports


Instead define:

Source namespace
+
Source pod
+
Destination pod
+
Protocol
+
Port

5. Microservice Needs to Connect to an External Database Through a VPN
Question

One of your microservices has to connect to an external database via a VPN inside the cluster. How would you architect this in Kubernetes with HA and security in mind?

Answer

For production, I would prefer a network-level VPN architecture rather than putting the VPN client directly inside every application pod.

Example:

             EKS Cluster
                  |
          Application Pods
                  |
                  v
            VPC Routing
                  |
                  v
          VPN Gateway / TGW
                  |
             VPN Tunnel
                  |
                  v
        External Network
                  |
                  v
           External DB


Depending on the environment, this could use:

AWS Site-to-Site VPN
Transit Gateway
Dedicated VPN gateway
Cloud networking
Dedicated VPN infrastructure
HA Design

I would avoid a single VPN pod as a single point of failure.

Use:

Redundant VPN tunnels
Multiple Availability Zones
HA VPN gateways
Health checks
Automatic failover
Redundant routing
Security

I would implement:

Private connectivity
Security groups/firewalls
NetworkPolicy
Database TLS
Secrets Manager/Kubernetes Secrets
Least-privilege database accounts
No public database exposure
Application NetworkPolicy

Only the required application should be allowed to connect to the database.

For example:

orders-service
     |
     | TCP 5432
     v
VPN/DB network


Other workloads should not have access.

Monitoring

Monitor:

VPN tunnel status
VPN latency
Packet loss
Database connection errors
Route availability
Connection count
Application latency
6. Multi-Tenant Platform on a Single EKS Cluster
Question

You're running a multi-tenant platform on a single EKS cluster. How do you isolate workloads and ensure security, quotas, and observability for each tenant?

Answer

I would use namespaces as the basic tenant boundary.

Example:

EKS Cluster
 |
 +-- tenant-a
 |
 +-- tenant-b
 |
 +-- tenant-c


For each namespace, configure:

RBAC
ResourceQuota
LimitRange
NetworkPolicy
Pod security controls
Service accounts
IAM permissions
Observability
ResourceQuota

Example:

apiVersion: v1
kind: ResourceQuota
metadata:
  name: tenant-quota
  namespace: tenant-a
spec:
  hard:
    requests.cpu: "10"
    requests.memory: 20Gi
    limits.cpu: "20"
    limits.memory: 40Gi
    pods: "50"


This prevents one tenant from consuming all cluster resources.

Network Isolation

Use default-deny policies.

Then explicitly allow only the required communication.

RBAC

Tenant A users should only access Tenant A:

Tenant A users
      |
      v
tenant-a namespace


They should not have permissions on:

tenant-b
tenant-c
kube-system

Workload Isolation

For stronger isolation, use:

Dedicated node groups
Node labels
Taints/tolerations
Node affinity
Security Groups
EKS Pod Identity / IAM roles

Example:

Tenant A
   |
   v
Dedicated Node Group
   |
   v
Tenant A workloads

Observability

All logs and metrics should contain tenant information:

tenant=tenant-a
namespace=tenant-a
application=orders


Then dashboards and alerts can be separated by tenant.

Strong Isolation

For highly sensitive customers, I would consider:

Separate AWS Account
+
Separate EKS Cluster


instead of relying only on namespaces.

7. Kubelet Is Constantly Restarting on a Node
Question

You notice the kubelet is constantly restarting on a particular node. What steps would you take to isolate the issue and ensure node stability?

Answer

First identify the node:

kubectl get nodes
kubectl describe node <node-name>


Check conditions:

Ready
MemoryPressure
DiskPressure
PIDPressure

Check Kubelet

On the node:

systemctl status kubelet


Then:

journalctl -u kubelet --since "1 hour ago"

Look For
Certificate problems
API server connectivity
Container runtime failures
Disk problems
Memory exhaustion
Cgroup problems
CNI failures
File descriptor exhaustion
PID exhaustion
Invalid kubelet configuration
Check Container Runtime
systemctl status containerd


Logs:

journalctl -u containerd --since "1 hour ago"

Check Node Resources
df -h
df -i
free -m
top


Also:

dmesg | tail -100


Look for:

OOM
disk errors
filesystem errors
kernel errors

Protect Production

If the node is unhealthy:

kubectl cordon <node-name>


Then, if appropriate:

kubectl drain <node-name> \
  --ignore-daemonsets \
  --delete-emptydir-data


After that, investigate or replace the node.

For EKS managed node groups, replacing an unhealthy node is often safer than manually repairing a badly damaged instance.

8. Critical Pod Evicted Due to Node Pressure
Question

A critical pod in production gets evicted due to node pressure. How would you prevent this from happening again, and how do QoS classes play a role?

Answer

First determine the eviction reason:

kubectl describe pod <pod-name>
kubectl describe node <node-name>


Possible reasons include:

Memory pressure
Disk pressure
PID pressure
Ephemeral storage exhaustion
QoS Classes
Guaranteed

Every container has CPU and memory requests equal to limits.

resources:
  requests:
    cpu: "1"
    memory: "1Gi"
  limits:
    cpu: "1"
    memory: "1Gi"

Burstable

At least one container has resource requests/limits but the pod does not qualify as Guaranteed.

BestEffort

No CPU or memory requests/limits are defined.

Under node pressure, Kubernetes generally evicts less-protected workloads before higher-protected ones, depending on the pressure condition.

Prevention

For critical applications:

1. Set Accurate Resource Requests
resources:
  requests:
    cpu: "1"
    memory: "1Gi"

2. Set Appropriate Limits
limits:
  cpu: "1"
  memory: "1Gi"

3. Use PriorityClass

Critical workloads can have higher priority.

4. Run Multiple Replicas
replicas: 3

5. Use PodDisruptionBudget

A PDB protects against certain voluntary disruptions.

Important:

PDB does not guarantee protection from node-pressure eviction.

6. Use Topology Spread

Distribute replicas across nodes/AZs.

7. Monitor Node Pressure

Monitor:

CPU
Memory
Disk
Ephemeral Storage
PID

8. Control Logs

Large container logs can cause disk pressure.

9. Service Requires TCP and UDP on the Same Port
Question

You need to deploy a service that requires TCP and UDP on the same port. How would you configure this in Kubernetes using Services and Ingress?

Answer

Kubernetes Services can expose TCP and UDP using the same numeric port because TCP and UDP are different protocols.

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


This provides:

TCP :8080 -> Pod :8080
UDP :8080 -> Pod :8080

Ingress Consideration

Traditional Kubernetes HTTP Ingress is designed mainly for:

HTTP
HTTPS


It does not automatically provide arbitrary TCP/UDP forwarding.

For TCP/UDP, I would normally use:

Service type LoadBalancer


or an ingress/load-balancer implementation that explicitly supports TCP/UDP.

On EKS, an NLB can be used depending on the protocol and controller configuration.

Interview Point

The key distinction is:

HTTP/HTTPS
    |
    v
Ingress

TCP/UDP
    |
    v
LoadBalancer / TCP-UDP capable gateway

10. Rolling Update Caused Downtime
Question

An application upgrade caused downtime even though you had rolling updates configured. What advanced strategies would you apply to ensure zero-downtime deployments next time?

Answer

A rolling update alone does not guarantee zero downtime.

I would use several protections together.

1. Multiple Replicas
replicas: 3

2. Readiness Probe

Only ready pods should receive traffic.

readinessProbe:
  httpGet:
    path: /ready
    port: 8080

3. Startup Probe

Useful for slow-starting applications.

startupProbe:
  httpGet:
    path: /health
    port: 8080
  failureThreshold: 30
  periodSeconds: 10

4. Rolling Update Configuration
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1


This helps ensure an old healthy pod remains available while the new pod starts.

5. Graceful Shutdown

Application should properly handle:

SIGTERM


and stop accepting new requests before exiting.

Example:

terminationGracePeriodSeconds: 30


A preStop hook may also be used where appropriate.

6. PodDisruptionBudget

Ensure enough replicas remain available during voluntary disruptions.

7. Topology Spread

Spread replicas across:

Nodes
Availability Zones
8. Canary Deployment

Instead of sending 100% traffic to the new version immediately:

v1 -> 95%
v2 -> 5%


Monitor:

Error rate
Latency
CPU
Memory
Business metrics

Then increase:

5% -> 25% -> 50% -> 100%

9. Blue/Green Deployment

Run:

Blue = Current Version
Green = New Version


Test Green and switch traffic when ready.

10. Automated Rollback

If metrics cross a threshold:

High error rate
      |
      v
Rollback


Tools such as Argo Rollouts can help implement canary/progressive deployment.

11. Istio Envoy Sidecar Consumes More Resources Than the Application
Question

Your service mesh sidecar, such as Istio Envoy, is consuming more resources than the app itself. How do you analyze and optimize it?

Answer

I would first measure the actual resource usage.

kubectl top pod <pod-name> --containers


This shows resource usage per container.

For example:

NAME       CPU    MEMORY
app        100m   200Mi
istio-proxy 500m  600Mi


Then investigate why Envoy is using resources.

Things to Check
Request rate
Number of connections
TLS/mTLS
Access logging
Telemetry
Metrics cardinality
Retry policies
Circuit breakers
Number of services/endpoints
Envoy configuration size
High connection churn
Common Optimizations

Reduce unnecessary logging.

Avoid:

DEBUG logging


in production unless temporarily needed.

Review telemetry and high-cardinality metrics.

Use Istio configuration to avoid sending unnecessary service configuration to sidecars where appropriate.

Resource Configuration

Example:

resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 500m
    memory: 512Mi


However, I would not blindly reduce Envoy resources.

Too-low limits can cause:

CPU throttling
OOMKilled
Latency
Connection failures

Interview Point

My approach is:

Measure
  |
Identify expensive Envoy feature
  |
Optimize configuration
  |
Load test
  |
Tune resources
  |
Monitor

12. Designing a Kubernetes Operator
Question

You need to create a Kubernetes operator to automate complex application lifecycle events. How do you design the CRD and controller loop logic?

Answer

A Kubernetes Operator normally contains:

CRD
 |
 v
Custom Resource
 |
 v
Controller
 |
 v
Kubernetes Resources

CRD Example

Suppose I create:

apiVersion: platform.example.com/v1
kind: DatabaseCluster
metadata:
  name: production-db
spec:
  replicas: 3
  version: "15"
  storage: 100Gi


The spec represents the desired state.

The status represents the observed state:

status:
  phase: Ready
  readyReplicas: 3

CRD Best Practices

I would include:

OpenAPI schema validation
Defaults
Enum validation
Versioning
Status conditions
Documentation
Controller Reconciliation Loop

The controller continuously compares:

Desired State
      vs
Actual State


Example:

Reconcile
   |
   v
Get Custom Resource
   |
   v
Check actual resources
   |
   v
Compare desired vs actual
   |
   v
Create/Update/Delete resources
   |
   v
Update status
   |
   v
Wait for next event

Pseudo Logic
Reconcile():

    Get DatabaseCluster

    If resource is being deleted:
        perform cleanup
        return

    Ensure StatefulSet exists

    Ensure Service exists

    Ensure PVCs exist

    Compare desired replicas with actual replicas

    Update resources if required

    Update status

    Return

Important Design Principles

The reconciliation loop should be:

Idempotent
Fault tolerant
Event driven
Retryable

I would also use:

Finalizers
OwnerReferences
Status conditions
Leader election
Error backoff
Upgrade handling
Backup/recovery logic
13. High Disk I/O Due to Container Logs
Question

Multiple nodes are showing high disk I/O usage due to container logs. What Kubernetes features or practices can you apply to avoid this scenario?

Answer

Large or verbose container logs can quickly consume node disk and cause DiskPressure.

1. Log Rotation

Configure container log rotation.

Kubelet supports settings such as:

containerLogMaxSize
containerLogMaxFiles


Example concept:

containerLogMaxSize = 10Mi
containerLogMaxFiles = 5


The exact configuration depends on the node/runtime setup.

2. Reduce Log Verbosity

Avoid running production workloads with:

DEBUG
TRACE


unless required for troubleshooting.

3. Centralized Logging

A common architecture:

Pod
 |
 v
Container Logs
 |
 v
Fluent Bit / OpenTelemetry
 |
 v
Central Logging System


For example:

CloudWatch
ELK/OpenSearch
Datadog
Splunk

4. Monitor Ephemeral Storage
kubectl describe node <node-name>


Also check:

df -h
df -i

5. Configure Ephemeral Storage

For applications that use significant temporary disk:

resources:
  requests:
    ephemeral-storage: "1Gi"

  limits:
    ephemeral-storage: "5Gi"

6. Avoid Excessive Local Writes

Do not use the container filesystem for data that needs long-term persistence.

Use:

Persistent Volumes
Object Storage
External Databases


where appropriate.

14. etcd Performance Is Degrading
Question

Your Kubernetes cluster's etcd performance is degrading. What are the root causes and how do you ensure etcd high availability and tuning?

Answer

etcd stores Kubernetes cluster state and is critical to the control plane.

Possible Root Causes
1. High API Write Volume

Examples:

High pod churn
Controllers constantly updating resources
Excessive status updates
Large number of objects
2. Large Objects

Large:

Secrets
ConfigMaps
CRDs
Resource metadata

can increase etcd load.

3. Disk Latency

etcd is very sensitive to storage latency, especially WAL/fsync operations.

4. CPU/Memory Pressure

Insufficient resources can affect performance.

5. Database Size/Fragmentation

Frequent writes/deletes can increase fragmentation.

Monitoring

Monitor:

Request latency
Commit latency
Disk fsync latency
Database size
Leader changes
Backend quota
CPU
Memory
Disk I/O


For self-managed etcd:

etcdctl endpoint health
etcdctl endpoint status

HA

Use an odd number of members.

Common configurations:

3 members
5 members


With:

3 members -> tolerate 1 failure
5 members -> tolerate 2 failures


Distribute members across failure domains where possible.

Best Practices
Use fast SSD storage
Monitor disk latency
Keep database size under control
Compact when appropriate
Defragment when appropriate
Reduce unnecessary API writes
Backup regularly
Test restore procedures
Keep network latency low between etcd members
EKS Important Point

For Amazon EKS, AWS manages the Kubernetes control plane and etcd.

Therefore, I would not directly tune etcd on EKS worker nodes.

Instead, I would investigate:

API server behavior
Control plane metrics
Kubernetes object churn
Controller behavior
AWS EKS control-plane health
AWS support if the issue is control-plane specific
15. Enforcing Images From a Trusted Internal Registry
Question

You want to enforce that all images used in the cluster must come from a trusted internal registry. How do you implement this at the policy level?

Answer

I would enforce this using Kubernetes admission control.

Example trusted registry:

registry.company.internal


Allowed:

image: registry.company.internal/myteam/payment:1.0.0


Rejected:

image: docker.io/nginx:latest

Policy Options

I can use:

Kyverno
OPA Gatekeeper
Kubernetes ValidatingAdmissionPolicy
Admission Webhooks

The policy should inspect container images during admission.

Conceptually:

Pod submitted
      |
      v
Admission Policy
      |
      +---- Trusted Registry? ---- YES ---> Allow
      |
      +---- NO -------------------------> DENY

Example Policy Concept

A Kyverno-style validation policy could enforce:

spec.containers[*].image
must start with:

registry.company.internal/


Conceptually:

validation:
  message: "Images must come from the internal registry"
  pattern:
    spec:
      containers:
        - image: "registry.company.internal/*"


The exact syntax should be validated against the Kyverno version deployed in the cluster.

Additional Security

Registry enforcement alone is not enough.

I would also implement:

Image vulnerability scanning
Image signing
Signature verification
SBOM generation
Immutable tags
Image promotion pipeline
Least-privilege registry access
Image digest pinning

For production:

image: registry.company.internal/payment@sha256:<digest>


Using a digest ensures the exact image is deployed.

Production Image Flow
Developer
    |
    v
CI Pipeline
    |
    v
Build Image
    |
    v
Security Scan
    |
    v
Generate SBOM
    |
    v
Sign Image
    |
    v
Internal Registry
    |
    v
Kubernetes Admission Policy
    |
    v
Deploy

Quick Interview Cheat Sheet
#	Topic	Key Point
1	CrashLoopBackOff	Check describe, --previous, exit code, probes, OOM
2	StatefulSet	Protect PVC/PV and investigate CSI, scheduling and attachment
3	Cluster Autoscaler	Check why pod is unschedulable, max size, IAM and autoscaler logs
4	NetworkPolicy	Default deny + explicit namespace/pod/port rules
5	VPN + DB	Prefer HA network-level VPN with restricted access
6	Multi-Tenancy	Namespace + RBAC + NetworkPolicy + quotas + IAM
7	Kubelet	Check kubelet/containerd logs, disk, memory, CNI and node health
8	Eviction	Requests/limits + QoS + PriorityClass + replicas + node headroom
9	TCP/UDP	Service can expose TCP and UDP on same numeric port
10	Zero Downtime	Probes + graceful shutdown + PDB + topology + canary/blue-green
11	Istio	Measure Envoy first, then optimize telemetry/logging/config
12	Operator	CRD + idempotent reconciliation + status + finalizers
13	Disk I/O	Log rotation + centralized logging + ephemeral storage controls
14	etcd	Monitor I/O, API churn, database size and quorum
15	Registry	Admission policy + trusted registry + signing/scanning/digests
Important Kubernetes Commands for Interviews
Pods
kubectl get pods -A

kubectl get pod <pod-name> -n <namespace>

kubectl describe pod <pod-name> -n <namespace>

kubectl logs <pod-name> -n <namespace>

kubectl logs <pod-name> -n <namespace> --previous

kubectl get events -A --sort-by=.lastTimestamp

Nodes
kubectl get nodes

kubectl describe node <node-name>

kubectl top nodes

kubectl top pods -A

kubectl top pod <pod-name> --containers

Storage
kubectl get pvc -A

kubectl get pv

kubectl describe pvc <pvc-name>

kubectl get storageclass

kubectl get volumeattachments

NetworkPolicy
kubectl get networkpolicy -A

kubectl describe networkpolicy <policy-name> -n <namespace>

kubectl get namespace --show-labels

kubectl get pods --show-labels -A

StatefulSet
kubectl get statefulset -A

kubectl describe statefulset <name> -n <namespace>

Deployment
kubectl get deployment -A

kubectl describe deployment <deployment-name>

kubectl rollout status deployment/<deployment-name>

kubectl rollout history deployment/<deployment-name>

kubectl rollout undo deployment/<deployment-name>

Autoscaler
kubectl get deployment -n kube-system cluster-autoscaler

kubectl logs -n kube-system deployment/cluster-autoscaler

Node Troubleshooting
systemctl status kubelet

journalctl -u kubelet

systemctl status containerd

journalctl -u containerd

df -h

df -i

free -m

top

How to Answer Kubernetes Scenario Questions in an Interview

For most production troubleshooting questions, follow this structure:

1. Identify the issue
       |
       v
2. Check status and events
       |
       v
3. Collect logs/metrics
       |
       v
4. Identify root cause
       |
       v
5. Apply the fix
       |
       v
6. Verify the fix
       |
       v
7. Add preventive measures


A strong interview answer should not stop at:

"I will restart the pod."

Instead say:

"First, I would identify why the pod is restarting by checking its events, termination reason, previous logs, resource usage, and health probes. Once I identify the root cause, I would fix the configuration or resource issue, verify that the workload is healthy, and then add appropriate monitoring or preventive controls so the issue does not happen again."

Final 4-Year Experience Interview Mindset

For a 4-year Kubernetes/DevOps interview, focus on demonstrating that you understand both Kubernetes concepts and production troubleshooting.

Be comfortable explaining:

Kubernetes architecture
Pods and controllers
Deployments
StatefulSets
DaemonSets
Jobs/CronJobs
Services
Ingress
NetworkPolicies
ConfigMaps and Secrets
PV/PVC/StorageClasses
Probes
QoS classes
Resource requests and limits
HPA/VPA
Cluster Autoscaler
RBAC
Service Accounts
Pod Security
EKS
IAM/IRSA or EKS Pod Identity
CNI
CSI
Helm
CI/CD
Rolling/Canary/Blue-Green deployments
Service Mesh
Monitoring and logging
Disaster recovery
Kubernetes security

The strongest answers follow this pattern:

Understand the problem
        ↓
Collect evidence
        ↓
Find root cause
        ↓
Fix safely
        ↓
Verify
        ↓
Prevent recurrence


This demonstrates real production experience rather than only theoretical Kubernetes knowledge.
