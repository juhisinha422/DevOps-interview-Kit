#  SRE / DevOps Interview (4 Years Experience)

---

### 1.Explain your roles and responsibilities as an SRE/DevOps engineer

* Managing CI/CD pipelines for automated deployments
* Ensuring system reliability, availability, and scalability
* Monitoring applications using tools like Prometheus/Grafana
* Automating infrastructure using Terraform/Ansible
* Handling incidents, troubleshooting, and performing RCA
* Reducing manual work by implementing automation
* Collaborating with developers for smooth releases

---

### 2.How do you build a standard Linux image?

* Use tools like Packer for automated image creation
* Start with a base image (AMI/ISO)
* Install required packages and dependencies
* Apply security patches and updates
* Configure users, SSH, firewall, and services
* Harden the OS (disable unnecessary services)
* Validate image and store it (e.g., AMI)

---

### 3.What partitioning standards and security parameters do you follow while building Linux images?

**Partitioning:**

* `/` → Root partition
* `/var` → Logs and application data
* `/home` → User data
* `/tmp` → Mounted with restricted permissions

**Security:**

* Enable firewall
* SELinux in enforcing mode
* Disable root login
* Strong password policies
* Least privilege permissions

---

### 4.What are SSH hardening techniques you have implemented?

* Disable root login
* Enable key-based authentication
* Disable password authentication
* Restrict users using AllowUsers
* Change default SSH port (optional)
* Enable fail2ban
* Configure idle session timeout

---

### 5.How do you deploy multiple Linux servers using Ansible?

* Maintain inventory file with all server details
* Write playbooks to install packages and configure services
* Use roles for reusable components
* Execute playbook across servers:
  ansible-playbook -i inventory deploy.yml

---

### 6.How do you create a user and assign permissions to directories like /apps?

* Create user: `useradd appuser`
* Set password: `passwd appuser`
* Create directory: `mkdir /apps`
* Assign ownership: `chown appuser:appuser /apps`
* Set permissions: `chmod 755 /apps`

---

### 7.How do you troubleshoot intermittent failures during image building?

* Check build logs (Packer/CI logs)
* Verify network connectivity
* Validate package repositories
* Check resource issues (CPU, memory, disk)
* Identify flaky scripts or dependencies
* Run build in debug mode

---

### 8.If disk shows 100% usage but actual data is less, what could be the issue?

* Deleted files still held by running processes (`lsof | grep deleted`)
* Inode exhaustion (`df -i`)
* Hidden large files
* Log files not rotated
* Mount point issues

---

### 9.How do you troubleshoot memory or heap issues in Linux/Java applications?

**Linux:**

* Use `top`, `htop`, `free -m`
* Check OOM logs using `dmesg`

**Java:**

* Analyze heap dump (jmap, jstack)
* Check GC logs
* Tune JVM parameters (-Xms, -Xmx)

---

### 10.How do you configure applications to start automatically on system boot?

* Create systemd service file
* Enable service: `systemctl enable app.service`
* Start service: `systemctl start app.service`

---

### 11.What is your organization’s patch management process?

* Identify vulnerabilities
* Schedule patching window
* Test patches in staging
* Apply patches using automation tools
* Validate systems after patching
* Maintain compliance reports

---

### 12.How do you handle a production application outage?

* Acknowledge alert immediately
* Identify impacted services
* Check logs and monitoring dashboards
* Apply fix or rollback deployment
* Communicate with stakeholders
* Perform RCA after resolution

---

### 13.Explain your CI/CD pipeline process using Jenkins (Dev → UAT → Prod)

* Code commit triggers pipeline
* Build stage (compile/package)
* Test stage (unit/integration tests)
* Code quality checks (SonarQube)
* Store artifact (Nexus/Artifactory)
* Deploy to Dev → UAT → Prod
* Approval before production deployment

---

### 14.How do you handle deployment failure in staging/UAT environment?

* Check logs and identify root cause
* Rollback to previous stable version
* Fix configuration or code issues
* Re-run pipeline
* Validate before promoting to production

---
