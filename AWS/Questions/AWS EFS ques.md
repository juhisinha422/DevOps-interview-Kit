# Top 20 Real-Time AWS EFS Interview Questions 🚀

Preparing for Cloud / DevOps / AWS interviews?
Here are the most asked real-time interview questions on Amazon Web Services EFS (Elastic File System) 👇

---

## 1️⃣ What is EFS in AWS?

* EFS (Elastic File System) is a fully managed shared file storage service in AWS
* Supports multiple EC2 instances simultaneously
* Uses NFS protocol

---

## 2️⃣ Difference between EFS and EBS?

| EFS                    | EBS               |
| ---------------------- | ----------------- |
| Shared file storage    | Block storage     |
| Multiple EC2 access    | Single EC2 mostly |
| Uses NFS               | Attached as disk  |
| Scalable automatically | Fixed size        |

---

## 3️⃣ Difference between EFS and S3?

| EFS                  | S3               |
| -------------------- | ---------------- |
| File storage         | Object storage   |
| Mountable filesystem | API-based access |
| Low latency          | High scalability |
| NFS protocol         | HTTP/HTTPS       |

---

## 4️⃣ Which protocol does EFS use?

* NFSv4 (Network File System version 4)

---

## 5️⃣ Can multiple EC2 instances access the same EFS simultaneously?

* Yes
* EFS supports concurrent access from multiple EC2 instances

---

## 6️⃣ What are EFS Mount Targets?

* Network endpoints created inside subnets
* EC2 instances connect to EFS using mount targets

---

## 7️⃣ In which VPC components is EFS deployed?

* VPC
* Subnets
* Security Groups
* Mount Targets

---

## 8️⃣ How do you mount EFS on Linux EC2 instances?

```bash id="efs1"
sudo mount -t nfs4 fs-xxxx:/ /mnt/efs
```

---

## 9️⃣ What security mechanisms are used in EFS?

* Security Groups
* IAM policies
* Encryption at rest
* Encryption in transit

---

## 🔟 What is the use of Security Groups in EFS?

* Control inbound/outbound NFS traffic
* Usually allow port 2049

---

## 1️⃣1️⃣ Explain EFS Performance Modes.

| Mode            | Use Case                  |
| --------------- | ------------------------- |
| General Purpose | Low latency apps          |
| Max I/O         | High throughput workloads |

---

## 1️⃣2️⃣ Explain Throughput Modes in EFS.

* Bursting Throughput
* Provisioned Throughput
* Elastic Throughput

---

## 1️⃣3️⃣ What is Bursting Throughput in EFS?

* Throughput scales automatically based on file system size

---

## 1️⃣4️⃣ How does EFS achieve high availability?

* Data replicated across multiple Availability Zones automatically

---

## 1️⃣5️⃣ Can EFS be accessed across Availability Zones?

* Yes
* Multiple EC2 instances across AZs can access same EFS

---

## 1️⃣6️⃣ What is lifecycle management in EFS?

* Automatically moves infrequently accessed files to lower-cost storage class

---

## 1️⃣7️⃣ Difference between Standard and Infrequent Access storage classes in EFS?

| Standard                 | Infrequent Access    |
| ------------------------ | -------------------- |
| Frequently accessed data | Rarely accessed data |
| Higher cost              | Lower cost           |

---

## 1️⃣8️⃣ How do you troubleshoot EFS mount failures?

* Check Security Group (2049)
* Verify mount target exists
* Check NFS packages installed
* Verify DNS resolution
* Check subnet routing

---

## 1️⃣9️⃣ What are common EFS use cases in production?

* Shared application storage
* Kubernetes persistent volumes
* Content management systems
* Shared web server files

---

## 2️⃣0️⃣ Why would you choose EFS over EBS in real-time projects?

* Shared storage requirement
* Multi-instance access
* Auto scaling storage
* Managed file system

---

# 💡 Bonus Scenario-Based Questions

---

## ✅ Your EFS mount is hanging — how will you troubleshoot?

* Check network connectivity
* Verify Security Group port 2049
* Check mount target availability
* Verify DNS resolution
* Check NFS client packages

---

## ✅ Application latency increased after mounting EFS — possible reasons?

* Network latency
* High IOPS demand
* Wrong performance mode
* Small throughput limits

---

## ✅ How would you share files between multiple EC2 instances in Auto Scaling?

* Use shared EFS mount across all instances

---

## ✅ How do you secure EFS for production workloads?

* Enable encryption
* Restrict Security Groups
* Use IAM authorization
* Enable backups
* Monitor access logs

---

# 📌 Pro Tip

For interviews, always remember:

👉 EBS = Single instance block storage
👉 EFS = Shared scalable file storage
👉 S3 = Object storage

---
