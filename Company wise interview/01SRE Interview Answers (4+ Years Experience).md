# 🚀 SRE Interview Answers (4+ Years Experience)

---

## • What are your roles and responsibilities as an SRE?

* Ensure system reliability, availability, and scalability
* Monitor production systems and respond to incidents
* Implement automation to reduce manual work
* Define and track SLIs, SLOs, SLAs
* Perform root cause analysis (RCA) and implement fixes
* Improve system performance and resilience

---

## • Which monitoring/observability tools have you used? (Prometheus, New Relic, etc.)

* Prometheus (metrics collection)
* Grafana (visualization dashboards)
* New Relic (APM, tracing, alerts)
* CloudWatch (AWS monitoring)
* ELK Stack (logs: Elasticsearch, Logstash, Kibana)

---

## • Why are you interested in this position?

* Strong interest in reliability engineering and large-scale systems
* Opportunity to work on production-critical systems
* Passion for automation, monitoring, and performance optimization

---

## • What value from your past experience will you bring to this role?

* Hands-on experience with AWS, Kubernetes, CI/CD
* Strong troubleshooting and incident handling skills
* Experience in building scalable and fault-tolerant systems
* Automation mindset to improve efficiency

---

## • What is your core experience mainly focused on?

* AWS cloud infrastructure
* Kubernetes (EKS)
* CI/CD pipelines (Jenkins)
* Infrastructure as Code (Terraform)
* Monitoring and observability

---

## • How would you design a scalable system (like a routing/web application) on AWS?

* Use ALB for routing traffic
* Deploy application on EKS/EC2 with Auto Scaling
* Use RDS (Multi-AZ) for database
* Use S3 + CloudFront for static content
* Implement caching using Redis (ElastiCache)
* Add Route53 for DNS routing

---

## • How do you troubleshoot network-related issues?

* Check connectivity (ping, curl, telnet)
* Verify security groups and NACLs
* Check route tables
* Analyze VPC flow logs
* Validate DNS resolution

---

## • How would you handle a continuous attack from a specific IP?

* Block IP using Security Groups / NACL
* Use AWS WAF to filter traffic
* Enable rate limiting
* Use CloudFront + Shield for DDoS protection

---

## • How do you run/host your services in production?

* Containerized applications using Docker
* Deploy on Kubernetes (EKS)
* Use CI/CD pipelines for automated deployments
* Use load balancers for traffic distribution

---

## • How do you ensure high availability?

* Multi-AZ deployment
* Auto Scaling
* Load balancing
* Health checks
* Database replication (Multi-AZ / Read replicas)

---

## • What monitoring/observability approach would you use?

* Metrics → Prometheus
* Logs → ELK
* Traces → New Relic / OpenTelemetry
* Alerts → Alertmanager / CloudWatch

---

## • What would you do if monitoring tools are down but latency/errors are high?

* Check system logs manually
* Use kubectl logs / describe
* Check application logs on servers
* Verify resource usage (CPU, memory)
* Perform basic health checks (curl endpoints)

---

## • How do you troubleshoot high latency in a specific API?

* Check APM traces (New Relic)
* Analyze logs
* Check DB query performance
* Verify network latency
* Identify bottlenecks (CPU/memory/thread issues)

---

## • How do you handle long-running database queries?

* Identify slow queries
* Add indexing
* Optimize query structure
* Use caching
* Tune database parameters

---

## • Which databases have you worked with?

* MySQL
* PostgreSQL
* MongoDB

---

## • How have you used APM tools like New Relic?

* Monitor application performance
* Trace requests across services
* Identify slow transactions
* Set alerts for latency/errors

---

## • What backend technologies do you use?

* Java (Spring Boot)
* Node.js (basic understanding)

---

## • How do you instrument Java microservices (OpenTelemetry)?

* Add OpenTelemetry Java agent
* Configure exporter (Jaeger/New Relic)
* Enable auto-instrumentation

---

## • Do you use code-based or agent-based instrumentation?

* Mostly agent-based (easy and faster)
* Code-based when custom tracing is needed

---

## • Where do you attach the OpenTelemetry agent?

* Attach as JVM argument:

  ```bash
  -javaagent:/path/to/opentelemetry-javaagent.jar
  ```

---

## • Did you use Kafka? Managed or self-managed?

* Yes, used Kafka
* Worked with managed service (AWS MSK)

---

## • Kafka vs SQS – when do you use each?

| Kafka                     | SQS                  |
| ------------------------- | -------------------- |
| High throughput streaming | Simple message queue |
| Event-driven architecture | Decoupling services  |
| Real-time data processing | Background jobs      |

---

## • What Kafka operational issues have you faced?

* Consumer lag
* Broker failures
* Partition imbalance
* Message retention issues

---

## • How do you handle high memory usage in Java services?

* Analyze heap usage (JVM tools)
* Enable garbage collection tuning
* Identify memory leaks
* Increase memory limits if required

---

## • Do you have scripting experience (JavaScript for synthetic monitoring)?

* Yes, used JavaScript for API monitoring scripts
* Automated health checks and validations

---

## • What do you use for Infrastructure as Code?

* Terraform

---

## • Where do you store Terraform state files?

* Remote backend (S3 bucket)
* With DynamoDB for state locking

---

## • What if a resource is deleted and Terraform config is missing?

* Import existing resource (if exists)
* Recreate configuration manually

---

## • How do you recover configuration if you don’t remember it?

* Check AWS console
* Use CloudTrail logs
* Review existing infrastructure
* Recreate using best practices

---

## • What if the resource is deleted – how will you recreate it?

* Use Terraform if config exists
* Otherwise recreate manually using console or scripts
* Restore from backups if needed

---

## • How do you use CloudTrail for troubleshooting?

* Track API calls
* Identify who made changes
* Analyze event history for issues

---

## • Write a script to generate unique random numbers (Python)

```python
import random

nums = random.sample(range(1, 100), 10)
print(nums)
```

---

## • How to generate unique numbers without using libraries?

```python
nums = []
n = 10
i = 1

while len(nums) < n:
    if i not in nums:
        nums.append(i)
    i += 1

print(nums)
```

---

## ✅ Final Tip

Focus on:

* Real scenarios
* Clear explanations
* Confidence while answering

---
