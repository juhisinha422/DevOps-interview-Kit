# DevOps / Kubernetes Interview Questions & Answers

---

## 1. Could you briefly outline your professional experience and career aspirations?

I have experience working as a DevOps and Cloud Engineer with hands-on knowledge of AWS, Linux, Docker, Kubernetes, Terraform, Jenkins, and CI/CD pipelines. I have worked on infrastructure automation, application deployments, monitoring, and containerized environments. I am also experienced in managing cloud resources and automating deployments using Infrastructure as Code tools like Terraform. My career goal is to become a strong DevOps/Cloud Architect by gaining deeper expertise in Kubernetes, cloud security, automation, and scalable production environments.

---

## 2. What is the difference between Continuous Integration, Continuous Delivery, and Continuous Deployment?

Continuous Integration is the practice where developers frequently merge code into a shared repository and automated builds and tests are executed to identify issues early. Continuous Delivery extends CI by ensuring the application is always deployment-ready, but deployment to production usually requires manual approval. Continuous Deployment goes one step further by automatically deploying every successful change directly to production without manual intervention. The main difference is the level of automation in the release process.

---

## 3. What is the difference between a Docker image, a Docker container, and a virtual machine?

A Docker image is a read-only template that contains the application code, dependencies, and configurations required to run an application. A Docker container is the running instance of that image. Containers are lightweight and share the host operating system kernel, which makes them faster and more efficient. A virtual machine, on the other hand, virtualizes complete hardware and runs a full operating system using a hypervisor. Compared to containers, virtual machines are heavier, consume more resources, and take longer to start.

---

## 4. What is k8s pod and how is it different from deployment?

A Pod is the smallest deployable unit in Kubernetes and it contains one or more containers that share the same network and storage. Pods are used to run applications inside Kubernetes. However, Pods alone are not ideal for production because if a Pod crashes, it will not automatically recover. A Deployment is a Kubernetes object that manages Pods automatically. It provides features like scaling, self-healing, rolling updates, and rollbacks. In production environments, Deployments are commonly used because they ensure the desired number of Pods are always running.

---

## 5. What is the difference between vertical scaling and horizontal scaling? When would you use each?

Vertical scaling means increasing the resources of an existing server, such as adding more CPU, RAM, or storage. For example, upgrading an EC2 instance from t2.medium to t2.large. It is simple to implement but has hardware limitations and may cause downtime. Horizontal scaling means adding more servers or instances to distribute traffic and workload. For example, adding multiple EC2 instances behind a Load Balancer or increasing Kubernetes pod replicas. Horizontal scaling is more suitable for high-traffic and cloud-native applications because it provides better scalability and fault tolerance.

---

## 6. How do you check if a port is open and which process is using it?

To check if a port is open and identify which process is using it, I usually use commands like netstat, ss, or lsof in Linux. For example, using `netstat -tulnp | grep 8080` or `lsof -i :8080` helps identify the process ID and application using that specific port. These commands are useful for troubleshooting application or connectivity issues in servers.

---

## 7. If a Linux server is slow, what are the first things you would check?

If a Linux server is slow, the first thing I check is CPU, memory, and disk utilization using commands like top, htop, free -m, and df -h. Then I verify if any process is consuming excessive resources using ps commands. I also check disk I/O, network usage, and system logs to identify any hardware, application, or service-related issues. Reviewing logs using journalctl or files inside /var/log helps identify errors causing performance problems.

---

## 8. Pick one production system you have worked on and explain:

I worked on a cloud-based microservices application deployed on AWS using Docker and Kubernetes. The architecture included EC2 instances, Kubernetes clusters, Jenkins CI/CD pipelines, RDS databases, and Application Load Balancers. My role involved managing CI/CD pipelines, containerizing applications, automating infrastructure using Terraform, and monitoring deployments. One major issue I solved was application downtime caused by high CPU usage in Kubernetes pods. I investigated the issue using pod metrics and logs, temporarily increased resource limits, enabled Horizontal Pod Autoscaler, and coordinated with developers to fix the memory leak issue. This improved application stability and reduced downtime significantly.

---

## 9. You have a CI/CD pipeline where deployments fail intermittently. How would you debug and stabilise it?

If deployments fail intermittently in a CI/CD pipeline, I first check pipeline logs to identify the exact stage where the failure occurs. Then I verify network connectivity, credentials, environment variables, build artifacts, and server resource usage. I also check if there are any timeout or dependency issues. To stabilize the pipeline, I implement proper error handling, retries, monitoring, automated testing, and ensure consistent deployment environments. Using Infrastructure as Code and version-controlled configurations also helps improve pipeline reliability.

---

## 10. What is CrashLoopBackOff?

CrashLoopBackOff is a Kubernetes error state where a container repeatedly crashes and Kubernetes continuously tries to restart it. This usually happens due to application failures, incorrect environment variables, missing dependencies, insufficient memory, or configuration issues. To troubleshoot it, I check pod logs using kubectl logs, inspect pod events using kubectl describe pod, and identify the root cause from the error messages. Once identified, I fix the application or configuration issue and redeploy the pod.

---

## 11. AWS bill spikes suddenly and increase issue what you will check first?

If AWS billing suddenly increases, the first thing I check is the AWS Billing Dashboard and Cost Explorer to identify which service is causing the spike. Then I verify EC2 usage, data transfer, S3 storage, Load Balancer charges, NAT Gateway usage, and recently created resources. I also check for unused resources such as unattached EBS volumes or idle EC2 instances. Services like AWS Budgets and Trusted Advisor help identify cost optimization opportunities and prevent unexpected billing increases.

---

## 12. Using Terraform, if someone manually changes infrastructure, what problems can happen?

If someone manually changes infrastructure managed by Terraform, it creates a situation called Terraform Drift, where the actual infrastructure no longer matches the Terraform state file. This can lead to inconsistent environments, unexpected resource modifications, deployment failures, downtime, and security risks. For example, if someone manually changes an EC2 instance type outside Terraform, the next terraform apply may overwrite or recreate resources unexpectedly. To avoid this, organizations should restrict manual changes, enforce Infrastructure as Code practices, and regularly run terraform plan to detect drift.

---
