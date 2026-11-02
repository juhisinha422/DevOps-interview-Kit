# Top Git Interview Questions & Answers – 4 Years of DevOps Experience

## 1. A developer accidentally pushes sensitive credentials to a Git repository. How would you handle this situation?

First, I would treat the exposed credentials as compromised and immediately revoke or rotate them, rather than simply deleting them from the repository. Next, I would identify the affected commits, check whether the repository is public or private, and review access logs for any suspicious activity. I would remove the credentials from Git history using tools such as `git filter-repo` or BFG Repo-Cleaner if required, and coordinate a force push with the team because rewriting shared history affects other developers. I would also check CI/CD logs and build artifacts for exposure. To prevent recurrence, I would use AWS Secrets Manager or another secret-management solution, enable Git secret scanning, and configure pre-commit hooks to detect sensitive information.

## 2. What is the difference between "git merge" and "git rebase"? Which would you prefer in a production environment?

`git merge` combines changes from one branch into another and usually creates a merge commit, preserving the complete branch history. `git rebase` moves commits from one branch on top of another, creating a cleaner and more linear history by rewriting commit history. For example, I use `git merge` when integrating completed feature branches into shared branches because it preserves the original history. I prefer rebase for cleaning up my local feature branch before creating a pull request. In a production environment, I generally prefer merge-based integration for shared branches because rewriting published history can create problems for other developers.

## 3. A developer accidentally commits code directly to the "main" branch. How would you recover safely?

First, I would check whether the commit has already been pushed to the remote repository and whether it contains any production-impacting changes. If it has not been pushed, I can use `git reset` to move the branch back to the previous commit, depending on whether the changes need to be preserved. If the commit is already pushed or shared with other developers, I would prefer `git revert <commit-id>` because it creates a new commit that safely reverses the unwanted changes without rewriting shared history. I would then create a feature branch, move the required changes there, and raise a pull request. To prevent this, I would configure branch protection rules, mandatory pull requests, and required approvals on the main branch.

## 4. How would you resolve merge conflicts when multiple developers modify the same file?

I would first identify the conflicting files using `git status` and understand which changes were introduced by each developer. Then I would open the conflicting files and review the conflict markers to determine which code should be retained or combined. I would communicate with the developers involved if the changes affect the same functionality, resolve the conflicts carefully, and run the required unit tests and application checks. After resolving the conflicts, I would use `git add <file>` followed by `git commit` if merging, or `git rebase --continue` if rebasing. I would also encourage smaller pull requests and regular synchronization with the target branch to reduce future conflicts.

## 5. What is the difference between "git reset", "git revert", and "git restore"?

These three commands are used for different types of Git recovery. `git reset` moves the current branch pointer to another commit and can also change the staging area or working directory depending on the option used. `git revert` creates a new commit that reverses the changes introduced by an earlier commit, making it safer for shared branches. `git restore` is mainly used to restore file contents in the working directory or staging area. For example, I use `git reset --soft HEAD~1` to undo the last commit while keeping changes staged, `git revert <commit-id>` to safely undo a pushed commit, and `git restore <file>` to discard unwanted local file modifications.

## 6. A production deployment fails after merging a pull request. How would you roll back the changes using Git?

First, I would identify the exact merge commit or deployment commit responsible for the failure and confirm the impact through application logs and monitoring. If the faulty change has already been merged into a shared production branch, I would use `git revert` rather than resetting the branch history. For a merge commit, I would use `git revert -m 1 <merge-commit-id>` after confirming the correct parent to retain. I would raise a pull request for the rollback, get the required approval, and allow the CI/CD pipeline or GitOps controller to deploy the previous working state. Finally, I would verify application health, logs, and customer-facing functionality before closing the incident.

## 7. How would you recover a deleted Git branch containing important commits?

If a local branch is deleted accidentally, I would first inspect the Git reflog using `git reflog` to identify the commit where the branch was last pointing. Once I find the required commit hash, I can recreate the branch using `git checkout -b recovered-branch <commit-id>` or `git switch -c recovered-branch <commit-id>`. If the branch was deleted from the remote repository, I would check whether another developer has a local copy or whether the commits are still available in the repository history. If the commit is reachable through reflog or another reference, I would recover it and push the recreated branch to the remote repository. I would also encourage regular pushes and backups for important work.

## 8. What is the difference between "git fetch" and "git pull"? When would you use each?

`git fetch` downloads the latest commits and references from the remote repository without modifying my current working branch. `git pull` performs a fetch followed by an integration operation, usually a merge or rebase depending on the configuration. I prefer using `git fetch` when working on shared branches because I can inspect incoming changes before integrating them. I use `git pull` when I am comfortable updating my local branch directly. For example, `git fetch origin` retrieves remote updates, and `git pull origin main` fetches and integrates changes from the remote main branch.

## 9. A developer accidentally performs "git reset --hard" and loses local commits. How would you recover them?

I would first avoid running additional cleanup commands because they could make recovery more difficult. I would execute `git reflog` to inspect the recent HEAD movements and identify the commit that existed before the `git reset --hard` operation. Once I locate the required commit hash, I would create a recovery branch using `git checkout -b recovery-branch <commit-id>`. This can recover commits as long as the commit objects are still available. If uncommitted changes were discarded by the reset, recovery is less certain because reflog does not normally track uncommitted file contents. In that case, I would check IDE local history, filesystem backups, or other copies of the files.

## 10. How would you identify which commit introduced a production bug using "git bisect"?

I would use `git bisect` to perform a binary search through the commit history and identify the commit that introduced the issue. First, I would mark the current faulty commit using `git bisect start` and `git bisect bad`, then identify a previously working commit using `git bisect good <commit-id>`. Git checks out intermediate commits, and I test whether the bug exists at each point, marking them with `git bisect good` or `git bisect bad`. Git gradually narrows down the range until it identifies the problematic commit. After finding it, I would inspect the changes, identify the root cause, implement a fix, and verify it through testing. Finally, I would run `git bisect reset` to return to the original branch.

## 11. What is Git cherry-picking, and how would you use it for an urgent production hotfix?

Cherry-picking allows me to apply a specific commit from one branch onto another branch without merging the entire source branch. For example, if a developer fixes a critical production issue in a development branch, but production needs only that particular fix, I can switch to the production hotfix branch and run `git cherry-pick <commit-id>`. I would resolve any conflicts, run the required tests, and raise a pull request for approval. Once merged, the CI/CD pipeline can deploy the hotfix. I would also ensure that the same fix is integrated into the development branch to avoid losing it in future releases.

## 12. Your Git repository has become extremely large and cloning takes too much time. How would you troubleshoot and optimize it?

I would first investigate what is increasing the repository size by checking its history, large files, generated artifacts, and binary files. Commands such as `git count-objects -vH` can help inspect repository storage, while tools such as `git filter-repo` can identify and remove unwanted large files from history. I would move large binaries and build artifacts to appropriate storage such as S3 or an artifact repository like Nexus instead of keeping them in Git. For repositories where only recent history is required, I could use shallow clones with `git clone --depth 1` or partial clones where supported. I would also add proper `.gitignore` rules and consider Git LFS for large files that genuinely need version control.

## 13. What happens internally when you execute "git add", "git commit", and "git push"?

When I execute `git add`, Git takes the current content of the selected files and places their snapshots into the staging area, also called the index. When I execute `git commit`, Git creates a commit object containing a snapshot reference, parent commit information, author details, timestamp, and commit message, and updates the current branch reference. When I execute `git push`, Git transfers the required Git objects and updates the remote branch reference, provided the remote accepts the update. In a CI/CD workflow, pushing changes can also trigger webhooks that start automated build, testing, and deployment pipelines.

## 14. How would you implement a Git branching strategy for development, staging, and production environments?

I would implement a controlled branching strategy based on the team's release process. Developers would create feature branches from the development branch and submit pull requests for code review and testing. Once approved and merged, changes would be deployed to the development environment. After validation, a release branch or approved promotion process would move the tested artifact into staging, followed by production approval and deployment. For production hotfixes, I would create a dedicated hotfix branch, test the fix, and merge it back into the relevant development and release branches. I would protect important branches with mandatory reviews, automated checks, and restricted direct pushes. I would also promote the same immutable build artifact across environments rather than rebuilding different versions for each environment.

## 15. A CI/CD pipeline fails because of Git authentication or permission issues. How would you troubleshoot it?

I would first inspect the pipeline logs to identify whether the failure is related to invalid credentials, expired tokens, SSH key issues, repository permissions, or branch protection rules. I would verify the configured Git credentials, access token permissions, SSH key, known hosts configuration, and repository URL. I would also check whether the CI/CD service account has the required read or write access to the repository. If the token has expired or permissions have changed, I would rotate or update the credentials securely in the CI/CD credential store. Finally, I would test repository access from the pipeline environment and ensure that credentials are not exposed in logs. For prevention, I would use dedicated service accounts, least-privilege access, and regular credential rotation.

## 16. What is the difference between a detached HEAD state and a normal branch? How would you recover from it?

In a normal Git branch, HEAD points to the branch reference, which moves forward when I create new commits. In a detached HEAD state, HEAD points directly to a specific commit instead of a branch. This usually happens when checking out a commit hash or tag directly. If I make commits in this state, they are not automatically associated with a branch. To recover, I would create a new branch using `git switch -c recovery-branch` if I want to preserve the commits. If I do not need the changes, I can simply switch back to the required branch using `git switch main`. I always check `git status` before switching to avoid losing uncommitted work.

## 17. How would you undo the last commit without losing your local code changes?

I would use `git reset --soft HEAD~1` if I want to remove the last commit while keeping all its changes in the staging area. If I want to keep the changes but move them out of the staging area, I would use `git reset HEAD~1`, which performs a mixed reset by default. If the commit has already been pushed to a shared branch, I would prefer `git revert HEAD` because it creates a new commit that reverses the previous one without rewriting shared history. Before choosing a command, I would always verify whether the commit is local or already shared with the team.

## 18. Multiple developers push changes to the same branch, causing non-fast-forward errors. How would you resolve them safely?

A non-fast-forward error usually occurs when the remote branch contains commits that are missing from my local branch. I would first run `git fetch origin` to retrieve the latest remote changes and inspect the branch history. Then I would integrate those changes using `git pull --rebase origin <branch-name>` or a merge, depending on the team's workflow. If conflicts occur, I would resolve them carefully, run tests, and continue the rebase using `git rebase --continue`. Once my local branch contains both the latest remote changes and my commits, I would push again. I would avoid force-pushing to shared branches because it can overwrite other developers' work. If history rewriting is explicitly required on a personal feature branch, I would use `git push --force-with-lease` rather than an unrestricted force push.

---

## Production-Level Git Interview Tips

For a 4-year DevOps Engineer role, I focus on three important areas while working with Git:

1. **Code safety:** Branch protection, pull requests, code reviews, secret scanning, and access control.
2. **Recovery:** Revert faulty commits, recover deleted branches, resolve conflicts, and investigate commit history.
3. **CI/CD integration:** Secure authentication, automated triggers, immutable artifacts, release management, and controlled deployments.

**My approach:** I always prefer safe Git operations on shared production branches, preserve commit history wherever possible, and verify changes through automated testing before allowing them into production.
