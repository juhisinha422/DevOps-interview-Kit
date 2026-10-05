# Interview Preparation: Cloud, Kubernetes, Terraform, and DevOps (4+ Years)

---

### **🔹 Brief Introduction**

I have over 4 years of experience working with cloud technologies and DevOps practices, specifically in **AWS**, **Docker**, **Kubernetes**, **Terraform**, and **CI/CD pipelines**. I have built, deployed, and maintained scalable infrastructure, managed cloud resources, and automated deployments. I specialize in leveraging **infrastructure-as-code** (IaC), ensuring high availability, security, and cost optimization in cloud environments.

---

### **🔹 Load Balancing & Auto Scaling**

**Load Balancing** is a method of distributing network traffic across multiple servers to ensure no single server is overwhelmed, which helps with fault tolerance and availability. AWS provides several types of load balancers:
- **Application Load Balancer (ALB)**: Best for HTTP/HTTPS traffic, and provides features like path-based routing.
- **Network Load Balancer (NLB)**: Suitable for TCP traffic, especially when low latency is required.
- **Classic Load Balancer (CLB)**: Legacy option, supports both Layer 4 and Layer 7.

**Auto Scaling** ensures that the number of instances in your application automatically adjusts to meet the demand. In AWS, **Auto Scaling Groups (ASG)** dynamically add or remove EC2 instances based on CloudWatch metrics (e.g., CPU utilization), ensuring high availability and cost efficiency.

---

### **🔹 What is Docker?**

**Docker** is a platform that automates the deployment of applications inside lightweight, portable, and self-sufficient containers. Containers are isolated environments that include everything the application needs to run: code, runtime, libraries, and dependencies. Docker simplifies configuration management, enables faster deployment, and ensures consistency across different environments (development, staging, production).

---

### **🔹 What is Kubernetes (K8s) and why do we prefer K8s over Docker with advantages?**

**Kubernetes (K8s)** is an open-source platform designed for automating the deployment, scaling, and management of containerized applications. While Docker is great for containerizing applications, Kubernetes provides advanced orchestration features, such as:
- **Container Scheduling**: Kubernetes schedules containers across multiple hosts to maintain application availability.
- **Self-healing**: If a container or pod fails, Kubernetes automatically replaces or restarts it.
- **Scaling**: Kubernetes supports both horizontal and vertical scaling based on resource demand.
- **Load Balancing and Service Discovery**: Kubernetes manages internal load balancing and service discovery for containers.
- **Declarative Configuration**: You define your desired state (e.g., number of replicas), and Kubernetes ensures the system converges to that state.

While Docker alone is focused on containerization, Kubernetes provides advanced orchestration features that are essential for managing large-scale containerized applications.

---

### **🔹 Write a Simple Dockerfile**

Here’s a basic example of a **Dockerfile** to create a container for a simple Python application:

```dockerfile
# Use official Python image from Docker Hub
FROM python:3.9-slim

# Set the working directory inside the container
WORKDIR /app

# Copy the local code to the container
COPY . /app

# Install any required dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Set the command to run the application
CMD ["python", "app.py"]
```

This Dockerfile creates an image that contains a Python application and its dependencies.


### Creating Load Balancer & Security Groups via Terraform
# Create Security Group
```bash
resource "aws_security_group" "example_sg" {
  name        = "example-sg"
  description = "Allow inbound traffic on HTTP and SSH"
  vpc_id      = "<VPC_ID>"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# Create Load Balancer
resource "aws_lb" "example_lb" {
  name               = "example-lb"
  internal           = false
  load_balancer_type = "application"
  security_groups   = [aws_security_group.example_sg.id]
  subnets            = ["<SUBNET_ID>"]
  enable_deletion_protection = false
}
```

This Terraform code creates a Security Group to allow SSH and HTTP traffic and an Application Load Balancer (ALB).

### Kubernetes Components

Kubernetes consists of several core components:

Node: A worker machine in Kubernetes, can be a virtual or physical machine.

Pod: The smallest deployable unit in Kubernetes, typically a single container or multiple containers sharing the same resources.

Deployment: Manages the deployment and scaling of a set of pods.

Service: Exposes a set of pods as a network service.

ReplicaSet: Ensures a specified number of identical pods are running at any given time.

Kubelet: An agent that runs on each node and ensures that containers are running in pods.

Controller Manager: Governs the overall state of the Kubernetes cluster (e.g., scaling pods, managing deployments).

Scheduler: Decides where to place pods based on resource requirements.

###  What Happens When a Pod Dies and Who manages it?

When a pod dies, Kubernetes automatically detects the failure and attempts to replace the pod to maintain the desired state. This process is managed by the ReplicaSet (if the pod is part of a deployment), which ensures that the specified number of replicas is maintained. The Controller Manager is responsible for ensuring the overall health of the cluster and scheduling replacements for failed pods.

### Choosing the Right Instance Size for Your EC2 Instance

Choosing the right EC2 instance size depends on the following factors:

Application Requirements: Assess CPU, memory, and storage needs based on the workload.

Expected Traffic: For high-traffic applications, larger instances may be required to handle the load.

Cost Considerations: Select instance types based on cost-performance balance. Use AWS Trusted Advisor to evaluate under-utilized instances.

Scaling: Use Auto Scaling Groups to ensure that resources scale with traffic, rather than relying on a single instance size.

###  How to configure the Networking when an application is deployed over K8s

In Kubernetes, networking can be configured as follows:

Pod-to-Pod Communication: Kubernetes automatically assigns each pod a unique IP within the cluster to allow direct communication between pods.

Services: To expose applications, define a Service that acts as a stable endpoint for accessing the pods.

Ingress Controllers: For external access, use Ingress Controllers (ALB/NGINX) to route traffic based on URL paths or domain names.

Network Policies: Use Network Policies to control the flow of traffic between pods, adding a layer of security.

### IAM Fundamentals

IAM (Identity and Access Management) in AWS is used to control access to AWS services and resources securely. Key concepts include:

Users: Individual identities within AWS.

Groups: Collections of users for easier policy management.

Roles: Assign permissions to entities (users, applications, or services).

Policies: Documents defining permissions (allow or deny actions on resources).

MFA (Multi-Factor Authentication): Adds an extra layer of security by requiring both password and verification code.

### How Networking is Managed in Kubernetes?

In Kubernetes, networking is managed through:

Pod Network: Each pod gets a unique IP address, and Kubernetes uses a flat network model where pods across different nodes can communicate with each other.

Services: A Service in Kubernetes provides a stable IP address and DNS name for accessing a set of pods, abstracting the dynamic nature of pod IPs.

Network Policies: Kubernetes Network Policies define how pods communicate with each other. You can allow or deny traffic based on labels and namespaces.

DNS: Kubernetes has an internal DNS server for service discovery, allowing pods to communicate using DNS names instead of IP addresses.

### Terraform Data Sources

Terraform Data Sources allow you to query information about existing resources in your infrastructure. They are read-only and do not modify the infrastructure. For example, you can use a data source to fetch an existing security group ID, an AMI ID, or any other resource attribute that is already part of your infrastructure.

```bash
Example:

data "aws_ami" "latest_amazon_linux" {
  most_recent = true
  owners      = ["amazon"]
  filters = {
    name = "amzn2-ami-hvm-*-x86_64-gp2"
  }
}
```

### Experience with Monitoring Tools (Datadog / Prometheus / Grafana / CloudWatch)

I have experience using several monitoring tools:

CloudWatch: For logging and monitoring AWS resources
