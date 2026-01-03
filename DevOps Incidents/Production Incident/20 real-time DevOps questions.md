# 🚀 20 Real-Time DevOps & Kubernetes Troubleshooting Questions

This Document contains **20 real-world DevOps interview questions** with clear explanations and practical commands.  
It is designed for **AWS DevOps, Kubernetes, EKS, Jenkins, and Docker** interview preparation and on-the-job troubleshooting.

---

## 1. Pod is in CrashLoopBackOff. How do you debug?

### Theory  
CrashLoopBackOff occurs when a container starts, crashes, and Kubernetes repeatedly restarts it.

### What to Check
- Pod events to understand restart reason  
- Application logs and previous logs  
- Resource limits and probe configuration  

### Commands
```bash
kubectl describe pod <pod>
kubectl logs <pod>
kubectl logs <pod> -p
```

### 2. Private EC2 instance cannot access the internet
### Theory

Private EC2 instances do not have direct internet access and must route outbound traffic through a NAT Gateway.

### What to Check

Route table pointing to NAT Gateway

NAT Gateway in a public subnet with Internet Gateway

Outbound rules in Security Groups and NACLs

Commands
```bash
aws ec2 describe-route-tables
aws ec2 describe-nat-gateways
```

3. Jenkins Docker build fails but works locally
Theory

Docker builds in Jenkins depend on agent permissions, Docker runtime, and platform compatibility.

What to Check

Jenkins user Docker permissions

Docker daemon status

OS or architecture mismatch

Commands
docker ps
systemctl status docker
groups jenkins

4. New deployment caused high latency
Theory

Latency can be introduced at application, container, or infrastructure layers after a deployment.

What to Check

Metrics to identify latency source

Application logs

Pod and node resource usage

Load balancer and network configuration

Commands
kubectl top pods
kubectl logs <pod>

5. How do you rotate Kubernetes secrets without downtime?
Theory

Updating a secret does not restart pods automatically. A rolling update is required.

What to Do

Create a new secret

Update deployment to reference the new secret

Let rolling update restart pods gradually

Commands
kubectl create secret generic new-secret
kubectl apply -f deployment.yaml
kubectl rollout status deployment/<name>

6. Kubernetes node goes NotReady
Theory

A node becomes NotReady when kubelet cannot communicate with the control plane or resources are exhausted.

What to Check

Node conditions and events

Kubelet and container runtime

Disk, memory, and network health

Commands
kubectl describe node <node>
systemctl status kubelet

7. Pods stuck in Pending state
Theory

Pending means the scheduler cannot find a suitable node.

Common Causes

Node not ready or insufficient resources

Taints without tolerations

NodeSelector or affinity mismatch

Commands
kubectl describe pod <pod>
kubectl describe node <node>

8. Traffic still going to old pods after deployment
Theory

Traffic routing depends on Services and pod readiness.

What to Check

Service selector mismatch

Deployment rollout stuck

Ingress or routing misconfiguration

Commands
kubectl get svc
kubectl describe deployment <name>

9. App works inside cluster but fails via Ingress
Theory

Ingress routes external traffic and depends on correct rules, services, and controller health.

What to Check

Ingress host and path rules

Service name and port

Ingress controller health

Commands
kubectl describe ingress <name>
kubectl get pods -n ingress-nginx

10. EKS node is Ready but no pods are scheduled
Theory

Scheduler avoids nodes that violate scheduling rules.

Possible Reasons

Node taints without tolerations

Insufficient CPU or memory

Label or selector mismatch

Commands
kubectl describe node <node>

11. Pod restarts randomly but logs show no errors
Theory

Restarts may occur due to probe failures or resource pressure.

What to Check

Pod events

Resource usage

Liveness and readiness probes

Node stability

Commands
kubectl describe pod <pod>
kubectl top pod <pod>

12. Pods cannot resolve DNS names
Theory

Kubernetes uses CoreDNS for internal and external DNS resolution.

What to Check

CoreDNS pod status

kube-dns service and endpoints

Pod DNS configuration

Commands
kubectl get pods -n kube-system
kubectl get svc kube-dns -n kube-system
kubectl exec <pod> -- cat /etc/resolv.conf

13. kubectl exec into a pod is timing out
Theory

Exec requires network connectivity between API server, node, and kubelet.

What to Check

Pod readiness

Network connectivity to node

Kubelet status

Commands
kubectl get pod <pod>
systemctl status kubelet

14. Service shows endpoints = 0
Theory

Endpoints are created only when Ready pods match service selectors.

Common Causes

Selector mismatch

Pods not Ready

Namespace mismatch

Commands
kubectl get endpoints <svc>
kubectl get pods --show-labels

15. Application cannot pull images from a private registry
Theory

Image pull failures occur due to authentication, naming, or network issues.

What to Check

Image name and tag

imagePullSecrets configuration

Node access to registry

Commands
kubectl describe pod <pod>
kubectl get secret

16. Pods are getting OOMKilled even with enough limits
Theory

OOMKill happens when memory usage exceeds container or namespace limits.

Possible Causes

Application memory leaks

ResourceQuota or LimitRange restrictions

Sudden memory spikes

Commands
kubectl describe pod <pod>
kubectl get resourcequota

17. Cluster Autoscaler is not scaling nodes
Theory

Cluster Autoscaler reacts only to unschedulable Pending pods.

What to Check

Pending pods exist

Scheduling constraints

Pod resource requests exceed node capacity

Commands
kubectl get pods --field-selector=status.phase=Pending

18. Deployment rollout is stuck
Theory

Rollouts pause when new pods fail readiness or availability constraints.

What to Check

Deployment events

Pod readiness status

Rollout strategy

Commands
kubectl rollout status deployment/<name>
kubectl describe deployment <name>

19. 502 / 504 errors at Ingress during traffic spikes
Theory

These errors indicate backend services are slow or unreachable.

What to Check

Ingress controller logs

Service and pod health

Timeout configuration and autoscaling

Commands
kubectl logs -n ingress-nginx <controller-pod>
kubectl get hpa

20. EKS pods need AWS access without access keys
Theory

AWS recommends using IAM roles instead of static credentials.

Solution

Use IAM Roles for Service Accounts (IRSA)

Attach IAM role to Kubernetes service account

Commands
kubectl annotate serviceaccount <sa> eks.amazonaws.com/role-arn=<arn>

