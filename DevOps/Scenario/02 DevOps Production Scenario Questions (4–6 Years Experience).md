# DevOps Production Scenario Questions (4–6 Years Experience)

This document contains **real-world DevOps production scenarios** often asked in **interviews for 4–6 years experience**. Each question includes a **clear paragraph-style answer** along with important troubleshooting commands.

---

# 1. Your API suddenly receives 1 million requests per minute. What will you do?

If an API suddenly receives extremely high traffic, the first step is to ensure the system can handle the load without crashing. I would start by checking whether the traffic is legitimate or a potential DDoS attack. Then I would enable **auto-scaling** so that additional application instances can be launched automatically. I would also place a **load balancer** in front of the application to distribute traffic across multiple servers. Implementing **rate limiting** and **API throttling** helps prevent abuse from a single user or IP. To reduce pressure on backend systems, I would introduce a **caching layer such as Redis** and use a **CDN** to serve static content. Monitoring tools like Prometheus and Grafana would help track system health during the traffic spike.

Important architecture example:

```
Users
 ↓
CDN
 ↓
Load Balancer
 ↓
Auto Scaling Application Servers
 ↓
Cache (Redis)
 ↓
Database
```

---

# 2. Kubernetes pods keep crashing (CrashLoopBackOff). How do you debug?

When a Kubernetes pod is in **CrashLoopBackOff**, it means the container starts but crashes repeatedly. My first step is to check the pod status using `kubectl get pods`. Then I examine the logs of the container using `kubectl logs` to identify application errors. I also run `kubectl describe pod` to view events such as image pull failures, configuration issues, or probe failures. Common causes include incorrect environment variables, dependency issues, insufficient memory limits, or failing liveness probes. After identifying the root cause, I fix the configuration or application issue and redeploy the pod.

Commands:

```
kubectl get pods
kubectl logs <pod-name>
kubectl describe pod <pod-name>
```

---

# 3. CI/CD pipeline suddenly takes 45 minutes instead of 10 minutes.

If a CI/CD pipeline suddenly becomes slow, I begin by checking recent changes in the pipeline configuration or dependencies. I analyze the build logs to determine which stage is taking the most time, such as dependency downloads, Docker builds, or test execution. Often the problem is caused by **missing build cache** or downloading dependencies repeatedly. I optimize the pipeline by enabling **dependency caching**, using **Docker layer caching**, running jobs in **parallel**, and using **pre-built base images**. I also verify that the build agents have sufficient CPU and memory resources.

---

# 4. Database CPU suddenly spikes to 95%.

A sudden database CPU spike usually indicates heavy queries or too many connections. I start by checking active queries using database tools like `SHOW PROCESSLIST` in MySQL or monitoring dashboards. I analyze slow queries and use `EXPLAIN` to understand query execution plans. If necessary, I add **indexes**, optimize queries, or introduce **read replicas** to distribute read traffic. Sometimes application bugs or sudden traffic spikes cause this issue, so I also review application logs and monitoring metrics.

Example commands:

```
SHOW PROCESSLIST;
EXPLAIN SELECT * FROM users WHERE email='example@test.com';
```

---

# 5. Users report slow application response.

When users report slow performance, I investigate the issue across multiple layers including the application, infrastructure, database, and network. I check application logs for errors and examine monitoring dashboards to analyze CPU, memory, and latency metrics. If database queries are slow, I optimize them or add caching using Redis. I also review network latency and load balancer performance. Tools such as **Prometheus, Grafana, New Relic, or Datadog** help identify performance bottlenecks quickly.

---

# 6. One microservice is failing but others are working.

In a microservices architecture, if one service fails while others remain operational, I first check the logs of that specific service. I verify whether the service has connectivity issues with its dependencies such as databases or message queues. I also confirm that service discovery and networking configurations are correct. Using Kubernetes commands like `kubectl logs` and `kubectl describe service`, I can identify configuration or deployment issues. Once the root cause is found, I redeploy or fix the service configuration.

Commands:

```
kubectl logs <pod>
kubectl describe service <service-name>
```

---

# 7. Disk usage on server reaches 100%.

If disk usage reaches 100%, the system may stop functioning properly. I start by checking disk utilization using `df -h`. Then I identify large files or directories using `du -sh`. Often the issue is caused by application logs growing indefinitely. I clean up unnecessary files, configure **log rotation**, or move large data to external storage. In cloud environments, expanding the disk volume may also be required.

Commands:

```
df -h
du -sh *
```

---

# 8. Application suddenly crashes in production.

When an application crashes in production, the first step is to check application logs and monitoring dashboards to understand what happened before the crash. I also review system resources like CPU, memory, and disk usage. If the crash happened after a recent deployment, I immediately **rollback to the previous stable version**. After restoring service availability, I analyze logs and metrics to identify the root cause and prevent future occurrences.

---

# 9. Kubernetes cluster nodes become NotReady.

If Kubernetes nodes become **NotReady**, workloads may stop functioning. I check the node status using `kubectl get nodes` and then inspect the node using `kubectl describe node`. Common reasons include kubelet service failure, network connectivity issues, or resource exhaustion. Restarting kubelet, fixing network configuration, or scaling the cluster may resolve the issue.

Commands:

```
kubectl get nodes
kubectl describe node <node-name>
```

---

# 10. Your website is down but servers are running.

If the servers are running but the website is not accessible, the problem may exist in the **network or routing layer**. I verify the load balancer configuration, DNS records, and SSL certificates. I also check whether the application service is running properly on the server. Tools like `curl`, load balancer logs, and DNS checks help determine where the issue lies.

---

# 11. Deployment caused production outage.

If a deployment causes an outage, the first priority is restoring service availability. I immediately perform a **rollback** to the previous stable version. After stabilizing the system, I analyze logs, configuration changes, and deployment differences to determine the root cause. To prevent future issues, I implement strategies such as **blue-green deployments or canary releases**.

Command example:

```
kubectl rollout undo deployment app
```

---

# 12. Memory usage keeps increasing in containers.

Continuous memory growth usually indicates a **memory leak** in the application. I monitor container memory usage using `docker stats` or `kubectl top pods`. Temporarily restarting the container may restore service, but the real solution requires fixing the application code. Profiling tools and monitoring dashboards help identify the exact component causing the leak.

Commands:

```
docker stats
kubectl top pods
```

---

# 13. Kubernetes service is not reachable.

If a Kubernetes service cannot be accessed, I first verify that the service exists using `kubectl get svc`. Then I check whether endpoints are correctly mapped to pods using `kubectl get endpoints`. Often the issue occurs because the **pod labels do not match the service selector**, preventing traffic from reaching the pods.

Commands:

```
kubectl get svc
kubectl get endpoints
```

---

# 14. Log files grow too large.

Large log files can consume disk space quickly. I implement **log rotation** using tools like `logrotate` so that old logs are archived and compressed automatically. Centralized logging solutions such as **ELK stack or Grafana Loki** can also help store and analyze logs efficiently.

Example configuration:

```
/var/log/app.log {
    daily
    rotate 7
    compress
}
```

---

# 15. Application deployment needs zero downtime.

To achieve zero downtime deployment, I use strategies such as **rolling deployments, blue-green deployments, or canary releases**. These methods gradually replace old application instances with new ones while keeping the service available to users. Kubernetes supports rolling updates by default, ensuring that new pods start before old ones are terminated.

Command example:

```
kubectl rollout status deployment app
```

---

# Summary

A DevOps engineer with **4–6 years of experience** is expected to handle:

* Production troubleshooting
* Infrastructure scaling
* CI/CD pipeline optimization
* Monitoring and alerting
* Kubernetes debugging
* Incident management
* High availability architecture

These scenarios demonstrate the **practical decision-making and problem-solving skills** required in real production environments.

---
