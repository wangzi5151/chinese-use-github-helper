# Git Visualization Guide (Illustrated)

## Git Three Areas

```
┌─────────────────────────────────────────────────────────────┐
│                     Working Directory                        │
│                  (Working Directory)                         │
│                                                             │
│   Where you edit files, all modifications start here        │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          │  git add
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                      Staging Area                            │
│                    (Staging Area)                             │
│                                                             │
│   Changes ready to commit, like a "draft area"              │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          │  git commit
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                      Repository                              │
│                    (Repository)                               │
│                                                             │
│   Committed history records, permanently saved              │
└─────────────────────────────────────────────────────────────┘
```

## File State Transitions

```
                    New File
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│                    Untracked                                  │
│               (Untracked State)                              │
│                                                             │
│   Git doesn't know about this file                          │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          │  git add
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                     Staged                                    │
│                 (Staged State)                                │
│                                                             │
│   File is ready to be committed                             │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          │  git commit
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                   Committed                                   │
│                 (Committed State)                             │
│                                                             │
│   Changes saved to repository history                       │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          │  Modify file
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                    Modified                                   │
│                 (Modified State)                              │
│                                                             │
│   File modified but not yet staged                          │
└─────────────────────────────────────────────────────────────┘
```

## Branch Model

```
main:      A ─── B ─── C ──────────── F (After merge)
            \             \         /
             \             D ─── E  (Feature branch)
              \           /
               ──────── G (Another branch)
```

### Branch Operations

| Operation | Command | Effect |
|-----------|---------|--------|
| Create branch | `git branch feature` | Create new pointer |
| Switch branch | `git checkout feature` | HEAD points to new branch |
| Merge branch | `git merge feature` | Create merge commit |
| Delete branch | `git branch -d feature` | Delete pointer |

## Merge Process

### Fast-Forward Merge

```
Before merge:
main:    A ─── B ─── C
                      \
feature:               D ─── E

Execute: git checkout main && git merge feature

After merge:
main:    A ─── B ─── C ─── D ─── E
                              ↑
                          (Fast-forward)
```

### 3-Way Merge

```
Before merge:
main:    A ─── B ─── C ─── F
                      \
feature:               D ─── E

Execute: git checkout main && git merge feature

After merge:
main:    A ─── B ─── C ─── F ─── M (Merge commit)
                      \         /
feature:               D ─── E
```

## Rebase Process

```
Before rebase:
main:    A ─── B ─── C
                      \
feature:               D ─── E

Execute: git checkout feature && git rebase main

After rebase:
main:    A ─── B ─── C
                      \
feature:               D' ─── E'  (New commits)
```

**Note: Rebase creates new commits (D' and E'), different from original D and E**

## Remote Tracking

```
Local Repository                    Remote Repository (origin)
┌─────────────┐                ┌─────────────┐
│             │                │             │
│   main ─────┼────────────────┼─── main    │
│             │   git push     │             │
│   feature ──┼────────────────┼─── feature │
│             │                │             │
└─────────────┘                └─────────────┘

git fetch: Sync remote branch info
git pull:  fetch + merge
```

## Common Commands Illustrated

### Commit Process

```
Modify file
    │
    ▼
git status  ──── View which files were modified
    │
    ▼
git add .   ──── Stage all changes
    │
    ▼
git status  ──── Confirm staged files
    │
    ▼
git commit -m "message"  ──── Commit to repository
    │
    ▼
git push    ──── Push to remote
```

### Branch Workflow

```
1. git checkout main          ──── Switch to main branch
2. git pull                   ──── Get latest code
3. git checkout -b feature    ──── Create feature branch
4. [Write code]               ──── Develop
5. git add .                  ──── Stage changes
6. git commit -m "feat: xxx"  ──── Commit changes
7. git push -u origin feature ──── Push branch
8. [Create Pull Request]      ──── Request merge
9. [Code Review]              ──── Review code
10. git checkout main         ──── Switch back to main
11. git merge feature         ──── Merge feature branch
12. git push                  ──── Push to remote
13. git branch -d feature     ──── Delete feature branch
```

## Conflict Illustration

```
When conflict occurs:
┌─────────────────────────────────────────────────┐
│ <<<<<<< HEAD                                      │
│ This is current branch's modification             │
│ =======                                           │
│ This is incoming branch's modification            │
│ >>>>>>> feature                                   │
└─────────────────────────────────────────────────┘

Resolution method:
1. Manually edit file, choose content to keep
2. Remove conflict markers (<<<<<<, =======, >>>>>>>)
3. git add to mark as resolved
4. git commit to complete merge
```

## Commit History Illustration

### Linear History

```
A ─── B ─── C ─── D ─── E  (main)
```

### Branch History

```
A ─── B ─── C ─── F ─── G  (main)
      \         /
       D ─── E            (feature)
```

### View History Commands

```bash
# Concise mode
git log --oneline
# Output: E G F C B A

# Graphical mode
git log --oneline --graph --all
# Output:
# * E (main)
# * G
# |\
# | * F
# |/
# * C
# |\
# | * D
# |/
# * B
# * A
```

## Summary Diagram

```
┌──────────────────────────────────────────────────────────────────┐
│                         Git Workflow                              │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Working Directory ──(git add)──→ Staging Area ──(git commit)──→ Repository │
│     ↑                                              │            │
│     │                                              │            │
│     └──────────────(git checkout/restore)───────────┘            │
│                                                                  │
│   Local Repository ──(git push)──→ Remote Repository            │
│     ↑                                              │            │
│     │                                              │            │
│     └──────────────(git pull)───────────────────────┘            │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```