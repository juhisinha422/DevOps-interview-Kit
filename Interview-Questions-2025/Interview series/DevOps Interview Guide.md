# 🚀 DevOps Interview Guide (CI/CD + Git + Docker + Terraform + AWS + Kubernetes + Linux)

---

# CICD

---

## 1.cicd work flow

A typical CI/CD workflow automates the software delivery lifecycle:

**CI (Continuous Integration):**

* Developer commits code to Git
* Trigger pipeline (Jenkins/GitHub Actions)
* Build stage → compile/package code
* Run unit tests
* Code quality scan (SonarQube)
* Store artifact (Nexus/Artifactory)

**CD (Continuous Deployment/Delivery):**

* Deploy to Dev environment
* Run integration tests
* Promote to UAT (with approval)
* Deploy to Production
* Monitor deployment

Goal: Faster, reliable, and automated releases.

---

## 2.if cicd fails what will be the step to check

Step-by-step debugging:

1. Check pipeline logs (Jenkins console output)
2. Identify stage where failure occurred (build/test/deploy)
3. Validate code changes (recent commits)
4. Check dependency issues
5. Verify environment variables/secrets
6. Check infrastructure (servers, Kubernetes, etc.)
7. Re-run failed stage after fix

---

## 3.what you will do in test environment

* Deploy application build
* Perform integration and regression testing
* Validate configurations
* Test APIs and DB connectivity
* Perform performance testing if needed
* Ensure application is stable before moving to UAT/Prod

---

# GIT

---

## 4.how will you get back deleted branch

If branch is deleted locally:

* Use reflog:
  git reflog
  git checkout -b branch-name <commit-id>

If deleted remotely:

* Restore from another developer repo or backup
* If commit exists → recreate branch using commit ID

---

## 5.what is the diff b/w git pull and git fetch

* **git fetch** → Downloads latest changes but does NOT merge
* **git pull** → Fetch + merge into current branch

---

## 6.git clone and git fork

* **git clone** → Copy repository to local system
* **git fork** → Create personal copy in GitHub account

---

## 7.git rebase

Rebase moves or reapplies commits on top of another branch.

Use case:

* Clean commit history
* Avoid merge commits

Example:
git rebase main

---

## 8.In which command we can see latest changes git fetch or git pull

* **git fetch** → safest way to see latest changes without merging
* So answer: git fetch

---

# Docker

---

## 9.different b/w container and VM

**Container:**

* Lightweight
* Shares host OS kernel
* Faster startup

**VM:**

* Heavyweight
* Runs full OS
* Slower

---

## 10.how will you enter jenkins container

* First find container:
  docker ps
* Enter container:
  docker exec -it <container-id> /bin/bash

---

## 11.how will you check container in docker

* List running containers:
  docker ps
* List all containers:
  docker ps -a

---

# Terraform

---

## 12.if terraform state file corrupt what will happens adn how will you debug

**Impact:**

* Terraform loses track of infrastructure
* May recreate or destroy resources incorrectly

**Debug steps:**

1. Check state file backup (.tfstate.backup)
2. Restore from backup
3. Use terraform refresh
4. Import resources manually:
   terraform import
5. Store state in remote backend (S3 + DynamoDB) to avoid corruption

---

## 13.Terraform work flow

1. Write configuration (.tf files)
2. Initialize:
   terraform init
3. Plan:
   terraform plan
4. Apply:
   terraform apply
5. Destroy (if needed):
   terraform destroy

---

# AWS

---

## 14.what is the use of Vpc

VPC (Virtual Private Cloud) is a logically isolated network in AWS.

Uses:

* Define IP ranges (CIDR)
* Create subnets (public/private)
* Control traffic using route tables
* Apply security (Security Groups, NACLs)
* Securely host applications

---

# Kubernetes

---

## 15.how will you scedule pod

* Scheduler automatically assigns pod to node based on:

  * Resource availability
  * Node selectors
  * Taints and tolerations
* Manual scheduling:

  * Use nodeSelector or affinity in YAML

---

## 16.in pod version , metada, Type commands explain these

* **apiVersion** → Kubernetes API version used (e.g., v1)
* **metadata** → Info like name, labels, namespace
* **kind (Type)** → Resource type (Pod, Deployment, Service)

---

## 17.differnce between pod and container

* Pod → wrapper for one or more containers
* Container → actual running application

---

## 18.how will you create services

* Create YAML file:
  kind: Service
* Apply:
  kubectl apply -f service.yaml

---

## 19.how will you check services

* kubectl get svc
* kubectl describe svc <name>

---

## 20.how will you use Node Port IP service

* Access service using:
  NodeIP:NodePort
  Example:
  http://<NodeIP>:30007

---

## 21.if production deployment fails how will you debug

Steps:

1. Check pod status:
   kubectl get pods
2. Check logs:
   kubectl logs <pod>
3. Describe pod:
   kubectl describe pod
4. Check events and errors
5. Verify image, configs, secrets
6. Rollback if needed:
   kubectl rollout undo

---

# Linux

---

## 22.How will you check mechine uptime in linux

* uptime
* top (shows uptime also)

---

## 23.how can you check memory

* free -m
* top / htop
* vmstat

---

## 24. awk command

awk is a powerful text processing tool.

Example:

* Print column:
  awk '{print $1}' file.txt

Use cases:

* Log analysis
* Filtering data
* Pattern matching

---

## 🚀 Final Tip

For 4 years experience:

* Always explain **commands + real scenarios**
* Show **debugging approach**
* Focus on **practical usage instead of theory**

---
