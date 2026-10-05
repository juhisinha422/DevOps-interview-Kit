# Git & GitHub Interview Questions and Answers (4 Years DevOps Experience)

## Q1. What is the difference between git fork and git clone, and when would you use each?

A **git clone** creates a local copy of an existing repository on your machine. It is mainly used when you have direct access to the repository and want to work on the code.

A **git fork** creates a copy of someone else's repository under your own GitHub account. It is commonly used in open-source projects where contributors do not have direct write access to the original repository.

I use **clone** for internal company repositories and **fork** when contributing to external or open-source projects.

---

## Q2. Explain a scenario where you used git fork instead of git clone. Why was forking necessary?

While contributing to an open-source Kubernetes Helm chart, I did not have write access to the original repository. I first forked the repository to my GitHub account, cloned my fork locally, made the required changes, pushed them to my fork, and then created a Pull Request to the original repository. Forking was necessary because contributors cannot directly push code to repositories they do not own.

---

## Q3. What is the difference between git fetch and git pull, and when would you use each?

**git fetch** downloads the latest changes from the remote repository but does not merge them into the current branch.

```bash
git fetch origin
```

**git pull** performs both fetch and merge automatically.

```bash
git pull origin main
```

I use **git fetch** when I want to review incoming changes before merging. I use **git pull** when I am confident that remote changes can be merged directly.

---

## Q4. What is the difference between git rebase and git merge? When would you use each?

**Git Merge**

* Preserves complete commit history
* Creates a merge commit
* Easier and safer for shared branches

```bash
git merge feature-branch
```

**Git Rebase**

* Rewrites commit history
* Creates a cleaner linear history
* Avoids unnecessary merge commits

```bash
git rebase main
```

I use **merge** for shared branches such as release and main. I use **rebase** on my local feature branches before creating Pull Requests to keep history clean.

---

## Q5. Explain the Git branching strategy you used in your company. Align it with the open-source branching strategy followed by Kubernetes.

In my organization we followed a GitFlow-style branching strategy.

### Branch Structure

```text
main
│
├── develop
│
├── feature/user-auth
├── feature/payment-api
│
├── release/v2.0
│
└── hotfix/login-issue
```

### Workflow

1. Developers create feature branches from develop.
2. Code is reviewed through Pull Requests.
3. Features are merged into develop.
4. Release branches are created for testing.
5. Release branch is merged into main after approval.
6. Production fixes are handled through hotfix branches.

Kubernetes follows a similar model using:

* master/main branch
* release branches
* feature development through Pull Requests
* code review and approval process

The overall concept is controlled branching, peer review, and stable release management.

---

## Q6. Explain three challenges you faced while using Git in your work experience.

### Challenge 1: Merge Conflicts

Multiple developers modified the same Kubernetes deployment YAML files.

Solution:
I fetched the latest code frequently, rebased feature branches, and resolved conflicts before raising Pull Requests.

### Challenge 2: Large Binary Files

Some teams accidentally committed build artifacts and logs.

Solution:
Configured .gitignore and removed unwanted files from repository history.

### Challenge 3: Accidental Push to Wrong Branch

A developer pushed changes directly to the release branch.

Solution:
Implemented branch protection rules requiring Pull Requests and approvals before merging.

---

## Q7. Explain a recent challenge you faced with Git and how you addressed it.

Recently, a developer force-pushed changes to a shared branch, causing multiple commits to disappear.

To resolve the issue:

1. Used git reflog to identify lost commits.
2. Created a recovery branch.
3. Restored the missing commits.
4. Raised a Pull Request for verification.
5. Enabled branch protection to prevent force pushes.

Commands used:

```bash
git reflog
git checkout -b recovery-branch
git cherry-pick <commit-id>
```

---

## Q8. How do you handle merge conflicts in Git?

Steps:

1. Pull latest changes.

```bash
git pull origin main
```

2. Git highlights conflicting files.

```bash
git status
```

3. Open conflicting files.

Example:

```text
<<<<<<< HEAD
new code
=======
old code
>>>>>>> main
```

4. Manually resolve conflicts.
5. Stage resolved files.

```bash
git add .
```

6. Complete merge.

```bash
git commit
```

7. Push changes.

```bash
git push
```

---

## Q9. What are Git tags and how do you use them?

Git tags are used to mark important points in repository history, typically releases.

Example:

```bash
git tag v1.0.0
git push origin v1.0.0
```

Benefits:

* Easy rollback
* Release tracking
* CI/CD deployment triggers

In production deployments we often trigger releases using version tags such as:

```text
v1.0.0
v1.1.0
v2.0.0
```

---

## Q10. How do you combine multiple commits into a single commit in Git?

Using interactive rebase:

```bash
git rebase -i HEAD~5
```

Replace:

```text
pick
pick
pick
pick
pick
```

With:

```text
pick
squash
squash
squash
squash
```

Save and provide a final commit message.

This creates one clean commit from multiple small commits.

---

## Q11. I want to ignore pushing changes to a specific file in Git. How can I do it?

If the file is not tracked:

Add it to .gitignore.

```bash
echo "config.properties" >> .gitignore
```

If the file is already tracked:

```bash
git update-index --skip-worktree config.properties
```

To track again:

```bash
git update-index --no-skip-worktree config.properties
```

This is useful for local configuration files that differ between environments.

---

## Q12. What is the purpose of the .git folder in a Git repository?

The .git directory is the heart of Git.

It stores:

* Commit history
* Branch information
* Tags
* Configuration
* Staging area metadata
* References
* Object database

Without the .git folder, Git cannot track changes or maintain repository history.

Example:

```bash
ls -la .git
```

Contents:

```text
objects/
refs/
logs/
config
HEAD
index
```

---

## Q13. A teammate accidentally committed a Kubernetes Secret to Git. What should you do?

This is a security incident and should be handled immediately.

### Step 1: Revoke the exposed secret

* Rotate passwords
* Regenerate API keys
* Update Kubernetes Secret

### Step 2: Remove secret from repository

If not pushed:

```bash
git reset HEAD~1
```

If already pushed:

```bash
git filter-branch
```

or

```bash
git filter-repo
```

to remove the secret from history.

### Step 3: Force push cleaned history

```bash
git push --force
```

### Step 4: Verify repository history

Ensure the secret is no longer accessible.

### Step 5: Prevent future incidents

* Add secret scanning tools (Trivy, Gitleaks)
* Use Kubernetes Secrets
* Use AWS Secrets Manager
* Use HashiCorp Vault
* Implement pre-commit hooks

In production, the first priority is always rotating the exposed credentials because removing the commit alone does not eliminate the risk.
