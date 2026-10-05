# Cloud DevOps Interview Questions at "Capital One"

---

## 𝐑𝐨𝐮𝐧𝐝𝟏 - 𝐓𝐞𝐜𝐡𝐧𝐢𝐜𝐚𝐥

### 1. What are your daily responsibilities as a DevOps engineer?

* Manage CI/CD pipelines (Jenkins/GitHub Actions)
* Monitor applications using Prometheus, Grafana
* Handle deployments using Docker & Kubernetes
* Automate repetitive tasks using Bash/Python
* Manage AWS infrastructure (EC2, S3, IAM, VPC)
* Troubleshoot production issues

---

### 2. Have you worked with monitoring and logging tools like Prometheus, Grafana, or ELK Stack?

Yes, I have hands-on experience with:

* Prometheus for metrics collection
* Grafana for dashboards and alerting
* ELK Stack (Elasticsearch, Logstash, Kibana) for centralized logging

---

### 3. Can you describe the CI/CD workflow in your project?

* Code pushed to Git repository
* CI pipeline triggered
* Build using Maven/Gradle
* Run unit tests
* Perform code quality checks (SonarQube)
* Build Docker image
* Push image to registry (ECR/Docker Hub)
* Deploy to Kubernetes using Helm

---

### 4. How do you handle the continuous delivery (CD) aspect in your projects?

* Automated deployments via pipelines
* Use staging and production environments
* Approval gates before production deployment
* Rolling updates / Blue-Green deployment strategies

---

### 5. What methods do you use to check for code vulnerabilities?

* SAST using SonarQube
* DAST using OWASP ZAP
* Dependency scanning using Snyk/Trivy
* Container image scanning using Trivy

---

### 6. What AWS services are you proficient in

* EC2, S3, IAM, VPC
* ALB/NLB, Auto Scaling
* RDS, CloudWatch
* EKS, Route53

---

### 7. How would you access data in an S3 bucket from Account A when your application is running on an EC2 instance in Account B?

* Create IAM Role in Account B
* Attach S3 access policy
* Add bucket policy in Account A to allow Account B role
* Use STS AssumeRole if required

---

### 8. How do containerisation technologies like Docker and Kubernetes simplify application deployment and management?

* Docker ensures consistent environments
* Kubernetes provides scaling, self-healing, and load balancing
* Enables faster deployments and easy rollbacks

---

## 𝐑𝐨𝐮𝐧𝐝𝟐 - 𝐓𝐞𝐜𝐡𝐧𝐢𝐜𝐚𝐥

### 8. How do you provide access to an S3 bucket, and what permissions need to be set on the bucket side?

* Use IAM policies and bucket policies
* Required permissions:

  * s3:GetObject
  * s3:PutObject
  * s3:ListBucket

---

### 9. How can Instance 2, with a static IP, communicate with Instance 1, which is in a private subnet and mapped to a multi-AZ load balancer?

* Instance 2 connects via Load Balancer DNS
* Load balancer routes traffic to Instance 1
* Configure security groups to allow traffic

---

### 10. For an EC2 instance in a private subnet, how can it verify and download required packages from the internet without using a NAT gateway or bastion host? Are there any other AWS services that can facilitate this?

* Use VPC Endpoints (for S3/ECR)
* Use AWS Systems Manager (SSM)
* Use PrivateLink services where applicable

---

### 11. What is the typical latency for a load balancer, and if you encounter high latency, what monitoring steps would you take?

* Typical latency: 10–100 ms
* Check CloudWatch metrics
* Enable access logs
* Verify backend health
* Analyze network delays

---

### 12. If your application is hosted in S3 and users are in different geographic locations, how can you reduce latency?

* Use CloudFront CDN
* Enable caching
* Use edge locations

---

### 13. Can you share an example of a complex automation script you've written?

* Script to automate:

  * Code pull
  * Docker build & push
  * Deployment to Kubernetes
* Implemented error handling, logging, retries

---

### 14. How do you approach troubleshooting and debugging automation scripts?

* Check logs and exit codes
* Debug step-by-step
* Validate inputs/environment variables
* Test locally before pipeline execution

---

## 𝐑𝐨𝐮𝐧𝐝𝟑 - 𝐓𝐞𝐜𝐡𝐧𝐢𝐜𝐚𝐥

### 15. Which services can be integrated with a CDN (Content Delivery Network)?

* S3
* EC2
* ALB
* API Gateway

---

### 16. How do you dynamically retrieve VPC details from AWS to create an EC2 instance using IaC, can you write the code?

```hcl
data "aws_vpc" "existing" {
  default = true
}

resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
  subnet_id     = data.aws_vpc.existing.id
}
```

---

### 17. How do you manage unmanaged AWS resources in Terraform?

* Use terraform import
* Bring resources into Terraform state
* Manage via code after import

---

### 16. How do you pass arguments to a VPC while using the `terraform import` command?

* Arguments are defined in .tf file
* Import command only maps resource:

```
terraform import aws_vpc.my_vpc vpc-123456
```

---

### 18. What are the prerequisites before importing a VPC in Terraform?

* Resource block must be defined
* Correct provider configuration
* Valid resource ID

---

### 19. If an S3 bucket was created through Terraform but someone manually added a policy to it, how do you handle this situation using IaC?

* Run terraform plan to detect drift
* Run terraform apply to revert changes
* Optionally import/update policy in code

---

### 20. Have you upgraded any Kubernetes clusters?

* Yes
* Upgrade control plane first
* Upgrade worker nodes
* Ensure version compatibility
* Validate workloads post-upgrade

---

### 21. How do you deploy an application in a Kubernetes cluster?

* Create Deployment and Service YAML files
* Apply using:

```
kubectl apply -f deployment.yaml
```

---

### 22. How do you communicate with a Jenkins server and a Kubernetes cluster?

* Use Jenkins Kubernetes plugin
* Configure kubeconfig/service account
* Use kubectl or Helm from Jenkins pipeline

---

### 23. Do you only update Docker images in Kubernetes, or do you also update replicas, storage levels, and CPU allocation?

* Update Docker images
* Modify replicas for scaling
* Adjust CPU/memory requests & limits
* Update storage (PVCs), ConfigMaps, Secrets

---
