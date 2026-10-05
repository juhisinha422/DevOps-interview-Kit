# Capgemini DevOps Engineer (4–5 Years) – Complete JD-Based Interview Q&A

---

## 🔹 1. Self Introduction

### Tell me about yourself
I am a DevOps Engineer with 4–5 years of experience working on CI/CD, Kubernetes, Docker, and Cloud platforms like AWS/GCP. I have experience in automating deployments, building scalable systems, and working closely with development teams using Java/Spring Boot applications. I focus on improving reliability, performance, and security of applications.

---

## 🔹 2. Product Design & Architecture

### How do you contribute to product design as a DevOps Engineer?
- Ensure application is scalable, deployable, and observable
- Define CI/CD and automation strategy
- Suggest containerization and microservices architecture
- Collaborate with architects and developers early

---

### How do you design a scalable microservices architecture?
- Use Spring Boot microservices
- API Gateway for routing
- Deploy on Kubernetes
- Use Load Balancer + Auto Scaling
- Database per service
- Use messaging queues (Kafka/PubSub)

---

### How do you ensure high availability?
- Multi-zone deployment
- Auto scaling
- Health checks
- Load balancing
- DB replication

---

## 🔹 3. Java / Spring Boot / API Design

### What are REST API best practices?
- Proper HTTP methods
- Stateless design
- Proper status codes
- Versioning (/v1)
- OpenAPI 3.0 documentation

---

### How do you secure APIs?
- JWT / OAuth2
- HTTPS
- Rate limiting
- API Gateway security

---

### What is OpenAPI 3.0?
Standard for API documentation describing endpoints, request/response, authentication.

---

## 🔹 4. Kubernetes & Docker

### Explain Kubernetes architecture
- Control Plane: API Server, Scheduler, Controller Manager, etcd
- Worker Node: Kubelet, Pods

---

### How do you deploy application in Kubernetes?
- Build Docker image
- Push to registry
- Create Deployment, Service
- Use Ingress

---

### What are probes?
- Liveness → restart container
- Readiness → traffic control

---

### What are ConfigMaps and Secrets?
- ConfigMap → non-sensitive data
- Secret → sensitive data

---

### What are sidecar containers?
Used for logging, monitoring, proxy

---

### What are taints and tolerations?
Control pod scheduling on nodes

---

### How do you troubleshoot pods?
- kubectl logs
- kubectl describe
- check events

---

### How do you ensure zero downtime deployments?
- Rolling updates
- Readiness probes
- Blue-green / canary deployments

---

## 🔹 5. CI/CD (GitHub, Jenkins, Groovy, Maven)

### How do you design CI/CD pipeline?
Code → Build → Test → Scan → Docker build → Push → Deploy → Verify

---

### What are Jenkins shared libraries?
Reusable Groovy scripts for pipelines

---

### Declarative vs Scripted pipelines?
- Declarative → structured
- Scripted → flexible

---

### How do you handle failures?
- Check logs
- Rollback
- Fix issue

---

### How do you manage secrets in pipelines?
- Jenkins credentials
- Environment variables

---

### How do you integrate Maven?
- Build Java apps
- Run tests
- Package artifacts

---

## 🔹 6. Git & Collaboration

### Git merge vs rebase?
- Merge → preserves history
- Rebase → clean history

---

### Branching strategy?
- Feature branches
- Pull requests
- Code reviews

---

## 🔹 7. Cloud (GCP Focus)

### How do you deploy apps in GCP?
- Use GKE
- Use Load Balancer
- Configure IAM

---

### How do you secure cloud infra?
- IAM roles
- Firewall rules
- Encryption

---

### Cost optimization?
- Right-sizing
- Auto scaling
- Remove unused resources

---

## 🔹 8. Automation & Ansible

### What is Ansible?
Tool for configuration management using YAML

---

### Use cases?
- Server setup
- App deployment
- Config management

---

## 🔹 9. Performance Tuning

### How do you improve performance?
- Optimize queries
- Use caching
- Tune JVM
- Scale app

---

## 🔹 10. Security & Compliance

### How do you handle vulnerabilities?
- Scan using tools
- Patch regularly
- Secure dependencies

---

### How do you ensure compliance?
- Logging & auditing
- Access control
- Security policies

---

## 🔹 11. Agile & Backlog Management

### How do you manage backlog?
- Use Jira
- Create epics/stories
- Prioritize tasks

---

### Role in Agile?
- Work with PO
- Automate deployments
- Improve delivery speed

---

## 🔹 12. Non-Functional Testing

### How do you ensure testability?
- Load testing
- Performance testing
- Failover testing

---

## 🔹 13. Scenario-Based Questions

### Pipeline works in staging but fails in prod?
- Check config differences
- IAM permissions
- Logs

---

### High latency issue?
- Check CPU/memory
- DB performance
- Network

---

### Production issue?
1. Identify issue
2. Check logs
3. Rollback
4. Fix root cause

---

## 🔥 Final Tip
Interviewers expect:
- Real production examples
- Debugging approach
- Clear explanation
- Hands-on experience
