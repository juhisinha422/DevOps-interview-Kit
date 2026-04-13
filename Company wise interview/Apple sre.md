# 🍏 SRE / DevOps Interview Preparation (4 Years Experience)

## 1. Roles & Responsibilities as an SRE/DevOps Engineer

* Manage CI/CD pipelines for faster and reliable deployments
* Ensure high availability, scalability, and reliability of applications
* Implement monitoring, alerting, and logging solutions
* Automate infrastructure using IaC tools (Terraform, CloudFormation)
* Handle incident management, root cause analysis (RCA)
* Improve system performance and reduce toil through automation
* Collaborate with dev teams for better release strategies

---

## 2. How do you build a standard Linux image?

* Use tools like **Packer** to automate image creation
* Start from a base image (AMI / ISO)
* Install required packages, security patches
* Configure users, SSH, firewall rules
* Harden OS (disable unused services, enforce policies)
* Validate image using test scripts
* Store image in repository (AWS AMI, VM templates)

---

## 3. Partitioning Standards & Security Parameters

**Partitioning:**

* `/` → Root (20–30 GB)
* `/var` → Logs & apps (separate to avoid root fill)
* `/home` → User data
* `/tmp` → Temporary files (mounted with restrictions)

**Security Parameters:**

* Enable firewall (iptables/firewalld)
* SELinux enforcing mode
* Disable root login
* Strong password policies
* File permissions (least privilege)

---

## 4. SSH Hardening Techniques

* Disable root login (`PermitRootLogin no`)
* Use SSH key-based authentication
* Change default port (optional)
* Disable password authentication
* Limit users/groups (`AllowUsers`)
* Enable fail2ban to block brute force attacks
* Set idle timeout

---

## 5. Deploy Multiple Linux Servers using Ansible

* Create inventory file with server details
* Use playbooks for automation
* Example tasks:

  * Install packages
  * Configure services
  * Deploy applications
* Use roles for reusable structure
* Execute:

```bash
ansible-playbook -i inventory.ini deploy.yml
```

---

## 6. Create User & Assign Permissions

```bash
useradd appuser
passwd appuser
mkdir /apps
chown appuser:appuser /apps
chmod 755 /apps
```

* Use groups for better access control
* Follow least privilege principle

---

## 7. Troubleshoot Intermittent Image Build Failures

* Check logs (Packer / CI logs)
* Validate network connectivity
* Verify repository/package availability
* Check disk/memory constraints
* Retry with debug mode
* Identify flaky scripts or dependencies

---

## 8. Disk Shows 100% Usage but Data is Less

Possible reasons:

* Deleted files still held by processes
  → Use: `lsof | grep deleted`
* Inode exhaustion
  → `df -i`
* Hidden large files
* Mounted directories issues

---

## 9. Troubleshoot Memory / Heap Issues

**Linux:**

* `top`, `htop`, `free -m`
* Check OOM killer logs (`dmesg`)

**Java:**

* Analyze heap dump using tools (jmap, jstack)
* Check GC logs
* Tune JVM parameters (-Xms, -Xmx)

---

## 10. Auto-start Applications on Boot

* Use systemd service:

```bash
systemctl enable myapp.service
systemctl start myapp.service
```

* Ensure proper service file configuration

---

## 11. Patch Management Process

* Identify vulnerabilities
* Schedule patch window
* Test patches in staging
* Apply using automation tools (Ansible, WSUS, etc.)
* Validate systems post-patch
* Maintain compliance reports

---

## 12. Handling Production Outage

* Acknowledge alert immediately
* Check monitoring dashboards
* Identify impact & affected services
* Rollback or apply hotfix
* Communicate with stakeholders
* Perform RCA after resolution
* Implement preventive measures

---

## 13. CI/CD Pipeline (Jenkins: Dev → UAT → Prod)

1. Code commit → Git
2. Build → Compile & package
3. Test → Unit & integration tests
4. SonarQube → Code quality
5. Artifact → Store in Nexus/Artifactory
6. Deploy to Dev
7. Promote to UAT after approval
8. Manual/automated approval
9. Deploy to Production

---

## 14. Handling Deployment Failure in UAT

* Analyze logs & error messages
* Rollback to previous stable version
* Fix configuration/code issues
* Re-run pipeline
* Validate thoroughly before promoting
* Add checks to prevent future failures

---

## ✅ Pro Tips (Apple-Level Expectations)

* Focus on **automation + reliability mindset**
* Strong understanding of **Linux internals**
* Experience with **scalability & failure handling**
* Be ready for **real incident scenarios**
* Emphasize **monitoring, observability, and RCA**

---

🚀 This README is optimized for **4 years experienced SRE/DevOps interviews**
