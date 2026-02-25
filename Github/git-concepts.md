# Git Concepts

A practical reference to the Git concepts you'll use while building these JavaScript projects. Each concept explains **what** it is, **why** it matters in real teams, and **how** it applies to your work here.

---

## 1. What is Git?

Git is a **distributed version control system** — it tracks every change you make to your code, who made it, and when. Think of it as an unlimited undo history that also lets multiple people work on the same codebase without overwriting each other's work.

**Why it matters:**
- Every software company uses version control. Git is the industry standard.
- You can experiment freely — if something breaks, you roll back.
- It creates a clear history of what changed and why.

---

## 2. Repository (Repo)

A repository is a project folder tracked by Git. It contains your code files plus a hidden `.git/` folder that stores the entire version history.

**Two types:**
- **Local repository** — lives on your machine
- **Remote repository** — lives on GitHub (or GitLab, Bitbucket, etc.) and acts as the shared source of truth

**In this project:**
- The remote repo on GitHub holds the starter code for all 5 projects.
- You'll clone it to create your own local repo, then push to your own remote.

---

## 3. Clone

Cloning creates a full copy of a remote repository on your local machine — all files, all branches, all history.

```bash
git clone <repository-url>
```

**Why clone instead of just downloading?**
- A downloaded ZIP has no Git history — you lose version tracking.
- Cloning preserves the connection to the remote, the branch structure, and all commit history.
- You can immediately start making commits and pushing changes.

---

## 4. Commits

A commit is a **snapshot** of your code at a point in time. Each commit has:
- A unique hash (ID)
- A message describing what changed
- A timestamp and author

```bash
git add index.html styles.css       # Stage specific files
git commit -m "Add header layout and base styles"
```

**Best practices:**
- Commit often — small, logical units of work
- Write clear messages: what you did and why (not "fixed stuff")
- Each commit should leave the code in a working state

**Why this matters in industry:**
- Code reviews happen commit-by-commit
- If a bug is introduced, you can pinpoint exactly which commit caused it
- Clear commits make it easy for teammates to understand your changes

---

## 5. Branches

A branch is a parallel line of development. You create a branch to work on something without affecting the main code. When you're done, you merge it back.

```
main ──────────────────────────────────
        \                    /
         feature/search ────
```

**Why branches matter:**
- In a team of 10 developers, everyone works on different features simultaneously
- No one's half-finished work breaks the shared codebase
- Features can be reviewed and tested before being merged

**Branch types you'll use:**

| Branch | Purpose | Who merges into it |
|--------|---------|-------------------|
| `main` | Production-ready code. Always stable. | Only from `dev` after testing |
| `dev` | Integration branch. All features merge here first. | From feature branches |
| `feature/*` | Individual features or tasks | Developer working on it |

---

## 6. The Branching Strategy (Why main → dev → feature)

### `main` branch
- Represents the **latest stable version** of your project
- In a company, this is what gets deployed to production (what users see)
- **Never commit directly to main** — changes only arrive via merges from `dev`

### `dev` branch (development)
- The **integration branch** where all features come together
- Created from `main` at the start of the project
- Acts as a staging area — lets you test how features work together before promoting to `main`

### `feature/*` branches
- Created from `dev` for each specific piece of work
- Named descriptively: `feature/search-bar`, `feature/cart-page`, `feature/dark-mode`
- Merged back into `dev` when complete

**Why this 3-tier approach?**

In real companies:
1. A developer picks up a task (e.g., "Add search functionality")
2. They create `feature/search` from `dev`
3. They build and commit on that branch
4. They open a **Pull Request** to merge into `dev`
5. Teammates review the code
6. After approval, it merges into `dev`
7. When `dev` is stable and tested, it merges into `main` for release

This means:
- `main` is **always deployable** — no half-finished features
- `dev` is where you catch integration issues early
- Feature branches keep work isolated and reviewable

---

## 7. Merging

Merging combines the changes from one branch into another.

```bash
git checkout dev
git merge feature/search-bar
```

This takes all the commits from `feature/search-bar` and applies them to `dev`.

**Merge conflicts** happen when two branches modify the same lines of a file. Git can't decide which version to keep, so it asks you to resolve it manually. This is normal — not an error.

---

## 8. Remote & Push / Pull

- **`git push`** — sends your local commits to the remote (GitHub)
- **`git pull`** — fetches and merges remote changes into your local branch

```bash
git push origin dev          # Push your dev branch to GitHub
git pull origin dev          # Get latest changes from GitHub
```

**Why push regularly?**
- Your code is backed up on GitHub
- Teammates can see and review your work
- If your laptop dies, your code is safe

---

## 9. Pull Requests (PRs)

A Pull Request is a **request to merge** one branch into another on GitHub. It's not a Git command — it's a GitHub feature built on top of Git.

**Why PRs matter:**
- They're the standard way code gets reviewed in every tech company
- Reviewers can comment on specific lines, suggest changes, request fixes
- PRs create a documented history of why each change was made
- Automated tests can run before merging to catch bugs

**In this project:**
- When you finish a feature branch, you'll create a PR to merge it into `dev`
- This builds the habit of code review and structured merging

---

## 10. .gitignore

A `.gitignore` file tells Git which files/folders to **not track**. Common entries:

```
node_modules/
dist/
.env
.DS_Store
```

**Why?**
- `node_modules/` can be 500MB+ — it's regenerated from `package.json` via `npm install`
- `.env` files contain secrets (API keys, passwords) — never commit these
- Build output (`dist/`) is generated from source code — no need to version it

---

## 11. Staging Area (Index)

Before committing, files go through a **staging area**. This lets you control exactly what goes into each commit.

```bash
git add header.js        # Stage only this file
git add .                # Stage everything (use carefully)
git status               # See what's staged vs unstaged
```

**Why not just commit everything?**
- You might have changes in 5 files but only 2 are related to your current task
- Staging lets you create focused, logical commits
- Keeps your history clean and reviewable

---

## 12. Git Log & History

```bash
git log --oneline        # Compact commit history
git log --graph          # Visual branch/merge history
git diff                 # See unstaged changes
git diff --staged        # See staged changes
```

**Why this matters:**
- When debugging, you can trace back through history to find when something broke
- `git blame <file>` shows who last changed each line (useful in teams)

---

## 13. Common Git Commands — Quick Reference

| Command | What it does |
|---------|-------------|
| `git clone <url>` | Copy a remote repo to your machine |
| `git status` | See what's changed, staged, untracked |
| `git add <file>` | Stage a file for commit |
| `git commit -m "message"` | Save a snapshot with a message |
| `git branch` | List all local branches |
| `git branch <name>` | Create a new branch |
| `git checkout <branch>` | Switch to a branch |
| `git checkout -b <name>` | Create and switch in one command |
| `git merge <branch>` | Merge a branch into current branch |
| `git push origin <branch>` | Push branch to GitHub |
| `git pull origin <branch>` | Pull latest from GitHub |
| `git log --oneline` | View commit history (compact) |
| `git diff` | See uncommitted changes |
| `git stash` | Temporarily shelve changes |
| `git stash pop` | Restore shelved changes |

---

## 14. Key Terminology Glossary

| Term | Definition |
|------|-----------|
| **HEAD** | Pointer to the current commit/branch you're on |
| **Origin** | The default name for the remote repository |
| **Upstream** | The original repo you forked/cloned from |
| **Hash** | The unique SHA identifier for each commit (e.g., `a3f2c91`) |
| **Working directory** | Your actual project files on disk |
| **Staging area** | The "prep zone" between working directory and a commit |
| **Fast-forward merge** | A merge where Git just moves the pointer forward (no conflicts) |
| **Merge conflict** | When two branches edit the same lines and Git needs your help to resolve |
| **Detached HEAD** | When you checkout a specific commit instead of a branch (read-only exploring) |
