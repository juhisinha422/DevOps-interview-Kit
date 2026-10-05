# DevOps Engineer Interview Questions & Answers

## ☁️ AWS & Networking

### 1) Design a 3-tier AWS architecture with a focus on networking
A typical 3-tier architecture includes:
- **Presentation Layer**: Web servers (e.g., EC2 instances or Elastic Beanstalk) deployed in a **public subnet** for serving front-end traffic.
- **Application Layer**: Business logic layer, usually EC2 instances or containerized applications in **private subnets** to keep them secure and inaccessible from the public.
- **Data Layer**: Databases (e.g., RDS, DynamoDB, or Aurora) placed in private subnets for security, not directly accessible from the internet.

Networking Configuration:
- Use **VPC** with a CIDR block (e.g., `10.0.0.0/16`).
- **Subnets**: Public subnets for load balancers and web servers, private subnets for application and database servers.
- **Security Groups**: Ensure the web server can access the application server and that the app server can access the database server.
- **NAT Gateway/Instance**: Provide internet access to private subnets.
- **Internet Gateway**: Attached to the VPC for public subnet internet access.

### 2) Difference between SNS vs SQS? Give real-world examples.
- **SNS (Simple Notification Service)**:
  - Push-based messaging service.
  - Sends messages to multiple subscribers (email, SMS, Lambda, etc.) at once.
  - Example: A website notifying users about promotions via email or SMS.

- **SQS (Simple Queue Service)**:
  - Pull-based message queuing service.
  - Messages are stored in a queue until retrieved by consumers.
  - Example: A food delivery app, where the order details are placed in a queue to be processed by a backend worker.

### 3) How to identify private vs public subnets?
- **Public Subnet**: Subnet has a route to an **Internet Gateway**. Resources in public subnets (e.g., web servers) can access the internet directly.
- **Private Subnet**: No direct route to the internet. Private subnets can access the internet via **NAT Gateway/Instance** for outgoing traffic but can't receive inbound traffic from the internet.

### 4) Suppose you have two EC2 instances in the same VPC, both deployed in different public subnets. How would you configure them to communicate with each other?
- Ensure **security groups** are configured to allow inbound and outbound traffic between the two EC2 instances (e.g., allowing traffic on port 80 or 443).
- Use **private IPs** of each EC2 instance to allow communication between them directly in the VPC.
- Make sure that **route tables** for the public subnets are set correctly, with routes to the **Internet Gateway** for external communication.

### 5) What if they are in different private subnets?
- Use **VPC Peering** or **Transit Gateway** to enable private communication between different subnets if they are in different VPCs.
- For subnets in the same VPC, ensure the **route tables** allow communication between them using their private IP addresses.
- Update **security groups** to allow communication between instances.

### 6) What is S3 Cross-Region Replication? Why do we use it?
- **Cross-Region Replication** (CRR) automatically copies objects from one S3 bucket to another in a different region.
- Use it for:
  - **Disaster Recovery**: Maintain data availability across regions.
  - **Performance**: Reduce latency by storing copies of your data closer to your users.
  - **Compliance**: Meet regulatory requirements for data locality.

### 7) Suppose your EC2 instance has attached EBS volume of 100 GB but the instance is not using full space, just using 40%. What to do to reduce volume?
- **Resize EBS Volume**:
  - **Step 1**: Stop the EC2 instance.
  - **Step 2**: Modify the EBS volume in the AWS Console/CLI to decrease the size.
  - **Step 3**: Start the instance again.
- **Optional**: You may need to adjust the file system on the EC2 instance (e.g., using `resize2fs` or `xfs_growfs`) to reflect the changes.

---

## Terraform

### 8) What is a data source in Terraform? Give an example where we can use it.
- **Data Source**: Used to retrieve information about existing infrastructure outside of Terraform management.
- **Example**: Fetching information about an existing AWS VPC.
  ```hcl
  data "aws_vpc" "existing_vpc" {
    id = "vpc-12345"
  }
  ```
### 9) What are key differences between terraform fmt vs terraform validate?

terraform fmt: Automatically formats your .tf configuration files to a consistent style.

terraform validate: Validates the syntax and configuration of Terraform files to ensure they are valid but doesn’t perform any actual provisioning.

### 10) What is Lifecycle Meta-Argument in Terraform?
Lifecycle Meta-Argument allows you to manage resource creation and destruction behavior.

create_before_destroy: Creates a new resource before destroying the old one.

prevent_destroy: Prevents Terraform from destroying a resource.

ignore_changes: Ignores certain changes when applying a plan.

```
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  lifecycle {
    prevent_destroy = true
  }
}
```

### 11) If I want to mark a Terraform variable as sensitive, how to mark? What happens if it's marked?
Use the sensitive = true flag when defining a variable.

```
Example

variable "password" {
  type      = string
  sensitive = true
}

```

Effect: Sensitive variables will not be displayed in Terraform plan/apply output to prevent sensitive data exposure.

## Kubernetes
### 12) What is Readiness Probe vs Liveness Probe in Kubernetes?

Readiness Probe: Checks if the application is ready to serve traffic. If the readiness probe fails, the pod is marked as "Not Ready."

Liveness Probe: Checks if the application is still running. If the liveness probe fails, Kubernetes will restart the pod.

### 13) Explain what is Deployment and StatefulSet with an example.
Deployment: Used for stateless applications, ensures a desired number of identical pods are running at all times. Example: Nginx server.

StatefulSet: Used for stateful applications, such as databases, where pods have unique identifiers (e.g., persistent storage).

### 14) What types of services in Kubernetes and explain each one?

ClusterIP: Exposes the service internally within the cluster.

NodePort: Exposes the service on a static port on each node.

LoadBalancer: Exposes the service externally via a cloud provider’s load balancer.

ExternalName: Maps the service to an external DNS name.


### 15) What do you understand by Taints and Tolerations?
Taints: Allow nodes to repel certain pods unless they tolerate the taint.

Tolerations: Allow a pod to schedule onto nodes with specific taints.

```
Example:

spec:
  tolerations:
  - key: "key1"
    operator: "Equal"
    value: "value1"
    effect: "NoSchedule"
```


### 16) What is Persistent Volume (PV) & Persistent Volume Claim (PVC)? How does it work?
Persistent Volume (PV): A piece of storage in the cluster that has been provisioned either statically or dynamically.

Persistent Volume Claim (PVC): A request for storage by a user.

PVCs are bound to PVs based on requested storage capacity and access modes.

## Jenkins

### 17) What is GitHub Action? Have you used it in your project?

GitHub Actions: CI/CD service to automate workflows directly within GitHub repositories. Used for tasks like testing, building, and deploying applications.

Example: Automating deployment after pushing code to GitHub.

### 18) How many types of Jenkins pipelines and which one you use in your project? Why?
Declarative Pipeline: More structured and easier to read, preferred for simpler pipelines.

Scripted Pipeline: Allows more flexibility and control, preferred for complex use cases.

###19) Suppose your Jenkins pipeline is stuck how to troubleshoot step by step?
Check logs: Start by reviewing the build logs to identify where the issue is.

Check agent status: Ensure Jenkins agents are online and not stuck.

Check pipeline configuration: Review the Jenkinsfile for any misconfigurations or missing stages.

Resource Allocation: Ensure there is enough system resource (CPU, memory) for Jenkins to run.

### 20) How do you integrate Jenkins with Git? Which plugin is required for this integration?

Use the Git plugin for Jenkins to integrate with Git repositories.

Configuration involves specifying the Git repository URL and credentials in the Jenkins job.

### 21) Can you explain the role of build executors in Jenkins, and how many executors were configured in your project?

- **Build Executors**: In Jenkins, an executor is a computational resource that runs jobs (builds). Each Jenkins node (master or agent) can have multiple executors, which means it can handle multiple jobs simultaneously.
- **Role**: Executors run the tasks specified in the Jenkins pipeline or job. The more executors you have, the more concurrent builds your Jenkins instance can handle.
- **Configuring Executors**: You configure the number of executors based on the load and performance needs. For example, a high-traffic Jenkins setup might have multiple executors per node to handle numerous builds in parallel.
  
  Example: In your Jenkins master or agent configuration, you might set it as follows:
  ```shell
  Number of executors: 4

## 🔧 DevOps Tools & Practices

### 22) What is the role of configuration management in DevOps? What tools have you used for it?

Configuration management ensures that systems are configured consistently and reliably, and it can automate the setup of infrastructure, applications, and environments.

Tools Used:

Ansible: Automates application deployment, configuration management, and orchestration.

Chef: Defines infrastructure as code to manage resources.

Puppet: Manages and enforces configuration of systems.

### 23) How do you implement Continuous Integration and Continuous Delivery (CI/CD)?
CI/CD Pipeline: A process that automates code integration (CI) and deployment (CD).

CI Tools: Jenkins, CircleCI, GitLab CI, Travis CI.

CI: Automatically builds and tests code when changes are pushed to a repository.

CD: Automates the deployment of tested code to production environments, ensuring continuous delivery of new features.

Example: A Jenkins pipeline could include stages like:
```
stage('Build') {
  steps {
    sh 'mvn clean package'
  }
}
stage('Test') {
  steps {
    sh 'mvn test'
  }
}
stage('Deploy') {
  steps {
    sh 'deploy.sh'
  }
}
```

### 24) What is Infrastructure as Code (IaC)? What tools have you used for it?
Infrastructure as Code (IaC) is the practice of managing and provisioning infrastructure through machine-readable files rather than manual configuration. It helps in version control, consistency, and automation of the infrastructure setup.

Tools Used:

Terraform: For provisioning cloud infrastructure in a declarative way.

AWS CloudFormation: AWS-specific tool to manage infrastructure as code.

Ansible: For configuration management and provisioning infrastructure.

### 25) What are container orchestration tools and why are they important in a microservices architecture?
Container Orchestration tools manage and automate the deployment, scaling, and operation of containers in a cluster.

Popular Orchestration Tools:

Kubernetes: The most widely used container orchestration platform that handles automated deployment, scaling, and management of containerized applications.

Docker Swarm: Docker's native clustering and orchestration solution.

Amazon ECS: A container service for managing Docker containers in AWS.

These tools are essential in a microservices architecture because they enable the management of many distributed, small services, allowing them to scale independently, self-heal, and maintain high availability.

### 26) Explain how monitoring and logging are handled in a DevOps environment?
Monitoring:

Prometheus: Open-source system monitoring and alerting toolkit that collects metrics and exposes them for analysis.

Grafana: A dashboard tool often paired with Prometheus for visualizing metrics.

CloudWatch: AWS’s native monitoring and logging service for cloud applications.

Logging:

ELK Stack (Elasticsearch, Logstash, Kibana): A set of tools for logging and visualization.

Fluentd: An open-source data collector for unified logging.

Splunk: A powerful tool for monitoring, searching, and analyzing machine-generated big data.

##🖥️ Linux & Troubleshooting

### 27) How to check system resource usage (CPU, memory, disk space) in Linux?
CPU Usage: Use top or htop to check the processes utilizing the most CPU.

Memory Usage: Use free -m or top to check memory usage.

Disk Space: Use df -h to check disk space and du -sh to find the size of specific directories.

Example for checking disk usage:

df -h   # Display disk space usage in human-readable format

du -sh * # Display size of each file/folder in the current directory

### 28) How to troubleshoot disk space issues in Linux?

Step 1: Use df -h to identify which partitions are running low on disk space.

Step 2: Use du -sh * to find large files and directories.

Step 3: Check if old logs or temporary files can be deleted to free up space.

Step 4: Check if there are any unnecessary Docker images or containers consuming disk space with docker system df.

Log Rotation: Configure automatic log rotation using tools like logrotate to ensure logs do not fill up disk space.

### 29) How to check open ports and services in Linux?

Use ss -tuln or netstat -tuln to check which ports are being listened to and by which services.
Example:

ss -tuln

### 30) How to find files modified in the last 30 minutes?

Use the find command with the -mmin option:

find /path/to/search -mmin -30

### 31) How do you troubleshoot high CPU usage in Linux?
Step 1: Use top or htop to find processes consuming excessive CPU.

Step 2: Investigate the application or process and optimize its resource usage.

Step 3: Check for any zombie processes using ps aux | grep Z.

Step 4: Review system logs (e.g., /var/log/syslog) for potential errors or misconfigurations.

## 🐳 Docker

### 32) What is Docker and why is it used?

Docker: A platform for developing, shipping, and running applications in containers. Containers allow you to package an application with all of its dependencies and run it consistently across different environments.

Why Docker?: It solves the "it works on my machine" problem and is widely used for microservices-based applications and continuous deployment pipelines.

### 33) How do you troubleshoot a Docker container?

Step 1: Check container logs using docker logs <container_name>.

Step 2: Inspect container status using docker ps and docker inspect <container_name>.

Step 3: Ensure that the container has the necessary resources (CPU, memory) and that it’s not being throttled.

Step 4: Use docker exec to access the container's shell for manual debugging.

## 🛠️ CI/CD and DevOps Practices

### 34) How would you implement Continuous Deployment in a Kubernetes-based application?

CI/CD pipeline:

Step 1: Use Jenkins, GitLab CI, or CircleCI to build and test the application.

Step 2: Use Helm to manage Kubernetes deployments and automate updates.

Step 3: Once the tests pass, deploy the application to Kubernetes using Helm or kubectl apply.

Step 4: Monitor the deployment using Prometheus and Grafana.

Example Helm deployment:

helm upgrade --install my-app ./charts/my-app
