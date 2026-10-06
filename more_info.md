# Git Beginners — Complete Cheat Sheet & Guide

> A hands-on walkthrough of Git basics, based on a real terminal session. Every command, what it does, and what happens under the hood.

---

## 📋 Table of Contents

1. [Setup & Configuration](#1-setup--configuration)
2. [Starting a Repository](#2-starting-a-repository)
3. [The Three States of Git](#3-the-three-states-of-git)
4. [Basic Workflow: Add → Commit](#4-basic-workflow-add--commit)
5. [Viewing Changes: status, diff, log](#5-viewing-changes-status-diff-log)
6. [Understanding Git History Structure](#6-understanding-git-history-structure)
7. [Undoing Things: restore & revert](#7-undoing-things-restore--revert)
8. [Branching](#8-branching)
9. [Merging & Conflicts](#9-merging--conflicts)
10. [The .gitignore File](#10-the-gitignore-file)
11. [Pushing to GitHub](#11-pushing-to-github)
12. [Quick Reference Cheat Sheet](#12-quick-reference-cheat-sheet)

---

## 1. Setup & Configuration

### Check your Git identity

```bash
$ git config --get user.name
Mohammed Faizal Razza

$ git config --list
user.name=Mohammed Faizal Razza
user.email=faizalrazza756@gmail.com
core.editor="C:\Users\faiza\AppData\Local\Programs\Microsoft VS Code\bin\code" --wait
```

**What this means:**
- Git stores your name and email with every commit you make.
- `user.name` → shown as the commit author
- `user.email` → used to link commits to your GitHub account
- `core.editor` → the editor Git opens when you need to type a commit message (without `-m`)

### Check Git version

```bash
$ git --version
git version 2.51.0.windows.1
```

### Common mistakes & fixes

| What you typed | What happened | Correct command |
|---|---|---|
| `git confog --get core.editor` | `'confog' is not a git command` | `git config --get core.editor` |
| `git config --get core.editor user.name` | Returns blank | Run separate commands: `git config --get user.name` |
| `git config --get core.editor, user.name` | `invalid key` | No commas — one key per command |

**💡 Tip:** `git config --list` shows *everything*. To see one value, use `git config --get <key>`.

---

## 2. Starting a Repository

```bash
$ mkdir git-beginners
$ cd git-beginners/
$ git init
Initialized empty Git repository in C:/Users/faiza/ddocumets/github/git-beginners/.git/
```

**What happens:**
- `git init` creates a hidden `.git/` folder. That folder **is** the repository — it stores all history, branches, and config.
- Your project files live *outside* `.git/`.

```
git-beginners/          ← your working directory
├── .git/               ← the actual repository (hidden)
│   ├── HEAD            ← points to current branch
│   ├── config          ← local repo settings
│   ├── objects/        ← all commits, files, snapshots
│   └── refs/           ← branches and tags
├── index.html
└── style.css
```

**Check it's a repo:**
```bash
$ git status
On branch main
No commits yet
nothing to commit (create/copy files and use "git add" to track)
```

**💡 Note:** `git init` set your default branch to `main` (from `init.defaultbranch=main` in your config).

---

## 3. The Three States of Git

Every file in Git lives in one of three states:

```
┌─────────────────┐    git add     ┌─────────────────┐   git commit   ┌─────────────────┐
│                 │ ─────────────▶ │                 │ ─────────────▶ │                 │
│  Working Tree   │                │   Staging Area  │                │   Repository    │
│  (your files)   │ ◀───────────── │   (the index)   │                │   (.git/)       │
│                 │  git restore   │                 │                │                 │
└─────────────────┘                └─────────────────┘                └─────────────────┘
```

| State | What it means | Command to move on |
|---|---|---|
| **Working Tree** | Files you're editing | `git add` → Staging |
| **Staging Area** | Changes ready to be committed | `git commit` → Repository |
| **Repository** | Saved snapshots in `.git/` | — |

---

## 4. Basic Workflow: Add → Commit

### Create files

```bash
$ touch index.html style.css
$ ls
index.html  style.css
```

### Check status — untracked files

```bash
$ git status
Untracked files:
        index.html
        style.css
```

**Untracked** = Git sees the file but isn't tracking it yet.

### Stage files

```bash
$ git add index.html style.css
```

**Common mistake:**
```bash
$ git add index.html style.html    # typo!
fatal: pathspec 'style.html' did not match any files
```
Git tells you the file doesn't exist. Double-check your filenames.

> To solve this write the correct file name after like this git add index.html style.css

### Check status — staged

```bash
$ git status
Changes to be committed:
        new file:   index.html
        new file:   style.css
```

### Commit

```bash
$ git commit -m "added the index.html and style.css"
[main (root-commit) 2f9da53] added the index.html and style.css
 2 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 index.html
 create mode 100644 style.css
```

**What happened:**
- `root-commit` = this is the very first commit (no parent)
- `2f9da53` = the commit's unique hash (short version)
- `2 files changed` = summary of the snapshot

---

## 5. Viewing Changes: status, diff, log

### `git status` — what's happening now

```bash
$ git status
On branch main
Changes not staged for commit:
        modified:   index.html

no changes added to commit
```

**Reading it:**
- "Changes not staged" = modified in working tree, but not yet `git add`-ed
- "Changes to be committed" = staged, ready to commit

### `git diff` — unstaged changes

```bash
$ git diff
diff --git a/index.html b/index.html
@@ -0,0 +1,12 @@
+<!DOCTYPE html>
+<html lang="en">
...
```

**Shows:** what you changed but **haven't staged** yet.

### `git diff --staged` — staged changes

```bash
$ git diff --staged
diff --git a/index.html b/index.html
+  <h1>Let's learn Git!</h1>
```

**Shows:** what's staged and will go into the next commit.

**Common mistake:**
```bash
$ git diff --stage     # wrong
error: invalid option: --stage
```
Correct is `--staged` (or `--cached`).

**💡 Rule of thumb:**
| Command | Shows |
|---|---|
| `git diff` | Working tree vs. staging area |
| `git diff --staged` | Staging area vs. last commit |
| `git diff HEAD` | Working tree vs. last commit |

### So:
        - You edit a file → git diff shows it.
        - You run git add → git diff goes empty, git diff --staged shows it.
        - You git commit → both go empty (until you edit again).

> That's the flow. ✅

## What is Head:
In Git, HEAD is a dynamic pointer that represents your current active location in the repository. Think of it like a "You Are Here" needle on a compass or a bookmark in a book. It tells Git which branch or commit you are currently looking at and editing.
### Here is exactly how HEAD, main, and your commits fit together.
        While main is a pointer to the latest commit on your primary development line, HEAD is a pointer to a pointer. Under normal circumstances, HEAD does not point directly to a commit. Instead, HEAD points to the active branch name, and the branch name points to           the latest commit.

         [ HEAD ] 
            │
            ▼
         [ main ] ──► [ Commit A ] ──► [ Commit B (Latest) ]

         When you save your work and type git commit, Git looks at HEAD to see where you are. Because HEAD points to main, Git knows to add the new commit to the main branch, and then both main and HEAD move forward together.
         
## Why does HEAD point toward main?
HEAD points toward main simply because main is the branch you currently have checked out.

### `git log` — full history

```bash
$ git log
commit 5698fc90ddcc5e131a2be0094a1a70b60e3d142e (HEAD -> main)
Author: Mohammed Faizal Razza <faizalrazza756@gmail.com>
Date:   Mon Oct 5 20:37:06 2026 +0530

    deleted the new.css

commit 7e11c79a4798b93d9bf618caec0ee217b1aba871
...
```

### `git log --oneline` — compact view

```bash
$ git log --oneline
5698fc9 (HEAD -> main) deleted the new.css
7e11c79 added .gitignore file for confidential data
a11786f add css folder made a new.css file and moved style.css file to css folder
57aa631 add styles
336de8f added html content
2f9da53 added the index.html and style.css
```

### `git log --parents` — show parent links

```bash
$ git log --oneline --parents
5698fc9 7e11c79 (HEAD -> main) deleted the new.css
7e11c79 a11786f added .gitignore file for confidential data
a11786f 57aa631 add css folder...
57aa631 336de8f add styles
336de8f 2f9da53 added html content
2f9da53 added the index.html and style.css
```

**What this shows:** each commit followed by its **parent's hash**. This proves Git is a **linked chain**, not a stack.

---

## 6. Understanding Git History Structure

### Visual: how commits link

```
   ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
   │  5698fc9    │     │  7e11c79    │     │  a11786f    │
   │ "deleted    │────▶│ "added      │────▶│ "add css    │
   │  new.css"   │     │  .gitignore"│     │  folder"    │
   └─────────────┘     └─────────────┘     └─────────────┘
         ▲
         │
       HEAD
      (main)
```

Each commit points **backward** to its parent. `HEAD` points to the tip.

### Stack vs. Chain

| Stack (LIFO) | Git (Linked Chain / DAG) |
|---|---|
| Push/pop from top | Each commit points to parent |
| Remove newest first | Can't "pop" — use `revert`/`reset` |
| Linear | Can branch and merge (DAG) |

---

## 7. Undoing Things: restore & revert

### `git restore` — discard working tree changes

```bash
$ rm css/new.css          # accidentally deleted
$ git status
        deleted:    css/new.css

$ git restore css/new.css # bring it back
$ git status
nothing to commit, working tree clean
```

**What it does:** restores the file from the last commit (or staging area).

**⚠️ Warning:** This **permanently discards** uncommitted changes to that file.

### `git revert` — undo a commit safely

```bash
$ git revert 5698fc9
[main 82af006] Revert "deleted the new.css"
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 css/new.css
```

**What it does:** creates a **new commit** that undoes the changes of an old commit. History is preserved.

```
Before revert:
  5698fc9 "deleted the new.css"   ← you want to undo this
  7e11c79 ...

After revert:
  82af006 "Revert 'deleted the new.css'"  ← new commit that undoes it
  5698fc9 "deleted the new.css"           ← still in history
  7e11c79 ...
```

**💡 `restore` vs `revert`:**
| Command | What it does | History |
|---|---|---|
| `git restore <file>` | Discards uncommitted changes | No new commit |
| `git revert <hash>` | Undoes a committed change | Creates a new commit |

---

## 8. Branching

### Create a branch

```bash
$ git branch welcome
```

Creates a branch pointing at the current commit. Doesn't switch to it.

### Switch branches

```bash
$ git switch welcome
Switched to branch 'welcome'

$ git switch -c temp
Switched to a new branch 'temp'
```

`-c` = create **and** switch.

### See all branches

```bash
$ git branch
  main
  temp
* welcome
```

`*` = the branch you're currently on.

### Branch visualization

```
                    ┌─────────────┐
                    │  05956c1    │
                    │ "welcome    │
                    │  message"   │
                    └─────────────┘
                          ▲
                          │
                       welcome

   ┌─────────────┐     ┌─────────────┐
   │  82af006    │     │  5698fc9    │
   │ "Revert"    │────▶│ "deleted"   │
   └─────────────┘     └─────────────┘
         ▲
         │
       main, temp
```

### Switching with uncommitted changes

```bash
$ git switch main
M       index.html
Switched to branch 'main'
```

Git **carries over** your uncommitted changes to the new branch. This is why you see `M index.html` — the change follows you.

**⚠️ Caution:** If the target branch has different content in that file, Git may refuse to switch to avoid losing work.

---

## 9. Merging & Conflicts

### Fast-forward merge

```bash
$ git switch main
$ git merge welcome
Updating 82af006..05956c1
Fast-forward
 index.html | 2 ++
 1 file changed, 2 insertions(+)
```

**Fast-forward** = main had no new commits, so Git just moves the `main` pointer forward. No merge commit needed.

```
Before:
  main ──▶ 82af006
  welcome ──▶ 05956c1 (child of 82af006)

After:
  main, welcome ──▶ 05956c1
```

### Conflict merge

```bash
$ git merge temp
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

**What happened:** both branches changed the *same lines* of `index.html`. Git can't decide which to keep.

### Conflict markers in the file

```html
<<<<<<< HEAD
  <h1>Let's learn Git!</h1>
=======
  <h1>Let's learn Git!</h1>
  <h2>Welcome to GitHub</h2>
>>>>>>> temp
```

| Marker | Meaning |
|---|---|
| `<<<<<<< HEAD` | Start of **your current branch's** version |
| `=======` | Separator |
| `>>>>>>> temp` | End of **incoming branch's** version |

**To resolve:** edit the file, keep what you want, delete the markers.

### Complete the merge

```bash
$ git add .
$ git commit -m "final merge"
[main 499dff4] final merge
```

### Abort a merge

```bash
$ git merge --abort
```

**This is the command you were looking for** when you typed `git cancel merge`. There is no `git cancel` — it's `git merge --abort`.

### What you *can't* do during a merge

| Command | Error |
|---|---|
| `git switch welcome` | `fatal: cannot switch branch while merging` |
| `git merge temp` | `error: Merging is not possible because you have unmerged files` |
| `git cancel merge` | `git: 'cancel' is not a git command` |

**✅ Fix:** either `git merge --abort` or resolve + `git add` + `git commit`.

### Merge with a merge commit (ort strategy)

```bash
$ git merge main
Merge made by the 'ort' strategy.
```

When both branches have diverged, Git creates a **merge commit** with **two parents**.

```
        ┌─────────────┐
        │  19f6fbb    │
        │ "Merge main │
        │  into temp" │
        │ parents:    │
        │ ae4dfe3 +   │
        │ 499dff4     │
        └─────────────┘
           ▲       ▲
          /         \
         /           \
   ┌─────────┐   ┌─────────┐
   │ ae4dfe3 │   │ 499dff4 │
   └─────────┘   └─────────┘
```

### Delete branches

```bash
$ git branch -d welcome temp
error: the branch 'temp' is not fully merged
hint: If you are sure you want to delete it, run 'git branch -D temp'
Deleted branch welcome (was 05956c1)

$ git branch -D temp
Deleted branch temp (was 19f6fbb)
```

| Flag | Meaning |
|---|---|
| `-d` | Delete only if fully merged (safe) |
| `-D` | Force delete even if not merged (dangerous) |

**Why `temp` wasn't "fully merged":** `temp` had commit `19f6fbb` (the merge commit) that wasn't in `main`. Git warns you because deleting would lose that work.

---

## 10. The .gitignore File

### The problem

```bash
$ git status
Untracked files:
        .env.local
        build/
```

You don't want to commit secrets (`.env.local`) or build artifacts (`build/`).

### The solution

Create `.gitignore`:

```gitignore
.env.local
build/
```

```bash
$ git add .
$ git status
Changes to be committed:
        new file:   .gitignore

$ git commit -m "added .gitignore file for confidential data"
[main 7e11c79] added .gitignore file for confidential data
 1 file changed, 2 insertions(+)
 create mode 100644 .gitignore
```

**What happened:** Git now ignores `.env.local` and `build/`. They won't show in `git status` or be staged by `git add .`.

**💡 Common .gitignore entries:**
```gitignore
# Secrets
.env
.env.local
*.key

# Dependencies
node_modules/
venv/
__pycache__/

# Build output
build/
dist/
*.log

# OS files
.DS_Store
Thumbs.db
```

---

## 11. Pushing to GitHub

### Add a remote

```bash
$ git remote add origin https://github.com/fgdhs788687/git-beginners.git
```

- `origin` = nickname for the remote URL
- `add` = register a new remote

### Push your branch

```bash
$ git push -u origin main
Enumerating objects: 28, done.
Counting objects: 100% (28/28), done.
...
To https://github.com/fgdhs788687/git-beginners.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

| Part | Meaning |
|---|---|
| `-u` | Set upstream — links local `main` to `origin/main` |
| `origin` | The remote name |
| `main` | The branch to push |

After `-u`, future pushes are just `git push`.

**💡 What `-u` does:** it remembers the link, so `git push` and `git pull` know where to go without arguments.

---

## 12. Quick Reference Cheat Sheet

### Setup
| Command | Purpose |
|---|---|
| `git config --get user.name` | Show your Git username |
| `git config --list` | Show all config |
| `git --version` | Show Git version |

### Starting
| Command | Purpose |
|---|---|
| `git init` | Create a new repo (`.git/` folder) |
| `git status` | See current state |

### Basic workflow
| Command | Purpose |
|---|---|
| `git add <file>` | Stage a file |
| `git add .` | Stage everything |
| `git commit -m "msg"` | Commit staged changes |
| `git commit -a -m "msg"` | Stage tracked files + commit |
| `git commit` | Open editor for commit message |

### Viewing
| Command | Purpose |
|---|---|
| `git status` | Working tree + staging state |
| `git diff` | Unstaged changes |
| `git diff --staged` | Staged changes |
| `git log` | Full history |
| `git log --oneline` | Compact history |
| `git log --oneline --parents` | Show parent hashes |

### Undoing
| Command | Purpose |
|---|---|
| `git restore <file>` | Discard uncommitted changes |
| `git restore --staged <file>` | Unstage a file |
| `git revert <hash>` | Undo a commit (new commit) |

### Branching
| Command | Purpose |
|---|---|
| `git branch` | List branches |
| `git branch <name>` | Create a branch |
| `git switch <name>` | Switch branch |
| `git switch -c <name>` | Create + switch |
| `git branch -d <name>` | Delete merged branch |
| `git branch -D <name>` | Force delete |

### Merging
| Command | Purpose |
|---|---|
| `git merge <branch>` | Merge into current branch |
| `git merge --abort` | Cancel a conflicted merge |

### Remotes
| Command | Purpose |
|---|---|
| `git remote add origin <url>` | Register a remote |
| `git push -u origin main` | Push + set upstream |
| `git push` | Push (after `-u`) |

---

## 🧠 Key Mental Models

1. **Git is a linked chain of snapshots** — each commit points to its parent.
2. **Three states:** Working Tree → Staging Area → Repository.
3. **Branches are just movable pointers** — creating one is cheap.
4. **`HEAD` points to your current branch** (or commit, if detached).
5. **Merge = combine histories** — fast-forward if possible, otherwise a merge commit.
6. **Conflicts are normal** — edit, `git add`, `git commit`.
7. **`restore` discards, `revert` undoes** — know the difference.
8. **`.gitignore` protects secrets** — set it up early.

---

## ⚠️ Common Mistakes (and Fixes)

| Mistake | Error | Fix |
|---|---|---|
| `git confog` | `not a git command` | `git config` |
| `git add style.html` (typo) | `pathspec did not match` | Check filename |
| `git diff --stage` | `invalid option` | `git diff --staged` |
| `git cancel merge` | `not a git command` | `git merge --abort` |
| `git switch` during merge | `cannot switch branch while merging` | Abort or finish merge |
| `git branch -d` unmerged | `not fully merged` | `git branch -D` (careful!) |

---

*Happy Git-ing! 🚀*
