# Linux, Networking, System Administration, AWS & Monitoring Interview Questions (4–6 Years Experience)

## 1. Difference between a Hard Link and a Soft Link?

### Answer

A **hard link** is another directory entry that points to the same inode as the original file. Both files share the same inode and data blocks. If the original file is deleted, the data remains accessible through the hard link until all hard links are removed. Hard links cannot be created across different file systems and cannot be created for directories.

A **soft link (symbolic link)** is a special file that stores the path to another file. It has its own inode number and acts like a shortcut. If the original file is deleted or moved, the symbolic link becomes broken. Soft links can be created across different file systems and can point to both files and directories.

### Commands

```bash
ln file1 hardlink
ln -s file1 softlink

ls -li
```

---

# 2. How do you check which process is using the most memory/CPU right now?

## Answer

The first command I use is `top` or `htop` because they provide a real-time view of CPU, memory, and process usage.

```bash
top
```

Inside `top`:

- Press `Shift + P` → Sort by CPU usage
- Press `Shift + M` → Sort by Memory usage

Using `ps`:

```bash
ps -eo pid,user,%cpu,%mem,cmd --sort=-%cpu | head
ps -eo pid,user,%mem,%cpu,cmd --sort=-%mem | head
```

Other monitoring commands:

```bash
htop
vmstat 1
iostat -x 1
sar
```

---

# 3. What does chmod 754 actually set?

## Answer

`chmod 754 file`

Permission breakdown:

```
7 = rwx = Owner
5 = r-x = Group
4 = r-- = Others
```

Result:

| User | Permission |
|------|------------|
| Owner | Read, Write, Execute |
| Group | Read, Execute |
| Others | Read Only |

Verify:

```bash
ls -l file
```

Output:

```
-rwxr-xr--
```

---

# 4. kill -9 vs kill -15 — What's the difference?

## Answer

### kill -15 (SIGTERM)

- Gracefully terminates the process.
- Allows cleanup operations.
- Closes files and releases resources.
- Default signal.

```bash
kill -15 PID
```

### kill -9 (SIGKILL)

- Forcefully kills the process.
- Cannot be ignored.
- No cleanup is performed.

```bash
kill -9 PID
```

Use `kill -15` first. Use `kill -9` only if the process does not respond.

---

# 5. How do you find and kill a process listening on port 8080?

## Answer

Find the process:

```bash
lsof -i :8080
```

or

```bash
netstat -tulnp | grep 8080
```

or

```bash
ss -tulnp | grep 8080
```

Kill the process:

```bash
kill -15 PID
```

If required:

```bash
kill -9 PID
```

One-liner:

```bash
kill -9 $(lsof -t -i:8080)
```

---

# 6. Walk me through what happens when you type a URL into a browser.

## Answer

1. Browser checks its cache.
2. OS checks DNS cache.
3. Checks `/etc/hosts`.
4. Queries the configured DNS resolver.
5. DNS resolver contacts root, TLD, and authoritative DNS servers.
6. Browser receives the IP address.
7. TCP three-way handshake is established.
8. TLS handshake occurs (HTTPS).
9. Browser sends the HTTP request.
10. Load Balancer or Reverse Proxy receives the request.
11. Application server processes it.
12. Response is returned.
13. Browser downloads HTML, CSS, JavaScript, and images.
14. Browser renders the page.

---

# 7. TCP vs UDP — When would you use each?

## Answer

| Feature | TCP | UDP |
|---------|-----|-----|
| Connection | Yes | No |
| Reliable | Yes | No |
| Ordered Delivery | Yes | No |
| Speed | Slower | Faster |
| Error Checking | Yes | Minimal |

### TCP Examples

- SSH
- HTTPS
- FTP
- SMTP
- Database connections

### UDP Examples

- DNS
- VoIP
- Video Streaming
- Gaming
- DHCP

---

# 8. Explain DNS resolution order — cache, hosts file, resolver.

## Answer

DNS resolution occurs in this order:

1. Browser DNS cache
2. Operating system DNS cache
3. `/etc/hosts`
4. Local DNS resolver
5. Root DNS server
6. TLD server (.com, .org)
7. Authoritative DNS server
8. IP address returned to the client

Useful commands:

```bash
dig google.com

nslookup google.com

host google.com
```

---

# 9. Forward Proxy vs Reverse Proxy — What's the difference?

## Answer

### Forward Proxy

- Sits between clients and the internet.
- Hides client identity.
- Used for internet access control and caching.

Example:

```
User → Forward Proxy → Internet
```

### Reverse Proxy

- Sits in front of servers.
- Hides backend servers.
- Performs SSL termination, load balancing, caching, and security.

Example:

```
Client → NGINX → Backend Servers
```

Examples:

- NGINX
- HAProxy
- AWS ALB
- Apache

---

# 10. A server is unreachable — What's your troubleshooting step-by-step?

## Answer

1. Check if the server is powered on.
2. Verify Security Groups and NACLs (AWS).
3. Ping the server.

```bash
ping <IP>
```

4. Check SSH connectivity.

```bash
ssh user@IP
```

5. Verify DNS resolution.

```bash
nslookup hostname
```

6. Check routing.

```bash
traceroute IP
```

7. Verify firewall rules.

```bash
iptables -L
firewall-cmd --list-all
```

8. Check NIC status.

```bash
ip addr
ip route
```

9. Review logs.

```bash
journalctl
```

10. Escalate if hardware or cloud infrastructure issue is identified.

---

# 11. Disk is at 95% — How do you find what's eating space?

## Answer

Check disk usage:

```bash
df -h
```

Find large directories:

```bash
du -sh /*
```

Largest files:

```bash
find / -type f -size +500M
```

Sort largest directories:

```bash
du -ah / | sort -rh | head -20
```

Check logs:

```bash
/var/log
```

Check Docker:

```bash
docker system df
docker system prune
```

---

# 12. How do you check and analyze system logs when a service crashes?

## Answer

Check service status:

```bash
systemctl status nginx
```

View logs:

```bash
journalctl -u nginx
```

Follow logs:

```bash
journalctl -fu nginx
```

System logs:

```bash
tail -100 /var/log/messages
tail -100 /var/log/syslog
```

Application logs:

```bash
tail -f /var/log/application.log
```

---

# 13. Walk me through the Linux boot process, BIOS to login prompt.

## Answer

1. BIOS/UEFI initializes hardware.
2. Bootloader (GRUB) loads.
3. Kernel is loaded into memory.
4. Initramfs initializes drivers.
5. Kernel mounts the root filesystem.
6. systemd (PID 1) starts.
7. Services start.
8. Network initializes.
9. Login prompt appears.

Flow:

```
BIOS/UEFI
↓
GRUB
↓
Kernel
↓
Initramfs
↓
systemd
↓
Services
↓
Login Prompt
```

---

# 14. How do you schedule and debug a cron job that isn't running?

## Answer

Edit cron:

```bash
crontab -e
```

List cron jobs:

```bash
crontab -l
```

Verify cron service:

```bash
systemctl status crond
```

Check logs:

```bash
journalctl -u crond
```

or

```bash
grep CRON /var/log/cron
```

Common issues:

- Incorrect path
- Missing execute permission
- Wrong environment variables
- Incorrect schedule
- Script errors

---

# 15. Package manager (yum/apt) vs compiling from source — When do you use each?

## Answer

### Package Manager

- Easy installation
- Automatic dependency handling
- Security updates
- Recommended for production

Examples:

```bash
yum install nginx
apt install nginx
```

### Source Compilation

Used when:

- Latest version required
- Custom modules needed
- Vendor package unavailable

Commands:

```bash
./configure
make
make install
```

---

# 16. Security Group vs NACL — What's the difference?

## Answer

| Feature | Security Group | NACL |
|---------|----------------|------|
| Level | Instance | Subnet |
| Stateful | Yes | No |
| Allow Rules | Yes | Yes |
| Deny Rules | No | Yes |
| Rule Order | No | Yes |

Security Groups are stateful firewalls attached to EC2 instances, while NACLs are stateless firewalls applied at the subnet level.

---

# 17. How do you set up an EC2 instance to auto-recover on a failed health check?

## Answer

1. Open **CloudWatch**.
2. Create a CloudWatch Alarm for the **StatusCheckFailed_System** metric.
3. Configure the alarm action as **Recover this instance**.
4. When the underlying hardware fails, AWS automatically recovers the instance to new hardware while retaining the instance ID, EBS volumes, Elastic IP, and metadata.

---

# 18. IAM Role vs IAM User — When do you use each?

## Answer

### IAM User

- Represents a person or application.
- Has long-term credentials.
- Used for administrators or developers.

### IAM Role

- Temporary credentials.
- Assumed by AWS services or users.
- Used by EC2, Lambda, ECS, EKS, and cross-account access.

Best practice: Use IAM Roles instead of storing access keys on EC2 instances.

---

# 19. How would you back up and restore an EC2 instance or its data?

## Answer

### Backup

- Create an EBS Snapshot.
- Create an AMI of the EC2 instance.
- Store application data in S3.
- Enable AWS Backup.

### Restore

- Launch a new EC2 instance from the AMI.
- Restore EBS volumes from snapshots.
- Reattach restored volumes if needed.

---

# 20. EBS vs S3 — When would you pick one over the other?

## Answer

| Feature | EBS | S3 |
|---------|-----|----|
| Storage Type | Block | Object |
| Attach to EC2 | Yes | No |
| Boot Volume | Yes | No |
| Scalability | Limited | Virtually Unlimited |
| Durability | High | 99.999999999% |

Use **EBS** for operating systems, databases, and applications requiring low latency. Use **S3** for backups, logs, static websites, media files, and archival storage.

---

# 21. How do you set up alerting so you know a service is down before a customer does?

## Answer

Implement proactive monitoring using tools such as Prometheus, Grafana, CloudWatch, or Datadog.

Steps:

1. Collect metrics from applications and infrastructure.
2. Configure health checks.
3. Define alert rules for CPU, memory, disk, response time, and error rates.
4. Integrate Alertmanager or CloudWatch with Slack, Microsoft Teams, PagerDuty, or email.
5. Use synthetic monitoring to continuously test critical user journeys.

---

# 22. Monitoring vs Synthetic Monitoring — What's the difference?

## Answer

| Monitoring | Synthetic Monitoring |
|------------|----------------------|
| Observes live systems | Simulates user actions |
| Uses real metrics | Uses scripted tests |
| Detects issues after traffic arrives | Detects issues before users notice |
| Examples: CPU, Memory, Logs | Examples: Login, Checkout, API Tests |

A mature monitoring strategy combines both infrastructure monitoring and synthetic monitoring.

---

# 23. How would you monitor disk, memory, and CPU across 50+ servers without logging into each one?

## Answer

Deploy a centralized monitoring solution:

- Install **Node Exporter** on each server.
- Configure **Prometheus** to scrape metrics.
- Visualize metrics using **Grafana** dashboards.
- Configure **Alertmanager** for notifications.

In AWS environments, **CloudWatch Agent** can also be installed on all EC2 instances to collect and monitor CPU, memory, disk, and log metrics centrally.

---

# 24. Tell me about a time a server went down in production. What did you do first?

## Answer

In one production incident, users reported that the application was inaccessible. I first acknowledged the incident and checked the monitoring dashboards to confirm the outage. I verified the EC2 instance health, application service status, and load balancer health checks. After identifying that the application service had crashed due to insufficient disk space, I freed disk space by removing old logs, restarted the service, and confirmed that health checks were passing. I monitored the environment to ensure stability and documented the Root Cause Analysis (RCA) with preventive actions, including log rotation and disk usage alerts.

---

# 25. Three servers report issues at the same time — How do you prioritize?

## Answer

I prioritize incidents based on business impact and service criticality.

1. Identify which server affects customer-facing or production services.
2. Assess the severity using monitoring dashboards and alerts.
3. Assign resources to investigate lower-priority issues while focusing on the highest-impact incident.
4. Restore the critical service first using rollback, failover, or recovery procedures.
5. Communicate regular updates to stakeholders throughout the incident.
6. After stabilizing production, investigate and resolve the remaining issues.
7. Perform a Root Cause Analysis (RCA) and implement preventive measures to reduce future occurrences.

This approach minimizes customer impact while ensuring efficient incident management.
