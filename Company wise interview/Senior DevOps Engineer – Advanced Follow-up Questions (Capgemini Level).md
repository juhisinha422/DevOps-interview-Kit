# Senior DevOps Engineer – Advanced Follow-up Questions (Capgemini Level)

These are **tricky follow-up questions** commonly asked after basic DevOps answers.
They test **depth, real production experience, and decision-making skills** for **4–6 years experience**.

---

## 🔹 Your Jenkins pipeline fails intermittently. How do you debug and stabilize it?

Intermittent failures usually indicate **non-deterministic issues** such as network instability, resource contention, or dependency failures. I would start by analyzing Jenkins console logs and identifying which stage fails randomly. Then I would check for external dependencies like artifact repositories, APIs, or Docker registries. I would enable **retry mechanisms** for unstable steps, ensure proper **timeouts**, and use **pipeline-level error handling**. Additionally, I would verify Jenkins agent stability, CPU/memory usage, and disk space. If needed, I would isolate builds using **dedicated agents or containers** to ensure consistency.

---

## 🔹 How do you secure Jenkins credentials and prevent leakage in pipelines?

To secure credentials, I always use **Jenkins Credentials Store** instead of hardcoding secrets. Sensitive data is injected using `withCredentials` and masked in logs. I ensure that secrets are not printed using `echo`. Role-Based Access Control (RBAC) is implemented to restrict access. For higher security, I integrate Jenkins with external secret managers like **HashiCorp Vault or AWS Secrets Manager**. I also enforce credential rotation and audit usage regularly.

---

## 🔹 What happens if a Kubernetes pod passes readiness probe but fails liveness probe?

If a pod passes the readiness probe, it is considered ready to receive traffic. However, if it fails the liveness probe, Kubernetes will **restart the container**. This means the pod might temporarily serve traffic but will be restarted once liveness fails. This situation usually indicates application instability. Proper configuration of probes is critical to avoid unnecessary restarts.

---

## 🔹 How do you handle zero-downtime database migrations in production?

For zero-downtime migrations, I follow **backward-compatible changes**. First, I deploy schema changes that do not break existing functionality, such as adding new columns instead of modifying existing ones. Then I update the application to use the new schema. Techniques like **blue-green deployment**, feature flags, and rolling updates ensure no downtime. In high-scale systems, I use tools like **Flyway or Liquibase** for controlled migrations.

---

## 🔹 Your Docker image works locally but fails in Kubernetes. Why?

This usually happens due to **environment differences**. In Kubernetes, issues may arise from missing environment variables, incorrect resource limits, networking problems, or volume mounts. I check pod logs using `kubectl logs`, inspect environment variables, and verify configurations like ConfigMaps and Secrets. I also ensure the container runs correctly without root privileges and follows Kubernetes constraints.

---

## 🔹 How do you design a highly available Kubernetes cluster?

A highly available Kubernetes cluster requires **multiple control plane nodes** and **worker nodes across availability zones**. I ensure etcd is replicated and backed up. Load balancers are used in front of API servers. Worker nodes are auto-scaled using cluster autoscaler. Networking should be resilient using a reliable CNI plugin. Monitoring and alerting are implemented using Prometheus and Grafana. This ensures no single point of failure.

---

## 🔹 What is the difference between horizontal and vertical pod autoscaling?

Horizontal Pod Autoscaler (HPA) scales the number of pods based on metrics like CPU or memory usage, while Vertical Pod Autoscaler (VPA) adjusts the resource requests and limits of a container. HPA is commonly used for stateless applications, whereas VPA is used when scaling vertically is more efficient. In production, both can be combined carefully but not on the same resource metric.

---

## 🔹 How do you troubleshoot networking issues in Kubernetes?

I start by checking whether pods can communicate using `kubectl exec` and tools like `curl` or `ping`. Then I verify services, endpoints, and DNS resolution using `kubectl get svc` and `kubectl get endpoints`. I also check network policies that might be blocking traffic. If required, I inspect the CNI plugin logs. Tools like `kubectl describe` and `nslookup` inside pods help identify issues.

---

## 🔹 Your application is memory-intensive. How do you prevent OOMKilled errors?

To prevent OOMKilled errors, I properly configure **resource requests and limits** in Kubernetes. I monitor memory usage using tools like Prometheus. If the application has memory leaks, I fix them at the code level. I may also increase memory limits or use horizontal scaling. JVM-based applications require tuning of heap memory settings.

---

## 🔹 How do you implement blue-green deployment in Kubernetes?

In blue-green deployment, I maintain two environments: **blue (current)** and **green (new)**. I deploy the new version to the green environment and test it. Once verified, I switch traffic from blue to green using a service or load balancer. If issues occur, I can quickly roll back by switching traffic back to blue. This ensures zero downtime and safe deployments.

---

## 🔹 How do you handle secrets securely in Kubernetes?

Secrets are stored in Kubernetes using **Secrets objects**, but for better security, I integrate external secret managers like AWS Secrets Manager or Vault. Secrets are mounted as environment variables or volumes. I ensure RBAC policies restrict access, and secrets are encrypted at rest using etcd encryption.

---

## 🔹 What is the difference between ConfigMap and Secret?

ConfigMaps store non-sensitive configuration data, while Secrets store sensitive information like passwords or tokens. Secrets are base64 encoded and can be encrypted, whereas ConfigMaps are plain text. Both are injected into pods as environment variables or files.

---

## 🔹 Your production deployment failed. How do you perform root cause analysis?

I start by collecting logs, metrics, and events from the system. I identify the exact time of failure and correlate it with recent changes such as deployments or configuration updates. I analyze application logs, Kubernetes events, and monitoring dashboards. Once the root cause is identified, I document it and implement preventive measures such as better testing or monitoring.

---

## 🔹 How do you optimize Docker image security?

I use minimal base images like Alpine, remove unnecessary packages, and avoid running containers as root. I implement multi-stage builds to reduce attack surface. I scan images using tools like Trivy or Snyk and regularly update dependencies. Secrets are never stored in images.

---

## 🔹 What is your approach to incident management in production?

During an incident, my priority is to **restore service quickly**. I follow incident response procedures such as identifying impact, rolling back changes if needed, and communicating with stakeholders. After resolution, I perform a **post-mortem analysis** to identify root causes and prevent recurrence. Monitoring and alerting improvements are also implemented.

---

# Final Interview Tip

At **Capgemini / similar companies**, interviewers expect:

* Not just tools knowledge, but **real production thinking**
* Clear explanation of **why + how**
* Ability to **handle failures, scale systems, and secure infrastructure**

---
