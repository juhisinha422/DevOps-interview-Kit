# 📘 Infogain AWS DevOps Engineer Interview – Answers (4+ Years Experience)

---

## 1. can you explain VPC architecture?

A VPC is a logically isolated network in AWS.

**Components:**

* Public Subnet → Internet-facing resources
* Private Subnet → Backend/DB
* Internet Gateway → Internet access
* NAT Gateway → Private subnet outbound access
* Route Tables → Traffic routing
* Security Groups & NACL → Security

---

## 2. Explain NACL and SG diffrence between them

| Feature    | Security Group | NACL               |
| ---------- | -------------- | ------------------ |
| Level      | Instance       | Subnet             |
| Type       | Stateful       | Stateless          |
| Rules      | Allow only     | Allow & Deny       |
| Evaluation | All rules      | Rule order matters |

---

## 3. Can you explain the CICD in your organization in your role?

* Code pushed to GitHub
* GitHub Actions/Jenkins triggers pipeline
* Build using Maven
* Docker image created & pushed
* Deploy to Kubernetes using Helm
* Monitor using CloudWatch

---

## 4. can you explain the step-by-step process, like the cardinal steps you have taken to create the cluster?

1. Create VPC & networking (Terraform)
2. Create IAM roles
3. Provision EKS cluster
4. Configure worker nodes/node groups
5. Setup kubectl access
6. Deploy apps using Helm

---

## 5. when Pods are in Pending stage, how will you troubleshoot?

* Check resources (CPU/memory)
* Node availability
* Taints/tolerations
* PVC binding

```bash
kubectl describe pod
```

---

## 6. What is image pull back of?

👉 Likely **ImagePullBackOff**

Occurs when:

* Wrong image name
* Auth issue
* Image not available

---

## 7. Can you heard about the distroless images?

Minimal images without OS package manager → more secure, lightweight.

---

## 8. explain your project architecture in the current project?

Example:

* Frontend → React (S3 + CloudFront)
* Backend → Spring Boot (EKS)
* DB → RDS
* CI/CD → GitHub Actions
* Monitoring → CloudWatch

---

## 9. Have you used any other like a tools for secret management?

* AWS Secrets Manager
* Parameter Store
* Kubernetes Secrets
* HashiCorp Vault

---

## 10. What is the difference between AWS Secret Management and Parameter Store?

| Feature  | Secrets Manager | Parameter Store |
| -------- | --------------- | --------------- |
| Rotation | Automatic       | Manual          |
| Cost     | Paid            | Free tier       |
| Use case | DB secrets      | Config values   |

---

## 11. what are the challenges you are faced in your current project or previous projects?

* Deployment failures
* Pod crashes
* Cost optimization
* CI/CD pipeline delays

👉 Example answer:
“Handled pod crash due to memory leak by tuning resources and adding liveness probe.”

---

## 12. suppose in an application hosted in a ec2 instance through elastic load balancer, you cannot access it, so what are the steps you

* Check EC2 status
* Check Security Group
* Check ELB health check
* Verify port listening
* Check logs

---

## 13. Explain Policies in Route53

* Simple
* Weighted
* Latency
* Failover
* Geolocation

---

## 14. Have you got any deployment strategies?

* Rolling Update
* Blue-Green
* Canary

---

## 15. What are the differences between Canary and Blue/Green deployment?

| Feature  | Canary  | Blue-Green  |
| -------- | ------- | ----------- |
| Traffic  | Gradual | Full switch |
| Risk     | Low     | Medium      |
| Rollback | Easy    | Quick       |

---

## 16. How do you implement lambda functions?

* Write function (Python/Node)
* Upload via console/CLI
* Attach IAM role
* Trigger via API Gateway/S3

---

## 17. Have you worked on cost optimization?

* Use Reserved Instances
* Right-size instances
* Auto-scaling
* Remove unused resources

---

## 18. explain Terraform life cycle?

```bash
terraform init
terraform plan
terraform apply
terraform destroy
```

---

## 19. explain auto-scaling?

Auto Scaling automatically adjusts resources based on demand.

**Types:**

* EC2 Auto Scaling
* Kubernetes HPA (Horizontal Pod Autoscaler)

**How it works:**

* Define min, max, desired capacity
* Based on metrics (CPU, memory)
* Scale out (increase instances)
* Scale in (decrease instances)

👉 Example:

* CPU > 70% → scale out
* CPU < 30% → scale in

---

## 🎯 Final Tip (4+ Years Answer Style)

Always answer like:
👉 “In my project, we used Auto Scaling with CloudWatch metrics to handle traffic spikes and reduce cost during low usage.”

---

✅ Real interview aligned answers
💼 Perfect for **Infogain / AWS DevOps 4 yrs**
🚀 Ready to copy as **README.md**
