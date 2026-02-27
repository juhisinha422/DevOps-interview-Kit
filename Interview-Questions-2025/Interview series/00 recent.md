# DevOps Interview Preparation – Spoken Format (4+ Years Experience)
Interview Date: 26/02/2026

This document contains natural spoken-style answers designed for real interview delivery.

---

## 1. Have you integrated TLS in a Load Balancer?

Yes, in production I have configured TLS termination at the Application Load Balancer level using AWS Certificate Manager. I create an HTTPS listener on port 443 and attach the ACM certificate. The ALB handles SSL termination and forwards traffic securely to backend target groups. In some cases, I also enable end-to-end encryption between ALB and backend instances. Additionally, I enforce strong TLS security policies and integrate AWS WAF for enhanced security.

---

## 2. Have you created a Load Balancer? What type?

Yes, I have created both Application Load Balancers and Network Load Balancers. For web applications and microservices, I mainly use ALB because it supports Layer 7 routing such as path-based and host-based routing. For high-performance TCP workloads requiring low latency and static IPs, I use Network Load Balancer.

---

## 3. Difference between ALB and NLB

Application Load Balancer operates at Layer 7 and supports HTTP/HTTPS routing with advanced features like path-based routing and WAF integration. Network Load Balancer operates at Layer 4, handles TCP/UDP traffic, provides ultra-low latency, and supports static IP addresses. ALB is ideal for web apps, while NLB is suited for performance-critical systems.

---

## 4. EC2 Pricing Models

EC2 pricing includes On-Demand, Reserved Instances, Savings Plans, and Spot Instances. For production workloads with predictable usage, I prefer Reserved Instances or Savings Plans to optimize costs. Spot Instances are used for fault-tolerant workloads like CI/CD runners or batch jobs. On-Demand is used for short-term or unpredictable workloads.

---

## 5. When to Use Specific Pricing Models

I use On-Demand for testing or temporary workloads. Reserved Instances or Savings Plans are used when workload is stable and long-term. Spot Instances are ideal for non-critical or interruptible workloads to reduce cost significantly.

---

## 6. IAM Roles and Policies

IAM policies are JSON documents that define permissions. IAM roles are attached to AWS services to grant temporary credentials securely. For example, I attach an IAM role to EC2 instances so they can access S3 without storing access keys. I always follow the principle of least privilege in production.

---

## 7. How Would You Optimize a 500MB Docker Image?

To optimize a large Docker image, I use a minimal base image like Alpine, implement multi-stage builds, remove unnecessary dependencies, clean package caches, combine RUN commands to reduce layers, and use .dockerignore to exclude unwanted files. I also scan images for vulnerabilities before pushing to registry.

---

## 8. Multi-Stage Build in Docker

Multi-stage build allows separating the build environment from the runtime environment. For example, I build the application in a builder image and then copy only the compiled artifact into a lightweight runtime image. This reduces image size and improves security.

---

## 9. Installing yum Packages in Private Subnet

For EC2 instances in private subnets, I use a NAT Gateway for outbound access. Alternatively, I configure VPC Endpoints or maintain an internal yum repository mirror. I also use AWS Systems Manager for patching and management without exposing instances to the public internet.

---

## 10. RDS Project Data

In my RDS projects, I managed structured relational data like user information, product details, and order transactions. The database was configured in Multi-AZ for high availability with automated backups and monitoring enabled via CloudWatch.

---

## 11. Troubleshooting CPU Utilization Above 80%

First, I check CloudWatch metrics to confirm sustained high CPU usage. Then I log into the instance and use tools like top or htop to identify processes consuming resources. I analyze logs, check for inefficient queries or traffic spikes, and implement scaling through Auto Scaling Groups if required.

---

## 12. Purpose of Kubernetes ConfigMap

ConfigMap is used to store non-sensitive configuration data separately from container images. It helps manage environment-specific configuration without rebuilding the image.

---

## 13. Types of Kubernetes Services

Kubernetes services include ClusterIP for internal communication, NodePort for exposing services on node IP, LoadBalancer for external exposure via cloud provider, and ExternalName for mapping to external DNS.

---

## 14. Difference Between Service and LoadBalancer Service

A Service provides stable networking for pods inside the cluster. A LoadBalancer service is a type of service that provisions an external cloud load balancer to expose the application publicly.

---

## 15. Troubleshooting CrashLoopBackOff

I check pod logs using kubectl logs and inspect events using kubectl describe pod. Common causes include incorrect environment variables, missing secrets, insufficient memory, or application crashes. I verify resource limits and health probes.

---

## 16. Segregating dev, test, UAT, prod Environments

I segregate environments using namespaces, RBAC, resource quotas, network policies, and environment-specific configurations. In production scenarios, separate clusters may be used for complete isolation.

---

## 17. Can a Deleted Namespace Be Recovered?

No, once a namespace is fully deleted, it cannot be recovered unless we have etcd or Velero backups. Backup strategy is critical in production clusters.

---

## 18. Kubernetes Architecture

Kubernetes consists of Control Plane components like API Server, Scheduler, Controller Manager, and etcd. Worker nodes run kubelet, kube-proxy, and container runtime. The control plane manages cluster state, while worker nodes run application workloads.

---

## 19. Pod Restart Behavior

When a pod is restarted, Kubernetes deletes and recreates it. Any ephemeral data inside the container is lost unless Persistent Volumes are attached.

---

## 20. git fetch vs git pull

git fetch downloads changes without merging. git pull downloads and automatically merges changes into the current branch.

---

## 21. Review Two Branches Before Merge

I use:
git diff branch1 branch2

This allows me to review differences before merging.

---

## 22. Git Pipeline Life Cycle

The CI/CD lifecycle includes build, test, security scan, package, deploy, and monitor stages. Code push triggers pipeline execution automatically.

---

## 23. CI Keywords: only, rules, trigger

only is used to restrict jobs to specific branches. rules provide advanced conditional logic. trigger is used to initiate downstream or child pipelines.

---

## 24. GitLab Runner Executors

Executors include Shell, Docker, Kubernetes, and Virtual Machine executors. In scalable environments, Docker or Kubernetes executors are preferred.

---

## 25. Practical Environment Created Using Terraform

I have provisioned VPC, subnets, Internet Gateway, NAT Gateway, EC2, Auto Scaling Groups, ALB, RDS, IAM roles, and Security Groups using Terraform. The code was modular and reusable across environments.

---

## 26. Production Environments with Terraform

Yes, I have created production environments using Terraform with remote backend in S3 and state locking via DynamoDB. I structured modules for reusability and integrated Terraform with CI/CD pipelines.

---

## 27. Purpose of Terraform tfstate File

The tfstate file stores the current state of infrastructure and maps Terraform configuration to real cloud resources. In production, it is stored remotely in S3 with DynamoDB locking to prevent conflicts.
