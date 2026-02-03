# AWS Interview Questions & Answers (4 Years Experience)

## Compute & Access Control

1. **How would you securely provide access to a specific EC2 instance (e.g., the 2nd instance) from a user?**
   To securely provide access, I would use **Security Groups** to restrict access based on IP addresses and ports. Additionally, IAM policies can be used to control user access. For SSH access, I would use a secure key pair and store it in **AWS Secrets Manager**. If more granular control is needed, I’d assign an IAM role to the EC2 instance with the required permissions.

2. **Can you explain IAM roles and IAM policies? What is the difference between them?**
   - **IAM Roles**: AWS identities with specific permissions. They can be assumed by AWS services or users to perform tasks on AWS resources.
   - **IAM Policies**: These define permissions (what actions are allowed or denied on specific resources). Policies can be attached to roles, users, or groups to grant permissions.

   **Difference**: A **role** is a set of permissions that are assumed by a user or service, whereas a **policy** defines the actions allowed or denied, and these policies can be attached to roles, users, or groups.

---

## AWS Networking

3. **What is a VPC and why do we need it?**
   A **Virtual Private Cloud (VPC)** is a logically isolated network in AWS, providing control over networking aspects such as IP address ranges, subnets, route tables, and network gateways. It allows you to isolate resources and manage inbound and outbound traffic securely.

4. **What is a Route Table in AWS?**
   A **Route Table** in AWS defines rules for routing traffic within a VPC. It determines where network traffic is directed (e.g., to the internet, to other subnets, or through a VPN).

5. **What is the difference between a Route Table and Auto Scaling?**
   A **Route Table** controls traffic routing at the network level, while **Auto Scaling** automatically adjusts the number of EC2 instances based on demand. Route tables manage traffic flow, while Auto Scaling ensures application scaling based on performance metrics.

6. **What is a Load Balancer? Why do we use it in real-time applications?**
   A **Load Balancer** distributes incoming application traffic across multiple targets (EC2 instances) to prevent overloading any single instance. In real-time applications, it ensures high availability and fault tolerance by rerouting traffic if one instance fails.

---

## Security

7. **What are Security Groups?**
   **Security Groups** act as virtual firewalls for EC2 instances and other resources. They define rules for inbound and outbound traffic based on IP addresses, ports, and protocols. Security Groups are stateful, meaning if inbound traffic is allowed, the outbound traffic is automatically allowed.

8. **Where do you use Security Groups and Network ACLs (NACLs)? What is the difference between them?**
   - **Security Groups** are used for controlling traffic at the instance level, while **Network ACLs (NACLs)** work at the subnet level.
   - **Security Groups** are stateful, whereas **NACLs** are stateless.
   - **Security Groups** are ideal for resource-level access control, while **NACLs** provide subnet-level security.

---

## Infrastructure & Cost Management

9. **How did you manage your infrastructure in your project?**
   I used **Infrastructure as Code (IaC)** tools like **AWS CloudFormation** and **Terraform** to automate infrastructure management, ensuring consistent and repeatable environments across development, staging, and production.

10. **What cost optimization techniques did you implement in AWS?**
   - **Right-sizing EC2 instances** based on actual usage.
   - **Auto Scaling** to adjust the number of instances based on traffic.
   - **Spot Instances** for non-critical workloads.
   - **Reserved Instances** for predictable workloads to get discounts.
   - **S3 Lifecycle Policies** for data archival.
   - Regularly using **AWS Trusted Advisor** for cost optimization recommendations.

11. **Why did you use Amazon S3 Glacier? In which scenarios is it useful?**
   **Amazon S3 Glacier** is used for long-term archival storage of infrequently accessed data, such as backups, logs, or old files that need to be retained for compliance reasons. It’s a cost-effective storage solution for data that doesn’t require immediate retrieval.

---

## Storage & Databases

12. **Can you explain Amazon S3 and Amazon RDS? What are their use cases?**
   - **Amazon S3**: An object storage service for storing unstructured data (e.g., backups, media files). Ideal for static website hosting, backups, and data storage.
   - **Amazon RDS**: A managed relational database service supporting various engines (e.g., MySQL, PostgreSQL). Ideal for applications needing structured data with complex queries (e.g., e-commerce, CRM systems).

---

## CI/CD & DevOps

13. **What was your main responsibility in the CI/CD pipeline?**
   I configured **AWS CodePipeline**, integrated it with **AWS CodeBuild** and **AWS CodeDeploy** to automate build, test, and deployment processes. I also managed version control systems like GitHub and ensured smooth deployments across different environments.

14. **What do you understand by a CI/CD pipeline? Can you explain the flow?**
   A **CI/CD pipeline** automates the process of integrating code and deploying it to production. It typically includes:
   - **Continuous Integration (CI)**: Code is automatically built and tested when changes are committed.
   - **Continuous Delivery (CD)**: After passing tests, the code is automatically deployed to production.

   The flow is: 
   - Code is committed to a repository.
   - The code is built and tested automatically.
   - If successful, the code is deployed to the staging or production environment.

---

## Containers

15. **What is Amazon ECS (Elastic Container Service)?**
   **Amazon ECS** is a container orchestration service that allows running and managing Docker containers at scale on AWS. It supports EC2 instances and **AWS Fargate** for serverless compute.

16. **What is AWS Fargate?**
   **AWS Fargate** is a serverless compute engine for containers that removes the need to manage the underlying EC2 instances. You only define the containers, and Fargate automatically provisions and manages the required compute resources.

17. **What is the difference between ECS and Fargate?**
   - **Amazon ECS** is a service that manages containers, but it requires you to manage the underlying EC2 instances.
   - **AWS Fargate** is a serverless compute engine that abstracts away the need to manage EC2 instances, allowing you to focus solely on running containers.

---

This `README.md` contains all answers to the AWS interview questions based on a 4-year experience level.
