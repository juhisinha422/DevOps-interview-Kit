# Production Pipeline Failed — Is Your Website Really Down?

A failed production pipeline does not automatically explain an outage.

Before clicking Retry, first determine what failed, when it failed, and whether it is actually related to the user-facing incident.

This guide explains how to investigate a production website outage when a deployment pipeline has failed, how to restore service safely, and how to explain the troubleshooting approach in a technical interview.

Incident Flow
Pipeline Failed
      |
      v
Confirm User Impact
      |
      v
Did deployment actually start?
      |
      +-------------------+
      |                   |
     No                  Yes
      |                   |
      v                   v
Check other        Identify failed stage
possible causes          |
                          +----------------------+
                          |          |           |
                       Rollout    Post-deploy   Traffic
                        failed      checks      switched
                          |          |           |
                          +----------+-----------+
                                     |
                                     v
                              Restore Service
                                     |
                                     v
                              Verify Recovery
                                     |
                                     v
                              Root Cause Analysis

1. Confirm the Impact

Do not assume the pipeline failure caused the outage.

First determine exactly what users are experiencing.

Questions to ask

Is the entire website unavailable?

Is only a specific feature failing?

Are users seeing:

502 Bad Gateway

503 Service Unavailable

504 Gateway Timeout

Application-level errors?

When did the failures begin?

Did the failures start before, during, or after the deployment?

What to check

Application monitoring

Error rate

Request latency

Load balancer target health

Application logs

Infrastructure metrics

Deployment events

Recent configuration changes

Recent infrastructure changes

Timeline comparison

Build a simple timeline:

10:00  Deployment started
10:02  New pods created
10:03  Pipeline failed
10:04  Error rate increased
10:05  Load balancer targets became unhealthy


If the outage started at 10:04, while the pipeline failed at 10:03, there is a stronger reason to investigate the deployment.

If the website failed at 09:50 and the pipeline failed at 10:10, the pipeline failure may be unrelated.

2. Stop Additional Changes

Once a production incident is confirmed, avoid making unrelated changes.

Immediate actions

Pause additional production deployments.

Notify the incident channel.

Notify the relevant service/application owners.

Record the currently deployed version.

Preserve relevant logs and monitoring information.

Record the incident timeline.

Why?

Multiple people making changes simultaneously can make an incident harder to diagnose.

For example:

Engineer A → deploys a new application version
Engineer B → changes environment variables
Engineer C → restarts Kubernetes workloads
Engineer D → changes load balancer configuration


If the service recovers, it becomes difficult to determine which change fixed the problem.

During an incident, coordinate changes and keep a clear timeline.

3. Locate the Failed Stage

A pipeline can fail at different points.

The first question should be:

Did the failed pipeline actually deploy anything to production?

3.1 Build/Test Failed

Example:

Code
 |
 v
Build ❌
 |
 X
Deployment never started


If the build or test stage failed, the new version may never have reached production.

In that case:

Confirm that no deployment occurred.

Check the currently running production version.

Compare the outage timeline with the pipeline timeline.

Investigate other recent application, infrastructure, or configuration changes.

Important

A failed build does not prove that the website outage was caused by that build.

3.2 Deployment Failed

Example:

Build ✅
   |
   v
Deployment ⚠️
   |
   +--> Some instances updated
   +--> Some instances still running old version


Investigate:

Partial rollout

Container startup failures

Missing environment variables

Missing secrets

Incorrect configuration

Image pull failures

Resource constraints

Unhealthy targets

Failed readiness/liveness checks

A deployment can fail while already having changed part of the production environment.

3.3 Post-Deployment Checks Failed

Example:

Build ✅
   |
Deployment ✅
   |
Traffic switched ✅
   |
Smoke tests ❌


This situation is particularly important.

The pipeline can be red while the new version is already serving production traffic.

Therefore:

A red pipeline does not necessarily mean the previous version is running.

Verify what is actually deployed before deciding whether to retry or roll back.

4. Restore Service Safely

If evidence shows that the release caused the outage, restore service using the safest available mechanism.

Possible approaches include:

Roll back to a known healthy application version.

Switch traffic to a verified healthy environment.

Route traffic away from unhealthy instances.

Apply a targeted fix when rollback is unsafe.

Rollback Considerations

Before rolling back an application, check database compatibility.

For example:

Version A
   |
   | Database migration
   v
Version B


If Version B changed the database schema:

Database schema = Version B
Application = Version A


Version A may no longer work correctly.

Therefore:

The previous application version must still be compatible with the current database state.

This is one reason database migrations should be designed with backward compatibility in mind.

5. Troubleshooting Kubernetes

When Kubernetes is involved, identify the actual failure instead of relying only on pod status.

5.1 Pods Are Pending
Pod
 |
 └── Pending


Investigate:

Scheduling events

Available cluster capacity

CPU/memory requests

Node availability

Taints and tolerations

Persistent storage

PersistentVolume/PersistentVolumeClaim problems

Useful commands:

kubectl get pods -n production

kubectl describe pod <pod-name> -n production

kubectl get events -n production --sort-by=.lastTimestamp

5.2 CrashLoopBackOff
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


Check the previous container's logs:

kubectl logs <pod-name> -n production --previous


Also inspect:

kubectl describe pod <pod-name> -n production


Look for:

Application startup errors

Missing environment variables

Missing secrets

Configuration errors

Dependency failures

Out-of-memory termination

Incorrect command/entrypoint

5.3 Readiness Probe Failures

A pod can be running but not ready to receive traffic.

Pod
 |
 +-- Running
 |
 └-- Ready ❌


Check:

Readiness endpoint

Container port

Service port

Application startup time

Required dependencies

Database connectivity

External service dependencies

For example:

readinessProbe:
  httpGet:
    path: /health
    port: 8080


A successful container startup does not automatically mean the application is ready to serve users.

5.4 Pods Are Healthy, But Requests Still Fail

This is an important troubleshooting scenario.

Pods ✅
 |
Service ?
 |
Ingress ?
 |
Load Balancer ?
 |
Users ❌


Investigate:

Kubernetes Service

Service endpoints

EndpointSlices

Ingress configuration

Ingress controller

Load balancer target health

DNS

TLS configuration

Network policies

Routing rules

Useful commands:

kubectl get svc -n production

kubectl get endpoints -n production

kubectl get endpointslices -n production

kubectl get ingress -n production

Key principle

A running container does not prove that the website works.

You need to verify the entire request path:

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

6. Verify Recovery

Restoring infrastructure is not the same as confirming that users can use the application.

After recovery, verify the actual user journey.

Functional verification

Test a realistic flow such as:

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


For an e-commerce application, this might be:

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

Monitor after recovery

Confirm that:

Error rates have returned to normal.

Latency has recovered.

Load balancer targets are healthy.

Application pods remain stable.

No repeated crashes are occurring.

Key user journeys work.

No new alerts are firing.

Do not immediately declare the incident resolved after seeing one successful request.

7. Document the Root Cause

After service has stabilized, document what happened.

A useful incident report should contain:

Timeline
10:00  Deployment started
10:02  New version deployed
10:03  Readiness checks failed
10:04  Error rate increased
10:06  Deployment stopped
10:08  Traffic moved to healthy environment
10:12  Error rate returned to normal

Root cause

Explain the technical cause using evidence.

Example:

The new application version was deployed successfully, but the
application failed its readiness checks because a required
configuration value was missing. Traffic was partially routed to
the
unhealthy instances, resulting in elevated 503 responses.

Contributing factors

Consider:

Missing deployment validation

Incomplete health checks

Configuration drift

Insufficient monitoring

Inadequate rollback automation

Database migration compatibility

Missing smoke tests

8. Improve the Deployment Process

The goal is not simply to fix one incident.

Use the incident to improve the system.

Possible improvements:

Add stronger pre-deployment validation.

Add automated smoke tests.

Improve readiness probes.

Use gradual/canary deployments.

Add automated rollback conditions.

Validate configuration before deployment.

Make database migrations backward compatible.

Improve alerting.

Add end-to-end health checks.

Document the rollback procedure.

Regularly test recovery procedures.

Interview Answer

If an interviewer asks:

"Your production pipeline failed and the website is down. What would you do?"

A structured answer could be:

"I wouldn't immediately retry the pipeline. First, I'd confirm the user impact and establish whether the outage actually correlates with the deployment. I'd check monitoring, application errors, load balancer health, and the deployment timeline.

Then I'd pause additional production changes and preserve the current state, including the deployed version and logs.

Next, I'd identify where the pipeline failed. If it failed during build or testing, the deployment may never have started, so I'd investigate other recent changes. If the deployment partially completed, I'd investigate unhealthy instances, configuration, container startup issues, and readiness failures. I'd also check whether post-deployment checks failed after traffic had already switched.

If the release is confirmed as the cause, I'd restore service using a known healthy version or another verified environment. Before rolling back, I'd verify database compatibility because the previous application version must work with the current database schema.

For Kubernetes, I'd check pod states, events, previous container logs, readiness probes, Services, endpoints, Ingress, and load balancer health. A running pod alone doesn't prove that the application is reachable.

Finally, I'd verify recovery using a real user journey and monitor error rates and latency. After the incident, I'd document the root cause and improve deployment validation, health checks, rollback mechanisms, and recovery procedures."

Production Incident Checklist

Use this checklist during an incident:

[ ] Confirm user impact
[ ] Identify affected functionality
[ ] Check error rates and latency
[ ] Check load balancer target health
[ ] Compare outage timeline with deployment events

[ ] Pause additional deployments
[ ] Notify incident channel and owners
[ ] Record deployed version
[ ] Preserve logs and monitoring data

[ ] Determine whether deployment actually started
[ ] Identify failed pipeline stage
[ ] Check for partial rollout
[ ] Check application/container health
[ ] Check configuration and secrets
[ ] Check readiness and liveness probes

[ ] Determine whether the release caused the outage
[ ] Check database compatibility
[ ] Roll back or switch traffic if appropriate
[ ] Apply targeted fix if rollback is unsafe

[ ] Test real user journey
[ ] Confirm error rates recovered
[ ] Confirm latency recovered
[ ] Continue monitoring

[ ] Document timeline
[ ] Document root cause
[ ] Document contributing factors
[ ] Create follow-up improvements

Key Takeaways

A failed pipeline does not automatically mean the deployment caused the outage.

First establish the timeline and actual user impact.

Determine whether anything was deployed before retrying.

A deployment can partially succeed even when the pipeline is red.

Post-deployment checks can fail after traffic has already switched.

Always consider database compatibility before rolling back.

A Running Kubernetes pod does not guarantee a working website.

Trace the complete request path from the user to the application.

Verify recovery using real user journeys, not just infrastructure health.

After recovery, improve the deployment and recovery process to prevent recurrence.

Golden Rule

Don't retry blindly. Establish what changed, determine what is actually serving traffic, restore service safely, and verify the recovery with evidence.
