### IBM AWS DevOps Engineer Interview Preparation

## 1. Self Introduction

Hello, my name is [Your Name], and I am a passionate DevOps engineer with [X years] of experience working with cloud technologies, automation, and containerization. I have a strong background in tools like AWS, Kubernetes, Terraform, Jenkins, and Docker to build scalable, automated, and secure infrastructure. I am experienced in managing the lifecycle of applications and services from deployment to monitoring, ensuring continuous integration and delivery. In my previous roles, I have collaborated closely with development and operations teams to streamline processes and deliver high-quality software solutions efficiently.

## 2. Explain Kubernetes Architecture

Kubernetes is an open-source container orchestration platform designed to automate deploying, scaling, and managing containerized applications.

The architecture of Kubernetes consists of two main components:

**Master Node:**
API Server: The frontend of the Kubernetes control plane. It exposes the Kubernetes API.
Controller Manager: Ensures the desired state of the cluster is maintained (e.g., replica count, stateful sets).
Scheduler: Assigns workloads (pods) to nodes based on resource availability and constraints.
etcd: A distributed key-value store that stores all the cluster's state data, including configurations and secrets.

**Worker Node:**
Kubelet: An agent that runs on each node and ensures the containers in the pod are running as expected.
Kube Proxy: Maintains network rules and handles routing of traffic between the pod network and external clients.
Container Runtime: The software responsible for running containers (e.g., Docker, containerd).
3. Have You Worked on Auto-scaling in Kubernetes?

Yes, I have worked extensively with auto-scaling in Kubernetes, both in terms of Horizontal Pod Autoscaler (HPA) and Cluster Autoscaler. HPA automatically adjusts the number of pods in a deployment based on CPU utilization or custom metrics. Cluster Autoscaler adjusts the number of nodes in the cluster when the pod demands exceed available resources or when there are unused resources.

```
For example, we can configure HPA like this:

apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
```

## 4. Difference Between HPA and VPA
Horizontal Pod Autoscaler (HPA): Scales the number of pods in a deployment, replicating more instances based on metrics like CPU utilization or memory usage.
Vertical Pod Autoscaler (VPA): Adjusts the resources (CPU/Memory) allocated to individual pods based on usage patterns, instead of changing the number of pods.

## 5. Explain What Happens When a Pod Crashes?

When a pod crashes, Kubernetes will attempt to restart it. The following steps typically occur:

Kubelet detects that the pod is not healthy.
The Replication Controller/ReplicaSet ensures the desired number of replicas are maintained, and it creates a new pod to replace the failed one.
Liveness and Readiness probes (if defined) are used to determine if the pod is running correctly. If the pod fails, the system will attempt to restart it as per the configured policies.

## 6. How Do You Explore Applications Running on Kubernetes Clusters?

To explore applications running in Kubernetes clusters, you can use the kubectl tool:

Use kubectl get pods to list all pods running in the cluster.
Use kubectl describe pod <pod-name> to get more details about a specific pod.
You can also use kubectl logs <pod-name> to view the logs of the running pod.
For debugging, kubectl exec <pod-name> -- /bin/bash allows you to exec into the pod to explore the container environment directly.

## 7. Explain Terraform Modules Which You Are Writing for Your Project

In my current project, I use Terraform modules to encapsulate infrastructure logic. For example, I write reusable modules to deploy EC2 instances, VPCs, and RDS databases.

```
A typical module for deploying an EC2 instance may look like this:

module "ec2_instance" {
  source = "./modules/ec2_instance"
  instance_type = "t2.micro"
  ami = "ami-xxxxxxxx"
  region = "us-east-1"
}
```

Modules help to reuse and organize infrastructure code into logical units. Each module can be customized using input variables and produces output values that can be passed to other modules or resources.

## 8. Write a Sample Terraform Module to Launch an EC2 Instance

``
Here’s an example of a simple Terraform module to launch an EC2 instance:

# ec2_instance.tf
resource "aws_instance" "example" {
  ami           = var.ami
  instance_type = var.instance_type
  key_name      = var.key_name
}

# variables.tf
variable "ami" {}
variable "instance_type" {
  default = "t2.micro"
}
variable "key_name" {}

# outputs.tf
output "instance_id" {
  value = aws_instance.example.id
}

```

## 9. Instance Types Are You Aware of, and Which Instance Type Have You Used?

I am familiar with several instance types in AWS, such as:

T2/T3 instances: General-purpose instances with burstable performance.
M5 instances: General-purpose instances with a good balance of compute, memory, and networking.
C5 instances: Compute-optimized instances.
R5 instances: Memory-optimized instances.
P3 instances: GPU instances for machine learning and high-performance computing.

I have used T2.micro instances for development and M5.large for production workloads.

## 10. Explain and Write Stages of Pipeline in Your Project

In my project, the typical stages of a CI/CD pipeline include:

Source: The pipeline is triggered when changes are pushed to the source repository (e.g., GitHub, GitLab).
Build: The application is built using a tool like Maven, Gradle, or a Docker image is built using a Dockerfile.
Test: Unit, integration, and regression tests are run to validate the application.
Deploy: The application is deployed to different environments (dev, staging, prod) using tools like Kubernetes or ECS.
Monitor: Once deployed, the system is monitored using tools like Prometheus and Grafana to ensure performance and health.

```
Here’s a simplified Jenkins pipeline:

pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'mvn clean install'
      }
    }
    stage('Test') {
      steps {
        sh 'mvn test'
      }
    }
    stage('Deploy') {
      steps {
        sh 'kubectl apply -f deployment.yaml'
      }
    }
  }
}
```

## 11. Write a Simple Dockerfile and Explain
```
A simple Dockerfile for a Python application might look like this:

# Use an official Python runtime as a parent image
FROM python:3.8-slim

# Set the working directory in the container
WORKDIR /app

# Copy the current directory contents into the container
COPY . /app

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Make port 80 available to the world outside the container
EXPOSE 80

# Define the command to run the app
CMD ["python", "app.py"]
FROM specifies the base image.
WORKDIR sets the working directory.
COPY copies files into the image.
RUN installs dependencies.
EXPOSE makes the container’s port available.
CMD specifies the command to run when the container starts.
```

## 12. If the Docker Image Size is Huge, How Do You Reduce It?

To reduce the size of a Docker image:

Use a smaller base image like alpine or slim versions.
Avoid unnecessary dependencies; only install what's required.
Use multi-stage builds to separate build and runtime environments.
Minimize layers by combining commands using && in the Dockerfile.

```
For example:

FROM python:3.8-alpine AS build
WORKDIR /app
COPY . .
RUN pip install --no-cache-dir -r requirements.txt

FROM python:3.8-slim
WORKDIR /app
COPY --from=build /app .
CMD ["python", "app.py"]
```

## 13. Difference Between ALB and NLB
Application Load Balancer (ALB): Best for HTTP/HTTPS traffic, providing features like URL path-based routing, host-based routing, and WebSocket support.
Network Load Balancer (NLB): Best for high-performance, low-latency traffic, typically used for TCP or UDP traffic. NLB operates at the connection level (Layer 4) and can handle millions of requests per second.
