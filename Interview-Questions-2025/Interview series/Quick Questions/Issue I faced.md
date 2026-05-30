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
