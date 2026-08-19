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

---

## Practical / Hands-On Scenarios Covered

Three production-style failure scenarios were practiced:

1. **EC2 Instance Failure / Server Crash**
2. **EBS Volume Failure / Data Recovery**
3. **AWS Regional Disaster / Cross-Region Failover**

### Demo Setup — Application Architecture

- **Users** → hit a **Public IP / Route 53** DNS record (e.g., `app.example.com`)
- → routes to **EC2 (Ubuntu)** instance running the app
- The EC2 instance has two attached volumes:
  - **Root EBS (20 GB):** Ubuntu OS, Nginx, HTML/JS app, application config
  - **Data EBS (10 GB):** `orders.txt`, `customers.txt`, `application-data`

### Demo 1: Server (EC2) Crashed Scenario

Simulated an EC2 crash and walked through detecting the failure, restoring the instance/volumes, reconfiguring the app, validating it, and switching traffic back — this is the scenario the RTO/RPO timeline above (10:20 AM disaster → 10:50 AM recovery) is based on.

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
