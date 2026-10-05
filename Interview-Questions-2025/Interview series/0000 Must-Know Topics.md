# SRE / DevOps Interview Notes (4+ Years Experience)

## How to answer in interviews

For 4 years of experience, keep answers practical and structured. Speak in this flow:
**problem → approach → tools/commands → result → prevention**.

---

# 1. Core Concepts (Short Notes)

## SLA, SLI, SLO

* **SLI (Service Level Indicator):** The actual metric you measure, such as latency, availability, error rate, or throughput.
* **SLO (Service Level Objective):** The target you set for that metric, such as 99.9% availability.
* **SLA (Service Level Agreement):** The business contract with the customer, usually tied to penalties if the SLO is breached.

**Interview-ready line:**
"In practice, SLI tells us what we are measuring, SLO defines the target, and SLA is the business commitment around that target."

---

## IaaS, PaaS, SaaS

* **IaaS:** Infrastructure provided by cloud vendor; you manage OS, runtime, and application. Example: EC2.
* **PaaS:** Platform provided; you mainly deploy your application. Example: Elastic Beanstalk.
* **SaaS:** Fully managed software used directly by end users. Example: Gmail, Office 365.

**Interview-ready line:**
"IaaS gives maximum control, PaaS reduces operational effort, and SaaS is ready to use with almost no infrastructure management."

---

## EKS Upgrade

A safe EKS upgrade usually follows this order:

1. Check current cluster version and AWS upgrade support.
2. Review API deprecations.
3. Upgrade the control plane.
4. Upgrade managed node groups.
5. Update add-ons like CoreDNS, kube-proxy, and VPC CNI.
6. Validate workloads, autoscaling, and ingress.

**Common challenges:**

* Deprecated Kubernetes APIs
* Add-on version mismatch
* Node cordon/drain impact
* Pod disruption during upgrade

**Interview-ready line:**
"I upgrade the control plane first, then node groups and add-ons, and I validate workload health after every step to avoid disruption."

---

## CI/CD

CI/CD is the automated flow from code commit to build, test, package, deploy, and verify.

Typical flow:

* Developer pushes code
* Pipeline runs build and tests
* Artifact or Docker image is created
* Image is pushed to registry
* Deployment happens in Kubernetes or VM environment
* Post-deploy validation and monitoring confirm success

**Interview-ready line:**
"CI/CD helps us reduce manual effort, improve release consistency, and catch issues early before they reach production."

---

## Infrastructure as Code (Terraform)

Terraform is used to provision and manage infrastructure in a repeatable way.

Common workflow:

```bash
terraform init
terraform plan
terraform apply
terraform destroy
```

**Interview-ready line:**
"Terraform helps us version infrastructure, automate provisioning, and maintain consistency across environments."

---

## Kubernetes Basics

* **Pod:** Smallest deployable unit.
* **Deployment:** Manages desired pod replicas and rollout.
* **Service:** Exposes pods internally or externally.
* **ConfigMap:** Stores non-sensitive configuration.
* **Secret:** Stores sensitive values.
* **Ingress:** HTTP/HTTPS routing into the cluster.

**Interview-ready line:**
"In Kubernetes, Deployment manages application lifecycle, Service exposes it, and ConfigMaps or Secrets provide configuration."

---

# 2. Golden Question

## Incident occurred in production, how will you handle it?

**Best interview answer:**
"My first priority is service restoration. I quickly assess the severity, identify the blast radius, and inform relevant stakeholders. Then I check logs, metrics, and recent changes to isolate the issue. If the problem is related to a new deployment, I roll back or scale the service to stabilize production. After recovery, I perform root cause analysis, document the findings, and make preventive changes such as alert tuning, health checks, or deployment guardrails."

**Strong sequence to remember:**

1. Detect and acknowledge
2. Classify severity
3. Mitigate fast
4. Communicate clearly
5. Investigate root cause
6. Fix permanently
7. Run postmortem
8. Add prevention measures

**Good closing line:**
"In production incidents, I focus first on restoring service, and then on root cause analysis and prevention."

---

# 3. Postmortem

## Blameless postmortem

A blameless postmortem focuses on system failure rather than individual fault.

**What it should include:**

* What happened
* Impact
* Timeline
* Root cause
* Detection gaps
* Recovery steps
* Action items

**Interview-ready line:**
"A good postmortem should improve the system, not assign blame. The goal is to prevent the same incident from repeating."

---

# 4. Kubernetes Troubleshooting

## Pod not starting

Possible reasons:

* Image pull issue
* Resource shortage
* Invalid config or secret
* Wrong node selector or taint/toleration mismatch

What to check:

```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl get events --sort-by=.metadata.creationTimestamp
```

**Interview-ready line:**
"I start with events and logs because they usually tell me whether the issue is scheduling, image pull, configuration, or runtime related."

---

## CrashLoopBackOff

Possible reasons:

* Application crash on startup
* Missing environment variables
* Bad config
* Insufficient memory
* Dependency not available

What to check:

```bash
kubectl logs <pod-name> --previous
kubectl describe pod <pod-name>
```

**Interview-ready line:**
"CrashLoopBackOff usually means the application starts and then exits repeatedly, so I check logs from the previous container instance first."

---

## Service not reachable

Check:

* Service type
* Port mapping
* Endpoints
* Ingress rules
* Security groups / network policies if applicable

Commands:

```bash
kubectl get svc
kubectl get endpoints
kubectl describe svc <service-name>
```

**Interview-ready line:**
"If a service is not reachable, I verify whether the issue is at the pod, service, ingress, or network layer."

---

## Node issues

Check:

* Node status
* Kubelet health
* CPU/memory/disk pressure
* Network connectivity
* Cordon/drain state

Commands:

```bash
kubectl get nodes
kubectl describe node <node-name>
```

**Interview-ready line:**
"When a node is unhealthy, I check whether the node is Ready, whether it has pressure conditions, and whether workloads need to be moved."

---

# 5. Prometheus and Grafana (Short Notes)

## Prometheus

Prometheus is an open-source monitoring tool used to collect and store time-series metrics.

**Key points:**

* Pull-based monitoring model
* Scrapes metrics from exporters and targets
* Uses PromQL for querying
* Stores metrics in time-series format
* Commonly used for CPU, memory, disk, pod, and application metrics

**Interview-ready line:**
"Prometheus is mainly used for metrics collection, alerting integration, and time-series analysis in Kubernetes and cloud environments."

---

## Grafana

Grafana is a visualization and dashboard tool used to display monitoring data.

**Key points:**

* Reads data from Prometheus and other sources
* Builds dashboards and graphs
* Useful for real-time visibility
* Supports alerting and sharing dashboards

**Interview-ready line:**
"Grafana helps teams visualize metrics clearly so they can identify trends, anomalies, and failures quickly."

---

## Prometheus + Grafana flow

1. Prometheus scrapes metrics
2. Metrics are stored in the Prometheus time-series database
3. Grafana queries Prometheus
4. Dashboards display metrics
5. Alerts notify the team when thresholds are breached

**Real project answer:**
"In production, Prometheus was used for metric collection and Grafana for dashboards and alert views, which helped us identify issues before they impacted users."

---

# 6. Python Scripting Interview Answer

## Smart way to answer

"I can read and troubleshoot Python scripts well. In my current organization, we also use tools like Copilot to support scripting and automation. Given an opportunity, I can quickly learn, debug, and contribute effectively."

**What they expect from 4 years experience:**

* Basic scripting understanding
* Debugging ability
* File handling
* Loops, conditions, functions
* Ability to automate small operational tasks

---

# 7. Python Interview – 20+ Short Exercises

## 1. Reverse a string

```python
print("devops"[::-1])
```

## 2. Check palindrome

```python
s = "madam"
print(s == s[::-1])
```

## 3. Factorial

```python
n = 5
fact = 1
for i in range(1, n + 1):
    fact *= i
print(fact)
```

## 4. Fibonacci series

```python
a, b = 0, 1
for _ in range(5):
    print(a)
    a, b = b, a + b
```

## 5. Largest number in a list

```python
print(max([1, 5, 3]))
```

## 6. Count vowels in a string

```python
print(sum(1 for c in "hello" if c in "aeiou"))
```

## 7. Remove duplicates from a list

```python
print(list(set([1, 1, 2, 3])))
```

## 8. Sort a list

```python
print(sorted([3, 1, 2]))
```

## 9. Check even or odd

```python
print("Even" if 4 % 2 == 0 else "Odd")
```

## 10. Merge two lists

```python
print([1, 2] + [3, 4])
```

## 11. Find common elements

```python
print(list(set([1, 2, 3]) & set([2, 3, 4])))
```

## 12. Loop through dictionary

```python
for k, v in {"a": 1, "b": 2}.items():
    print(k, v)
```

## 13. Swap two numbers

```python
a, b = 1, 2
a, b = b, a
print(a, b)
```

## 14. Check prime number

```python
n = 7
print(all(n % i for i in range(2, n)))
```

## 15. Count characters in a string

```python
from collections import Counter
print(Counter("hello"))
```

## 16. Read a file

```python
# with open("file.txt") as f:
#     print(f.read())
```

## 17. Write to a file

```python
# with open("file.txt", "w") as f:
#     f.write("DevOps")
```

## 18. Simple API call

```python
# import requests
# print(requests.get("https://api.github.com").status_code)
```

## 19. Exception handling

```python
try:
    x = 1 / 0
except Exception as e:
    print(e)
```

## 20. Sum of list

```python
print(sum([1, 2, 3]))
```

## 21. Find length of string

```python
print(len("devops"))
```

## 22. Print even numbers from a list

```python
nums = [1, 2, 3, 4, 5, 6]
print([n for n in nums if n % 2 == 0])
```

## 23. Find maximum of three numbers

```python
print(max(10, 25, 17))
```

## 24. Reverse words in a sentence

```python
s = "hello devops world"
print(" ".join(s.split()[::-1]))
```

## 25. Check if a number is divisible by 5

```python
n = 25
print(n % 5 == 0)
```

**Interview-ready line for Python:**
"I may not use Python daily for application development, but I can read, debug, and use it effectively for automation and operational tasks."

---

# 8. Final Interview Strategy

## What to focus on

* Strong fundamentals
* Real production scenarios
* Troubleshooting flow
* Communication during incidents
* Confidence in explaining trade-offs

## How to answer

* Keep answers short and structured
* Mention tools used in real projects
* Show what you did, not just what you know
* Always connect the answer to production reliability

## Strong closing line

"My focus in DevOps and SRE is to keep systems reliable, automate repetitive work, respond quickly to incidents, and continuously improve stability."
