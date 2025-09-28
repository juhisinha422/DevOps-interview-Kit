# DevOps Interview Questions and Answers

## 1. Monolithic vs Microservice Architecture

* **Monolithic**: Single deployable unit, tightly coupled, harder to scale and update.
* **Microservices**: Independent services, loosely coupled, easier to scale, deploy, and maintain.

---

## 2. Design in AWS

* Start with **VPC** → **Subnets (public/private)** → **EC2/Containers** → **RDS/DynamoDB** → **Load Balancer (ALB/NLB)** → **Auto Scaling** → **IAM for security** → **CloudWatch/CloudTrail for monitoring**.

---

## 3. How to design Highly Available & Scalable architecture

* Use **multi-AZ deployments**.
* Configure **Auto Scaling Groups & Load Balancers**.
* Build **stateless services**.
* Use **CDN (CloudFront)**.
* **Decouple** services with **SQS/Kinesis**.

---

## 4. Why Horizontal scaling is preferred over Vertical scaling

* **Vertical Scaling**: Increase machine size (limited & costly).
* **Horizontal Scaling**: Add more servers (cheaper, resilient, avoids single point of failure).

---

## 5. SonarQube

* Tool for **code quality analysis** (bugs, vulnerabilities, code smells).
* Integrates with **CI/CD pipelines** to enforce coding standards.

---

## 6. Pod Communication in Kubernetes

* Pods communicate via **Services** (ClusterIP, NodePort, LoadBalancer).
* **kube-proxy + iptables** route traffic to healthy pods.
* Service discovery happens via **DNS** in Kubernetes.

---

## 7. How to know the exit status of code

```bash
echo $?
```

* `0` → success
* Non-zero → failure

---

## 8. How to run any command in background

* Append `&` at the end:

```bash
command &
```

* Or use:

```bash
nohup command &
```

---

## 9. Example Shell Script

```bash
#!/bin/bash
tar -czf /backup/home_$(date +%F).tar.gz /home/ubuntu
```

---

## 10. How to handle production issues

* Check logs (`kubectl logs`, `docker logs`, or app logs).
* Use monitoring tools (**Prometheus, CloudWatch, Grafana**).
* Rollback using **CI/CD pipeline** or previous deployment.
* Perform **root cause analysis** and fix.

---

## 11. Event Driven Architecture

* Services are triggered by **events** instead of direct calls.
* Uses **queues, topics, event buses (Kafka, SQS, SNS)**.
* Improves **decoupling, scalability, and real-time processing**.

---

## 12. QA Pipeline Stages

* **Stages**: Code Checkout → Build → Unit Test → Integration Test → Security Scan → Deploy to QA → Automated Tests → Approval → Deploy to Prod.
* **Responsibility**: DevOps engineers create/manage the pipeline, developers/testers define test cases.

---
