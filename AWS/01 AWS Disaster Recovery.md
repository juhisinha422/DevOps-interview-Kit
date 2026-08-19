# Practical AWS Disaster Recovery Strategies: EC2, EBS, and Regional Failover

Demonstrates hands-on AWS disaster recovery by walking through three realistic scenarios: EC2 instance failure, EBS data volume corruption, and a complete AWS regional outage. The instructor methodically explains key disaster recovery concepts, related terms, and then implements practical recovery steps within AWS, emphasizing automation readiness and business continuity.

## 1. Disaster Recovery Concepts and Terminology

Disaster recovery (DR) focuses on **restoring applications, infrastructure, data, and business operations after major failures that render the primary environment unavailable**. Examples of disasters include:

- Entire cloud region outages or data center failures
- Database corruption or ransomware attacks
- Major network outages, accidental data deletion
- Physical disasters like fire, flood, or power failure  
- Critical human errors

### Distinction between High Availability and Disaster Recovery

- **High Availability (HA)** manages **minor infrastructure failures** (e.g., one EC2 instance crashing while others remain active). HA ensures continued service without downtime.
- **Disaster Recovery** kicks in when the **primary environment is entirely unavailable**, such as a complete AWS region outage, requiring failover to a secondary region.

**Summary Table: HA vs. DR**

| Aspect           | High Availability                  | Disaster Recovery                          |
|------------------|----------------------------------|-------------------------------------------|
| Failure scope    | Small/infrastructure component failures | Major outages including region failure    |
| Application state | Continues running                 | Application becomes unavailable temporarily|
| Response goal    | No downtime                      | Minimize downtime and data loss            |
| Example          | One EC2 instance down but load balanced | Entire AWS region down, need failover      |

### Core Disaster Recovery Terms

| Term                      | Definition                                                                                                    | Business Impact                                 |
|---------------------------|---------------------------------------------------------------------------------------------------------------|------------------------------------------------|
| **Recovery Point Objective (RPO)** | Maximum acceptable age of data loss measured in time before disaster (how much data loss is tolerable). | If backups happen every 30 minutes, max 30 min data loss can be accepted.                             |
| **Recovery Time Objective (RTO)**   | Maximum acceptable downtime or time to restore application after disaster.                              | If RTO is set to 1 hour, application must be up within 1 hour.                                    |
| **Failover**                 | The process of switching operations from the primary environment to the DR environment after disaster.      | Switch from primary AWS region (Mumbai) to DR region (Hyderabad).                                |
| **Failback**                 | The process of reverting operations from DR environment back to the primary environment post-recovery.       | Switch back from DR region (Hyderabad) to primary (Mumbai) after resolution.                      |

**Key Insights:**

- RPO is measured in **time intervals** indicating tolerable data loss.
- RTO specifies tolerable downtime duration.
- Failover/failback enable continuity during region-level outages.

## 2. Scenario 1: EC2 Instance Failure Recovery

This scenario simulates an EC2 server crash while preserving application data integrity by separating application data on a dedicated EBS volume and using an AMI for server recovery.

### Setup Highlights:

- EC2 instance with:
  - Root EBS volume (OS and application server)
  - Separate **10 GB EBS data volume** for application data storage (e.g., order records)
- Application server runs **Nginx** with a custom `index.html` displaying data from attached EBS volume.
- Security Group configured for SSH and HTTP access.
- AMI snapshot created for the EC2 server (capturing configuration, installed software).
- Snapshot created for the data EBS volume maintaining application data backup.

### Recovery Process:

- EC2 instance is **terminated** to simulate failure.
- A **new EC2 instance is launched from the AMI**, restoring the application environment.
- The **existing application data EBS volume remains intact** because it was detached, not deleted.
- The volume is **re-attached and mounted** on the new instance.
- The application resumes showing **all current data** immediately.

**Key points:**

- Separating application data on a distinct EBS volume ensures data is preserved even if the EC2 instance is deleted.
- Creating and using AMIs allow rapid restoration of server setup.
- Attaching and mounting the preserved data volume completes recovery seamlessly.

## 3. Scenario 2: Data Volume Failure and Recovery

This scenario addresses corruption or deletion of application data on the EBS volume while the EC2 instance remains operational.

### Steps:

- Application data file (`orders.txt`) inside the mounted volume is deleted to simulate corruption.
- The corrupted volume is **detached and deleted**.
- A **new EBS volume is created from the latest snapshot** containing previously backed-up data.
- The restored volume is attached and mounted to the instance.
- Refreshing the application shows **only the data present in the snapshot** (older state before additional data was added).

**Conclusions:**

- Snapshots create restore points for EBS data volumes.
- Recovery from data corruption involves restoring a volume from snapshot and reattaching.
- Data added after the snapshot is lost, highlighting importance of snapshot frequency aligned to RPO requirements.

## 4. Scenario 3: Complete AWS Regional Failure and Cross-Region Recovery

This real-world enterprise disaster scenario covers coping with an entire AWS region outage (primary region down) and failover to a secondary AWS region.

### Process Overview:

- Primary region (Mumbai, `ap-south-1`) becomes unavailable.
- Application and data backups (AMIs and EBS snapshots) are **copied to a secondary region** (Hyderabad, `ap-south-2`) proactively.
- AMI copied to DR region is used to **launch a new EC2 instance** in Hyderabad.
- EBS volume snapshot copied to DR region is used to **create and attach the application data volume**.
- Volume is mounted on the new instance.
- Application runs in DR region showing the expected backed-up data.

**Automation Note:**  
In production, copying snapshots and AMIs to DR regions is automated and scheduled; here it is done manually for demonstration.

### Summary Table: Cross-Region Disaster Recovery Workflow

| Step                        | Description                                   | AWS Resource                        |
|-----------------------------|-----------------------------------------------|-----------------------------------|
| Backup creation             | Create snapshots of volumes and AMIs in primary region | EBS snapshots, AMIs               |
| Cross-region copy           | Copy snapshots and AMIs to secondary DR region            | Snapshot copy, AMI copy           |
| DR region deployment       | Launch EC2 from copied AMI, create volume from snapshot   | EC2 instance, EBS volume          |
| Volume attachment & mounting | Attach application data volume and mount on EC2          | Volume attach, mount command      |
| Application verification   | Confirm application availability with DR data               | Web application                   |

## 5. Additional Key Best Practices and Notes

- Splitting root volume and application data volume is critical to **protect data independently** from server failure.
- AMIs and snapshots serve as **recovery points** for infrastructure and data respectively.
- Recovery operations must ensure resource region alignment (e.g., volumes and instances in the same subnet/region).
- In practice, all snapshotting, copying, and failover steps are **automated with scripts and CI/CD pipelines** for scale and error minimization.
- Clean-up of created resources such as EC2 instances, snapshots, and AMIs is necessary after demos to avoid unwanted costs.

---

This tutorial emphasizes holistic disaster recovery by integrating theoretical knowledge of RTO, RPO, failover/failback concepts with practical AWS hands-on implementations covering server failure, volume failure, and multi-region failover scenarios. Applying these strategies supports resilient, highly available cloud applications and ensures business continuity under adverse events.
