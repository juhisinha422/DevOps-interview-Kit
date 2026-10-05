# FinTech Company SRE / Platform Engineering Interview Experience (Detailed Answers)

---

# Round 1 – Technical

## Tell me about yourself

I introduced myself as a DevOps/SRE engineer with experience in cloud infrastructure, Kubernetes, CI/CD, monitoring, and automation. I explained that my day-to-day responsibilities include managing Kubernetes clusters, creating CI/CD pipelines using Jenkins/GitHub Actions, provisioning AWS infrastructure using Terraform, troubleshooting production incidents, implementing monitoring and alerting using Prometheus and Grafana, and optimizing infrastructure cost and reliability. I also discussed projects where I worked on microservices deployment, ingress configuration, centralized logging, and production support activities.

---

## Current responsibilities and projects

I explained that I work mainly on:

* Kubernetes cluster administration
* Terraform infrastructure provisioning
* Jenkins/GitHub Actions CI/CD pipelines
* Monitoring and alerting
* Production incident handling
* AWS cloud resource management
* Security hardening and cost optimization

In my projects, applications are containerized using Docker and deployed on Kubernetes. Infrastructure is managed through Terraform modules with remote state stored in S3 and locking using DynamoDB. Monitoring is implemented using Prometheus and Grafana, while logging is centralized through ELK or Loki.

---

# Kubernetes Scheduling Constraint Troubleshooting

The interviewer provided a Kubernetes lab issue related to scheduling constraints. I explained that if pods are not getting scheduled, my first step is checking pod events using:

```bash id="n2ot1u"
kubectl describe pod <pod-name>
```

Usually scheduling failures occur because of:

* NodeSelector mismatch
* Taints and tolerations
* Affinity/Anti-affinity rules
* Insufficient CPU or memory
* PVC binding failures
* Node resource exhaustion

Then I check cluster nodes:

```bash id="2m1vgm"
kubectl get nodes
kubectl top nodes
```

If taints exist on nodes, I verify them:

```bash id="5m8v11"
kubectl describe node <node-name>
```

Resolution depends on root cause:

* Add proper tolerations
* Modify affinity rules
* Scale nodes
* Increase resources
* Fix labels/selectors

I also explained how Kubernetes scheduler evaluates:

* Resource availability
* Taints/tolerations
* Affinity rules
* Topology spread constraints
* Pod priority

before assigning pods to nodes.

---

# Explain Prometheus and Grafana End-to-End

I explained that Prometheus works using a pull-based metrics collection model. Exporters expose metrics endpoints such as `/metrics`, and Prometheus scrapes these endpoints periodically based on scrape configuration.

Flow:

```text id="k7j6t4"
Exporter → Prometheus Scraping → TSDB Storage → Grafana Query → Dashboard
```

Prometheus stores metrics inside its TSDB (Time Series Database). Metrics are stored as timestamped series data.

Grafana itself does not store monitoring metrics. Instead, it stores:

* Dashboard definitions
* Alert configuration
* Datasource configuration
* User/session data

Grafana queries Prometheus using PromQL and renders graphs, panels, alerts, and dashboards.

I also explained Prometheus components:

* Prometheus server
* Exporters
* Alertmanager
* Service discovery

and discussed retention policies, scaling limitations, and federation concepts.

---

# Metrics Scraping

Metrics scraping works as follows:

1. Exporters expose metrics
2. Prometheus periodically pulls metrics
3. Metrics stored in TSDB
4. Alert rules evaluated
5. Grafana visualizes data

Example exporters:

* Node Exporter
* kube-state-metrics
* cAdvisor
* Blackbox exporter

---

# What Grafana stores internally at high level

Grafana mainly stores:

* Dashboard JSON
* User accounts
* Alert definitions
* Datasource configuration
* Teams/folders/permissions

Actual metrics remain stored in Prometheus or external data sources.

---

# Visualization flow

Visualization flow:

```text id="cw2gdn"
Application → Exporter → Prometheus → Grafana → Dashboard Visualization
```

Grafana queries Prometheus using PromQL and renders dashboards in real time.

---

# Walked through a production incident and explained RCA approach

I explained that during a production incident, my first priority is service restoration and impact reduction.

My approach:

1. Identify affected services
2. Check alerts and dashboards
3. Validate recent deployments
4. Analyze logs and metrics
5. Verify infrastructure health
6. Mitigate issue quickly
7. Perform RCA after stabilization

Tools used:

* Grafana
* Prometheus
* ELK/Loki
* kubectl logs
* CloudWatch
* Application logs

Typical RCA document includes:

* Timeline
* Root cause
* Business impact
* Resolution steps
* Preventive actions
* Monitoring improvements

I also explained how I differentiate:

* Infra issue
* Application issue
* Network issue
* Database bottleneck
* Resource exhaustion

during troubleshooting.

---

# AWS / Cloud Fundamentals Questions

Topics discussed included:

* VPC architecture
* Public/private subnets
* Security Groups vs NACL
* EC2
* IAM roles/policies
* Auto Scaling
* Load Balancers
* Route53
* S3
* CloudWatch
* EKS/ECS

I explained high-level cloud architecture and secure networking design.

---

# Coding/Scripting Task – Parse IPs from NGINX logs and maintain request counter

I wrote a Python script that parses client IPs from NGINX access logs and maintains request count per IP.

Example:

```python id="e0q6o5"
from collections import defaultdict

counter = defaultdict(int)

with open("access.log") as file:
    for line in file:
        ip = line.split()[0]
        counter[ip] += 1

for ip, count in counter.items():
    print(f"{ip}: {count}")
```

I explained:

* defaultdict usage
* Log parsing
* Counter increment
* Possible optimizations for large files

I also discussed production improvements:

* Regex parsing
* Real-time monitoring
* Blocking suspicious IPs
* Rate limiting integration

---

# Round 2 – Technical

## Walkthrough of setting up Ingress NGINX and Gateway API in Kubernetes

I explained that Ingress NGINX acts as reverse proxy and routes HTTP/HTTPS traffic into Kubernetes services.

Traffic flow:

```text id="r2cnp6"
Internet → LoadBalancer → Ingress Controller → Service → Pod
```

Setup steps:

1. Deploy ingress controller
2. Create ingress resources
3. Configure host/path routing
4. Configure TLS certificates
5. Expose through LoadBalancer

I also explained:

* SSL termination
* Path-based routing
* Host-based routing
* Sticky sessions
* Rate limiting

---

# Gateway API in Kubernetes

Gateway API is the newer Kubernetes networking API replacing some limitations of Ingress.

Advantages:

* Better extensibility
* More granular routing
* Better RBAC separation
* Advanced traffic management

Components:

* GatewayClass
* Gateway
* HTTPRoute

---

# Detailed discussion on production incident and RCA with follow-up questions

The interviewer asked follow-up questions around:

* Root cause identification
* Monitoring gaps
* Scaling behavior
* Rollback decisions
* Prevention strategies

I explained how I:

* Correlate logs and metrics
* Validate deployments
* Compare healthy vs unhealthy pods
* Analyze latency spikes
* Use dashboards during incidents

---

# Explain logging solutions – Grafana Loki and EFK Stack

## Grafana Loki

Grafana Loki is a lightweight log aggregation system optimized for Kubernetes.

Characteristics:

* Label-based indexing
* No full log indexing
* Lower infrastructure cost
* Tight Grafana integration

Best suited for:

* Kubernetes workloads
* Cost-efficient logging
* Large-scale container logs

---

## EFK Stack

EFK includes:

* Elasticsearch
* Fluentd/FluentBit
* Kibana

Advantages:

* Full-text search
* Powerful analytics
* Advanced querying

Disadvantages:

* High storage cost
* Higher memory/CPU usage
* Operational overhead

---

# Difference between indexing vs labeling in logs

## Indexing

Used in Elasticsearch.

Entire log content is indexed.

Advantages:

* Fast searching
* Advanced filtering
* Full-text queries

Disadvantages:

* Higher storage usage
* Increased infra cost

---

## Labeling

Used in Loki.

Only metadata labels indexed:

* Namespace
* Pod
* Application
* Container

Advantages:

* Lower storage
* Better scalability
* Lower operational cost

---

# Why DaemonSet preferred for ingress controllers even though normal replicas can also work

DaemonSet ensures one ingress pod runs on every node.

Advantages:

* Better traffic locality
* Lower latency
* Predictable routing
* Better node-level traffic handling
* Useful with host networking

Deployments can also work, but DaemonSet often provides more efficient ingress traffic distribution.

---

# Common Terraform Questions

## Terraform Workflow

I explained Terraform workflow:

```bash id="w0pwm7"
terraform init
terraform validate
terraform plan
terraform apply
```

* init → initialize providers/modules
* validate → syntax validation
* plan → execution preview
* apply → infrastructure creation/update

---

## Terraform State

Terraform state tracks real infrastructure resources and maps them to Terraform configuration.

Importance:

* Dependency tracking
* Resource mapping
* Faster execution

Best practice:

* Store remotely in S3
* Use DynamoDB locking

---

## Terraform Drift

Drift occurs when infrastructure changes manually outside Terraform.

Detect using:

```bash id="x0a8cx"
terraform plan
```

Resolution:

* Import changes
* Revert manual modifications
* Reapply Terraform

---

# Product / Hiring Round

## Products worked on

I explained:

* Product architecture
* Traffic scale
* Monitoring stack
* CI/CD implementation
* Infrastructure design
* Security controls
* Observability practices
* Incident management

---

## Tools/products used and why chosen over competitors

Examples:

* Loki vs ELK
* EKS vs ECS
* Jenkins vs GitHub Actions
* Prometheus vs Datadog

I explained tool selection depends on:

* Cost
* Scalability
* Team expertise
* Operational overhead
* Cloud integration
* Observability needs

---

## Understanding of their product offerings

I explained my understanding of:

* Their platform architecture
* Scaling requirements
* Reliability expectations
* Security challenges
* Monitoring needs
* High availability requirements

---

## What improvements or contributions I could bring to their platform

I suggested:

* Better observability
* Infrastructure automation
* Cost optimization
* Security hardening
* Improved CI/CD
* Better incident response
* SLO/SLA implementation
* Centralized monitoring/logging

---

# Asked to choose a database and justify decision

Instead of directly selecting a database, I explained that I would first gather requirements.

Questions to ask:

* Structured or unstructured data?
* Read-heavy or write-heavy?
* Strong consistency needed?
* Expected scale?
* Query patterns?
* HA/DR requirements?
* Latency expectations?

Then database selection:

* PostgreSQL/MySQL → relational workloads
* MongoDB → flexible schema
* Redis → caching/session storage
* DynamoDB/Cassandra → massive scale
* Elasticsearch → search workloads

The interviewer mainly evaluated structured decision-making and architecture thinking rather than direct technology selection.

---
