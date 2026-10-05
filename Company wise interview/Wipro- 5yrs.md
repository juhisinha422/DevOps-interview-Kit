# Questions asked recently for wipro for 5+ year experience

---

## If my state files got deleted in terraform what will I do ?

* Check if remote backend (S3/Azure Blob) is configured
* Restore state from **versioning (S3 versioning)**
* If no backup:

  * Use `terraform refresh` to sync state with real infra
  * Use `terraform import` to re-import resources manually
* Validate using:

```bash
terraform plan
```

* Best practice: Always enable remote backend + locking (DynamoDB)

---

## What are node affinity and pod affinity?

* **Node Affinity**: Schedule pods on specific nodes based on labels
* **Pod Affinity**: Schedule pods close to other pods
* **Pod Anti-Affinity**: Avoid placing pods together

---

## Explain ur project in detail from scratch how it works?

* Code pushed to Git (GitHub/GitLab)
* CI triggered (Jenkins/GitHub Actions)
* Build Docker image
* Push to registry (ECR/DockerHub)
* Terraform provisions infra (VPC, EKS)
* Deploy using Helm/Kubectl
* Monitoring (Prometheus/Grafana)
* Logging (ELK)

---

## What if someone delete my pod which is running in production has kind deployment what will happen and what will u do ?

* Deployment controller will recreate pod automatically
* No impact if replicas > 1
* Steps:

```bash
kubectl get pods
kubectl describe pod <pod>
```

* Check logs and ensure desired replicas maintained

---

## Explain what u did to optimise the cost in ur current infrastructure?

* Used Auto Scaling
* Used Spot instances
* Rightsizing EC2
* S3 lifecycle policies
* Removed unused resources
* Reserved instances

---

## What security measures do u follow to maintain a ci/cd pipelines?

* Use secrets manager (no hardcoded secrets)
* Role-based access (RBAC)
* Secure Jenkins with authentication
* Use HTTPS
* Scan images (Trivy)
* Least privilege IAM

---

## Explain me the challenges situation u came across which u did not solved?

* Example:

  * Faced complex network latency issue
  * Tried debugging using logs/monitoring
  * Could not fully resolve
  * Escalated to senior team
  * Learned better troubleshooting approach

---

## If new feature is released will u be following deployment strategies?

* Yes:

  * Rolling deployment
  * Blue-Green
  * Canary
* Goal: zero downtime and safe rollout

---

## What is drift in k8s cluster ?

* Difference between desired state and actual state

---

## My website went into 503 service unavailable state what u will do now ?

* Check pods:

```bash
kubectl get pods
```

* Check service & endpoints
* Check ingress / load balancer
* Check logs
* Check CPU/memory
* Restart pods if required

---

## Say ur collguue accidentally deleted the jenkinsfile while doing copy paste which was critical for production and u do not have backup and u r working since ur collgue shift ends and product manager came to u and scolder u what will u do and how will u handle such situation without blaming ur collgue.

* Stay calm and professional
* Do not blame colleague
* Try recovery:

  * `git reflog`
  * Previous commits
  * Existing Jenkins job config
* Recreate Jenkinsfile quickly
* Communicate clearly with PM
* Focus on resolution first

---

## What is git fetch and git pull?

| Command   | Meaning                |
| --------- | ---------------------- |
| git fetch | Downloads changes only |
| git pull  | Fetch + merge          |

---
