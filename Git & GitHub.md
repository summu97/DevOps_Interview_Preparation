Below is a **real interview–grade Git + GitHub question set**, split into **theoretical + scenario-based**, aligned with senior DevOps expectations.

---

# 🔹 Git – Theoretical Interview Questions

### Centralized (SVN) — what you actually get

When you checkout:

* You get **files only**
* You do **NOT** get:

  * Full commit history
  * All branches
  * Repo metadata

So your machine **cannot act as the repository**.

👉 If your laptop is deleted, **nothing is lost**
👉 If the server is deleted, **everything is lost**

That’s why it’s called **one copy**.

---

## Distributed (Git) — what you get

When you clone:

* You get:

  * Files
  * Full history
  * All branches
  * Repo metadata

Your machine **IS a repository**.

👉 If server is deleted, **any developer’s machine can restore it**

---

## 1️⃣ Git Fundamentals

### 1. What is Git?
* Global Information Tracker
* Distributed Version Control System
---

### 2. Difference between:
* Git vs SVN
  - Git is a Distributed Version Control System (DVCS)
  - SVN is a Centralized Version Control System (CVCS)

* Git vs Mercurial
  - Git: Powerful, flexible, industry standard.
  - Mercurial: Simpler, cleaner, but less widely adopted.
* 👉 Both are distributed version control systems; Git just won the popularity war.

---

### 3. What problem does Git solve?
* Git helps many people work on the same code without overwriting each other’s work.
---

### 4. Explain **distributed version control**.
* Distributed version control means every developer has a complete copy of the project (code + full history) on their own machine, so they can work independently and sync changes later.
---

### 5. What are the three states in Git?
* The **three Git states** correspond to:

1. **Working Directory** – where files are modified.
2. **Staging Area (Index)** – where files are added before committing.
3. **Repository (Local Repo)** – where files are permanently committed.

**Flow:**
`Working Directory → Staging Area → Repository`

---

## 2️⃣ Git Architecture & Internals

### 6. What is a **repository**?
> Repository = source code + complete change history.


---

### 7. Difference between:

   * Working directory
   * Staging area
   * Local repository

---

### 8. What is a **commit**?
- A commit is like saving your progress in a project, along with a message describing what changed.

---

### 9. What is a **SHA-1 hash**?

* A **SHA-1 hash** is like a **unique ID for each commit** in Git.
* It lets Git track and reference that specific snapshot of your project.
* A **SHA-1 hash** looks like a long string of 40 hexadecimal characters.

**Example:**

```
f5a3c2e7b9d8a1c4e6f7b8d9a0c1e2f3a4b5c6d7
```

* Each commit in Git gets a **unique SHA-1 hash** like this.
* It’s used to identify and reference commits.


---

### 10. What is HEAD?
* **HEAD usually points to the most recent commit** on the branch you are working on.
* When you make a new commit, **HEAD moves to that new commit**.

---

## 3️⃣ Branching & Merging

### 11. What is a branch in Git?
* A branch in Git is a separate line of development, allowing you to work on features, fixes, or experiments without affecting the main code.

---

### 12. Why are branches lightweight?
* Git **stores the real files once** in the repository.
* A **branch is just a pointer** to a commit.
* When you make a new branch, it **points to the same files** without copying them.

**Simple version:**

> Branch = a label pointing to existing code, not a duplicate of it.


---

### 13. Difference between:

* `merge`
* `rebase`
Exactly ✅

* **Merge:**

  * Git creates a **merge commit**.
  * `git log` shows **branches diverging and then coming together**.
  * History shows **all branch structure**.

* **Rebase:**

  * Git **moves your branch commits on top of another branch**.
  * `git log` shows a **straight, linear history**.
  * It looks like your commits happened **after the main branch**, even if they were developed separately.

**Simple way to remember:**

> Merge = keep history as it happened
> Rebase = rewrite history to look linear


---

### 14. What is a fast-forward merge?
* **Fast-forward merge** is just a **normal merge**, but in this case:

  * **Main branch hasn’t changed** since `feature1` was created.
  * Git **can simply move the main branch pointer forward** to include `feature1`’s commits.
  * **No new merge commit** is created.

**Simple way to remember:**

> It’s a merge where the target branch has no new changes, so Git just “fast-forwards” it.
> If the target branch has no new commits since the feature branch was created, Git can do a fast-forward merge, which keeps the history linear — similar to git rebase, but without rewriting commits.

---

### 15. What is a merge conflict?
* Classic scenario for a merge conflict.

**Step by step:**

1. You have **main** → two feature branches: **feature1** and **feature2**.
2. **feature1** makes changes to a file and is merged into **main**.
3. **feature2** also modified the **same part of that file**.
4. Now, when you try to merge **feature2** into **main**, Git sees **conflicting changes** and cannot decide automatically.

**Result:**

> Merge conflict occurs, and you need to manually resolve which changes to keep.

**Simple way to remember:**

> Conflict = “two different edits to the same line(s), Git needs your help.”

---

## 4️⃣ Git Commands (Conceptual)

### 16. Difference between:

* `git fetch`
* `git pull`

**Difference between `git fetch` and `git pull`:**

* **`git fetch`**: Downloads changes from the remote repository and updates **remote-tracking branches** without affecting your current branch. to get the changes run git merge or rebase.
* **`git pull`**: Downloads changes from the remote repository **and merges them into your current branch** automatically. This is git **fetch + merge**

---

### 17. What does `git clone` do?

---

### 18. Difference between:

* `git reset`
* `git revert`

* **`git reset`** = removes commits from history, as if they never happened.
* **`git revert`** = keeps history but adds a new commit that undoes the changes.


---

### 19. Soft vs Mixed vs Hard reset.

1. **Soft reset (`--soft`)**

   * Undo commit, **keep changes staged**
   * `git reset --soft HEAD~1` → last commit undone, ready to recommit

2. **Mixed reset (`--mixed`)** *(default)*

   * Undo commit, **unstage changes** but keep in files
   * `git reset HEAD~1` → last commit undone, changes in working files

3. **Hard reset (`--hard`)**

   * Undo commit, **remove changes completely**
   * `git reset --hard HEAD~1` → last commit undone, changes deleted from files

---

### 20. What is `git cherry-pick`?

* **Git merge:** brings **all commits** from the source branch into your current branch.
* **Git cherry-pick:** brings **only the specific commit(s)** you choose, identified by commit ID, into your current branch.

**Simple version:**

> Merge = grab everything, Cherry-pick = grab just one change.

---

## 5️⃣ Stash, Tags & History

### 21. What is `git stash`?
**`git stash`** temporarily **saves your uncommitted changes** so you can work on something else without committing them.

**Simple version:**

> Stash = hide your current changes and clean your working directory.

**Example:**

```bash
git stash       # save changes
git stash apply # bring changes back
git stash pop   # bring changes back and remove from stash
```

---

### 22. When do you use stash?
You use **`git stash`** when you want to **temporarily set aside your uncommitted changes** without committing them.

### Common scenarios:

1. **Switching branches quickly**

   * You’re working on something but need to check or fix another branch.

2. **Pulling updates safely**

   * Your working directory has changes, and you need to `git pull` or `merge` without conflicts.

3. **Experimenting**

   * You want to try something else but don’t want to commit unfinished work.

**Simple version:**

> Stash = “pause my current work, do something else, then come back later.”

---

### 23. What are Git tags?
**Git tags** are **markers or labels** used to mark a specific commit, usually for **releases or important points** in history.

**Simple version:**

> Tag = a name you give to a commit to remember it, like “v1.0” or “release-2025”.

**Example:**

```bash
git tag v1.0       # create a tag on the current commit
git show v1.0      # see details of the tagged commit
```

---

### 24. Difference between:

* **Lightweight tag:** just a name for a commit, no extra info, e.g., `git tag v1.0`
* **Annotated tag:** stores author, date, and message, e.g., `git tag -a v1.0 -m "Version 1 release"`

---

### 25. How do you view commit history?
**Basic command:**

```bash
git log
```

**Simple version:**

> Shows all commits, with commit ID, author, date, and message.

**Some useful variations:**

* `git log --oneline` → shows each commit in **one line** (short form)
* `git log --graph --oneline --all` → shows a **branch graph** with all commits
* `git log -p` → shows **diffs for each commit**

---

## 6️⃣ Remote Repositories

### 26. What is `origin`?
**`origin`** is the default name Git gives to the **remote repository you cloned from**.

**Simple version:**

> `origin` = the remote location that stores your code, usually on GitHub, GitLab, or another server.

**Example:**

```bash
git push origin main   # push changes to the main branch on the remote
git fetch origin       # fetch updates from the remote
```

---

### 27. How do you add multiple remotes?

You can add multiple remotes in Git using the **`git remote add`** command with different names.

**Example:**

```bash
git remote add origin https://github.com/user/repo.git
git remote add backup https://gitlab.com/user/repo.git
```

* Now you have **two remotes**: `origin` and `backup`.
* You can push or fetch to a specific remote:

```bash
git push origin main    # push to GitHub
git push backup main    # push to GitLab
```

**Simple version:**

> Each remote gets a unique name, and you can use them independently.

---

### 28. What is `upstream`?

In Git, **`upstream`** usually refers to the **remote branch that your local branch is tracking**.

**Simple version:**

> Upstream = the branch on the remote repository that your local branch will pull from or push to by default.

**Example:**

```bash
git branch -u origin/main    # set upstream of current branch to origin/main
git pull                     # pulls changes from upstream branch
```

**Memory trick:**

> Upstream = where your branch “flows from” on the remote.

---

### 29. How do you sync a forked repository?

To **sync a forked repository** with the original (upstream) repository, you follow these steps:

1. **Add the upstream remote** (only once):

```bash
git remote add upstream https://github.com/original/repo.git
```

2. **Fetch changes from upstream**:

```bash
git fetch upstream
```

3. **Merge or rebase changes into your local branch**:

```bash
git checkout main
git merge upstream/main   # or git rebase upstream/main
```

4. **Push the updates to your fork**:

```bash
git push origin main
```

**Simple version:**

> Fetch changes from the original repo, merge them locally, then push to your fork.

---

### 30. What happens during `git push`?
* During git push, Git uploads your local commits from your current branch to a remote repository.
---

## 7️⃣ Git Hooks & Configuration

### 31. What are Git hooks?

**Git hooks** are **scripts that Git automatically runs before or after certain events** in a repository, like committing or pushing.

**Simple version:**

> Hooks = automated actions Git can perform at specific events.

**Types / Examples:**

* **Pre-commit:** runs before a commit is created (e.g., check code style)
* **Post-commit:** runs after a commit is made (e.g., send notifications)
* **Pre-push:** runs before pushing to remote (e.g., run tests)

**Location:**

* Hooks are stored in the `.git/hooks/` directory of your repository.

**Example:**

```bash
.git/hooks/pre-commit   # script that runs before every commit
```

---

### 32. Client-side vs Server-side hooks.

Here’s a **simpler version**:

* **Client-side hooks:** run on your **local machine**, e.g., check code before committing or pushing.
* **Server-side hooks:** run on the **remote repository**, e.g., reject bad pushes or trigger CI/CD.

**Memory trick:**

> Client-side = local checks, Server-side = remote rules.

---

### 33. Use cases for pre-commit hooks.

**Pre-commit hooks** run **before a commit is made** and are used to **catch issues early**.

### Common use cases:

1. **Code formatting / linting**

   * Automatically check and enforce code style (e.g., ESLint, Prettier).

2. **Run tests**

   * Prevent committing code that breaks tests.

3. **Check commit messages**

   * Ensure commit messages follow a required format.

4. **Scan for secrets**

   * Prevent accidentally committing passwords or API keys.

**Simple version:**

> Pre-commit hooks = automatic checks before saving a commit.

---

### 34. What is `.gitignore`?

---

### 35. Can ignored files be committed?

By default ✅, **ignored files (listed in `.gitignore`) are not committed**.

* Git **skips these files** when you run `git add` or `git commit`.
* **However**, if a file was already tracked before being added to `.gitignore`, it **can still be committed** unless you remove it from tracking:

```bash
git rm --cached filename
```

**Simple version:**

> Ignored files = normally not committed, but already tracked files can still be committed.

---

# 🔹 GitHub – Theoretical Interview Questions

## 8️⃣ GitHub Basics

### 36. What is GitHub?

---

### 37. Difference between:

* Git
* GitHub


---

### 38. What is a GitHub repository?

---

### 39. Public vs Private repositories.

---

### 40. What is a GitHub organization?

Yes ✅, that’s a good way to think about it.

* A **GitHub organization** is like a **tenant/account** that “owns” repositories, teams, and permissions.
* Individuals or teams within the organization **don’t own the repos individually**; the organization does.

**Simple version:**

> Organization = a GitHub account for a team or company to own and manage repos.

---

## 9️⃣ Pull Requests & Collaboration

### 41. What is a Pull Request (PR)?
* A Pull Request (PR) is a request to merge changes from one branch into another (usually from a feature branch to the main branch) on platforms like GitHub.
---

### 42. PR vs direct push to main branch.

---

### 43. What is a code review?
* A code review is the process where someone else (or a team) examines your code before it is merged into the main branch.
---

### 44. How do you resolve PR conflicts?

**Step by step simplified:**

1. **Fetch latest changes** from the target branch.
2. **Merge or rebase** your feature branch with the target branch.
3. **Git identifies conflicts** in files.
4. **Manually edit** the conflicted files to resolve differences.
5. **Stage the resolved files** using `git add`.
6. **Commit** the resolution (or continue rebase with `git rebase --continue`).
7. **Push** your branch to update the PR.

**Simple memory:**

> Fetch → Merge/Rebase → Fix → Add → Commit → Push

---

### 45. What is squash merge?

* A **squash merge** in Git is when you **combine all commits from a feature branch into a single commit** before merging into the target branch.

**Simple version:**

> Squash merge = “squash” multiple commits into one clean commit for the main branch.

**Key points:**

* Keeps the main branch **history clean and linear**.
* Useful for merging **feature branches with many small commits**.

**Example:**

```bash
git checkout main
git merge --squash feature-branch
git commit -m "Add feature X"
```

**Memory trick:**

> Many small commits → one big commit in main.

---

## 🔟 Branching Strategies

### 46. What branching strategies have you used?

Here are some **common Git branching strategies** often used in projects:

1. **Git Flow**

   * Uses `main` (or `master`) for production, `develop` for integration, and feature/hotfix/release branches.
   * Good for structured release cycles.

2. **GitHub Flow**

   * Simple: `main` branch always deployable, feature branches for development, merged via PRs.
   * Popular for continuous deployment.

3. **GitLab Flow / Environment-based Flow**

   * Combines feature branches and environment branches (staging, production).
   * Useful for CI/CD pipelines.

4. **Trunk-Based Development**

   * Developers commit **directly to main (trunk)** frequently.
   * Feature toggles are used instead of long-lived branches.

**Simple version:**

> Git Flow = structured releases, GitHub Flow = simple PR-based, Trunk-Based = frequent commits to main.

I can also give a **diagram showing how each flow works** if you want.

---

### 47. Explain:

* Git Flow
* Trunk-based development


---

### 48. When would you avoid Git Flow?
* Avoid Git Flow when you need fast, frequent releases or have a small team.
---

### 49. What is a release branch?
* Release branch = “almost ready” branch for production.
---

### 50. How do you manage hotfixes?
* Hotfix = create branch from main, fix, merge to main and develop, tag release.
---

## 1️⃣1️⃣ GitHub Security & Access Control

### 51. How do you manage access in GitHub?

In GitHub, **access management** is done using **permissions, roles, and teams**.

### Key ways to manage access:

1. **Repository-level permissions**

   * Roles: **Read, Write, Maintain, Admin**
   * Assign collaborators to control who can view, push, or manage the repo.

2. **Organization teams**

   * Group users into **teams** and assign team-level access to multiple repositories.
   * Easier to manage large groups.

3. **Branch protection rules**

   * Restrict who can **push or merge** to certain branches (like `main`).
   * Enable **required reviews, status checks, or PR approvals**.

4. **GitHub Actions / Secrets access**

   * Control which workflows can access **repository secrets or environments**.

5. **Outside collaborators**

   * Give access to specific users **without adding them to the organization**.

**Simple version:**

> Use roles, teams, branch protection, and secrets to control who can do what on GitHub.

---

### 52. What are GitHub teams?
* GitHub teams are groups of users within a GitHub organization that share the same permissions for repositories.
* Teams = a way to manage access and collaboration for multiple people at once.
---

### 53. How do branch protection rules work?

**Branch protection rules** in GitHub are settings that **control how changes can be made to a branch**, usually `main` or `master`.

**Simple version:**

> Branch protection = rules that prevent direct pushes, enforce reviews, or require checks before merging.

### Key features:

1. **Require pull request reviews**

   * Changes must be approved by one or more reviewers before merging.

2. **Require status checks to pass**

   * CI/CD tests must pass before merging.

3. **Restrict who can push**

   * Only certain users or teams can push to the branch.

4. **Require signed commits**

   * Ensures commits are verified with GPG keys.

5. **Prevent force pushes and deletions**

   * Keeps branch history safe.

**Example workflow:**

* Developers create a feature branch → open PR → tests run → reviewers approve → merge allowed.

**Memory trick:**

> Branch protection = “safety rules” for critical branches.

---

### 54. What is CODEOWNERS?

**CODEOWNERS** is a special file in a GitHub repository that **defines who is responsible for specific files or folders**.

**Simple version:**

> CODEOWNERS = automatically assign reviewers for code changes.

### Key points:

* When someone opens a **pull request**, GitHub automatically requests a review from the **designated owners**.
* You can assign **individuals or teams** to specific paths.
* Helps enforce **accountability and code review policies**.

**Example `CODEOWNERS` file:**

```
# All files in the repo
*       @org/team-name

# Specific folder
/docs/  @doc-team

# Specific file
README.md @alice
```

**Memory trick:**

> CODEOWNERS = “who owns which code” for automatic review assignments.

---

### 55. How do you prevent force pushes?
* Use branch protection → enable “prevent force pushes” to safeguard main branches.
---

## 1️⃣2️⃣ GitHub Actions (High-Level)

### 56. What is GitHub Actions?
* GitHub Actions is GitHub’s built-in CI/CD and automation platform that lets you automate workflows like building, testing, and deploying code.
---

### 57. Difference between:

* Jenkins vs GitHub Actions

---

### 58. What is a workflow?
* Workflow in GitHub Actions is a defined process of automated steps that run in response to specific events (like push, pull request, or schedule).
---

### 59. What is a runner?
* Runner in GitHub Actions is the server or machine that executes your workflow jobs.
---

### 60. Self-hosted vs GitHub-hosted runners.

---



## 🔥 Scenario 6: Secrets Leaked

**Q:**
A secret key was committed to GitHub. What steps do you take?

**Expected actions:**

* Rotate secret immediately
* Remove from history
* Add secret scanning
* Update `.gitignore`



---

## 🔥 Scenario 8: CI Failing on PR

**Q:**
PR is approved but CI is failing. Can it be merged?

**Expected answer:**

* No (if checks enforced)
* Fix pipeline
* Re-run jobs

---



---

# 🔹 Advanced Git & GitHub Questions (4+ Years Level)

### 61. How does Git store data internally?
* Git = takes snapshots of your files at each commit and links them together.
---

### 62. What is reflog?

Exactly ✅

* **`git log`** shows the **commits in the visible history** of your branch.
* **`git reflog`** shows **all changes to HEAD or branch pointers**, including commits, resets, rebases, and checkouts—even if they are no longer in the branch history.

**Simple version:**

> Log = what’s in history, Reflog = **how HEAD moved over time**.

* git log = what exists now
* git reflog = what existed before
---

### 63. How do you recover a deleted commit?

You can **recover a deleted commit** in Git using **`git reflog`**, because reflog tracks all changes to HEAD, even for commits no longer in branch history.

### Steps:

1. **View the reflog** to find the deleted commit:

```bash
git reflog
```

* Look for the commit hash you want to recover.

2. **Create a new branch** pointing to that commit:

```bash
git checkout -b recover-branch <commit-hash>
```

3. **Or reset your branch** to that commit:

```bash
git reset --hard <commit-hash>
```

**Simple version:**

> Use reflog to find the commit, then checkout or reset to restore it.

**Memory trick:**

> Reflog = safety net for lost commits.

---

### 64. What is shallow clone?

A **shallow clone** in Git is a clone of a repository that **only includes the latest commits**, not the full history.

**Simple version:**

> Shallow clone = copy of the repo with **limited history** to save time and space.

**Key points:**

* Useful for **large repositories** where full history isn’t needed.
* Speeds up cloning and uses less disk space.

**Example:**

```bash
git clone --depth 1 https://github.com/user/repo.git
```

* `--depth 1` → only fetches the latest commit.

**Memory trick:**

> Shallow = “just the tip of the repo, not the full history.”

---

### 65. How do you sign commits?
* You can **sign commits** in Git to **verify that they were made by you** using GPG or SSH keys.
---

### 66. What is GitHub secret scanning?
* GitHub secret scanning is a built in security feature that detects sensitive data (like API keys, passwords, or tokens) in your repository.
* For public repos, it’s automatically enabled.
* For private repos, it can be enabled in repository settings.
---

---

### 68. How do you audit GitHub access?
* GitHub provides a built-in audit feature (for organizations) that lets you see who has access, what actions they performed, and track changes.
---

### 69. How do you handle submodules?

**Git submodules** allow you to **include one Git repository inside another** as a subdirectory.

### How to handle submodules:

1. **Add a submodule**

```bash
git submodule add https://github.com/user/subrepo.git path/to/subrepo
```

2. **Initialize and update submodules** (after cloning a repo with submodules)

```bash
git submodule init
git submodule update
# Or combined:
git submodule update --init --recursive
```

3. **Make changes in a submodule**

* Enter the submodule folder, make commits, push to its remote.

```bash
cd path/to/subrepo
git add .
git commit -m "Update submodule code"
git push
```

4. **Update the main repository to point to the latest submodule commit**

```bash
cd main-repo
git add path/to/subrepo
git commit -m "Update submodule reference"
git push
```

5. **Remove a submodule** (optional)

```bash
git submodule deinit -f path/to/subrepo
git rm path/to/subrepo
rm -rf .git/modules/path/to/subrepo
```

**Simple version:**

> Submodule = a repo inside a repo; you commit submodule changes separately and update the main repo to track them.

**Memory trick:**

> Main repo tracks **submodule commits**, not its full history.

---

### 70. How do you manage GitOps using GitHub?
* GitOps with GitHub = store all configs in Git, use PRs to change them, and automatically deploy via CI/CD.
---
