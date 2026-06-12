# Git Basics — Core Commands Used in the Lab Series

This document covers the Git commands used across all five labs in this series. Each command includes what it does, why it matters, and how it fits into the lab workflow.

---

## Why Git in a Lab Environment?

Every lab in this series involves writing scripts, configuration files, and documentation that evolve as the lab progresses. Git provides three things that matter in this context:

- **Version history** — every change is recorded with a message explaining what was done and why
- **Accountability** — commits are timestamped and tied to your identity, which is exactly what a portfolio reviewer wants to see
- **Recovery** — if something breaks, you can compare the current state to what was working before

In a production environment, infrastructure-as-code (IaC) scripts, runbooks, and configuration files all live in version control for the same reasons. Using Git in the labs reflects that professional standard.

---

## Initial One-Time Setup

Before using Git for the first time on a new machine, set your identity. This information appears in every commit you make.

```bash
git config --global user.name "Sammatha Adams"
git config --global user.email "sammdion@gmail.com"
```

**Why:** Git attaches a name and email to every commit. Without this, commits show up with no author, which looks unprofessional on a public GitHub repo.

---

## Core Commands

### `git init`

Initializes a new Git repository in the current directory.

```bash
git init
```

**What it does:** Creates a hidden `.git` folder that contains the entire version history, configuration, and tracking metadata for the project.

**Why:** This is the first command run in every lab. It turns a plain folder into a tracked repository. Nothing is committed yet — it just enables tracking from this point forward.

**When to use it:** Once, at the start of each lab, before any files are created or scripts are written.

---

### `git status`

Shows the current state of the working directory — what's been changed, what's staged, and what Git doesn't know about yet.

```bash
git status
```

**What it does:** Lists files in three categories:
- **Untracked** — new files Git hasn't seen before
- **Modified** — files Git knows about that have changed since the last commit
- **Staged** — files queued up and ready to be committed

**Why:** Running `git status` before every `git add` or `git commit` is a good habit. It prevents accidentally staging files you didn't mean to include and confirms the working directory is in the state you expect.

**When to use it:** Frequently — before staging, before committing, and any time you want to confirm what's happening.

---

### `git add`

Stages files for the next commit — tells Git which changes to include.

```bash
# Stage a specific file
git add scripts/deploy-ad.ps1

# Stage everything in the current directory
git add .
```

**What it does:** Moves file changes from the working directory into the *staging area* (also called the index). Only staged changes are included in the next commit.

**Why:** Staging lets you be deliberate about what goes into each commit. In the labs, `git add .` is used to capture the full project baseline on the first commit. On later commits, specific files are staged so that the commit message accurately describes what changed.

**When to use it:** After creating or modifying files you're ready to save into history.

---

### `git commit`

Records the staged changes into the repository's history with a descriptive message.

```bash
git commit -m "feat: complete active directory core deployment script with gpo injection automation"
```

**What it does:** Takes everything in the staging area and saves it as a permanent snapshot in the repository's history. The `-m` flag lets you write the commit message inline.

**Why:** The commit message is a record of *what* changed and *why*. Good commit messages make the history readable — a portfolio reviewer looking at your commit log should be able to understand the progression of the lab without opening a single file.

**Commit message conventions used in this series:**

| Prefix | Meaning |
|---|---|
| `feat:` | A new feature, script, or capability was added |
| `docs:` | Documentation only — README, SOP, comments |
| `fix:` | A bug or error was corrected |
| `chore:` | Housekeeping — folder setup, cleanup, renaming |
| `init:` | First commit, project scaffolding |

**When to use it:** After staging the files for a logical unit of work — a completed script, a finished section of the README, a successful deployment step.

---

### `git log`

Shows the commit history for the current repository.

```bash
# Full log with messages, authors, and dates
git log

# Compact one-line-per-commit view
git log --oneline
```

**What it does:** Prints a chronological list of all commits — most recent first — with the commit hash, author, date, and message.

**Why:** `git log --oneline` is the fastest way to verify that your commits look right before pushing to GitHub. It's also useful for showing the progression of a lab in a screenshot for the portfolio README.

**When to use it:** Before pushing, and any time you want to review what's been committed.

---

### `git remote add origin`

Links your local repository to a remote repository on GitHub.

```bash
git remote add origin https://github.com/sammathaadams/active-directory-lab1.git
```

**What it does:** Registers a remote connection named `origin` pointing to the GitHub repo URL. `origin` is the conventional name for the primary remote — it's used by default in `git push` and `git pull`.

**Why:** A local Git repo has no connection to GitHub until you add a remote. This command is run once per lab after creating the repo on GitHub.

**When to use it:** Once, after creating the repo on GitHub and before the first push.

---

### `git push`

Uploads your local commits to GitHub.

```bash
# First push — sets the upstream branch
git push -u origin main

# Subsequent pushes
git push
```

**What it does:** Sends all commits that exist locally but not on GitHub up to the remote repository. The `-u origin main` flag on the first push sets the default upstream so that future `git push` calls don't need arguments.

**Why:** This is what makes the work visible on GitHub. Until you push, the repo on GitHub is either empty or behind the local copy. Pushing after each major milestone — completing a deployment step, finishing a script, committing the README — keeps the remote in sync and creates a visible, timestamped record of progress.

**When to use it:** After committing a meaningful unit of work, at minimum once per lab before cleanup.

---

### `git branch -M main`

Renames the default branch to `main`.

```bash
git branch -M main
```

**What it does:** Renames the current branch (typically `master` on older Git installs) to `main`.

**Why:** GitHub's default branch name is `main`. Renaming locally before pushing avoids a mismatch that can cause the first push to create a second branch instead of updating the expected one.

**When to use it:** Once per repo, right after `git init` and before the first `git push`.

---

## Typical Lab Git Workflow

This is the sequence used in every lab from start to finish:

```bash
# 1. Create and enter the project folder
mkdir active-directory-lab1
cd active-directory-lab1

# 2. Initialize the repo and rename the branch
git init
git branch -M main

# 3. Create baseline files
echo "# Active Directory Lab 1" > README.md
mkdir scripts

# 4. Stage and commit the baseline
git add .
git commit -m "init: project environment baseline setup"

# 5. Link to GitHub (after creating the repo on github.com)
git remote add origin https://github.com/sammathaadams/active-directory-lab1.git

# 6. Push
git push -u origin main

# --- work happens: scripts written, screenshots taken, README built ---

# 7. Stage and commit completed work
git add scripts/deploy-ad.ps1
git commit -m "feat: complete active directory core deployment script with gpo injection automation"

# 8. Push the final state
git push
```

---

## Quick Reference

| Command | What It Does |
|---|---|
| `git init` | Initialize a new repo in the current folder |
| `git config --global user.name / user.email` | Set your identity for all commits |
| `git status` | Show what's changed, staged, or untracked |
| `git add .` | Stage all changes |
| `git add <file>` | Stage a specific file |
| `git commit -m "message"` | Save staged changes to history |
| `git log --oneline` | View commit history in compact form |
| `git branch -M main` | Rename current branch to main |
| `git remote add origin <url>` | Link local repo to GitHub |
| `git push -u origin main` | First push — sets upstream |
| `git push` | Push subsequent commits |
