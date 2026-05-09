# DevOps Interview Questions & Answers

---

## Tell me about yourself?

* I have around 4 years of experience in DevOps and Cloud Engineering
* Worked on AWS, Kubernetes, Docker, Terraform, Jenkins, and CI/CD pipelines
* Experienced in infrastructure automation, container orchestration, monitoring, and troubleshooting production issues
* Worked closely with development and operations teams for deployment automation and infrastructure optimization

---

## Day to Day activities as a Devops Engineer?

* Monitoring production systems
* Managing CI/CD pipelines
* Infrastructure provisioning using Terraform
* Kubernetes deployment and troubleshooting
* Docker image management
* Incident handling and RCA
* Security patching and cost optimization
* Supporting developers for deployment issues

---

## C/CD Implementation Work flow on Jenkins?

1. Developer pushes code to GitHub/GitLab
2. Webhook triggers Jenkins pipeline
3. Jenkins pulls code
4. Build stage executes
5. Unit tests & code scan
6. Docker image build
7. Push image to registry
8. Deploy to Kubernetes/servers
9. Post-deployment validation
10. Monitoring & notifications

---

## What you to do if EC2 instances fail ?Where you check?

* Check EC2 instance status
* Verify CloudWatch metrics/logs
* Check Security Groups/NACL
* Verify application logs
* Check disk/memory/CPU utilization
* Verify Auto Scaling events
* Check SSH accessibility

---

## Flow of Terraform in your project?

1. Write Terraform code
2. Initialize:

```bash id="tfinit11"
terraform init
```

3. Validate:

```bash id="tfvalidate11"
terraform validate
```

4. Plan:

```bash id="tfplan11"
terraform plan
```

5. Apply:

```bash id="tfapply11"
terraform apply
```

6. Store state remotely (S3 + DynamoDB locking)

---

## Which tool you use for ticketing purpose?

* Jira
* ServiceNow

---

## What use as back-end for Aws?

* S3 bucket for Terraform backend
* DynamoDB for state locking

---

## What command you use to write the file?

```bash id="linuxfile1"
vi filename
```

OR

```bash id="linuxfile2"
nano filename
```

---

## How do you check the logs if pipeline fails?

* Jenkins Console Output
* Application logs
* Kubernetes pod logs:

```bash id="klogs11"
kubectl logs <pod-name>
```

* Docker logs:

```bash id="dlogs11"
docker logs <container-id>
```

---

## Explain IAM Roles and what policies attached?

* IAM Role provides temporary permissions to AWS services/users
* Policies define allowed actions/resources

Types:

* Managed policies
* Inline policies

Example:

* EC2 role with S3 access policy

---

## What is pod? How it is used?

* Smallest deployable unit in Kubernetes
* Contains one or more containers
* Used to run applications in cluster

---

## Monitoring Tool in Jira ?

* Jira is mainly ticketing/project management tool
* Monitoring tools integrated:

  * Prometheus
  * Grafana
  * CloudWatch
  * Datadog

---

## What is groovy ? How it is used?

* Groovy is scripting language used in Jenkins pipelines

Example:

```groovy id="groovy1"
pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        echo 'Build Started'
      }
    }
  }
}
```

---

## Modules in Terraform? Explain in details how you use in project?

* Modules are reusable Terraform code blocks

Usage:

* Separate modules for:

  * VPC
  * EC2
  * EKS
  * S3

Advantages:

* Reusability
* Standardization
* Easy maintenance

Example:

```hcl id="tfmodule11"
module "vpc" {
  source = "./modules/vpc"
}
```

---

## Explain your project Flow how you build pipelines?

1. Developer commits code
2. Git webhook triggers Jenkins
3. Build + test stages run
4. SonarQube scan
5. Docker image build
6. Push image to ECR
7. Deploy to Kubernetes
8. Monitoring and alerting configured

---

## What are the monitoring tools ? How it can be used?

* Prometheus → Metrics collection
* Grafana → Visualization
* ELK → Log analysis
* CloudWatch → AWS monitoring
* Datadog → Infra monitoring

Used for:

* Alerts
* Dashboards
* Troubleshooting
* Performance monitoring

---

## Write Terraform code for EC2, VPC,S3?

### EC2

```hcl id="ec2tf11"
resource "aws_instance" "ec2" {
  ami           = "ami-xxxx"
  instance_type = "t2.micro"
}
```

### VPC

```hcl id="vpctf11"
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

### S3

```hcl id="s3tf11"
resource "aws_s3_bucket" "bucket" {
  bucket = "my-demo-bucket"
}
```

---

## What is statefile in terraform?

* Terraform state file stores infrastructure state mapping
* Keeps track of created resources
* File:

```text id="tfstate11"
terraform.tfstate
```

* Best practice:

  * Store remotely in S3
  * Use DynamoDB locking

---
