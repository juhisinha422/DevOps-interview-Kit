# DevOps Interview Preparation – 4+ Years Experience

This document contains commonly asked **DevOps interview questions and answers** for professionals with **4+ years of experience**.  
Each section includes a **short explanation and an “Interview Line”** you can use while answering in interviews.

Topics Covered:

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

**RPO (Recovery Point Objective)** is the maximum acceptable data loss measured in time.

Example:  
If RPO = **5 minutes**, the system should not lose more than 5 minutes of data.

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
| Backup & Restore | Restore from backup storage | High | High |
| Pilot Light | Minimal infrastructure in DR | Medium | Low |
| Warm Standby | Scaled-down active environment | Low | Very Low |
| Active-Active | Multiple regions active | Near Zero | Near Zero |

---

### 3. Example AWS DR Architecture

Primary Region

- EC2
- RDS
- Application Load Balancer

DR Region

- RDS cross-region replication
- AMI backups
- Infrastructure via Terraform

AWS services used:

- Route53 Health Checks
- S3 Cross Region Replication
- AWS Backup
- RDS Read Replicas

---

### 4. Failover Strategy

If the primary region fails, **Route53 health checks automatically redirect traffic to the DR region**.

---

### Interview Line

> When designing Disaster Recovery, I first define RTO and RPO with stakeholders and then choose the appropriate DR strategy such as backup-restore, pilot light, warm standby, or active-active. In AWS, I typically implement this using cross-region replication, automated backups, and Route53 failover routing.

---

# 2. How AWS VPC Works

A **VPC (Virtual Private Cloud)** is a logically isolated network in AWS where cloud resources are deployed securely.

---

## Key Components

### CIDR Block

Defines the IP range of the VPC.

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

Allows resources in **public subnets** to communicate with the internet.

---

### NAT Gateway

Allows instances in **private subnets** to access the internet for updates without exposing them publicly.

---

### Route Tables

Define traffic routing rules.

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

Network ACLs

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

### Interview Line

> AWS VPC provides network isolation in the cloud. I design multi-tier architectures using public and private subnets, route tables, NAT gateways, and security groups to ensure secure communication between application and database layers.

---

# 3. Optimizing CI/CD Pipeline Build Time

Improving CI/CD performance helps teams deploy faster and increase developer productivity.

---

## Techniques to Optimize CI/CD Pipelines

### 1. Dependency Caching

Avoid downloading dependencies repeatedly.

Example:

```
actions/cache
```

---

### 2. Parallel Jobs

Instead of sequential execution:

```
Build → Test → Lint
```

Run jobs in parallel:

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

### 4. Lightweight Docker Images

Instead of:

```
node:18
```

Use smaller images:

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

### Interview Line

> To optimize CI/CD pipelines, I focus on dependency caching, Docker layer caching, parallel job execution, and lightweight container images. I also use scalable runners to reduce overall build and deployment time.

---

# 4. When to Choose ECS vs EKS

Both services are used to run containerized applications.

---

## ECS (Elastic Container Service)

Use ECS when:

- Simpler container orchestration is required
- Kubernetes expertise is not needed
- Faster setup is preferred

Example Architecture

```
ECR → ECS Service → Fargate → ALB
```

Advantages:

- Simple setup
- Fully managed
- Less operational overhead

---

## EKS (Elastic Kubernetes Service)

Use EKS when:

- Kubernetes ecosystem is required
- Helm charts or operators are needed
- Multi-cloud portability is important

Example Architecture

```
ECR → EKS Cluster → Pods → Services → Ingress
```

Advantages:

- Kubernetes compatibility
- Highly flexible
- Advanced orchestration capabilities

---

### Interview Line

> I choose ECS for simpler container workloads with minimal operational overhead, whereas I use EKS when Kubernetes features like Helm, operators, or multi-cloud portability are required.

---

# 5. AWS Lambda Concepts

## What is AWS Lambda?

AWS Lambda is a **serverless compute service** that runs code without managing servers.

It automatically handles:

- Infrastructure
- Scaling
- Availability

---

## Lambda Scaling

Lambda automatically scales depending on the number of requests.

Example:

```
100 requests → 100 Lambda instances
```

Concurrency control options:

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

Common triggers include:

- API Gateway
- S3 events
- DynamoDB Streams
- CloudWatch Events
- SNS
- SQS

Example Workflow

```
S3 Upload → Lambda → Image Processing
```

---

## Lambda Cold Start

Cold start occurs when a new Lambda execution environment is created.

Solutions:

- Provisioned concurrency
- Lightweight runtime
- Keep warm strategy

---

## Monitoring

Lambda monitoring tools include:

- CloudWatch Logs
- CloudWatch Metrics
- AWS X-Ray
- Lambda Insights

---

### Interview Line

> AWS Lambda is ideal for event-driven architectures. I have used it with services like API Gateway, S3, and SQS to build scalable serverless workflows while managing concurrency, monitoring, and cold start optimizations.

---

# Conclusion

This guide covers key DevOps interview topics for professionals with **4+ years of experience**:

- Disaster Recovery strategies using RTO and RPO
- AWS VPC networking architecture
- CI/CD pipeline optimization techniques
- ECS vs EKS container orchestration
- AWS Lambda serverless architecture

These topics are frequently asked in **DevOps and Cloud engineering interviews**.
