# DevOps Engineer Interview Questions & Answers

## 🔹 Cloud & AWS

### 1. How will you design a highly available application on the cloud?
To design a highly available application in the cloud:
- **Multi-AZ Deployment**: Deploy your application across multiple Availability Zones (AZs) within a region to ensure redundancy. In AWS, this means setting up your EC2 instances across different AZs and using a load balancer to distribute traffic.
- **Load Balancer**: Use an Application Load Balancer (ALB) or Network Load Balancer (NLB) to automatically distribute incoming application traffic across multiple EC2 instances.
- **Auto Scaling**: Set up auto-scaling policies that adjust the number of EC2 instances based on the traffic load, ensuring that the application can scale up or down seamlessly.
- **RDS for Database Availability**: Use Amazon RDS with Multi-AZ deployment for automatic failover. For highly available database architecture, consider using Aurora for built-in multi-region support.
- **Data Replication**: Ensure data is replicated to prevent single points of failure, such as S3 cross-region replication for backups and availability.
- **CloudWatch Monitoring**: Use AWS CloudWatch for monitoring the health of the application and setting up alerts for auto-scaling triggers and failure notifications.

### 2. Suppose an EC2 instance has a 50GB disk and 48GB is full — how can you increase volume size without downtime?
- **Step 1**: Stop the EC2 instance.
- **Step 2**: Modify the volume in the AWS Management Console or AWS CLI by selecting the EC2 instance's volume and resizing it (e.g., increase the size to 100GB).
- **Step 3**: Start the EC2 instance.
- **Step 4**: SSH into the EC2 instance and resize the file system using `resize2fs` for ext4 or `xfs_growfs` for XFS file systems (depending on the file system used).
This process is mostly non-disruptive and avoids downtime if done carefully, but it’s always a good idea to test in a staging environment first.

### 3. Difference between Multi-AZ and Read Replicas in AWS RDS?
- **Multi-AZ**: Provides synchronous replication for high availability. A primary database is mirrored to a standby instance in a different AZ. In case of a failure, AWS automatically fails over to the standby instance, ensuring minimal downtime.
- **Read Replicas**: Provide asynchronous replication, where read traffic can be distributed to replicas to offload the primary database. These are mainly for scaling read workloads and do not provide automatic failover.

### 4. How do you transfer data from an S3 bucket in one AWS account to another?
- **IAM Roles**: Create an IAM role in the destination account with `s3:ListBucket` and `s3:GetObject` permissions for the source bucket, and assign it to an IAM user in the destination account.
- **Bucket Policy**: Alternatively, add a bucket policy in the source bucket to allow access from the destination account's IAM user/role.
- **CLI Command**: Use the `aws s3 cp` or `aws s3 sync` commands to copy data between accounts. For example:
 
```bash
  aws s3 cp s3://source-bucket/path/to/file s3://destination-bucket/path/to/file --profile source-profile
```

## 🔹 Scaling & Performance

### 5. Your application suddenly receives high traffic — how would you handle it?

Auto Scaling: Ensure auto-scaling groups are configured for your EC2 instances. Set up dynamic scaling policies based on metrics like CPU utilization or request count.
Load Balancer: Ensure that your load balancer (e.g., ELB or ALB) is properly configured to distribute traffic across instances.
Caching: Use CloudFront for caching static content. Set up Elasticache (Redis/Memcached) to cache dynamic content.
Database Scaling: Scale your database (e.g., RDS or Aurora) horizontally by adding read replicas or vertically by upgrading the instance type.
Traffic Management: Use AWS WAF to protect against malicious traffic spikes and route users to healthy instances.

### 6. What is Auto Scaling? Explain min, max, and desired capacity.
Auto Scaling: Automatically adjusts the number of EC2 instances in your application based on demand. It helps ensure application availability and optimizes cost by adjusting capacity.
Min Capacity: The minimum number of instances that should always be running.
Max Capacity: The maximum number of instances that Auto Scaling should scale up to during high traffic or load.
Desired Capacity: The target number of instances that Auto Scaling tries to maintain. It can adjust between the min and max capacity based on the load.

### 7. How would you design a system for high availability across multiple regions?
Multiple Regions: Deploy your application in at least two regions for redundancy. Use Route 53 for DNS failover to ensure traffic is routed to the healthy region.
Cross-Region Load Balancing: Use AWS Global Accelerator or Route 53 for traffic distribution and automatic failover between regions.
Data Replication: Use services like Aurora Global Databases or DynamoDB Global Tables for cross-region data replication and consistency.
Multi-Region Auto Scaling: Set up auto-scaling in each region to handle increased traffic.

## 🔹 Networking & Infrastructure

### 8. In a VPC with two subnets, how do you identify which is public and which is private?
Public Subnet: A subnet is considered public if it has a route to an Internet Gateway. Public subnets are typically used for resources that need direct internet access (e.g., web servers).
Private Subnet: A subnet is private if it does not have a direct route to an Internet Gateway, but it may have a route to a NAT Gateway or NAT Instance for internet access.

### 9. Explain Docker networking.
Bridge Network: The default network mode for Docker containers. Containers can communicate with each other on the same host using this network.
Host Network: The container shares the network stack of the host system. It is useful for scenarios requiring high performance and network isolation.
None Network: No networking is enabled for the container, isolating it from the network.
Overlay Network: Used in Docker Swarm, enabling communication between containers running on different Docker hosts across multiple machines.
Macvlan Network: Assigns a unique MAC address to each container, making it appear as a physical device on the network.

### 10. CMD vs ENTRYPOINT in Docker?

CMD: Provides default arguments to the entry point when running a container. It can be overridden by providing arguments at runtime.
Example: CMD ["python", "app.py"]
ENTRYPOINT: Defines the executable that will run when the container starts. It cannot be overridden by providing arguments at runtime, but the CMD can provide default arguments.
Example: ENTRYPOINT ["python"]

## 🔹 Kubernetes (K8s)

### 11. What are common Kubernetes security measures in EKS?
RBAC: Role-Based Access Control to define who can access and modify Kubernetes resources.
IAM Roles for Service Accounts (IRSA): Assign AWS IAM roles to Kubernetes service accounts for fine-grained access to AWS services.
Network Policies: Use Kubernetes Network Policies to control traffic flow between pods and services.
Encryption: Enable encryption at rest and in transit for sensitive data.
Audit Logging: Enable API server auditing to track all activities within the Kubernetes cluster.

### 12. What are Kubernetes Services and how are they used?
Kubernetes Service: A logical abstraction that defines a policy to access a set of Pods. Services enable load balancing across Pods, providing stable network access.
ClusterIP: Exposes the service on an internal IP in the cluster (default).
NodePort: Exposes the service on a static port on each node.
LoadBalancer: Exposes the service externally via a cloud provider’s load balancer.
ExternalName: Maps the service to an external DNS name.

### 13. What is CrashLoopBackOff? How do you troubleshoot it?
CrashLoopBackOff: This error occurs when a container repeatedly crashes and restarts within a Kubernetes pod. To troubleshoot:
Check the pod logs: kubectl logs <pod_name>.
Investigate resource limits (CPU/Memory), environment variables, or application crashes.
Use kubectl describe pod <pod_name> to view events related to the pod.

### 14. Pod is stuck in Pending state — how do you debug?
Check Resources: Ensure there are enough available resources in the cluster to schedule the pod (memory, CPU).
Node Affinity: Verify that no node affinity rules are preventing pod scheduling.
Taints & Tolerations: Ensure that the pod is not blocked by taints on nodes.
Check Scheduler Logs: Use kubectl describe pod <pod_name> to check for scheduling errors.

### 15. Difference between rolling updates and rollback?
Rolling Updates: A method of updating your application where Pods are updated incrementally. This ensures there is no downtime during updates.
Rollback: If a new deployment fails, a rollback reverts the deployment to a previous stable version.


### 16. Explain Blue-Green vs Canary deployments.
- **Blue-Green Deployment**: In a Blue-Green deployment, you maintain two environments (Blue and Green). Blue represents the current production environment, and Green is the new version of the application. After testing the Green environment, you switch all traffic from Blue to Green. If issues arise, you can quickly switch back to Blue.
  - **Advantages**: Quick rollback, no downtime.
  - **Disadvantages**: Requires twice the resources (Blue and Green environments).
  
- **Canary Deployment**: In a Canary deployment, you release a new version of the application to a small subset of users (the "canaries") before gradually rolling it out to the entire user base. The deployment is monitored, and if there are issues, you can halt the rollout.
  - **Advantages**: Gradual release, easier to monitor and test in production.
  - **Disadvantages**: Requires sophisticated traffic management and monitoring.

### 17. How do you provide read-only access to a developer in Kubernetes (RBAC)?
- **RBAC (Role-Based Access Control)** allows you to define granular access control to Kubernetes resources. To give a developer read-only access:
  - Create a **ClusterRole** with `get`, `list`, and `watch` permissions for the resources you want to allow access to.
  - Bind the ClusterRole to the developer using a **ClusterRoleBinding**.
 
Example:
  ```yaml
  kind: ClusterRole
  apiVersion: rbac.authorization.k8s.io/v1
  metadata:
    # Name of the ClusterRole
    name: read-only-access
  rules:
    - verbs: ["get", "list", "watch"]
      resources: ["pods", "services", "deployments"]

  kind: ClusterRoleBinding
  apiVersion: rbac.authorization.k8s.io/v1
  metadata:
    name: read-only-binding
  subjects:
    - kind: User
      name: "developer"  # User or Group Name
      apiGroup: rbac.authorization.k8s.io
  roleRef:
    kind: ClusterRole
    name: read-only-access
    apiGroup: rbac.authorization.k8s.io
```


## DevOps Tools & Practices

### 18. How do you use Grafana? How did you integrate it in your project?
Grafana is an open-source platform for monitoring and observability. It integrates with various data sources like Prometheus, InfluxDB, or Elasticsearch to visualize metrics and logs.
Integration: To integrate Grafana into your project:
Set up Prometheus to collect application metrics.
Install Grafana and configure it to use Prometheus as the data source.
Create and customize dashboards to visualize key metrics, like CPU usage, memory, request rate, and error rates.
Alerting: Set up alert rules in Grafana to notify teams about threshold breaches or system issues.

### 19. What tools have you used for Kubernetes (self-managed vs cloud)?
Self-Managed Kubernetes:
Helm: A package manager for Kubernetes that simplifies application deployment and management.
Kops: Kubernetes Operations (Kops) is used to create, destroy, upgrade, and maintain Kubernetes clusters on AWS.
Kubectl: The primary command-line tool for interacting with Kubernetes clusters.
Cloud-Managed Kubernetes (e.g., AWS EKS):
eksctl: A command-line tool for simplifying the creation and management of EKS clusters.
AWS CLI: Used to interact with AWS services, including EKS for creating and managing Kubernetes clusters.
AWS SDKs: Used for automating tasks programmatically, especially in CI/CD pipelines.

### 20. What do you do in Terraform? How do you write and manage infra code?
Terraform is an Infrastructure-as-Code (IaC) tool that allows you to define, provision, and manage infrastructure using declarative configuration files. In Terraform:
Writing Infra Code: You write configuration files in .tf format, specifying resources like EC2 instances, VPCs, S3 buckets, etc.
Modules: Organize code into reusable modules for managing complex infrastructure.
State Management: Terraform maintains state files to track the infrastructure you’ve provisioned, ensuring you can make changes safely.
Execution: Run terraform init to initialize the working directory, terraform plan to see what changes will be applied, and terraform apply to provision the infrastructure.

```
Example:

resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}
```

### 21. How would you build a 3-tier architecture using Infrastructure as Code?
3-Tier Architecture: Involves three layers—presentation (frontend), application (business logic), and data (database).
Presentation Layer: EC2 instances or Elastic Beanstalk for web servers in a public subnet.
Application Layer: EC2 instances or containers (ECS/EKS) in private subnets, running business logic and API services.
Data Layer: RDS, DynamoDB, or Aurora for the database layer.
Security: Use security groups and IAM roles to control access between layers. For example, only the application layer should be able to access the database.

```
Terraform Code Example:

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"
  map_public_ip_on_launch = true
}
resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.public.id
}
```

## 🔹 Linux & Troubleshooting

### 22. How to check open ports in Linux?

Use netstat -tuln or ss -tuln to list open ports and services listening on those ports.

Example:

ss -tuln

This shows open TCP/UDP ports and their associated services.

### 23. How to find large files in Linux?

Use the find command to search for large files:

find / -type f -size +100M

This command searches for files larger than 100MB.

### 24. How to find files modified in the last 30 minutes?

Use find with the -mmin flag to search for files modified within the last 30 minutes:

find /path/to/search -mmin -30

### 25. How do you troubleshoot disk space issues?
Check Disk Usage: Use df -h to check the overall disk usage.
Identify Large Files: Use du -sh * in directories to locate large files or directories.
Clean Up: Delete unnecessary files, logs, or old backups. You can also use log rotation to manage log file sizes automatically.

## 🔹 Docker

### 26. What is multi-stage Docker build?
Multi-Stage Builds: This Docker feature allows you to use multiple FROM statements in a Dockerfile. Each stage can perform a specific task, such as building and compiling code, and only the final stage contains the runtime environment.
This helps reduce image size by discarding intermediate stages.

```
Example:

# Stage 1: Build Stage
FROM node:14 AS build
WORKDIR /app
COPY . .
RUN npm install

# Stage 2: Final Stage
FROM node:14-slim
WORKDIR /app
COPY --from=build /app .
CMD ["node", "index.js"]
```

### 27. Why avoid Alpine images sometimes? What are alternatives?
Alpine Images: Alpine images are lightweight, but they are based on musl libc instead of glibc, which can lead to compatibility issues with certain applications.
Alternatives: Use debian or ubuntu images when compatibility with glibc is required, or when you need a more comprehensive package ecosystem.
When to use Alpine: Alpine is ideal when you need a small, minimal base image and can handle the additional complexity of working with musl libc.


