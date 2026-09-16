# Production Pipeline Failed — Is Your Website Really Down?

A failed production pipeline does **not automatically mean the deployment caused an outage**.

Before clicking **Retry**, first determine:

* What actually failed?
* When did it fail?
* Did anything reach production?
* What are users experiencing?
* Is the pipeline failure related to the outage?

This guide provides a structured approach to investigating production outages when a deployment pipeline has failed, restoring service safely, and explaining the troubleshooting process in a technical interview.

---

## Incident Flow

```text
                    Pipeline Failed
                          |
                          v
                  Confirm User Impact
                          |
                          v
               Did Deployment Start?
                    /           \
                  No             Yes
                  |               |
                  v               v
          Check Other Causes   Identify Failed Stage
                                      |
                        +-------------+-------------+
                        |             |             |
                     Rollout      Post-deploy     Traffic
                      Failed        Checks        Switched
                        |             |             |
                        +-------------+-------------+
                                      |
                                      v
                              Restore Service
                                      |
                                      v
                              Verify Recovery
                                      |
                                      v
                            Root Cause Analysis
```

---

# 1. Confirm the Impact

**Do not assume the pipeline failure caused the outage.**

First determine exactly what users are experiencing.

### Questions to Ask

* Is the entire website unavailable?
* Is only a specific feature failing?
* Are users seeing:

  * `502 Bad Gateway`
  * `503 Service Unavailable`
  * `504 Gateway Timeout`
  * Application-level errors?
* When did the failures begin?
* Did the failures start **before, during, or after** the deployment?

### What to Check

Check multiple layers of the system:

* Application monitoring
* Error rate
* Request latency
* Load balancer target health
* Application logs
* Infrastructure metrics
* Deployment events
* Recent configuration changes
* Recent infrastructure changes

### Build a Timeline

Create a simple incident timeline:

```text
10:00  Deployment started
10:02  New pods created
10:03  Pipeline failed
10:04  Error rate increased
10:05  Load balancer targets became unhealthy
```

If the outage started at **10:04** and the pipeline failed at **10:03**, there is a stronger reason to investigate the deployment.

If the website failed at **09:50** and the pipeline failed at **10:10**, the pipeline failure may be unrelated.

> **Key principle:** Correlation with the incident timeline is more useful than assuming causation from a red pipeline.

---

# 2. Stop Additional Changes

Once a production incident is confirmed, avoid making unrelated changes.

### Immediate Actions

* Pause additional production deployments.
* Notify the incident channel.
* Notify the relevant service/application owners.
* Record the currently deployed version.
* Preserve relevant logs and monitoring information.
* Record the incident timeline.

### Why?

Multiple people making changes simultaneously can make an incident significantly harder to diagnose.

For example:

```text
Engineer A → Deploys a new application version
Engineer B → Changes environment variables
Engineer C → Restarts Kubernetes workloads
Engineer D → Changes load balancer configuration
```

If the service recovers, it becomes difficult to determine which change actually fixed the problem.

During an incident:

> **Coordinate changes and maintain a clear timeline.**

---

# 3. Locate the Failed Pipeline Stage

A pipeline can fail at different points.

The first question should be:

> **Did the failed pipeline actually deploy anything to production?**

---

## 3.1 Build or Test Failed

Example:

```text
Code
 |
 v
Build ❌
 |
 X
Deployment never started
```

If the build or test stage failed, the new version may never have reached production.

### Actions

* Confirm that no deployment occurred.
* Check the currently running production version.
* Compare the outage timeline with the pipeline timeline.
* Investigate other recent application changes.
* Investigate infrastructure changes.
* Investigate configuration changes.

### Important

A failed build does **not** prove that the website outage was caused by that build.

---

## 3.2 Deployment Failed

Example:

```text
Build ✅
   |
   v
Deployment ⚠️
   |
   +--> Some instances updated
   |
   +--> Some instances still running old version
```

A deployment can fail after changing part of the production environment.

### Investigate

* Partial rollout
* Container startup failures
* Missing environment variables
* Missing secrets
* Incorrect configuration
* Image pull failures
* Resource constraints
* Unhealthy targets
* Failed readiness probes
* Failed liveness probes

> **A failed deployment does not necessarily mean that nothing changed.**

---

## 3.3 Post-Deployment Checks Failed

Example:

```text
Build ✅
   |
Deployment ✅
   |
Traffic switched ✅
   |
Smoke tests ❌
```

This situation is particularly important.

The pipeline can be **red while the new version is already serving production traffic**.

Therefore:

> **A red pipeline does not necessarily mean the previous version is running.**

Always verify what is actually deployed before deciding whether to retry or roll back.

---

# 4. Restore Service Safely

If evidence shows that the release caused the outage, restore service using the safest available mechanism.

Possible approaches include:

* Roll back to a known healthy application version.
* Switch traffic to a verified healthy environment.
* Route traffic away from unhealthy instances.
* Apply a targeted fix when rollback is unsafe.

The priority is:

```text
Stop User Impact
      ↓
Restore Known-Good State
      ↓
Verify Recovery
      ↓
Investigate Root Cause
```

---

## Rollback Considerations

Before rolling back an application, check **database compatibility**.

For example:

```text
Version A
   |
   | Database migration
   v
Version B

Database schema = Version B
Application     = Version A
```

If Version B introduced a database schema change, Version A may no longer work correctly with the current database state.

Therefore:

> **The previous application version must be compatible with the current database schema before performing an application rollback.**

This is one reason database migrations should ideally be designed with **backward compatibility** in mind.

---

# 5. Troubleshooting Kubernetes

When Kubernetes is involved, identify the actual failure instead of relying only on pod status.

---

## 5.1 Pods Are `Pending`

```text
Pod
 |
 └── Pending
```

### Investigate

* Scheduling events
* Available cluster capacity
* CPU/memory requests
* Node availability
* Taints and tolerations
* Persistent storage
* PersistentVolume problems
* PersistentVolumeClaim problems

### Useful Commands

```bash
kubectl get pods -n production
```

```bash
kubectl describe pod <pod-name> -n production
```

```bash
kubectl get events -n production --sort-by=.lastTimestamp
```

---

## 5.2 Pods Are in `CrashLoopBackOff`

Typical sequence:

```text
Container starts
      |
      v
Application crashes
      |
      v
Container restarts
      |
      v
CrashLoopBackOff
```

Check the previous container's logs:

```bash
kubectl logs <pod-name> -n production --previous
```

Also inspect the pod:

```bash
kubectl describe pod <pod-name> -n production
```

### Look For

* Application startup errors
* Missing environment variables
* Missing secrets
* Configuration errors
* Dependency failures
* Out-of-memory termination
* Incorrect command or entrypoint

---

## 5.3 Readiness Probe Failures

A pod can be **Running** but still not be ready to receive traffic.

```text
Pod
 |
 +-- Running
 |
 └-- Ready ❌
```

Check:

* Readiness endpoint
* Container port
* Service port
* Application startup time
* Required dependencies
* Database connectivity
* External service dependencies

Example:

```yaml
readinessProbe:
  httpGet:
    path: /health
    port: 8080
```

A successful container startup does **not automatically mean the application is ready to serve users**.

---

## 5.4 Pods Are Healthy, But Requests Still Fail

This is an important troubleshooting scenario.

```text
Pods ✅
 |
 v
Service ?
 |
 v
Ingress ?
 |
 v
Load Balancer ?
 |
 v
Users ❌
```

Investigate:

* Kubernetes Service
* Service endpoints
* EndpointSlices
* Ingress configuration
* Ingress controller
* Load balancer target health
* DNS
* TLS configuration
* Network policies
* Routing rules

### Useful Commands

```bash
kubectl get svc -n production
```

```bash
kubectl get endpoints -n production
```

```bash
kubectl get endpointslices -n production
```

```bash
kubectl get ingress -n production
```

### Key Principle

> **A running container does not prove that the website works.**

Trace the complete request path:

```text
User
  |
  v
DNS
  |
  v
Load Balancer
  |
  v
Ingress
  |
  v
Service
  |
  v
Pod
  |
  v
Application
  |
  v
Database / Dependencies
```

---

# 6. Verify Recovery

Restoring infrastructure is not the same as confirming that users can use the application.

After recovery, verify the **actual user journey**.

## Functional Verification

Test a realistic flow:

```text
Open Website
     |
     v
Login
     |
     v
Open Dashboard
     |
     v
Perform Main Action
     |
     v
Verify Success
```

For an e-commerce application:

```text
Browse Product
      |
      v
Add to Cart
      |
      v
Checkout
      |
      v
Payment
      |
      v
Order Confirmation
```

---

## Monitor After Recovery

Confirm that:

* Error rates have returned to normal.
* Latency has recovered.
* Load balancer targets are healthy.
* Application pods remain stable.
* No repeated crashes are occurring.
* Key user journeys work.
* No new alerts are firing.

> **Do not immediately declare the incident resolved after seeing one successful request.**

Continue monitoring to make sure the recovery is stable.

---

# 7. Document the Root Cause

After service has stabilized, document what happened.

A useful incident report should contain:

## Timeline

```text
10:00  Deployment started
10:02  New version deployed
10:03  Readiness checks failed
10:04  Error rate increased
10:06  Deployment stopped
10:08  Traffic moved to healthy environment
10:12  Error rate returned to normal
```

## Root Cause

Explain the technical cause using evidence.

Example:

> The new application version was deployed successfully, but the application failed its readiness checks because a required configuration value was missing. Traffic was partially routed to unhealthy instances, resulting in elevated `503` responses.

## Contributing Factors

Consider:

* Missing deployment validation
* Incomplete health checks
* Configuration drift
* Insufficient monitoring
* Inadequate rollback automation
* Database migration compatibility
* Missing smoke tests

---

# 8. Improve the Deployment Process

The goal is not simply to fix one incident.

Use the incident to improve the system.

### Possible Improvements

* Add stronger pre-deployment validation.
* Add automated smoke tests.
* Improve readiness probes.
* Use gradual or canary deployments.
* Add automated rollback conditions.
* Validate configuration before deployment.
* Make database migrations backward compatible.
* Improve alerting.
* Add end-to-end health checks.
* Document the rollback procedure.
* Regularly test recovery procedures.

A mature deployment process should make failures **detectable, reversible, and recoverable**.

---

# 9. Technical Interview Answer

### Interview Question

> **"Your production pipeline failed and the website is down. What would you do?"**

### Structured Answer

> "I wouldn't immediately retry the pipeline. First, I'd confirm the user impact and establish whether the outage actually correlates with the deployment. I'd check monitoring, application errors, load balancer health, and the deployment timeline.
>
> Then I'd pause additional production changes and preserve the current state, including the deployed version and relevant logs.
>
> Next, I'd identify where the pipeline failed. If it failed during build or testing, the deployment may never have started, so I'd investigate other recent changes. If the deployment partially completed, I'd investigate unhealthy instances, configuration issues, container startup failures, and readiness failures. I'd also check whether post-deployment checks failed after traffic had already switched.
>
> If the release is confirmed as the cause, I'd restore service using a known healthy version or another verified environment. Before rolling back, I'd verify database compatibility because the previous application version must work with the current database schema.
>
> For Kubernetes, I'd check pod states, events, previous container logs, readiness probes, Services, endpoints, Ingress, and load balancer health. A running pod alone doesn't prove that the application is reachable.
>
> Finally, I'd verify recovery using a real user journey and monitor error rates and latency. After the incident, I'd document the root cause and improve deployment validation, health checks, rollback mechanisms, and recovery procedures."

---

# 10. Production Incident Checklist

Use this checklist during a production incident.

## Confirm Impact

* [ ] Confirm user impact
* [ ] Identify affected functionality
* [ ] Check error rates and latency
* [ ] Check load balancer target health
* [ ] Compare outage timeline with deployment events

## Stabilize the Incident

* [ ] Pause additional deployments
* [ ] Notify the incident channel and relevant owners
* [ ] Record the deployed version
* [ ] Preserve logs and monitoring data
* [ ] Record the incident timeline

## Investigate the Pipeline

* [ ] Determine whether deployment actually started
* [ ] Identify the failed pipeline stage
* [ ] Check for partial rollout
* [ ] Check application/container health
* [ ] Check configuration and secrets
* [ ] Check readiness and liveness probes

## Determine the Cause

* [ ] Determine whether the release caused the outage
* [ ] Check recent application changes
* [ ] Check recent infrastructure changes
* [ ] Check recent configuration changes
* [ ] Check database compatibility

## Restore Service

* [ ] Roll back or switch traffic if appropriate
* [ ] Apply a targeted fix if rollback is unsafe
* [ ] Confirm the restored version is known-good

## Verify Recovery

* [ ] Test a real user journey
* [ ] Confirm error rates recovered
* [ ] Confirm latency recovered
* [ ] Confirm load balancer targets are healthy
* [ ] Confirm pods remain stable
* [ ] Continue monitoring

## Post-Incident

* [ ] Document the timeline
* [ ] Document the root cause
* [ ] Document contributing factors
* [ ] Create follow-up improvements
* [ ] Review deployment and rollback procedures

---

# Key Takeaways

1. **A failed pipeline does not automatically mean the deployment caused the outage.**
2. **Establish the timeline and actual user impact first.**
3. **Determine whether anything was deployed before retrying.**
4. **A deployment can partially succeed even when the pipeline is red.**
5. **Post-deployment checks can fail after traffic has already switched.**
6. **Always consider database compatibility before rolling back.**
7. **A `Running` Kubernetes pod does not guarantee a working website.**
8. **Trace the complete request path from the user to the application.**
9. **Verify recovery using real user journeys, not just infrastructure health.**
10. **After recovery, improve the deployment and recovery process to prevent recurrence.**

---

# Golden Rule

> **Don't retry blindly. Establish what changed, determine what is actually serving traffic, restore service safely, and verify the recovery with evidence.**

---

## Quick Mental Model

When faced with a failed production pipeline and an outage, think:

```text
1. Are users actually affected?
          ↓
2. When did the outage start?
          ↓
3. What stage of the pipeline failed?
          ↓
4. Did anything reach production?
          ↓
5. What is actually serving traffic?
          ↓
6. Did the deployment cause the failure?
          ↓
7. What is the safest recovery?
          ↓
8. Is rollback compatible with the database?
          ↓
9. Can a real user complete their journey?
          ↓
10. What should we improve afterward?
```

**Investigate first. Change deliberately. Recover safely. Verify with evidence.**
