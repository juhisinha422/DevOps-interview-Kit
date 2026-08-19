# Disaster Recovery (DR) — Notes

## What is Disaster Recovery?

**Disaster Recovery (DR)** is the process of restoring applications, infrastructure, data, and business operations after a major failure makes the primary environment unavailable.

### Examples of Disasters
1. Entire cloud region outage
2. Data-centre failure
3. Database corruption
4. Ransomware attack
5. Major network outage
6. Accidental deletion
7. Fire, flood, or power failure
8. Critical human error

---

## High Availability (HA) vs Disaster Recovery (DR)

| | Purpose |
|---|---|
| **HA** | Keep the application running during *normal* component failures (e.g., one server in a pool dies, traffic shifts to the healthy one). |
| **DR** | Recover the application after a *major* disaster (e.g., an entire region goes down). |

**HA example:** A Load Balancer sits in front of two EC2 instances (`EC2-1`, `EC2-2`). If one instance crashes, the load balancer simply routes traffic to the surviving instance — no "disaster" event, just normal failure handling.

**DR example:** Primary Region fails entirely (❌) → DR process kicks in → traffic/workload moves to a Secondary Region (✅). This is a full failover, not just rerouting within a pool.

**Summary Table: HA vs DR**

| Aspect | High Availability | Disaster Recovery |
|---|---|---|
| Failure scope | Small/infrastructure component failures | Major outages including region failure |
| Application state | Continues running | Application becomes unavailable temporarily |
| Response goal | No downtime | Minimize downtime and data loss |
| Example | One EC2 instance down but load balanced | Entire AWS region down, need failover |

---

## RPO — Recovery Point Objective

**Definition:** The maximum acceptable amount of **data loss**, measured in time. It answers: *"How much data can we afford to lose?"*

**Example walkthrough:**
- `10:00 AM` → Snapshot taken (captures orders **1001, 1002, 1003**)
- `10:05 AM` → Order 1004 placed
- `10:10 AM` → Order 1005 placed
- `10:15 AM` → Order 1006 placed
- `10:20 AM` → **Disaster strikes**

Since the last snapshot was at 10:00 AM and the disaster happened at 10:20 AM, orders 1004–1006 (created *after* the snapshot) are lost — that's a **20-minute data gap**.

- **Actual data loss in this scenario:** 20 minutes
- **Defined RPO target:** 30 minutes
- **Result:** 20 min ≤ 30 min → **RPO requirement met** ✅ (snapshots need to be taken at least every 30 minutes to satisfy this RPO)

---

## RTO — Recovery Time Objective

**Definition:** The maximum acceptable amount of **downtime**. It answers: *"How long can the application be down before recovery?"*

**Example walkthrough (continuing the same incident):**
- `10:20 AM` — Disaster happens, application goes **DOWN** ❌
  - Detect the failure
  - Start DR process
  - Restore EC2
  - Restore EBS
  - Configure application
  - Validate application
  - Switch traffic
- `10:50 AM` — Application is **UP** ✅

**Recovery took:** 30 minutes (10:20 → 10:50)

- **Allowed RTO:** 60 minutes
- **Actual recovery:** 30 minutes
- **Result:** 30 ≤ 60 → **RTO requirement met** ✅

---

## Failover and Failback

- **Failover** = Primary → DR (switching operations to the disaster recovery environment when the primary fails)
- **Failback** = DR → Primary (switching operations back to the primary environment once it's restored/healthy again)

### Core DR Terms — Quick Reference

| Term | Definition | Business Impact |
|---|---|---|
| **RPO (Recovery Point Objective)** | Maximum acceptable age of data loss, measured in time before the disaster. | If backups happen every 30 min, max 30 min of data loss is acceptable. |
| **RTO (Recovery Time Objective)** | Maximum acceptable downtime / time to restore the application after a disaster. | If RTO is 1 hour, the application must be back up within 1 hour. |
| **Failover** | Switching operations from the primary environment to the DR environment after a disaster. | Switch from primary region (Mumbai) to DR region (Hyderabad). |
| **Failback** | Reverting operations from the DR environment back to the primary environment post-recovery. | Switch back from DR region (Hyderabad) to primary (Mumbai) after the issue is resolved. |

---

## Practical / Hands-On Scenarios Covered

Three realistic scenarios were walked through end-to-end in AWS, moving from a small-scope failure to a full regional disaster:

1. **EC2 Instance Failure / Server Crash Recovery**
2. **EBS Data Volume Failure / Corruption Recovery**
3. **AWS Regional Disaster / Cross-Region Failover**

### Demo Setup — Application Architecture

- **Users** → hit a **Public IP / Route 53** DNS record (e.g., `app.example.com`)
- → routes to **EC2 (Ubuntu)** instance running the app
- The EC2 instance has two attached volumes:
  - **Root EBS (20 GB):** Ubuntu OS, Nginx, HTML/JS app, application config
  - **Data EBS (10 GB):** `orders.txt`, `customers.txt`, `application-data`

### Demo 1: EC2 Instance Failure / Server Crash Recovery

Simulates a full EC2 server crash while keeping application data safe, by separating app data onto its own EBS volume and using an AMI to rebuild the server.

**Setup:**
- EC2 instance with a **root EBS volume** (OS + application server) and a separate **10 GB data EBS volume** (application data, e.g. order records)
- **Nginx** serving a custom `index.html` that displays data pulled from the attached data volume
- **Security Group** configured for SSH and HTTP access
- An **AMI snapshot** taken of the EC2 server (captures OS config + installed software)
- A **snapshot** taken of the data volume as a backup

**Recovery steps:**
1. EC2 instance is **terminated** to simulate the failure.
2. A **new EC2 instance is launched from the saved AMI**, restoring the server environment (OS, Nginx, app code, config).
3. The application **data volume is untouched** (it was detached, not deleted), so no data is lost.
4. The data volume is **re-attached and mounted** on the new instance.
5. The application comes back up showing **all current data immediately** — this is the scenario the RTO/RPO timeline above (10:20 AM disaster → 10:50 AM recovery) is based on.

**Key takeaway:** Keeping app data on a *separate* EBS volume from the OS/root volume means the data survives even if the EC2 instance itself is destroyed. AMIs let you rebuild the server quickly; reattaching the data volume completes the recovery.

### Demo 2: EBS Data Volume Failure / Corruption Recovery

Simulates corruption or accidental deletion of application data on the EBS volume, while the EC2 instance itself keeps running fine.

**Steps:**
1. The application data file (`orders.txt`) on the mounted volume is **deleted** to simulate corruption/data loss.
2. The corrupted volume is **detached and deleted**.
3. A **new EBS volume is created from the latest snapshot** (the backup taken earlier).
4. The restored volume is **attached and mounted** to the running instance.
5. Refreshing the application shows **only the data present at snapshot time** — anything added after the snapshot was taken is gone.

**Key takeaway:** Snapshots are restore points for EBS volumes. Recovering from data corruption means restoring a volume from the most recent snapshot and reattaching it. Data created *after* that snapshot is permanently lost, which is exactly why **snapshot frequency should be aligned to your RPO target** (see RPO section above).

### Demo 3: Complete AWS Regional Failure / Cross-Region Failover

The full enterprise-style scenario: the entire primary AWS region goes down, and the workload has to fail over to a secondary region.

**Process:**
1. Primary region (**Mumbai**, `ap-south-1`) becomes unavailable.
2. AMIs and EBS snapshots had already been **proactively copied to the secondary region** (**Hyderabad**, `ap-south-2`) ahead of the disaster.
3. The copied AMI is used to **launch a new EC2 instance** in Hyderabad.
4. The copied EBS snapshot is used to **create a new data volume** in Hyderabad.
5. The new data volume is **attached and mounted** to the new instance.
6. The application comes up in the DR region, serving the expected backed-up data.

**Automation note:** In this demo the copying was done manually for clarity. In real production environments, copying snapshots/AMIs to the DR region — and the whole failover — is **automated and scheduled** (e.g., via scripts or CI/CD pipelines) rather than performed by hand.

**Summary Table: Cross-Region DR Workflow**

| Step | Description | AWS Resource |
|---|---|---|
| Backup creation | Create snapshots of volumes and AMIs in the primary region | EBS snapshots, AMIs |
| Cross-region copy | Copy snapshots and AMIs to the secondary DR region | Snapshot copy, AMI copy |
| DR region deployment | Launch EC2 from the copied AMI, create a volume from the copied snapshot | EC2 instance, EBS volume |
| Volume attachment & mounting | Attach the application data volume and mount it on the EC2 instance | Volume attach, mount command |
| Application verification | Confirm the application is up and serving the DR data | Web application |

---

## Additional Best Practices

- **Split root volume and application data volume** — this is critical to protect data independently from server failure (this is the pattern all three demos rely on).
- **AMIs and snapshots are your recovery points** — AMIs recover the server/infrastructure, snapshots recover the data.
- **Keep resources region-aligned** — volumes and instances being restored must be in the same region/subnet as each other.
- **Automate in production** — snapshotting, cross-region copying, and failover steps should be automated via scripts/CI-CD pipelines for reliability and speed at scale; doing it manually (as in the demos) doesn't scale and is error-prone.
- **Clean up after testing** — remove EC2 instances, snapshots, and AMIs created during DR drills/demos to avoid unnecessary ongoing costs.

---

## AWS Multi-Region Disaster Recovery Architecture

**Overall pattern:** Region Failover, Server Crash Recovery, and Data Loss Recovery, tied together under **Amazon Route 53** for health checks and failover routing.

**Primary Region — `ap-south-1` (Mumbai)**
- EC2 App Servers
- RDS Primary DB
- S3 App Data
- EBS Snapshots (crash recovery)
- Behavior: continuous replication & automated snapshot schedule

**⇒ Failover ⇒**

**DR Region — `ap-south-2` (Hyderabad)**
- EC2 Standby Servers
- RDS Read Replica
- S3 Cross-Region Replica
- Restored Volumes (data recovery)
- Behavior: promoted on failover / point-in-time restore

**Recovery objectives guide the strategy:**
- **RTO** → Max acceptable downtime
- **RPO** → Max acceptable data loss
- **DR Strategy spectrum** (increasing cost/complexity, decreasing RTO/RPO):
  `Backup/Restore → Pilot Light → Warm Standby → Multi-Site`

### DR Strategy Spectrum — Explained

| Strategy | What it means | Typical RTO | Typical RPO | Cost |
|---|---|---|---|---|
| **Backup & Restore** | Data is backed up regularly (snapshots/backups) to another region/account. Nothing is running in the DR region until a disaster happens — infrastructure is provisioned and data restored on demand. | Hours | Hours | Lowest |
| **Pilot Light** | A minimal version of the core infrastructure (e.g., DB replica) always runs in the DR region, but app/compute servers are kept off. On failover, you scale up the rest of the stack around this "pilot light." | Tens of minutes | Minutes | Low–Medium |
| **Warm Standby** | A scaled-down but fully functional copy of the production environment runs continuously in the DR region. On failover, it's scaled up to handle full production load. | Minutes | Seconds–minutes | Medium–High |
| **Multi-Site (Hot/Hot)** | Full production-scale environments run simultaneously in both regions, actively serving traffic (active-active). Failover is near-instant since the DR region is already live. | Near-zero | Near-zero | Highest |

**Rule of thumb:** As you move right along the spectrum, RTO and RPO shrink (faster, less lossy recovery) but cost and operational complexity increase — reinforcing the doc's key takeaway that the DR strategy should be chosen based on what the business can tolerate, not the other way around.

---

## Key Takeaway

Choosing a Disaster Recovery plan is fundamentally about **balancing budget against acceptable downtime and data loss**:

- **Full duplicate systems running 24/7** → fastest recovery, but most expensive.
- **Simple backups** → cheapest, but slower to restore.
- **The business's tolerance for downtime/data loss (RTO/RPO) should drive the DR setup — not the other way around.**

### Hands-on session summary
Completed a practical AWS Disaster Recovery session covering 3 production-style failure scenarios:
- **Region Failover** — orchestrating failover from a primary region (Mumbai) to a secondary DR region (Hyderabad), validating cross-region routing and data availability
- **Server Crash Recovery** — restoring compute instances post-failure while minimizing service disruption
- **Data Loss Recovery** — executing point-in-time restoration workflows mapped to defined RTO and RPO targets
