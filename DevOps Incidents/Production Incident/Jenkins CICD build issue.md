## explanation of the issue and resolution for your interview:

---

## Problem
Jenkins CI/CD pipeline was failing at the **Docker Image Push** stage with two errors:
1. `UnrecognizedClientException: The security token included in the request is invalid`
2. `Cannot perform an interactive login from a non TTY device`

---

## Root Cause
The AWS Access Key `AKIAUB5VAYHFZHQLSUDX` configured in `/root/.aws/credentials` on the Jenkins EC2 server was **Inactive** in IAM. Since `aws ecr get-login-password` returned an empty/invalid token, `docker login` failed with the TTY error as a downstream effect.

---

## Troubleshooting Steps
1. Checked Jenkins pipeline logs — identified AWS and TTY errors
2. Ran `aws sts get-caller-identity` — confirmed Jenkins was using an IAM user, not an EC2 role
3. Checked `~/.aws/credentials` — credentials were present
4. Tested ECR login manually as root — succeeded
5. Tested as jenkins user — docker permission denied
6. Added jenkins to docker group — fixed docker permission
7. Ran `ps aux` — found Jenkins running as **root**, not jenkins user
8. Checked IAM console — found the access key was **Inactive**

---

## Fix
Activated the AWS Access Key in **IAM → Users → Security Credentials → Activate**

---

## Key Learnings
- **TTY error is usually a symptom**, not the root cause — always check what's upstream
- Always verify AWS key status in IAM console first
- Jenkins running as root uses `/root/.aws/credentials`
- Jenkins running as jenkins user uses `/var/lib/jenkins/.aws/credentials`
- Always check `aws sts get-caller-identity` to debug AWS credential issues quickly
