# Git Undo Operations Complete Guide

> **"In Git, almost no operation is irreversible."**

One of Git's most powerful features is that it gives you "regret medicine". Whether it's accidentally deleting files, committing wrong code, or needing to revert to a previous version, Git provides multiple ways to undo operations. This chapter will systematically introduce all undo operations in Git, helping you handle problems calmly.

As a developer, you will inevitably make mistakes in daily work. Maybe you accidentally deleted an important file, maybe you committed a version with wrong code, maybe you need to revert to a previous stable version. Without version control systems, these operations might take you a lot of time to fix, or even cause irreparable losses. But with Git, these operations become simple and safe.

This chapter will start from the most basic undo operations and gradually go deeper into more complex scenarios, helping you fully master Git's undo functionality. Whether you are a Git beginner or an experienced developer, you can find the knowledge you need here.

---

## Table of Contents

- [Undo Working Directory Changes](#undo-working-directory-changes)
- [Undo Staging](#undo-staging)
- [Undo Last Commit](#undo-last-commit)
- [git reset Three Modes Details](#git-reset-three-modes-details)
- [git revert vs git reset](#git-revert-vs-git-reset)
- [Modify History Commits](#modify-history-commits)
- [Restore Deleted Branch](#restore-deleted-branch)
- [Restore Deleted File](#restore-deleted-file)
- [git clean Clean Untracked Files](#git-clean-clean-untracked-files)
- [Undo Pushed Commits](#undo-pushed-commits)
- [git restore Multiple Usages](#git-restore-multiple-usages)
- [Safe Operation Principles](#safe-operation-principles)
- [Dangerous Operation Warnings](#dangerous-operation-warnings)
- [Undo Operation Decision Tree](#undo-operation-decision-tree)
- [Common Undo Scenarios](#common-undo-scenarios)

---

## Undo Working Directory Changes

### Scenario Description

When you modified files in working directory but haven't executed `git add`, and found the modification is wrong, you want to discard these changes and restore to the state of last commit.

### Using git restore (Recommended, Git 2.23+)

```bash
# Undo single file modification
git restore filename.js

# Undo multiple files modification
git restore file1.js file2.js file3.js

# Undo all modified files (dangerous!)
git restore .
```

**Command Details:** `git restore` command is a new command introduced in Git 2.23, specifically for restoring working directory files. Its semantics are clearer and more explicit compared to the old `git checkout` command, easier to understand and use. When you execute `git restore filename.js`, Git reads the original content of the file from staging area (if file is staged) or the latest commit, and overwrites it to the corresponding position in working directory.

### Using git checkout (Old Syntax)

```bash
# Undo single file modification
git checkout -- filename.js

# Undo all modifications (dangerous!)
git checkout -- .
```

### Practical Operation Demo

```bash
# 1. View current status
git status
# Output: modified: app.js

# 2. View modification content
git diff app.js

# 3. Execute after confirming to undo
git restore app.js

# 4. View status again
git status
# Output: nothing to commit, working tree clean
```

> **Warning:** This operation will **permanently discard** uncommitted modifications in working directory and cannot be recovered. Please confirm whether you need to backup before executing.

### Undo Specific Directory Modifications

```bash
# Undo all modifications in src directory
git restore src/

# Undo all .js file modifications
git restore '*.js'
```

---

## Undo Staging

### Scenario Description

When you executed `git add` to add files to staging area, but don't want to include that file in the next commit, you need to remove it from staging area (but keep the modifications in working directory). This situation is very common in actual development, for example, you might accidentally added a wrong file, or want to split a large commit into multiple small commits. Understanding how to correctly undo staging operations is very important for maintaining a clear commit history.

### Using git restore --staged (Recommended, Git 2.23+)

```bash
# Remove single file from staging area
git restore --staged filename.js

# Remove multiple files from staging area
git restore --staged file1.js file2.js

# Remove all files from staging area
git restore --staged .
```

### Using git reset (Old Syntax)

```bash
# Remove single file from staging area
git reset HEAD filename.js

# Remove all files from staging area
git reset HEAD
```

### Practical Operation Demo

```bash
# 1. Suppose you accidentally added wrong file
git add secret-keys.txt

# 2. View status
git status
# Output: Changes to be committed: new file: secret-keys.txt

# 3. Remove file from staging area
git restore --staged secret-keys.txt

# 4. View status again
git status
# Output: Untracked files: secret-keys.txt
# File returned to untracked state, modifications still preserved
```

### Keep File After Removing from Staging Area

If you want to remove file from staging area and also delete it from working directory:

```bash
# Use git rm --cached to only remove from staging area (keep working directory file)
git rm --cached filename.js

# Use git rm to delete from both staging area and working directory (dangerous!)
git rm filename.js
```

---

## Undo Last Commit

### Scenario Description

You just executed `git commit`, but found the commit message is wrong, or forgot to add some files, or committed content that shouldn't be committed, and need to undo this commit.

### Method 1: git commit --amend (Modify Most Recent Commit)

```bash
# Modify commit message
git commit --amend -m "Correct commit message"

# Add forgotten file and modify commit (keep original commit message)
git add forgotten-file.js
git commit --amend --no-edit

# Add forgotten file and modify commit message
git add forgotten-file.js
git commit --amend -m "Updated commit message"
```

### Method 2: git reset Rollback Commit

```bash
# Soft rollback: Undo commit, keep modifications in staging area
git reset --soft HEAD~1

# Mixed rollback: Undo commit, keep modifications in working area (default mode)
git reset HEAD~1

# Hard rollback: Undo commit, discard all modifications (dangerous!)
git reset --hard HEAD~1
```

### Method 3: git revert Create Undo Commit

```bash
# Create a new commit to undo changes from last commit
git revert HEAD

# Create undo commit but don't auto-commit (only put in staging area)
git revert --no-commit HEAD
```

---

## git reset Three Modes Details

`git reset` is one of the most powerful and commonly used undo commands in Git. It has three modes: `--soft`, `--mixed` (default) and `--hard`. Understanding the differences between these three modes is crucial for correctly using Git. Many Git beginners feel confused when using undo commands, mainly because they don't truly understand the working principles and applicable scenarios of these three modes.

### Working Principles of Three Modes

Beforedeeply understand the three modes，first need to understand Git three core areas of：Working Directory、Staging Area and Repository。Working Directoryis where you actually edit code，Staging Area is temporary storage for content you plan to commit，Repository is where Git all commit history is stored。`git reset` command moves the HEAD pointer，and decides whether to modify staging area and working directory based on different modesWorking Directory。

```
Working Directory  ←→  Staging Area  ←→  Repository
     Your code files     git add snapshot     git commit record
```

| Mode | Working Directory | Staging Area | Repository | Use Case |
|------|-------------------|--------------|------------|----------|
| `--soft` | Keep | Keep | Rollback | Modify commit message, merge multiple commits |
| `--mixed` | Keep | Clear | Rollback | Re-select files to commit |
| `--hard` | Clear | Clear | Rollback | Completely discard changes |

### --soft Mode Details

**Purpose:** Only rollback commit, keep all modifications in staging area and working directory.

```bash
git reset --soft HEAD~1
```

**Use Cases:**
- Modify content of most recent commit
- Merge multiple commits into one
- Need small adjustments after commit

```bash
# Example: Merge most recent three commits into one
git reset --soft HEAD~3
git commit -m "Merged commit message"

# Example: Add forgotten file after commit
git commit -m "Add feature A"
git add forgotten-file.js
git reset --soft HEAD~1
git commit -m "Add feature A (complete version)"
```

### --mixed Mode Details (Default Mode)

**Purpose:** Rollback commit and staging area, but keep modifications in working directory.

```bash
git reset --mixed HEAD~1
# Equivalent to
git reset HEAD~1
```

**Use Cases:**
- Need to reorganize commit content
- Staged wrong files, want to re-select
- Split a large commit into multiple small commits

```bash
# Example: Split one commit into multiple small commits
git reset HEAD~1
# Now all modifications are in working directory
git add feature-a.js
git commit -m "Add feature A"
git add feature-b.js
git commit -m "Add feature B"
```

### --hard Mode Details

**Purpose:** Rollback commit, staging area and working directory, completely discard all modifications.

```bash
git reset --hard HEAD~1
```

**Use Cases:**
- Completely discard recent modifications, return to previous state
- Local branch needs forced rollback to specific version

```bash
# Example: Completely rollback to previous version
git reset --hard HEAD~1

# Example: Rollback to specific commit
git reset --hard abc1234

# Example: Force sync with remote branch
git fetch origin
git reset --hard origin/main
```

> **Danger Warning:** `--hard` mode will **permanently delete** uncommitted modifications! Please ensure you have backed up important content before executing.

### Three Modes Comparison Chart

```
Initial state (after three commits):
  Repository: A <- B <- C (HEAD)
  Staging Area: C's content
  Working Directory: C's content + possible modifications

After executing git reset --soft HEAD~1:
  Repository: A <- B (HEAD)
  Staging Area: C's content (preserved)
  Working Directory: C's content + possible modifications (preserved)

After executing git reset --mixed HEAD~1:
  Repository: A <- B (HEAD)
  Staging Area: B's content (cleared C's staging)
  Working Directory: C's content + possible modifications (preserved)

After executing git reset --hard HEAD~1:
  Repository: A <- B (HEAD)
  Staging Area: B's content (cleared)
  Working Directory: B's content (cleared all modifications)
```

---

## git revert vs git reset

In Git, both `git revert` and `git reset` can be used to undo commits, but their working methods and applicable scenarios have essential differences. Understanding the differences between these two commands is a must for becoming a Git expert. Many team collaboration problems are caused by incorrectly choosing undo commands.

### Core Difference

`git reset` works by moving the HEAD pointer and directly deleting commit history, which changes the project's history. `git revert` creates a new commit to undo the changes of a specified commit, it doesn't modify history but adds a new "undo" operation to the history. This difference determines that their usage scenarios are completely different.

| Feature | git reset | git revert |
|---------|-----------|------------|
| **Working Method** | Move HEAD pointer, delete commit | Create new commit to undo changes |
| **History Record** | Modify history (delete commit) | Preserve history (add undo commit) |
| **Safety** | May lose work | Safe, doesn't lose any commits |
| **Applicable Scenario** | Local unpushed commits | Commits pushed to remote |
| **Whether Changes History** | Yes | No |

### git revert Details

`git revert` creates a new commit to undo specified commit changes, doesn't modify history.

```bash
# Undo most recent commit
git revert HEAD

# Undo specific commit
git revert abc1234

# Undo multiple commits
git revert HEAD~3..HEAD

# Undo merge commit
git revert -m 1 merge-commit-hash

# Create undo commit but don't auto-commit
git revert --no-commit HEAD
```

### Usage Scenario Comparison

#### Scenario 1: Error Found During Local Development

**Recommend using git reset:**

```bash
# Just committed, haven't pushed, found code has bug
git reset --soft HEAD~1
# Fix bug
git add .
git commit -m "Commit after fixing bug"
```

#### Scenario 2: Pushed Commit Needs to be Undone

**Must use git revert:**

```bash
# Code already pushed to remote, other colleagues have pulled
git revert HEAD
git push origin main

# After other colleagues pull, they will see an "undo" commit, history remains clear
```

#### Scenario 3: Undo Middle Commit

```bash
# Use revert (recommended, safe)
git revert abc1234

# Use reset (dangerous, will lose subsequent commits)
git reset --hard abc1234
```

### revert Multiple Commits

```bash
# Undo one by one (each undo creates a commit)
git revert HEAD~3..HEAD

# Merge into one undo commit
git revert --no-commit HEAD~3..HEAD
git commit -m "Undo most recent three commits"
```

---

## Modify History Commits

### Using git commit --amend

`git commit --amend` can only modify the most recent commit. This command is used very frequently in daily development, main uses include: modifying commit message, adding forgotten files to last commit, modifying content of last commit. Note that `git commit --amend` actually creates a new commit to replace the original commit, rather than truly "modifying" the original commit. Therefore, if it has been pushed to remote repository, you need to force push after using `--amend`.

```bash
# Modify commit message
git commit --amend -m "New commit message"

# Don't modify commit message, only add file
git add new-file.js
git commit --amend --no-edit

# Interactive modify (open editor)
git commit --amend
```

### Using git rebase -i to Modify History Commits

Interactive rebase can modify any history commit, very powerful. It is the most flexible commit modification tool in Git, can achieve commit reordering, merging multiple commits, modifying commit message, deleting unwanted commits, etc. Although the learning curve of interactive rebase is relatively steep, once mastered, it will become an indispensable tool in your daily development. Note that interactive rebase modifies commit history, so it only applies to local commits that haven't been pushed to remote repository.

```bash
# Modify most recent 3 commits
git rebase -i HEAD~3
```

After execution, editor will open, showing content like:

```
pick abc1234 First commit
pick def5678 Second commit
pick ghi9012 Third commit

# Rebase aaa1111..ghi9012 onto bbb2222 (3 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# d, drop = remove commit
```

#### Common Operation Examples

**Modify commit message (reword):**

```
reword abc1234 First commit
pick def5678 Second commit
pick ghi9012 Third commit
```

**Merge multiple commits (squash):**

```
pick abc1234 First commit
squash def5678 Second commit
squash ghi9012 Third commit
```

**Delete specific commit (drop):**

```
pick abc1234 First commit
drop def5678 Unwanted commit
pick ghi9012 Third commit
```

**Modify specific commit content (edit):**

```
pick abc1234 First commit
edit def5678 Commit to modify
pick ghi9012 Third commit
```

When rebase stops at `edit` marked commit:

```bash
# Modify file
vim need-fix.js

# Add modification
git add need-fix.js

# Continue rebase
git rebase --continue
```

> **Note:** Interactive rebase modifies history, only applies to commits not yet pushed to remote.

---

## Restore Deleted Branch

### Scenario Description

You accidentally deleted an important branch, need to restore it. This situation happens from time to time in actual work, especially when developers use `git branch -D` command to force delete branches. Fortunately, Git provides `reflog` mechanism to help us restore deleted branches. As long as the branch wasn't deleted too long ago (default within 90 days), we can find the branch's last commit record through reflog and recreate the branch.

### Using git reflog to Find and Restore

`git reflog` is a very powerful tool in Git, it records all movement history of HEAD pointer, including branch creation, switching, commit, reset operations. Even if a branch is deleted, reflog still retains the commit record that the branch last pointed to. Through these records, we can find the branch's last state and restore it.

```bash
# 1. View all operation history (including deleted branch operations)
git reflog

# Output example:
# abc1234 HEAD@{0}: checkout: moving from feature-branch to main
# def5678 HEAD@{1}: commit: Add new feature
# ghi9012 HEAD@{2}: branch: Created from HEAD

# 2. Find branch's last commit
# Find feature-branch's last commit hash from output

# 3. Restore branch
git checkout -b feature-branch def5678
# Or using git switch
git switch -c feature-branch def5678
```

### Complete Operation Flow

```bash
# 1. Suppose accidentally deleted branch
git branch -D important-feature
# Output: Deleted branch important-feature (was def5678).

# 2. Immediately view reflog
git reflog --all | grep important-feature

# 3. Find branch's commit record
git reflog
# Look for records like:
# def5678 HEAD@{5}: commit: feat: Add important feature

# 4. Restore branch
git checkout -b important-feature def5678

# 5. Verify restoration successful
git log --oneline -5
```

### reflog Validity Period

- Default retention 90 days (can be modified through configuration)
- Expired reflog records will be cleaned by garbage collection
- Important branches should be restored as soon as possible after deletion

```bash
# View reflog expiration time configuration
git config gc.reflogExpire
git config gc.reflogExpireUnreachable

# Set longer retention time
git config gc.reflogExpire "180 days"
git config gc.reflogExpireUnreachable "90 days"
```

---

## Restore Deleted File

In software development process, file deletion is a common problem. Maybe you accidentally executed `git rm` command to delete an important file, or manually deleted a file and then realized you need to restore it. Git provides multiple ways to restore deleted files, which method to use depends on how the file was deleted and whether the deletion operation has been committed to the repository. Mastering these restoration methods can help you quickly find back important content when encountering file loss, avoiding unnecessary losses.

### Method 1: Using git checkout to Restore

```bash
# Restore single file to most recent commit state
git checkout HEAD -- filename.js

# Restore file to specific commit state
git checkout abc1234 -- filename.js

# Restore entire directory
git checkout HEAD -- src/
```

### Method 2: Using git restore to Restore

```bash
# Restore single file
git restore filename.js

# Restore file to specific commit
git restore --source=abc1234 filename.js

# Restore entire directory
git restore src/
```

### Method 3: Using git show to View and Restore

```bash
# View file content in specific commit
git show HEAD:filename.js
git show abc1234:src/app.js

# Redirect file content to file
git show HEAD:filename.js > filename.js
```

### Method 4: Restore File Deleted After Commit

```bash
# 1. Find commit that deleted file
git log --diff-filter=D --summary | grep filename.js

# 2. Find commit before deletion
git log --all --full-history -- filename.js

# 3. Restore file from commit before deletion
git checkout abc1234^ -- filename.js
# abc1234^ means parent commit of abc1234 (commit before deletion operation)
```

### Restore Multiple Files

```bash
# Restore all deleted files
git ls-files -d | xargs git checkout HEAD --

# Restore specific type files
git ls-files -d '*.js' | xargs git checkout HEAD --
```

---

## git clean Clean Untracked Files

### Scenario Description

In daily development, working directory often accumulates many untracked files, such as temporary files from compilation, log files, backup files generated by editors, operating system hidden files, etc. These files don't affect code functionality, but will make working directory messy, and may interfere with `git status` output. `git clean` command is specifically used to clean these untracked files. Special attention needed when using, because files deleted by `git clean` cannot be recovered through Git, because these files were never tracked by Git.

### Basic Usage

```bash
# Preview files to be deleted (don't actually delete)
git clean -n

# Delete untracked files
git clean -f

# Delete untracked files and directories
git clean -fd

# Delete untracked and ignored files (more thorough)
git clean -fdx
```

### Common Options

| Option | Description |
|--------|-------------|
| `-n` | Preview mode, only show files to be deleted |
| `-f` | Force delete files |
| `-d` | Also delete untracked directories |
| `-x` | Also delete files ignored by .gitignore |
| `-X` | Only delete files ignored by .gitignore |
| `-i` | Interactive mode, confirm one by one |

### Safe Operation Flow

```bash
# 1. First preview content to be deleted
git clean -n
# Output: Would remove temp-file.txt
# Output: Would remove build/

# 2. Execute deletion after confirmation
git clean -f

# 3. If need to delete directory
git clean -fd

# 4. Use interactive mode (safest)
git clean -i
# Will ask whether to delete each file one by one
```

> **Warning:** Files deleted by `git clean` **cannot be recovered**, because they were never tracked by Git. Please always use `-n` option to preview before executing.

### Use with .gitignore

```bash
# Clean build artifacts (if configured in .gitignore)
git clean -fdX

# Only clean node_modules
git clean -fdX node_modules/

# Clean all untracked files, but keep .env and other config files
# First add files to keep to .gitignore
echo ".env" >> .gitignore
git clean -fdx
```

---

## Undo Pushed Commits

### Safe Method: git revert (Recommended)

When code has been pushed to remote repository, undo operations need special caution. Because other developers may have already developed based on your commit, if you directly modify history (like using `git reset` + `git push --force`), it will cause problems for others' work. In this case, the safest approach is to use `git revert` command to create a new "undo" commit, which achieves the purpose of undoing changes while preserving complete history, not affecting other developers' workflow.

```bash
# 1. Create undo commit
git revert HEAD

# 2. Push undo commit
git push origin main

# Undo specific commit
git revert abc1234
git push origin main

# Undo multiple commits
git revert HEAD~3..HEAD
git push origin main
```

### Unsafe Method: git reset + force push (Use with Caution)

```bash
# 1. Rollback local commit
git reset --hard HEAD~1

# 2. Force push to remote (dangerous!)
git push --force origin main
# Or use safer option
git push --force-with-lease origin main
```

### force-with-lease vs force

```bash
# --force: Unconditional force push, may overwrite others' commits
git push --force origin main

# --force-with-lease: Only push when remote branch has no new commits
# If others have pushed new commits, will refuse to push
git push --force-with-lease origin main
```

> **Severe Warning:** Using `git push --force` on public branches may cause other developers' work to be lost. Only use when you are completely sure of the consequences.

### Best Practices

1. **Unpushed commits**: Use `git reset` or `git commit --amend`
2. **Pushed commits**: Use `git revert` to create undo commit
3. **Personal branches**: Can use `git reset` + `git push --force-with-lease`
4. **Public branches**: Must use `git revert`

---

## git restore Multiple Usages

`git restore` is a new command introduced in Git 2.23, used to restore working directory and staging area files. This command was introduced to solve the problem of `git checkout` command having too many responsibilities. In old versions of Git, `git checkout` was responsible for both switching branches and restoring files, which led to unclear command semantics. `git restore` focuses on file restoration operations, with clearer semantics and more intuitive use. For Git beginners, it's recommended to use `git restore` command first, it's easier to understand and remember.

### Restore Working Directory Files

```bash
# Restore working directory file to staging area state
git restore filename.js

# Restore working directory file to specific commit state
git restore --source=HEAD~2 filename.js
git restore --source=abc1234 filename.js

# Restore entire directory
git restore src/

# Restore all files
git restore .
```

### Restore Staging Area Files

```bash
# Restore staging area file to HEAD state (unstage)
git restore --staged filename.js

# Restore staging area file to specific commit state
git restore --staged --source=HEAD~2 filename.js

# Unstage all files
git restore --staged .
```

### Simultaneously Restore Working Directory and Staging Area

```bash
# Restore both working directory and staging area to HEAD state
git restore --staged --worktree filename.js

# Restore all files
git restore --staged --worktree .
```

### Common Parameter Combinations

```bash
# Restore file from specific branch
git restore --source=feature-branch filename.js

# Restore file from specific commit
git restore --source=abc1234 filename.js

# Restore and create file that doesn't exist in working directory
git restore --source=HEAD filename.js

# Confirm mode (show operation to be executed)
git restore --dry-run filename.js
```

### git restore vs git checkout

| Function | git restore | git checkout |
|----------|-------------|--------------|
| Restore working directory file | `git restore file` | `git checkout -- file` |
| Restore staging area | `git restore --staged file` | `git reset HEAD file` |
| Restore from specific commit | `git restore --source=commit file` | `git checkout commit -- file` |
| Switch branch | Not supported | `git checkout branch` |

`git restore` is more focused on file restoration operations, with clearer semantics, recommended to use.

---

## Safe Operation Principles

When using Git undo operations, following some safety principles can avoid many unnecessary troubles. These principles are lessons summarized by countless developers in actual work, mastering them can make you more confident when using Git. Remember, prevention is always easier than remedy, developing good operation habits can greatly reduce losses caused by misoperations.

### Principle 1: Backup Before Operation

```bash
# Create backup branch
git branch backup-before-reset

# Then execute dangerous operation
git reset --hard HEAD~3

# If need to recover, switch to backup branch
git checkout backup-before-reset
```

### Principle 2: Use git stash to Save Temporary Work

```bash
# Save current modifications
git stash push -m "Save current progress"

# View saved list
git stash list

# Restore saved modifications
git stash pop

# Restore but don't delete stash record
git stash apply stash@{0}
```

### Principle 3: Use reflog as Safety Net

```bash
# reflog records all movement history of HEAD
git reflog

# Even after executing git reset --hard, can recover through reflog
git reflog
# Find commit hash before reset
git reset --hard abc1234
```

### Principle 4: Preview Before Execute

```bash
# Use --dry-run to preview
git clean --dry-run
git restore --dry-run filename.js

# Use diff to view modifications
git diff
git diff --staged

# Use status to view status
git status
```

### Principle 5: Small Commits, Frequent Commits

```bash
# Don't commit large amount of modifications at once
# Good practice: small commits
git add feature-a.js
git commit -m "feat: Implement feature A"

git add feature-b.js
git commit -m "feat: Implement feature B"

# This way when need to undo, impact range is smaller
```

---

## Dangerous Operation Warnings and Safety Nets

In Git, some operations have certain risks, may cause data loss or affect team collaboration. Understanding these dangerous operations, and knowing how to correctly use safety net mechanisms to protect yourself, is a skill every Git user must master. Below we will detail high-risk operations in Git, and how to use Git's built-in safety mechanisms to avoid and recover from misoperations.

### High-Risk Operation Checklist

| Operation | Danger Level | Description |
|-----------|--------------|-------------|
| `git reset --hard` | High | Discard all uncommitted modifications |
| `git push --force` | High | May overwrite remote others' commits |
| `git clean -f` | Medium | Delete untracked files, cannot recover |
| `git branch -D` | Medium | Force delete unmerged branch |
| `git checkout -- .` | Medium | Discard all working directory modifications |
| `git reset --mixed` | Low | Only clear staging area, working directory preserved |

### Safety Net Mechanism

#### 1. reflog is the Last Lifeline

```bash
# Almost all misoperations can be recovered through reflog
git reflog

# Example: Accidentally executed git reset --hard
git reset --hard HEAD~3
# Found rollback went too far

# Find original position through reflog
git reflog
# Output:
# abc1234 HEAD@{0}: reset: moving to HEAD~3
# def5678 HEAD@{1}: commit: Commit I need

# Recover
git reset --hard def5678
```

#### 2. Staging Area is Second Line of Defense

```bash
# Even if working directory files are modified, staging area may have previous version
git restore --staged filename.js
```

#### 3. Backup Branches are Always Good Habit

```bash
# Create backup before executing any dangerous operation
git branch backup-$(date +%Y%m%d-%H%M%S)

# View all backup branches
git branch | grep backup
```

---

## Undo Operation Decision Tree

When you need to undo operations, facing numerous Git commands may feel confusing. To help you quickly make correct decisions, we designed a detailed decision tree. This decision tree covers all common undo scenarios, through answering a few simple questions, you can find the most suitable command. Suggest you save this decision tree for reference when encountering undo needs. With accumulated experience, you will gradually form your own judgment ability, no longer needing to rely on decision tree.

When you need to undo operations, you can refer to the following decision tree:

```
                        What to undo?
                            |
            +---------------+---------------+
            |               |               |
        Working Dir     Staging Area      Commit
            |               |               |
    +-------+-------+       |       +-------+-------+
    |               |       |       |               |
 Unstaged       Staged      |    Pushed          Unpushed
    |               |       |       |               |
git restore     git restore  |   git revert    git reset
    |          --staged      |       |          (--soft/
    |               |       |       |           mixed/hard)
    |               |       |       |
    v               v       v       v
  Discard        Unstage   Unstage  Safe Undo    Local Rollback
                               |
                         +-----+-----+
                         |           |
                     Pushed       Unpushed
                         |           |
                    git revert   git reset
                         |       or amend
                         |
                         v
                    Create Undo Commit
                    (Preserve History)
```

### Select Command by Scenario

In actual work, we often need to choose the most suitable undo command based on specific scenario. The following table summarizes all common scenarios and corresponding recommended commands, can be used as your quick reference manual. Remember, choosing the correct command requires not only considering technical implementation, but also considering impact on team collaboration. When operating on public branches, must choose safe commands that won't break history.

```
Scenario                          Recommended Command
─────────────────────────────────────────────────────
Modify unstaged file               git restore <file>
Staged want to unstage              git restore --staged <file>
Modify last commit                  git commit --amend
Undo local commit                   git reset --soft HEAD~1
Completely discard local changes    git reset --hard HEAD~1
Undo pushed commit                  git revert HEAD
Restore deleted branch              git reflog + git checkout -b
Restore deleted file                git checkout HEAD -- <file>
Clean temporary files               git clean -fd
Modify history commit               git rebase -i
```

---

## Common Undo Scenarios Practice

Theoretical knowledge is important, but actual operations can better help us understand and master Git undo commands. In this section, we will demonstrate how to correctly use various undo commands to solve actual problems through a series of real development scenarios. Each scenario includes detailed problem description, solution and operation steps, helping you quickly find correct handling method when encountering similar problems. These scenarios are very common in daily development, mastering them will greatly improve your development efficiency.

### Scenario 1: Commit Message Written Wrong

**Situation:** Just committed code, but commit message has typo or inaccurate description. This is one of the most common small errors in development, although it doesn't affect code functionality, it affects commit history readability and team collaboration experience. Especially in open source projects, clear and accurate commit messages are very important for code review and issue tracking. Using `git commit --amend` command can easily fix this problem.

```bash
# Method: Modify last commit message
git commit --amend -m "Correct commit message"

# If already pushed to remote
git commit --amend -m "Correct commit message"
git push --force-with-lease origin main
```

### Scenario 2: Forgotten File After Commit

**Situation:** After commit, found forgot to add some file.

```bash
# Method 1: Use amend
git add forgotten-file.js
git commit --amend --no-edit

# Method 2: If multiple forgotten files
git add file1.js file2.js
git commit --amend -m "Complete commit message"
```

### Scenario 3: Accidentally Committed Sensitive Information

**Situation:** Accidentally committed passwords, API Keys, database connection strings and other sensitive information. This is a very serious problem, because once sensitive information is committed to version control system, even if file is deleted later, information will still remain in commit history. In open source projects, this may lead to security vulnerabilities and privacy leaks. After discovering this situation, should take immediate action, not only undo commit, but also immediately replace leaked keys and passwords.

```bash
# 1. If haven't pushed
git reset --soft HEAD~1
# Remove sensitive file from staging area
git restore --staged secret.env
# Add to .gitignore
echo "secret.env" >> .gitignore
git add .gitignore
git commit -m "Remove sensitive information"

# 2. If already pushed (sensitive information leaked, recommend immediately replacing keys)
git revert HEAD
git push origin main
# Then immediately replace leaked keys/passwords
```

### Scenario 4: Committed Wrong File

**Situation:** Added file that shouldn't be committed to staging area.

```bash
# 1. Remove wrong file from staging area
git restore --staged wrong-file.js

# 2. Add correct file
git add correct-file.js

# 3. If haven't committed
git commit -m "Correct commit"

# 4. If committed but haven't pushed
git reset --soft HEAD~1
git restore --staged wrong-file.js
git add correct-file.js
git commit -m "Correct commit"
```

### Scenario 5: Need to Completely Redo Recent Commit

**Situation:** Most recent commit content needs major modifications, maybe because found many problems to fix, or want to reorganize code structure. In this case, using `git reset --soft HEAD~1` command can rollback commit but keep all modifications in staging area, then you can freely modify code and recommit. This method is cleaner than creating multiple fix commits, can keep commit history clear.

```bash
# 1. Rollback commit, keep modifications in staging area
git reset --soft HEAD~1

# 2. Make modifications
vim need-fix.js

# 3. Recommit
git add .
git commit -m "Redone commit"
```

### Scenario 6: Need to Rollback to Previous Version

**Situation:** Found most recent few commits all have problems, need to rollback to earlier version.

```bash
# 1. View commit history
git log --oneline -10
# Output:
# abc1234 (HEAD -> main) Latest commit
# def5678 Problematic commit
# ghi9012 Problematic commit
# jkl3456 This version is normal

# 2. Local rollback (unpushed)
git reset --hard jkl3456

# 3. Already pushed situation (use revert)
git revert ghi9012
git revert def5678
git revert abc1234
git push origin main
```

### Scenario 7: Accidentally Deleted Branch

**Situation:** Accidentally deleted an important branch, maybe because executed `git branch -D` command, or accidentally deleted still-in-use branch when cleaning branches. This situation is particularly common in team collaboration, especially when multiple developers are handling multiple feature branches simultaneously. Fortunately, as long as branch wasn't deleted too long ago, we can find branch's last pointed commit through `git reflog` command and recreate branch. This function is a major safety net of Git, allowing developers to manage branches without worrying about data loss.

```bash
# 1. View reflog
git reflog
# Find branch's last commit, e.g., def5678

# 2. Restore branch
git checkout -b recovered-branch def5678

# 3. Verify
git log --oneline -5
```

### Scenario 8: Accidentally Deleted File

**Situation:** Executed `git rm` or manually deleted file.

```bash
# 1. If only deleted working directory file
git restore filename.js

# 2. If executed git rm (staged deletion operation)
git restore --staged filename.js  # Unstage
git restore filename.js           # Restore file

# 3. If file was deleted in previous commit
git log --all --full-history -- filename.js
# Find commit before deletion
git checkout abc1234^ -- filename.js
```

### Scenario 9: Want to Abort Merge After Conflict

**Situation:** Many conflicts when merging branches, very complex to resolve, or found merge direction is wrong, want to abort merge and start over. In this case, can use `git merge --abort` command to abort ongoing merge operation, restore working directory to state before merge. Similarly, if ongoing rebase or cherry-pick operation, can also use corresponding abort command to abort operation. These abort commands are safety mechanisms provided by Git, allowing developers to abort ongoing operations at any time.

```bash
# Abort ongoing merge
git merge --abort

# Abort ongoing rebase
git rebase --abort

# Abort ongoing cherry-pick
git cherry-pick --abort
```

### Scenario 10: Stash Content Lost

**Situation:** Accidentally executed `git stash drop` to delete some stash record, or executed `git stash clear` to clear all stashes. `git stash` is a very convenient feature, can temporarily save working directory modifications, allowing developers to quickly switch to other branches to handle urgent tasks. But if accidentally deleted stash record, saved modifications seem to be lost. Actually, Git still retains these modification's commit objects, we can find these dangling commit objects through `git fsck` command and restore them.

```bash
# 1. View fsck to find dangling stash objects
git fsck --unreachable | grep commit

# 2. View found commits
git show <commit-hash>

# 3. Restore stash
git stash apply <commit-hash>
```

---

## Summary

Through this chapter's learning, we systematically mastered all undo operation methods in Git. From the most basic working directory modification undo, to complex commit history modification, to various safety recovery mechanisms, this knowledge will help you use Git more confidently in daily development. Remember, Git's design philosophy is "safety first", as long as you correctly use these undo commands, almost no operation is irreversible. In actual work, recommend using safer commands first (like `git restore` and `git revert`), only use high-risk commands (like `git reset --hard` and `git push --force`) when completely sure of consequences.

### Core Points

1. **Understanding relationship between working directory, staging area, repository** is foundation for correctly using undo commands
2. **Unpushed commits** can be safely modified using `git reset`
3. **Pushed commits** should use `git revert` for safe undo
4. **reflog is the last safety net** can almost recover any misoperation
5. **Backup before operation** develop habit of creating backup branches

### Command Quick Reference

| Scenario | Command |
|----------|---------|
| Undo working directory changes | `git restore <file>` |
| Unstage | `git restore --staged <file>` |
| Modify recent commit | `git commit --amend` |
| Undo local commit | `git reset --soft HEAD~1` |
| Completely discard changes | `git reset --hard HEAD~1` |
| Safely undo pushed commit | `git revert HEAD` |
| Restore deleted branch | `git reflog` + `git checkout -b` |
| Restore deleted file | `git restore <file>` |
| Clean untracked files | `git clean -fd` |
| Modify history commit | `git rebase -i` |

### Final Suggestions

- **Beginner suggestion:** Prioritize using `git restore` and `git revert`, they are safer and have clearer semantics
- **Advanced users:** Master the differences between `git reset` three modes, can choose the most suitable command based on specific scenario
- **Team collaboration:** Public branches only use `git revert`, avoid using `git push --force` affecting other developers
- **Develop habits:** Execute `git status` and `git diff` before operation, confirm current working directory state
- **Backup first:** Before executing any dangerous operation, creating backup branch is a very good habit
- **Use reflog well:** `git reflog` is the last safety net, can almost recover any misoperation
- **Small commits:** Frequently make small commits, so when need to undo, impact range is smaller
- **Test verification:** After executing undo operation, must verify whether code is correct, ensure no new problems introduced

---

## Next Step

[Create and Manage Repositories →](14-create-repo.md)