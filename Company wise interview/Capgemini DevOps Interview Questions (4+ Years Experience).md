# 🚀 Capgemini DevOps Interview Questions (4+ Years Experience)

---

## Git / Source Control:

### 1.Difference between Git Fetch and Git Merge

* **Git Fetch**

  * Downloads latest changes from remote
  * Does NOT modify local working branch
  * Used for safe inspection

* **Git Merge**

  * Merges fetched changes into current branch
  * Updates working directory
  * Can create merge conflicts

---

### 2.What are the conflicts you have seen in Git?

* Same line modified in two branches
* File deleted in one branch but modified in another
* Merge conflicts during rebase
* Binary file conflicts

**Resolution:**

* Manual fix
* `git mergetool`
* Proper branching strategy

---

## Docker:

### 3.Explain Docker Client Architecture

* **Docker Client** → CLI commands (`docker run`, `build`)
* **Docker Daemon** → Executes requests
* **Docker Images** → Templates
* **Docker Containers** → Running instances
* **Docker Registry** → Stores images

Flow:
Client → Docker Daemon → Image → Container

---

## Maven:

### 4.What are the Maven Phases?

* validate
* compile
* test
* package
* verify
* install
* deploy

---

## CI/CD / Pipelines:

### 5.What are the different types of pipelines?

* CI Pipeline (Build + Test)
* CD Pipeline (Deployment)
* Release Pipeline
* Multi-branch pipeline
* Blue-Green / Canary pipeline

---

## GCP Networking:

### 6.Difference between Shared VPC and VPC Peering

| Shared VPC              | VPC Peering       |
| ----------------------- | ----------------- |
| Centralized network     | Connects two VPCs |
| Managed by host project | Independent       |
| Better for large orgs   | Simple use cases  |
| Central IAM control     | Separate IAM      |

---

## Kubernetes Basics:

### 7.What is a DaemonSet?

* Runs one pod per node
* Used for logging and monitoring agents

---

### 8.What are Taints and Tolerations?

* **Taints** → Restrict pod scheduling
* **Tolerations** → Allow pods to run on tainted nodes

---

### 9.Issues you identified in deployment times

* Slow image pulling
* Large Docker images
* Resource constraints
* Pod scheduling delays
* Readiness probe failures

---

### 10.Resolutions implemented to fix them

* Used lightweight base images (Alpine)
* Implemented multi-stage builds
* Enabled image caching
* Optimized resource requests/limits
* Tuned readiness/liveness probes

---

### 11.Which command is used to describe pod status?

```bash
kubectl describe pod <pod-name>
```

---

### 12.What is the use of Namespace in Kubernetes

* Logical isolation
* Resource separation
* Multi-team environment support

---

## Kubernetes Troubleshooting:

### 13.Difference between CrashLoopBackOff and ImagePullBackOff

| CrashLoopBackOff             | ImagePullBackOff            |
| ---------------------------- | --------------------------- |
| Container crashes repeatedly | Image cannot be pulled      |
| App/config issue             | Registry/auth/network issue |

---

### 14.Reasons for CrashLoopBackOff

* Application crash
* Wrong configuration
* Missing environment variables
* Port conflicts
* Out of memory (OOMKilled)

---

### 15.Different types of Probes

* Liveness Probe → Restart container
* Readiness Probe → Control traffic
* Startup Probe → Handle slow startup apps

---

## Terraform:

### 16.Major use of Terraform Lint

* Identify syntax issues
* Enforce best practices
* Improve code quality

---

### 17.What is Terraform Drift

* Difference between actual infra and Terraform state
* Happens when manual changes are made outside Terraform

---

### 18.What is Terraform Taint

* Marks a resource for recreation

```bash
terraform taint <resource_name>
```

---

### 19.What is for_each in Terraform

* Used to create multiple resources using map/set
* Provides better control than count

---

### 20.What is count in Terraform

* Used to create fixed number of resources

```hcl
count = 3
```

---

### 21.Difference between for_each and count in Terraform

| for_each                 | count                      |
| ------------------------ | -------------------------- |
| Uses map/set             | Uses index                 |
| Stable resource tracking | Can break if order changes |
| Preferred approach       | Less flexible              |

---

## Kubernetes Autoscaling:

### 23.Difference between Horizontal Pod Autoscaler (HPA) and Vertical Pod Autoscaler (VPA)

| HPA                   | VPA               |
| --------------------- | ----------------- |
| Scales number of pods | Scales CPU/Memory |
| Based on metrics      | Adjusts resources |
| Horizontal scaling    | Vertical scaling  |

---

## Kubernetes / GKE Services:

### 24.Different types of Services in GKE/Kubernetes

* ClusterIP
* NodePort
* LoadBalancer
* ExternalName

---

### 25.NodePort IP/port range

* 30000 - 32767

---

### 26.Different types of Load Balancers

* Network Load Balancer (Layer 4)
* HTTP(S) Load Balancer (Layer 7)
* Internal Load Balancer

---

### 27.Difference between Load Balancer and Ingress

| LoadBalancer         | Ingress                   |
| -------------------- | ------------------------- |
| Works at Layer 4     | Works at Layer 7          |
| One service exposure | Multiple services routing |
| Costly               | Cost-efficient            |

**Which is best: Load Balancer or Ingress?**

* Ingress → Best for microservices
* LoadBalancer → Best for simple exposure

---

## GCP Serverless:

### 28.Difference between Cloud Run and Cloud Functions

| Cloud Run          | Cloud Functions |
| ------------------ | --------------- |
| Container-based    | Function-based  |
| Flexible runtime   | Limited runtime |
| Supports HTTP apps | Event-driven    |

---

## GCP Storage:

### 29.Different types of Buckets in Google Cloud Storage

* Standard
* Nearline
* Coldline
* Archive

---

## ✅ Final Tips (4+ Years Experience)

* Focus on real-time scenarios
* Explain troubleshooting steps clearly
* Mention optimization & cost-saving strategies
* Use examples from your projects

---

🔥 Ready to copy-paste into GitHub README
