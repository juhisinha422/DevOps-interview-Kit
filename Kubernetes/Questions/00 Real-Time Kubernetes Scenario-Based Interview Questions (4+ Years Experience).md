# Real-Time Kubernetes Scenario-Based Interview Questions (4+ Years Experience)

---

## 1️⃣ A production pod is in CrashLoopBackOff. How would you troubleshoot and identify the root cause?

When a pod is in CrashLoopBackOff, I first check the pod status and events using `kubectl get pods` and `kubectl describe pod` to understand whether the failure is due to OOMKilled, probe failure, or configuration issues. Then I check container logs using `kubectl logs` and especially `--previous` to see the logs from the last crashed instance. Most commonly, this happens due to incorrect environment variables, wrong ConfigMap/Secret values, failed database connections, or liveness probe misconfiguration. If resource limits are too low, the container may be killed due to memory exhaustion. Once the root cause is identified, I fix the configuration or resource settings and redeploy using a rolling update to avoid downtime. I also ensure proper monitoring is in place to prevent recurrence.

---

## 2️⃣ A node shows NotReady status during peak traffic. What steps would you take to diagnose and recover it?

If a node becomes NotReady during peak traffic, I first check its condition using `kubectl describe node` to identify issues like MemoryPressure, DiskPressure, PIDPressure, or NetworkUnavailable. Then I SSH into the node to check CPU, memory, and disk usage using tools like `top`, `free -m`, and `df -h`. I verify the kubelet and container runtime status using systemctl logs. Often, high resource consumption or kubelet crashes cause this state. To prevent impact, I safely drain the node using `kubectl drain` so workloads are rescheduled to healthy nodes. After fixing the issue (resource cleanup, service restart, or instance replacement in cloud), I uncordon the node and bring it back into service. Preventive measures include autoscaling and resource monitoring.

---

## 3️⃣ After a new deployment, users report errors. How do you perform a safe rollback with zero downtime?

If users report errors after deployment, I immediately check rollout status and revision history using `kubectl rollout status` and `kubectl rollout history`. If the issue is confirmed, I perform a rollback using `kubectl rollout undo` to revert to the last stable version. Since Kubernetes uses a RollingUpdate strategy by default, downtime is avoided as long as readiness probes are configured properly and replica count is greater than one. I ensure `maxUnavailable` is set to 0 and `maxSurge` is configured appropriately to maintain availability. After rollback, I analyze logs and metrics to identify what failed in the new release and fix it before redeploying.

---

## 4️⃣ Application is not accessible via Service (LoadBalancer/NodePort). How do you debug networking issues?

If an application is not accessible via Service, I start by verifying the Service object using `kubectl get svc` and `kubectl describe svc`. Then I check endpoints using `kubectl get endpoints` to ensure pods are correctly mapped. If endpoints are empty, it usually indicates a label selector mismatch between Service and Pods. I validate pod labels and ensure the targetPort matches the containerPort defined in the pod spec. I also verify the application is actually listening on the expected port. If using LoadBalancer, I check cloud provider security groups and firewall rules. Additionally, I review NetworkPolicies that might block traffic. Testing connectivity internally using `kubectl exec` and curl helps isolate whether the issue is application-level or networking-level.

---

## 5️⃣ Pods are stuck in ImagePullBackOff from a private registry. What checks will you perform?

When pods are stuck in ImagePullBackOff, I describe the pod to see the exact error message. Common issues include incorrect image name or tag, wrong registry URL, authentication failure, or expired credentials. I verify whether imagePullSecrets are configured properly in the deployment and check if the secret exists in the namespace. If missing, I create a docker-registry secret and attach it to the deployment. I also confirm network connectivity to the registry and ensure there are no firewall restrictions. After correcting the issue, I restart the deployment and verify the image pulls successfully.

---

## 6️⃣ ConfigMap is updated but changes are not reflected in running pods. Why and how to fix it?

ConfigMap updates do not automatically restart pods. If the ConfigMap is used as environment variables, the pod must be restarted to reflect changes. If mounted as a volume, Kubernetes updates the file automatically, but the application may need a reload mechanism to pick up the new configuration. To fix this, I perform a rollout restart of the deployment. In production setups, I use a checksum annotation strategy in the deployment YAML so that any change in ConfigMap automatically triggers a rolling update. This ensures configuration consistency without manual intervention.

---

## 7️⃣ HPA is configured but pods are not scaling under load. What could be the issue?

If HPA is not scaling, I first check the HPA object using `kubectl describe hpa` to see current metrics and scaling conditions. A common issue is the metrics server not being installed or functioning properly. I verify using `kubectl top pods`. Another common mistake is not defining CPU or memory requests in the deployment, since HPA calculates scaling based on resource requests. Sometimes the load is not reaching the defined threshold, or custom metrics are misconfigured. After fixing metrics or configuration issues, I test again under load to confirm scaling behavior.

---

## 8️⃣ Persistent Volume is not mounting in a stateful application. How do you troubleshoot PVC/PV?

If a Persistent Volume is not mounting, I start by checking PVC status using `kubectl get pvc` and `kubectl describe pvc` to confirm whether it is Bound. If not bound, I verify StorageClass, access modes, and capacity requirements. I also check the corresponding PV to ensure it matches the claim. Sometimes the issue is due to access mode mismatch (RWO vs RWX) or insufficient storage. If using cloud storage like AWS EBS or Azure Disk, I verify whether the disk is attached to the correct node and IAM permissions are properly configured. Reviewing pod events helps identify mount failures or permission issues. Once resolved, the pod should mount the volume successfully.

---

