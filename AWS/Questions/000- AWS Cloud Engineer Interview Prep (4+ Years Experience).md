# AWS Cloud Engineer Interview Prep (4+ Years Experience)

> At 4+ years, interviewers stop asking "What is X?" and start asking "**What would you do when X breaks in production?**"
> Every section: **Core concepts → Commands → Scenarios → Follow-up questions.**

---

## Table of Contents

1. [How to Answer (Framework)](#0-how-to-answer-framework)
2. [EC2](#1-ec2)
3. [VPC & Networking](#2-vpc--networking)
4. [IAM](#3-iam)
5. [S3](#4-s3)
6. [Linux Troubleshooting](#5-linux-troubleshooting)
7. [Load Balancing & Auto Scaling](#6-load-balancing--auto-scaling)
8. [RDS](#7-rds)
9. [Monitoring & Auditing](#8-monitoring--auditing)
10. [Troubleshooting Mindset](#9-troubleshooting-mindset)
11. [Extra Topics Expected at 4+ Years](#10-extra-topics-expected-at-4-years)
12. [Scenario Bank (Rapid Fire)](#11-scenario-bank-rapid-fire)
13. [Behavioral / STAR](#12-behavioral--star)
14. [30-Day Study Plan](#13-30-day-study-plan)
15. [Last-Day Cheat Sheet](#14-last-day-cheat-sheet)

---

## 0. How to Answer (Framework)

For every scenario question, structure your answer like this:

1. **Clarify**: Is it new or did it work before? What changed recently? Scope (one instance or all)? Any error message?
2. **Hypothesize**: List likely causes from the outside in (DNS → network → OS → app).
3. **Verify**: Name the exact command, console page, or log you'd check.
4. **Fix**: Smallest safe change. Mention change control in production.
5. **Validate**: Prove it's fixed (curl, health check, metric back to normal).
6. **Prevent**: Alarm, automation, runbook, IaC, post-incident review.

**Tip:** Always say "I'd check X **because** Y". Reasoning matters more than the answer.

---

## 1. EC2

### 1.1 Core concepts

| Topic | What to know |
|---|---|
| Instance families | General (M, T), Compute (C), Memory (R, X), Storage (I, D), GPU (P, G). T-series are **burstable** (CPU credits). |
| Purchasing | On-Demand, Reserved, Savings Plans, Spot (can be interrupted, 2-min notice), Dedicated Host/Instance |
| Storage | **EBS** (persistent, AZ-bound, snapshots to S3), **Instance Store** (ephemeral, lost on stop/terminate) |
| EBS types | gp3 (default, independent IOPS/throughput), gp2 (burst credits), io1/io2 (provisioned IOPS, DBs), st1/sc1 (HDD, throughput/cold) |
| Networking | ENI, private IP, public IP (changes on stop/start), **Elastic IP** (static), Enhanced Networking (ENA) |
| Metadata | IMDSv2 (token-based): `curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/` |
| Placement groups | Cluster (low latency), Spread (separate hardware), Partition (large distributed systems) |
| Status checks | **System status** (AWS hardware/host) vs **Instance status** (OS/network inside your instance) |
| AMI | Region-specific; copy to use in another region. Can be shared across accounts. |
| User data | Runs once at first boot by default (as root). Logs: `/var/log/cloud-init-output.log` |

### 1.2 Troubleshooting checklist

**A. Cannot connect (SSH / RDP)**

1. Instance state running? Both status checks 2/2?
2. Public IP / Elastic IP assigned? (Private subnet → need bastion/SSM)
3. **Security Group** allows port 22/3389 from your IP (inbound)?
4. **NACL** allows inbound AND outbound ephemeral ports (1024–65535)?
5. **Route table** has `0.0.0.0/0 → IGW` (public subnet)?
6. Key pair correct? File permission `chmod 400 key.pem`? Right username (`ec2-user`, `ubuntu`, `admin`)?
7. OS firewall (`iptables`, `firewalld`, Windows Firewall)? `sshd` running?
8. Disk 100% full can break SSH logins. Use **EC2 Instance Connect / SSM Session Manager / EC2 Serial Console**.
9. Last resort: stop instance → detach root volume → attach to a rescue instance → fix `/etc/ssh`, `fstab`, space → reattach.

**B. High CPU**

- CloudWatch `CPUUtilization`; for T-series check `CPUCreditBalance` (zero credits = throttling).
- On the box: `top`/`htop`, `pidstat`, identify the process. Is it the app, a runaway cron, malware/crypto-miner?
- Fix: tune/kill process, scale up/out, move T-series to unlimited or fixed-performance family, add ASG.

**C. High memory**

- CloudWatch does **not** provide memory by default → install **CloudWatch Agent**.
- `free -h`, `ps aux --sort=-%mem | head`, `dmesg | grep -i oom`, `journalctl -k | grep -i kill`.
- OOM killer events, memory leak, JVM heap sizing, missing swap.

**D. Disk space**

- `df -h` (blocks) **and** `df -i` (inodes! lots of tiny files can fill inodes with free blocks).
- `du -xh / --max-depth=1 | sort -h`, `lsof +L1` (deleted files still held by a process).
- Expanding EBS: modify volume (no downtime) → `growpart /dev/xvda 1` → `resize2fs` (ext4) / `xfs_growfs` (xfs).

**E. Application/service failures**

- `systemctl status app`, `journalctl -u app -n 100 --no-pager`, app logs.
- Port listening? `ss -tulnp`. Local test `curl localhost:PORT`. Config/env vars, dependencies (DB reachable?), permissions, disk full.

**F. Instance networking**

- `ip a`, `ip route`, `cat /etc/resolv.conf`, `dig`, `ping`/`traceroute`/`mtr`, `nc -zv host port`.
- Source/Dest check (needed OFF for NAT instances), multiple ENIs and routing, MTU issues, DNS (VPC `.2` resolver).

**G. IAM Role issues on EC2**

- `aws sts get-caller-identity` shows which identity is actually in use.
- Instance profile attached? Policy allows the action? Env vars/`~/.aws/credentials` overriding the role? IMDS reachable (hop limit =1 breaks containers)?

### 1.3 Frequently asked questions

- **Stop vs Terminate vs Hibernate?** Stop keeps EBS (no compute charge, public IP lost); Terminate deletes (root volume deleted by default via `DeleteOnTermination`); Hibernate saves RAM to EBS.
- **Instance status check failed. What now?** System check failed → stop/start to move to new host. Instance check failed → OS issue: check console output/screenshot, reboot, rescue.
- **How to migrate an instance to another AZ/Region?** Snapshot/AMI → copy → launch.
- **Reduce EC2 cost?** Right-size (Compute Optimizer), Savings Plans, Spot for stateless, schedule off hours, gp2→gp3, delete unattached EBS/old snapshots/idle EIPs.
- **EBS vs Instance Store vs EFS?** Block, persistent / ephemeral high-speed / shared NFS across AZs.
- **Secure EC2 access without opening port 22?** **SSM Session Manager** (no inbound, IAM-controlled, logged).

---

## 2. VPC & Networking

### 2.1 Core concepts

| Component | Key points |
|---|---|
| **VPC & CIDR** | Regional, e.g. `10.0.0.0/16` (65,536 IPs). Plan CIDRs to **avoid overlap** (peering, VPN, TGW need non-overlapping). AWS reserves **5 IPs per subnet** (first 4 + last). |
| **Subnet** | AZ-scoped. **Public** = route to IGW. **Private** = no IGW route. |
| **Route Table** | Longest prefix match wins. `local` route always present. |
| **Internet Gateway** | Horizontally scaled, HA, 1:1 NAT for instances with public IPs. |
| **NAT Gateway** | Managed, in a **public subnet**, needs EIP. Private subnets route `0.0.0.0/0 → NAT GW`. **Per-AZ for HA.** Outbound only. |
| **Security Group** | **Stateful**, instance/ENI level, **allow rules only**, evaluates all rules. Can reference other SGs. |
| **NACL** | **Stateless**, subnet level, allow+deny, numbered rules evaluated in order. Must allow return (ephemeral) traffic. |
| **VPC Peering** | Private connectivity between 2 VPCs (cross-account/region OK). **Non-transitive**, no overlapping CIDRs, needs routes on both sides + SG rules. |
| **Transit Gateway** | Hub-and-spoke, transitive routing for many VPCs/VPNs/DX. |
| **VPC Endpoints** | **Gateway** (S3, DynamoDB; free; route-table entry via prefix list). **Interface** (PrivateLink, ENI in subnet, paid; most other services). |
| **VPN / Direct Connect** | Site-to-Site VPN (IPSec over internet, quick). DX (dedicated, consistent latency; pair with VPN for backup). |
| **Flow Logs** | Capture ACCEPT/REJECT metadata at VPC/subnet/ENI to CW Logs/S3. Key to diagnosing SG/NACL drops. |
| **DNS** | `enableDnsSupport`, `enableDnsHostnames`; Route 53 private hosted zones; Route 53 Resolver. |
| **IPv6/Egress-only IGW** | IPv6 outbound-only for private instances. |

### 2.2 Security Group vs NACL

| | Security Group | NACL |
|---|---|---|
| Level | ENI / instance | Subnet |
| State | Stateful | Stateless |
| Rules | Allow only | Allow + Deny |
| Evaluation | All rules | In number order, first match |
| Default | Deny in, allow out | Default NACL allows all |

### 2.3 Traffic flow: **be able to draw this**

**Internet user → public EC2:**
`User → DNS → IGW → Route table → NACL (in) → SG (in) → EC2 → response → SG (stateful) → NACL (out) → IGW → User`

**Private EC2 → Internet:**
`EC2 → Private RT (0.0.0.0/0 → NAT GW) → NAT GW (public subnet, EIP) → Public RT (→ IGW) → Internet`

**Private EC2 → S3 (no internet) with Gateway Endpoint:**
`EC2 → RT (pl-xxxx S3 prefix list → vpce-xxxx) → S3` (never leaves AWS network)

### 2.4 Scenarios

**"Private EC2 cannot access S3. What will you check?"**

1. Path: NAT Gateway or **S3 Gateway Endpoint**? Is the endpoint route in the **private subnet's** route table?
2. If NAT: NAT GW in a public subnet, with a route to the IGW, and the private RT points to it? NAT GW state available?
3. SG outbound allows 443? NACL allows outbound 443 and inbound ephemeral?
4. **IAM**: instance role has `s3:GetObject/ListBucket` on correct ARNs (bucket vs `bucket/*`)? `aws sts get-caller-identity`.
5. **Endpoint policy** restricting buckets? **Bucket policy** with `aws:SourceVpce` / `aws:SourceIp` conditions or explicit Deny? KMS key permission if SSE-KMS?
6. DNS resolution OK? Region mismatch (bucket in another region + gateway endpoint is regional)?
7. Test: `aws s3 ls s3://bucket --debug`, `curl -v https://s3.<region>.amazonaws.com`, VPC Flow Logs.

**"Instance in private subnet can't reach internet."** → RT has no NAT route; NAT in private subnet by mistake; NAT lacks EIP; NACL blocks; SG egress; NAT GW in different AZ that failed.

**"Two VPCs peered but can't communicate."** → Routes missing on **both** sides, SG not allowing peer CIDR/SG, NACLs, overlapping CIDR, transitive attempt (A↔B↔C), DNS resolution over peering not enabled.

**"Design a 3-tier VPC."** → 2–3 AZs; public subnets (ALB, NAT); private app subnets; private DB subnets (no internet); SG chaining (ALB SG → App SG → DB SG); endpoints for S3/DynamoDB/SSM; Flow Logs on; NAT per AZ.

**"NAT Gateway cost is huge."** → Check data processing charges: add S3/DynamoDB **Gateway endpoints** (free), interface endpoints for ECR/SSM, keep traffic in-AZ, review what is going out (Flow Logs).

### 2.5 CLI quick hits

```bash
aws ec2 describe-vpcs
aws ec2 describe-subnets --filters Name=vpc-id,Values=vpc-xxxx
aws ec2 describe-route-tables --filters Name=association.subnet-id,Values=subnet-xxxx
aws ec2 describe-security-groups --group-ids sg-xxxx
aws ec2 describe-network-acls
aws ec2 describe-nat-gateways
aws ec2 describe-vpc-endpoints
```

**Tool:** VPC **Reachability Analyzer** tests the path between two resources and tells you the blocking component.

---

## 3. IAM

### 3.1 Core concepts

- **Users** (long-term creds), **Groups**, **Roles** (temporary creds via STS, assumed by services/users/accounts/federated identities).
- **Policy types:** Identity-based, Resource-based (bucket policy, trust policy, KMS key policy), **Permission boundaries**, **SCPs** (Organizations; set max permissions, don't grant), Session policies, VPC endpoint policies.
- **Evaluation logic:** Default **deny** → explicit **deny always wins** → explicit allow needed. For same-account, either identity OR resource policy allow is enough; for cross-account, **both** must allow.
- **Role = trust policy** (who can assume) **+ permission policy** (what they can do).
- **AssumeRole flow:** Caller → `sts:AssumeRole` → temp creds (AccessKey, Secret, SessionToken) with expiry.
- **Least privilege:** Start narrow, use IAM Access Analyzer / "last accessed" data / CloudTrail to refine. Use conditions (`aws:SourceIp`, `aws:MultiFactorAuthPresent`, `aws:PrincipalOrgID`, tags/ABAC).
- **MFA:** Enforce on root & privileged users; condition keys to require MFA for sensitive actions.
- **Root account:** MFA, no access keys, use only for tasks requiring it.
- **Federation:** IAM Identity Center (SSO), SAML/OIDC, Web Identity (IRSA for EKS).

### 3.2 Sample policies

**Least-privilege S3 read for one bucket**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    { "Effect": "Allow", "Action": ["s3:ListBucket"], "Resource": "arn:aws:s3:::my-bucket" },
    { "Effect": "Allow", "Action": ["s3:GetObject"], "Resource": "arn:aws:s3:::my-bucket/*" }
  ]
}
```

**Trust policy for EC2 role**

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": { "Service": "ec2.amazonaws.com" },
    "Action": "sts:AssumeRole"
  }]
}
```

**Cross-account AssumeRole (trust):** `"Principal": {"AWS": "arn:aws:iam::<account-B>:root"}` plus an `sts:AssumeRole` allow on the caller side.

### 3.3 Scenarios

**"EC2 needs access to S3."** → Create IAM **role** with least-privilege S3 policy → attach via **instance profile** → app uses SDK default credential chain (no keys). Benefits: auto-rotated temp creds, nothing to leak or rotate, auditable in CloudTrail.

**"Access denied. How do you debug?"**

1. `aws sts get-caller-identity`: who am I really?
2. Identity policy allows action + resource ARN? Any explicit Deny?
3. Resource policy (bucket/KMS/SQS)? Cross-account needs both sides.
4. SCP / permission boundary / session policy limiting?
5. Conditions (IP, VPCe, MFA, tags, time)?
6. KMS: does role have `kms:Decrypt`/`GenerateDataKey` AND key policy allows?
7. Tools: **IAM Policy Simulator**, **CloudTrail** (error message), **Access Analyzer** policy validation, `aws iam simulate-principal-policy`.

**"Access keys leaked on GitHub."** → Deactivate/delete key immediately → check CloudTrail for activity by that key → revoke sessions/rotate → look for new users/roles/resources (persistence) → move to roles/Secrets Manager → add git-secrets/pre-commit scanning, GuardDuty, alerts.

**"Give a third-party vendor access."** → Cross-account role with **ExternalId** (confused deputy protection), least privilege, MFA/time-bound, logged.

**"Prevent anyone from disabling CloudTrail across all accounts."** → **SCP** deny on `cloudtrail:StopLogging/DeleteTrail`.

### 3.4 Quick Q&A

- **Role vs User?** Role = temporary, assumable, no long-term creds. Prefer roles.
- **Inline vs Managed policy?** Managed is reusable/versioned; inline is tightly coupled to one principal.
- **SCP vs IAM policy?** SCP sets the guardrail (never grants); IAM grants within it.
- **Permission boundary?** Maximum permissions a user/role can have, useful for delegated admin.
- **Secrets storage?** Secrets Manager (rotation) vs SSM Parameter Store (cheaper, SecureString).

---

## 4. S3

### 4.1 Core concepts

| Topic | Notes |
|---|---|
| Consistency | Strong read-after-write consistency for all operations |
| Access control | IAM policy + **Bucket policy** + ACLs (disable via *Object Ownership: Bucket owner enforced*) + **Block Public Access** (account & bucket) |
| Encryption | SSE-S3 (default, AES-256), SSE-KMS (audit/key control, request-rate limits, KMS cost), SSE-C, client-side. Enforce TLS via `aws:SecureTransport` condition. |
| Versioning | Protects against overwrite/delete. Delete creates a delete marker. Needed for CRR. Can't be disabled, only suspended. |
| MFA Delete | Extra protection for versioned buckets |
| Object Lock | WORM (governance/compliance modes, legal hold) |
| Lifecycle | Transition (Standard → IA → Glacier) and Expire (current/noncurrent versions, abort incomplete multipart) |
| Storage classes | Standard, **Intelligent-Tiering**, Standard-IA, One Zone-IA, Glacier Instant, Glacier Flexible, Glacier Deep Archive |
| Replication | CRR / SRR, needs versioning + IAM role; not retroactive (use Batch Replication) |
| Presigned URL | Temp access to a private object using the **signer's** permissions; expires |
| Performance | 3,500 PUT / 5,500 GET per second **per prefix**; multipart upload (>100MB recommended); Transfer Acceleration; byte-range fetches |
| Events | S3 → Lambda/SQS/SNS/EventBridge |
| Static website | Prefer CloudFront + OAC with private bucket |
| Access Points / Multi-Region AP | Simplify per-app access policies |
| Logging | Server access logs (best-effort), CloudTrail data events (reliable object-level) |
| Others | S3 Inventory, Storage Lens, S3 Select, Batch Operations |

### 4.2 Sample bucket policy: enforce TLS + deny unencrypted uploads

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyInsecureTransport",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": ["arn:aws:s3:::my-bucket", "arn:aws:s3:::my-bucket/*"],
      "Condition": { "Bool": { "aws:SecureTransport": "false" } }
    }
  ]
}
```

### 4.3 CLI practice

```bash
aws s3 ls
aws s3 ls s3://my-bucket/ --recursive --human-readable --summarize
aws s3 cp file.txt s3://my-bucket/path/
aws s3 sync ./local s3://my-bucket/path/ --delete --dryrun
aws s3 rm s3://my-bucket/path/ --recursive
aws s3 presign s3://my-bucket/file.txt --expires-in 3600
aws s3api get-bucket-policy --bucket my-bucket
aws s3api put-bucket-versioning --bucket my-bucket --versioning-configuration Status=Enabled
aws s3api get-bucket-encryption --bucket my-bucket
aws s3api put-bucket-lifecycle-configuration --bucket my-bucket --lifecycle-configuration file://lifecycle.json
aws s3api list-object-versions --bucket my-bucket --prefix file.txt
aws s3api head-object --bucket my-bucket --key file.txt
```

### 4.4 Scenarios

**"403 AccessDenied on S3. Checklist?"** → Which principal? IAM policy (bucket vs object ARN) → bucket policy explicit deny/conditions (VPCe, IP, TLS) → Block Public Access → KMS key policy/permissions → Object ownership (uploaded by another account) → SCP → endpoint policy → wrong region/bucket name → presigned URL expired / signer lost permission.

**"Accidentally deleted objects."** → With versioning: remove delete marker or restore previous version. Without: gone (unless backups/replication). Prevention: versioning + MFA delete/Object Lock + lifecycle for noncurrent versions.

**"S3 cost reduction for 100 TB logs."** → Lifecycle to IA/Glacier, Intelligent-Tiering for unknown patterns, expire old data, abort incomplete multipart uploads, compress/parquet, Storage Lens to find waste.

**"Share a private file with a customer for 1 hour."** → Presigned URL (or CloudFront signed URL).

**"Make bucket content available globally with low latency, privately."** → CloudFront + Origin Access Control, bucket stays private.

**"Upload a 50 GB file reliably."** → Multipart upload (CLI does automatically), parallel parts, retry failed parts, Transfer Acceleration if far.

---

## 5. Linux

### 5.1 Command → Output → Diagnosis → Next action

| Command | What to look at | Diagnosis | Next action |
|---|---|---|---|
| `df -h` | `Use%` ≥ 90% | Disk full | `du` to locate, clean/expand |
| `df -i` | `IUse%` 100% | Inode exhaustion | Find dirs with millions of files, delete |
| `du -xh / --max-depth=1 \| sort -h` | Largest directory | Culprit (logs, /tmp, docker) | Drill deeper; rotate logs |
| `lsof +L1` | Deleted files still open | Space not released | Restart owning process |
| `top` / `htop` | `%CPU`, load average, `wa`, `st` | High `us`=app, `wa`=I/O wait, `st`=steal (noisy neighbor/T-credits) | Profile app, check disk, resize |
| `uptime` | load avg vs vCPU count | Load > cores = saturation | Find runnable/blocked processes |
| `free -h` | `available` (not "free") | Low available = pressure | Find top RSS; check OOM |
| `vmstat 1` / `iostat -xz 1` | `si/so` (swap), `%util`, `await` | Swapping / disk saturated | Tune, bigger/faster EBS |
| `ss -tulnp` | Is port LISTEN, on `0.0.0.0` or `127.0.0.1`? | App not listening / bound to localhost | Fix bind address/service |
| `ss -s` / `ss -tan state time-wait` | Connection counts | Connection leak / exhaustion | Tune, keep-alive, fix app |
| `ip a` / `ip route` | IP, default route | Missing route/IP | Fix network config |
| `curl -v localhost:8080/health` | HTTP code, timing | App up locally? | If OK locally but not remote → SG/NACL/route/LB |
| `curl -w "@fmt" -o /dev/null -s URL` | DNS/connect/TLS/TTFB times | Where latency is | Target that layer |
| `dig example.com` / `nslookup` | Answer, TTL, server | DNS failure | Check resolver/Route 53 |
| `nc -zv host 5432` | succeeded/timeout/refused | **Timeout** = firewall/route; **Refused** = no listener | Check SG or service |
| `traceroute` / `mtr` | Hop where loss starts | Network path issue | Escalate/route fix |
| `journalctl -u svc -n 100 --no-pager` / `-xe` / `-f` | Errors around failure | Root cause in logs | Fix config/dependency |
| `systemctl status svc` | active/failed, exit code | Crash/misconfig | `systemctl restart`, check unit |
| `dmesg -T \| grep -i -E "oom\|error"` | OOM kills, disk errors | Memory/hardware issue | Add memory/limit app |
| `ps aux --sort=-%cpu \| head` | Top consumers | Heavy process | Investigate/kill/limit |
| `iptables -L -n` / `firewall-cmd --list-all` | Blocking rules | OS firewall drop | Adjust rule |
| `lsblk`, `mount`, `/etc/fstab` | Volumes mounted? | Missing mount/bad fstab | Fix fstab (`nofail`) |
| `chmod`, `chown`, `ls -l`, `getenforce` | Permissions/SELinux | Permission denied | Fix perms/context |
| `tail -f /var/log/...`, `grep -i error` | Live errors | | |
| `find / -type f -size +500M 2>/dev/null` | Big files | | |
| `tcpdump -i any port 80 -nn` | Packets arriving? | Proves traffic reaches host | |

### 5.2 Useful one-liners

```bash
# Top 10 largest directories
du -xh / 2>/dev/null | sort -rh | head -10

# Biggest files over 500MB
find / -xdev -type f -size +500M -exec ls -lh {} \; 2>/dev/null

# Which process uses a port
ss -tulnp | grep :8080

# Count connections by state
ss -tan | awk '{print $1}' | sort | uniq -c

# Top 5 memory hogs
ps aux --sort=-%mem | head -6

# Check for OOM kills
journalctl -k | grep -i "killed process"

# Find IPs hitting nginx the most
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head

# Extend XFS/ext4 after EBS resize
sudo growpart /dev/nvme0n1 1
sudo xfs_growfs -d /          # XFS
sudo resize2fs /dev/nvme0n1p1 # ext4

# Truncate a huge log safely (keeps file handle)
: > /var/log/app/big.log
```

### 5.3 Also know

- systemd units, cron/`systemctl list-timers`, logrotate, `ulimit`/file descriptors (`Too many open files`), swap, SELinux, SSH hardening, `sudo`, package managers (`yum/dnf/apt`), `rsync`, `awk/sed/grep`, basic bash scripting (loops, `set -euo pipefail`, exit codes).

---

## 6. Load Balancing & Auto Scaling

### 6.1 Load balancers

| | **ALB** | **NLB** | **GWLB** | CLB |
|---|---|---|---|---|
| Layer | 7 (HTTP/HTTPS/gRPC/WebSocket) | 4 (TCP/UDP/TLS) | 3 (appliances) | Legacy |
| Routing | Path, host, header, query, method | Port-based | Transparent | |
| IP | DNS name (changing IPs) | **Static IP / EIP per AZ**, preserves source IP | | |
| Perf | Great for web/microservices | Millions of req/s, ultra-low latency | | |
| SG | Yes | Yes (now supported) | | |
| Use | Web apps, containers, Lambda targets, auth (OIDC/Cognito), WAF | Non-HTTP, static IPs, PrivateLink backends | Firewalls/IDS | |

**Concepts:** Listener (port/protocol + rules) → Rules (conditions → actions) → Target Group (instance/IP/Lambda/ALB) → Health Checks. Cross-zone LB (on by default for ALB), connection draining/**deregistration delay**, sticky sessions, SSL termination with ACM, SNI for multiple certs, `X-Forwarded-For`, access logs to S3, idle timeout (default 60s).

### 6.2 "ALB shows unhealthy targets. What could be wrong?"

1. **Health check config**: wrong path/port/protocol, wrong success codes (e.g., app returns 301/401/404), too aggressive timeout/threshold.
2. **Security Group**: target SG must allow traffic **from the ALB SG** on the app/health check port.
3. **App**: service down, not listening on that port/interface, slow startup (grace period), crashing; `curl localhost:port/health` from the instance.
4. **NACL/route/OS firewall** blocking.
5. **Wrong target group registration** (port mismatch, instance in different VPC, IP targets).
6. **Subnet/AZ**: ALB not enabled in the target's AZ.
7. **Resource exhaustion** (CPU/memory/disk) → health check timeouts.
8. Use target health "Reason code" in console: `Target.Timeout`, `Target.ResponseCodeMismatch`, `Target.FailedHealthChecks`, check ALB access logs and CloudWatch `UnHealthyHostCount`, `HTTPCode_ELB_5XX`/`HTTPCode_Target_5XX`.

**502 vs 503 vs 504**
- **502**: bad response from target (app crashed, protocol mismatch, closed connection early).
- **503**: no healthy targets / capacity.
- **504**: target didn't respond before timeout (slow app, idle timeout, SG blocked).

### 6.3 Auto Scaling

- **ASG** = Launch Template + min/desired/max + subnets (multi-AZ) + health checks (EC2 / **ELB**) + scaling policies.
- **Policies:** **Target tracking** (e.g., keep avg CPU at 50%; or ALB `RequestCountPerTarget`), Step, Simple, **Scheduled**, **Predictive**.
- **Cooldown / warm-up**, **lifecycle hooks** (run bootstrap/drain before in-service/termination), **instance refresh** (rolling AMI updates), **termination policies**, **scale-in protection**, mixed instances (On-Demand + Spot), warm pools.
- Use **ELB health checks** in the ASG so unhealthy instances are replaced.
- Stateless design: store sessions in ElastiCache/DynamoDB, files in S3/EFS, logs shipped off-box, config via user data/SSM.

**"ASG keeps launching and terminating instances (flapping)."** → Health check failing right after boot (grace period too short), bootstrap script failing, wrong AMI/launch template, missing IAM profile, thresholds/cooldown too tight, scale-out/in alarms oscillating.

### 6.4 Design: **Route 53 → ALB → Auto Scaling → EC2**

```
Users
  │
Route 53 (alias record, health checks, failover / latency / weighted routing)
  │
(Optional) CloudFront + WAF
  │
ALB (public subnets, 2+ AZs, HTTPS via ACM, SG: 80/443 from internet)
  │
Target Group (health check /health)
  │
Auto Scaling Group (private subnets, 2+ AZs, SG: app port from ALB SG only)
  │
EC2 instances (IAM role, SSM, CW agent)
  │
RDS Multi-AZ (private DB subnets, SG: 3306/5432 from App SG only)  + ElastiCache
```

Talk about: HA across AZs, security layering, scaling metric choice, zero-downtime deploys (rolling/blue-green/canary via CodeDeploy or weighted target groups), NAT per AZ, observability, DR, cost.

---

## 7. RDS

### 7.1 Core concepts

- **Engines:** MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, **Aurora** (MySQL/PG compatible, 6 copies across 3 AZs, up to 15 replicas, fast failover, storage auto-grows to 128 TiB).
- **Multi-AZ → High Availability**: synchronous standby in another AZ, automatic failover (~60–120 s), **same DNS endpoint**, standby **not** readable (classic Multi-AZ instance). *Multi-AZ DB cluster* gives 2 readable standbys.
- **Read Replica → Read scalability**: **asynchronous**, own endpoint, readable, can be cross-region, can be **promoted** (DR), replica lag possible.
- **Backups:** Automated (daily snapshot + transaction logs, **PITR** within retention 0–35 days, deleted with instance unless final snapshot) vs **Manual snapshots** (persist until deleted). Restore **always creates a new instance**.
- **Security:** private subnets, SG allowing DB port from app SG only, no public access, **encryption at rest (KMS)**: must be chosen at creation (to encrypt existing: snapshot → copy with encryption → restore), TLS in transit, IAM DB authentication, Secrets Manager rotation, parameter groups, option groups.
- **Maintenance:** maintenance windows, minor version auto-upgrade, major upgrades need planning (test with snapshot restore; **Blue/Green Deployments** for low-downtime change).
- **Storage:** gp3/io1/io2; storage autoscaling; `FreeStorageSpace`.
- **RDS Proxy:** connection pooling for Lambda/many clients, faster failover.

| | Multi-AZ | Read Replica |
|---|---|---|
| Purpose | HA / failover | Read scaling / DR |
| Replication | Sync | Async |
| Readable | No (instance deployment) | Yes |
| Endpoint | Same | Different |
| Cross-region | No | Yes |

### 7.2 "RDS has high CPU. How will you investigate?"

1. CloudWatch: `CPUUtilization`, `DatabaseConnections`, `ReadIOPS/WriteIOPS`, `DiskQueueDepth`, `FreeableMemory`, `SwapUsage`, replica lag. When did it start? Correlate with a deploy/traffic spike/batch job.
2. **Performance Insights**: top SQL, wait events, top hosts/users; **Enhanced Monitoring**: OS-level process list.
3. Slow query log / general log (parameter group) → CloudWatch Logs; `SHOW FULL PROCESSLIST` (MySQL), `pg_stat_activity` / `pg_stat_statements` (PostgreSQL).
4. Causes: missing indexes/full table scans, bad query plans, N+1 queries, connection storms (no pooling), long-running transactions/locks, undersized instance, stats outdated, batch/ETL jobs.
5. Fix: add indexes, optimize queries (`EXPLAIN`), connection pooling/RDS Proxy, add **read replicas** or cache (ElastiCache), scale up instance class, kill runaway queries, schedule heavy jobs off-peak.
6. Prevent: CPU & connection alarms, Performance Insights retained, query review in CI, load tests.

### 7.3 Other RDS scenarios

- **"App can't connect to RDS."** → SG (source = app SG), subnet group/route, public accessibility, endpoint/port, credentials, max connections, DNS, instance state, NACL, TLS requirement.
- **"Failover happened. What do apps need?"** → Use DNS endpoint (not IP), retry logic, low DNS TTL caching in app, connection pool validation.
- **"Out of storage."** → Enable storage autoscaling, purge/archive, check logs/temp tables, increase allocated storage (can't shrink).
- **"Migrate on-prem DB to AWS with minimal downtime."** → **DMS** (+ SCT for heterogeneous), full load + CDC, cutover.
- **"RPO/RTO?"** → Multi-AZ (RPO≈0), PITR (RPO ~5 min), cross-region replica/Aurora Global (RPO seconds).

---

## 8. Monitoring & Auditing

### 8.1 CloudWatch

| Feature | Notes |
|---|---|
| **Metrics** | Namespace/dimensions; EC2 basic = 5 min, detailed = 1 min; **no memory/disk by default**, use **CloudWatch Agent**. Custom metrics, high-res (1 s). |
| **Logs** | Log groups/streams, retention policy (default never expires → cost!), **Metric Filters**, **Logs Insights** queries, subscription filters → Lambda/Kinesis/OpenSearch |
| **Alarms** | OK/ALARM/INSUFFICIENT_DATA; period, evaluation periods, datapoints-to-alarm, missing data treatment; actions → SNS, Auto Scaling, EC2 actions; **composite alarms** |
| **Dashboards** | Cross-account/region views |
| **Others** | EventBridge (events/schedules), Synthetics (canaries), Container Insights, Anomaly Detection, Contributor Insights |

Logs Insights example:
```
fields @timestamp, @message
| filter @message like /ERROR/
| stats count() by bin(5m)
| sort @timestamp desc
| limit 50
```

### 8.2 CloudTrail

- Records **API calls** (who, what, when, from where, response). Console, CLI, SDK, services.
- **Management events** (default, 90-day Event history) vs **Data events** (S3 object, Lambda invoke: opt-in, costly) vs **Insights events**.
- Create an **organization trail** → S3 (encrypted, **log file validation**, MFA delete/Object Lock) + CloudWatch Logs for alerts.
- "Who deleted the security group / terminated the instance?" → CloudTrail event history filter by `EventName` (`TerminateInstances`, `DeleteSecurityGroup`) → check `userIdentity`, `sourceIPAddress`, `userAgent`.
- Alert on: root login, `ConsoleLogin` w/o MFA, IAM policy changes, security group open to 0.0.0.0/0, `StopLogging`.

### 8.3 Related services

| Service | Purpose |
|---|---|
| **AWS Config** | Resource configuration history & compliance rules (e.g., S3 public, unencrypted EBS), auto-remediation |
| **GuardDuty** | Threat detection (CloudTrail, VPC Flow, DNS logs) |
| **Security Hub** | Aggregated security findings/standards |
| **Inspector** | Vulnerability scanning (EC2, ECR, Lambda) |
| **Trusted Advisor** | Cost, security, limits, performance checks |
| **X-Ray** | Distributed tracing |
| **Systems Manager** | Session Manager, Patch Manager, Run Command, Parameter Store, Automation |
| **Cost Explorer / Budgets / CUR** | Cost visibility and alerts |
| **Health Dashboard** | AWS events affecting your resources |

**CloudWatch vs CloudTrail vs Config:** *performance & logs* vs *who did what (API)* vs *what changed & is it compliant*.

### 8.4 Scenario: "Design alerting for a web app."

- ALB: `HTTPCode_Target_5XX_Count`, `TargetResponseTime` (p95/p99), `UnHealthyHostCount`.
- EC2/ASG: CPU, memory/disk (agent), status check failed.
- RDS: CPU, connections, free storage, replica lag, `DiskQueueDepth`.
- App: error-rate metric filters, synthetic canary.
- Route alarms → SNS → PagerDuty/Slack; set severity tiers; avoid alert fatigue (use p95, multiple datapoints, composite alarms); runbook link in every alert.

---

## 9. Troubleshooting Mindset

### 9.1 The chain

```
Application → Port → OS Firewall → Security Group → NACL → Route Table → DNS → Logs → Metrics
```

Then: **Fix → Validate → Prevent recurrence.**

### 9.2 "EC2 is running but the application is unreachable."

| Step | Check | Command / Tool |
|---|---|---|
| 0 | What changed? Scope? Exact symptom (timeout vs refused vs 5xx)? | Change log, CloudTrail |
| 1 | Instance healthy? Status checks 2/2 | Console, `describe-instance-status` |
| 2 | **Application** running? | `systemctl status`, app logs |
| 3 | **Port** listening on right interface? | `ss -tulnp`, `curl localhost:PORT` |
| 4 | **OS firewall** | `iptables -L -n`, `firewall-cmd` |
| 5 | **Security Group** inbound from source | Console / CLI / Reachability Analyzer |
| 6 | **NACL** (both directions, ephemeral ports) | Console |
| 7 | **Route table** (IGW/NAT/peering/TGW) | `describe-route-tables` |
| 8 | **DNS** record, TTL, resolves to right IP? | `dig`, Route 53 |
| 9 | **Logs** | `journalctl`, CW Logs, ALB logs, Flow Logs (REJECT) |
| 10 | **Metrics** (CPU, mem, disk, network) | CloudWatch |

**Read the symptom:**
- **Connection timeout** → network layer (SG/NACL/route/IGW/public IP/OS firewall dropping).
- **Connection refused** → reached host but nothing listening (app down/wrong port/bound to 127.0.0.1).
- **HTTP 5xx** → app/upstream problem.
- **DNS NXDOMAIN** → record/zone issue.

### 9.3 "Production server at 100% disk. What do you do?"

1. **Triage (don't panic, don't `rm -rf` blindly):** confirm impact; `df -h`, `df -i`.
2. **Quick relief:** find culprit with `du -xh / --max-depth=1 | sort -h`; usual suspects: `/var/log`, `/tmp`, app logs, core dumps, docker (`docker system df`), old package caches, journal (`journalctl --vacuum-size=200M`).
3. **Safe cleanup:** compress/archive logs to S3 then delete; truncate rather than delete files held open (`: > file` or restart process; check `lsof +L1`).
4. **If can't log in:** SSM/Serial console, or attach volume to rescue instance.
5. **Expand:** modify EBS size (online) → `growpart` → `xfs_growfs`/`resize2fs`. (Note: 6-hour cooldown between modifications.)
6. **Validate:** app healthy, `df -h` OK, alarms clear.
7. **Prevent:** CloudWatch Agent disk alarms at 70/80/90%, logrotate, ship logs to CW/S3, lifecycle, separate data volume, capacity reviews, RCA documented.

### 9.4 Other classic scenarios

- **"Website is slow."** → Where? (`curl -w` timing) DNS / TLS / TTFB. CloudFront cache? ALB latency vs target latency? CPU/memory/I-O wait? DB slow queries/connections? Network (NAT bandwidth)? Recent deploy? Check dependencies (3rd-party APIs).
- **"Intermittent 504s."** → Idle timeout mismatch (LB vs app keep-alive), slow downstream, health check flapping, one bad target, connection exhaustion, SG/NACL stateless issue.
- **"Instance can't reach internet."** → Public: IGW route + public IP + SG egress + NACL. Private: NAT GW route + NAT in public subnet + EIP.
- **"Deployment succeeded but users see old version."** → CloudFront cache, DNS TTL, ASG still using old AMI/launch template version, sticky sessions, target group not updated.
- **"Suddenly huge AWS bill."** → Cost Explorer by service/usage type/linked account → NAT data processing, data transfer, forgotten resources, CloudWatch Logs ingestion, unattached EBS, compromised keys (crypto-mining) → Budgets/Anomaly Detection.

---

## 10. Extra Topics Expected at 4+ Years

### 10.1 Infrastructure as Code (Terraform / CloudFormation)

- Why IaC: repeatable, reviewable, versioned, drift detection.
- **Terraform:** providers, resources, variables/outputs, **modules**, **remote state (S3 + DynamoDB locking / S3 native locking)**, workspaces, `plan` → `apply`, `import`, `lifecycle` (`create_before_destroy`, `prevent_destroy`), `for_each` vs `count`, state drift, never edit state by hand (`terraform state mv/rm`).
- **CloudFormation:** stacks, change sets, nested stacks, StackSets, drift detection, rollback behavior, `DeletionPolicy`, intrinsic functions (`!Ref`, `!GetAtt`, `!Sub`), CDK as a higher-level option.
- Q: "Someone changed a resource manually. What now?" → Detect drift (`terraform plan` / CFN drift), decide: revert via code or codify the change; restrict console changes (SCP/IAM), enforce via pipeline.

### 10.2 CI/CD & Deployment Strategies

- Tools: CodePipeline/CodeBuild/CodeDeploy, Jenkins, GitHub Actions, GitLab CI.
- **Rolling, Blue/Green, Canary, Immutable**: know trade-offs (rollback speed, cost, risk).
- Pipeline stages: build → test → security scan → artifact (ECR/S3) → deploy (staging → approval → prod) → smoke test → auto-rollback on alarm.
- Use OIDC federation from CI to AWS (no static keys).

### 10.3 Containers (basic expectation)

- **ECS** (Fargate vs EC2 launch type), **EKS** (managed control plane, node groups, IRSA), **ECR** (image scanning).
- Docker basics: image/layers, Dockerfile best practices, `docker logs/exec/ps/stats`.
- Troubleshoot: task stops (exit code, `stoppedReason`), image pull failures (NAT/endpoint/ECR perms), health checks, OOMKilled, CrashLoopBackOff (`kubectl describe pod`, `kubectl logs --previous`).

### 10.4 Route 53 & DNS

- Record types: A, AAAA, CNAME (not at zone apex), **Alias** (free, apex OK, AWS targets), MX, TXT, NS.
- Routing policies: Simple, Weighted, Latency, Failover, Geolocation, Geoproximity, Multi-value.
- Health checks, private hosted zones, TTL & propagation, Resolver endpoints (hybrid DNS).

### 10.5 CloudFront / Edge

- Distribution, origins (S3 with OAC, ALB), behaviors, cache policies, TTL, invalidations, signed URLs/cookies, WAF, Shield, custom error pages, origin failover.

### 10.6 Serverless & Integration (know basics)

- **Lambda:** triggers, concurrency, cold starts, timeouts (15 min), VPC attachment, layers, DLQ, IAM execution role.
- **SQS** (standard vs FIFO, DLQ, visibility timeout), **SNS** (fan-out), **EventBridge**, **Step Functions**, **API Gateway**, **DynamoDB** (partition key design, RCU/WCU, on-demand vs provisioned, GSIs, DAX, TTL).

### 10.7 Security Best Practices

- Multi-account strategy (**AWS Organizations**, Control Tower, SCPs), least privilege, MFA, no root usage, encryption everywhere (KMS, TLS), **KMS** (CMK vs AWS-managed, key policies, grants, envelope encryption), Secrets Manager, WAF/Shield, GuardDuty/Security Hub/Config, private subnets, SSM instead of SSH, patching via Patch Manager, vulnerability scanning, audit logs centralized.

### 10.8 High Availability & Disaster Recovery

| Strategy | RTO/RPO | Cost |
|---|---|---|
| Backup & Restore | Hours / hours | $ |
| Pilot Light | Tens of minutes | $$ |
| Warm Standby | Minutes | $$$ |
| Multi-Site Active/Active | Near-zero | $$$$ |

- Define **RTO** (time to recover) and **RPO** (data loss tolerated).
- Multi-AZ for HA; multi-region for DR; test failover regularly; AWS Backup for centralized policies; S3 CRR; Aurora Global; Route 53 failover.

### 10.9 Cost Optimization

- Right-sizing, Savings Plans/RIs, Spot, auto scaling, schedule non-prod, gp3, S3 lifecycle/IA, delete orphaned resources, NAT/data transfer reduction, VPC endpoints, tagging + cost allocation, Budgets, Compute Optimizer, Graviton instances (~20–40% better price/performance).

### 10.10 Well-Architected Framework (6 pillars)

Operational Excellence · Security · Reliability · Performance Efficiency · Cost Optimization · Sustainability. Be ready to map your decisions to a pillar.

### 10.11 Systems Manager (very commonly used)

- **Session Manager** (shell without SSH/bastion), **Run Command**, **Patch Manager**, **Parameter Store**, **Automation runbooks**, **Inventory**. Needs: SSM Agent + instance role `AmazonSSMManagedInstanceCore` + network path (internet/NAT or VPC endpoints `ssm`, `ssmmessages`, `ec2messages`).

---

## 11. Scenario Bank (Rapid Fire)

Practice answering each in **2 minutes** using the framework in Section 0.

| # | Scenario | Key points to hit |
|---|---|---|
| 1 | EC2 is running, app unreachable | Section 9.2 chain |
| 2 | Private EC2 can't reach S3 | NAT vs Gateway endpoint, RT, IAM, bucket policy, endpoint policy, KMS |
| 3 | ALB unhealthy targets | Health check path/port/codes, SG from ALB SG, app listening |
| 4 | RDS high CPU | Performance Insights, slow queries, indexes, pooling, replicas |
| 5 | Production disk 100% | Section 9.3 |
| 6 | SSH timeout vs "Permission denied (publickey)" | Timeout = network; permission denied = key/user |
| 7 | Can't SSH, disk is full | SSM / rescue instance / serial console |
| 8 | S3 AccessDenied | IAM + bucket policy + BPA + KMS + SCP + endpoint policy |
| 9 | Auto Scaling not scaling out | Max reached, cooldown, alarm not firing/wrong metric, launch failures (capacity/quota/subnet IPs), suspended processes |
| 10 | Instances launching but failing health checks | Bootstrapping errors, SG, grace period, wrong AMI/port |
| 11 | RDS connection refused after failover | Using IP vs DNS, pool not refreshing, SG |
| 12 | Cross-account S3 access | Bucket policy (principal = other account role) + IAM policy in other account |
| 13 | Lambda in VPC can't reach internet | Needs NAT GW (Lambda ENIs have no public IP) or endpoints |
| 14 | Intermittent connection timeouts via NAT GW | Port exhaustion (55k connections per destination), scale with multiple NATs |
| 15 | Spike in 5xx after deployment | Rollback, compare versions, check logs/metrics, canary |
| 16 | High data transfer cost | Cross-AZ/region/internet egress, NAT, CloudFront, endpoints |
| 17 | Secure a public web app | CloudFront+WAF, ALB in public, app/DB private, SG chaining, TLS, least privilege, logging |
| 18 | Migrate EC2 workload with minimal downtime | AMI/replication (MGN), DNS TTL lowering, weighted routing cutover, rollback plan |
| 19 | Instance compromised | Isolate (quarantine SG), snapshot for forensics, rotate creds, CloudTrail/Flow Logs, rebuild from clean AMI, root cause |
| 20 | Kubernetes pod stuck Pending/CrashLoop | `kubectl describe`, resources/quota, image pull, probes, logs `--previous` |

---

## 12. Behavioral / STAR

Prepare **5–6 stories** from your 4+ years using **S**ituation → **T**ask → **A**ction → **R**esult (quantify!).

Stories to have ready:
1. **Major production incident** you led (timeline, RCA, prevention).
2. **Cost optimization** win (e.g., "cut monthly bill by 28%").
3. **Automation** that saved time (scripts, Terraform, pipeline).
4. **Migration / architecture** project you owned.
5. **Disagreement / tough stakeholder** and how you resolved it.
6. **Mistake you made** and what you learned (change that caused an outage → added guardrails).
7. **Mentoring / on-call improvements / documentation**.

Common questions:
- "Tell me about your current project/architecture." (Practice a 3-minute diagram walkthrough.)
- "Biggest outage you handled?"
- "How do you handle on-call and incident communication?"
- "How do you keep up with AWS?"
- "Why are you leaving your current role?" (Positive framing: growth, scale, ownership.)

---

## 13. 30-Day Study Plan

| Week | Focus | Hands-on |
|---|---|---|
| **1** | VPC, EC2, IAM, Linux troubleshooting | Build custom VPC (public/private, NAT, IGW), launch EC2 in private subnet via SSM, break SG/NACL/route on purpose and fix |
| **2** | S3, ALB/ASG, Route 53, RDS | ALB + ASG + RDS Multi-AZ 3-tier app; presigned URLs; S3 lifecycle; simulate unhealthy targets |
| **3** | CloudWatch, CloudTrail, Config, SSM, Terraform, CI/CD | Alarms + dashboards + agent (mem/disk); Terraform the whole Week 1–2 stack; pipeline |
| **4** | Containers, security, DR, cost, mock interviews | Deploy ECS/EKS app; DR design; 10 scenario answers out loud; mock interview with a friend |

**Daily routine:** 1 hr concepts + 1 hr hands-on + 30 min speaking scenarios aloud.

**Free-tier tip:** Use AWS Budgets alert ($1–5), destroy resources after labs (`terraform destroy`), watch NAT Gateways (hourly cost).

---

## 14. Last-Day Cheat Sheet

**Numbers to remember**
- Subnet reserves **5 IPs**; VPC CIDR /16 to /28.
- SG stateful / NACL stateless.
- S3: 5,500 GET & 3,500 PUT per prefix/s; max object 5 TB; single PUT 5 GB; multipart for >100 MB.
- RDS backup retention 0–35 days; Multi-AZ failover ~1–2 min.
- CloudTrail event history: 90 days.
- Lambda max 15 min.
- ALB idle timeout 60 s default.
- EBS: gp3 baseline 3,000 IOPS / 125 MB/s.
- NAT GW: up to 55,000 simultaneous connections per destination; scales to 100 Gbps.

**One-liners**
- EC2 → S3 access? **IAM Role**, never hard-coded keys.
- Multi-AZ = HA; Read Replica = scale reads.
- CloudTrail = *who did it*; CloudWatch = *how is it performing*; Config = *what changed*.
- Timeout = network; Refused = no listener; 5xx = app.
- Private subnet → internet = **NAT GW**; → S3/DynamoDB = **Gateway Endpoint**.
- Peering is **non-transitive**; use **Transit Gateway** for hub-and-spoke.
- Explicit Deny always wins.
- Don't change things randomly: **Observe → Hypothesize → Verify → Fix → Validate → Prevent.**

**Final advice**
- Draw diagrams while answering (VPC flow, 3-tier).
- Say *trade-offs* ("I'd choose X because Y, but the downside is Z").
- If you don't know, reason from first principles and say how you'd find out.
- Mention **security, cost, and reliability** unprompted. That separates senior from junior answers.

Good luck! 🚀
