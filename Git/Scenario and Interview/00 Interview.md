# Git Interview Handbook

# Most Frequently Asked Git Interview Scenarios
### (DevOps | SRE | Cloud Engineer)

---

# 1. You committed code to the wrong branch. How will you move that commit to the correct branch?

## Answer

If I committed to the wrong branch and the commit has **not been pushed** to the remote repository, I would use **git cherry-pick** to move the commit to the correct branch. First, I identify the commit hash using `git log --oneline`, then switch to the correct branch and cherry-pick the commit. Finally, I return to the wrong branch and remove the commit using `git reset`.

If the commit has already been pushed and other team members may have pulled it, I avoid rewriting history. Instead, I discuss with the team before using force push or choose a safer option like reverting the commit.

## Commands

```bash
git log --oneline
```

Find the commit hash.

```bash
git checkout feature-branch
```

Switch to the correct branch.

```bash
git cherry-pick <commit_hash>
```

Copy the commit to the correct branch.

```bash
git checkout wrong-branch
git reset --hard HEAD~1
```

Remove the commit from the wrong branch.

If already pushed:

```bash
git push --force
```

Use only when you are certain no one else is using that branch.

## Example

Current Branch:

```
main
```

Accidentally committed:

```
Added Jenkins Pipeline
```

Move it:

```
main
↓
Cherry Pick
↓
feature/jenkins
```

Then remove it from `main`.

## Why Interviewers Ask This

To verify whether you understand branch management and know how to safely move commits without affecting teammates.

## Best Practice

Avoid force pushing to shared branches. Prefer cherry-pick for moving commits between branches.

---

# 2. You pushed code, but now you need to undo the last commit that has already been pushed to remote. How will you do it?

## Answer

When the commit has already been pushed to a shared repository, the safest approach is to use **git revert** instead of **git reset**. Revert creates a new commit that undoes the previous changes while preserving Git history.

If no one else has pulled the changes, I can use `git reset --hard` followed by a force push, but this should only be done with team approval because it rewrites history.

## Safe Method

```bash
git revert HEAD
```

Push the new commit.

```bash
git push origin main
```

## Unsafe Method (History Rewrite)

```bash
git reset --hard HEAD~1
git push --force
```

## Difference

| git revert | git reset |
|------------|-----------|
| Safe | Rewrites history |
| Creates new commit | Deletes commit |
| Recommended for shared branches | Suitable for local branches |

## Production Scenario

Suppose a deployment introduced a bug into production. Instead of deleting history, use `git revert` so everyone has a consistent commit history.

## Interview Tip

Mention that `git revert` is always preferred on shared repositories.

---

# 3. Your local branch is behind the remote branch. How will you bring your branch up to date?

## Answer

First, I fetch the latest changes from the remote repository. Then I either merge or rebase depending on the team's Git strategy.

Merge preserves history, while rebase creates a cleaner, linear history.

## Commands

Fetch latest changes.

```bash
git fetch origin
```

Merge latest changes.

```bash
git merge origin/main
```

OR

Rebase latest changes.

```bash
git rebase origin/main
```

Using pull:

```bash
git pull origin main
```

## Difference

### Merge

```
A---B---C
         \
          D
```

History remains intact.

### Rebase

```
A---B---C---D
```

Linear history.

## Production Scenario

Before raising a Pull Request, always synchronize your feature branch with the latest main branch to reduce merge conflicts.

## Best Practice

Always use `git fetch` before merging so you know what changes are coming.

---

# 4. You and a teammate have conflicting changes in the same file. How will you resolve this merge conflict?

## Answer

Merge conflicts occur when two developers modify the same lines of a file. Git cannot automatically decide which change should be kept.

To resolve the conflict, I first pull the latest changes, identify the conflicting files, manually edit the conflict markers, test the application, and commit the resolved version.

## Commands

```bash
git pull origin main
```

Git displays conflicting files.

Check status.

```bash
git status
```

Edit the file.

Conflict markers:

```text
<<<<<<< HEAD
My Changes
=======
Teammate Changes
>>>>>>> main
```

Remove markers and keep the correct code.

Stage the resolved file.

```bash
git add .
```

Commit.

```bash
git commit
```

## Production Scenario

After resolving conflicts, always rebuild and test the application before pushing changes.

## Interview Tip

Never blindly accept incoming changes. Review both versions carefully.

---

# 5. You accidentally deleted a branch. How can you recover it?

## Answer

If the branch was deleted accidentally, Git usually still keeps the commit history in the reflog.

First, locate the lost commit using `git reflog`, then recreate the branch pointing to that commit.

## Commands

View reflog.

```bash
git reflog
```

Example

```
abc123 HEAD@{2}: commit: Added Kubernetes deployment
```

Recreate branch.

```bash
git checkout -b feature-branch abc123
```

If deleted remotely.

```bash
git push origin feature-branch
```

## Production Scenario

Developers often recover accidentally deleted branches using Git reflog because Git retains references for some time.

## Best Practice

Avoid deleting branches until they have been merged and verified.

---

# End of Part 1
### Covers Questions 1–5
