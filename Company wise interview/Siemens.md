# 🚀 DevOps & SRE Interview Questions – Siemens (4+ Years Experience)

---

## 1. How do you solve P1 and P2 incidents during deployment failures, and what rollback strategy do you use?

* **P1 (Critical)**: Immediate rollback, restore service, notify stakeholders
* **P2 (High)**: Investigate + partial rollback
* Rollback strategies:

  * Blue-Green deployment
  * Canary rollback
  * Helm rollback / previous stable build

---

## 2. What is an error budget, MTTR, SLA, SLO, and SLI?

* **SLA**: Service agreement with customer
* **SLO**: Target reliability (e.g., 99.9%)
* **SLI**: Metric (latency, uptime)
* **Error Budget**: Allowed failure = (1 - SLO)
* **MTTR**: Mean Time To Recovery

---

## 3. What are throughput, error rate, and latency? Also, explain index error or index score.

* **Throughput**: Requests processed per second
* **Error Rate**: Failed requests percentage
* **Latency**: Response time
* **Index score**: Performance metric combining multiple factors (e.g., search/index efficiency)

---

## 4. What is Apdex score in New Relic?

* Measures user satisfaction based on response time
* Formula based on satisfied, tolerating, frustrated users

---

## 5. Can you give a short introduction about yourself to a recruiter?

* DevOps Engineer with 4+ years experience
* Skilled in AWS, Kubernetes, CI/CD, Terraform
* Experience in automation, monitoring, production support
* Focused on reliability and scalability

---

## 6. How do you use IAM, VPC, S3, and CloudWatch during AMI rehydration, AWS infrastructure changes, or patching activities?

* IAM: Role-based access
* VPC: Network isolation
* S3: Store AMIs/backups
* CloudWatch: Monitor logs & metrics during patching

---

## 7. Apart from hardware and programming languages, what tools do you use?

* Jenkins, Docker, Kubernetes
* Terraform, Ansible
* Prometheus, Grafana
* Git, SonarQube

---

## 8. How can we automate manual tasks or day-to-day activities in operations?

* Bash/Python scripts
* Cron jobs
* CI/CD pipelines
* Infrastructure as Code

---

## 9. Can you explain the cron jobs you configured, what tasks you automated, and how it reduced manual work?

* Log cleanup scripts
* Backup jobs
* Health checks
* Reduced manual intervention & improved efficiency

---

## 10. How would you implement a new project using DevOps methodology with respect to organizational processes?

* Requirement → Design → Git → CI/CD → Testing → Deployment → Monitoring
* Use Agile + automation + feedback loop

---

## 11. What is a Git merge conflict, when does it occur, and how do you resolve it?

* Occurs when multiple changes affect same lines
* Resolve manually, test, commit

---

## 12. How do you configure CI/CD pipelines from scratch using the Jenkins dashboard?

* Create job → Configure SCM → Add build steps
* Add triggers → Configure stages → Deploy

---

## 13. What are upstream and downstream jobs in Jenkins?

* **Upstream**: Parent job
* **Downstream**: Triggered job

---

## 14. What is a Dockerfile, and what are RUN, CMD, and ENTRYPOINT?

* Dockerfile = Instructions to build image
* RUN: Executes at build time
* CMD: Default command
* ENTRYPOINT: Fixed execution

---

## 15. A Docker container crashed — how do you troubleshoot and fix it?

* Check logs: `docker logs`
* Inspect container
* Verify config/env variables
* Restart or rebuild

---

## 16. Over the past month, five pods crashed — how do you get information about them?

* Use:

  * `kubectl get pods`
  * `kubectl describe pod`
  * `kubectl logs`

---

## 17. Without monitoring tools, how do you get the last five pod crash events in Kubernetes?

```bash id="3g9vje"
kubectl get events --sort-by=.metadata.creationTimestamp | tail -5
```

---

## 18. What is ELB, and what are the types of ELBs?

* Elastic Load Balancer distributes traffic
* Types:

  * ALB (HTTP/HTTPS)
  * NLB (TCP/UDP)
  * CLB (Legacy)

---

## 19. What is min, max, and desired state in Kubernetes or auto-scaling?

* **Min**: Minimum pods
* **Max**: Maximum pods
* **Desired**: Target pods

---

## 20. If an EC2 instance in an auto-scaling group is restarted manually, what happens?

* ASG ensures desired state
* Instance may be replaced if unhealthy

---

## 21. How do you integrate metrics into monitoring tools?

* Export metrics via exporters
* Scrape using Prometheus
* Visualize in Grafana

---

## 22. If you receive two high-priority tickets at the same time, how do you handle them?

* Prioritize based on impact
* Communicate with team
* Delegate if needed

---

## 23. What is your biggest achievement in your career so far?

* Example: Automated CI/CD pipeline reducing deployment time by 70%

---

## 24. What are the biggest issues you resolved recently?

* Production outage, high CPU issue, deployment failure

---

## 25. What is Cloud Print and Cloud Pub/Sub?

* Cloud Print: Print via cloud
* Pub/Sub: Messaging service (event-driven communication)

---

## 26. What is IAM, an IAM user, and an IAM role?

* IAM: Identity management
* User: Individual identity
* Role: Temporary access

---

## 27. Can you explain Kubernetes architecture?

* Master: API server, scheduler
* Worker: kubelet, pods

---

## 28. What are duplicate assets and a duplication controller?

* Duplicate assets: Redundant resources
* Controller ensures consistency

---

## 29. What are liveness and readiness probes?

* Liveness: Restart container if unhealthy
* Readiness: Control traffic routing

---

## 30. What is Kubernetes abstraction?

* Abstracts infrastructure into objects (pods, services)

---

## 31. How many types of services run in Kubernetes?

* ClusterIP
* NodePort
* LoadBalancer

---

## 32. In Kubernetes networking, what network mechanisms work?

* Pod networking
* Service networking
* Ingress

---

## 33. What are the authentication methods in Kubernetes?

* Certificates
* Tokens
* IAM (cloud integration)

---

## 34. A Kubernetes pod crashed — how do you troubleshoot and fix it?

* Check logs
* Describe pod
* Check events
* Fix config/resource issue

---

## 35. How do you check the last five pod crashes without using monitoring tools?

```bash id="h8hq3c"
kubectl get events --sort-by=.metadata.creationTimestamp | tail -5
```

---

## 🎯 Summary

Covers:

* SRE concepts (SLA, SLO, MTTR)
* CI/CD, Docker, Kubernetes
* AWS infrastructure
* Real-world troubleshooting scenarios

👉 Perfect for **DevOps / SRE interviews (4+ years experience)**

---
