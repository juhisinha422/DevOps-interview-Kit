# Cloud / DevOps / Infrastructure Interview Questions & Detailed Answers (~3 YOE)

---

# 1. Explain Terraform state file and why is it important?

Terraform state file is used to store the current state of infrastructure managed by Terraform. It maps real cloud resources with Terraform configuration.

File name:

```text id="b9t2pk"
terraform.tfstate
```

Why it is important:

* Tracks infrastructure resources
* Maintains resource metadata
* Detects infrastructure drift
* Helps Terraform know what to create/update/delete
* Improves execution performance

Without state file:

* Terraform cannot compare desired vs current infrastructure
* Duplicate resources may be created

Best practice:

* Store state remotely in S3
* Enable versioning
* Use locking mechanism

Example:

```hcl id="m7v1qw"
terraform {
  backend "s3" {
    bucket = "terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-east-1"
  }
}
```

---

# 2. How does state locking work using S3 + DynamoDB? What kind of conflicts can happen?

In AWS:

* S3 stores Terraform state file
* DynamoDB provides state locking

When `terraform apply` starts:

1. Terraform creates lock entry in DynamoDB
2. Other users cannot modify state simultaneously
3. After execution lock is released

This prevents:

* Concurrent terraform apply
* State corruption
* Infrastructure inconsistency

Possible conflicts:

* Two engineers running apply simultaneously
* Interrupted terraform apply leaving stale lock
* Manual state modifications

Troubleshooting stale lock:

```bash id="3w7nkp"
terraform force-unlock LOCK_ID
```

Benefits:

* Team collaboration safety
* Consistent infrastructure management

---

# 3. Difference between count and for_each in Terraform? Which one would you prefer and why?

## count

Creates resources using index numbers.

Example:

```hcl id="t6q8mw"
count = 3
```

Access:

```text id="g2v4pl"
aws_instance.app[0]
```

Problem:

* Index shifting causes unnecessary recreation

---

## for_each

Creates resources using unique keys.

Example:

```hcl id="u8n1tx"
for_each = toset(["dev", "prod"])
```

Access:

```text id="f5r9zq"
aws_instance.app["dev"]
```

Advantages:

* Stable resource mapping
* Better readability
* Easier management

Preferred:
I prefer `for_each` in production because it avoids index-related issues and provides more predictable infrastructure management.

---

# 4. Write Terraform code to create an EC2 instance.

Example:

```hcl id="d1y7mc"
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  tags = {
    Name = "DevServer"
  }
}
```

Commands:

```bash id="j7w4vm"
terraform init
terraform plan
terraform apply
```

---

# 5. In your inventory, you have 10 servers. How will you install Nginx across all of them?

I would use Ansible for centralized configuration management.

Inventory Example:

```ini id="r2v9pk"
[webservers]
server1
server2
server3
```

Playbook:

```yaml id="m8p4qt"
- hosts: webservers
  become: yes

  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
```

Run:

```bash id="y5q7dn"
ansible-playbook nginx.yml
```

Benefits:

* Automation
* Scalability
* Consistency
* Faster deployment

---

# 6. Production EC2 CPU suddenly reaches 100%. How will you troubleshoot step by step?

Step-by-step approach:

## Step 1 – Verify CPU usage

```bash id="p1t8vk"
top
htop
```

---

## Step 2 – Identify high CPU process

```bash id="m9q3wr"
ps -aux --sort=-%cpu
```

---

## Step 3 – Check application logs

```bash id="q2w6tm"
tail -f /var/log/app.log
```

---

## Step 4 – Check traffic spikes

* ALB metrics
* CloudWatch metrics
* Nginx access logs

---

## Step 5 – Verify memory and disk

```bash id="f4v1kx"
free -m
df -h
```

---

## Step 6 – Check recent deployments

* Deployment issues
* Infinite loops
* Bad queries

---

## Step 7 – Scale infrastructure if needed

* Auto Scaling
* Add instances
* Increase resources temporarily

---

## Step 8 – Root Cause Analysis

Document:

* Cause
* Impact
* Resolution
* Preventive action

---

# 7. What is Active Directory and how is it used in organizations?

Active Directory (AD) is Microsoft's centralized identity and access management service.

Used for:

* User authentication
* Authorization
* Group policy management
* Centralized access control

Components:

* Domain Controller
* Users
* Groups
* Organizational Units

Common enterprise usage:

* Single Sign-On (SSO)
* Windows authentication
* Access management

Example:

```text id="r0w4py"
Employee Login → Active Directory Authentication
```

Benefits:

* Centralized management
* Improved security
* Policy enforcement

---

# 8. What is Redis Cluster? How do replication and failover work?

Redis Cluster provides:

* Distributed caching
* High availability
* Horizontal scalability

Architecture:

* Master nodes
* Replica nodes

Replication:

* Replica copies master data

Failover:

1. Master node fails
2. Replica promoted as new master
3. Cluster continues serving traffic

Benefits:

* Fault tolerance
* Faster performance
* Distributed storage

Common use cases:

* Session caching
* Real-time analytics
* Leaderboards
* Queue systems

---

# 9. What is AWS MSK and what is your understanding of Kafka architecture?

AWS MSK (Managed Streaming for Apache Kafka) is AWS managed Kafka service.

Kafka Components:

* Producer
* Broker
* Topic
* Partition
* Consumer
* Zookeeper/KRaft

Workflow:

```text id="u7m5qw"
Producer → Kafka Broker → Topic Partition → Consumer
```

Features:

* Distributed messaging
* High throughput
* Fault tolerance
* Event streaming

Use cases:

* Log processing
* Real-time analytics
* Event-driven architecture

MSK advantages:

* Managed infrastructure
* Automated patching
* Easier scaling
* High availability

---

# 10. Difference between Datadog and Prometheus + Grafana?

| Feature       | Datadog            | Prometheus + Grafana |
| ------------- | ------------------ | -------------------- |
| Type          | SaaS Monitoring    | Open-source          |
| Setup         | Easier             | Self-managed         |
| Storage       | Managed            | Prometheus TSDB      |
| Visualization | Built-in           | Grafana              |
| Cost          | Paid               | Mostly open-source   |
| Scalability   | Managed by Datadog | User-managed         |

---

## Datadog

Advantages:

* Easier integration
* Full-stack observability
* APM support
* Managed platform

---

## Prometheus + Grafana

Advantages:

* Open-source
* Flexible
* Kubernetes-native
* Cost-effective

Preferred usage:

* Enterprise SaaS → Datadog
* Kubernetes/open-source stack → Prometheus + Grafana

---

# 11. What monitoring setup have you implemented till now?

I implemented monitoring using:

* Prometheus
* Grafana
* CloudWatch
* Loki/ELK
* Alertmanager

Metrics monitored:

* CPU
* Memory
* Disk
* Pod health
* API latency
* Error rates
* Network traffic

Alerting:

* Slack
* Email
* PagerDuty

Architecture:

```text id="o4p7tm"
Application → Prometheus → Grafana Dashboard → Alertmanager
```

---

# 12. Explain Terraform vs Ansible with real-world use cases.

| Terraform                   | Ansible                  |
| --------------------------- | ------------------------ |
| Infrastructure Provisioning | Configuration Management |
| Declarative                 | Mostly Procedural        |
| Cloud Resource Creation     | Software Installation    |
| State Management            | Agentless Automation     |

---

## Terraform Use Cases

* Create VPC
* Launch EC2
* Create EKS cluster
* Provision S3

---

## Ansible Use Cases

* Install Nginx
* Configure servers
* Patch systems
* Deploy applications

---

## Real-world Workflow

```text id="a8q3vn"
Terraform → Create Infrastructure
Ansible → Configure Infrastructure
```

Both tools complement each other.

---

# 13. Linux distribution preference — which would you choose for enterprise workloads and why?

For enterprise workloads, I generally prefer:

* RHEL
* Ubuntu LTS

---

## RHEL (Red Hat Enterprise Linux)

Advantages:

* Enterprise support
* Stability
* Security patches
* Widely used in production

Best for:

* Banking
* Enterprise environments
* Large organizations

---

## Ubuntu LTS

Advantages:

* Easy package management
* Large community
* Cloud-friendly
* Frequent updates

Best for:

* Cloud-native applications
* Kubernetes environments
* Startups

---

My preference depends on business requirements:

* Enterprise support required → RHEL
* Cloud-native/open-source ecosystem → Ubuntu LTS

---
