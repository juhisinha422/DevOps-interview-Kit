# 🔧 Git & GitHub Production Issue-Based Interview Q&A (4+ Years Experience)

---

## A developer accidentally pushed sensitive data (like passwords) to a repository on GitHub — how do you remove it from history?

* Revoke/rotate exposed secrets immediately
* Remove from history using:

```bash
git filter-repo --path <file> --invert-paths
```

* Force push cleaned repo
* Invalidate cached copies (GitHub cache)

---

## A commit was pushed to the wrong branch — how do you fix it safely?

* Create new branch from correct base
* Cherry-pick commit:

```bash
git cherry-pick <commit-id>
```

* Revert from wrong branch:

```bash
git revert <commit-id>
```

---

## A production deployment failed due to incorrect merge — how do you rollback changes?

* Revert merge commit:

```bash
git revert -m 1 <merge-commit>
```

* Redeploy previous stable version

---

## Multiple developers are facing merge conflicts frequently — how do you manage and reduce conflicts?

* Use small, frequent commits
* Rebase regularly
* Follow branching strategy (GitFlow)
* Improve communication

---

## A branch was accidentally deleted — how do you recover it?

* Use reflog:

```bash
git reflog
git checkout -b <branch> <commit-id>
```

---

## Someone force-pushed to the main branch — how do you restore the previous state?

* Use reflog or remote history
* Reset to previous commit:

```bash
git reset --hard <commit-id>
git push --force
```

---

## Repository size has become very large — how do you reduce repository size?

* Remove large files using `git filter-repo`
* Clean history
* Use Git LFS

---

## A developer committed large binary files — how do you handle large files in Git?

* Remove from history
* Use Git LFS for binaries
* Add `.gitignore`

---

## Changes are not reflecting after pulling latest code — what could be wrong?

* Wrong branch
* Local changes not committed
* Need reset:

```bash
git fetch
git reset --hard origin/main
```

---

## A developer cloned the repo but cannot push changes — how do you troubleshoot permission issues?

* Check access rights in GitHub
* Verify SSH/HTTPS credentials
* Check branch protection rules

---

## Pull request checks are failing — how do you debug failed checks?

* Review CI logs
* Fix test/build failures
* Re-run workflow

---

## Code review approvals are required but PR cannot be merged — what could be wrong?

* Missing approvals
* Failing checks
* Branch protection rules

---

## Branch protection rules are blocking deployment — how do you troubleshoot?

* Review rules:

  * Required checks
  * Required reviews
* Temporarily adjust (if needed)

---

## A merge introduced bugs into production — how do you revert safely?

* Revert commit (not reset)

```bash
git revert <commit-id>
```

* Redeploy stable version

---

## Git history became messy due to multiple merges — how do you clean it?

* Use interactive rebase:

```bash
git rebase -i HEAD~n
```

* Squash commits

---

## Developers are working on outdated branches — how do you synchronize branches?

```bash
git fetch
git rebase origin/main
```

---

## A hotfix needs to be applied urgently to production — how do you handle it using branching?

* Create hotfix branch from main
* Apply fix
* Merge to main & release branches

---

## A feature branch has many commits — how do you squash commits before merging?

```bash
git rebase -i HEAD~n
```

---

## A workflow in GitHub Actions is failing — how do you debug it?

* Check logs in Actions tab
* Verify YAML syntax
* Validate environment variables

---

## CI/CD pipeline is not triggering on push — what could be the issue?

* Check trigger config in YAML
* Verify branch filters
* Check webhook

---

## GitHub Actions cannot access secrets — how do you troubleshoot?

* Verify secrets are defined
* Check environment scope
* Ensure correct syntax

---

## Workflow runs successfully but deployment fails — how do you debug it?

* Check deployment logs
* Verify credentials & configs
* Validate target environment

---

## Pipeline works locally but fails in GitHub Actions — what could be the reason?

* Environment mismatch
* Missing dependencies
* OS differences

---

## GitHub runner is not picking jobs — how do you troubleshoot?

* Check runner status
* Verify labels
* Restart runner

---

## Self-hosted runner is offline — how do you fix it?

* Restart service
* Check network connectivity
* Re-register runner

---

## A user cannot access the repository — how do you troubleshoot access control?

* Check repo permissions
* Verify org/team access
* Confirm authentication

---

## Unauthorized changes were pushed to repository — how do you investigate?

* Check commit history
* Audit logs in GitHub
* Identify user & revert changes

---

## API token used in GitHub expired — how do you rotate tokens securely?

* Generate new token
* Update secrets
* Revoke old token

---

## Repository secrets were exposed — what steps will you take immediately?

* Revoke secrets immediately
* Rotate credentials
* Audit logs
* Update pipeline

---

## How do you audit repository changes to identify who modified critical files?

* Use `git log`:

```bash
git log <file>
```

* Use GitHub audit logs
* Track commit authors

---

## 🎯 Summary

Covers:

* Git troubleshooting
* GitHub Actions & CI/CD
* Security incidents
* Real production scenarios

👉 Perfect for **DevOps interviews (4+ years experience)**

---
