# DevOps Interview Preparation – 4+ Years Experience

This document contains commonly asked **DevOps interview questions and answers for 4+ years experience**.  
Topics covered:

- Disaster Recovery (RTO & RPO)
- AWS VPC Architecture
- CI/CD Pipeline Optimization
- ECS vs EKS
- AWS Lambda Concepts

---

# 1. Designing Disaster Recovery Based on RTO and RPO

## What is RTO?

**RTO (Recovery Time Objective)** is the maximum time allowed to restore an application after a failure.

Example:  
If RTO = **30 minutes**, the system must be restored within 30 minutes.

---

## What is RPO?

**RPO (Recovery Point Objective)** is the maximum acceptable amount of data loss measured in time.

Example:  
If RPO = **5 minutes**, backups or replication must ensure no more than 5 minutes of data loss.

---

## Steps to Design Disaster Recovery

### 1. Identify Business Requirements

Define acceptable RTO and RPO with stakeholders.

| Application | RTO | RPO |
|------|------|------|
| Banking Systems | Minutes | Near Zero |
| E-commerce | Minutes | Few Minutes |
| Internal Tools | Hours | Hours |

---

### 2. Choose DR Strategy

| Strategy | Description | RTO | RPO |
|------|------|------|------|
| Backup & Restore | Restore from backup | High | High |
| Pilot Light | Minimal core services running in DR | Medium | Low |
| Warm Standby | Fully functional but scaled-down environment | Low | Very Low |
| Active-Active | Multiple regions active | Near Zero | Near Zero |

---

### 3. Example AWS DR Architecture

Primary Region

- EC2
- RDS
- ALB

DR Region

- Cross-region RDS replication
- AMI backups
- Infrastructure via Terraform

Services used:

- Route53 Health Checks
- S3 Cross Region Replication
- AWS Backup
- RDS Read Replicas

---

### 4. Failover Strategy

Route53 automatically redirects traffic to the **DR region** if the primary region fails.

---

### 5. DR Testing

Best practices:

- Simulate region failures
- Validate RTO and RPO
- Perform periodic DR drills

---

# 2. How AWS VPC Works

A **VPC (Virtual Private Cloud)** is a logically isolated network where AWS resources are deployed.

---

## VPC Components

### CIDR Block

Defines the IP address range of the VPC.

Example:

```
10.0.0.0/16
```

---

### Subnets

Subnets divide the VPC into smaller networks.

Example:

Public Subnet

```
10.0.1.0/24
```

Private Subnet

```
10.0.2.0/24
```

---

### Internet Gateway

Allows resources in a **public subnet** to access the internet.

---

### NAT Gateway

Allows instances in **private subnets** to access the internet without exposing them publicly.

---

### Route Tables

Define traffic routing.

Public Route Table

```
0.0.0.0/0 → Internet Gateway
```

Private Route Table

```
0.0.0.0/0 → NAT Gateway
```

---

### Security Layers

Security Groups

- Instance-level firewall
- Stateful

Network ACL

- Subnet-level firewall
- Stateless

---

## Example Architecture

```
VPC
│
├── Public Subnet
│   ├── Application Load Balancer
│   └── Bastion Host
│
└── Private Subnet
    ├── Application Servers
    └── Database (RDS)
```

---

# 3. Optimizing CI/CD Pipeline Build Time

Optimizing pipeline performance improves development productivity and deployment speed.

---

## Techniques to Optimize CI/CD Pipelines

### 1. Dependency Caching

Cache dependencies to avoid repeated downloads.

Example (GitHub Actions):

```
actions/cache
```

---

### 2. Parallel Job Execution

Instead of sequential jobs:

```
Build → Test → Lint
```

Run them in parallel:

```
Build
Test
Lint
```

---

### 3. Incremental Builds

Build only modified components.

Example:

```
git diff
```

---

### 4. Use Lightweight Base Images

Instead of:

```
node:18
```

Use:

```
node:18-alpine
```

---

### 5. Docker Layer Caching

Bad Dockerfile:

```
COPY . .
RUN npm install
```

Optimized Dockerfile:

```
COPY package.json .
RUN npm install
COPY . .
```

---

### 6. Auto Scaling CI Runners

Use scalable runners:

- Jenkins agents
- GitHub hosted runners
- Kubernetes runners

---

### 7. Separate Pipelines

PR Pipeline

- Fast validation checks

Main Pipeline

- Full build and deployment

---

# 4. When to Choose ECS vs EKS

Both services run containerized applications.

---

## ECS (Elastic Container Service)

Choose ECS when:

- Simpler container orchestration required
- No Kubernetes expertise
- Faster deployment needed

Example architecture:

```
ECR → ECS Service → Fargate → ALB
```

Advantages:

- Simple setup
- Fully AWS managed
- Lower operational complexity

---

## EKS (Elastic Kubernetes Service)

Choose EKS when:

- Kubernetes ecosystem required
- Need Helm charts
- Need Kubernetes operators
- Multi-cloud portability

Example architecture:

```
ECR → EKS Cluster → Pods → Services → Ingress
```

Advantages:

- Kubernetes compatibility
- Highly customizable
- Supports advanced orchestration patterns

---

# 5. AWS Lambda Concepts

## What is AWS Lambda?

AWS Lambda is a **serverless compute service** that runs code without provisioning or managing servers.

It automatically handles:

- Infrastructure
- Scaling
- High availability

---

## Lambda Scaling

Lambda automatically scales based on incoming events.

Example:

```
100 requests → 100 Lambda instances
```

Concurrency controls include:

- Reserved concurrency
- Provisioned concurrency

---

## Lambda Limits

Timeout

```
Maximum 15 minutes
```

Memory

```
128 MB – 10 GB
```

Increasing memory also increases CPU allocation.

---

## Lambda Triggers

Common event sources:

- API Gateway
- S3
- DynamoDB Streams
- CloudWatch Events
- SNS
- SQS

Example workflow:

```
S3 Upload → Lambda → Image Processing
```

---

## Lambda Cold Start

Cold start occurs when:

- Lambda function runs after inactivity
- New execution environment is created

Solutions:

- Provisioned concurrency
- Keep warm strategy
- Use lightweight runtime

---

## Lambda Best Practices

- Use environment variables
- Follow IAM least privilege
- Keep deployment package small
- Use Lambda Layers for dependencies

---

## Lambda Monitoring

Monitoring tools:

- CloudWatch Logs
- CloudWatch Metrics
- AWS X-Ray
- Lambda Insights

---

# Summary

This document covered key DevOps concepts:

- Disaster Recovery design using RTO and RPO
- AWS VPC networking fundamentals
- CI/CD pipeline optimization techniques
- Differences between ECS and EKS
- AWS Lambda architecture and best practices

These topics are frequently asked in **DevOps interviews for engineers with 4+ years of experience**.
