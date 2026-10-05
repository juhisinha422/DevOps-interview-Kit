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

---

# 6. You committed sensitive information (like passwords/API keys) to Git. What will you do?

## Answer

Committing sensitive information such as passwords, API keys, AWS access keys, database credentials, or certificates is a serious security issue. The first step is to determine whether the commit has been pushed to the remote repository.

If the commit has **not been pushed**, I remove it from Git history using `git reset` or an interactive rebase.

If it has **already been pushed**, simply deleting the file is not enough because Git history still contains the secret. I immediately revoke or rotate the compromised credential, remove it from the repository history using a history-rewriting tool such as `git filter-repo` (or BFG Repo-Cleaner), and force push only after coordinating with the team.

Finally, I add the file to `.gitignore` and use secret scanning tools such as GitHub Secret Scanning or GitLeaks to prevent future incidents.

## Commands

View commit history:

```bash
git log --oneline
```

Undo last commit (not pushed):

```bash
git reset --soft HEAD~1
```

Remove tracked file:

```bash
git rm --cached secrets.txt
```

Add to .gitignore

```
.env
*.pem
*.key
credentials.json
```

Commit again:

```bash
git add .
git commit -m "Remove secrets"
```

If already pushed:

- Rotate credentials immediately.
- Clean Git history.
- Force push after team approval.

## Production Scenario

An engineer accidentally commits AWS Access Keys. Even if the commit is deleted later, anyone who cloned the repository may still have the key. Therefore, the AWS keys must be disabled immediately before cleaning Git history.

## Interview Tip

Always mention **credential rotation**. Removing the file alone is never enough.

---

# 7. Explain the difference between git fetch and git pull.

## Answer

Both commands retrieve updates from a remote repository, but they behave differently.

### git fetch

Downloads the latest commits from the remote repository without changing your current working branch.

```bash
git fetch origin
```

Your code remains unchanged.

### git pull

Downloads changes and immediately merges (or rebases) them into the current branch.

```bash
git pull origin main
```

Equivalent to:

```bash
git fetch
git merge
```

## Comparison

| git fetch | git pull |
|------------|-----------|
| Downloads changes only | Downloads + merges |
| Safe | May create merge conflicts |
| Lets you review changes | Updates working tree immediately |
| Preferred before review | Used for quick synchronization |

## Production Scenario

Before starting work every morning, I run:

```bash
git fetch
git log HEAD..origin/main
```

This allows me to review incoming commits before merging them.

## Best Practice

Prefer **git fetch** before pull in production environments.

---

# 8. Explain the difference between Merge and Rebase.

## Answer

Both Merge and Rebase combine changes from different branches.

Merge preserves branch history.

Rebase rewrites commit history to create a cleaner, linear timeline.

### Merge

```bash
git merge feature
```

History

```
A---B---C------M
     \        /
      D------E
```

Creates a Merge Commit.

### Rebase

```bash
git rebase main
```

History

```
A---B---C---D'---E'
```

No merge commit.

## Comparison

| Merge | Rebase |
|---------|---------|
| Preserves history | Rewrites history |
| Creates merge commit | No merge commit |
| Safe for shared branches | Better for local feature branches |
| Easier to understand | Cleaner history |

## Production Scenario

Many companies allow developers to rebase feature branches before opening a Pull Request to maintain a clean commit history.

## Interview Tip

Never rebase commits that have already been shared with other developers.

---

# 9. What is Git Stash? When will you use it?

## Answer

Git Stash temporarily saves uncommitted changes without creating a commit. It is useful when you need to switch branches or pull urgent fixes but your current work is incomplete.

## Commands

Save changes:

```bash
git stash
```

View stashes:

```bash
git stash list
```

Restore latest stash:

```bash
git stash pop
```

Restore without deleting:

```bash
git stash apply
```

Delete stash:

```bash
git stash drop
```

Clear all:

```bash
git stash clear
```

## Production Scenario

Suppose you're developing a new feature and suddenly receive a production issue. Instead of making an incomplete commit, stash your work, switch to the production branch, fix the issue, and later restore your changes.

## Best Practice

Use stash only for temporary work. Long-term work should be committed to a feature branch.

---

# 10. One of your commits introduced a production issue. How would you identify which commit caused it?

## Answer

The fastest way is to use **Git Bisect**.

Git Bisect performs a binary search through commit history, allowing you to quickly identify the commit that introduced the bug.

## Commands

Start bisect:

```bash
git bisect start
```

Mark current version as bad.

```bash
git bisect bad
```

Mark older stable commit.

```bash
git bisect good <commit>
```

Git checks out the middle commit.

Run tests.

If good:

```bash
git bisect good
```

If bad:

```bash
git bisect bad
```

Continue until Git identifies the faulty commit.

Finish.

```bash
git bisect reset
```

## Why Git Bisect?

Instead of manually checking 100 commits, Git performs binary search.

100 commits

↓

50

↓

25

↓

12

↓

6

↓

3

↓

1

Very efficient.

## Production Scenario

A deployment succeeds but users start reporting login failures. Git Bisect identifies the exact commit that introduced the regression, allowing quick rollback or fix.

---

# 11. How do you recover a deleted commit?

## Answer

If a commit is accidentally deleted due to a reset, rebase, or branch deletion, Git usually still keeps a reference to it in the **Reflog**. Git Reflog records every movement of the HEAD pointer, making it one of the most powerful recovery tools.

To recover the deleted commit, first inspect the reflog to locate the commit hash. Once identified, create a new branch pointing to that commit or reset the current branch to it.

If Git garbage collection has not yet removed the object, the commit can usually be restored successfully.

## Commands

View Reflog

```bash
git reflog
```

Example

```
abc1234 HEAD@{3}: commit: Added Jenkins Pipeline
```

Recover using checkout

```bash
git checkout -b recovered-branch abc1234
```

Recover using reset

```bash
git reset --hard abc1234
```

## Production Scenario

A developer accidentally runs:

```bash
git reset --hard HEAD~5
```

Five commits disappear.

Instead of panicking, use:

```bash
git reflog
```

Locate the previous HEAD and restore it.

## Interview Tip

Mention **Git Reflog** first. Most interviewers expect this answer.

---

# 12. What is Cherry-pick? When would you use it?

## Answer

Git Cherry-pick copies one or more specific commits from one branch and applies them to another branch without merging the entire branch.

It is useful when only a single bug fix or feature needs to be moved to another branch.

## Command

```bash
git cherry-pick <commit_hash>
```

Multiple commits

```bash
git cherry-pick commit1 commit2 commit3
```

Commit range

```bash
git cherry-pick commit1^..commit5
```

## Example

```
main

A --- B --- C

feature

A --- B --- D --- E
```

Need only commit D.

```
git checkout main
git cherry-pick D
```

Result

```
main

A --- B --- C --- D
```

## Production Scenario

A critical production bug is fixed in the development branch.

Instead of merging unfinished work, cherry-pick only the bug-fix commit into the production release branch.

## Advantages

- Copies selected commits
- No full merge required
- Useful for hotfixes
- Keeps unrelated changes out

## Interview Tip

Cherry-pick copies commits.

Merge combines branches.

---

# 13. Explain Git Reset (Soft, Mixed, Hard).

## Answer

Git Reset moves the current branch pointer to a previous commit.

There are three reset modes.

### Soft Reset

Moves HEAD only.

Changes remain staged.

```bash
git reset --soft HEAD~1
```

Use when you want to modify the previous commit.

---

### Mixed Reset (Default)

Moves HEAD.

Unstages changes.

Files remain in working directory.

```bash
git reset HEAD~1
```

---

### Hard Reset

Moves HEAD.

Deletes staged changes.

Deletes working directory changes.

```bash
git reset --hard HEAD~1
```

---

## Comparison

| Reset Type | Commit | Staging | Working Directory |
|------------|---------|----------|-------------------|
| Soft | Removed | Preserved | Preserved |
| Mixed | Removed | Cleared | Preserved |
| Hard | Removed | Cleared | Deleted |

## Production Scenario

Suppose you committed incorrect code but haven't pushed it.

Use

```bash
git reset --soft HEAD~1
```

Edit the code and recommit.

## Warning

Never use

```bash
git reset --hard
```

without verifying that no important local changes exist.

---

# 14. Explain Git Revert.

## Answer

Git Revert safely undoes a commit by creating another commit that reverses the previous changes.

Unlike Git Reset, it does not rewrite Git history, making it the preferred approach for shared repositories.

## Command

Revert latest commit

```bash
git revert HEAD
```

Revert specific commit

```bash
git revert <commit_hash>
```

Push changes

```bash
git push origin main
```

## Difference between Reset and Revert

| Reset | Revert |
|---------|---------|
| Deletes history | Preserves history |
| Local operation | Creates new commit |
| Unsafe on shared branches | Safe on shared branches |

## Production Scenario

A deployment introduces a bug.

Instead of deleting history, revert the deployment commit.

This keeps the audit trail intact.

## Interview Tip

Whenever asked about undoing changes in production, answer **Git Revert**.

---

# 15. Explain Git Tags.

## Answer

Git Tags are used to mark important points in repository history, such as software releases.

Tags are commonly used to identify production versions like:

```
v1.0
v2.0
v3.1
```

There are two types of tags.

### Lightweight Tag

Acts like a bookmark.

```bash
git tag v1.0
```

### Annotated Tag

Stores author information, date, and message.

```bash
git tag -a v1.0 -m "First Stable Release"
```

View tags

```bash
git tag
```

Push single tag

```bash
git push origin v1.0
```

Push all tags

```bash
git push origin --tags
```

Delete local tag

```bash
git tag -d v1.0
```

Delete remote tag

```bash
git push origin --delete v1.0
```

## Production Scenario

CI/CD pipelines often deploy applications based on release tags.

Example

```
v1.5.2
```

Deployment automatically starts after this tag is pushed.

## Best Practice

Use semantic versioning.

Example

```
v1.0.0

v1.1.0

v1.2.5

v2.0.0
```

## Interview Tip

Remember:

Branches move.

Tags are permanent references to specific commits.

---

---

# 16. Explain Git Rebase with an example.

## Answer

Git Rebase is used to move or replay the commits of one branch on top of another branch. Instead of creating a merge commit, it rewrites the commit history to produce a clean and linear history.

Unlike Merge, Rebase makes it appear as though all feature branch work started after the latest commit of the target branch.

## Syntax

```bash
git checkout feature-branch
git rebase main
```

## Before Rebase

```
main

A ----- B ----- C

              \
feature         D ----- E
```

## After Rebase

```
main

A ----- B ----- C ----- D' ----- E'
```

Notice that D and E receive new commit IDs because history is rewritten.

## Handling Conflicts

If conflicts occur during rebase:

```bash
git status
```

Resolve the conflicts manually.

Stage the changes:

```bash
git add .
```

Continue rebase:

```bash
git rebase --continue
```

Abort rebase if required:

```bash
git rebase --abort
```

## Advantages

- Cleaner Git history
- Easier code review
- No unnecessary merge commits
- Better visualization of project history

## Disadvantages

- Rewrites commit history
- Unsafe on shared branches
- Can confuse teammates if used incorrectly

## Production Scenario

A developer has been working on a feature branch for a week. Meanwhile, the `main` branch has received multiple updates. Before creating a Pull Request, the developer rebases the feature branch on top of `main` to minimize merge conflicts and maintain a clean history.

## Interview Tip

Rebase is recommended only for local or private branches. Never rebase commits that have already been pushed to a shared branch unless everyone agrees.

---

# 17. What is Detached HEAD in Git?

## Answer

A Detached HEAD state occurs when HEAD points directly to a specific commit instead of pointing to a branch.

Normally,

```
HEAD → main
```

In Detached HEAD,

```
HEAD → Commit
```

You can inspect old commits, test code, or debug issues in this state. However, if you create new commits without creating a branch, those commits may become unreachable later.

## Example

Checkout a commit directly:

```bash
git checkout 3d2f7ab
```

Git displays:

```
You are in 'detached HEAD' state.
```

Create a branch if you want to preserve your work:

```bash
git checkout -b bugfix
```

## Production Scenario

A developer checks out an old production release to investigate a bug. After identifying the issue, they create a new branch from that commit to implement the fix.

## Best Practice

Never continue long-term development in Detached HEAD mode. Always create a new branch before making commits.

---

# 18. What is Git Blame? When would you use it?

## Answer

Git Blame shows who last modified each line of a file, along with the commit ID and timestamp. It is a useful debugging tool for understanding the history of specific lines of code.

## Command

```bash
git blame filename
```

Example:

```bash
git blame Jenkinsfile
```

Output:

```
a1b2c3d (Juhi 2026-07-20) pipeline {
e4f5g6h (Aman 2026-07-21) stage('Build')
```

## Uses

- Find who introduced a change
- Understand why a line was modified
- Debug production issues
- Review code ownership

## Production Scenario

A Jenkins pipeline suddenly starts failing. Using `git blame Jenkinsfile`, the team identifies the developer and commit that introduced the problematic configuration, making it easier to investigate and fix.

## Interview Tip

Git Blame identifies **who changed a line**, not who originally created the file.

---

# 19. What is Git Squash? Why is it used?

## Answer

Git Squash combines multiple commits into a single commit. It helps keep the commit history clean and makes Pull Requests easier to review.

Instead of:

```
Fixed typo
Updated typo
Another typo
Formatting
Final changes
```

You can squash them into:

```
Added Login Feature
```

## Interactive Rebase

```bash
git rebase -i HEAD~5
```

Example:

```
pick a123 First commit
pick b234 Second commit
pick c345 Third commit
pick d456 Fourth commit
pick e567 Fifth commit
```

Change to:

```
pick a123 First commit
squash b234 Second commit
squash c345 Third commit
squash d456 Fourth commit
squash e567 Fifth commit
```

Save and edit the final commit message.

## Advantages

- Clean history
- Easier code review
- Smaller Pull Requests
- Better release notes

## Production Scenario

Before merging a feature branch, developers squash multiple "fix" and "update" commits into one meaningful commit to keep the main branch history readable.

## Best Practice

Write clear commit messages after squashing so the final commit accurately describes the feature or fix.

---

# 20. Explain Git Hooks.

## Answer

Git Hooks are scripts that run automatically before or after specific Git events. They help automate tasks and enforce project standards.

Hooks are stored in:

```
.git/hooks/
```

Common hooks include:

### pre-commit

Runs before a commit is created.

Typical uses:

- Run unit tests
- Check code formatting
- Run linting
- Scan for secrets

### commit-msg

Validates commit message format.

Example:

```
JIRA-123: Fix Kubernetes deployment
```

### pre-push

Runs before pushing code.

Typical uses:

- Execute test suites
- Security scanning
- Verify build success

### post-merge

Runs after a successful merge.

Typical uses:

- Install dependencies
- Generate configuration files
- Clear caches

## Example

A simple `pre-commit` hook:

```bash
#!/bin/sh
echo "Running tests..."
npm test

if [ $? -ne 0 ]; then
    echo "Tests failed. Commit aborted."
    exit 1
fi
```

Make it executable:

```bash
chmod +x .git/hooks/pre-commit
```

## Production Scenario

Many DevOps teams use pre-commit hooks to prevent committing secrets, enforce formatting, and ensure tests pass before code reaches the repository.

## Interview Tip

Hooks improve code quality by automating checks before code is committed or pushed. They complement CI/CD pipelines but do not replace them.

---
---

# 21. Explain Git Submodules.

## Answer

Git Submodules allow one Git repository to include another Git repository as a subdirectory. They are useful when a project depends on another project that is maintained separately.

Each submodule points to a specific commit of the external repository rather than tracking its latest branch automatically.

## Commands

Add a submodule

```bash
git submodule add https://github.com/company/common-library.git
```

Clone repository with submodules

```bash
git clone --recurse-submodules <repository_url>
```

Initialize submodules

```bash
git submodule init
```

Update submodules

```bash
git submodule update
```

Update to latest remote version

```bash
git submodule update --remote
```

## Advantages

- Reuse shared libraries
- Independent versioning
- Separate repository ownership
- Better modular architecture

## Disadvantages

- More complex workflow
- Developers must initialize submodules
- Easy to forget updating submodules

## Production Scenario

A company maintains a common authentication library used by multiple microservices. Instead of copying the code into every repository, each service includes it as a Git Submodule.

## Interview Tip

Submodules track a specific commit, not the latest branch.

---

# 22. What is Git LFS?

## Answer

Git LFS (Large File Storage) is an extension that stores large binary files outside the normal Git repository while keeping lightweight pointers inside Git.

Git is optimized for source code, not large files like videos, datasets, machine learning models, or high-resolution images.

## Install

```bash
git lfs install
```

Track files

```bash
git lfs track "*.zip"
git lfs track "*.psd"
git lfs track "*.mp4"
```

Commit

```bash
git add .gitattributes
git add .
git commit -m "Track files using Git LFS"
```

## Advantages

- Faster cloning
- Smaller repositories
- Better performance
- Efficient storage

## Production Scenario

A machine learning project contains 5 GB model files. Instead of storing them directly in Git, the team tracks them using Git LFS, keeping the repository fast and manageable.

## Interview Tip

Git LFS stores **pointers in Git** and **large files in external storage**.

---

# 23. What is .gitignore? Why is it important?

## Answer

The `.gitignore` file tells Git which files and directories should not be tracked. It prevents temporary files, build artifacts, secrets, and IDE-specific files from being committed.

## Example

```text
# Environment files
.env

# Logs
*.log

# Build output
target/
dist/
build/

# Node modules
node_modules/

# IDE files
.vscode/
.idea/

# OS files
.DS_Store
Thumbs.db

# Certificates
*.pem
*.key
```

## Important Note

If a file is already tracked, adding it to `.gitignore` will **not** stop Git from tracking it.

Remove it first:

```bash
git rm --cached filename
```

Then commit the change.

## Production Scenario

Sensitive files such as `.env` containing database passwords should always be added to `.gitignore` before the first commit.

## Best Practice

Maintain a project-specific `.gitignore` and review it regularly to ensure secrets and generated files are excluded.

---

# 24. How do you resolve merge conflicts during a Git Rebase?

## Answer

Conflicts during a rebase occur when Git cannot automatically apply your commits on top of the target branch.

When a conflict occurs, Git pauses the rebase and asks you to resolve it manually.

## Steps

Start rebase

```bash
git rebase main
```

If conflicts occur:

```bash
git status
```

Open the conflicted file.

Example:

```text
<<<<<<< HEAD
Current code
=======
Incoming code
>>>>>>> feature
```

Edit the file, remove the conflict markers, and keep the correct version.

Stage the resolved file:

```bash
git add .
```

Continue rebase:

```bash
git rebase --continue
```

Abort if necessary:

```bash
git rebase --abort
```

Skip the problematic commit:

```bash
git rebase --skip
```

## Production Scenario

A feature branch is two weeks old. Before creating a Pull Request, the developer rebases onto the latest `main`. A conflict occurs because another developer modified the same configuration file. After resolving the conflict, the rebase completes successfully, and the Pull Request contains a clean, linear history.

## Interview Tip

Always run tests after resolving rebase conflicts to ensure functionality has not been broken.

---

# 25. What Git best practices do you follow while working in a team?

## Answer

When working in a collaborative environment, following Git best practices helps maintain a clean history, reduces conflicts, and improves code quality.

### Best Practices

- Create a separate feature branch for every task.
- Pull or fetch the latest changes before starting work.
- Make small, logical commits.
- Write meaningful commit messages.
- Never commit passwords, API keys, or certificates.
- Keep `.gitignore` updated.
- Rebase or merge regularly to reduce conflicts.
- Use Pull Requests for all code changes.
- Request code reviews before merging.
- Squash unnecessary commits before merging.
- Prefer `git revert` over `git reset` on shared branches.
- Avoid force pushing to shared branches.
- Tag production releases.
- Delete merged feature branches.
- Run tests before committing or pushing.
- Follow the team's branching strategy (GitFlow or Trunk-Based Development).

## Production Scenario

In a DevOps team, every feature is developed in its own branch, reviewed through a Pull Request, validated by CI/CD pipelines, and merged only after successful automated tests and peer approval. This process minimizes defects and keeps the repository stable.

## Interview Tip

Interviewers look for collaboration skills as much as Git knowledge. Emphasize safe workflows, code reviews, automation, and communication with the team.

---

# Frequently Used Git Commands

| Command | Description |
|---------|-------------|
| `git init` | Initialize a new Git repository |
| `git clone <url>` | Clone a remote repository |
| `git status` | Show the working tree status |
| `git add <file>` | Stage changes |
| `git add .` | Stage all changes |
| `git commit -m "message"` | Commit staged changes |
| `git log --oneline` | View concise commit history |
| `git diff` | Show unstaged changes |
| `git branch` | List branches |
| `git branch <name>` | Create a new branch |
| `git checkout <branch>` | Switch branches |
| `git switch <branch>` | Switch branches (modern command) |
| `git merge <branch>` | Merge a branch |
| `git rebase <branch>` | Rebase onto another branch |
| `git cherry-pick <hash>` | Copy a specific commit |
| `git stash` | Save uncommitted changes |
| `git stash pop` | Restore stashed changes |
| `git fetch` | Download remote changes |
| `git pull` | Fetch and merge changes |
| `git push` | Push commits to remote |
| `git reset` | Move HEAD and optionally unstage/delete changes |
| `git revert` | Create a commit that undoes another commit |
| `git reflog` | Show HEAD history |
| `git blame` | Show who modified each line |
| `git tag` | Manage tags |
| `git bisect` | Find the commit that introduced a bug |

---

# Typical Git Workflow

1. Clone the repository.

```bash
git clone <repository_url>
```

2. Create a feature branch.

```bash
git checkout -b feature/login
```

3. Make code changes.

4. Check status.

```bash
git status
```

5. Stage files.

```bash
git add .
```

6. Commit changes.

```bash
git commit -m "Add login feature"
```

7. Fetch the latest changes.

```bash
git fetch origin
```

8. Rebase or merge with the latest main branch.

```bash
git rebase origin/main
```

9. Push the feature branch.

```bash
git push origin feature/login
```

10. Create a Pull Request.

11. Address review comments.

12. Merge after approval.

13. Delete the feature branch.

---

# Common Git Interview Mistakes

- Confusing `git fetch` with `git pull`.
- Using `git reset` instead of `git revert` on shared branches.
- Force pushing without understanding the consequences.
- Rebasing shared branches.
- Forgetting to rotate secrets after accidentally committing them.
- Not understanding the difference between Merge and Rebase.
- Ignoring merge conflicts without testing afterward.
- Committing directly to the `main` branch.
- Writing poor commit messages like "Update" or "Fix".

---

# Quick Revision Notes

- **Fetch** = Download only.
- **Pull** = Fetch + Merge.
- **Merge** = Preserves history.
- **Rebase** = Creates linear history.
- **Reset** = Removes commits (local history rewrite).
- **Revert** = Safely undoes commits with a new commit.
- **Cherry-pick** = Copy specific commits.
- **Reflog** = Recover lost commits.
- **Bisect** = Find the bad commit using binary search.
- **Blame** = Identify who changed a line.
- **Squash** = Combine multiple commits.
- **Hooks** = Automate checks before/after Git events.
- **Submodules** = Embed one Git repository inside another.
- **Git LFS** = Manage large binary files.
- **Tags** = Mark releases or important milestones.
- **.gitignore** = Exclude files from version control.

---



