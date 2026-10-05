# 🚀 DevOps Interview Guide – Accenture (3–5 Years Experience)

---

## Walk through your professional background and current role

I am a DevOps Engineer with around 3–5 years of experience working primarily on AWS cloud and modern DevOps tools. In my current role, I am responsible for designing and maintaining CI/CD pipelines using Jenkins, managing infrastructure using Terraform, and deploying applications using Docker and Kubernetes. I also handle monitoring, troubleshooting production issues, and ensuring high availability and scalability. My work involves close collaboration with development teams to automate workflows and improve release efficiency while maintaining security and cost optimization.

---

## Explain the difference between Git merge and Git rebase

Git merge combines changes from one branch into another by creating a new merge commit, preserving the complete history of both branches. Git rebase, on the other hand, rewrites commit history by moving commits from one branch on top of another, creating a linear history. In real projects, merge is preferred when maintaining history is important, while rebase is used to keep a clean and readable commit history.

---

## Describe the branching strategy you follow in your projects

In my projects, I typically follow a Git-based branching strategy similar to GitFlow or a simplified version of it. There is a main branch for production, a develop branch for ongoing development, and feature branches for individual changes. Once development is complete, code is merged into develop and later into main after testing. For hotfixes, a separate branch is created from main and merged back after fixing the issue. This ensures controlled releases and stability.

---

## How frequently do you release applications to production and why?

Release frequency depends on project requirements, but typically we follow a weekly or bi-weekly release cycle. For critical fixes, we perform immediate releases. The goal is to balance speed and stability by ensuring sufficient testing while delivering features quickly. CI/CD pipelines enable frequent and reliable deployments.

---

## Explain Linux server patching and how it is handled in AWS environments

Linux server patching involves updating system packages and applying security fixes to keep systems secure and stable. In AWS, this is commonly handled using AWS Systems Manager Patch Manager, which automates patching across instances. Maintenance windows are defined to schedule updates without impacting production. Before applying patches, testing is done in lower environments, and backups are taken to avoid risks.

---

## Describe your approach to troubleshooting issues in CI/CD pipelines

When troubleshooting CI/CD issues, I start by identifying the failed stage using pipeline logs. I analyze error messages, check recent code changes, and verify environment variables and dependencies. If the issue is related to infrastructure, I validate connectivity and permissions. I may rerun the job with debug logs or replicate the issue locally. This systematic approach helps quickly identify and resolve problems.

---

## Explain the end-to-end steps involved in your production deployment pipeline

In a typical pipeline, code is pushed to a Git repository, which triggers Jenkins via webhook. Jenkins checks out the code, builds the application, runs unit tests, and performs code quality analysis using tools like SonarQube. The artifact is then stored in a repository and containerized using Docker. The image is pushed to a registry and deployed to Kubernetes or cloud environments. After deployment, monitoring tools ensure the application is running correctly.

---

## How do you investigate and resolve failures in a CI/CD job?

I begin by reviewing console logs to identify the root cause. Then I check for issues like build failures, test failures, dependency problems, or configuration errors. I validate credentials, network connectivity, and resource availability. Once the issue is identified, I fix it and rerun the job. If needed, I involve relevant teams and document the issue for future reference.

---

## What is a multi-stage Dockerfile and why is it used?

A multi-stage Dockerfile is used to create smaller and more efficient Docker images by separating the build environment from the runtime environment. It allows us to use one stage to build the application and another stage to run it, reducing image size and improving security. This is especially useful in production environments.

---

## Write a sample multi-stage Dockerfile

```
# Build Stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Runtime Stage
FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

## Explain Kubernetes services and their use cases

Kubernetes services provide stable networking and expose applications running in pods. Since pods are ephemeral, services ensure consistent access. Common types include ClusterIP for internal communication, NodePort for exposing services on a node port, and LoadBalancer for external access. Services enable load balancing and service discovery within the cluster.

---

## Write a Kubernetes Pod YAML manifest

```
apiVersion: v1
kind: Pod
metadata:
  name: sample-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
    ports:
    - containerPort: 80
```

---

## How do you recover or handle a corrupted Terraform file or state?

If the Terraform state file is corrupted, I first check for backups, especially if stored in S3 with versioning enabled. I can restore a previous version of the state file and validate it using Terraform commands. If needed, I use `terraform import` to rebuild state for specific resources. Proper use of remote backends and locking mechanisms helps prevent such issues.

---

## How do you resolve merge conflicts in Git?

Merge conflicts occur when changes overlap. I resolve them by manually reviewing conflicting files, deciding which changes to keep, and editing the file accordingly. After resolving conflicts, I stage the changes and commit them. Good communication and smaller commits help reduce conflicts.

---

## Which AWS services have you worked with in real projects?

I have worked with services like EC2, S3, VPC, IAM, RDS, EKS, CloudWatch, Auto Scaling Groups, and Elastic Load Balancers. I use these services to design scalable, secure, and highly available systems.

---

## Explain how you set up and manage an Auto Scaling Group

An Auto Scaling Group (ASG) is configured with a launch template that defines instance configuration. Scaling policies are defined based on metrics like CPU utilization. The ASG automatically adds or removes instances based on demand, ensuring optimal performance and cost efficiency. Health checks and load balancers are integrated to maintain availability.

---

## What is elasticity in cloud computing, and how have you implemented it?

Elasticity refers to the ability to dynamically scale resources up or down based on demand. In my projects, I implement elasticity using Auto Scaling Groups in AWS and Horizontal Pod Autoscaling in Kubernetes. This ensures efficient resource utilization, cost optimization, and consistent performance during traffic spikes.

---

## 🚀 Final Tip

For 3–5 years experience:

* Focus on **real implementation + tools used**
* Show **end-to-end understanding**
* Always include **debugging + production scenarios**

---
