# Merge and Rebase

## What are Merge and Rebase?

- **Merge**: Combine changes from two branches together
- **Rebase**: "Replay" current branch commits onto target branch

## Merge on GitHub Web

### Method 1: Merge via PR (Recommended)

This is the safest merge method, suitable for team collaboration.

**Step 1: Create PR**
1. Push feature branch to remote
2. Click **Compare & pull request** on repository page

**Step 2: Review Code**
1. View code differences
2. Add comments
3. Click **Approve**

**Step 3: Merge PR**
1. Find merge button at bottom of PR page
2. Select merge method
3. Click **Merge pull request**
4. Click **Confirm merge**

```
┌─────────────────────────────────────────────┐
│  Merge pull request                          │
│                                             │
│  All checks have passed                      │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │  ○ Create a merge commit            │    │
│  │    Preserve complete commit history  │    │
│  │                                     │    │
│  │  ○ Squash and merge                 │    │
│  │    Squash into one commit           │    │
│  │                                     │    │
│  │  ○ Rebase and merge                 │    │
│  │    Linear history                   │    │
│  └─────────────────────────────────────┘    │
│                                             │
│  Merge message:                             │
│  ┌─────────────────────────────────────┐    │
│  │ feat: add new button feature        │    │
│  └─────────────────────────────────────┘    │
│                                             │
│        [Merge pull request]                 │
└─────────────────────────────────────────────┘
```

**Merge Method Description:**

| Method | Features | Use Case |
|--------|----------|----------|
| **Create a merge commit** | Preserve complete history | Default method, recommended |
| **Squash and merge** | Squash into one commit | Feature branch has many small commits |
| **Rebase and merge** | Linear history | Prefer clean history |

### Method 2: Direct Merge in Repository (Not Recommended)

⚠️ **Note: Direct merge to main branch may break branch protection rules**

## Merge in Terminal

### Fast-Forward Merge

When target branch has no new commits:

```bash
git checkout main
git merge feature
```

```
Before merge:
main:    A --- B --- C
feature:         D --- E

After merge:
main:    A --- B --- C --- D --- E
```

### Three-Way Merge

When both branches have new commits:

```bash
git checkout main
git merge feature
```

```
Before merge:
main:    A --- B --- C --- F
feature:         D --- E

After merge:
main:    A --- B --- C --- F --- G
                \             /
feature:         D --- E --- H
```

### Squash Merge

Squash all commits into one:

```bash
git checkout main
git merge --squash feature
git commit -m "feat: add new feature"
```

### Merge Options

```bash
# No fast-forward (always create merge commit)
git merge --no-ff feature

# Edit merge message
git merge --edit feature

# Abort merge
git merge --abort
```

---

## Rebase

### What is Rebase?

Rebase moves the base of a branch to a new position:

```
Before rebase:
main:    A --- B --- C
feature:         D --- E

After rebase:
main:    A --- B --- C
feature:              D' --- E'
```

### Basic Rebase

```bash
# Rebase feature branch onto main
git checkout feature
git rebase main

# Or
git rebase main feature
```

### Interactive Rebase

```bash
# Interactive rebase last 3 commits
git rebase -i HEAD~3

# Interactive rebase from specific commit
git rebase -i abc1234
```

Interactive rebase editor:

```
pick abc1234 feat: add login
pick def5678 fix: fix typo
pick ghi9012 feat: add register

# Rebase commands:
# p, pick = use commit
# r, reword = use commit, but edit commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command using shell
# d, drop = remove commit
```

### Rebase Options

```bash
# Continue rebase after resolving conflicts
git rebase --continue

# Abort rebase
git rebase --abort

# Skip current commit
git rebase --skip

# Autosquash (for fixup commits)
git rebase -i --autosquash HEAD~5
```

---

## Merge vs Rebase

| Feature | Merge | Rebase |
|---------|-------|--------|
| History | Non-linear, complete | Linear, clean |
| Commits | All commits preserved | Commits rewritten |
| Conflicts | Resolve once | May resolve multiple times |
| Safety | Safe | Can cause issues |
| Use Case | Team collaboration | Clean history |

### When to Use Merge

- Team collaboration
- Preserve complete history
- Shared branches
- Public branches

### When to Use Rebase

- Clean up local history
- Before merging to main
- Private branches
- Linear history preferred

---

## Cherry-Pick

### What is Cherry-Pick?

Cherry-pick applies specific commits from one branch to another:

```bash
# Cherry-pick specific commit
git checkout main
git cherry-pick abc1234

# Cherry-pick multiple commits
git cherry-pick abc1234 def5678

# Cherry-pick range
git cherry-pick abc1234..ghi9012
```

### Cherry-Pick Options

```bash
# No commit (only apply changes)
git cherry-pick --no-commit abc1234

# Edit commit message
git cherry-pick --edit abc1234

# Abort cherry-pick
git cherry-pick --abort
```

---

## Conflict Resolution

### When Conflicts Occur

```
<<<<<<< HEAD
Current branch content
=======
Incoming branch content
>>>>>>> feature
```

### Resolve Conflicts

1. Edit conflict file
2. Choose which content to keep
3. Remove conflict markers
4. Stage resolved file
5. Continue merge/rebase

```bash
# Edit file
vim conflicted-file.txt

# Stage resolved file
git add conflicted-file.txt

# Continue merge
git commit

# Or continue rebase
git rebase --continue
```

### Abort Operation

```bash
# Abort merge
git merge --abort

# Abort rebase
git rebase --abort
```

---

## Best Practices

1. **Use merge for shared branches**: Preserve history
2. **Use rebase for private branches**: Clean history
3. **Don't rebase public branches**: Can cause issues
4. **Test after rebase**: Ensure code works
5. **Communicate with team**: Coordinate merge/rebase strategy

---

**Next: [Resolve Conflicts →](12-resolve-conflicts.md)**