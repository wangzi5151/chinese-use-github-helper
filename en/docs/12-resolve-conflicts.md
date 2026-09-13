# Chapter 12: Resolving Git Merge Conflicts

> This chapter will deeply explain the causes, types, resolution methods, and prevention strategies of Git merge conflicts, helping developers confidently handle conflicts in version control.

---

## Table of Contents

1. [What is Merge Conflict](#what-is-merge-conflict)
2. [Types of Conflicts](#types-of-conflicts)
3. [Manual Conflict Resolution](#manual-conflict-resolution)
4. [Using VS Code to Resolve Conflicts](#using-vs-code-to-resolve-conflicts)
5. [Using Command Line Tools](#using-command-line-tools)
6. [Using git rerere](#using-git-rerere)
7. [Merge Conflict vs Rebase Conflict](#merge-conflict-vs-rebase-conflict)
8. [Complex Conflict Scenarios](#complex-conflict-scenarios)
9. [Conflict Prevention Strategies](#conflict-prevention-strategies)
10. [Abort Merge/Rebase Operations](#abort-mergerebase-operations)
11. [Git Conflict Resolution Tools Comparison](#git-conflict-resolution-tools-comparison)
12. [Team Conflict Resolution Workflow](#team-conflict-resolution-workflow)
13. [Common Conflict Issues](#common-conflict-issues)

---

## What is Merge Conflict

### Root Cause of Conflicts

Git Merge Conflict occurs when Git cannot automatically determine how to merge certain changes when merging two branches, requiring developer manual intervention.

Core conditions for conflicts:

```
Timeline:

Branch A (main):     a --- b --- c --- f (you modified file.txt)
                                    \
Branch B (feature):   x --- y --- z --- e (others also modified same area of file.txt)
```

When the following conditions are met, Git cannot automatically merge:

1. **Two branches modified the same area of the same file**
2. **One branch deleted a file, another branch modified that file**
3. **Two branches added files with same name but different content**

### Git Merge Underlying Principle

Git uses Three-way Merge algorithm to merge branches. Three-way merge requires three versions:

```
           ┌─────────────┐
           │  Common Ancestor (Base)  │
           │  merge-base  │
           └───────┬─────┘
                   │
          ┌────────┴────────┐
          │                 │
   ┌──────▼──────┐   ┌─────▼───────┐
   │  Current Branch (Ours) │   │  Target Branch (Theirs) │
   │    HEAD      │   │   branch    │
   └─────────────┘   └─────────────┘
```

Git decides how to merge by comparing the three versions:

- If only Ours modified a line → Use Ours' modification
- If only Theirs modified a line → Use Theirs' modification
- If both modified the same line → **Conflict occurs, manual resolution needed**

### Conflict Markers Details

When conflicts occur, Git inserts special conflict markers in the conflict file to identify conflict areas:

```plaintext
This is a normal line, no conflict.

<<<<<<< HEAD
This is the current branch (your) modification.
You can see changes made by HEAD pointing branch.
=======
This is the incoming branch (their) modification.
You can see changes made by incoming branch.
>>>>>>> feature

This is another normal line.
```

**Conflict Marker Explanation:**

| Marker | Meaning |
|--------|---------|
| `<<<<<<< HEAD` | Start of current branch content |
| `=======` | Separator between two versions |
| `>>>>>>> feature` | End of incoming branch content |

---

## Types of Conflicts

### Content Conflict

The most common type, two branches modified the same line.

```plaintext
<<<<<<< HEAD
console.log("Hello World");
=======
console.log("Hello Git");
>>>>>>> feature
```

### Delete Conflict

One branch deleted a file, another branch modified that file.

```bash
# Conflict message
CONFLICT (modify/delete): file.txt deleted in feature and modified in HEAD.
```

### Add Conflict

Two branches added files with same name but different content.

```bash
# Conflict message
CONFLICT (add/add): Merge conflict in file.txt
```

### Rename Conflict

One branch renamed a file, another branch modified the original file.

```bash
# Conflict message
CONFLICT (rename/delete): file.txt renamed in feature but deleted in HEAD
```

---

## Manual Conflict Resolution

### Step 1: View Conflict Status

```bash
# View conflicted files
git status

# Output example:
# Unmerged paths:
#   (use "git add <file>..." to mark resolution)
#         both modified:   file.txt
```

### Step 2: Edit Conflict File

Open the conflicted file and look for conflict markers:

```plaintext
Normal content...

<<<<<<< HEAD
Current branch content
=======
Incoming branch content
>>>>>>> feature

More normal content...
```

### Step 3: Resolve Conflict

Choose which content to keep, or combine both:

**Option A: Keep current branch (HEAD)**
```plaintext
Current branch content
```

**Option B: Keep incoming branch (feature)**
```plaintext
Incoming branch content
```

**Option C: Combine both**
```plaintext
Combined content from both branches
```

### Step 4: Remove Conflict Markers

Delete all conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).

### Step 5: Stage Resolved File

```bash
git add file.txt
```

### Step 6: Continue Operation

```bash
# If merging
git commit

# If rebasing
git rebase --continue
```

---

## Using VS Code to Resolve Conflicts

VS Code provides a visual conflict resolution interface.

### Step 1: Open Conflicted File

VS Code will highlight conflict areas.

### Step 2: Use Conflict Resolution Buttons

```
┌─────────────────────────────────────────────┐
│  <<<<<<< HEAD                               │
│  Current branch content                     │
│  =======                                    │
│  Incoming branch content                    │
│  >>>>>>> feature                            │
│                                             │
│  [Accept Current] [Accept Incoming] [Accept Both] │
└─────────────────────────────────────────────┘
```

### Step 3: Click Resolution Button

- **Accept Current**: Keep current branch content
- **Accept Incoming**: Keep incoming branch content
- **Accept Both**: Keep both contents

### Step 4: Stage and Continue

```bash
git add file.txt
git commit
```

---

## Using Command Line Tools

### Using git mergetool

```bash
# Configure merge tool
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# Launch merge tool
git mergetool
```

### Using git checkout

```bash
# Choose specific version
git checkout --ours file.txt    # Keep current branch
git checkout --theirs file.txt  # Keep incoming branch
```

---

## Using git rerere

`git rerere` (Reuse Recorded Resolution) automatically records conflict resolutions.

### Enable git rerere

```bash
# Enable rerere
git config --global rerere.enabled true
```

### How git rerere Works

1. First time you resolve a conflict, rerere records the resolution
2. Next time same conflict occurs, rerere automatically applies the recorded resolution

### View Recorded Resolutions

```bash
# View recorded resolutions
git rerere status

# View resolution details
git rerere diff
```

---

## Merge Conflict vs Rebase Conflict

### Merge Conflict

```bash
# During merge
git checkout main
git merge feature
# Conflict occurs

# Resolve and continue
git add resolved-file.txt
git commit
```

### Rebase Conflict

```bash
# During rebase
git checkout feature
git rebase main
# Conflict occurs

# Resolve and continue
git add resolved-file.txt
git rebase --continue
```

### Key Difference

| Aspect | Merge | Rebase |
|--------|-------|--------|
| Conflict Resolution | Once | Multiple times (once per commit) |
| History | Non-linear | Linear |
| Commit Rewriting | No | Yes |

---

## Complex Conflict Scenarios

### Binary File Conflicts

Binary files (images, videos, etc.) cannot be merged automatically.

```bash
# Choose which version to keep
git checkout --ours image.png
git checkout --theirs image.png

# Stage
git add image.png
git commit
```

### Multiple File Conflicts

```bash
# View all conflicted files
git diff --name-only --diff-filter=U

# Resolve each file
# ...

# Stage all resolved files
git add .

# Continue
git commit
```

### Submodule Conflicts

```bash
# Update submodules
git submodule update --init --recursive

# Resolve submodule conflicts
cd submodule-directory
# Resolve conflicts in submodule
git add .
git commit

# Go back to parent
cd ..
git add submodule-directory
git commit
```

---

## Conflict Prevention Strategies

### 1. Pull Frequently

```bash
# Pull latest changes regularly
git pull origin main
```

### 2. Use Feature Branches

```bash
# Create feature branch
git checkout -b feature/new-feature

# Work on feature branch
# ...

# Merge back to main
git checkout main
git merge feature/new-feature
```

### 3. Communicate with Team

- Coordinate who works on which files
- Use different files for different features
- Regular team syncs

### 4. Use Small Commits

```bash
# Make frequent, small commits
git add specific-file.txt
git commit -m "feat: specific change"
```

### 5. Keep Branches Short-Lived

```bash
# Merge branches frequently
git checkout main
git merge feature/short-lived-branch
```

---

## Abort Merge/Rebase Operations

### Abort Merge

```bash
# Abort merge
git merge --abort

# Or
git reset --hard HEAD
```

### Abort Rebase

```bash
# Abort rebase
git rebase --abort

# Or
git reset --hard ORIG_HEAD
```

---

## Git Conflict Resolution Tools Comparison

| Tool | Type | Features |
|------|------|----------|
| VS Code | IDE Integration | Visual, easy to use |
| GitKraken | GUI Tool | Graphical interface |
| Beyond Compare | External Tool | Powerful comparison |
| Vimdiff | Terminal | Fast, lightweight |
| Meld | GUI Tool | Simple, intuitive |

---

## Team Conflict Resolution Workflow

### Standard Process

1. **Identify Conflict**: Run `git status`
2. **Analyze Conflict**: Understand what changed
3. **Resolve Conflict**: Choose appropriate resolution
4. **Test Code**: Ensure code works
5. **Commit Resolution**: `git add` and `git commit`
6. **Communicate**: Inform team about resolution

### Conflict Resolution Checklist

- [ ] Understand both versions of changes
- [ ] Choose appropriate resolution strategy
- [ ] Remove all conflict markers
- [ ] Test code after resolution
- [ ] Commit with clear message
- [ ] Communicate with team if needed

---

## Common Conflict Issues

### Q: Accidentally committed conflict markers?

**Solution:**
```bash
# Undo last commit
git reset --soft HEAD~1

# Re-resolve conflict
# ...

# Commit again
git add .
git commit -m "Resolve conflict properly"
```

### Q: How to resolve conflicts in specific lines?

**Solution:**
```bash
# Edit file manually
vim conflicted-file.txt

# Find conflict markers
# Choose which content to keep
# Remove markers

# Stage and commit
git add conflicted-file.txt
git commit
```

### Q: How to see what caused the conflict?

**Solution:**
```bash
# View merge base
git merge-base HEAD feature

# View common ancestor
git show $(git merge-base HEAD feature):file.txt

# View both versions
git show HEAD:file.txt
git show feature:file.txt
```

---

## Best Practices

1. **Pull frequently**: Stay up to date with main branch
2. **Use feature branches**: Isolate changes
3. **Communicate with team**: Coordinate work
4. **Test after resolution**: Ensure code works
5. **Use visual tools**: VS Code, GitKraken, etc.
6. **Learn rerere**: Automate conflict resolution
7. **Keep calm**: Conflicts are normal

---

**Next: [Undo Operations →](13-undo.md)**