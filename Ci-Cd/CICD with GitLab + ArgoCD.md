CI/CD with GitLab + ArgoCD?

Architecture diagram of the CI/CD pipeline (Docker + GitLab + ArgoCD + Kubernetes) with internal steps

Step 1

Developer: GitLab Repo - Developer writes code and pushes (git push) to GitLab.

step 2

GitLab Repo: GitLab CI/CD - GitLab detects the push and triggers pipeline defined in .gitlab-ci.yml

step 3

GitLab CI/CD: Docker Registry - Builds Docker image from Dockerfile & Pushes image to Docker Registry (GitLab Registry/Docker Hub).

step 4

GitLab Repo (Manifests/Helm):  ArgoCD - GitLab stores Kubernetes manifests/Helm charts and ArgoCD continuously watches this repo for changes.

step 5

ArgoCD: Kubernetes Cluster - When repo changes (new image/manifests), ArgoCD syncs the desired state with the cluster and Applies deployments, services, ingress, etc.

step 6

Kubernetes Cluster: Running Application - Kubernetes pulls the new Docker image, schedules pods, and exposes the app.

Users are now able to access the updated running application.

Developer -> GitLab Repo -> GitLab CI/CD -> Docker Registry -> ArgoCD -> Kubernetes Cluster -> Running Application


![Image](https://github.com/user-attachments/assets/9770b8b4-2f78-4aea-9176-2664aaa2c066).
