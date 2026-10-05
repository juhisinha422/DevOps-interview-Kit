# 📘 Kubernetes Quick Scenario Answers (4+ Years Experience)

---

### What happens if an initContainer fails?

Pod won’t start. Kubernetes keeps retrying the initContainer until it succeeds.
If `restartPolicy: Never` is set, the Pod will fail permanently.

---

### Delete a Pod from StatefulSet — what changes?

Nothing major. The Pod is recreated automatically with:

* Same name
* Same identity
* Same persistent storage

---

### Does DaemonSet run on master nodes?

Not by default.
Master nodes have taints, so you need to add **tolerations** to run DaemonSet pods on them.

---

### Rolling update + new image pushed mid-deploy?

Kubernetes updates the Deployment spec and continues rollout with the new image.
It does not restart the entire deployment from scratch.

---

### Pod stuck in Pending?

Check the following:

* Insufficient CPU/Memory
* Node selectors / affinity rules
* Taints and tolerations
* PVC not bound
* No available nodes

---

✅ Short, crisp answers — perfect for **rapid interview revision 🚀**
