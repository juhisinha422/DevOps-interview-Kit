# DevOps/SRE Detailed Interview Questions & Answers (4–6 Years Experience)

---

# Linux

## What is SSH and why do we use it? Can we use it in Windows?

SSH (Secure Shell) is a secure remote login protocol used to access servers over a network. It encrypts communication between client and server, unlike Telnet which sends data in plain text. SSH is mainly used for remote server management, file transfer using SCP/SFTP, automation scripts, and secure command execution.

In Linux:

```bash
ssh user@server-ip
```

Yes, SSH can also be used in Windows. Modern Windows systems provide:

* OpenSSH client
* PowerShell SSH support
* PuTTY tool

Common SSH uses:

* Remote server login
* Git authentication
* Secure automation
* Tunneling/port forwarding

---

## Netstat vs SS

| Netstat                       | SS                            |
| ----------------------------- | ----------------------------- |
| Older command                 | Modern replacement            |
| Slower on large systems       | Faster                        |
| Reads from `/proc` filesystem | Uses kernel socket statistics |
| Deprecated in some distros    | Recommended now               |

Examples:

```bash
netstat -tulnp
```

```bash
ss -tulnp
```

Used for:

* Checking listening ports
* Active connections
* Troubleshooting network issues

---

## Port conflict, iptables, Curl

### Port Conflict

Occurs when two applications try to use same port.

Check:

```bash
ss -tulnp | grep 8080
```

Resolve by:

* Stopping existing service
* Changing application port

---

### iptables

Linux firewall utility used for:

* Allowing/blocking traffic
* NAT
* Port forwarding

Block port:

```bash
iptables -A INPUT -p tcp --dport 8080 -j DROP
```

---

### Curl

Command-line tool for sending HTTP requests.

Example:

```bash
curl https://google.com
```

Used for:

* API testing
* Connectivity checks
* SSL verification
* Health checks

---

## Nginx configuration, SSL/TLS, DNS resolution, when you run curl what happens internally

### Nginx

Nginx acts as:

* Web server
* Reverse proxy
* Load balancer

Basic flow:

```text
Client → Nginx → Backend Application
```

---

### SSL/TLS

Provides encrypted communication over HTTPS.

TLS Handshake:

1. Client Hello
2. Server Certificate
3. Key Exchange
4. Secure Session Established

Certificates usually managed using:

* Let's Encrypt
* ACM (AWS Certificate Manager)

---

## DNS Resolution Internally

When accessing:

```text
www.example.com
```

Flow:

1. Browser cache
2. OS cache
3. Recursive DNS resolver
4. Root DNS server
5. TLD server
6. Authoritative DNS server
7. Returns IP address

---

## What happens internally when you run curl?

Example:

```bash
curl https://example.com
```

Internal steps:

1. DNS resolution
2. TCP handshake
3. TLS handshake (if HTTPS)
4. HTTP request sent
5. Server processes request
6. Response returned
7. curl displays output

---

## When you run binary command like ls -la how it works internally

Steps:

1. Shell parses command
2. Shell searches binary using PATH variable
3. Executes `/bin/ls`
4. Kernel creates process using `fork()`
5. Binary loaded into memory
6. System calls used to read directory contents
7. Output printed to terminal

---

# Docker

## Docker Networking (Bridge, Host)

### Bridge Network

Default Docker network.
Containers communicate internally.

### Host Network

Container shares host networking stack.
No NAT overhead.

---

## Ways to reduce Docker image size

* Use Alpine images
* Multi-stage builds
* Remove unnecessary packages
* Use `.dockerignore`
* Minimize layers

Example:

```dockerfile
FROM golang:1.22 AS builder
RUN go build app

FROM alpine
COPY --from=builder /app .
```

---

## Multi-stage Image

Used to separate:

* Build dependencies
* Runtime environment

Benefits:

* Smaller image
* Better security
* Faster deployment

---

# Kubernetes

## How to troubleshoot CrashLoopBackOff

Steps:

1. Check logs:

```bash
kubectl logs pod-name
```

2. Describe pod:

```bash
kubectl describe pod pod-name
```

3. Verify:

* Environment variables
* Secrets
* ConfigMaps
* Resource limits
* Application startup

Common causes:

* App crash
* Wrong config
* DB connectivity
* Port conflict

---

## Events like 137,1,0 means?

| Exit Code | Meaning                   |
| --------- | ------------------------- |
| 0         | Successful exit           |
| 1         | Application/general error |
| 137       | OOMKilled (SIGKILL)       |

137 usually means:

* Memory limit exceeded

---

## What happens internally when kubectl apply -f runs?

Flow:

1. kubectl sends request to API Server
2. API Server validates YAML
3. Desired state stored in etcd
4. Scheduler selects node
5. Kubelet creates containers
6. Container runtime starts pod
7. CNI assigns IP
8. kube-proxy updates networking

---

## CNI vs kube-proxy

| CNI                       | kube-proxy                 |
| ------------------------- | -------------------------- |
| Handles pod networking    | Handles service networking |
| Assigns Pod IP            | Routes traffic             |
| Examples: Calico, Flannel | Uses iptables/ipvs         |

---

## Endpoints, Ingress, LoadBalancer, ClusterIP

### Endpoints

Maps service to pod IPs.

### ClusterIP

Internal cluster communication.

### LoadBalancer

Exposes service externally using cloud LB.

### Ingress

HTTP/HTTPS routing layer.

---

## Types of Deployment

* Rolling
* Blue-Green
* Canary
* Recreate

---

# AWS

## How request reaches service and returns back to user

Flow:

```text
User → Route53 → CloudFront/WAF → ALB → Ingress → Service → Pod → DB
```

Response travels back same path.

---

## How to protect from DDoS and bot attacks

Security layers:

* AWS Shield
* AWS WAF
* CloudFront
* Rate limiting
* CAPTCHA
* Security Groups
* NACLs

---

## Which IAM role/policy used to read secrets?

Usually:

* IAM role attached to EC2/EKS

Policies:

* SecretsManagerReadWrite
* Custom least-privilege policy

---

## Difference between Web Server and ALB

| Web Server                 | ALB                 |
| -------------------------- | ------------------- |
| Serves application content | Distributes traffic |
| Example: Nginx             | AWS managed LB      |

---

## Types of Load Balancer

AWS:

* ALB
* NLB
* GWLB
* CLB

---

## Security Group vs NACL

| Security Group | NACL         |
| -------------- | ------------ |
| Stateful       | Stateless    |
| Instance level | Subnet level |

---

## Golden Signals in SRE

* Latency
* Traffic
* Errors
* Saturation

Used for monitoring system health.

---

# Scenario Based

## How will you migrate server from AWS to Azure with minimum downtime?

Approach:

1. Build infra in Azure
2. Sync application/data
3. Configure replication
4. Test in staging
5. Reduce DNS TTL
6. Perform cutover during low traffic
7. Monitor closely
8. Rollback plan ready

---

## How will you secure DB?

* Private subnet
* Encryption at rest/in transit
* IAM authentication
* Strong passwords⁸
* Backup enabled
* Audit logging
* Restricted SG access

---

## SQS vs Redis

| SQS                   | Redis               |
| --------------------- | ------------------- |
| Managed queue service | In-memory datastore |
| Durable               | Very fast           |
| Async processing      | Caching/pub-sub     |

---

## ECS vs EKS vs Kubernetes

| ECS         | EKS         | Kubernetes               |
| ----------- | ----------- | ------------------------ |
| AWS managed | Managed K8s | Open-source orchestrator |

---

## How to configure EKS cheaper than ECS?

* Use Spot instances
* Cluster autoscaler
* Right-size nodes
* Fargate selective usage
* Efficient requests/limits

---

## If subnet is private and backend service is there how to access?

Methods:

* Bastion host
* VPN
* AWS Systems Manager Session Manager
* PrivateLink

---

## Logs are getting heavy what to do?

* Log rotation
* Retention policies
* Compression
* Centralized logging
* Archive old logs to S3

---

## How will you manage auto backup?

* Scheduled snapshots
* AWS Backup
* Lifecycle policies
* Cross-region backup

---

## How will you do VPC peering and when we use this?

VPC peering connects two VPCs privately.

Used for:

* Inter-VPC communication
* Multi-account architecture

Steps:

1. Create peering request
2. Accept request
3. Update route tables
4. Update SG/NACL

---

## How will you block ports in server?

Linux:

```bash
iptables -A INPUT -p tcp --dport 80 -j DROP
```

Cloud:

* Security Groups
* NACL

---

## How will you analyze traffic coming over system?

Tools:

```bash
tcpdump
wireshark
iftop
netstat
ss
```

Monitoring:

* VPC Flow Logs
* CloudWatch
* ELK

---

## Which WAF are you comfortable with and why?

AWS WAF because:

* Easy CloudFront/ALB integration
* Managed rules
* Bot protection
* Rate limiting
* Good AWS ecosystem integration

---

## IIS vs Nginx vs Tomcat vs Apache

| IIS                  | Nginx                    | Tomcat          | Apache                 |
| -------------------- | ------------------------ | --------------- | ---------------------- |
| Microsoft web server | Reverse proxy/web server | Java app server | Traditional web server |

---

# Terraform

## State Drift

Occurs when infrastructure changes manually outside Terraform.

Detect:

```bash
terraform plan
```

---

## Taint

Marks resource for recreation:

```bash
terraform taint aws_instance.demo
```

---

## DynamoDB in Terraform

Used for:

* State locking
* Prevent concurrent modifications

---

## Modular Terraform

Reusable Terraform code structure.

Benefits:

* Reusability
* Standardization
* Easier maintenance

---

## Terragrunt

Wrapper around Terraform used for:

* DRY code
* Managing environments
* Shared backend configs

---

## Pulumi

Infrastructure as Code using programming languages like:

* Python
* Go
* TypeScript

---

## SELinux

Security mechanism in Linux enforcing mandatory access control.

Modes:

* Enforcing
* Permissive
* Disabled

Check:

```bash
getenforce
```

---
