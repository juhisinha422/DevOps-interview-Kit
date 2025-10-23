https://www.notion.so/GIT-Branching-and-Merging-282c67bf816780ec9b36fe2936a125ed?source=copy_link


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

![image.png](attachment:4719b2be-438a-4ad0-9df7-91bf6adade6d:image.png)

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

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/fcdf6014-12a4-4fed-a486-19809f963ae1/image.png)

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

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/dd8055dc-0b8d-47dd-953a-cd591c363cd7/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0979e1a8-b57b-4408-a0c4-0938618ac4ad/a0f053b5-d466-46de-a57d-d08d2cd21e86/image.png)

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

![](https://lukemerrett.com/content/images/2021/08/72823609-8eaf-4f4e-85
