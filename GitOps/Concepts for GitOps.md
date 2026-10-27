# GitOps Engineer: Concepts You Should Know

Study notes covering the 20 core areas of a GitOps engineer's toolkit. Each section has the key ideas, the commands or snippets you'll actually use, and a short checklist.

## Table of Contents

1. [Linux](#1-linux)
2. [Git](#2-git)
3. [YAML](#3-yaml)
4. [Kubernetes](#4-kubernetes)
5. [GitOps Principles](#5-gitops-principles)
6. [Argo CD](#6-argo-cd)
7. [Flux CD](#7-flux-cd)
8. [CI/CD Pipelines](#8-cicd-pipelines)
9. [Helm](#9-helm)
10. [Kustomize](#10-kustomize)
11. [Terraform](#11-terraform)
12. [Secrets Management](#12-secrets-management)
13. [RBAC & Security](#13-rbac--security)
14. [Observability](#14-observability)
15. [Reconciliation & Drift Detection](#15-gitops-reconciliation--drift-detection)
16. [Rollbacks & Disaster Recovery](#16-rollbacks--disaster-recovery)
17. [Multi-Environment Deployments](#17-multi-environment-deployments)
18. [Troubleshooting](#18-troubleshooting)
19. [Cloud Platforms](#19-cloud-platforms)
20. [GitOps Project Practice](#20-gitops-project-practice)

---

## 1. Linux

Almost every GitOps component runs in Linux containers, so shell fluency is non-negotiable.

**Core areas**
- Filesystem navigation, permissions (`chmod`, `chown`), users and groups
- Processes and services: `ps`, `top`, `systemctl`, `journalctl`
- Networking: `curl`, `dig`, `ss`, `netstat`, `ip`, `iptables` basics
- Text processing: `grep`, `awk`, `sed`, `jq`, `yq`
- Shell scripting: variables, conditionals, loops, exit codes, `set -euo pipefail`
- SSH and key management

```bash
# Useful everyday one-liners
journalctl -u kubelet -f                 # follow a service's logs
ss -tulpn                                # listening ports and owning processes
curl -sS -o /dev/null -w "%{http_code}\n" https://example.com
kubectl get pods -o json | jq '.items[].metadata.name'
```

**Checklist:** Can you read logs, inspect ports, fix file permissions, and write a safe bash script?

---

## 2. Git

Git is the **source of truth** in GitOps, so you need to know it deeply, not just `add/commit/push`.

**Core areas**
- Commits, branches, tags, remotes
- Merge vs. rebase, squash, cherry-pick
- `git revert` (safe rollback) vs. `git reset` (rewrites history)
- Pull requests and code review as the change-approval mechanism
- Branching strategies: trunk-based, GitFlow, environment branches vs. folders
- Signed commits (GPG/SSH), branch protection, CODEOWNERS
- Git hooks and pre-commit checks

```bash
git switch -c feature/bump-image
git commit -S -m "chore: bump api to v1.4.2"
git revert <commit-sha>                  # undo a deploy without rewriting history
git log --oneline --graph --decorate
git tag -a v1.4.2 -m "Release 1.4.2"
```

**Tip:** In GitOps, `git revert` is your rollback button. Avoid force-pushing to branches that a controller watches.

---

## 3. YAML

Kubernetes manifests, Helm values, CI pipelines, Argo CD and Flux resources are all YAML.

**Core areas**
- Scalars, lists, maps, nesting, and indentation (spaces only, never tabs)
- Multi-line strings: `|` (keeps newlines) and `>` (folds them)
- Anchors and aliases: `&anchor`, `*alias`
- Multi-document files separated by `---`
- Common pitfalls: `yes`/`no`/`on`/`off` parsed as booleans, unquoted version numbers like `1.10`

```yaml
app:
  name: api
  replicas: 3
  ports: [8080, 8443]
  config: |
    line one
    line two
defaults: &defaults
  cpu: 100m
prod:
  <<: *defaults
  memory: 256Mi
```

**Tools:** `yamllint`, `yq`, `kubeconform`, `kubectl apply --dry-run=client`

---

## 4. Kubernetes

The runtime that GitOps tools reconcile against.

**Core objects**
| Object | Purpose |
|---|---|
| Pod | Smallest deployable unit |
| Deployment | Manages stateless replicas and rollouts |
| StatefulSet | Stable identity and storage |
| DaemonSet | One pod per node |
| Job / CronJob | Batch and scheduled work |
| Service | Stable networking for pods (ClusterIP, NodePort, LoadBalancer) |
| Ingress / Gateway API | HTTP routing into the cluster |
| ConfigMap / Secret | Configuration and sensitive data |
| PersistentVolume / PVC | Storage |
| Namespace | Logical isolation |
| CRD | Extend the API (Argo CD `Application`, Flux `Kustomization`) |

**Also know:** the declarative model, controllers and reconcile loops, labels and selectors, probes (liveness, readiness, startup), resource requests and limits, HPA, taints and tolerations, NetworkPolicy, admission controllers.

```bash
kubectl get pods -A
kubectl describe pod <pod> -n <ns>
kubectl logs <pod> -c <container> --previous
kubectl rollout status deploy/api
kubectl diff -f manifest.yaml
kubectl auth can-i create deployments --as=dev-user -n prod
```

---

## 5. GitOps Principles

The four OpenGitOps principles:

1. **Declarative:** the desired system state is described declaratively.
2. **Versioned and immutable:** the desired state is stored in a way that enforces immutability and keeps full history (Git).
3. **Pulled automatically:** agents pull the desired state from the source rather than CI pushing into the cluster.
4. **Continuously reconciled:** software agents observe actual state and keep correcting it toward desired state.

**Push vs. pull**
- *Push (traditional CI/CD):* the pipeline holds cluster credentials and runs `kubectl apply`.
- *Pull (GitOps):* an in-cluster agent pulls from Git. The cluster credentials never leave the cluster, which is a significant security win.

**Benefits:** audit trail, easy rollback, consistency, self-healing, better security posture, and the PR workflow for all changes.

**Common repo layouts**
- *App repo* (source code) and *config repo* (manifests) kept separate
- Monorepo vs. polyrepo for config

---

## 6. Argo CD

A declarative, GitOps continuous delivery tool for Kubernetes with a strong UI.

**Core concepts**
- **Application:** maps a Git source (repo, path, revision) to a destination (cluster, namespace)
- **AppProject:** groups apps and restricts allowed repos, clusters, and namespaces
- **ApplicationSet:** generates many Applications from templates (list, cluster, git, matrix generators)
- **Sync policies:** manual vs. automated, `prune`, `selfHeal`
- **Sync waves and hooks:** order resources with `argocd.argoproj.io/sync-wave`, run PreSync/PostSync jobs
- **Health and sync status:** `Healthy / Progressing / Degraded`, `Synced / OutOfSync`
- App-of-apps pattern for bootstrapping

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: api
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/org/config-repo.git
    targetRevision: main
    path: apps/api/overlays/prod
  destination:
    server: https://kubernetes.default.svc
    namespace: api
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

```bash
argocd login argocd.example.com
argocd app list
argocd app sync api
argocd app diff api
argocd app history api
argocd app rollback api <ID>
```

**Companions:** Argo Rollouts (canary, blue-green), Argo Image Updater, Argo Workflows/Events.

---

## 7. Flux CD

A set of composable GitOps controllers (the GitOps Toolkit), driven entirely by CRDs and the CLI.

**Controllers**
| Controller | Role |
|---|---|
| source-controller | Fetches Git, OCI, Helm, and bucket sources |
| kustomize-controller | Applies Kustomizations |
| helm-controller | Manages HelmReleases |
| notification-controller | Alerts and webhooks |
| image-reflector / image-automation | Detect new images and commit updates to Git |

**Key resources:** `GitRepository`, `OCIRepository`, `HelmRepository`, `Kustomization`, `HelmRelease`, `Alert`, `Provider`, `ImagePolicy`.

```bash
flux bootstrap github --owner=org --repository=fleet --path=clusters/prod --personal
flux get all
flux reconcile kustomization apps --with-source
flux logs --level=error
flux suspend kustomization apps       # pause during maintenance
flux resume kustomization apps
```

```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: apps
  namespace: flux-system
spec:
  interval: 5m
  path: ./apps/prod
  prune: true
  sourceRef:
    kind: GitRepository
    name: fleet
```

**Argo CD vs. Flux (rough guide):** Argo CD has a rich UI and ApplicationSets. Flux is lighter, more modular, and Kubernetes-native with strong multi-tenancy and image automation.

---

## 8. CI/CD Pipelines

In GitOps, **CI builds and tests; CD is the controller reconciling Git.**

**Typical flow**
1. Developer opens a PR on the app repo
2. CI runs lint, unit tests, security scans (SAST, dependency, container image scan)
3. CI builds and pushes an image tagged with the commit SHA or semver
4. CI (or an image-automation tool) updates the image tag in the config repo
5. PR is reviewed and merged
6. Argo CD / Flux detects the change and syncs it to the cluster

**Tools:** GitHub Actions, GitLab CI, Jenkins, Tekton, CircleCI

**Best practices**
- Never use `latest`; use immutable tags or digests
- Sign images (Cosign/Sigstore) and generate SBOMs
- Validate manifests in CI (`kubeconform`, `kubeval`, `conftest`, `kyverno test`)
- Keep CI credentials scoped to the registry and config repo only, with no cluster access

```yaml
# GitHub Actions sketch
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: docker build -t ghcr.io/org/api:${{ github.sha }} .
      - run: docker push ghcr.io/org/api:${{ github.sha }}
```

---

## 9. Helm

The package manager for Kubernetes: templated manifests plus values.

**Core concepts**
- Chart structure: `Chart.yaml`, `values.yaml`, `templates/`, `charts/`
- Go templating, `_helpers.tpl`, `include`, `toYaml`, `nindent`
- Values override order: chart defaults, then `-f values.yaml`, then `--set`
- Releases, revisions, hooks, and tests
- Dependencies and OCI registries
- Argo CD renders Helm with `helm template` (no Tiller or release secrets by default); Flux uses `HelmRelease`

```bash
helm create mychart
helm lint mychart
helm template my-release ./mychart -f values-prod.yaml
helm upgrade --install my-release ./mychart -n app --create-namespace
helm history my-release
helm rollback my-release 2
```

**Watch out for:** over-templating, large unreadable values files, and chart versions not being pinned.

---

## 10. Kustomize

Template-free customization: a base plus overlays, built into `kubectl` (`kubectl apply -k`).

**Core concepts**
- `kustomization.yaml` with `resources`, `namePrefix`, `namespace`, `labels`, `images`
- **Patches:** strategic merge and JSON 6902
- `configMapGenerator` / `secretGenerator` with hash suffixes that trigger rollouts
- Components for reusable features
- Base and overlay layout per environment

```
apps/api/
├── base/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── kustomization.yaml
└── overlays/
    ├── dev/kustomization.yaml
    └── prod/kustomization.yaml
```

```yaml
# overlays/prod/kustomization.yaml
resources:
  - ../../base
images:
  - name: ghcr.io/org/api
    newTag: v1.4.2
patches:
  - path: replicas-patch.yaml
```

```bash
kustomize build apps/api/overlays/prod
kubectl diff -k apps/api/overlays/prod
```

**Helm vs. Kustomize:** Helm for packaging and distributing apps with many knobs; Kustomize for simple environment-specific tweaks. Many teams combine them (Kustomize `helmCharts`, or Flux `postBuild`).

---

## 11. Terraform

Infrastructure as Code for the layer *under* Kubernetes: clusters, networks, IAM, databases.

**Core concepts**
- Providers, resources, data sources, variables, outputs, locals
- State and **remote backends** (S3 + DynamoDB lock, GCS, Azure Blob, Terraform Cloud)
- Modules for reuse
- Workspaces vs. separate state per environment
- `plan` then `apply`, review the plan in PRs
- Drift detection with `terraform plan` on a schedule
- Alternatives and complements: OpenTofu, Crossplane, Terragrunt, Atlantis

```bash
terraform init
terraform fmt -recursive
terraform validate
terraform plan -out=tfplan
terraform apply tfplan
terraform state list
terraform import aws_s3_bucket.logs my-bucket
```

**Typical split:** Terraform provisions the cluster and bootstraps Argo CD or Flux; GitOps takes over from there for everything in-cluster.

---

## 12. Secrets Management

**Never commit plaintext secrets to Git.** Base64 is encoding, not encryption.

**Approaches**
| Approach | How it works |
|---|---|
| **Sealed Secrets** | Encrypt with a cluster public key; only the in-cluster controller can decrypt |
| **SOPS** (with age, KMS, or PGP) | Encrypts values inside YAML files; Flux has native SOPS decryption, Argo CD via plugins |
| **External Secrets Operator** | Syncs from AWS Secrets Manager, Vault, GCP Secret Manager, Azure Key Vault into K8s Secrets |
| **HashiCorp Vault** | Central secret store with dynamic secrets, injected via agent or CSI driver |
| **Secrets Store CSI Driver** | Mounts secrets from external stores as volumes |

```bash
# SOPS with age
age-keygen -o key.txt
sops --encrypt --age <public-key> --encrypted-regex '^(data|stringData)$' secret.yaml > secret.enc.yaml
sops --decrypt secret.enc.yaml

# Sealed Secrets
kubeseal --format yaml < secret.yaml > sealed-secret.yaml
```

**Also:** rotate secrets, enable etcd encryption at rest, scan repos for leaks (`gitleaks`, `trufflehog`), and prefer short-lived credentials and workload identity (IRSA, Workload Identity) over static keys.

---

## 13. RBAC & Security

**Kubernetes RBAC**
- `Role` / `ClusterRole` define permissions; `RoleBinding` / `ClusterRoleBinding` assign them
- ServiceAccounts for workloads
- Principle of least privilege

```bash
kubectl auth can-i list secrets -n prod --as=system:serviceaccount:app:api
kubectl get rolebindings,clusterrolebindings -A
```

**GitOps tool security**
- Argo CD: SSO (OIDC), RBAC policy in `argocd-rbac-cm`, AppProjects to limit scope
- Flux: multi-tenancy with per-tenant ServiceAccounts, `serviceAccountName` on Kustomizations
- Git: branch protection, required reviews, signed commits, CODEOWNERS

**Cluster and supply-chain hardening**
- Pod Security Standards (restricted/baseline), `securityContext` (non-root, read-only root FS, drop capabilities)
- NetworkPolicies, default deny
- Policy engines: **Kyverno**, **OPA Gatekeeper**
- Image scanning (Trivy, Grype), image signing and verification (Cosign)
- Audit logging and regular access reviews

---

## 14. Observability

You can't trust automation you can't see.

**Three pillars**
- **Metrics:** Prometheus, Grafana, Alertmanager
- **Logs:** Loki, Elasticsearch/OpenSearch, Fluent Bit
- **Traces:** OpenTelemetry, Jaeger, Tempo

**GitOps-specific signals**
- Sync status, health status, and last sync time per app
- Reconciliation duration and failure counts
- Drift events and out-of-sync duration
- Argo CD exports metrics at `/metrics`; Flux controllers expose Prometheus metrics and `gotk_reconcile_condition`
- Notifications to Slack, Teams, or email (Argo CD Notifications, Flux notification-controller)

**Practices:** define SLIs/SLOs, alert on symptoms rather than causes, dashboards-as-code in Git, annotate deploys on Grafana graphs.

---

## 15. GitOps Reconciliation & Drift Detection

**Reconciliation loop:** observe actual state, compare to desired state in Git, then act to converge them.

**Drift** is any difference between live cluster state and Git, such as a manual `kubectl edit`, an operator mutating a field, or a HPA changing replicas.

**Handling drift**
- **Detect only:** show `OutOfSync` and alert (Argo CD manual sync)
- **Auto-correct:** `selfHeal: true` (Argo CD) or Flux's periodic reconcile reverts changes
- **Ignore legitimate differences:** `ignoreDifferences` in Argo CD (e.g. HPA-managed `replicas`), `kustomize.toolkit.fluxcd.io/ssa: Ignore` annotations in Flux
- **Prune:** delete resources removed from Git (`prune: true`)

**Settings to understand:** reconcile interval, webhook-triggered syncs (faster than polling), server-side apply, health checks and `dependsOn` ordering.

```bash
argocd app diff api
flux diff kustomization apps
kubectl get kustomization -A
```

---

## 16. Rollbacks & Disaster Recovery

**Rollbacks**
- Preferred: **`git revert`** the bad commit, merge it, and let the controller sync. This keeps Git as the truth and leaves an audit trail.
- `argocd app rollback` or `helm rollback` work in emergencies but can leave Git and the cluster out of sync (and are blocked when auto-sync is on in Argo CD).
- Progressive delivery (Argo Rollouts, Flagger) with automated analysis and abort on bad metrics.

**Disaster recovery**
- Git holds the desired state, so rebuilding a cluster means: re-provision infra (Terraform), bootstrap Argo CD/Flux, point it at the repo, and let it reconcile.
- Back up what Git doesn't hold: **etcd**, persistent volumes (Velero, CSI snapshots), databases, and the **secret encryption keys** (SOPS/Sealed Secrets keys).
- Define **RPO** and **RTO**, document runbooks, and run restore drills regularly.
- Multi-region or multi-cluster failover, and mirrored Git and registry access.

```bash
velero backup create full-backup --include-namespaces app
velero restore create --from-backup full-backup
```

---

## 17. Multi-Environment Deployments

**Environment strategies**
| Strategy | Pros | Cons |
|---|---|---|
| Folder per environment (`overlays/dev`, `overlays/prod`) | Simple, visible diffs, easy promotion | Folder drift if not disciplined |
| Branch per environment | Familiar | Merge conflicts, hard to see drift |
| Repo per environment | Strong isolation and access control | More overhead |

Folder-per-environment on a single main branch is the most commonly recommended pattern.

**Promotion flow:** dev, then staging, then prod, with promotion via PR (copy or bump the image tag or chart version). Automate the dev bump and require approval for prod.

**Multi-cluster management**
- Argo CD ApplicationSets with cluster generators
- Flux fleet repo structure (`clusters/<name>`, `infrastructure/`, `apps/`)
- Cluster-scoped vs. app-scoped configuration, with per-environment values for replicas, resources, domains, and feature flags
- Use separate AppProjects or namespaces and tenant isolation

```
fleet/
├── clusters/{dev,staging,prod}/
├── infrastructure/      # ingress, cert-manager, monitoring
└── apps/
    ├── base/
    └── overlays/{dev,staging,prod}/
```

---

## 18. Troubleshooting

**Method:** check Git, then the controller, then the cluster resource, then the workload.

**Argo CD**
```bash
argocd app get api --refresh
kubectl -n argocd logs deploy/argocd-repo-server
kubectl -n argocd logs statefulset/argocd-application-controller
```
Common issues: repo auth failure, manifest render errors, `OutOfSync` caused by mutated fields, CRD ordering, stuck `Progressing` on failing health checks, sync hooks hanging.

**Flux**
```bash
flux get sources git
flux get kustomizations
flux get helmreleases -A
flux logs --all-namespaces --level=error
kubectl describe kustomization apps -n flux-system
```
Common issues: `GitRepository` not ready (credentials), SOPS decryption key missing, `dependsOn` not satisfied, Helm install timeout.

**Kubernetes**
| Symptom | Likely cause / first check |
|---|---|
| `ImagePullBackOff` | Wrong tag, registry auth, imagePullSecrets |
| `CrashLoopBackOff` | `kubectl logs --previous`, bad config, failing probe |
| `Pending` | Resources, taints, PVC unbound, node selectors |
| `OOMKilled` | Memory limit too low |
| Service unreachable | Selector/label mismatch, endpoints empty, NetworkPolicy |
| Webhook errors | Admission controller or cert issue |

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl describe pod <pod>
kubectl get endpoints <svc>
kubectl debug -it <pod> --image=busybox
```

---

## 19. Cloud Platforms

Know at least one cloud well and the equivalents on the others.

| Area | AWS | Azure | GCP |
|---|---|---|---|
| Managed Kubernetes | EKS | AKS | GKE |
| Container registry | ECR | ACR | Artifact Registry |
| Identity | IAM, IRSA / Pod Identity | Entra ID, Workload Identity | IAM, Workload Identity |
| Secrets | Secrets Manager, KMS | Key Vault | Secret Manager |
| Networking | VPC, ALB/NLB, Route 53 | VNet, App Gateway, DNS | VPC, Cloud LB, Cloud DNS |
| Storage | EBS, EFS, S3 | Managed Disks, Blob | Persistent Disk, GCS |
| Observability | CloudWatch | Azure Monitor | Cloud Monitoring |

**Concepts:** regions and availability zones, VPC design, load balancers, DNS, IAM and least privilege, autoscaling (cluster autoscaler, Karpenter), cost management (FinOps basics), and managed GitOps offerings (Azure Arc/AKS GitOps extension with Flux, GKE Config Sync).

---

## 20. GitOps Project Practice

Build these to make the concepts stick, in roughly this order.

1. **Local cluster:** create a cluster with `kind` or `minikube`, deploy a sample app with plain manifests.
2. **First GitOps loop:** install Argo CD (or Flux), point it at a repo, and watch a Git change appear in the cluster.
3. **Drift experiment:** `kubectl edit` a live resource, then observe `selfHeal` revert it.
4. **Helm + Kustomize:** deploy a chart, then add dev/prod overlays.
5. **Secrets:** add SOPS or Sealed Secrets and commit only encrypted values.
6. **CI pipeline:** GitHub Actions builds an image, pushes it, and opens a PR that bumps the tag in the config repo.
7. **Multi-environment:** dev, staging, and prod folders with promotion PRs.
8. **Progressive delivery:** canary with Argo Rollouts or Flagger.
9. **Observability:** Prometheus and Grafana dashboards plus Slack alerts for sync failures.
10. **Policy and security:** Kyverno rules, RBAC, branch protection, and image signing.
11. **Infrastructure:** Terraform provisions a cloud cluster and bootstraps GitOps.
12. **DR drill:** destroy the cluster and rebuild it from Git alone.

**Capstone idea:** a fleet repo that provisions two clusters with Terraform, bootstraps Flux or Argo CD, deploys a microservices app through CI-driven image promotion, and includes monitoring, secrets, policies, and a documented recovery runbook.

---

## Quick Reference: Learning Path

```
Linux → Git → YAML → Kubernetes → GitOps Principles
      → Helm / Kustomize → Argo CD or Flux → CI/CD
      → Secrets, RBAC, Observability → Terraform & Cloud
      → Multi-env, DR, Troubleshooting → Real projects
```

## Useful Resources

- [OpenGitOps principles](https://opengitops.dev)
- [Argo CD docs](https://argo-cd.readthedocs.io)
- [Flux docs](https://fluxcd.io/docs/)
- [Kubernetes docs](https://kubernetes.io/docs/)
- [Helm docs](https://helm.sh/docs/)
- [Kustomize docs](https://kubectl.docs.kubernetes.io/references/kustomize/)
- [Terraform docs](https://developer.hashicorp.com/terraform/docs)
- [CNCF Certifications](https://www.cncf.io/training/certification/): CKA, CKAD, CKS, and the CNCF GitOps Associate (CGOA)
