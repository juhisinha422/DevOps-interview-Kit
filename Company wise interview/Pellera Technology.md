# Senior DevOps Interview – Pellera Technology (Round 1 Answers)

---

## What steps do you follow to create infra for an 3Tier Application?

1. **Requirement gathering**

   * Define web, app, DB tiers
   * Decide scalability, HA, security

2. **Network design**

   * Create VPC
   * Public subnet (ALB)
   * Private subnet (App + DB)
   * Internet Gateway + NAT Gateway

3. **Security**

   * Security Groups (least privilege)
   * NACL (subnet level control)

4. **Compute layer**

   * Web/App servers (EC2 or EKS)
   * Auto Scaling Groups

5. **Database layer**

   * RDS (Multi-AZ, backups enabled)

6. **Load balancing**

   * Application Load Balancer

7. **Storage**

   * S3 for static content

8. **Monitoring**

   * CloudWatch, logs, alerts

9. **Automation**

   * Terraform for provisioning
   * CI/CD for deployments

---

## How would you reduce docker image size?

* Use **alpine/slim base images**
* Multi-stage builds
* Remove unnecessary packages
* Use `.dockerignore`
* Combine RUN commands
* Avoid cache layers

---

## What deployment strategies do you use, explain?

* **Rolling Deployment** → gradual update
* **Blue-Green** → switch traffic between environments
* **Canary** → release to small % users first
* **Recreate** → stop old, start new

---

## Explain data types in Terraform?

* **string** → `"value"`
* **number** → `10`
* **bool** → `true/false`
* **list** → `["a","b"]`
* **map** → `{key="value"}`
* **object** → structured data
* **tuple** → ordered mixed types

---

## Have you faced any problems in production systems, explain how did you resolve that.

**Example:**

* Issue: Application downtime due to high CPU
* Action:

  * Checked metrics (CloudWatch)
  * Identified traffic spike
  * Scaled Auto Scaling Group
* Fix:

  * Enabled auto scaling policy
* Result:

  * System stabilized

---

## Explain Kubernetes architecture.

* **Master Components:**

  * API Server
  * Scheduler
  * Controller Manager
  * etcd (key-value store)

* **Worker Nodes:**

  * Kubelet
  * Kube-proxy
  * Container runtime

* **Flow:**

  * User → API Server → Scheduler → Node → Pod

---

## How do you upgrade Kubernetes cluster?

* Backup etcd
* Upgrade control plane
* Upgrade worker nodes
* Drain nodes before upgrade:

```bash id="k8sdrain"
kubectl drain <node>
```

* Upgrade kubelet/kubeadm
* Uncordon node:

```bash id="k8suncordon"
kubectl uncordon <node>
```

---

## How do you manage resources via terraform if they are already created on cloud.

* Use `terraform import` to bring resources into state
* Write matching Terraform code
* Run:

```bash id="tfimport"
terraform import <resource> <id>
```

* Validate using `terraform plan`

---

## What is nat gateway?

* Allows **private subnet instances** to access internet
* Prevents inbound internet traffic
* Used for outbound connectivity (updates, APIs)

---
