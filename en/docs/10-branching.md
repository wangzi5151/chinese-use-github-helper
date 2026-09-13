# Branch Operations

## Basic Concepts

Branches are independent lines for parallel development. The main branch is usually `main` (or `master`).

```
main:     A --- B --- C
                   \
feature:            D --- E
```

**Simple Understanding:**
- **main branch**: Main branch, stable version
- **feature branch**: Feature branch, develop new features
- **bugfix branch**: Fix branch, fix bugs
- **hotfix branch**: Emergency fix branch

## View Branches on GitHub Web

### View All Branches

1. Open repository page
2. Click branch dropdown (usually shows `main`)

```
┌─────────────────────────────────────────────┐
│  Code  Issues  Pull requests                │
├─────────────────────────────────────────────┤
│                                             │
│  [main ▼]  ← Click this dropdown           │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │ Switch branches/tags                │    │
│  │                                     │    │
│  │ Branches                            │    │
│  │   main                              │    │
│  │   feature-login                     │    │
│  │   feature-new-button                │    │
│  │                                     │    │
│  │ Tags                                │    │
│  │   v1.0.0                            │    │
│  │   v1.0.1                            │    │
│  └─────────────────────────────────────┘    │
│                                             │
└─────────────────────────────────────────────┘
```

### Create Branch on GitHub

**Method 1: Create on Repository Page**

1. Click branch dropdown
2. Enter new branch name in search box
3. Click **Create branch: xxx from main**

```
┌─────────────────────────────────────┐
│  Switch branches/tags                │
│                                     │
│  Find or create a branch...         │
│  ┌─────────────────────────────┐    │
│  │ feature-new-button          │    │
│  └─────────────────────────────┘    │
│                                     │
│  ✓ Create branch:                   │
│    feature-new-button from main     │
└─────────────────────────────────────┘
```

**Method 2: Auto-create after Push**

When you push a new branch to remote, GitHub will show PR creation prompt:

```
┌─────────────────────────────────────────────┐
│  feature-new-button had recent pushes       │
│                                             │
│  [Compare & pull request]  ← Click to create PR │
└─────────────────────────────────────────────┘
```

---

## Git Branch Commands

### View Branches

```bash
# View local branches
git branch

# View all branches (local + remote)
git branch -a

# View remote branches
git branch -r

# View current branch
git branch --show-current

# View last commit of each branch
git branch -v
```

### Create Branch

```bash
# Create new branch
git branch feature-login

# Create and switch to new branch
git checkout -b feature-login

# Or using switch (Git 2.23+)
git switch -c feature-login

# Create branch from specific commit
git branch feature-login abc1234

# Create branch from another branch
git branch feature-login develop
```

### Switch Branch

```bash
# Switch to branch
git checkout feature-login

# Or using switch (Git 2.23+)
git switch feature-login

# Switch to previous branch
git checkout -
git switch -
```

### Delete Branch

```bash
# Delete merged branch
git branch -d feature-login

# Force delete branch
git branch -D feature-login

# Delete remote branch
git push origin --delete feature-login

# Or
git push origin :feature-login
```

### Rename Branch

```bash
# Rename current branch
git branch -m new-name

# Rename specific branch
git branch -m old-name new-name

# Rename remote branch
git push origin :old-name
git push origin new-name
```

---

## Branch Workflow

### Feature Branch Workflow

```
main:     A --- B --- C --- F --- G
                   \         \
feature:            D --- E --- H
```

```bash
# Create feature branch
git checkout -b feature-login main

# Work on feature
# ...

# Commit changes
git add .
git commit -m "feat: add login feature"

# Push to remote
git push origin feature-login

# Create PR on GitHub
# After review, merge PR
```

### Gitflow Workflow

```
main:     A ------- F ------- H
           \       /           \
develop:    B --- C --- D --- E --- G
               \           /
feature:        I --- J --- K
```

**Branches:**
- `main`: Production code
- `develop`: Development code
- `feature/*`: Feature branches
- `release/*`: Release branches
- `hotfix/*`: Hotfix branches

---

## Branch Strategies

### GitHub Flow

Simple workflow suitable for most projects:

1. Create branch from `main`
2. Work on branch
3. Create Pull Request
4. Review and merge
5. Deploy from `main`

### Gitflow Workflow

Complex workflow for release-based projects:

1. `main` for production
2. `develop` for development
3. `feature/*` for features
4. `release/*` for releases
5. `hotfix/*` for hotfixes

### Trunk-Based Development

All developers work on `main`:

1. Short-lived branches
2. Frequent integration
3. Feature flags

---

## Branch Protection

### Configure Branch Protection on GitHub

1. Go to repository Settings
2. Click Branches
3. Click Add rule
4. Configure protection rules

```
┌─────────────────────────────────────────────┐
│  Branch protection rule                     │
│                                             │
│  Branch name pattern: main                  │
│                                             │
│  ☑ Require pull request before merging      │
│    ☑ Require approvals: 2                   │
│  ☑ Require status checks to pass            │
│  ☑ Require branches to be up to date        │
│  ☑ Include administrators                    │
│                                             │
│  [Create]                                   │
└─────────────────────────────────────────────┘
```

### Protection Rules

- **Require PR**: Must create PR before merging
- **Require Reviews**: Must have approvals before merging
- **Require Status Checks**: CI must pass before merging
- **Restrict Pushes**: Only specific people can push
- **Restrict Force Push**: Prevent force push

---

## Merge Branches

### Fast-Forward Merge

```bash
# Fast-forward merge
git checkout main
git merge feature-login
```

```
Before:
main:     A --- B
feature:        C --- D

After:
main:     A --- B --- C --- D
```

### Three-Way Merge

```bash
# Three-way merge
git checkout main
git merge feature-login
```

```
Before:
main:     A --- B --- E
feature:        C --- D

After:
main:     A --- B --- E --- F
                \       /
feature:        C --- D
```

### Squash Merge

```bash
# Squash merge
git checkout main
git merge --squash feature-login
git commit -m "feat: add login feature"
```

### Rebase

```bash
# Rebase feature branch
git checkout feature-login
git rebase main

# Interactive rebase
git rebase -i HEAD~3
```

---

## Resolve Conflicts

### When Conflicts Occur

Conflicts occur when:
- Two branches modify the same line
- One branch deletes a file another modifies
- Two branches add the same file

### View Conflicts

```bash
# View conflict status
git status

# View conflict content
git diff
```

### Resolve Conflicts

```bash
# Edit conflict file
# Look for conflict markers:
# <<<<<<< HEAD
# Current branch content
# =======
# Incoming branch content
# >>>>>>> feature-login

# After resolving
git add resolved-file.txt
git commit -m "Resolve merge conflict"
```

### Abort Merge

```bash
# Abort merge
git merge --abort
```

---

## Branch Best Practices

1. **Use descriptive names**: `feature/user-login`, `bugfix/issue-123`
2. **Keep branches short-lived**: Merge frequently
3. **Delete merged branches**: Keep repository clean
4. **Use branch protection**: Protect important branches
5. **Follow naming conventions**: Consistent branch naming

---

**Next: [Merge and Rebase →](11-merge-rebase.md)**