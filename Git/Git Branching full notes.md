# **GIT Branching and Merging**

Branching strategy determines how team approaches code branching. A **branching strategy** defines how a team creates, uses, merges, and deletes branches in Git during software development.

🤝 **Why It Matters**:

In real-world software engineering and DevOps:

- Teams may be distributed across time zones
- Releases may happen daily or monthly
- Features and hotfixes require coordination
- CI/CD pipelines demand **code stability**

The **branching model** chosen directly affects:

- Collaboration
- Release velocity
- Merge conflicts
- Code quality

There are 2 different branching patterns: **long-lived** and **short-lived**.

### 🔹 1. **Long-lived Branching Pattern**

### 📘 **Definition:**

A **long-lived branch** exists for a significant amount of time—**days, weeks, or even months**. Developers work on features or environments in isolated branches away from the main line.
Long-lived branching is a good option for a longer-term, deeply complex project with multiple distributed development teams. A long-lived branch is a copy or branch of the mainline code that is used to work on a feature. A long-lived branching pattern works best for a feature isolation development approach. Example Main and Dev branch .

### 📂 **Common Branches:**

- `main` or `master`: Stable production code
- `develop`: Ongoing integration of features
- `feature/login`, `release/v1.0`, `hotfix/urgent-patch`: Other long-term branches

### 📌 Real-Time Use Case (DevOps + Developer):

### Scenario:

You are working in a **large enterprise team** with multiple squads building microservices.

- You create a `develop` branch from `main`
- Developers branch off `develop` for features (feature branches may or may not be short-lived)
- DevOps builds CI pipelines around `develop`
- Once tested, `develop` merges into `main` and deploys to production

### ✅ Advantages:

- Feature isolation: Teams can work without affecting the main codebase
- Supports staged release pipelines (dev → QA → staging → prod)
- Great for versioning, enterprise workflows

---

### ❌ Disadvantages:

- Merge conflicts over time if branches drift
- Slower feedback (delayed integration = more risk)
- Not ideal for fast, frequent releases

### 🔸 2. **Short-lived Branching Pattern**

### 📘 **Definition:**

Short-lived branches are used to develop and integrate features within **a day or two**. These branches are quickly merged back into the main branch and deleted. Short-lived pattern offers a different working approach through short-lived branches. Unlike long-lived branching, which can take weeks of development, short-lived branching requires developers to integrate feature updates in days. This style of branching works best for a continuous integration development approach. Example Feature branches.

### 📂 **Common Use:**

- Feature branches: `feature/add-search`
- Bugfix branches: `bugfix/fix-login-crash`

### 📌 Real-Time Use Case:

### Scenario:

In a **startup**, developers push small changes daily:

- A developer creates `feature/user-avatar`
- Pushes code in 2 days
- Opens PR (pull request), gets reviewed
- Merges into `main` (or `develop`)
- Branch is deleted immediately

This enables **Continuous Integration** and **fast feedback loops**.

### ✅ Advantages:

- Encourages frequent commits and fast reviews
- Less chance of merge conflicts
- Enables continuous delivery and trunk-based development

---

### ❌ Disadvantages:

- Not ideal for big features unless broken into small parts
- Can overwhelm reviewers with frequent PRs
- Poor planning may result in unfinished or half-baked code

## 🧭 When to Use Which?

| **Factor** | **Long-lived Branching** | **Short-lived Branching** |
| --- | --- | --- |
| Team Size | Large distributed teams | Small or mid-sized teams |
| Feature Complexity | Large, multi-week features | Small, modular features |
| Integration Style | Scheduled / staged | Continuous |
| Merge Risk | High (delayed merges) | Low (frequent merges) |
| Tools | Jira, GitFlow | GitHub Flow, GitLab Flow, CI/CD |

<img width="545" height="429" alt="Image" src="https://github.com/user-attachments/assets/5782f2ce-52bd-4573-8355-a7f5bebc432a" />

## **Trunk-Based Development**

Trunk-Based Development is a branching model used within source control tools, such as git. When using Trunk-Based Development, developers collaborate on the trunk, often called main. They either commit directly to the trunk or use short-lived feature branches.

When I say short-lived feature branches, I mean *really* short-lived. This is all to avoid long-lived development branches and the conflicts that can happen when merging them.

The goal of Trunk-Based Development is to provide high throughput using *continuous integration (CI)* and *continuous delivery (CD)*. It benefits from short iterations, and especially the use of stories that take hours — not days — to complete. Because of this, it is not particularly compatible with waterfall but is very compatible with small broken down stories.

It would perhaps be useful to briefly discuss *Git Flow*, as an alternative to Trunk-Based Development, before diving deeper. This is to give you something concrete to compare Trunk-Based Development against.

# **Git Flow branching strategy**

Git Flow is an alternative branching model, which uses the following branches: main, develop, feature, release and hotfix.

The main branch is used to contain production-ready code that can be released, whereas the develop branch is used to contain newly developed features that are in the process of being tested. Development work actually happens on feature branches, which branch off of develop and are merged back into develop when a feature is complete.

Release branches also branch off of develop, once the develop branch has reached the desired state of the release. Once a release branch is created, only bug fixes should be added to it. Bug fixes should be merged back into the develop branch. Once a release branch is ready for deployment after some QA, it is merged into main and released.

Of course, things can go wrong and might require a hotfix. Here, a hotfix branch is created that branches off of main. Developers perform the bug fix and merge it into both develop and main. Once merged into main, another deployment is performed.

This is quite process heavy and there are a lot of branches. While this steady approach can be useful, in that it allows us to be confident before a release, it can be quite slow. There is also the risk that regressions could easily happen; if a release or hotfix branch with bug fixes on is never merged back into develop then regressions will happen.

Git flow is a popular Git branching strategy aimed at simplifying release management.

**Git flow strategy uses 5 branches**: 

**Main Branches**

- **Master**
- **Develop**

**Supporting branches**

- **Feature**
- **Release**
- **Hotfix**

<img width="786" height="1042" alt="Image" src="https://github.com/user-attachments/assets/b7a3abd4-956f-457a-b5ed-144a876f6f2c" />

### **Master branch :**

We consider `origin/master` to be the main branch where the source code of `HEAD` always reflects a *production-ready* state. The purpose of the master branch in the Git flow workflow is to contain production-ready code that can be released. The main branch is created at the start of a project and is maintained throughout the development process. The branch can be tagged at various commits in order to signify different versions or releases of the code, and other branches will be merged into the main branch after they have been sufficiently vetted and tested.

### **Develop branch :**

We consider `origin/develop` to be the main branch where the source/Development code of `HEAD` always reflects a state with the latest delivered development changes for the next release. Some would call this the “**integration branch"**. The develop branch is created at the start of a project and is maintained throughout the development process, and contains pre-production code with newly developed features that are in the process of being tested. Newly created features should be based off the develop branch, and then merged back in when ready for testing. When the source code in the `develop` branch reaches a stable point and is ready to be released, all of the changes should be merged back into `master` somehow and then tagged with a release number.

## **Supporting branches :**

### **Feature Branch** :
May branch off from:

<aside>
🔑

`develop`

Must merge back into:

`develop`

Branch naming convention:

anything except `master`, `develop`, `release-*`, or `hotfix-*`

</aside>

<img width="534" height="804" alt="Image" src="https://github.com/user-attachments/assets/15866c36-5a95-4a4b-9e2b-0cbe3ef1ce1a" />

<img width="256" height="687" alt="Image" src="https://github.com/user-attachments/assets/f9aa528d-4439-4c3c-b215-1af3833adf83" />

Feature branches (or sometimes called topic branches) are used to develop new features for the upcoming or a distant future release. When starting development of a feature, the target release in which this feature will be incorporated may well be unknown at that point. The essence of a feature branch is that it exists as long as the feature is in development, but will eventually be merged back into `develop` (to definitely add the new feature to the upcoming release) or discarded (in case of a disappointing experiment).

Feature branches typically exist in developer repos only, not in `origin`.

Feature branching is a simple branching strategy where each new feature is developed on its own branch. This approach allows for isolated development and testing of features, making it easier to roll back changes if necessary. When working on a new feature, you will start a feature branch off the develop branch, and then merge your changes back into the develop branch when the feature is completed and properly reviewed

### **Creating a feature branch**

When starting work on a new feature, branch off from the `develop` branch.

$ git checkout -b myfeature develop
Switched to a new branch "myfeature"

### **Incorporating a finished feature on develop**

Finished features may be merged into the `develop` branch to definitely add them to the upcoming release:

<aside>
💡

**$ git checkout develop**
Switched to branch 'develop'
**$ git merge --no-ff myfeature**
Updating ea1b82a..05e9557
(Summary of changes)
**$ git branch -d myfeature**
Deleted branch myfeature (was 05e9557).
**$ git push origin develop**

</aside>

The `--no-ff` flag causes the merge to always create a new commit object, even if the merge could be performed with a fast-forward. This avoids losing information about the historical existence of a feature branch and groups together all commits that together added the feature. Compare:

![](https://nvie.com/img/merge-without-ff@2x.png)

In the latter case, it is impossible to see from the Git history which of the commit objects together have implemented a feature—you would have to manually read all the log messages. Reverting a whole feature (i.e. a group of commits), is a true headache in the latter situation, whereas it is easily done if the `--no-ff` flag was used.

## **Release branch**

May branch off from:

`develop`

Must merge back into:

`develop` and `master`

Branch naming convention:

`release-*`

Release branches support preparation of a new production release. They allow for last-minute dotting of i’s and crossing t’s. Furthermore, they allow for minor bug fixes and preparing meta-data for a release (version number, build dates, etc.). By doing all of this work on a release branch, the `develop` branch is cleared to receive features for the next big release.

The key moment to branch off a new release branch from `develop` is when develop (almost) reflects the desired state of the new release. At least all features that are targeted for the release-to-be-built must be merged in to `develop` at this point in time. All features targeted at future releases may not—they must wait until after the release branch is branched off.

**Creating a release branch**

`$ git checkout -b release-1.2 develop`

This new branch may exist there for a while, until the release may be rolled out definitely. During that time, bug fixes may be applied in this branch (rather than on the `develop` branch). Adding large new features here is strictly prohibited. They must be merged into `develop`, and therefore, wait for the next big release.

**Finishing a release branch** 

When the state of the release branch is ready to become a real release, some actions need to be carried out. First, the release branch is merged into `master` (since every commit on `master` is a new release *by definition*, remember). Next, that commit on `master` must be tagged for easy future reference to this historical version. Finally, the changes made on the release branch need to be merged back into `develop`, so that future releases also contain these bug fixes.

The first two steps in Git:

`$ git checkout master
Switched to branch 'master'
$ git merge --no-ff release-1.2
Merge made by recursive.
(Summary of changes)
$ git tag -a 1.2`

The release is now done, and tagged for future reference

To keep the changes made in the release branch, we need to merge those back into `develop`, though. In Git:

`$ git checkout develop
Switched to branch 'develop'
$ git merge --no-ff release-1.2
Merge made by recursive.
(Summary of changes)`

This step may well lead to a merge conflict (probably even, since we have changed the version number). If so, fix it and commit.

## **Hot fix branches:**

May branch off from:

`master`

Must merge back into:

`develop` and `master`

Branch naming convention:

`hotfix-*`      

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/cb5588d2-c1ee-49eb-9a25-3e2fa2d9a77f/4ad5f8af-29c5-4c02-9bde-4226361fe7d5.png)

[]()

When a critical bug in a production version must be resolved immediately, a hotfix branch may be branched off from the corresponding tag on the master branch that marks the production version.

The essence is that work of team members (on the `develop` branch) can continue, while another person is preparing a quick production fix.

### Key Characteristics of a Hotfix Branch:

1. **Purpose**: To resolve high-priority bugs in the production environment.
2. **Branching**: Created directly from the main or production branch (e.g., `trunk`).
3. **Isolation**: Isolated from ongoing development to avoid unnecessary changes or conflicts.
4. **Naming Convention**: Often named with a prefix like `hotfix/` followed by a descriptive name or identifier (e.g., `hotfix/critical-bug-123`).
5. **Short-lived**: Typically merged back into the main branch and deleted after the fix is completed.
6. **Merging Strategy**:
    - Merged into the main branch to apply the fix in production.
    - Merged back into the development branch (e.g., `DEV`) to ensure the fix is present in future releases.

### Example Workflow:

1. **Identify Issue**: A critical bug is reported in production.
2. **Create Branch**:
    
    ```bash
    bash
    Copy code
    git checkout main
    git checkout -b hotfix/critical-bug
    
    ```
    
3. **Fix the Bug**: Implement and test the changes on the hotfix branch.
4. **Merge Changes**:
    - Merge into `main` to deploy the fix.
    - Merge into `DEV` to propagate the fix to ongoing development.
    
    ```bash
    git checkout main
    git merge hotfix/critical-bug
    git checkout DEV
    git merge hotfix/critical-bug
    
    ```
    
5. **Delete the Branch**:
    
    ```bash
    
    git branch -d hotfix/critical-bug
    
    ```
    

### Benefits:

- Ensures a clear and isolated process for resolving production issues.
- Prevents accidental inclusion of incomplete features in the production environment.
- Maintains stability while addressing urgent problems effectively.

**Creating the hotfix branch :**

Hotfix branches are created from the `master` branch. For example, say version 1.2 is the current production release running live and causing troubles due to a severe bug. But changes on `develop` are yet unstable. We may then branch off a hotfix branch and start fixing the problem:

```
$ git checkout -b hotfix-1.2.1 master
Switched to a new branch "hotfix-1.2.1"
$ git commit -a -m "Bumped version number to 1.2.1"
[hotfix-1.2.1 41e61bb] Bumped version number to 1.2.1
1 files changed, 1 insertions(+), 1 deletions(-)

Then, fix the bug and commit the fix in one or more separate commits.

$ git commit -m "Fixed severe production problem"
[hotfix-1.2.1 abbe5d6] Fixed severe production problem
5 files changed, 32 insertions(+), 17 deletions(-)
```

**Finishing a hotfix branch:**

When finished, the bugfix needs to be merged back into `master`, but also needs to be merged back into `develop`, in order to safeguard that the bugfix is included in the next release as well. This is completely similar to how release branches are finished.

First, update `master` and tag the release.

`$ git checkout master
Switched to branch 'master'
$ git merge --no-ff hotfix-1.2.1
Merge made by recursive.
(Summary of changes)
$ git tag -a 1.2.1`

Next, include the bugfix in `develop`, too:

`$ git checkout develop
Switched to branch 'develop'
$ git merge --no-ff hotfix-1.2.1
Merge made by recursive.
(Summary of changes)`

The one exception to the rule here is that, **when a release branch currently exists, the hotfix changes need to be merged into that release branch, instead of `develop`**. Back-merging the bugfix into the release branch will eventually result in the bugfix being merged into `develop` too, when the release branch is finished. (If work in `develop` immediately requires this bugfix and cannot wait for the release branch to be finished, you may safely merge the bugfix into `develop` now already as well.)

![GITBranchingFlow.gif](attachment:40b3624e-e85a-495c-98d9-74d1f0a63d9a:GITBranchingFlow.gif)

# **Git Merging**

## **What is Git Merging?**

Git merging is the process of combining the changes from one branch into another. It integrates the history and commits from one branch into the branch where the merge is executed. This is a fundamental operation in collaborative workflows to integrate changes made by multiple contributors or branches.

How to Merge Two Branches

- **Switch to the Target Branch**:
    
    ```bash
    
    git checkout main
    git fetch
    git pull
    
    ```
    
- **Merge the Source Branch**:
    
    ```bash
    
    git checkout feature-branch
    git fetch 
    git pull
    
    git merge feature-branch
    
    ```
    
- **Push the Changes** (if working with remotes):
    
    ```bash
    
    git push origin main
    
    ```
    

## **Types of Git Merging**

we have below types of merging options 

## **Squash & Merge:**

- Combines all commits from the source branch into a single commit before merging.
- The commit history of the source branch is not preserved in the target branch.
- **Example**:
    
    ```bash
    git checkout main
    git merge --squash feature-branch
    git commit -m "Merged feature-branch with squash"
    
    ```
    

## **Re base & Merge:**

- Rewrites the commits of the source branch onto the target branch’s history, creating a linear commit history.
- **No merge commit is created**, but history is rewritten.
- **Example**:
    
    ```bash
    git checkout feature-branch
    git rebase main
    git checkout main
    git merge feature-branch
    
    ```
    

**Example**

We have 2 branches, the base branch where the merge is going into (e.g: main, or release) and the branch being merged.

The branch being merged was created at commit **2**, so contains commits 1,2,A,B,C

The base branch has had changes made since the new branch was made: 3,4,5.

![](https://lukemerrett.com/content/images/2021/08/72823609-8eaf-4f4e-8514-47d1f27ef037.png)

1. **Default Merge commit/Three-Way Merge/—no—ff**

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/b0b0d886-ba26-42c3-94ff-73bed60b25c7/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/4194ef3a-5c9f-4f26-9db7-87554d64ee9c/image.png)

A standard merge will take each commit in the branch being merged (Source branch) and add them to the history of the base branch(Target) .

It will also create a `merge commit`, That’s contain base branch and Target branch latest commit Details.

Base branch have commit 1,2 and created  source branch  it have 1,2,A,B,C and meantime Target branch has new commits 3,4,5 After merging git will add each commit of source branch to target branch like  Target : 1,2,A,3,B,4,C,5,6 so here 6 is new commit it contain details of 5,C commits

- Used when the branches have diverged and Git cannot simply "fast-forward" the target branch.
- Git compares the common ancestor of the two branches with their respective heads to produce a new merge commit.
- **Creates a new commit** to represent the merge.

**Example**

```bash
git checkout main
git merge feature-branch

```

**Advantages:**

- Most descriptive and verbose history, tells us exactly when things happened, helps give the best context about code changes
- Allows us to see a graph of when branches were made using `git log --oneline --graph` which can help understanding why changes were made and when
- Allows us to see each commit that made up the eventual merged changes, no loss of granularity

**Disadvantages:**

- Merge commits are often seen as messy as they are empty and only really there for historical reasons. Can be especially confusing if you are trying to revert a set of changes.
- Can end up having a complex graph of previous branches that’s more difficult to read

## **2.Fast-Forward Merge**

If we change our example so **no new commits** were made to the base branch since our branch was created, Git can do something called a “Fast Forward Merge”. This is the same as a Merge but **does not** create a merge commit.

This is as if you made the commits directly on the base branch. The idea is because no changes were made to the base branch there’s no need to capture a branch had occurred.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/82a1f54b-c321-49bf-89ca-dd58685f1ffe/image.png)

- Occurs when the branch being merged has no additional commits since the source branch was created.
- Git simply moves the pointer of the target branch to the head of the source branch.
- **No new commit** is created.
- **Example**:
    
    ```bash
    
    git checkout main
    git merge feature-branch
    
    ```
    

**Advantages:**

- Keeps a very clean commit history
- Allows us to see each commit that made up the eventual merged changes, no loss of granularity

**Disadvantages:**

- Can only be done when the base branch hasn’t had any new commits, a rarity in a shared repository
- Can be seen as a inaccurate view of history as it hasn’t captured that a branch was created, or when it was merged

## 3.**Squash & Merge:**

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/0ea3fff1-f18a-4b43-ae93-a1aa29183bb2/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/0034a78b-b428-4f5c-b084-85b1d8778194/image.png)

Squash takes all the commits in the branch (A,B,C) and melds them into 1 commit. That commit is then added to the history, but none of the commits that made up the branch are preserved

- Combines all commits from the source branch into a single commit before merging.
- The commit history of the source branch is not preserved in the target branch.
- **Example**:
    
    ```bash
    bash
    Copy code
    git checkout main
    git merge --squash feature-branch
    git commit -m "Merged feature-branch with squash"
    
    ```
    

**Advantages:**

- Keeps a very clean commit history
- Can look at a single commit to see a full piece of work, rather than shifting through multiple commits in the log

**Disadvantages:**

- Lost of granularity, any useful detail in those commits that made up the branch is lost, as are any interesting decisions, changes in logic etc captured during the development process.

## **Rebase and Merge**

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/6a220ef4-5137-4ad8-bbb5-76291fe5569d/image.png)

A rebase and merge will take where the branch was created and move that point to the last commit into the base branch, then reapply the commits on top of those changes.

This is like a fast forward merge, but works when changes have been made into the base branch in the mean while

- Rewrites the commits of the source branch onto the target branch’s history, creating a linear commit history.
- **No merge commit is created**, but history is rewritten.
- **Example**:
    
    ```bash
    
    git checkout feature-branch
    git rebase main
    git checkout main
    git merge feature-branch
    
    ```
    

**Advantages:**

- Keeps a very clean commit history
- Keeps the individual commit granularity

**Disadvantages:**

- Can cause frustration as, if someone was to commit to the base branch against before you get to merge, you have to rebase again
- Can be seen as a inaccurate view of history as it hasn’t captured that a branch was created, or when it was merged
- Difficult to see which commits relate to which PR / branch

**How to Merge Two Branches in Git**

# [**Step 1: Clone the Repository**](https://github.com/snblaise/MASTERING-GIT-AND-VERSION-CONTROL/tree/master/Mastering%20Git%20Part%205#step-1-clone-the-repository)

Start by cloning the repository to your local machine if you haven’t already. Replace `<repository_url>` with the actual URL of your GitHub repository.

```
git clone <repository_url>
cd <repository_directory>
```

# [Step 2: Checkout Current Branch](https://github.com/snblaise/MASTERING-GIT-AND-VERSION-CONTROL/tree/master/Mastering%20Git%20Part%205#step-2-check-current-branch)

Ensure you are on the branch where you want to merge changes into. You can list all local branches and check the current branch with:

```
git branch
```

To switch to a different branch, use:

```
git checkout <branch_name>
```

# [**Step 3: Update the Current Branch**](https://github.com/snblaise/MASTERING-GIT-AND-VERSION-CONTROL/tree/master/Mastering%20Git%20Part%205#step-3-update-the-current-branch)

It’s essential to have the latest changes from the target branch (the one you want to merge into). Fetch the latest updates from the remote repository:

```
git fetch origin
git pull

checkout to Target branch and update it 
git fetch 
git pull
```

# [**Step 4: Merge the Target Branch**](https://github.com/snblaise/MASTERING-GIT-AND-VERSION-CONTROL/tree/master/Mastering%20Git%20Part%205#step-4-merge-the-target-branch)

Now, you are ready to merge the target branch into your current branch. Use the `git merge` command:

```
git merge <target_branch>
```

For example, if you want to merge the `feature/new-feature` branch into the current branch:

```
git merge feature/new-feature
```

# [**Step 5: Resolve Conflicts (If Any)**](https://github.com/snblaise/MASTERING-GIT-AND-VERSION-CONTROL/tree/master/Mastering%20Git%20Part%205#step-5-resolve-conflicts-if-any)

If there are conflicting changes between the branches you’re merging, Git will prompt you to resolve them. Open the conflicted files, resolve the conflicts, and save the changes.

After resolving conflicts, add the modified files to the staging area and continue the merge:

```
git add <conflicted_files>
git commit
```

# [**Step 6: Push Changes to GitHub**](https://github.com/snblaise/MASTERING-GIT-AND-VERSION-CONTROL/tree/master/Mastering%20Git%20Part%205#step-6-push-changes-to-github)

Once the merge is complete and there are no conflicts or after you’ve resolved them, push the changes to GitHub:

```
git push origin <current_branch>
```

# Rebasing (`git rebase`)

## **What is Git Merge?**

![](https://miro.medium.com/v2/resize:fit:1225/1*6ru-sB5tnJq4v94lCZARgg.png)

In Git, `*git merge*` is a command that combines the changes from one branch into another branch. It creates a new commit, often referred to as a "**merge commit**," that has two parent commits, preserving the history of both branches. This is a common way to integrate changes from one branch, such as a feature branch, into another branch, typically the main or master branch.

**Here’s how to use git merge:**

- ***Switch to the Target Branch***: First, you need to be on the branch where you want to merge the changes. For example, if you want to merge changes from a feature branch into the main branch, switch to the main branch:

```
git checkout main
```

- ***Merge the Source Branch***: Then, use the `*git merge*` command to merge the changes from the source branch, for example: the feature-branch, into the target branch “main” in this case:

```
git merge feature-branch
```

- ***Resolve Conflicts (if any)***: Git will attempt to automatically merge the changes, but if there are conflicting changes in both branches, you’ll need to resolve these conflicts manually.
- ***Complete the Merge***: Once conflicts (if any) are resolved, and you’re satisfied with the merge, you can commit the changes to create the merge commit.

**When to Use Git Merge:**

Use `*git merge*` in the following scenarios:

1. *Shared Branches*: When you’re working on a project collaboratively and want to integrate changes from feature branches into the main branch.
2. *Public Repositories*: In open-source projects, using merge commits can provide a clear history of contributions.

To summarize, `*git merge*` is a Git command that is used to integrate changes from one branch into another. It creates merge commits to preserve the history of both branches, making it suitable for shared or public repositories and collaborative development.

## **What is Git Rebase?**

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/ea788323-5bd4-4016-80c2-0bf657cc30f7/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/ca22201a-9086-4d1e-b2d9-a2d711d7db9a/image.png)

In Git, `*git rebase*` is a powerful command that allows you to change the base of your current branch. Essentially, it rewrites the commit history of your branch by moving it to a new starting point, usually another branch.

To put it simply, when you perform a rebase, Git takes the commits from your current branch and reapplies them on top of the target branch. This creates a linear, more organized commit history by eliminating the merge commits that can clutter your project’s history.

**How to Use Git Rebase:**

- ***Switch to Your Branch***: First, make sure you’re on the branch you want to rebase. For example, if you’re on a feature branch and you want to rebase it onto the “main” branch:

```
git checkout feature-branch
```

- ***Initiate the Rebase***: Use the `*git rebase*` command followed by the name of the branch you want to rebase onto. For example, to rebase your “feature-branch” onto “main”:

```
git rebase main
```

- ***Resolve Conflicts (if any)***: During the rebase, Git might encounter conflicts when applying your commits on top of the new base. You’ll need to resolve these conflicts manually. Read this article on how to resolve conflicts.
- ***Complete the Rebase***: Once you’ve resolved any conflicts, continue the rebase with:

```
git rebase --continue
```

- ***Finish the Rebase***: When the rebase is complete, you’ll have a linear history with your branch’s changes neatly applied on top of the target branch.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/aedc8471-97d2-48c3-be85-7c391e7bf486/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/0121d0c0-9e66-4214-8c3e-9e2dd2bf41ff/image.png)

### **The Golden Rule of Rebasing**

Once you understand what *rebasing* is, the most important thing to learn is when *not* to do it. The golden rule of git rebase is to never use it on ***public*** branches.

For example, think about what would happen if you rebased **master** onto your **feature** branch:

![](https://miro.medium.com/v2/resize:fit:982/1*-7QP_r7I0qbZo0tHjjUyDQ.jpeg)

The rebase moves all of the commits in master onto the tip of the feature. The problem is that this only happened in your *repository*. All of the other developers are still working with the original master. Since rebasing results in brand new commits, Git will think that your master branch’s history has diverged from everybody else’s.

The only way to synchronize the two master branches is to merge them back together, resulting in an extra merge commit and two sets of commits that contain the same changes (the original ones, and the ones from your rebased branch). Needless to say, this is a very confusing situation.

So, before you run git rebase, always ask yourself, “Is anyone else looking at this branch?” If the answer is yes, take your hands off the keyboard and start thinking about a non-destructive way to make your changes. Otherwise, you’re safe to re-write history as much as you like.

## **Problem Statement**

Consider that our team just completed the production release from master branch. Now they have started working on a entirely new feature in one of the dedicate branch named as **dev-feature01** branch. However, some one found a bug in the production release. Therefore, one of the developers created a **quickfix** branch to fix the bug and merged his/her code into **master** branch. Now we need to bring together the **master** branch and **dev-feature01** branch.

Below is graphical summary of what commit tree looks like:

![](https://miro.medium.com/v2/resize:fit:1225/1*P8HvnNGEgOp7uuw5Uvf-HA.png)

problem-statement-git-branches-with-different-history

## **Solution 01: Git Merge**

The merge is the most widely used method. To incorporate changes from **master** branch to **dev-feature01** branch, first we need to checkout the **dev-feature01** branch and then merge the **master** branch:

```
git checkout dev-feature01
git merge master
```

Or, we can condense this to a one-liner:

```
git checkout master dev-feature01
```

Once, its successful, the result commit tree would look something like below:

![](https://miro.medium.com/v2/resize:fit:1225/1*bMfmJ0HjxtpR1tyT8-EV5w.png)

what-happens-after-git-merge

Merging is nice because it’s a *non-destructive* operation. The existing branches are not changed in any way. However, it adds an extra commit called merge commit. This is added every time we need to incorporate changes from other branches into this branch.

## **Solution 02: Git Rebase**

As an alternative to merging, we can rebase the **dev-feature01** branch onto **master** branch using the following commands:

```
git checkout dev-feature01
git rebase master
```

This moves the entire **dev-feature01** branch to begin on the tip of the master branch, effectively incorporating all of the new commits in **master**. But, instead of using a merge commit, rebasing *re-writes* the project history by *creating brand new commits* for each commit in the original branch.

So our commit tree would look something like below:

![](https://miro.medium.com/v2/resize:fit:1225/1*z810gkDE9jR_fi0bT52eqA.png)

the-resultant-commit-tree-after-rebasing

So instead of commits E and F, we have the commits E’ and F’. Note that the commits E and F are still there in git history, but they are just not accessible any more.

### **Resolving Conflicts During Rebasing**

If there are conflicts during rebasing, Git stops and allows you to resolve them.

### **Steps:**

1. Resolve the conflict in the affected file(s).
2. Mark the conflict as resolved:
    
    ```bash
    
    git add <file>
    
    ```
    
3. Continue rebasing:
    
    ```bash
    
    git rebase --continue
    
    ```
    

---

### **4. Aborting a Rebase**

If you decide to cancel the rebase process:

```bash

git rebase --abort

```

### 2.10.3 Interactive Rebasing (`git rebase -i`)

Interactive rebasing lets you edit, squash, or reorder commits or pick of selected comiits and drop the selected commit's.

### **Command**

```bash
git rebase -i <commit-hash>

```

### **Example**

1. Start an interactive rebase:
    
    ```bash
    
    git rebase -i HEAD~3
    
    ```
    
    This opens an editor showing the last 3 commits:
    
    ```sql
    
    pick abc123 Commit message 1
    pick def456 Commit message 2
    pick ghi789 Commit message 3
    
    ```
    
2. Actions you can perform:
    - **pick**: Use the commit as is.
    - **reword**: Edit the commit message.
    - **edit**: Pause to edit the commit.
    - **squash**: Combine the commit with the previous one.
    - **drop**: Remove the commit.
3. Example: Squash commits:
    
    ```sql
    
    pick abc123 Commit message 1
    squash def456 Commit message 2
    squash ghi789 Commit message 3
    
    ```
    
    Save and exit. Git combines these commits into one.
    

# **Advantages of Rebasing**

1. **Linear History**: Rebasing can make your commit history linear, which is often easier to follow.
2. **Cleaner** **History**: It eliminates unnecessary merge commits, reducing clutter in your history.
3. **Easier** **Conflict** **Resolution**: Conflicts, if any, are often easier to resolve when rebasing because you deal with them one at a time as the commits are applied.

# **Cautionary Notes**

- **History Alteration**: Rebasing rewrites the commit history, so it should not be used on shared branches. If you’ve pushed your branch to a remote repository, rebasing can cause issues for collaborators.
- **Potential for Conflicts**: Conflicts can occur during the rebase process, and you’ll need to resolve them manually.
- **Use with Caution**: While powerful, rebasing should be used judiciously, especially in a team environment. It’s better suited for personal or feature branches.

# **When to Use Git Rebase**

1. **Feature Branches**: Rebasing is great for feature branches, making their commit history cleaner and more manageable.
2. **Pull Requests**: You can use rebasing to keep your feature branch up-to-date with the main branch before creating a pull request.
3. **Maintaining a Clean History**: If you want to maintain a clean and linear commit history, rebasing is your friend.

# **How to Avoid Common Pitfalls**

- **Communicate**: If you’re working in a team, communicate with your collaborators before rebasing to avoid conflicts.
- **Backup**: Always make a backup of your branch before rebasing to avoid losing work.
- **Practice**: The more you use `git rebase`, the better you'll become at managing it effectively.

# Cherry-Picking Commits (`git cherry-pick`)

### **What is Cherry-Picking?**

Cherry-picking is a Git command that allows you to apply specific commits from one branch to another. Instead of merging or rebasing, you can selectively copy individual commits into the current branch.`*git cherry-pick` is a powerful command that enables arbitrary Git commits to be picked by reference and appended to the current working HEAD. Cherry picking is the act of picking a commit from a branch and applying it to another. `git cherry-pick` can be useful for undoing changes. For example, say a commit is accidently made to the wrong branch.*

### **Why Use Cherry-Picking?**

- To apply a bug fix or feature commit from another branch to your current branch.
- To include specific changes without merging the entire branch.
- To bring isolated commits into multiple branches without merging all changes.

### **Basic Usage**

### **Command**

```bash

git cherry-pick <commit-hash>

```

### **Example Workflow**

1. **Find the commit hash you want to cherry-pick:**
    
    ```bash
    
    git log
    
    ```
    
    Example output:
    
    ```sql
    
    commit abc12345 (HEAD -> feature-branch)
    Author: Your Name <you@example.com>
    Date:   Mon Jan 1 10:00:00 2025 +0000
    
        Add new feature
    
    ```
    
2. **Switch to the target branch:**
    
    ```bash
    
    git checkout main
    
    ```
    
3. **Cherry-pick the commit:**
    
    ```bash
    
    git cherry-pick abc12345
    
    ```
    
4. **Result:**
The commit from `feature-branch` is applied to the `main` branch.

### **Advanced Options**

### **Cherry-Picking Multiple Commits**

You can cherry-pick multiple commits by specifying a range:

```bash

git cherry-pick <commit-hash1>..<commit-hash2>

```

This applies all commits between `commit-hash1` and `commit-hash2` (excluding `commit-hash1`).

Or list them individually:

```bash

git cherry-pick <commit-hash1> <commit-hash2>

```

### **Skip a Commit During a Cherry-Pick**

If a conflict arises and you want to skip the problematic commit:

```bash

git cherry-pick --skip

```

### **Abort a Cherry-Pick**

To cancel the cherry-pick process:

```bash

git cherry-pick --abort

```

---

### **Resolving Conflicts During Cherry-Picking**

If conflicts arise during cherry-picking, Git pauses the process to let you resolve them.

1. Resolve the conflicts in the affected file(s).
2. Mark the conflicts as resolved:
    
    ```bash
    
    git add <file>
    
    ```
    
3. Continue the cherry-pick:
    
    ```bash
    
    git cherry-pick --continue
    
    ```
    

### **Interactive Cherry-Picking**

Cherry-pick with commit editing:

```bash

git cherry-pick -e <commit-hash>

```

This lets you modify the commit message before applying the commit.

## **How to use cherry-pick?**

Using cherry-pick is very easy if you follow these simple steps in order.

![](https://miro.medium.com/v2/resize:fit:1225/1*edXEsvCUwVtq5Zr2mQGZFA.png)

Suppose you have a tree like this. Now you want commit ***h*** to be cherry-picked from feature-two branch to master.

![](https://miro.medium.com/v2/resize:fit:1225/1*nfNfum6_DJNlwGbiZDPF_w.png)

The steps to achieve this are as follows:

1. Checkout the branch you want to put the commit to. In our case, we want the commit to go to master.

```
git checkout master
```

2. Now we need to create a branch off of master to put our cherry-picked commit to. So, the next step is to create a new branch from master.

```
git checkout -b cherry-pick-commit----or----git branch cherry-pick-commit
git checkout cherry-pick-commit
```

3. Now it’s time to cherry-pick our commit. For that, you only need the SHA of the commit which you can get from the git GUI or by command line through **git log** command. The commit SHA is generally veryyyyyy long but for simplicity, I have denoted the commits with just single alphabets.

```
git cherry-pick h
```

4. Now you have the cherry-picked commit in your cherry-pick-commit branch. Now push the changes to your repo.

```
git push origin cherry-pick-commit
```

5. Create a PR with the base branch as master, merge it and you’re done.

# Working with Tags (`git tag`)

Git tags are a way to mark specific points in your repository's history as important. Typically, tags are used to mark release points (e.g., v1.0, v2.0, etc.). Unlike branches, which can move as new commits are added, tags are fixed to a specific commit.

### Types of Git Tags

1. **Annotated Tags**:
    - These are stored as full objects in the Git database.
    - Annotated tags can include a message, the tagger’s name, email, and date.
    - They are considered permanent objects in the Git database.
    
    To create an annotated tag:
    
    ```bash
    
    git tag -a <tagname> -m "Tag message"
    
    ```
    
    Example:
    
    ```bash
    git tag -a v1.0 -m "Release version 1.0"
    
    ```
    
2. **Lightweight Tags**:
    - These are simpler and act like a pointer to a specific commit.
    - They don’t store additional information like a message or tagger details.
    
    To create a lightweight tag:
    
    ```bash
    git tag <tagname>
    
    ```
    
    Example:
    
    ```bash
    
    git tag v1.0
    
    ```
    

### Listing Tags

To see a list of all tags in a repository:

```bash
git tag

```

### Viewing Tag Details

For an annotated tag, to see more details:

```bash
git show <tagname>

```

### Sharing Tags

By default, `git push` doesn’t transfer tags to the remote repository. You need to explicitly push tags.

To push a specific tag:

```bash
git push origin <tagname>

```

To push all tags:

```bash
git push origin --tags

```

### Deleting Tags

To delete a tag locally:

```bash
git tag -d <tagname>

```

To delete a tag from the remote:

```bash
git push origin --delete <tagname>

```

### Checking Out Tags

You can check out a tag to view the repository at the state of that tag. However, this will put you in a "detached HEAD" state.

```bash
git checkout <tagname>

```

If you want to create a new branch from a tag:

```bash
git checkout -b <branchname> <tagname>

```

### Practical Uses of Tags

- **Releases**: Marking a particular point in your project as a release version.
- **Milestones**: Marking significant points in the development process.
- **Deployments**: Tagging a commit to mark a deployment.

Using tags effectively can help keep your repository organized, especially when dealing with multiple releases or versions of a project.

# Git Hooks for Automation

Git hooks are scripts that Git executes before or after events like commits, merges, and pushes. They can be used to automate tasks, enforce policies, or integrate with external tools.

### Types of Git Hooks

1. **Client-Side Hooks**:
These are executed on your local machine before or after certain operations, like committing or merging.
    - **Pre-commit**: Runs before a commit is finalized. Common uses include linting code or running tests.
    - **Prepare-commit-msg**: Runs before the commit message editor is launched. Useful for modifying the default message.
    - **Commit-msg**: Runs after the commit message is created. Often used to validate the commit message format.
    - **Post-commit**: Runs after a commit is made. Can be used for notifications or logging.
2. **Server-Side Hooks**:
These are executed on the Git server during operations like receiving a push.
    - **Pre-receive**: Runs before any refs are updated on the server. Used to enforce policies.
    - **Update**: Similar to pre-receive, but runs once per branch being pushed.
    - **Post-receive**: Runs after the push has been completed. Commonly used to trigger CI/CD pipelines.

### Creating and Using Hooks

1. **Locating Hooks**:
Hooks are located in the `.git/hooks/` directory of your repository. By default, Git provides sample hooks (e.g., `pre-commit.sample`).

**Creating a Hook**:

- Rename the sample file to remove the `.sample` extension (e.g., `pre-commit`).

Here's an example of a **post-receive hook** that blocks a push if any commit message in the pushed branch does not follow the correct format: `branch_name:commit message`.

### Post-Receive Hook Example

```bash

#!/bin/sh

# Function to validate commit message
validate_commit_message() {
  local branch_name=$1
  local commit_message=$2

  # Expected format: branch_name:commit message
  if [[ ! "$commit_message" =~ ^$branch_name: ]]; then
    echo "Commit message '$commit_message' does not follow the required format: '$branch_name:commit message'"
    return 1
  fi
  return 0
}

# Read oldrev, newrev, and refname from stdin
while read oldrev newrev refname; do
  branch_name=$(echo $refname | sed 's|refs/heads/||')

  # Get all commits in the push
  for commit in $(git rev-list $oldrev..$newrev); do
    commit_message=$(git log -1 --pretty=%B $commit | head -n 1)

    if ! validate_commit_message "$branch_name" "$commit_message"; then
      echo "Push rejected due to invalid commit message format."
      exit 1
    fi
  done
done

echo "All commit messages are correctly formatted."
exit 0

```

### How It Works:

1. **Branch Name Extraction**: The branch name is extracted from `refname`.
2. **Commit Iteration**: The script iterates over each commit in the range from `oldrev` to `newrev`.
3. **Message Validation**: The `validate_commit_message` function checks if the commit message starts with the branch name followed by a colon (`:`).
4. **Push Rejection**: If any commit message does not match the expected format, the push is rejected with an error message.

This hook ensures that every commit in the pushed branch adheres to the required message format.
