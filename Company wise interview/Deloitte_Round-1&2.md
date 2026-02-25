# AWS, DevOps & Cloud Infrastructure Interview Preparation (4+ Years)

---

### **1. Self Introduction**
I have over 4 years of experience working in cloud technologies, primarily focusing on AWS, Docker, Kubernetes, Terraform, and CI/CD pipelines. Throughout my career, I've managed end-to-end cloud infrastructure, implemented highly available and scalable systems, optimized cloud costs, and automated workflows to ensure efficiency. I have worked on projects ranging from small-scale applications to enterprise-level systems, and I am always keen to leverage cloud-native solutions to address business challenges.

---

### **2. If you are not able to access the EC2 instance, what might be the issues?**
Several factors could prevent access to an EC2 instance:
1. **Security Group Configuration**: Ensure the security group attached to the EC2 instance allows inbound traffic on necessary ports (e.g., port 22 for SSH, port 3389 for RDP).
2. **Network ACLs**: Ensure NACLs aren’t blocking inbound or outbound traffic.
3. **Elastic IP**: Verify if the Elastic IP is properly associated with the instance, especially if you’re trying to connect via SSH or RDP.
4. **SSH Key Pair**: Ensure the correct SSH key pair is being used and is available on the machine you're connecting from.
5. **Instance Health**: Ensure the EC2 instance is in the "running" state and passing health checks.
6. **VPC and Subnet Configuration**: If the EC2 instance is in a private subnet, ensure the correct routing and NAT gateway are set up for outbound internet access (if needed).

---

### **3. What is your project architecture?**
In my projects, I follow a microservices-based architecture deployed in the AWS cloud. Typical components include:
- **VPC** with private and public subnets across multiple Availability Zones for high availability.
- **Elastic Load Balancer (ALB/NLB)** to distribute traffic across EC2 instances in multiple AZs.
- **EC2 instances** for hosting application logic, managed by **Auto Scaling Groups** to scale dynamically.
- **RDS** for database management (with Multi-AZ for high availability).
- **S3** for object storage and backups.
- **Lambda** for event-driven functions.
- **CloudWatch** for monitoring, logging, and alerts.
- **IAM** for managing secure access to AWS resources.
We use **Terraform** for infrastructure provisioning and **Jenkins/GitLab CI** for CI/CD automation to deploy the applications seamlessly.

---

### **4. How many AWS accounts have you managed in your project?**
I have worked with up to 3 AWS accounts for different environments: Development, Staging, and Production. These accounts are managed using **AWS Organizations** to consolidate billing and policies while enforcing isolation between environments. I’ve set up **cross-account IAM roles** to allow secure access between these environments when necessary, and all environments follow strict IAM policies to enforce security best practices.

---

### **5. How many types of S3 buckets are there?**
Amazon S3 storage is organized into various **storage classes**, which are designed to address different use cases:
1. **S3 Standard**: High-performance, low-latency storage for frequently accessed data.
2. **S3 Standard-IA (Infrequent Access)**: For data that is less frequently accessed but still needs to be readily available.
3. **S3 One Zone-IA**: For infrequently accessed data that does not require resilience across multiple Availability Zones.
4. **S3 Glacier and Glacier Deep Archive**: Low-cost archival storage with retrieval times from minutes to hours.
5. **S3 Intelligent-Tiering**: Moves objects between two access tiers (frequent and infrequent) based on access patterns to optimize costs.
6. **S3 Outposts**: For on-premises data storage solutions when using AWS Outposts.

---

### **6. What are Security Groups and NACLs, and what are the differences between them?**
- **Security Groups**: These are stateful firewalls that control inbound and outbound traffic to AWS resources like EC2 instances. They are applied at the instance level and automatically apply changes in real-time.
- **NACLs (Network Access Control Lists)**: Stateless firewalls applied at the subnet level. NACLs require explicit rules for both inbound and outbound traffic and are processed in order. 
The key differences:
- **Security Groups** are stateful, while **NACLs** are stateless.
- **Security Groups** apply to specific instances, whereas **NACLs** apply at the subnet level.

---

### **7. How do you reduce AWS costs in your project?**
To optimize AWS costs, I follow these strategies:
1. **Right-Sizing EC2 Instances**: Regularly monitor EC2 instances using CloudWatch metrics to identify underutilized instances and resize or terminate them.
2. **Spot Instances**: Use **Spot Instances** for non-critical workloads to save costs.
3. **Auto Scaling**: Implement **Auto Scaling Groups** to automatically scale the number of EC2 instances up or down based on demand.
4. **Reserved Instances and Savings Plans**: Purchase **Reserved Instances** or **Savings Plans** for long-term predictable workloads.
5. **Use S3 Lifecycle Policies**: Automatically transition infrequently accessed data to cheaper storage tiers like Glacier.
6. **Consolidated Billing**: Consolidate multiple AWS accounts into one, leveraging volume discounts.
7. **Monitor Using Trusted Advisor**: Regularly review recommendations from **AWS Trusted Advisor** for cost optimization opportunities.

---

### **8. What is VPC Peering and Transit Gateway, and how do you set them up?**
- **VPC Peering**: This allows direct communication between two VPCs. It's a one-to-one connection where traffic can flow between VPCs using private IPs. To set it up, create a peering connection and update route tables in both VPCs.
- **Transit Gateway**: This is a scalable hub that allows multiple VPCs and on-premises networks to connect through a central gateway. It simplifies the network architecture when dealing with many VPCs. To set it up, create the Transit Gateway, attach VPCs to it, and update route tables for all connected VPCs.

---

### **9. Have you used load balancers? What kinds of load balancers have you used, and why are they important?**
Yes, I have worked with the following types of **AWS Load Balancers**:
1. **Application Load Balancer (ALB)**: Ideal for HTTP/HTTPS traffic, ALBs operate at the application layer and provide advanced routing based on host or path.
2. **Network Load Balancer (NLB)**: Operates at the transport layer (Layer 4) for low-latency, high-throughput traffic, especially for TCP or UDP protocols.
3. **Classic Load Balancer (CLB)**: This is the legacy option and operates both at Layer 4 and Layer 7 but lacks some of the advanced features of ALB and NLB.
Load balancers are critical for distributing traffic evenly across servers, ensuring fault tolerance, reducing latency, and preventing any single instance from being overwhelmed.

---

### **10. How do you set up a highly available application?**
To set up a highly available application:
1. **Distribute across multiple Availability Zones (AZs)** to ensure redundancy.
2. Use **Elastic Load Balancers (ALBs/NLBs)** to distribute traffic across instances in different AZs.
3. Implement **Auto Scaling Groups** for dynamic scaling based on traffic load.
4. Set up **RDS Multi-AZ** for database failover and replication.
5. Use **Route 53** for DNS failover between regions or availability zones.
6. Ensure **data redundancy** with S3, EFS, or DynamoDB in cross-region replication.

---

### **11. How do you reduce Docker images?**
To reduce Docker image size:
1. Use **multi-stage builds** to separate build and runtime environments.
2. Opt for **minimal base images**, like **Alpine Linux**, to keep the image lightweight.
3. Remove unnecessary files during the build process (e.g., package manager caches, build tools).
4. Use a `.dockerignore` file to exclude unnecessary files (like local development files) from being added to the image.

---

### **12. How do you secure the Terraform state file?**
To secure the Terraform state file:
1. Store it remotely in **S3** with **versioning** and **server-side encryption** enabled to ensure data is secure and recoverable.
2. Use **Terraform Cloud** or **Terraform Enterprise** to centrally manage state.
3. Ensure restricted access to the state file by using **IAM roles** and **policies** to define who can access or modify the state file.
4. Enable **state locking** to avoid concurrent modifications.

---

### **13. Cluster security**
Cluster security involves securing all components within the cluster:
1. **IAM Roles and Policies**: Ensure that only authorized users and services have access to the resources in the cluster.
2. **Pod Security Policies**: Use **Kubernetes RBAC** (Role-Based Access Control) to control access to the Kubernetes API and define permissions for resources.
3. **Network Policies**: Use network policies to control the communication between pods, limiting unnecessary exposure.
4. **Encryption**: Enable encryption for Kubernetes secrets and use HTTPS for communication within the cluster.
5. **Security Groups for EC2 instances**: Ensure that the instances running the cluster have strict security group rules that limit access to essential ports.

---

### **14. If one service is consuming too much CPU and memory, how do you check which component is causing high usage?**

To identify which component is causing high CPU and memory usage, I follow these steps:

1. **CloudWatch Metrics**: I start by checking the **CloudWatch** metrics to monitor resource usage. Metrics such as CPU utilization, memory utilization, disk I/O, and network traffic can help identify which instance or service is under high load. 
2. **EC2 Instance Logs**: For EC2 instances, I would review the system and application logs to see if any processes are consuming excessive resources. I often use **top** or **htop** commands to see real-time processes consuming CPU and memory.
3. **Docker Metrics (if applicable)**: If the service is containerized (using Docker), I use **Docker stats** or **kubectl top pod** (for Kubernetes) to check container-level resource consumption. These commands show the CPU and memory usage of each running container.
4. **Scaling and Load**: Check if the instance or pod is part of an **Auto Scaling Group** and ensure it is scaling appropriately under heavy load. If Auto Scaling isn’t set up, you may need to manually adjust resource allocation.
5. **Profiling Tools**: For more in-depth analysis, I would use application profiling tools such as **New Relic** or **Datadog** to monitor resource usage and pinpoint specific components, database queries, or code paths that are consuming excessive resources.
6. **Container Orchestration (Kubernetes)**: In a Kubernetes environment, I would check if a specific pod is consuming too much memory or CPU by examining its **resource requests and limits**. If these aren’t configured properly, Kubernetes will not limit resource usage, leading to excessive consumption. Adjusting pod resource limits and requests can help resolve these issues.

---

## **R-2 Interview: Top Service-Based Company (Deloitte)**

---

### **1. One team has 5 members and another team has 4 members. If both teams shake hands with each other, how many handshakes will happen in total?**

The number of handshakes between two teams is calculated by multiplying the number of members in each team. In this case:
\[
\text{Total Handshakes} = 5 \times 4 = 20
\]
So, 20 handshakes will happen in total.

---

### **2. Consider a VPC with only one subnet. There are no route tables, no security groups, no configurations — it’s completely empty. Do you think it is a private subnet or a public subnet?**

If a VPC has only one subnet and lacks route tables, security groups, and configurations, it is typically a **private subnet**. A public subnet requires a route to the **Internet Gateway** (IGW) in the route table. Without this configuration, the subnet cannot route traffic to the internet, making it a private subnet.

---

### **3. What is EBS and EFS? Explain clearly.**

- **EBS (Elastic Block Store)**: EBS provides block-level storage that can be attached to EC2 instances. It behaves like a hard drive for EC2 instances, suitable for storing data that requires low-latency access, such as databases, operating system files, and applications. EBS volumes are persistent and can be backed up with snapshots.
- **EFS (Elastic File System)**: EFS is a fully managed, scalable file storage service that can be accessed by multiple EC2 instances simultaneously. It supports shared file storage, making it ideal for workloads that require file-based access, like web servers or content management systems. It is commonly used for applications that need to share data across multiple instances or scale storage dynamically.

---

### **4. How do you set up a highly available application?**

To ensure high availability for applications:
1. **Multiple Availability Zones (AZs)**: Distribute application components (e.g., EC2 instances, RDS instances) across multiple AZs to avoid a single point of failure.
2. **Elastic Load Balancer (ELB)**: Use ALB or NLB to distribute incoming traffic across instances in multiple AZs. This improves fault tolerance and ensures requests are routed to healthy instances.
3. **Auto Scaling**: Set up Auto Scaling Groups to automatically adjust the number of EC2 instances based on load. This ensures that the application can scale based on traffic demands.
4. **Multi-AZ RDS**: Use **Multi-AZ deployments** for RDS to ensure that the database is replicated to another AZ. In case of failure, RDS automatically fails over to the standby instance.
5. **Route 53 DNS Failover**: Set up Route 53 health checks and DNS failover to route traffic to healthy resources in case of failure.
6. **Data Replication**: Use cross-region or cross-AZ replication for data stored in S3, DynamoDB, or other storage services to ensure data availability and redundancy.

---

### **5. How many accounts have you managed in your project, and how do you configure them with Jenkins?**

I’ve typically worked with 2-3 AWS accounts in a project (Development, Staging, and Production). Each account is isolated for better security and governance using **AWS Organizations**. I configure Jenkins to deploy to these multiple environments by setting up **cross-account IAM roles** and leveraging the **AWS CodePipeline plugin** for Jenkins, or using **AWS CLI** with properly configured IAM roles to authenticate between Jenkins and the AWS accounts. For each environment, I define different Jenkins pipelines that correspond to the specific account’s requirements, ensuring deployments are managed independently.

---

### **6. What are parameters in Jenkins?**

**Parameters** in Jenkins allow users to define dynamic input for jobs. They enable users to customize builds based on different criteria. Common types of parameters include:
- **String Parameter**: Used for text input.
- **Choice Parameter**: Allows the user to select a value from a pre-defined list.
- **Boolean Parameter**: Used for True/False input.
- **File Parameter**: Allows users to upload files during the build.
- **Password Parameter**: Used for sensitive input, where the value is masked.

Parameters allow flexibility in Jenkins jobs, enabling them to handle different configurations without hardcoding values into the pipeline scripts.

---

### **7. I have 5 pods — 3 are running and 2 are not running. What might be the issue?**

The issue could be related to several factors:
1. **Resource Constraints**: Check if the nodes running the pods have enough CPU or memory. If the pods exceed the available resources, Kubernetes will not schedule them.
2. **Pod Logs**: Use `kubectl logs <pod_name>` to view logs and check for errors preventing the pods from running.
3. **Pod Configuration**: Verify if there are issues in the pod specifications, such as incorrect images, missing environment variables, or broken dependencies.
4. **Deployment/ReplicaSet Issues**: Ensure that the **Deployment** or **ReplicaSet** is correctly configured to ensure the desired number of pods are always running.

---

### **8. When creating EC2, S3, Lambda — how are resources created? Parallel or sequential? How does Terraform create resources?**

Terraform creates resources in parallel by default. However, it respects dependencies between resources, ensuring that resources are created in the proper order. For example, it will create **VPC** first and then the **EC2 instances** that depend on that VPC. You can explicitly define dependencies using `depends_on` to force sequential creation if necessary.

---

### **9. How do you create 10 resources one by one?**

To create resources one by one in Terraform, you can:
1. **Define resources sequentially in the Terraform configuration**: Terraform will automatically create resources in the order they are defined unless there are dependencies between them. 
2. **Use `depends_on`**: If you want to explicitly specify the order of creation, you can use the `depends_on` meta-argument to make sure resources are created in the desired order.

---

### **10. Pod vs ReplicaSet vs Deployment — which one should you choose for application deployment?**

- **Pod**: A single container instance running in Kubernetes, suitable for stateless applications but not ideal for managing replicas or ensuring high availability.
- **ReplicaSet**: Ensures a specified number of identical pods are running. It’s used to guarantee that a set number of pod replicas are maintained.
- **Deployment**: The most flexible and recommended option for deploying applications in Kubernetes. It manages ReplicaSets and allows easy rolling updates and rollbacks, ensuring smooth updates and high availability for your application.

For application deployment, **Deployment** is the recommended choice due to its manageability and ability to handle rolling updates and rollbacks.

---

### **11. How do you make applications highly available?**

To make applications highly available:
1. **Distribute across multiple Availability Zones (AZs)**: Ensure that the application is running in at least two AZs to minimize downtime in case of a failure.
2. **Use Load Balancers**: Use **ALB** or **NLB** to distribute traffic evenly across multiple instances or services in different AZs.
3. **Auto Scaling**: Set up **Auto Scaling** to scale instances automatically based on demand.
4. **Multi-AZ RDS**: Ensure databases are configured for Multi-AZ deployments to provide high availability and automatic failover.
5. **Route 53 DNS Failover**: Use **Route 53** for automatic DNS failover between regions or AZs in case of application or resource failure.
6. **Data Replication**: Use cross-region or cross-AZ replication in services like S3, DynamoDB, or EFS to ensure data availability across regions.

---

