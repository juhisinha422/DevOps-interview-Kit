# 🚀 DevOps / Cloud Engineer (Specialist) Interview Guide

---

## 1. What are the different ways to host Docker container in AWS using AWS cloud?

There are multiple ways to run Docker containers in AWS depending on the use case. The most common approach is using Amazon ECS (Elastic Container Service), which can run containers using either EC2 launch type or Fargate (serverless). Another widely used option is Amazon EKS (Elastic Kubernetes Service), which provides Kubernetes-based container orchestration for complex microservices architectures. Containers can also be hosted directly on EC2 instances by installing Docker and managing containers manually. Additionally, AWS Lambda supports container images for serverless execution, and AWS App Runner can be used for simplified container deployment without managing infrastructure. The choice depends on factors like scalability, operational overhead, and architecture complexity.

---

## 2.Have you ever deployed a Docker container on AWS Lambda?If yes briefly explain?

Yes, AWS Lambda supports container images, allowing deployment of Dockerized applications. The process involves building a Docker image that follows Lambda runtime requirements, pushing it to Amazon ECR (Elastic Container Registry), and then creating a Lambda function that references this image. The container must include the Lambda runtime interface and handler. This approach is useful when application dependencies are complex or exceed traditional deployment package limits.

---

## 3.What will be the maximum size of container we can use for Lambda?

The maximum container image size supported by AWS Lambda is 10 GB. This includes all layers of the container image stored in Amazon ECR.

---

## 4.What is the maximum memory we can use for Lambda?

AWS Lambda allows configuring memory from 128 MB up to 10,240 MB (10 GB). Memory allocation directly impacts CPU performance, so higher memory provides more compute power.

---

## 5.What is a layer in Lambda?

A Lambda layer is a way to package and share common dependencies, libraries, or runtime components across multiple Lambda functions. Instead of including dependencies in every function, layers allow reuse, reducing package size and improving maintainability.

---

## 6.What kind of security scans you will do on what applications?

In a DevSecOps approach, I perform multiple types of security scans. Static Application Security Testing (SAST) is done on source code using tools like SonarQube. Software Composition Analysis (SCA) checks for vulnerable dependencies. Container image scanning is done using tools like Trivy before deployment. Infrastructure as Code (IaC) scanning ensures Terraform or CloudFormation templates are secure. Additionally, dynamic testing (DAST) may be performed on running applications. These scans are integrated into CI/CD pipelines for early detection.

---

## 7.What is a vulnerability?

A vulnerability is a weakness or flaw in a system, application, or configuration that can be exploited by attackers to gain unauthorized access, disrupt services, or compromise data. Examples include outdated libraries, misconfigured permissions, or insecure network exposure.

---

## 8.Have you received any vulnerability scans when you are running security scans like what are the ones you have experienced in your day-to-day activities?

Yes, in day-to-day work, I have encountered vulnerabilities such as outdated dependencies with known CVEs, hardcoded credentials, improper IAM permissions, and open ports in security groups. In container scans using Trivy, I often see high or critical vulnerabilities in base images. In SonarQube, issues like code smells, security hotspots, and bugs are identified. These are resolved by updating dependencies, fixing configurations, and following secure coding practices.

---

## 9.What is cognitive complex?

Cognitive complexity is a metric used to measure how difficult code is to understand and maintain. It increases with nested logic, loops, and conditionals. Tools like SonarQube use this metric to highlight complex code that should be simplified to improve readability and maintainability.

---

## 10.What  kind of pipelines you are using while setting up Jenkins like can you give an example of any pipeline you have set up?

I use Declarative pipelines in Jenkins for most projects because they are structured and easy to maintain. A typical pipeline includes stages like code checkout, build, unit testing, code quality scan using SonarQube, container image build using Docker, image scan using Trivy, push to ECR, and deployment to Kubernetes. I also include approval stages before production deployment and rollback mechanisms in case of failure.

---

## 11.What is  TF state file in terraform?

The Terraform state file is a JSON file that stores the current state of infrastructure managed by Terraform. It maps resources defined in configuration files to real-world resources in the cloud. This file is critical for tracking changes and ensuring Terraform performs correct updates. In production, it is stored in a remote backend like S3 with state locking enabled.

---

## 12.What is the  infrastructure size you are hosting  like how many supports or containers you are managing infra size?

In my current environment, I manage a medium-scale infrastructure consisting of multiple microservices running in Kubernetes clusters. This includes dozens of services and containers, along with supporting components like load balancers, databases, and monitoring tools. The exact size varies, but it typically involves managing clusters with multiple nodes and handling production workloads with high availability.

---

## 13. Do you manage the infrastructure based upon client or centralized infrastructure?

It depends on the organization, but I have worked in both models. In some projects, infrastructure is managed per client with isolated environments for security and compliance. In others, a centralized infrastructure is used with proper segmentation using namespaces, accounts, or VPCs. Centralized setups improve cost efficiency and management, while client-based setups provide stronger isolation.

---

## 14.Which deployment strategy you will use for pipelines?

I choose deployment strategies based on application criticality. Common strategies include rolling updates for minimal downtime, blue-green deployments for zero downtime and easy rollback, and canary deployments for gradual traffic shifting. For high-risk changes, canary or blue-green is preferred to reduce impact.

---

## 15.What is a blue green deployment?

Blue-green deployment is a strategy where two identical environments are maintained: one active (blue) and one idle (green). The new version is deployed to the idle environment, tested, and then traffic is switched to it. This ensures zero downtime and allows quick rollback by switching traffic back to the previous version if needed.

---

## 🚀 Final Tip

For Specialist-level interviews:

* Combine **DevOps + Cloud + Security (DevSecOps)**
* Always mention **real tools + real scenarios**
* Show **decision-making (why you choose something)**

---
