# DevOps / SRE Production Troubleshooting Interview Guide

## Kubernetes | AWS EKS | Datadog | Kafka | Elasticsearch | MongoDB | Python | Shell

This guide is designed for a **DevOps Engineer with around 4 years of experience**, focusing on production troubleshooting and scenario-based interview questions.

The goal is not just to remember commands, but to explain:

```text
Symptom
   ↓
Impact
   ↓
Evidence
   ↓
Isolation
   ↓
Root Cause
   ↓
Fix
   ↓
Validation
   ↓
Prevention
```

---

# ☁️ 1. Kubernetes / EKS

## Q1. After a production deployment, Pods go into CrashLoopBackOff. How would you troubleshoot it step by step?

### Answer

I would first establish whether the issue is related to the deployment itself or an existing application problem.

### Step 1: Check Pod status

```bash
kubectl get pods -n <namespace>
```

Look for:

```text
CrashLoopBackOff
Error
OOMKilled
ImagePullBackOff
Pending
```

Also check restart counts:

```bash
kubectl get pods -n <namespace> -o wide
```

---

### Step 2: Describe the Pod

```bash
kubectl describe pod <pod-name> -n <namespace>
```

I would focus on:

```text
Events
Container State
Last State
Exit Code
Reason
Liveness Probe
Readiness Probe
Startup Probe
```

For example:

```text
Last State:
  Terminated:
    Reason: OOMKilled
    Exit Code: 137
```

This immediately gives me a direction.

---

### Step 3: Check application logs

```bash
kubectl logs <pod-name> -n <namespace>
```

If there are multiple containers:

```bash
kubectl logs <pod-name> \
  -c <container-name> \
  -n <namespace>
```

---

### Step 4: Check previous container logs

This is particularly important for CrashLoopBackOff.

```bash
kubectl logs <pod-name> \
  --previous \
  -n <namespace>
```

The previous container may have crashed before I had a chance to inspect the current container.

---

### Step 5: Check deployment changes

Since this happened immediately after deployment, I would compare the current deployment with the previous version.

```bash
kubectl rollout history deployment/<deployment-name> \
  -n <namespace>
```

Check the deployment:

```bash
kubectl describe deployment <deployment-name> -n <namespace>
```

I would look for changes to:

* Container image
* Environment variables
* ConfigMaps
* Secrets
* Resource requests/limits
* Commands/arguments
* Probes
* Volume mounts
* Service configuration

---

### Step 6: Check image

```bash
kubectl get deployment <deployment-name> \
  -n <namespace> \
  -o jsonpath='{.spec.template.spec.containers[*].image}'
```

Verify that the expected image/tag was deployed.

I would also check whether the image itself starts correctly.

---

### Step 7: Check configuration

```bash
kubectl get configmap -n <namespace>
kubectl get secrets -n <namespace>
```

I would verify whether the new deployment expects any environment variables or secrets that are missing.

I would not expose secret values while troubleshooting.

---

### Step 8: Check probes

A very common production issue is an incorrectly configured health probe.

```bash
kubectl describe pod <pod-name> -n <namespace>
```

Look for:

```text
Liveness probe failed
Readiness probe failed
Startup probe failed
```

For example, if the application needs 2 minutes to start but the liveness probe starts checking after only 10 seconds, Kubernetes could continuously kill the container.

---

### Step 9: Check resources

```bash
kubectl top pod <pod-name> -n <namespace>
```

Check:

```bash
kubectl describe pod <pod-name> -n <namespace>
```

Look for:

```yaml
resources:
  requests:
  limits:
```

If the container is:

```text
OOMKilled
Exit Code: 137
```

I would investigate memory usage and container limits.

---

### Step 10: Check dependencies

The application may be crashing because it cannot connect to:

```text
MongoDB
PostgreSQL
Redis
Kafka
External API
S3
Secrets Manager
```

I would verify connectivity and application logs.

---

### Step 11: Compare with previous version

If the previous version was healthy, I would compare:

```text
Image
Config
Environment variables
Secrets
Resources
Probes
Dependencies
Application code/configuration
```

---

### Step 12: Roll back if production impact is high

If the new release is confirmed as the cause:

```bash
kubectl rollout undo deployment/<deployment-name> \
  -n <namespace>
```

Then:

```bash
kubectl rollout status deployment/<deployment-name> \
  -n <namespace>
```

---

### Strong interview answer

> "Since the CrashLoopBackOff started after deployment, I would first check Pod status, restart count and events using `kubectl get pods` and `kubectl describe pod`. Then I would check both current and previous container logs. I would inspect the exit code to determine whether it is an application crash, OOMKilled, probe failure or configuration issue. Since this happened after deployment, I would compare the new image, ConfigMaps, Secrets, probes, resources and environment variables with the previous release. If the deployment is confirmed to be the cause and production impact is high, I would roll back using `kubectl rollout undo`, validate the rollback, and then perform RCA before redeploying."

---

# Q2. An application running in EKS cannot access S3 and returns AccessDenied. What would you check?

### Answer

I would first determine **which AWS identity the Pod is using**.

The main areas I would check are:

```text
Pod
 ↓
Service Account
 ↓
IAM Role
 ↓
IAM Policy
 ↓
S3 Bucket Policy
 ↓
S3/KMS permissions
 ↓
AWS Network configuration
```

---

## Step 1: Identify the Kubernetes ServiceAccount

```bash
kubectl get pod <pod-name> -n <namespace> \
  -o jsonpath='{.spec.serviceAccountName}'
```

Then:

```bash
kubectl get serviceaccount <service-account> \
  -n <namespace> -o yaml
```

In EKS with IRSA, I would check for the IAM role annotation.

Example:

```yaml
annotations:
  eks.amazonaws.com/role-arn: arn:aws:iam::<account-id>:role/<role-name>
```

---

## Step 2: Verify IAM role

Check the role:

```bash
aws iam get-role \
  --role-name <role-name>
```

I would verify the trust relationship allows the EKS OIDC provider and correct Kubernetes ServiceAccount.

---

## Step 3: Check IAM permissions

```bash
aws iam list-attached-role-policies \
  --role-name <role-name>
```

Also check inline policies:

```bash
aws iam list-role-policies \
  --role-name <role-name>
```

I would verify required actions such as:

```text
s3:GetObject
s3:PutObject
s3:ListBucket
```

depending on what the application actually needs.

---

## Step 4: Check resource-level permissions

For example:

```text
arn:aws:s3:::my-bucket
```

and:

```text
arn:aws:s3:::my-bucket/*
```

are different resources.

`ListBucket` generally applies to the bucket, while `GetObject`/`PutObject` applies to objects.

---

## Step 5: Check S3 bucket policy

Even if IAM allows access, the bucket policy could explicitly deny it.

```bash
aws s3api get-bucket-policy \
  --bucket <bucket-name>
```

Look for:

```text
Explicit Deny
Principal restrictions
VPC endpoint restrictions
Source IP restrictions
Organization restrictions
```

Remember:

> An explicit `Deny` overrides an `Allow`.

---

## Step 6: Check KMS permissions

If the S3 bucket uses SSE-KMS, I would also check whether the IAM role has permissions such as:

```text
kms:Decrypt
kms:Encrypt
kms:GenerateDataKey
```

depending on the operation.

---

## Step 7: Verify the identity from the Pod

If appropriate and the AWS CLI is available inside the container:

```bash
aws sts get-caller-identity
```

This is extremely useful because it tells me **which AWS identity the application is actually using**.

---

## Step 8: Check CloudTrail

I would use CloudTrail to identify:

```text
Who made the request?
What API was called?
Which resource was accessed?
Why was it denied?
```

This is especially useful when the IAM configuration looks correct but access is still denied.

---

### Strong interview answer

> "For an S3 AccessDenied issue from EKS, I would first identify the AWS identity used by the Pod. I would verify the ServiceAccount and its IAM role, then check the IAM policy for the required S3 actions and correct resource ARNs. After that I would inspect the S3 bucket policy for explicit denies or conditions. If the bucket uses KMS encryption, I would verify KMS permissions as well. Finally, I would use `aws sts get-caller-identity` from the workload where possible and check CloudTrail to confirm exactly which API request is being denied."

---

# Q3. How would you verify that a production rollback was successful?

Rollback success means more than:

```text
kubectl rollout undo
```

I would validate both the **deployment state and application behavior**.

---

## Step 1: Check rollout status

```bash
kubectl rollout status deployment/<deployment-name> \
  -n <namespace>
```

Expected:

```text
deployment "<deployment-name>" successfully rolled out
```

---

## Step 2: Check Pods

```bash
kubectl get pods -n <namespace>
```

I would verify:

```text
READY
STATUS
RESTARTS
AGE
```

All expected replicas should be healthy.

---

## Step 3: Verify image version

```bash
kubectl get deployment <deployment-name> \
  -n <namespace> \
  -o jsonpath='{.spec.template.spec.containers[*].image}'
```

Confirm that the previous stable image is running.

---

## Step 4: Check rollout history

```bash
kubectl rollout history deployment/<deployment-name> \
  -n <namespace>
```

---

## Step 5: Check application logs

```bash
kubectl logs <pod-name> -n <namespace>
```

Look for:

```text
Errors
Exceptions
Connection failures
Startup failures
```

---

## Step 6: Test the application

I would perform:

```text
Health check
Smoke test
API test
Critical business transaction
```

For example:

```bash
curl -i https://application.example.com/health
```

---

## Step 7: Monitor production metrics

I would check:

```text
Error rate
Latency
Request rate
CPU
Memory
Pod restarts
Kafka lag
Database metrics
Load Balancer 4xx/5xx
```

I would compare these with the metrics from before the incident.

---

## Strong interview answer

> "I would verify rollback at three levels: Kubernetes state, application health, and business metrics. First I would check rollout status, Pod readiness and the running image version. Then I would validate application logs and perform health/smoke tests. Finally I would monitor error rate, latency, traffic, resource usage and dependent systems to make sure the original symptoms have disappeared. I would consider the rollback successful only after both technical and customer-facing metrics return to normal."

---

# 📊 2. Monitoring / Datadog / Production

# Q4. API latency suddenly increases from 200 ms to 12 seconds, but CPU is 30%, memory is 40%, and there are no Pod restarts. How would you troubleshoot it?

### Answer

The important clue is:

```text
CPU normal
Memory normal
No Pod restarts
Latency extremely high
```

So I would **not immediately blame Kubernetes resources**.

I would investigate dependencies, network latency, database queries, thread/connection pools, external APIs and application-level bottlenecks.

---

## Step 1: Establish when the problem started

I would check:

```text
When did latency increase?
Was there a deployment?
Was traffic increased?
Did a dependency become slow?
Did database latency increase?
```

---

## Step 2: Break latency down by endpoint

Instead of looking only at overall latency:

```text
/api/users
/api/orders
/api/payment
/api/reports
```

I would identify which endpoint is responsible.

---

## Step 3: Use Datadog APM

I would check:

```text
APM
Services
Resources
Traces
Flame graphs
Service Map
```

I would identify where the 12 seconds are being spent.

Example:

```text
API
 |
 +-- Application processing: 200 ms
 |
 +-- MongoDB query: 9 sec
 |
 +-- External API: 2 sec
 |
 +-- Network: 800 ms
```

This immediately narrows the root cause.

---

## Step 4: Check distributed traces

A trace can show:

```text
API Gateway
   ↓
Service A
   ↓
Service B
   ↓
MongoDB
   ↓
External API
```

I would identify the slow span.

---

## Step 5: Check database performance

For example:

```text
MongoDB slow queries
PostgreSQL slow queries
Connection pool exhaustion
Locks
Index problems
```

---

## Step 6: Check external dependencies

The application may be healthy but an external API could be taking:

```text
10 seconds
```

instead of:

```text
100 ms
```

I would check dependency latency and error metrics.

---

## Step 7: Check application-level resources

CPU and memory can be normal while:

```text
Thread pool
Connection pool
HTTP connection pool
Worker threads
Queue
```

are exhausted.

---

## Step 8: Check network

I would check:

```text
DNS latency
Load Balancer
Ingress
Network connectivity
Service-to-service latency
NAT Gateway
External API connectivity
```

---

### Strong interview answer

> "Since CPU and memory are normal and there are no Pod restarts, I would move away from resource saturation and investigate application and dependency latency. I would use Datadog APM and distributed tracing to identify which endpoint and downstream span is consuming the 12 seconds. Then I would check database latency, connection pools, external APIs, network latency and application thread pools. I would also correlate the start time with deployments or infrastructure changes."

---

# Q5. Customers report intermittent failures. CPU and memory look normal, but the error rate is increasing. Which Datadog features would you use?

### Answer

I would use several Datadog features together rather than relying only on infrastructure metrics.

### 1. APM

Check:

```text
Error rate
Request rate
Latency
Affected endpoints
Affected services
```

---

### 2. Distributed Tracing

I would identify failed traces and determine where failures originate.

Example:

```text
API Gateway
   ↓
Service A
   ↓
Service B
   ↓
Database
```

---

### 3. Service Map

The Service Map helps identify whether failures are isolated to one service or spreading through dependencies.

---

### 4. Error Tracking

I would group errors by:

```text
Exception
Stack trace
Service
Endpoint
Version
Host
Pod
```

This can reveal that the increasing errors are all caused by the same exception.

---

### 5. Logs

I would correlate logs around the exact failure period.

I would search using:

```text
request_id
trace_id
service
endpoint
status_code
error
exception
```

---

### 6. Infrastructure metrics

Even when CPU and memory are normal, I would check:

```text
Network
Disk I/O
Connection pools
Load Balancer
Kubernetes
Kafka
Database
```

---

### 7. Deployment correlation

I would compare:

```text
Deployment time
Error rate
Latency
Exception count
Traffic
```

This helps determine whether a recent release caused the issue.

---

### Strong answer

> "For intermittent failures, I would use Datadog APM, distributed traces, Error Tracking, Logs and the Service Map. I would correlate errors using request or trace IDs and break the failures down by service, endpoint, version and dependency. I would also compare the error spike against deployment timestamps and downstream systems. CPU and memory being normal doesn't rule out application-level issues such as connection pool exhaustion, dependency failures or code-level exceptions."

---

# Q6. Ten minutes after a production deployment, CPU spikes, Kafka lag increases, and customer tickets start coming in. How would you prove whether the deployment caused the issue?

### Answer

I would establish a **timeline and correlation**.

The timeline is:

```text
Deployment
    |
    | +10 minutes
    v
CPU spike
    |
    v
Kafka lag increases
    |
    v
Customer failures
```

Correlation alone does not prove causation, so I would gather additional evidence.

---

## Step 1: Identify exact deployment timestamp

For example:

```text
Deployment: 10:00
CPU spike: 10:10
Kafka lag: 10:11
Customer errors: 10:12
```

---

## Step 2: Compare application versions

Identify which Pods are running the new version.

```bash
kubectl get pods -n <namespace> \
  -o wide
```

Check image:

```bash
kubectl get deployment <deployment> \
  -o jsonpath='{.spec.template.spec.containers[*].image}'
```

---

## Step 3: Compare old and new versions

If possible, compare:

```text
CPU
Memory
Request latency
Error rate
Kafka processing rate
Kafka consumer lag
```

between the old and new version.

---

## Step 4: Check application logs

```bash
kubectl logs <pod-name> -n <namespace>
```

Look for:

```text
Exceptions
Slow processing
Retry loops
Kafka consumer errors
Database errors
```

---

## Step 5: Check Kafka metrics

I would check:

```text
Consumer lag
Records consumed
Records processed
Consumer errors
Rebalances
Poll latency
Processing latency
```

---

## Step 6: Check Datadog correlation

I would overlay:

```text
Deployment event
CPU
Latency
Error rate
Kafka lag
Request rate
```

If the metrics change immediately after the new version reaches production, that's strong evidence.

---

## Step 7: Canary / rollback validation

If safe, I would roll back the deployment and observe whether:

```text
CPU decreases
Kafka lag drains
Error rate decreases
Latency returns to normal
```

If rollback causes all these metrics to recover, that is strong evidence that the deployment caused the problem.

---

### Strong interview answer

> "I would build a timeline first, then compare the behavior of the old and new versions. I would correlate deployment events with CPU, latency, error rate and Kafka lag using Datadog. I would inspect application and Kafka consumer logs for errors or processing changes. The strongest validation would be rollback: if the metrics recover after reverting to the previous version, while the infrastructure and traffic remain comparable, that provides strong evidence that the deployment caused the incident. I would still perform RCA to identify the exact code or configuration change."

---

# Q7. Explain a real production incident you handled.

## Sample answer

Use a real incident from your own experience if possible. A strong structure is:

```text
Situation
Impact
Investigation
Root Cause
Resolution
Prevention
```

### Sample DevOps incident

> "In one production incident, users started experiencing API failures after a deployment. The Pods were showing Running status, so initially it looked like the application was healthy."

> "I first checked the Kubernetes Pods and application logs using `kubectl get pods` and `kubectl logs`. The Pods were healthy, but requests through the API layer were returning 404. I then checked the Ingress configuration and the API Gateway routing."

> "The issue was caused by an incorrect routing path in the API Gateway/Ingress configuration. The frontend was sending requests to one path, while the backend route expected a different path."

> "I validated the issue by testing the API directly through the Service and then testing it through the Ingress/API Gateway. The Service worked correctly, which isolated the issue to the routing layer."

> "We corrected the routing configuration and validated the APIs through the customer endpoint. Error rates returned to normal."

> "As a corrective action, we added better API smoke tests to the deployment pipeline and improved monitoring for 4xx/5xx responses so that similar routing problems could be detected before reaching customers."

### Commands I could mention

```bash
kubectl get pods -n <namespace>
kubectl logs <pod> -n <namespace>
kubectl get svc -n <namespace>
kubectl get ingress -n <namespace>
kubectl describe ingress <ingress> -n <namespace>
kubectl get endpoints -n <namespace>
```

### Important

Do **not** memorize this incident word-for-word if it wasn't your actual incident.

Interviewers commonly ask follow-up questions such as:

```text
What was the exact root cause?
What logs did you check?
Why did you check Ingress?
How did you prove the Pod was healthy?
What was the customer impact?
What did you change afterward?
```

Your answer must therefore match an incident you can genuinely explain.

---

# ⚡ 3. Kafka

# Q8. Kafka consumer lag increases from 500 to 2.5 million within 30 minutes, while producers are healthy. What could cause this, and what would you investigate first?

### Answer

This indicates that:

```text
Producer rate > Consumer processing rate
```

The producers may be completely healthy while consumers cannot keep up.

---

## Possible causes

### 1. Consumer application is slow

For example:

```text
Slow database calls
External API latency
Heavy processing
CPU throttling
Memory pressure
```

---

### 2. Consumer instances decreased

Check whether consumers are running:

```text
Consumer count
Pod count
Consumer group membership
```

---

### 3. Consumer rebalancing

Frequent rebalances can reduce processing efficiency.

Possible causes:

```text
Pod restarts
Long processing time
Heartbeat failures
Network issues
```

---

### 4. Consumer errors

Check application logs for:

```text
Exceptions
Deserialization errors
Commit failures
Connection errors
Timeouts
```

---

### 5. Partition imbalance

Check:

```text
Number of partitions
Number of consumers
Partition assignment
Lag per partition
```

One or a few partitions may have disproportionately high lag.

---

### 6. Downstream dependency is slow

For example:

```text
Kafka → Consumer → MongoDB
```

If MongoDB becomes slow, consumer processing slows down and Kafka lag increases.

---

## What I would investigate first

### Consumer group lag

```text
Consumer group
Topic
Partition
Current offset
Log end offset
Lag
```

---

### Consumer health

Check:

```bash
kubectl get pods -n <namespace>
kubectl top pods -n <namespace>
```

Then application logs:

```bash
kubectl logs <consumer-pod> -n <namespace>
```

---

### Check processing rate

I would compare:

```text
Messages produced/sec
Messages consumed/sec
Messages processed/sec
```

If producers generate:

```text
100,000 msg/sec
```

but consumers process:

```text
20,000 msg/sec
```

lag will continue increasing.

---

### Strong interview answer

> "Since producers are healthy, I would focus on the consumer side. I would first check consumer-group lag by topic and partition, then verify the number and health of consumer instances. I would check for consumer rebalances, Pod restarts, processing latency, exceptions, deserialization issues and downstream dependency latency. I would also compare producer throughput with consumer throughput. If consumer throughput is lower than producer throughput, I would identify why processing has slowed before simply increasing consumers."

---

# Q9. Around 5,000 messages land in a DLT/DLQ. The application bug has been fixed and the business wants them replayed. What precautions would you take?

### Answer

I would **not immediately replay all 5,000 messages**.

First I would validate the fixed application and understand the failed messages.

---

## Step 1: Understand why they failed

Check:

```text
Error type
Message schema
Application version
Failure timestamp
Topic
Partition
Offset
```

---

## Step 2: Validate the fix

Before replaying production messages:

```text
Test in lower environment
Test with representative failed messages
Validate application behavior
```

---

## Step 3: Check message compatibility

Verify:

```text
Schema
Headers
Payload
Required fields
Version compatibility
```

---

## Step 4: Check duplicate processing risk

Ask:

```text
Was the message partially processed before failure?
```

For example:

```text
Kafka message
   ↓
Database update succeeded
   ↓
Application crashed
   ↓
Message sent/retried
```

Replaying it could cause the operation to happen again.

---

## Step 5: Confirm idempotency

The consumer should ideally use an idempotency key such as:

```text
event_id
transaction_id
order_id
message_id
```

Before processing:

```text
Have I already processed this event?
```

---

## Step 6: Replay gradually

Instead of:

```text
5,000 messages immediately
```

I would use:

```text
Small batch
   ↓
Observe
   ↓
Verify
   ↓
Increase batch
```

---

## Step 7: Monitor

Monitor:

```text
Consumer lag
Error rate
DLQ count
Processing latency
Database load
CPU
Memory
Downstream services
```

---

### Strong answer

> "I would not replay all 5,000 messages immediately. First I would understand the original failure, validate that the bug is fixed, verify message/schema compatibility, and check whether processing is idempotent. I would replay a small batch first, monitor errors and downstream impact, and gradually increase the replay rate. I would also make sure we have a way to stop the replay quickly if the issue reappears."

---

# Q10. How would you prevent duplicate processing while replaying messages?

### Answer

The primary strategy is **idempotent consumer design**.

For example, each event has:

```text
event_id = abc123
```

Before processing:

```text
Check whether event_id was already processed.
```

If it exists:

```text
Skip processing
```

Otherwise:

```text
Process event
Store event_id
```

---

## Other approaches

### Database uniqueness

Create a unique constraint on an event ID.

```text
event_id UNIQUE
```

This prevents duplicate records.

---

### Transactional processing

Where appropriate:

```text
Consume message
     ↓
Process business operation
     ↓
Persist result + processing marker atomically
```

---

### Kafka offset management

Offsets help with Kafka consumption state, but **offset management alone does not guarantee business-level exactly-once behavior**.

A consumer can process a message and fail before committing its offset.

The message can then be consumed again.

Therefore:

> Kafka offset tracking and application-level idempotency solve different problems.

---

# Q11. Why shouldn't thousands of failed messages always be replayed at once?

Because replaying thousands of messages can create another production incident.

Potential problems:

```text
Consumer CPU spike
Database overload
Connection pool exhaustion
External API rate limits
Kafka consumer lag fluctuations
Retry storms
Duplicate processing
```

Example:

```text
5,000 failed messages
       ↓
Replay immediately
       ↓
Consumer throughput spikes
       ↓
Database overloaded
       ↓
Requests become slow
       ↓
Consumer processing slows
       ↓
Lag increases
       ↓
More retries
```

Therefore I prefer:

```text
Controlled replay
+ Rate limiting
+ Batch processing
+ Monitoring
+ Ability to stop
```

---

# 🔎 4. Elasticsearch / Kibana

# Q12. Kibana searches suddenly take 30 seconds, but Elasticsearch cluster health is green. How would you troubleshoot it?

### Answer

`green` cluster health does **not** mean every query will be fast.

It mainly indicates that primary and replica shards are allocated correctly.

I would investigate query performance and cluster/resource behavior.

---

## Step 1: Check Elasticsearch health

```bash
GET /_cluster/health
```

Check:

```text
status
number_of_nodes
active_primary_shards
active_shards
relocating_shards
initializing_shards
unassigned_shards
```

---

## Step 2: Check node resources

```bash
GET /_cat/nodes?v
```

Look at:

```text
CPU
Heap
RAM
Load
Disk
```

---

## Step 3: Check JVM heap

High JVM heap usage can cause:

```text
GC pauses
Slow queries
Reduced throughput
```

---

## Step 4: Check search thread pools

```bash
GET /_cat/thread_pool/search?v
```

Look for:

```text
active
queue
rejected
```

If search queues are high or requests are being rejected, that is an important clue.

---

## Step 5: Check slow logs

Elasticsearch slow logs can identify expensive searches.

Look for:

```text
Slow query
Large aggregation
Wildcard query
Regex query
Script
```

---

## Step 6: Analyze the actual query

The problem could be Kibana generating an expensive query.

Common causes:

```text
Large time range
Wildcard search
Regex
High-cardinality aggregation
Sorting large datasets
Script-based query
```

---

## Step 7: Check shard distribution

```bash
GET /_cat/shards?v
```

Look for:

```text
Very large shards
Unbalanced allocation
Many shards
Hot nodes
```

---

## Step 8: Check disk

```bash
GET /_cat/allocation?v
```

Disk pressure can affect performance even when cluster health is green.

---

### Strong interview answer

> "I would not assume green health means Elasticsearch is performing well. I would check node CPU, JVM heap and GC, search thread-pool queues and rejections, slow logs, the actual Kibana query, shard distribution and disk usage. I would also check whether the query is scanning a very large time range or performing expensive aggregations. Then I would use query profiling to identify the expensive part of the search."

---

# Q13. What shard-related checks would you perform when Elasticsearch searches become slow?

I would check:

### 1. Number of shards

Too many shards can increase coordination and search overhead.

```bash
GET /_cat/indices?v
```

---

### 2. Shard size

Look for unusually large shards.

```bash
GET /_cat/shards?v
```

---

### 3. Shard distribution

Check whether shards are unevenly distributed.

```bash
GET /_cat/allocation?v
```

---

### 4. Hot shards

One shard may receive much more traffic than others.

---

### 5. Primary vs replica distribution

Check whether replicas are properly distributed.

---

### 6. Segment count

Large numbers of segments can affect performance.

---

### 7. Merge activity

Heavy segment merges can consume:

```text
CPU
Disk I/O
```

---

### 8. Search fan-out

A query against many indices/shards can become expensive.

Example:

```text
Kibana query
    ↓
50 indices
    ↓
500 shards
    ↓
Large coordination overhead
```

---

# 🍃 5. MongoDB

# Q14. An API backed by MongoDB increases from 300 ms to 20 seconds. How would you troubleshoot it?

### Answer

I would determine whether the latency is caused by:

```text
Application
 ↓
Connection pool
 ↓
MongoDB query
 ↓
Disk/CPU/Memory
 ↓
Replica set
```

---

## Step 1: Check API metrics

Determine:

```text
Which endpoint?
When did latency increase?
Which percentage of requests?
```

---

## Step 2: Check MongoDB metrics

I would check:

```text
CPU
Memory
Disk I/O
Connections
Operations/sec
Query latency
Locks
Replication lag
```

---

## Step 3: Check slow queries

If profiling is enabled:

```javascript
db.system.profile.find().sort({ millis: -1 }).limit(10)
```

---

## Step 4: Check current operations

```javascript
db.currentOp()
```

Look for:

```text
Long-running operations
Blocked operations
High execution time
```

---

## Step 5: Check indexes

A missing or ineffective index can cause a query to move from:

```text
Fast IXSCAN
```

to:

```text
Slow COLLSCAN
```

---

## Step 6: Run explain

```javascript
db.collection.find({
  userId: "123"
}).explain("executionStats")
```

---

## Step 7: Check connection pool

The database itself may be healthy while the application is waiting for a connection.

I would check:

```text
Connection pool size
Active connections
Waiting connections
Connection timeouts
```

---

## Step 8: Check replica set

Check:

```javascript
rs.status()
```

Look for:

```text
Primary
Secondaries
Replication lag
Unhealthy members
```

---

# Q15. What MongoDB checks would you perform for slow queries, indexes, connections, replica-set health, CPU, memory and disk I/O?

## Slow queries

```javascript
db.currentOp()
```

and profiling:

```javascript
db.system.profile.find()
  .sort({ millis: -1 })
  .limit(10)
```

---

## Indexes

```javascript
db.collection.getIndexes()
```

---

## Query execution

```javascript
db.collection.find({
  userId: "123"
}).explain("executionStats")
```

---

## Connections

```javascript
db.serverStatus().connections
```

Important fields include:

```text
current
available
totalCreated
```

---

## Replica set

```javascript
rs.status()
```

Also:

```javascript
rs.printSecondaryReplicationInfo()
```

---

## Server status

```javascript
db.serverStatus()
```

This provides a broad view of MongoDB health.

---

## CPU / memory / disk

From the host:

```bash
top
free -m
df -h
iostat -xz 1
```

In a containerized environment I would also correlate with:

```bash
kubectl top pod
kubectl top nodes
```

---

# Q16. How would you use explain() to determine whether MongoDB is performing an IXSCAN or COLLSCAN?

Run:

```javascript
db.collection.find({
  userId: "123"
}).explain("executionStats")
```

Look for:

```text
IXSCAN
```

or:

```text
COLLSCAN
```

### IXSCAN

```text
IXSCAN
```

means MongoDB is using an index.

This is generally preferable when the index is appropriate for the query.

---

### COLLSCAN

```text
COLLSCAN
```

means MongoDB is scanning the collection.

For a large collection, this can be expensive.

---

## Important explain metrics

I would compare:

```text
nReturned
totalKeysExamined
totalDocsExamined
executionTimeMillis
```

Example:

```text
nReturned: 10
totalDocsExamined: 10
```

is generally much better than:

```text
nReturned: 10
totalDocsExamined: 10,000,000
```

A useful ratio to think about is:

```text
Documents examined
        /
Documents returned
```

A very high ratio can indicate an inefficient query/index.

---

# 🐍 6. Python / Shell Scripting

# Q17. How comfortable are you with Python and Shell scripting for DevOps automation?

### Strong 4-year answer

> "I am comfortable using both Shell scripting and Python for DevOps automation. I generally use Shell for Linux administration, log processing, service checks, AWS CLI automation and simple operational tasks. I use Python when the automation becomes more complex, especially when I need structured error handling, API integrations, JSON processing, AWS SDK usage or reusable automation."

### Shell examples

I have used Shell scripting for:

```text
Log analysis
Disk usage checks
Process monitoring
Service health checks
AWS CLI automation
Backup scripts
Deployment utilities
```

Example:

```bash
#!/bin/bash

THRESHOLD=80

USAGE=$(df / | awk 'NR==2 {print $5}' | tr -d '%')

if [ "$USAGE" -gt "$THRESHOLD" ]; then
    echo "Disk usage is high: ${USAGE}%"
    exit 1
else
    echo "Disk usage is normal: ${USAGE}%"
fi
```

---

## Python example

```python
import subprocess

result = subprocess.run(
    ["systemctl", "is-active", "nginx"],
    capture_output=True,
    text=True
)

if result.stdout.strip() == "active":
    print("Nginx is running")
else:
    print("Nginx is not running")
```

---

# Q18. You receive a production Python script you've never seen before. What would you inspect before executing it?

This is an important production-safety question.

### I would NEVER execute an unknown production script immediately.

First I would inspect it statically.

---

# Step 1: Understand the purpose

I would first read:

```text
README
Comments
Function names
Arguments
Main function
Dependencies
```

I want to understand:

```text
What does the script do?
What systems does it interact with?
What does it modify?
```

---

# Step 2: Check imports

Look at:

```python
import os
import subprocess
import boto3
import requests
```

Imports tell me what capabilities the script may have.

Particularly important:

```text
subprocess
os.system
eval
exec
requests
boto3
paramiko
```

These may indicate system, network or cloud operations.

---

# Step 3: Search for destructive commands

I would look for:

```text
rm
rm -rf
shutil.rmtree
DROP
DELETE
TRUNCATE
terminate_instances
delete_object
delete_bucket
terraform destroy
kubectl delete
```

Also inspect dynamic execution:

```python
eval(...)
exec(...)
```

---

# Step 4: Check external connections

I would identify:

```text
AWS
Databases
APIs
Kubernetes
SSH
S3
MongoDB
Kafka
```

Look for:

```python
requests.get(...)
requests.post(...)
boto3.client(...)
pymongo.MongoClient(...)
```

---

# Step 5: Check credentials and secrets

I would search for:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
password
token
API_KEY
private_key
```

I would verify whether credentials are:

```text
Hardcoded
Environment variables
AWS IAM role
Secrets Manager
Vault
```

Hardcoded production credentials would be a major red flag.

---

# Step 6: Check command execution

Search for:

```python
subprocess.run()
subprocess.Popen()
os.system()
```

Understand exactly what commands are executed.

---

# Step 7: Check file operations

Look for:

```python
open()
os.remove()
shutil.copy()
shutil.move()
shutil.rmtree()
```

I want to know whether the script modifies production files.

---

# Step 8: Check input validation

If the script accepts:

```text
CLI arguments
Environment variables
User input
API response
File input
```

I would verify how those inputs are handled.

---

# Step 9: Check dependencies

Look for:

```text
requirements.txt
pyproject.toml
Pipfile
```

I would verify the Python version and package versions.

I would **not blindly install dependencies directly on a production server**.

---

# Step 10: Run static analysis

Depending on the environment, I could use:

```bash
python -m py_compile script.py
```

For linting:

```bash
ruff check script.py
```

or:

```bash
pylint script.py
```

For security scanning:

```bash
bandit -r script.py
```

---

# Step 11: Test in a non-production environment

This is the most important step.

I would preferably run it in:

```text
Development
Staging
Sandbox
Container
Isolated VM
```

before production.

---

# Step 12: Use dry-run if supported

If the script supports:

```text
--dry-run
```

I would use:

```bash
python script.py --dry-run
```

This allows me to understand what it would change without actually performing the production action.

---

# Step 13: Review permissions

I would ask:

```text
What IAM permissions does it need?
What Linux user executes it?
What Kubernetes permissions does it have?
What database permissions does it have?
```

The principle should be:

> **Least privilege.**

---

# Step 14: Backup / rollback plan

Before a script modifies production data, I would establish:

```text
Backup
Rollback procedure
Recovery procedure
Monitoring
Owner/approval
Maintenance window
```

---

### Strong interview answer

> "I would never execute an unfamiliar production script directly. First I would inspect its purpose, imports, dependencies, external connections, credentials, subprocess calls, file operations and any destructive commands. I would check whether it interacts with AWS, databases, Kubernetes or external APIs and what permissions it requires. Then I would run static checks and, if possible, test it in a non-production environment using a dry-run. Before production execution I would make sure there is proper approval, least-privilege access, monitoring and a rollback or recovery plan."

---

# 🚨 7. Production Troubleshooting Framework

For almost any scenario-based DevOps interview question, use this framework.

## 1. Define the symptom

Example:

```text
Latency increased from 200 ms → 12 sec
```

---

## 2. Establish timeline

Ask:

```text
When did it start?
Was there a deployment?
Was traffic increased?
Did a dependency change?
```

---

## 3. Check impact

Determine:

```text
How many users?
Which APIs?
Which region?
Which services?
```

---

## 4. Check golden signals

The four important signals are:

```text
Latency
Traffic
Errors
Saturation
```

---

## 5. Correlate dependencies

Check:

```text
Kubernetes
AWS
Database
Kafka
Elasticsearch
External APIs
Network
```

---

## 6. Isolate the failing layer

Example:

```text
Customer
   ↓
DNS
   ↓
Load Balancer
   ↓
Ingress
   ↓
Service
   ↓
Pod
   ↓
Application
   ↓
Database
```

Find exactly where the request starts failing.

---

## 7. Collect evidence

Use:

```text
Logs
Metrics
Traces
Events
Application errors
System metrics
```

---

## 8. Apply the safest mitigation

Depending on the issue:

```text
Rollback
Scale
Disable feature
Restart unhealthy component
Route traffic
Stop replay
Increase capacity
```

Do not make random production changes.

---

## 9. Validate

After the fix:

```text
Error rate ↓
Latency ↓
Kafka lag ↓
CPU normal
Customer errors ↓
```

---

## 10. Prevent recurrence

Examples:

```text
Better alerts
Automated tests
Canary deployment
Runbooks
Capacity planning
Idempotency
Better dashboards
Health checks
Circuit breakers
```

---

# 🎯 8. High-Value Commands to Remember

## Kubernetes

```bash
kubectl get pods -n <ns>
kubectl describe pod <pod> -n <ns>
kubectl logs <pod> -n <ns>
kubectl logs <pod> --previous -n <ns>

kubectl get deployment -n <ns>
kubectl describe deployment <deployment> -n <ns>
kubectl rollout history deployment/<deployment> -n <ns>
kubectl rollout status deployment/<deployment> -n <ns>
kubectl rollout undo deployment/<deployment> -n <ns>

kubectl get svc -n <ns>
kubectl get endpoints -n <ns>
kubectl get ingress -n <ns>
kubectl get events -n <ns> --sort-by=.lastTimestamp

kubectl top pods -n <ns>
kubectl top nodes
```

---

# AWS / EKS

```bash
aws sts get-caller-identity

aws iam get-role \
  --role-name <role>

aws iam list-attached-role-policies \
  --role-name <role>

aws s3api get-bucket-policy \
  --bucket <bucket>
```

---

# MongoDB

```javascript
db.currentOp()

db.serverStatus()

db.serverStatus().connections

db.collection.getIndexes()

db.collection.find({
  userId: "123"
}).explain("executionStats")

rs.status()
```

---

# Elasticsearch

```text
GET /_cluster/health

GET /_cat/nodes?v

GET /_cat/indices?v

GET /_cat/shards?v

GET /_cat/allocation?v

GET /_cat/thread_pool/search?v
```

---

# 🧠 9. Interview Cheat Sheet

| Problem               | First Things to Check                                                       |
| --------------------- | --------------------------------------------------------------------------- |
| CrashLoopBackOff      | `describe`, logs, `--previous`, exit code, probes                           |
| S3 AccessDenied       | ServiceAccount → IAM Role → IAM Policy → Bucket Policy → KMS                |
| Rollback              | rollout status → image → Pods → smoke test → metrics                        |
| High API latency      | APM → traces → dependencies → DB → connection pools                         |
| Intermittent errors   | Datadog APM → Error Tracking → Logs → Traces → Service Map                  |
| Deployment incident   | Timeline → version comparison → metrics → logs → rollback validation        |
| Kafka lag             | Consumer health → lag/partition → throughput → rebalances → dependencies    |
| DLQ replay            | Fix validation → idempotency → small batch → monitoring                     |
| Elasticsearch slow    | Query → shards → JVM → thread pool → disk → slow logs                       |
| MongoDB slow          | Query → `explain()` → indexes → connections → replica set → I/O             |
| Unknown Python script | Read → dependencies → permissions → destructive actions → dry-run → staging |

---

# ⭐ Final Interview Mindset

For a 4-year DevOps Engineer, avoid answering production questions with only:

```text
"Restart the Pod."
"Increase CPU."
"Restart Kafka."
"Create an index."
"Rollback."
```

Instead explain **how you would prove the root cause**.

A strong production answer sounds like:

> "I would first establish the timeline and customer impact. Then I would check metrics, logs and traces to identify the failing component. I would correlate the issue with recent deployments or infrastructure changes, validate the dependency health, isolate the root cause, apply the safest mitigation, and finally verify recovery through both technical and customer-facing metrics. After recovery, I would document the RCA and implement preventive actions."

That style demonstrates **production ownership**, which is what interviewers generally expect from someone with around **4 years of DevOps experience**.
