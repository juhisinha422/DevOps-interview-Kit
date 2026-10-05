Here are detailed README-style notes for HTTP Error Codes with DevOps, AWS, Kubernetes, Nginx, and real-time production examples.

https://www.anjidevops.online/blog/
# HTTP Status Codes – Complete DevOps & AWS Notes

## What are HTTP Status Codes?

HTTP status codes are 3-digit responses returned by a server when a client (browser, API, mobile app, curl, Postman, etc.) sends a request.

They help identify:

* Request success
* Client-side issues
* Server-side failures
* Redirections
* Authentication problems

As a DevOps/SRE engineer, these codes are extremely important while:

* Troubleshooting production issues
* Debugging APIs
* Monitoring ALB/Nginx logs
* Investigating Kubernetes ingress failures
* Observing CloudWatch/Grafana alerts

---

# HTTP Status Code Categories

| Category | Meaning       |
| -------- | ------------- |
| 1xx      | Informational |
| 2xx      | Success       |
| 3xx      | Redirection   |
| 4xx      | Client Errors |
| 5xx      | Server Errors |

---

# 1xx – Informational Responses

These indicate the request was received and processing is continuing.

---

## 100 Continue

Meaning:

* Server received request headers
* Client can continue sending request body

Used in:

* Large file uploads
* API uploads

Example:

```bash id="jlwmk1"
HTTP/1.1 100 Continue
```

Real-time usage:

* Uploading large files to application servers

---

## 101 Switching Protocols

Meaning:

* Server agrees to switch protocols

Commonly used in:

* WebSockets
* Real-time chat apps

Example:

```text id="wpfz9t"
HTTP → WebSocket upgrade
```

---

## 102 Processing

Meaning:

* Request accepted and processing is still happening

Mostly used in:

* WebDAV operations

---

# 2xx – Success Responses

Indicates request completed successfully.

---

## 200 OK

Meaning:

* Request successful

Most common status code.

Examples:

* Successful API call
* Website loads correctly

Example:

```bash id="2wy6md"
HTTP/1.1 200 OK
```

AWS Example:

* ALB successfully routed traffic to healthy EC2/EKS pods

---

## 201 Created

Meaning:

* Resource created successfully

Used in:

* User registration
* Database insertion APIs

Example:

```bash id="4v87qg"
HTTP/1.1 201 Created
```

---

## 204 No Content

Meaning:

* Request successful
* No response body returned

Used in:

* DELETE operations

Example:

```bash id="gk08jh"
HTTP/1.1 204 No Content
```

---

## 206 Partial Content

Meaning:

* Only part of file/resource returned

Used in:

* Video streaming
* Resume downloads

---

# 3xx – Redirection Responses

These indicate additional action is required.

---

## 301 Moved Permanently

Meaning:

* URL permanently changed

Important for:

* SEO
* Website migration

Example:

```text id="tjlwm1"
http://example.com → https://example.com
```

Nginx Example:

```nginx id="dcrzv2"
return 301 https://$host$request_uri;
```

---

## 302 Found (Temporary Redirect)

Meaning:

* Temporary redirection

Common use:

* Temporary maintenance pages

---

## 304 Not Modified

Meaning:

* Browser cache is still valid

Benefits:

* Faster loading
* Reduced bandwidth

---

## 307 Temporary Redirect

Meaning:

* Temporary redirect
* HTTP method remains same

Difference from 302:

* POST remains POST

---

## 308 Permanent Redirect

Meaning:

* Permanent redirect
* HTTP method preserved

---

# 4xx – Client Errors

These occur due to client-side mistakes.

---

## 400 Bad Request

Meaning:

* Invalid or malformed request

Causes:

* Invalid JSON
* Missing parameters
* Bad syntax

Example:

```bash id="9bdyq8"
HTTP/1.1 400 Bad Request
```

API Example:

* Incorrect request payload

---

## 401 Unauthorized

Meaning:

* Authentication required

Causes:

* Missing token
* Invalid credentials

Example:

```bash id="xjlwm8"
HTTP/1.1 401 Unauthorized
```

AWS Example:

* Invalid IAM credentials

---

## 403 Forbidden

Meaning:

* User authenticated
* But not authorized

Difference:

* 401 = Who are you?
* 403 = I know you, but access denied

AWS Example:

* S3 bucket permission denied

Example:

```bash id="6r0lkg"
AccessDenied
```

---

## 404 Not Found

Meaning:

* Resource does not exist

Causes:

* Wrong URL
* Missing endpoint
* Deleted resource

Example:

```bash id="7j0kfe"
HTTP/1.1 404 Not Found
```

Kubernetes Example:

* Incorrect ingress path routing

---

## 405 Method Not Allowed

Meaning:

* HTTP method not supported

Example:

```text id="8qmp2z"
GET allowed but POST attempted
```

---

## 429 Too Many Requests

Meaning:

* Rate limit exceeded

Common in:

* APIs
* Cloudflare
* WAF systems

Protection against:

* DDoS
* Spam bots

AWS Example:

* API Gateway throttling

---

# 5xx – Server Errors

These are server-side failures.

Most critical in production systems.

---

## 500 Internal Server Error

Meaning:

* Generic server failure

Causes:

* Application crash
* Unhandled exceptions
* Database failures

Troubleshooting:

* Check application logs
* Verify DB connectivity
* Analyze stack traces

Linux Commands:

```bash id="r3p6nq"
tail -f app.log
```

---

## 502 Bad Gateway

Meaning:

* Proxy received invalid response from backend

Common architecture:

```text id="1s1nmt"
User → ALB/Nginx → App Server
```

Common causes:

* Backend crashed
* Wrong upstream config
* Service unavailable

Nginx Example:

```text id="u7d6vj"
502 Bad Gateway
```

AWS Example:

* ALB unable to connect EC2/EKS pod

Troubleshooting:

* Check backend health
* Verify target groups
* Validate service endpoints

---

## 503 Service Unavailable

Meaning:

* Server overloaded or unavailable

Causes:

* Application downtime
* Resource exhaustion
* Maintenance mode

Kubernetes Example:

* No healthy pods available

AWS Example:

* Target group unhealthy

Troubleshooting:

* Check pod status
* Verify autoscaling
* Check readiness probes

Commands:

```bash id="rvmgq1"
kubectl get pods
```

```bash id="gh1qmp"
kubectl describe pod <pod-name>
```

---

## 504 Gateway Timeout

Meaning:

* Upstream server did not respond in time

Very common in:

* Load balancers
* Reverse proxies
* APIs

Architecture:

```text id="hn0xmq"
Client → ALB/Nginx → Backend
```

Causes:

* Slow database queries
* Backend latency
* Network issues
* Timeout settings

AWS Example:

* ALB idle timeout exceeded

Troubleshooting:

* Check backend response time
* Verify DB performance
* Analyze latency metrics
* Tune timeout values

---

# Real-Time DevOps Troubleshooting Examples

---

## Scenario 1 – 502 Bad Gateway in Kubernetes

Issue:

* Users unable to access application

Checks:

1. Verify pod health
2. Check ingress controller logs
3. Validate service endpoints
4. Check readiness probes

Commands:

```bash id="5yyzk8"
kubectl get endpoints
```

```bash id="3m2x7o"
kubectl logs <pod-name>
```

---

## Scenario 2 – Frequent 503 Errors During Traffic Spike

Cause:

* Insufficient replicas

Solution:

* Enable HPA
* Increase pod replicas
* Optimize resources

---

## Scenario 3 – 504 Errors in AWS ALB

Cause:

* Backend API slow response

Solution:

* Increase ALB timeout
* Optimize application
* Tune database queries

---

# HTTP Codes Frequently Seen in DevOps

| Code | Meaning             | Common Usage        |
| ---- | ------------------- | ------------------- |
| 200  | Success             | Healthy application |
| 301  | Redirect            | HTTPS redirect      |
| 400  | Bad request         | API validation      |
| 401  | Unauthorized        | Auth failure        |
| 403  | Forbidden           | Permission issue    |
| 404  | Not found           | Missing endpoint    |
| 429  | Too many requests   | Rate limiting       |
| 500  | Internal error      | App crash           |
| 502  | Bad gateway         | Proxy/backend issue |
| 503  | Service unavailable | Pods unavailable    |
| 504  | Gateway timeout     | Slow backend        |

---

# Important DevOps Monitoring Areas

As DevOps/SRE engineers, we monitor:

* HTTP error spikes
* Latency
* Request rates
* Backend health
* Load balancer health
* Pod availability

Tools:

* Prometheus
* Grafana
* ELK
* CloudWatch
* Datadog

---

# Interview Tips

Remember:

* 4xx = Client-side issues
* 5xx = Server-side issues

Very common interview questions:

* Difference between 502 and 504
* Difference between 401 and 403
* Why 503 occurs in Kubernetes
* How ALB handles HTTP errors
* How to troubleshoot 500 errors

---

# Quick Memory Trick

| Series | Meaning           |
| ------ | ----------------- |
| 1xx    | Hold on           |
| 2xx    | Success           |
| 3xx    | Go somewhere else |
| 4xx    | Client mistake    |
| 5xx    | Server problem    |

---
