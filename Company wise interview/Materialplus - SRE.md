# Interview experience - Materialplus - SRE 🧳

---

## What happens when a node becomes NotReady?

* Node stops reporting heartbeats to API server
* Pods may become unreachable
* Scheduler avoids placing new pods on that node
* Existing pods may get evicted after timeout
* Workloads recreated on healthy nodes if managed by Deployment/ReplicaSet

Check:

```bash id="k8snotready1"
kubectl get nodes
kubectl describe node <node-name>
```

---

## Explain CrashLoopBackOff

* Container starts and repeatedly crashes
* Kubernetes retries restart with exponential backoff

Common causes:

* Wrong application config
* Missing env variables
* Port conflicts
* Memory issues
* Dependency failures

Troubleshoot:

```bash id="k8scrash1"
kubectl logs <pod>
kubectl describe pod <pod>
```

---

## Difference between HPA and VPA

| HPA                      | VPA                          |
| ------------------------ | ---------------------------- |
| Scales pods horizontally | Scales CPU/Memory vertically |
| Increases replicas       | Adjusts resources            |
| Based on metrics         | Based on usage patterns      |

---

## How does HPA work during traffic spikes?

1. Metrics Server collects CPU/memory metrics
2. HPA compares against threshold
3. Increases pod replicas automatically
4. Load distributed across pods

---

## How do you troubleshoot application downtime in Kubernetes?

1. Check pod status
2. Check logs/events
3. Verify service/endpoints
4. Check ingress/load balancer
5. Verify DNS/networking
6. Check node health

Commands:

```bash id="k8sdown1"
kubectl get pods
kubectl logs
kubectl describe pod
```

---

## How do you monitor Kubernetes clusters?

Tools:

* Prometheus
* Grafana
* ELK Stack
* CloudWatch Container Insights
* Datadog

Monitor:

* CPU
* Memory
* Pod health
* Network latency
* Node utilization

---

## Explain kubectl apply

* Sends YAML manifest to API Server
* Creates or updates Kubernetes resources
* Stores desired state in etcd

Command:

```bash id="kubectlapply1"
kubectl apply -f deployment.yaml
```

---

## Difference between Deployment, StatefulSet, and DaemonSet

| Deployment       | StatefulSet     | DaemonSet         |
| ---------------- | --------------- | ----------------- |
| Stateless apps   | Stateful apps   | One pod per node  |
| Random pod names | Stable identity | Runs on all nodes |

---

## How do you troubleshoot ImagePullBackOff?

* Verify image name/tag
* Check registry credentials
* Verify network access
* Ensure image exists

Commands:

```bash id="imagepull1"
kubectl describe pod <pod>
```

---

## How do services communicate inside Kubernetes?

* Using ClusterIP Services
* CoreDNS resolves service names
* kube-proxy routes traffic to pods

Example:

```text id="svccomm1"
service-name.namespace.svc.cluster.local
```

---

## Explain Terraform workflow from start to end

1. Write `.tf` code
2. Initialize:

```bash id="tfwf1"
terraform init
```

3. Validate:

```bash id="tfwf2"
terraform validate
```

4. Plan:

```bash id="tfwf3"
terraform plan
```

5. Apply:

```bash id="tfwf4"
terraform apply
```

6. Destroy if required:

```bash id="tfwf5"
terraform destroy
```

---

## What is Terraform state?

* Stores infrastructure mapping/state
* Tracks created resources

File:

```text id="tfstate1"
terraform.tfstate
```

---

## Why do we store Terraform state remotely?

* Team collaboration
* State locking
* Backup/versioning
* Avoid corruption

Example:

* S3 + DynamoDB

---

## Difference between count and for_each

| count         | for_each                    |
| ------------- | --------------------------- |
| Numeric index | Key-value based             |
| Less flexible | Better for unique resources |

---

## How do you detect infrastructure drift/manual changes?

* Run:

```bash id="tfdrift21"
terraform plan
```

* Compare actual infra vs Terraform state

---

## What happens if Terraform state file is deleted?

* Terraform loses resource tracking
* Existing infra may still exist
* Recover using:

```bash id="tfimport21"
terraform import
```

---

## How do you import existing resources into Terraform?

```bash id="tfimport22"
terraform import aws_instance.example i-123456
```

---

## Difference between local state and remote state

| Local State    | Remote State       |
| -------------- | ------------------ |
| Stored locally | Stored remotely    |
| Single-user    | Team collaboration |
| Higher risk    | Safer & scalable   |

---

## Which CI/CD tools do you have experience with?

* Jenkins
* GitHub Actions
* GitLab CI/CD
* ArgoCD

---

## Explain Jenkins/GitLab pipeline flow

1. Code push triggers webhook
2. Pipeline starts
3. Build & test stages
4. Docker image build
5. Security scans
6. Deployment to environment

---

## How are pipelines triggered?

* Git webhook
* Manual trigger
* Scheduled trigger
* API trigger

---

## What are GitLab runners?

* Agents executing GitLab CI jobs
* Can be:

  * Shared runners
  * Specific runners

---

## How do you handle deployment failures?

* Check logs/events
* Rollback deployment
* Analyze RCA
* Fix pipeline/configuration

---

## Explain rollback strategy in production

* Rolling rollback
* Blue-Green rollback
* Kubernetes rollout undo

Example:

```bash id="rollback1"
kubectl rollout undo deployment app
```

---

## How do you investigate a P1 production incident?

1. Identify impact
2. Restore service quickly
3. Check logs/metrics
4. Coordinate with teams
5. Perform RCA
6. Add preventive measures

---

## What will you do if Grafana dashboards show “No Data”?

* Check Prometheus target status
* Verify datasource connectivity
* Check exporters
* Verify query correctness
* Check network/firewall issues

---

## How do alerts work in monitoring systems?

1. Metrics collected
2. Alert rules evaluated
3. Threshold breached
4. AlertManager sends notification

---

## What metrics do you monitor?

* CPU
* Memory
* Disk
* Network
* Application latency
* Error rate
* Pod restarts

---

## How do Slack/Outlook alerts get triggered?

* AlertManager/Webhooks integrated with:

  * Slack
  * Email
  * Teams

---

## How do you identify latency/connectivity issues?

* Ping
* traceroute
* curl
* tcpdump
* Metrics dashboards
* Application traces

---

## Which Linux commands do you use daily?

```bash id="linuxcmd1"
top
df -h
free -m
ps -ef
netstat -tulnp
grep
tail -f
journalctl
```

---

## How do you troubleshoot VM-to-storage connectivity issues?

* Check mount status
* Verify network connectivity
* Check Security Groups/firewall
* Verify storage service health

---

## How do you check network connectivity in Linux?

```bash id="linuxnet1"
ping
telnet
nc
curl
traceroute
```

---

## Commands used for log monitoring and process troubleshooting

```bash id="linuxlog1"
tail -f
grep
journalctl
ps -ef
top
htop
```

---

## Experience with SonarQube?

* Used for static code analysis
* Detects:

  * Bugs
  * Vulnerabilities
  * Code smells
* Integrated into CI/CD pipeline

---

## How do vulnerability scans work in CI/CD?

* Scan code/images/dependencies during pipeline
  Tools:
* Trivy
* Snyk
* SonarQube
* Clair

---

## Difference between public and private APIs

| Public API          | Private API      |
| ------------------- | ---------------- |
| Internet accessible | Internal only    |
| External consumers  | Internal systems |

---

## How do you secure deployments and access?

* IAM least privilege
* RBAC
* Secrets management
* Image scanning
* TLS encryption
* MFA enabled access

---

## Application down after deployment — how will you troubleshoot?

1. Check rollout status
2. Check pod logs/events
3. Verify readiness/liveness probes
4. Check ingress/service
5. Rollback if needed

---

## Sudden traffic spike — how will autoscaling behave?

* Metrics increase
* HPA/ASG scales resources automatically
* New pods/instances launched

---

## Monitoring dashboard stopped receiving data — what could be the reasons?

* Exporter down
* Prometheus issue
* Network issue
* Wrong datasource
* Storage full

---

## Someone made manual production changes outside Terraform — how do you detect it?

* Run:

```bash id="tfmanual1"
terraform plan
```

* Drift detected when actual infra differs from Terraform state

---
