# 🚀 DevOps Interview – Answers (4 Years Experience)

---

## 🔹 What is CIDR?

CIDR (Classless Inter-Domain Routing) is a method used for IP address allocation and routing.

* It allows flexible division of IP ranges
* Represented as: `IP address / prefix`
  Example: `192.168.1.0/24`

👉 `/24` means first 24 bits are network bits, rest are for hosts

**Why it’s used:**

* Efficient IP allocation
* Reduces wastage of IPs
* Helps in subnetting VPC

---

## 🔹 How do you design a VPC from scratch?

### Steps:

1. **Choose CIDR block**

   * Example: `10.0.0.0/16`

2. **Create Subnets**

   * Public Subnets (for ALB, Bastion Host)
   * Private Subnets (for apps, DB)

3. **Internet Gateway (IGW)**

   * Attach to VPC for internet access

4. **Route Tables**

   * Public route → IGW
   * Private route → NAT Gateway

5. **NAT Gateway**

   * Allows private instances to access internet

6. **Security Groups & NACLs**

   * Control inbound/outbound traffic

7. **Optional Components**

   * VPC Peering / Transit Gateway
   * Flow Logs for monitoring

---

## 🔹 For path-based or host-based routing, which load balancer would you choose and why?

I would use **Application Load Balancer (ALB)**.

### Why:

* Supports **Layer 7 (HTTP/HTTPS)** routing
* Enables:

  * Path-based routing (`/api`, `/app`)
  * Host-based routing (`app.example.com`)

👉 Best suited for microservices architecture

---

## 🔹 What is the difference between ALB, NLB, and CLB?

| Feature     | ALB                     | NLB                   | CLB            |
| ----------- | ----------------------- | --------------------- | -------------- |
| Layer       | Layer 7 (HTTP/HTTPS)    | Layer 4 (TCP/UDP)     | Layer 4 & 7    |
| Routing     | Path & Host-based       | No advanced routing   | Basic routing  |
| Performance | Moderate                | High (low latency)    | Moderate       |
| Use Case    | Web apps, microservices | High-performance apps | Legacy systems |

---

## 🔹 What are Terraform modules?

Terraform modules are reusable, self-contained blocks of Terraform code.

### Benefits:

* Code reusability
* Better organization
* Standardization

### Example:

* VPC module
* EKS module
* S3 module

---

## 🔹 How do you reference a module inside a resource?

You can reference module outputs using:

```hcl
module.<module_name>.<output_name>
```

### Example:

```hcl
resource "aws_instance" "example" {
  subnet_id = module.vpc.public_subnet_id
}
```

👉 Here, we are using output from VPC module

---

## 🔹 What is "source" in Terraform?

`source` defines the location of the module.

### It can be:

* Local path:

```hcl
source = "./modules/vpc"
```

* Git repository:

```hcl
source = "git::https://github.com/org/repo.git"
```

* Terraform Registry:

```hcl
source = "terraform-aws-modules/vpc/aws"
```

👉 It tells Terraform **where to fetch the module from**

---

## 🔹 What Python libraries do you commonly use in DevOps?

### Common Libraries:

* **boto3**

  * Used for AWS automation (EC2, S3, IAM)

* **requests**

  * Used for API calls (ServiceNow, monitoring tools)

* **os / sys**

  * For system-level operations

* **subprocess**

  * To run shell commands

* **json / yaml**

  * For config parsing

* **paramiko**

  * For SSH automation

### Example Use Case:

* Automating EC2 creation using boto3
* Triggering REST APIs for alerts or deployments

---

## ✅ Summary (For Interview)

* Strong understanding of networking (CIDR, VPC)
* Hands-on with AWS Load Balancers
* Good knowledge of Terraform (modules, source, outputs)
* Python scripting for automation

---
