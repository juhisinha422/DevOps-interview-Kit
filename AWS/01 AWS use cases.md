# ☁️ AWS Use Cases (2026) – SRE / DevOps Guide

---

## 1) AWS Internet Gateway vs NAT Gateway – When to Use What?

↳ https://lnkd.in/giixyN4E

**Internet Gateway (IGW):**

* Enables communication between instances in VPC and the internet
* Used with public subnets
* Instances must have public IP

**NAT Gateway:**

* Allows instances in private subnets to access the internet
* Prevents inbound internet traffic
* Used for secure outbound connectivity

**When to Use:**

* IGW → Public-facing apps (web servers, ALB)
* NAT Gateway → Private backend services (DB, app servers)

---

## 2) AWS VPC Peering Overview

↳ https://lnkd.in/g7RdM54v

* Connects two VPCs privately using AWS network
* No internet gateway or VPN required
* Works across regions and accounts

**Use Cases:**

* Microservices communication
* Multi-account architecture

**Limitations:**

* No transitive peering
* No overlapping CIDR

---

## 3) Hexagonal Architecture in AWS

↳ https://lnkd.in/gwJ3UmYm

* Also called Ports & Adapters architecture
* Separates core business logic from external systems

**AWS Mapping:**

* Core → Lambda / ECS
* Adapters → API Gateway, SQS, DynamoDB

**Benefits:**

* Loose coupling
* High testability
* Easy to replace components

---

## 4) Cloud Scaling Patterns Breakdown

↳ https://lnkd.in/gCpXKHPC

**Types:**

* Vertical scaling → Increase instance size
* Horizontal scaling → Add/remove instances

**Patterns:**

* Reactive scaling (CPU-based)
* Scheduled scaling
* Predictive scaling

**Best Practice:**

* Stateless apps + Load Balancer + Auto Scaling

---

## 5) Cloud Disaster Recovery Strategies

↳ https://lnkd.in/gTgjkNm9

**Strategies:**

* Backup & Restore → Low cost, high RTO
* Pilot Light → Minimal setup running
* Warm Standby → Scaled-down environment
* Multi-site Active/Active → High availability

**Key Metrics:**

* RTO (Recovery Time Objective)
* RPO (Recovery Point Objective)

---

## 6) AWS VPC Network Segmentation Break Down

↳ https://lnkd.in/grtAeerp

**Segmentation Layers:**

* Public Subnet → Internet-facing components
* Private Subnet → Application layer
* Isolated Subnet → Databases

**Security Controls:**

* Security Groups (instance level)
* NACLs (subnet level)
* Route tables for traffic control

---

## 7) CloudFront Signed URL vs S3 Pre Signed URL - When to Use What?

↳ https://lnkd.in/g2ypcPeA

**S3 Pre-Signed URL:**

* Direct access to S3 object
* Temporary access for upload/download

**CloudFront Signed URL:**

* Access via CDN
* Better performance + caching
* Used for secure content delivery

**When to Use:**

* S3 → Direct file access
* CloudFront → Secure, scalable content distribution

---

## 🚀 SRE Tips (Important for Interviews)

* Focus on **real use cases**, not definitions
* Explain **why + when**, not just what
* Always mention **security + scalability + cost trade-offs**

---
