# DevOps Interview Questions and Answers

# 1st round:

## Which projects u r working on and skills

### Answer:

Currently I am working on Kubernetes and cloud-based projects. My skills include Docker, Kubernetes, Jenkins/GitLab CI, AWS, Terraform, Helm, Linux, monitoring tools like Prometheus and Grafana.

I work on:

* CI/CD pipeline creation
* Kubernetes deployments
* Docker image management
* Infrastructure automation
* Monitoring and troubleshooting

---

## Kubernetes workflow in your company how request flows in architecture (I took reference of Qx and YES DR).

### Answer:

In our architecture, request flow works like this:

1. User request comes through DNS.
2. DNS routes traffic to Load Balancer.
3. Load Balancer forwards request to Ingress Controller.
4. Ingress routes traffic to Kubernetes Service.
5. Service forwards traffic to Pods.
6. Pods communicate with backend/database.

We use:

* Ingress Controller
* Services
* Deployments
* ConfigMaps and Secrets

---

## Middleware services other then core application proxy, loadbalancer, speagel, other things more

### Answer:

Middleware services are used for traffic management, security, monitoring, and communication.

Examples:

* Proxy: Nginx/HAProxy
* Load Balancer: ALB/NLB
* Service Mesh: Istio
* Monitoring: Prometheus/Grafana
* Logging: ELK/Loki
* Messaging: Kafka/RabbitMQ

These help in scalability and observability.

---

## Storage classes in K8s current version which u r using. How you migrate or take backup of database suppose PostgreSQL

### Answer:

StorageClass is used for dynamic provisioning of Persistent Volumes.

Common storage classes:

* gp2/gp3 in EKS
* SSD/NFS storage

For PostgreSQL backup:

```bash
pg_dump -U username dbname > backup.sql
```

Restore:

```bash
psql -U username dbname < backup.sql
```

Production best practices:

* Store backups in S3
* Use snapshots
* Schedule backups using cron jobs
* Test restoration process regularly

---

## AWS Questions basics IAM roles policies, what aws services your company uses

### Answer:

IAM Role:
Provides temporary permissions to AWS services/users.

IAM Policy:
JSON document that defines permissions.

AWS services used:

* EC2
* EKS
* IAM
* S3
* RDS
* CloudWatch
* Route53
* Load Balancers

---

## Asked u know database SQL queries or how u get data in NOSQL database.

### Answer:

SQL example:

```sql
SELECT * FROM employees WHERE id = 1;
```

NoSQL databases:

* MongoDB
* DynamoDB
* Cassandra

MongoDB query example:

```javascript
db.users.find({id:1})
```

Difference:

* SQL uses tables and relations.
* NoSQL uses document/key-value structure.

---

## Helm questions he asked basic i answered deep i told i don't know.

### Answer:

Helm is a package manager for Kubernetes.

Important concepts:

* Helm Chart
* values.yaml
* templates
* releases

Commands:

```bash
helm install app ./chart
helm upgrade app ./chart
helm rollback app 1
helm uninstall app
```

Benefits:

* Easy deployments
* Reusable templates
* Rollback support

---

## CICD he told me to walk through the CI and CD in your current project not deep just the walkthrough.

### Answer:

CI/CD workflow:

1. Developer pushes code to Git.
2. Jenkins/GitLab CI pipeline triggers.
3. Build and unit testing happen.
4. Docker image is created.
5. Image is pushed to registry.
6. Deployment happens to Kubernetes using Helm.
7. Monitoring and validation are performed.

Tools used:

* Git
* Jenkins/GitLab CI
* Docker
* Kubernetes
* Helm

---

# 2nd round:

## K8s explain control plane and data plane (worker nodes) each component etcd, api server, etc all cka type

### Answer:

Control Plane Components:

* API Server: Main communication entry point.
* Scheduler: Assigns Pods to nodes.
* Controller Manager: Maintains desired state.
* etcd: Stores cluster state.

Data Plane Components:

* Kubelet
* Kube Proxy
* Container Runtime

---

## shifted to CI/CD he asked why u used the particular tool and stuff I took the example of our health monitor project

### Answer:

We used Jenkins/GitLab CI because:

* Easy Git integration
* Pipeline automation
* Kubernetes support
* Scalability
* Plugin ecosystem

In our health monitor project, CI/CD helped automate deployments and reduced manual effort.

---

## Troubleshooting walkthrough from incident creation to RCA challenges why you took this decision.

### Answer:

Troubleshooting flow:

1. Monitoring alert received.
2. Check logs and dashboards.
3. Identify impacted service.
4. Verify infrastructure and application health.
5. Restart/redeploy if needed.
6. Perform RCA.
7. Document issue and preventive action.

Example:
Pod was crashing because of memory issue.

Resolution:

* Checked kubectl logs
* Increased memory limits
* Added monitoring alerts

---

## Apart from that what u want to learn in future things that's it.

### Answer:

I want to learn:

* Advanced Kubernetes
* ArgoCD
* Service Mesh
* DevSecOps
* Cloud architecture
* Observability tools

---

# 3rd round managerial:

## Why u want to switch 10 months experience he scolded and brushed me for 30 minutes on that.

### Answer:

I am looking for better learning opportunities, challenging projects, and exposure to advanced DevOps practices. I want to improve my technical skills and work on larger-scale infrastructure.

---

## Normal questions manager type

### Answer:

Managerial questions are usually about:

* Teamwork
* Handling pressure
* Communication
* Deadlines
* Conflict management

Sample answer:
I prioritize tasks properly, communicate clearly with team members, and stay calm during production issues.

---

## How will you give access to a person for Kubernetes cluster in EKS or GKE

### Answer:

In EKS:

* Create IAM user/role.
* Map role in aws-auth ConfigMap.
* Provide RBAC permissions.

Example:

```bash
kubectl create role developer --verb=get,list,watch --resource=pods
kubectl create rolebinding dev-binding --role=developer --user=user1
```

In GKE:

* Use Google IAM
* Configure RBAC
* Provide least privilege access

---

# 4th round with VP:

## Why u want to leave in tough job market

### Answer:

I appreciate my current company, but I am looking for better technical growth, challenging projects, and opportunities to improve my skills further.

---

## He send me his podcast link and docs to get to know about the company more

### Answer:

You can respond:

"Thank you for sharing the resources. I will go through the podcast and documentation to understand the company and its architecture better."
