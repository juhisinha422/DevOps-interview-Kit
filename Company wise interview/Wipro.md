# Wipro DevOps Engineer Interview – Answers (4 Years Experience)

---

## 1. Self introduction
I am a DevOps Engineer with around 4 years of experience working on AWS, Kubernetes, Docker, and Terraform. I have hands-on experience in building CI/CD pipelines using Jenkins/GitHub Actions, containerizing applications, deploying on EKS, and managing infrastructure using Terraform. I also have experience in monitoring, troubleshooting production issues, and implementing scalable and secure architectures.

---

## 2. I'm making an application instance, okay? I want to create my application inside delivery, and then what do I do? in aws
After creating the application:
- Launch EC2 instance or deploy on EKS
- Configure security groups
- Install application dependencies
- Deploy code (via CI/CD)
- Attach ALB for traffic routing
- Configure Auto Scaling if required
- Setup monitoring (CloudWatch)

---

## 3. So currently the hostels may application in the CSC distance. Okay. I want to make my application highly available. So how we will make it?
- Deploy application across multiple Availability Zones
- Use Auto Scaling Group
- Attach Application Load Balancer
- Use RDS Multi-AZ for database
- Store static data in S3
- Add health checks

---

## 4. from my application, my application was hosted in one of the private subnet of AWS. so the application is hosted in EC2 instance and EC2 instance is in the private subnet of the VPC. so from this, I want to access one URL. You can say Google.com. We want to access URL, but I am not able to connect it.
Reason:
Private subnet has no internet access.

Fix:
- Add NAT Gateway in public subnet
- Update route table (0.0.0.0/0 → NAT Gateway)

---

## 5. You fix the net gateway and you make another necessary routes in routing table, so those two get fixed. And in SDP, what changes you will do?
- Allow outbound traffic in Security Group (port 80/443)
- Ensure NACL allows outbound/inbound ephemeral ports
- Check DNS resolution enabled in VPC

---

## 6. Explain architecture of Kubernetes
- Master Node:
  - API Server
  - Scheduler
  - Controller Manager
  - etcd
- Worker Node:
  - Kubelet
  - Kube Proxy
  - Pods (containers)
Flow: User → API Server → Scheduler → Node → Pod

---

## 7. what do you mean by sidecar containers?
Sidecar container runs alongside main container in same pod to provide supporting functionality like logging, monitoring, proxy.

---

## 8. What do you mean demon shit?
DaemonSet:
Ensures one pod runs on every node (or selected nodes).
Used for logging, monitoring agents.

---

## 9. in the current season, I'm having some like DB credentials and URL authentication tokens. So where do we store this thing in Kubernetes?
Store in Kubernetes Secrets (not ConfigMap for sensitive data).

---

## 10. what do you mean by Helm chart?
Helm chart is a package of Kubernetes manifests used to deploy applications easily using templates.

---

## 11. you need to deploy the Helm chart to do staging and a prod environment. How do you manage the differences?
- Use separate values.yaml files (values-staging.yaml, values-prod.yaml)
- Override configs using --values flag

---

## 12. if I do the Helm upgrade, if it fails, it automatically needs to roll back. So at that time, what you will do?
Use:
helm upgrade --install --atomic
This ensures automatic rollback on failure.

---

## 13. what are all the loops available in Terraform?
- count
- for_each
- for loop (expressions)

---

## 14. I'm having a database, middle server, and presentation layer, that is web servers. So everything, I set it in Terraform. I have created everything in Terraform. I need to create all these things on daily basis. I need to give Terraform apply. And if necessary, it will create a resource. And at evening, need to destroy the things. Okay. So what's happening?
Terraform checks state file:
- If resources exist → no changes
- If not → creates resources
- terraform destroy removes all resources

This is idempotency behavior.

---

## 15. what do you mean by probe?
Probes check container health:
- Liveness Probe → restart container if unhealthy
- Readiness Probe → check if ready to serve traffic

---

## 16. what do you mean by taints and tolerations?
- Taints → restrict pods from scheduling on nodes
- Tolerations → allow pods to be scheduled on tainted nodes

---

## 17. in GitHub, you want to develop one new feature. So you have one feature branch from the main main branch, and you are properly committing the changes for your requirement. your colleagues who are working with you, they have some main changes in that branch. now you have a temporary branch and feature branch it contains many commits. now we want to complete your development and now you want to push the code to main branch. At that time, you want to push only the changes what you have performed.
- Use git rebase or cherry-pick
- Rebase feature branch with latest main:
  git fetch
  git rebase origin/main
- Resolve conflicts
- Push clean commits

---

## 18. I got to take some files which are greater than 15 GB, one file from the Linux server. could you provide a command.
Use rsync or scp:

scp largefile user@host:/path/

OR better:
rsync -avz largefile user@host:/path/

---

## Key Takeaway
Strong understanding of AWS networking, Kubernetes, Terraform, and Git workflows is important for DevOps roles.

## Pro Tip (4 Years Experience)
Focus on real-time scenarios, troubleshooting steps, and explaining architecture clearly.
