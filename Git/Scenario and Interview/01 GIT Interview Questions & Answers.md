# 🚀 GIT Interview Questions & Answers (3–4 Years Experience)

---

# LEVEL 1 (Basic) - 3 Questions

---

## 1. Difference between git merge and git rebase? When to use each?

**Answer:**

**git merge:**

* Combines branches with a merge commit
* Keeps history intact

**git rebase:**

* Rewrites commit history
* Creates linear history

**When to use:**

* Use **merge** → for shared/public branches (safe)
* Use **rebase** → for cleaning local commits before PR

---

## 2. How do you resolve merge conflicts?

**Answer:**

Steps:

1. Pull latest changes
2. Identify conflict markers:

   ```bash
   <<<<<<< HEAD
   =======
   >>>>>>> branch
   ```
3. Manually fix code
4. Add changes:

   ```bash id="g1"
   git add .
   ```
5. Commit:

   ```bash id="g2"
   git commit
   ```

---

## 3. What is the difference between git reset and git revert?

**Answer:**

**git reset:**

* Removes commits from history
* Used for local changes

**git revert:**

* Creates a new commit to undo changes
* Safe for shared branches

---

# LEVEL 2 (Intermediate) - 2 Questions

---

## 1. Explain GitFlow vs trunk-based development. Which one for microservices?

**Answer:**

**GitFlow:**

* Feature → develop → release → main
* Structured but slower

**Trunk-based:**

* Short-lived branches
* Frequent commits to main

**For Microservices:**
👉 Prefer **trunk-based**
Reason:

* Faster releases
* Better CI/CD support

---

## 2. How do you squash multiple commits into one? Why would you do this?

**Answer:**

Command:

```bash id="g3"
git rebase -i HEAD~5
```

* Select `squash` for commits

**Why:**

* Clean commit history
* Better readability
* Easier code review

---

# LEVEL 3 (Advanced) - 5 Questions

---

## 1. Developer force-pushed to main and wiped last 10 commits. How to recover?

**Answer:**

Use reflog:

```bash id="g4"
git reflog
git checkout <commit-id>
git checkout -b recovery-branch
```

Then push back to main

---

## 2. File with passwords committed 2 months ago. How to remove from entire history?

**Answer:**

Use:

```bash id="g5"
git filter-repo --path secrets.txt --invert-paths
```

Or:

```bash id="g6"
git filter-branch
```

Then force push:

```bash id="g7"
git push --force
```

---

## 3. Merged feature branch but need to undo after 5 more commits. How?

**Answer:**

Use revert:

```bash id="g8"
git revert -m 1 <merge-commit-id>
```

✔ Safe method (does not break history)

---

## 4. Production hotfix needed but main has unfinished features. How to handle?

**Answer:**

Steps:

* Create hotfix branch from stable release:

```bash id="g9"
git checkout -b hotfix main
```

* Fix issue
* Merge to main and release branch

---

## 5. CI passes on PR but fails after merge to main. Why and how to prevent?

**Answer:**

**Why:**

* Environment mismatch
* Dependency conflicts
* Parallel changes merged

**Prevention:**

* Rebase before merge
* Run pipeline on main branch
* Use branch protection rules
* Use required checks before merge

---

## 🔥 Final Tips (3–4 Years Experience)

* Focus on **real scenarios**
* Use **safe commands (revert > reset for shared code)**
* Explain **impact + solution**

---

✅ Ready for DevOps & Git Interviews
