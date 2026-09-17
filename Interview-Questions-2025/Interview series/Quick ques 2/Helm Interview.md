# 🚀 Helm Interview Questions & Answers

## Kubernetes + Helm | Production-Level Interview Preparation

---

# 1️⃣ What is Helm and why do we use it with Kubernetes?

Helm is a package manager for Kubernetes. In Kubernetes, deploying a complete application can require multiple YAML manifests such as Deployments, Services, ConfigMaps, Secrets, Ingress and Horizontal Pod Autoscalers. Managing all these YAML files separately can become difficult, especially when the same application needs to be deployed across Dev, QA and Production. Helm solves this problem by packaging Kubernetes manifests into reusable **Charts** and allowing us to parameterize configuration using `values.yaml`. It also provides release management, versioning, upgrades and rollbacks. In my projects, I use Helm to standardize Kubernetes deployments and maintain environment-specific configurations without duplicating the entire set of manifests.

### Simple flow:

```text
Helm Chart
    ↓
values.yaml
    ↓
Templates
    ↓
Helm renders Kubernetes YAML
    ↓
Kubernetes API
    ↓
Deployment / Service / ConfigMap / Ingress etc.
```

---

# 2️⃣ What is the difference between Helm 2 and Helm 3? Why was Tiller removed?

The major architectural difference is that Helm 2 used a server-side component called **Tiller**, which ran inside the Kubernetes cluster and was responsible for managing Helm releases. This introduced additional security and permission concerns because Tiller needed access to Kubernetes resources. Helm 3 removed Tiller and moved the release-management functionality into the Helm client itself. Helm 3 communicates directly with the Kubernetes API using the user's or service account's Kubernetes credentials and RBAC permissions. This simplified the architecture and improved security because there is no longer a centralized Tiller component requiring broad cluster permissions.

### Key difference:

| Helm 2                                 | Helm 3                          |
| -------------------------------------- | ------------------------------- |
| Used Tiller                            | Tiller removed                  |
| Client + Server architecture           | Client-based architecture       |
| Tiller required Kubernetes permissions | Uses Kubernetes RBAC directly   |
| More complex security model            | Simpler security model          |
| Releases managed through Tiller        | Releases managed without Tiller |

### Interview line:

> **"Tiller was removed in Helm 3 mainly to simplify the architecture and improve security by allowing Helm to communicate directly with the Kubernetes API using normal Kubernetes RBAC."**

---

# 3️⃣ Explain the Helm Chart structure — `Chart.yaml`, `values.yaml`, `templates/`, and `_helpers.tpl`.

A Helm Chart is a collection of files that defines how an application should be deployed on Kubernetes.

### `Chart.yaml`

This file contains metadata about the chart, such as the chart name, description, chart version and application version.

Example:

```yaml
apiVersion: v2
name: my-application
description: Helm chart for my application
type: application
version: 1.0.0
appVersion: "1.0"
```

### `values.yaml`

This contains the default configuration values used by the templates.

For example:

```yaml
replicaCount: 3

image:
  repository: myapp
  tag: "1.0.0"

service:
  port: 80
```

Instead of hardcoding these values inside Kubernetes manifests, I reference them through Helm templates.

### `templates/`

This directory contains the Kubernetes manifest templates, such as:

```text
deployment.yaml
service.yaml
configmap.yaml
ingress.yaml
hpa.yaml
```

Helm processes these templates using the values provided in `values.yaml` or through command-line overrides.

### `_helpers.tpl`

This file is generally used for reusable Helm template helpers, such as generating consistent names and labels.

For example:

```yaml
{{- define "myapp.fullname" -}}
{{ .Release.Name }}-{{ .Chart.Name }}
{{- end }}
```

I can then use the helper through `include`.

### Overall structure:

```text
my-chart/
│
├── Chart.yaml
├── values.yaml
├── Chart.lock
│
├── charts/
│
└── templates/
    ├── deployment.yaml
    ├── service.yaml
    ├── ingress.yaml
    ├── configmap.yaml
    ├── _helpers.tpl
    └── ...
```

---

# 4️⃣ How do you manage Dev, QA, and Production environments using Helm?

I normally use a common Helm chart and maintain environment-specific values rather than creating completely separate charts for every environment. For example, I can have:

```text
values-dev.yaml
values-qa.yaml
values-prod.yaml
```

The common templates remain the same, while environment-specific configuration such as replica count, image tag, resource limits, domain names and feature flags can differ.

For example:

```bash
helm upgrade --install myapp ./my-chart \
  -f values-dev.yaml
```

For QA:

```bash
helm upgrade --install myapp ./my-chart \
  -f values-qa.yaml
```

For Production:

```bash
helm upgrade --install myapp ./my-chart \
  -f values-prod.yaml
```

I prefer keeping environment differences in configuration rather than duplicating Kubernetes manifests. For production, I also use appropriate approval gates and separate credentials or deployment identities. Secrets should not be committed directly into these values files.

### Typical structure:

```text
helm/
└── my-app/
    ├── Chart.yaml
    ├── values.yaml
    ├── values-dev.yaml
    ├── values-qa.yaml
    ├── values-prod.yaml
    └── templates/
```

---

# 5️⃣ What is Helm templating? Explain `if/else`, `range`, `with`, `include`, and Helm functions.

Helm templating allows me to create dynamic Kubernetes manifests instead of hardcoding every value. Helm uses Go templates along with Helm-provided functions.

### `if / else`

I use `if` conditions when a Kubernetes resource or configuration should be created only when a particular value is enabled.

Example:

```yaml
{{- if .Values.ingress.enabled }}
apiVersion: networking.k8s.io/v1
kind: Ingress
...
{{- end }}
```

If `ingress.enabled` is true, Helm renders the Ingress.

---

### `range`

`range` is used to iterate over a list or map.

For example:

```yaml
{{- range .Values.environments }}
- {{ . }}
{{- end }}
```

This is useful when I need to dynamically generate multiple configuration entries.

---

### `with`

`with` changes the current context to a particular object and is useful for simplifying nested values.

For example:

```yaml
{{- with .Values.image }}
repository: {{ .repository }}
tag: {{ .tag }}
{{- end }}
```

---

### `include`

`include` allows me to reuse a named template, usually defined in `_helpers.tpl`.

Example:

```yaml
{{ include "myapp.fullname" . }}
```

This helps maintain consistent names and labels throughout the chart.

---

### Helm functions

Helm provides many functions for manipulating values, such as:

```text
default
quote
toYaml
nindent
required
tpl
lookup
```

For example:

```yaml
replicas: {{ .Values.replicaCount | default 1 }}
```

I commonly use functions such as `toYaml` and `nindent` when converting structured values into correctly indented Kubernetes YAML.

---

# 6️⃣ What is the difference between `helm install`, `helm upgrade`, `helm rollback`, and `helm uninstall`?

### `helm install`

I use `helm install` when deploying a new Helm release for the first time.

```bash
helm install myapp ./my-chart
```

It creates the Kubernetes resources defined by the chart.

---

### `helm upgrade`

I use `helm upgrade` when an existing Helm release needs to be updated with a new chart version or configuration.

```bash
helm upgrade myapp ./my-chart
```

In CI/CD, I often use:

```bash
helm upgrade --install myapp ./my-chart
```

This allows the command to install the release if it does not exist or upgrade it if it already exists.

---

### `helm rollback`

If a deployment introduces a problem, I can return the release to a previous revision.

```bash
helm rollback myapp 2
```

This is particularly useful for production incidents when the previous release was known to be healthy.

---

### `helm uninstall`

This removes the Helm release and the Kubernetes resources managed by that release.

```bash
helm uninstall myapp
```

### Simple comparison:

| Command          | Purpose                     |
| ---------------- | --------------------------- |
| `helm install`   | Deploy new release          |
| `helm upgrade`   | Update existing release     |
| `helm rollback`  | Return to previous revision |
| `helm uninstall` | Remove release              |

---

# 7️⃣ How do you troubleshoot a failed Helm deployment or Pods in `CrashLoopBackOff`?

I troubleshoot Helm failures at both the Helm and Kubernetes levels. First, I check the Helm release status and history to understand whether the deployment created a new revision and whether Helm itself reported an error. Then I check the Kubernetes resources created by that release. If pods are in `CrashLoopBackOff`, I check `kubectl get pods`, followed by `kubectl describe pod` and the container logs. I also check the previous container logs if the container is repeatedly restarting.

I verify the rendered Helm templates because sometimes the problem is caused by an incorrect value or template condition. I also check ConfigMaps, Secrets, environment variables, image tags, resource limits, probes and service dependencies. If the new release is clearly responsible and the previous revision was healthy, I can rollback the Helm release while continuing the root-cause investigation.

### My troubleshooting flow:

```text
Helm Status
     ↓
Helm History
     ↓
Compare Revisions
     ↓
Check Values
     ↓
Render Templates
     ↓
Check Kubernetes Resources
     ↓
kubectl describe pod
     ↓
Application Logs
     ↓
Check ConfigMap / Secret
     ↓
Check Image / Probes / Resources
     ↓
Fix
     ↓
Rollback if required
     ↓
Redeploy & Validate
```

---

# 8️⃣ What is Helm dependency management? How do `Chart.yaml` and `Chart.lock` work?

Helm allows one chart to depend on other charts. For example, an application chart might depend on Redis, PostgreSQL or another internal chart. Dependencies are declared in `Chart.yaml`.

Example:

```yaml
dependencies:
  - name: redis
    version: "20.0.0"
    repository: "https://example.com/charts"
```

I can then use:

```bash
helm dependency update
```

Helm downloads the required dependencies into the `charts/` directory.

`Chart.lock` records the resolved dependency versions and information about the dependency state. This helps make dependency resolution more predictable and repeatable. When dependencies change, the lock file can be regenerated. In a CI/CD pipeline, I can use the chart and lock file to ensure that the expected dependency versions are used rather than unexpectedly pulling different versions.

### Simple difference:

**Chart.yaml**

→ Defines what dependencies the chart requires.

**Chart.lock**

→ Records the resolved dependency versions.

---

# 9️⃣ How do you integrate Helm with Jenkins/GitHub Actions for CI/CD?

I integrate Helm into the deployment stage of the CI/CD pipeline. The pipeline first checks out the code, builds and tests the application, performs code-quality and security checks, builds the Docker image and pushes it to the container registry. Once the image is available, the deployment stage uses Helm to deploy that specific image version to Kubernetes.

For example, Jenkins can execute:

```bash
helm upgrade --install myapp ./helm/myapp \
  --namespace production \
  --create-namespace \
  -f values-prod.yaml \
  --set image.tag=$BUILD_NUMBER
```

I prefer using an immutable image tag such as a Git commit SHA or build ID rather than always deploying `latest`. Before deployment, I can use:

```bash
helm lint ./helm/myapp
```

and:

```bash
helm template myapp ./helm/myapp -f values-prod.yaml
```

to validate the chart and inspect the rendered manifests.

After deployment, I verify the rollout and application health.

### CI/CD flow:

```text
Git Commit
    ↓
Jenkins / GitHub Actions
    ↓
Build & Test
    ↓
Security Scans
    ↓
Docker Build
    ↓
Push Image
    ↓
Helm Lint
    ↓
Helm Template / Validation
    ↓
Helm Upgrade
    ↓
Kubernetes Rollout
    ↓
Health Check
    ↓
Monitoring
```

---

# 🔟 How do you securely manage Secrets when deploying applications using Helm?

I avoid storing passwords, API keys and database credentials directly in Helm templates or Git repositories. Although Kubernetes Secrets exist, I don't treat a plain Secret manifest committed to Git as sufficient protection. For production, I prefer integrating Kubernetes with a dedicated secret-management solution such as AWS Secrets Manager or another organization-approved secret store. Depending on the architecture, the secrets can be made available to workloads through an appropriate Kubernetes secret integration mechanism.

I also make sure CI/CD credentials have only the permissions they need and avoid printing secret values in Jenkins or GitHub Actions logs. Access to Kubernetes Secrets is controlled using RBAC, and cloud access should use workload identity or short-lived credentials wherever possible. I would also implement secret rotation and audit access to sensitive values.

### Secure approach:

```text
Secret Manager
      ↓
Controlled Access
      ↓
Kubernetes Workload
      ↓
Application
```

Rather than:

```text
Git Repository
      ↓
Plain Password
      ↓
Helm values.yaml
```

The key principle is:

> **Secrets should never be treated as normal application configuration or source code.**

---

# 🔥 MOST IMPORTANT REAL-TIME SCENARIO

# Your application was working before a Helm deployment, but after `helm upgrade`, Pods went into `CrashLoopBackOff`. How would you troubleshoot it?

This is how I would approach the issue in production:

---

## Step 1 — Check Helm Release Status

First, I would verify whether the Helm upgrade itself completed successfully.

```bash
helm status myapp -n production
```

I would check the release status and any messages reported during the upgrade.

---

## Step 2 — Check Helm History

Next, I would inspect the release history.

```bash
helm history myapp -n production
```

This tells me which revision was deployed and allows me to compare the current release with the previous working revision.

For example:

```text
REVISION    STATUS
1           superseded
2           deployed
3           deployed
```

If revision 3 introduced the issue and revision 2 was healthy, that gives me an important comparison point.

---

## Step 3 — Compare the Changes

I would identify what changed between the working and failing releases.

I would specifically look for changes to:

* Image tag
* Environment variables
* ConfigMaps
* Secrets
* Resource requests/limits
* Liveness probes
* Readiness probes
* Container command
* Arguments
* Service configuration
* Ingress
* Dependencies

The objective is to find the configuration difference that correlates with the failure.

---

## Step 4 — Check Current Pod Status

I would check the pods:

```bash
kubectl get pods -n production
```

If I see:

```text
CrashLoopBackOff
```

I would not immediately restart the pods. I would investigate why the application process is terminating.

---

## Step 5 — Describe the Pod

I would use:

```bash
kubectl describe pod <pod-name> -n production
```

This helps me identify:

* Container state
* Exit code
* Restart count
* OOMKilled
* Failed probes
* Image issues
* Mount failures
* Events
* Scheduling problems

For example, if I see:

```text
Reason: OOMKilled
```

I would investigate memory limits and application memory consumption.

If I see:

```text
Liveness probe failed
```

I would investigate whether the probe configuration changed during the Helm deployment.

---

## Step 6 — Check Application Logs

Next, I would check the logs:

```bash
kubectl logs <pod-name> -n production
```

Because the container is restarting, I would also check the previous container instance:

```bash
kubectl logs <pod-name> -n production --previous
```

This is particularly important for `CrashLoopBackOff` because the current container may not have enough runtime to produce useful logs.

I would look for errors such as:

```text
Database connection failed
Invalid configuration
Missing environment variable
Authentication failed
Application startup exception
Port already in use
Permission denied
```

---

## Step 7 — Check Helm Values

Since the problem started after `helm upgrade`, I would inspect the values being used by the release.

I would verify:

```text
image.repository
image.tag
replicaCount
resources
environment variables
ConfigMap references
Secret references
probe configuration
service configuration
```

A common production issue is an incorrect value being passed through an environment-specific values file.

---

## Step 8 — Render the Helm Templates

Before making another deployment, I would render the chart locally or in the CI pipeline:

```bash
helm template myapp ./my-chart \
  -f values-prod.yaml
```

This allows me to see the actual Kubernetes manifests generated by Helm.

I would compare the rendered Deployment with the previously working version and look for incorrect environment variables, image tags, commands, ports or probe configurations.

I can also validate the chart with:

```bash
helm lint ./my-chart
```

---

## Step 9 — Check ConfigMaps and Secrets

If the application fails during startup, I would verify that the required configuration exists.

```bash
kubectl get configmap -n production
```

and:

```bash
kubectl get secret -n production
```

I would verify that the pod is referencing the correct ConfigMap or Secret and that required keys are present.

I would **not print secret values into logs or expose them during troubleshooting**.

---

## Step 10 — Check the Image

I would verify that the new deployment is actually using the expected image.

For example:

```text
Previous:
myapp:abc123

Current:
myapp:def456
```

If the new image is causing the application to crash, I would inspect the image build and compare it with the previous known-good image.

I would also verify that the image was successfully built, pushed and pulled by Kubernetes.

---

## Step 11 — Check Probes

If the application itself is running but Kubernetes keeps restarting it, I would check:

```text
livenessProbe
readinessProbe
startupProbe
```

For example, if the application now takes 60 seconds to start but the liveness probe starts checking after only 10 seconds, Kubernetes could repeatedly restart an otherwise healthy application.

I would verify the endpoint, port, initial delay, timeout and failure thresholds.

---

## Step 12 — Fix the Root Cause

Once I identify the issue, I would fix the Helm values or template rather than manually editing the live Kubernetes resource.

For example:

```text
Incorrect image tag
        ↓
Correct image tag

Wrong database hostname
        ↓
Correct service hostname

Incorrect memory limit
        ↓
Appropriate resource configuration

Incorrect probe
        ↓
Correct health-check configuration
```

Then I would test the rendered configuration before deploying again.

---

## Step 13 — Rollback if Production Impact Is High

If production is actively impacted and the previous Helm release was healthy, I would prioritize service restoration.

I would use:

```bash
helm rollback myapp <previous-revision> -n production
```

Then verify:

```bash
kubectl rollout status deployment/myapp -n production
```

and check the pods and application behavior.

Rollback is a mitigation; I would still investigate the root cause afterward.

---

## Step 14 — Redeploy and Validate

After fixing the issue, I would deploy the corrected chart:

```bash
helm upgrade --install myapp ./my-chart \
  -f values-prod.yaml \
  -n production
```

Then I would validate:

```text
Helm release
      ↓
Deployment
      ↓
Pod status
      ↓
Pod logs
      ↓
Readiness
      ↓
Service endpoints
      ↓
Ingress / Load Balance
