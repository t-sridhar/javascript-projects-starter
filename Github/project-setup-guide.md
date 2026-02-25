# Project Setup Guide

Step-by-step guide to clone this project, set up your own GitHub repository, and follow a professional branching workflow. Each step includes the **command**, **what it does**, and **why**.

---

## Prerequisites

- [Git](https://git-scm.com/) installed on your machine
- A [GitHub](https://github.com/) account
- [Node.js](https://nodejs.org/) installed (for the local dev server)
- A terminal (Terminal on Mac, Git Bash or WSL on Windows)

Verify your setup:

```bash
git --version       # Should output something like: git version 2.x.x
node --version      # Should output something like: v20.x.x
```

---

## Part 1: Clone the Starter Repository

### Step 1 — Clone the repo

```bash
git clone https://github.com/t-sridhar/javascript-projects-starter.git
```

**What this does:** Creates a folder called `javascript-projects-starter/` on your machine with all 5 projects inside it. It also copies the full Git history and connects your local repo to the GitHub remote.

**Why clone instead of downloading the ZIP?**
Cloning preserves the Git connection. You get version history, branch tracking, and the ability to push/pull — a ZIP gives you none of that.

### Step 2 — Navigate into the project

```bash
cd javascript-projects-starter
```

### Step 3 — Explore the structure

```bash
ls
```

You should see:

```
01-skyvoyage/
02-urbancart/
03-devconnect/
04-fooddash/
05-tripplanner/
CONCEPTS-COVERED.md
Github/
README.md
```

Each project folder contains a `starter/` directory — that's where you'll write your code.

---

## Part 2: Create Your Own GitHub Repository

Right now, your clone points to the original starter repo. You need your **own repo** on GitHub so you can push your work.

### Step 4 — Create a new repo on GitHub

1. Go to [github.com/new](https://github.com/new)
2. Name it something meaningful: `javascript-projects` (or `js-learning-projects`, etc.)
3. **Do NOT** check "Add a README" or ".gitignore" — your clone already has these
4. Set visibility to **Private** (your work, your code)
5. Click **Create repository**

GitHub will show you a page with setup commands. You'll use the "push an existing repository" option.

### Step 5 — Change the remote to point to YOUR repo

The clone currently points to the original starter repo. Replace it with your own:

```bash
git remote set-url origin https://github.com/YOUR_USERNAME/javascript-projects.git
```

**What this does:** Changes where `git push` and `git pull` send/receive code. Instead of the instructor's repo, it now points to yours.

**Verify it worked:**

```bash
git remote -v
```

You should see your GitHub URL listed for both `fetch` and `push`.

### Step 6 — Push the starter code to your repo

```bash
git push -u origin main
```

**What `-u origin main` means:**
- `-u` (or `--set-upstream`) tells Git to remember that your local `main` tracks `origin/main`
- After this, you can just type `git push` without specifying the branch every time

**Why push now?**
- Your code is now backed up on GitHub
- You have a starting point to compare against as you build
- If you need to start over, you can always re-clone from your own repo

---

## Part 3: Set Up the Branching Workflow

Now set up the branching structure used by professional development teams.

### Step 7 — Create the `dev` branch

```bash
git checkout -b dev
```

**What this does:** Creates a new branch called `dev` based on `main` and switches to it. At this point, `dev` and `main` are identical.

**Why create `dev`?**

In industry, `main` is the **production branch** — it represents the live, deployed version of your application. You never want half-finished features on `main`.

`dev` acts as the **integration branch** — all your feature work merges here first. Once everything in `dev` is tested and stable, it gets merged into `main`. This two-step process ensures `main` is always in a deployable state.

```
main  ← only receives tested, stable code
  ↑
dev   ← all features merge here first
  ↑
feature branches ← individual pieces of work
```

### Step 8 — Push `dev` to GitHub

```bash
git push -u origin dev
```

Now both `main` and `dev` exist on GitHub.

---

## Part 4: Working on Features (Daily Workflow)

This is the workflow you'll follow every time you work on a new feature or task.

### Step 9 — Create a feature branch

Always start from `dev`:

```bash
git checkout dev                      # Make sure you're on dev
git pull origin dev                   # Get latest changes (important in teams)
git checkout -b feature/search-bar    # Create feature branch from dev
```

**Naming conventions for feature branches:**

| Pattern | Example | When to use |
|---------|---------|-------------|
| `feature/<name>` | `feature/search-bar` | New functionality |
| `fix/<name>` | `fix/cart-total-bug` | Bug fixes |
| `style/<name>` | `style/header-responsive` | CSS/UI changes |

**Why branch from `dev` and not `main`?**

`dev` has the latest integrated work from all feature branches. If you branch from `main`, you'd miss features that other teammates (or you, on other branches) have already merged into `dev`. Your feature branch would be based on outdated code.

### Step 10 — Build your feature

Write code, test it, commit as you go:

```bash
# After making changes to search.js and search.css:
git add src/search.js styles/search.css
git commit -m "Add search input with debounced filtering"

# After more work:
git add src/search.js
git commit -m "Display search results with highlighting"
```

**Commit tips:**
- **Commit often** — after each logical piece of work, not at the end of the day
- **Be specific** — "Add debounced search" is better than "update search"
- **Stage intentionally** — use `git add <file>` instead of `git add .` to avoid accidentally committing unrelated changes

### Step 11 — Push your feature branch

```bash
git push -u origin feature/search-bar
```

**Why push feature branches?**
- Backup — your work exists on GitHub even if your computer breaks
- Visibility — in a team, others can see what you're working on
- Pull Requests — you need the branch on GitHub to open a PR

---

## Part 5: Merging Your Feature into `dev`

When your feature is complete and working:

### Option A — Merge locally (solo workflow)

```bash
git checkout dev                          # Switch to dev
git pull origin dev                       # Get any updates
git merge feature/search-bar              # Merge your feature into dev
git push origin dev                       # Push the updated dev to GitHub
```

### Option B — Pull Request on GitHub (team workflow, recommended)

1. Push your feature branch (Step 11)
2. Go to your repo on GitHub
3. Click **"Compare & pull request"** (GitHub usually shows a banner)
4. Set:
   - **Base:** `dev`
   - **Compare:** `feature/search-bar`
5. Write a description of what you built and why
6. Click **"Create pull request"**
7. Review the changes yourself (or have someone review)
8. Click **"Merge pull request"**

**Why use Pull Requests even when working solo?**
- Builds the habit for when you join a team
- Creates a documented history of each feature
- Forces you to review your own code before merging
- GitHub shows a clean diff of all changes

### Step 12 — Clean up the feature branch

After merging:

```bash
git checkout dev
git pull origin dev                            # Get the merge
git branch -d feature/search-bar               # Delete local branch
git push origin --delete feature/search-bar     # Delete remote branch
```

**Why delete merged branches?**
- Keeps your branch list clean — only active work appears
- In a team repo with 20 developers, undeleted branches pile up fast
- The commits are preserved in `dev` — the branch name was just a pointer

---

## Part 6: Promoting `dev` to `main`

When a major milestone is complete (e.g., you finished the entire search feature with all edge cases handled):

```bash
git checkout main
git pull origin main          # Get latest main
git merge dev                 # Merge dev into main
git push origin main          # Push updated main to GitHub
```

**When to merge `dev` into `main`:**
- A complete feature or set of features is working and tested
- At the end of a project milestone
- NOT after every small feature — `main` should represent stable checkpoints

**In industry, this is often:**
- A "release" — version 1.0, 1.1, etc.
- Triggered by a team lead or through an automated CI/CD pipeline
- Sometimes accompanied by a "tag" (`git tag v1.0`) to mark the release point

---

## Part 7: Complete Workflow Example

Here's a full example of building the **menu page** for the FoodDash project:

```bash
# 1. Start from dev
git checkout dev
git pull origin dev

# 2. Create feature branch
git checkout -b feature/menu-page

# 3. Build the HTML structure
# ... write code in menu.html ...
git add menu.html
git commit -m "Add menu page HTML structure with grid layout"

# 4. Add the styling
# ... write CSS ...
git add styles/pages/menu.css
git commit -m "Style menu grid with cards and responsive breakpoints"

# 5. Add JavaScript functionality
# ... write JS for fetching and rendering meals ...
git add src/pages/menuPage.js
git commit -m "Fetch meals from API and render as cards"

# 6. Add search and filtering
# ... more JS ...
git add src/pages/menuPage.js
git commit -m "Add category filter and search with debouncing"

# 7. Push the feature branch
git push -u origin feature/menu-page

# 8. Create a Pull Request on GitHub (dev ← feature/menu-page)
# ... review and merge on GitHub ...

# 9. Clean up
git checkout dev
git pull origin dev
git branch -d feature/menu-page
git push origin --delete feature/menu-page

# 10. When the whole project is stable, merge to main
git checkout main
git merge dev
git push origin main
```

---

## Part 8: Common Scenarios

### "I committed to the wrong branch"

If you accidentally committed to `dev` instead of a feature branch:

```bash
# Save the commit hash
git log --oneline -1           # Note the hash, e.g., a3f2c91

# Undo the commit on dev (keeps the changes as unstaged)
git reset HEAD~1

# Create your feature branch and reapply
git stash
git checkout -b feature/my-feature
git stash pop
git add .
git commit -m "My feature changes"
```

### "My feature branch is behind dev"

If `dev` got new merges while you were working on your feature:

```bash
git checkout feature/my-feature
git merge dev                    # Bring dev's changes into your branch
# Resolve any conflicts if they appear
git push origin feature/my-feature
```

**Why merge `dev` into your feature branch?**
- Keeps your feature up to date with the latest code
- Catches conflicts early (easier to fix small conflicts than large ones)
- Your eventual merge back into `dev` will be cleaner

### "I want to see the reference implementation"

The reference (complete) implementations are available as a deployed site. Check the live demo link in each project's README. The reference source code is not available in the starter repo — build it yourself using the concepts guide and the live demo as your target.

---

## Quick Reference — Branch Commands

| Task | Command |
|------|---------|
| See all branches | `git branch -a` |
| Create & switch to branch | `git checkout -b <name>` |
| Switch to existing branch | `git checkout <name>` |
| Merge branch into current | `git merge <name>` |
| Delete local branch | `git branch -d <name>` |
| Delete remote branch | `git push origin --delete <name>` |
| See branch graph | `git log --oneline --graph --all` |

---

## Summary — Your Branch Structure

```
main                          ← Stable releases only
 └── dev                      ← Integration branch (all features merge here)
      ├── feature/header      ← Individual feature work
      ├── feature/search
      ├── feature/cart
      ├── fix/total-bug
      └── style/responsive
```

**Golden rules:**
1. Never commit directly to `main`
2. Always create feature branches from `dev`
3. Merge features into `dev` via Pull Requests
4. Merge `dev` into `main` only when stable
5. Delete feature branches after merging
6. Commit often, push regularly
