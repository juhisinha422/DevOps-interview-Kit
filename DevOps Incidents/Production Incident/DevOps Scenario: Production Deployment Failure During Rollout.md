# DevOps Scenario: Production Deployment Failure During Rollout

Scenario:
Production deployment fails mid-rollout. New pods are crashing, old pods are terminated, and application is down.

Approach (4+ Years DevOps Engineer):

1. Stop the rollout immediately to prevent further damage:
kubectl rollout pause deployment <app>

2. Restore service (top priority) by rolling back:
kubectl rollout undo deployment <app>

If rollback fails, redeploy last stable image or trigger previous pipeline.
Goal is to bring application back online ASAP.

3. Debug failing pods:
kubectl get pods
kubectl describe pod <pod>
kubectl logs <pod>

Check for:
- CrashLoopBackOff
- ImagePullBackOff
- Config/Secret issues
- Probe failures

4. Check cluster events:
kubectl get events --sort-by=.metadata.creationTimestamp

5. Verify traffic routing:
kubectl get svc
kubectl get endpoints

Ensure traffic is going to healthy pods or switch traffic back.

6. After service is stable, do root cause analysis:
- Code changes
- Image issues
- ConfigMap/Secret changes
- Resource limits (OOMKilled)
- Dependency failures

7. Validate CI/CD changes:
- Image tags
- Deployment configs
- Infra changes

Key Principle:
Stabilization > Debugging

One-line answer:
Stop rollout, rollback to stable version to restore service, then debug using logs, events, and recent changes.
