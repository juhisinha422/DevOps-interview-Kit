# Senior DevOps Engineer Interview Q&A (4+ Years Experience)

## 1. Tell me about your day-to-day work as a DevOps Engineer

**Answer:**
In my current role, my day typically involves managing CI/CD pipelines, monitoring infrastructure, and ensuring system reliability. I work extensively with AWS services like EC2, S3, IAM, and EKS.

* Monitor deployments and troubleshoot failures
* Optimize CI/CD pipelines (Jenkins/GitHub Actions)
* Manage Kubernetes clusters (deployments, scaling, troubleshooting)
* Implement Infrastructure as Code using Terraform
* Work on logging and monitoring using CloudWatch/Prometheus/Grafana
* Collaborate with developers to improve deployment strategies

I focus on automation, reliability, and reducing manual intervention.

---

## 2. What is your experience with AWS?

**Answer:**
I have hands-on experience designing and managing cloud infrastructure in AWS:

* Compute: EC2, Auto Scaling
* Containers: EKS, ECS
* Storage: S3, EBS
* Networking: VPC, Subnets, Route Tables, NAT Gateway
* Security: IAM roles, policies, IRSA
* CI/CD: CodePipeline, Jenkins
* Monitoring: CloudWatch, CloudTrail

I’ve also worked on cost optimization and high availability architectures.

---

## 3. Explain your experience with Kubernetes and EKS specifically

**Answer:**
I have worked extensively with Kubernetes and AWS EKS:

* Deployed and managed EKS clusters using Terraform
* Managed node groups (managed and self-managed)
* Configured IAM Roles for Service Accounts (IRSA) for secure pod access
* Used AWS VPC CNI for pod networking
* Managed deployments, services, ingress controllers
* Troubleshooting pod failures, crash loops, networking issues

EKS differs from AKS/GKE mainly in IAM integration, networking setup, and cluster control plane management.

---

## 4. What is IRSA (IAM Roles for Service Accounts)?

**Answer:**
IRSA allows Kubernetes pods to assume IAM roles securely.

* Maps Kubernetes service accounts to AWS IAM roles
* Eliminates need for storing AWS credentials inside pods
* Uses OIDC identity provider

This improves security and follows least privilege access.

---

## 5. How would you migrate Argo CD from one cluster to another?

**Answer:**
Steps:

1. Backup from Cluster A:

   * Export all Kubernetes manifests
   * Backup secrets (repo credentials, tokens)
   * Export Argo CD applications

2. Setup Cluster B:

   * Install Argo CD using Helm with same values
   * Configure access and authentication

3. Restore:

   * Apply backed-up manifests and secrets
   * Reconnect Git repositories

4. Validate:

   * Ensure all applications sync properly
   * Check health status (should be “Healthy” and “Synced”)

---

## 6. How would you set up EKS with Argo CD from scratch in a new AWS account?

**Answer:**
**Step-by-step approach:**

1. **Account Setup:**

   * Configure IAM users/roles
   * Enable billing alerts

2. **Networking:**

   * Create VPC
   * Setup public/private subnets across AZs
   * Configure Internet Gateway & NAT Gateway

3. **EKS Cluster:**

   * Create cluster using Terraform/eksctl
   * Configure control plane

4. **Node Groups:**

   * Add managed node groups
   * Configure autoscaling

5. **Access Setup:**

   * Update kubeconfig
   * Setup RBAC

6. **Install Argo CD:**

   * Deploy via Helm
   * Expose via LoadBalancer/Ingress

7. **GitOps Setup:**

   * Connect Git repository
   * Deploy applications

8. **Validation:**

   * Ensure applications sync successfully

---

## 7. Rolling Update vs Canary Deployment

**Answer:**

**Rolling Update:**

* Gradually replaces old pods with new ones
* No downtime
* Entire traffic shifts progressively

**Canary Deployment:**

* Releases new version to small subset of users
* Monitor performance before full rollout
* Reduces risk of failure

---

## 8. ALB vs NLB

**Answer:**

**Application Load Balancer (ALB):**

* Layer 7 (HTTP/HTTPS)
* Supports path-based and host-based routing
* Ideal for microservices and web apps

**Network Load Balancer (NLB):**

* Layer 4 (TCP/UDP)
* Ultra-low latency
* Static IP support
* Suitable for high-performance or internal services

---

## 9. How do you restrict image pulls to only Amazon ECR?

**Answer:**

* Use IAM policies to allow only ECR access
* Block public registries via network policies
* Use Kubernetes ImagePolicyWebhook (if needed)
* Configure private repositories
* Use VPC endpoints for ECR access
* Enforce via admission controllers (OPA/Gatekeeper)

---

## 10. Linux Basics (User creation & SSH setup)

**Answer:**

**Create user:**

```bash
sudo adduser devopsuser
```

**Grant sudo access:**

```bash
sudo usermod -aG sudo devopsuser
```

**Generate SSH key:**

```bash
ssh-keygen -t rsa -b 4096
```

**Setup SSH access:**

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

**Add public key to authorized_keys**

---

## Final Tips

* Be specific with real-world experience
* Focus on “how” not just “what”
* Explain decision-making and trade-offs
* Practice architecture explanations clearly
* Don’t ignore basics like Linux and networking

---

**Use this as a quick revision guide before interviews.**
