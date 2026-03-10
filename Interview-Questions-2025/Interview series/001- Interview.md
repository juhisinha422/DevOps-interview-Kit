# DevOps Knowledge - 4+ Years Experience

This README provides answers to various DevOps-related questions across different tools and technologies such as Linux, Terraform, AWS, Networking, CI/CD, and Docker. This is designed for individuals with **4+ years of experience**.

---

## 🔹 Linux

### 1. How do you find a file with a specific size in Linux?
You can use the `find` command to search for files by size:
```bash
find /path/to/search -type f -size +100M
This will find files larger than 100MB. You can adjust the size as needed.
```

### 2. How do you search for a file by name in Linux?

To search for a file by name, use:
```
find /path/to/search -type f -name "filename"
```

### 3. How do you change file permissions in Linux?
To change file permissions, use chmod:

```
chmod 755 filename
This gives full permissions to the owner, and read/execute permissions to others. You can also use symbolic permissions:
chmod u+x filename
```

## 🔹 Terraform

#### 1. What is the difference between terraform taint and terraform untaint?

terraform taint: Marks a resource to be destroyed and recreated in the next terraform apply.

terraform untaint: Removes the taint from a resource, preventing it from being recreated.

### 2. What is the use of terraform refresh?

The terraform refresh command updates the Terraform state with the actual state of the resources, without making any changes to the infrastructure.

### 3. What is the difference between terraform init, plan, and apply?

terraform init: Initializes the working directory, downloads the necessary provider plugins.

terraform plan: Creates an execution plan showing what changes Terraform will apply.

terraform apply: Applies the changes to the infrastructure.

### 4. How do you store Terraform state remotely?
```
To store the state remotely, you can configure a backend, such as AWS S3:

terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "path/to/state"
    region = "us-east-1"
  }
}
5. How do you view the Terraform state file?

To view the state file, use:

terraform show

```

### 6. How do you avoid exposing sensitive values during terraform apply?

```
Use sensitive variables:

variable "my_password" {
  type      = string
  sensitive = true
}

Also, pass sensitive data using environment variables or .tfvars files.
```

### 7. How do you handle secrets in Terraform?

Secrets can be managed via AWS Secrets Manager, Vault, or environment variables. Avoid storing them directly in .tf files.

### 8. How do you avoid hardcoding values in Terraform?

You can use input variables, data sources, or remote state outputs to avoid hardcoding values.

### 9. How do you import existing cloud infrastructure into Terraform?

```
Use the terraform import command:

terraform import aws_instance.example i-1234567890abcdef0
```

## 🔹 AWS

#### 1. What is the difference between EKS and ECS?

EKS (Elastic Kubernetes Service) is a managed Kubernetes service for running containerized applications using Kubernetes.

ECS (Elastic Container Service) is a managed container service for running Docker containers.

### 2. What is an S3 lifecycle policy?

An S3 lifecycle policy automatically transitions objects between storage classes or deletes objects after a certain period.

### 3. What is the maximum object size in S3?

The maximum object size in S3 is 5TB.

### 4. Is there a limit on S3 bucket size?

No, there is no limit on the total size of an S3 bucket. You can store an unlimited amount of data.

### 5. What is the maximum runtime of AWS Lambda?

The maximum runtime for an AWS Lambda function is 15 minutes.

### 6. What are common Lambda issues like cold start?

Cold start: Increased latency when Lambda needs to initialize the function runtime.

Memory limits: If the Lambda function is allocated insufficient memory, it might run slower.

Timeouts: Functions that run longer than the configured timeout will be terminated.

### 7. If RDS CPU usage is high, how would you troubleshoot it?

Check the RDS Performance Insights for slow queries.

Scale your RDS instance (increase CPU or memory).

Monitor CloudWatch metrics for spikes in resource usage.

Review query execution plans to optimize database performance.

## 🔹 Networking

### 1. What is a CIDR block?

CIDR (Classless Inter-Domain Routing) block represents an IP address range. For example, 10.0.0.0/24 represents a subnet with IP addresses from 10.0.0.0 to 10.0.0.255.

### 2. What is the difference between public and private subnet?

Public subnet: Has a route to the internet via an Internet Gateway, used for resources that require internet access (e.g., EC2 instances).

Private subnet: Does not have direct internet access, used for internal resources that don't need to be publicly reachable.

### 3. How do you design CIDR blocks for VPC, public subnet, and private subnet?

Example of a typical CIDR block design:

VPC: 10.0.0.0/16 (65,536 IP addresses)

Public subnet: 10.0.1.0/24 (256 IP addresses)

Private subnet: 10.0.2.0/24 (256 IP addresses)

## 🔹 CI/CD & DevOps

### 1. What types of pipeline failures have you encountered during build or deployment stages?

Build failures due to missing dependencies or incorrect versions.

Deployment failures because of misconfigured environments or infrastructure.

Test failures caused by external service disruptions or incorrect configurations.

### 2. How do you troubleshoot CI/CD pipeline failures?

Review logs: Examine build and deployment logs to identify the error.

Check configuration files: Verify if there are issues in CI/CD configuration (e.g., .gitlab-ci.yml, Jenkinsfile).

Monitor environment variables: Ensure that correct secrets and environment variables are provided.

Validate external dependencies: Ensure services your pipeline depends on are up and running.

## 🔹 Docker

### 1. What is the purpose of the docker tag command?

The docker tag command is used to assign a tag to an image, making it easier to refer to in Docker Hub or a private registry.

docker tag myimage:latest myusername/myimage:v1.0

### 2. What command is used to build a Docker image from a Dockerfile?

To build a Docker image from a Dockerfile, use:

docker build -t myimage:latest .

This will build the image from the current directory (.) using the Dockerfile.

