# 🚨 Production Issue – Dashboard V2.0 APIs Returning 404

## 📌 Issue Summary

Most of the Dashboard V2.0 APIs were returning **404 Not Found** response.

The issue was observed in browser Network tab for APIs like:

```json
{
  "status": 404,
  "error": "Not Found",
  "path": "/gateway/nsws_olap_report/getPendencyData"
}
```

Dashboard widgets such as:
- Pendency Analysis
- Approval Counts
- Dashboard Cards

were not loading properly.

---

# 🔍 Troubleshooting Steps

## 1️⃣ Verified Failed APIs from Browser

Checked API failures from Chrome DevTools → Network tab.

Observed failing API:

```bash
/gateway/nsws_olap_report/getPendencyData
```

Response:

```json
404 Not Found
```

This confirmed that the request was reaching API Gateway but routing was failing internally.

---

## 2️⃣ Verified Kubernetes Ingress

Executed:

```bash
kubectl get ingress -n tcs-prod
```

Verified ingress resources:

- api-gateway
- api-gateway-1
- api-gateway-bi-report
- react-app

All ingresses were healthy and attached to internal ALB successfully.

---

## 3️⃣ Checked Ingress Rules

Executed:

```bash
kubectl describe ingress api-gateway -n tcs-prod
```

Verified ingress path mappings:

```bash
/gateway/
/DMS_min/
/nsws_external_integration/
/nsws-unified-approvals
```

Backend services and target ports were resolving correctly.

No issue found in:
- ALB
- Ingress
- Kubernetes service mapping

---

# 🔎 Root Cause Analysis

Since ingress routing was working properly, next step was checking Zuul route configuration inside API Gateway.

Checked SVN configuration path:

```bash
WORKSPACE/PRODUCTION-NEW/app/NSWS_CONFIG
```

Reviewed gateway configuration file:

```bash
application.gateway2
```

Found incorrect Zuul route mapping:

```properties
zuul.routes.nsws_bi_report.url=${BIREPORT_SERVICE_URL}
zuul.routes.nsws_olap_report.url=${OLAP_SERVICE_URL}
```

OLAP report microservice was incorrectly pointing with `/gateway/` routing reference.

Because of this, APIs were getting routed incorrectly:

```bash
/gateway/nsws_olap_report/*
```

which resulted in HTTP 404 responses.

---

# ✅ Fix Implemented

Updated Zuul route configuration in:

```bash
application.gateway2
```

Corrected OLAP report service URL mapping by removing incorrect `/gateway/` reference.

Post changes:
- Saved configuration
- Committed changes in SVN
- Restarted/Redeployed API Gateway pods

---

# ✅ Validation Performed

## ✔ API Validation

Dashboard APIs started returning successful responses.

## ✔ Dashboard Validation

Verified:
- Pendency Analysis loaded successfully
- Dashboard cards populated correctly
- Approval count APIs started working

## ✔ Kubernetes Validation

Executed:

```bash
kubectl get pods -n tcs-prod

kubectl logs <gateway-pod> -n tcs-prod
```

No routing or 404 errors observed after deployment.

---

# 📌 Root Cause

Incorrect Zuul route configuration for OLAP report microservice in API Gateway.

The service route was mistakenly mapped through `/gateway/`, causing invalid backend routing and HTTP 404 responses.

---

# 🛠 Key Learnings

- Always verify:
  - Browser Network tab
  - Kubernetes ingress mappings
  - Gateway route configurations
  - Zuul/Spring Cloud route definitions

- 404 errors in microservice architecture are not always ingress issues.

- Many routing issues originate from:
  - Incorrect gateway routes
  - Wrong service URLs
  - Invalid path rewrites

- Comparing ingress paths with Zuul mappings helps isolate issues faster.

---

# 🔧 Commands Used

```bash
kubectl get ingress -n tcs-prod

kubectl describe ingress api-gateway -n tcs-prod

kubectl get pods -n tcs-prod

kubectl logs <pod-name> -n tcs-prod
```

---

# 🚀 Final Resolution

Dashboard V2.0 APIs were restored successfully after correcting Zuul route configuration for OLAP report microservice in API Gateway configuration.


In one of the production incidents, most of the Dashboard V2.0 APIs suddenly started returning HTTP 404 errors. Users were unable to load dashboard components like Pendency Analysis, approval counts, and dashboard cards. I started troubleshooting from the frontend by checking the browser Network tab and noticed that APIs such as `/gateway/nsws_olap_report/getPendencyData` were failing with 404 responses. Initially, we suspected an ingress or ALB issue, so I verified Kubernetes ingress resources and checked ingress path mappings using `kubectl describe ingress`. Everything looked healthy at the Kubernetes and ALB level.

Then I moved to the API Gateway layer and reviewed the Zuul route configuration inside the `application.gateway2` properties file. During analysis, I found that the OLAP report microservice route was incorrectly configured with the `/gateway/` path reference. Because of this incorrect routing, requests were not reaching the actual backend service and resulted in 404 errors. I corrected the Zuul route mapping, updated the configuration in SVN, and restarted the API Gateway pods. After deployment, all dashboard APIs started returning 200 responses and the dashboard loaded successfully.

This issue taught me the importance of checking the complete request flow in microservice architecture — from frontend calls, ingress routing, API Gateway configuration, and backend service mapping — instead of assuming every 404 is a Kubernetes ingress issue.


-------

## I've debugged broken Kubernetes clusters at 2am more times than I'd like to admit.

After years of doing this — here are the 7 most painful scenarios I've personally debugged in production. 
━━━━━━━━━━━━━━━━━━━━━
## 🔴 Scenario 1 — CrashLoopBackOff
━━━━━━━━━━━━━━━━━━━━━

You deploy a new service. Pod comes up. Dies. Comes back. Dies again.

Real example: We had a Node.js service that needed a DATABASE_URL env var to boot. It was missing in the Deployment spec. Pod exited with code 1 every 10 seconds.

How I fixed it:

→ kubectl logs <pod> --previous (the "previous" flag is gold — logs from the dead container)

→ kubectl describe pod <pod> — looked at the Exit Code and Events

→ Found the missing env var, patched the secret, redeployed

Pro tip: "--previous" is the most underused kubectl flag. Use it.

━━━━━━━━━━━━━━━━━━━━━
## 🟡 Scenario 2 — Pod Stuck in Pending Forever
━━━━━━━━━━━━━━━━━━━━━

Pod created. Status: Pending. 10 minutes pass. Still Pending.

Real example: A data pipeline pod requested 8Gi memory. Every node had 6Gi free. Nobody noticed the mismatch for 45 minutes.

How I fixed it:

→ kubectl describe pod — Events said "0/3 nodes available: Insufficient memory"

→ kubectl top nodes — confirmed memory pressure across all nodes

→ Reduced the memory request + added a new spot node group

Pro tip: Always set realistic resource requests. Wildly high requests will silently strand your pods.

━━━━━━━━━━━━━━━━━━━━━
## 🔴 Scenario 3 — ImagePullBackOff
━━━━━━━━━━━━━━━━━━━━━

Happens on almost every first deploy to a new cluster.

Real example: Team pushed image to a private ECR repo. Forgot to attach the AmazonEC2ContainerRegistryReadOnly policy to the node IAM role. Classic.

How I fixed it:

→ kubectl describe pod — Events showed "401 Unauthorized" from ECR

→ Verified IAM role attached to the node group in AWS console

→ Added the ECR read policy. Pods started in under 2 minutes.

Pro tip: For EKS, always verify node IAM permissions before your first deploy. Don't wait for the 2am call.

━━━━━━━━━━━━━━━━━━━━━
## 🔴 Scenario 4 — OOMKilled (Exit Code 137)
━━━━━━━━━━━━━━━━━━━━━

Pod dies. No obvious error. Restart count climbing. Exit code 137.

Real example: A Spring Boot service had no -Xmx flag set. JVM grew until Linux killed the container. Memory limit was 512Mi. JVM was eating 900Mi. This ran fine for weeks until traffic doubled.

How I fixed it:

→ kubectl describe pod — Last State showed OOMKilled

→ kubectl top pod --containers — saw the actual usage spike

→ Added -Xmx400m to the JVM flags and bumped the limit to 640Mi

Pro tip: Never run JVM apps in containers without explicitly setting -Xmx. The JVM will use whatever it sees.


