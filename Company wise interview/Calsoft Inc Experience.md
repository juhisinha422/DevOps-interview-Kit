# 🚀 DevOps Interview Guide – Calsoft Inc Experience

---

## 1 Tell me about yourself

I am a DevOps Engineer with around 4 years of experience working on AWS and cloud-native technologies. My core focus is on automating CI/CD pipelines, managing infrastructure using Terraform, and deploying applications on Kubernetes. In my current role, I handle end-to-end deployment workflows, monitor system health using Prometheus and Grafana, and troubleshoot production issues. I also work closely with development and QA teams to ensure faster and reliable releases. My goal is to improve system reliability, automate manual processes, and optimize performance.

---

## 2 Explain your CI/CD pipeline in your project

In my project, the CI/CD pipeline starts with code commit to GitLab, which triggers the pipeline automatically. The pipeline includes stages like code checkout, build, unit testing, code quality analysis using SonarQube, Docker image build, and image scanning. After that, the image is pushed to a registry like ECR. Deployment is then performed on Kubernetes using Helm or kubectl. We also include approval stages for production deployments and rollback mechanisms in case of failure.

---

## 3 How have you used SonarQube? Why is it important?

I have integrated SonarQube in CI pipelines to perform static code analysis. It helps identify bugs, vulnerabilities, and code smells before deployment. It also enforces quality gates, which can fail the pipeline if code quality standards are not met. This ensures better code maintainability and security.

---

## 4 What tools have you used for deployment?

I have used tools like Jenkins and GitLab CI/CD for automation, Docker for containerization, Kubernetes for orchestration, and Helm for managing Kubernetes deployments. For infrastructure provisioning, I use Terraform.

---

## 5 Have you worked with Kubernetes? Explain your experience

Yes, I have worked extensively with Kubernetes for deploying and managing containerized applications. I have created deployments, services, ingress resources, and configured autoscaling using HPA. I have also handled troubleshooting issues like pod failures, networking issues, and resource constraints.

---

## 6 How do you troubleshoot:

### - Image issues in Kubernetes?

For image issues, I check if the image exists in the registry, verify image tags, and ensure proper image pull secrets are configured. I also check pod events using `kubectl describe pod` to identify errors like ImagePullBackOff.

### - CrashLoopBackOff errors?

I analyze container logs using `kubectl logs`, check resource limits, and verify application configuration. I also inspect liveness and readiness probes to ensure they are not causing restarts.

---

## 7 What will you check if an application is not responding?

I start by checking pod status and logs. Then I verify service configuration, endpoints, and ingress rules. I also check resource usage, network connectivity, and recent deployments. Monitoring tools help identify bottlenecks.

---

## 8 How do you identify if an issue is related to:

### - Database

I check database connectivity, logs, and query performance. I also verify credentials and connection limits.

### - Network

I test connectivity using tools like ping, curl, or telnet and check network policies and firewall rules.

### - End-user internet

I verify if the issue is reproducible internally and check CDN or ISP-related issues.

---

## 9 How do you confirm if the issue is network-related?

I perform connectivity tests between services, check DNS resolution, and analyze network logs. If requests fail due to timeout or unreachable errors, it indicates a network issue.

---

## 10 Explain your Terraform usage in projects

I use Terraform for Infrastructure as Code to provision and manage AWS resources. I maintain reusable modules, use remote backends like S3 for state storage, and implement environment-specific configurations.

---

## 11 What resources have you deployed using Terraform?

I have deployed resources like VPCs, subnets, EC2 instances, security groups, load balancers, EKS clusters, and S3 buckets.

---

## 12 How do you troubleshoot Terraform state file issues?

I check for state file corruption, ensure proper backend configuration, and use commands like `terraform refresh` and `terraform plan` to identify inconsistencies. Remote state backups help in recovery.

---

## 13 What if state file is not showing correct resources?

This usually indicates drift. I compare actual resources with Terraform configuration and use `terraform import` to sync missing resources into the state.

---

## 14 Where do you store secrets in your project?

Secrets are stored securely using services like AWS Secrets Manager or Kubernetes Secrets. I avoid hardcoding sensitive data in code or configuration files.

---

## 15 How is GitLab integrated with AWS?

GitLab pipelines use IAM roles or access keys to interact with AWS services. Pipelines can build images, push to ECR, and deploy applications to EKS using AWS CLI or SDK.

---

## 16 Write a simple Terraform code to deploy a resource

Example of creating an EC2 instance:

```id="k9e3yq"
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}
```

---

## 17 How are Prometheus and Grafana connected? What happens internally?

Prometheus collects metrics from applications and stores them as time-series data. Grafana connects to Prometheus as a data source and queries this data to create dashboards. Internally, Prometheus scrapes metrics at regular intervals, and Grafana visualizes them using queries, providing insights into system performance.

---

## 🚀 Final Tip

For this level:

* Focus on **real project explanation**
* Always mention **tools + troubleshooting steps**
* Show **end-to-end understanding**

---
