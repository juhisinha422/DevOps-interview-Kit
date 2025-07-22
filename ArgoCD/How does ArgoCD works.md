How does Argo CD work?

Argo CD uses git repositories as the source of truth for the desired state of applications and the target deployment environments. Kubernetes manifests can be specified as YAML files, ksonnet/jsonnet applications or Helm packages. Argo CD automates the synchronization of the desired application state with each of the specified target environments. Here is a high-level architectural view-


Argo CD is implemented as a Kubernetes CRD which continuously monitors running applications and compares the current, live state against the desired target state (as specified in the git repo). A deployed application whose live state deviates from its target state is considered out-of-sync. Argo CD reports & visualizes any deviation as well as provides mechanisms to automatically or manually sync the live state to the desired target state. Any modifications made to the desired target state in the git repo can be automatically applied and reflected in the specified target environments.


![Image](https://github.com/user-attachments/assets/ae912836-e99c-4340-b680-eca5909a81f6)
