# LTM DevOps Interview Questions & Answers (4 Years Experience)

---

# Kubernetes

## 1. Difference between a Deployment and a StatefulSet?

### Answer

A **Deployment** is used for **stateless applications** where Pods are interchangeable and do not require persistent identities. Examples include web applications, APIs, and microservices. Deployments support rolling updates, rollbacks, and automatically manage ReplicaSets.

A **StatefulSet** is used for **stateful applications** such as PostgreSQL, MySQL, MongoDB, Kafka, or Elasticsearch. Each Pod has a stable hostname, a unique network identity, and its own Persistent Volume that remains attached even if the Pod is recreated. StatefulSets also support ordered deployment and termination, which is important for clustered databases.

In production, I use Deployments for stateless workloads and StatefulSets for applications that require persistent storage and stable identities.

---

## 2. How does a Service discover and route traffic to Pods?

### Answer

A Kubernetes Service uses **labels and selectors** to identify the Pods that belong to it. When a Pod is created with matching labels, it is automatically added to the Service's Endpoints.

When a client sends a request to the Service, **kube-proxy** uses iptables or IPVS rules to distribute traffic across all healthy Pods associated with that Service. Pods are automatically added or removed from the endpoint list as they become ready or terminate.

For external traffic, requests typically follow this flow:

**Client → Load Balancer/Ingress → Service → Pod**

This provides a stable endpoint even though Pod IPs change over time.

---

## 3. How would you troubleshoot a Pod stuck in CrashLoopBackOff?

### Answer

I start by describing the Pod using:

```bash
kubectl describe pod <pod-name>
```

to review events and identify restart reasons.

Next, I inspect the application logs:

```bash
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
```

I then verify ConfigMaps, Secrets, environment variables, mounted volumes, image versions, startup commands, liveness and readiness probes, and resource limits.

I also check whether the application can connect to required services such as databases or APIs.

If the issue started after a deployment, I compare the current release with the previous working version and roll back if necessary.

---

# GitHub

## 4. Difference between Merge and Rebase?

### Answer

A **Merge** combines two branches by creating a new merge commit. It preserves the complete commit history and clearly shows when branches were integrated.

A **Rebase** moves commits from one branch onto another, creating a cleaner and more linear history by rewriting commit hashes.

In production, I generally use **Merge** for shared branches because it preserves history. I use **Rebase** on my local feature branch before creating a Pull Request to keep the commit history clean, but I avoid rebasing branches that others are already using.

---

## 5. How do you handle merge conflicts in a team workflow?

### Answer

When a merge conflict occurs, I first pull the latest changes from the target branch and attempt the merge or rebase.

Git highlights the conflicting files, and I manually review each conflict, discuss any business logic conflicts with the development team if needed, and resolve them carefully.

After resolving the conflicts, I test the application, commit the resolved changes, and push the updated branch.

To reduce future conflicts, I encourage developers to create small Pull Requests, rebase frequently, and integrate changes regularly.

---

## 6. GitFlow vs Trunk-Based Development — what's your branching strategy?

### Answer

**GitFlow** uses long-lived branches such as `main`, `develop`, `feature`, `release`, and `hotfix`. It is suitable for large projects with scheduled releases and strict release management.

**Trunk-Based Development** encourages developers to commit small changes frequently to a single main branch or very short-lived feature branches. It supports faster CI/CD and continuous delivery.

In my projects, I primarily follow a GitFlow-style workflow with feature branches, Pull Requests, code reviews, and release branches because it aligns well with controlled production deployments.

---

# Jenkins

## 7. Declarative vs Scripted Pipeline — how do you structure each?

### Answer

A **Declarative Pipeline** uses a structured syntax with predefined sections such as `pipeline`, `agent`, `stages`, and `steps`. It is easier to read, maintain, and is the preferred choice for most CI/CD pipelines.

A **Scripted Pipeline** is written entirely in Groovy and provides greater flexibility for complex workflows, conditional logic, loops, and dynamic behavior.

In production, I generally use Declarative Pipelines because they are standardized and easier for teams to maintain, while using Scripted syntax only for advanced use cases that require more flexibility.

---

## 8. How do you manage secrets/credentials in a pipeline?

### Answer

I never hardcode passwords, API keys, or tokens in Jenkinsfiles.

Instead, I store them securely in the Jenkins Credentials Store as Secret Text, Username/Password, SSH Keys, or AWS credentials.

The pipeline retrieves credentials using the `credentials()` function or the `withCredentials` block, ensuring secrets are injected only during execution and are masked in console logs.

For cloud-native deployments, I also integrate with AWS Secrets Manager or HashiCorp Vault where appropriate.

---

## 9. How do you trigger a pipeline automatically on a Git push?

### Answer

I configure a **Git Webhook** in GitHub or GitLab that sends an HTTP POST request to Jenkins whenever code is pushed.

Jenkins listens for the webhook, detects the repository event, and automatically triggers the appropriate pipeline.

This eliminates manual builds and enables fully automated Continuous Integration.

---

# AWS Infrastructure

## 10. How would you design a VPC for a 3-tier application?

### Answer

I would create a VPC spanning at least two or three Availability Zones for high availability.

Each Availability Zone contains:

- Public Subnet
- Private Application Subnet
- Private Database Subnet

The Application Load Balancer resides in the public subnets, application servers or EKS worker nodes run in private subnets, and the database resides in isolated private database subnets.

Internet access for private instances is provided through NAT Gateways, while Security Groups and Network ACLs restrict traffic between layers.

This architecture improves security, scalability, and fault tolerance.

---

## 11. ALB vs NLB — when do you use each?

### Answer

I use an **Application Load Balancer (ALB)** for HTTP and HTTPS applications because it supports Layer 7 features such as path-based routing, host-based routing, SSL termination, authentication, and AWS WAF integration.

I use a **Network Load Balancer (NLB)** for high-performance TCP or UDP workloads that require ultra-low latency, static IP addresses, or millions of concurrent connections.

Most web applications use ALB, while gaming servers, Kafka, VoIP, and financial applications often benefit from NLB.

---

## 12. IAM Roles vs Policies — how do you enforce least privilege?

### Answer

An **IAM Policy** defines what actions are allowed or denied on AWS resources.

An **IAM Role** is an identity that applications, EC2 instances, Lambda functions, or EKS Pods can assume to obtain temporary credentials.

To enforce least privilege, I create narrowly scoped policies granting only the permissions required for a specific workload and attach them to IAM Roles instead of users. This eliminates long-term credentials and minimizes security risks.

---

# Helm

## 13. Difference between a Helm Chart and a Helm Release?

### Answer

A **Helm Chart** is a reusable package containing Kubernetes manifests, templates, default values, and metadata required to deploy an application.

A **Helm Release** is a running instance of that chart installed in a Kubernetes cluster.

One chart can be installed multiple times as different releases with different configurations.

---

## 14. How do you manage environment-specific values (Dev/Stage/Prod)?

### Answer

I maintain separate values files for each environment.

Example:

```
values-dev.yaml

values-stage.yaml

values-prod.yaml
```

During deployment, I specify the appropriate values file:

```bash
helm upgrade --install app ./chart \
-f values-prod.yaml
```

This allows the same chart to be reused across environments while customizing replicas, resource limits, image tags, and environment-specific configurations.

---

## 15. How do you roll back a failed Helm deployment?

### Answer

First, I review the release history:

```bash
helm history <release-name>
```

Then I roll back to the previous stable revision:

```bash
helm rollback <release-name> <revision-number>
```

After rollback, I verify Pod health, application functionality, and Kubernetes events before investigating the root cause.

---

# Docker

## 16. COPY vs ADD in a Dockerfile — what's the difference?

### Answer

`COPY` simply copies files and directories from the local system into the Docker image.

`ADD` provides additional functionality such as automatically extracting local compressed archives and downloading files from URLs.

Since these extra features can introduce unexpected behavior, I use **COPY** in most cases and reserve **ADD** only when archive extraction is genuinely required.

---

## 17. How do you reduce Docker image size?

### Answer

I use **multi-stage builds** to separate the build environment from the runtime image.

Other optimizations include:

- Using lightweight base images such as Alpine or Distroless.
- Removing unnecessary build dependencies.
- Combining RUN commands to reduce layers.
- Using `.dockerignore` to exclude unnecessary files.
- Cleaning package caches after installation.

These practices improve deployment speed, reduce storage costs, and lower the attack surface.

---

## 18. CMD vs ENTRYPOINT — when do you use each?

### Answer

`ENTRYPOINT` defines the main executable that always runs when the container starts.

`CMD` provides default arguments to the ENTRYPOINT or specifies the default command if no ENTRYPOINT exists.

In production, I use ENTRYPOINT for the application executable and CMD for configurable default parameters, allowing users to override arguments without replacing the executable.

---

# Terraform

## 19. Do you know Terraform, and have you used it?

### Answer

Yes. I have used Terraform extensively to provision and manage AWS infrastructure including VPCs, subnets, EC2 instances, IAM roles, EKS clusters, security groups, Route 53 records, Application Load Balancers, and Amazon ECR repositories.

I organize Terraform code using reusable modules, store the remote state in Amazon S3 with DynamoDB state locking, and execute Terraform through Jenkins CI/CD pipelines. I also manage multiple environments using environment-specific variable files and follow Infrastructure as Code best practices to ensure consistent, repeatable, and version-controlled deployments.
