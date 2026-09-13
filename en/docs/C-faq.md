# Frequently Asked Questions

## Basic Questions

### Q: What's the difference between Git and GitHub?
**A:** Git is version control software (local use), GitHub is a code hosting platform based on Git (online service).

### Q: How to change Git commit username and email?
**A:**
```bash
git config --global user.name "New Name"
git config --global user.email "new@email.com"
# Note: This won't modify historical commits, only applies to new commits
```

### Q: How to undo the last commit?
**A:**
```bash
# Keep changes
git reset --soft HEAD~1

# Discard changes
git reset --hard HEAD~1
```

### Q: How to view a file's modification history?
**A:**
```bash
git log --follow -p filename
```

## Branch Questions

### Q: How to delete a remote branch?
**A:**
```bash
git push origin --delete branch-name
```

### Q: How to merge changes from one branch to another?
**A:**
```bash
git checkout target-branch
git merge source-branch
```

### Q: How to switch branches without committing?
**A:**
```bash
# Stash changes
git stash

# Switch branch
git checkout other-branch

# Restore changes
git stash pop
```

## Remote Questions

### Q: How to change remote repository URL?
**A:**
```bash
git remote set-url origin new-url
```

### Q: How to sync a Fork?
**A:**
```bash
git remote add upstream original-url
git fetch upstream
git merge upstream/main
git push origin main
```

### Q: How to force push?
**A:**
```bash
git push --force origin branch
# Or safer option
git push --force-with-lease origin branch
```

## Conflict Questions

### Q: How to resolve merge conflicts?
**A:**
1. Open conflicted file
2. Find conflict markers `<<<<<<<` and `>>>>>>>`
3. Manually choose content to keep
4. Remove conflict markers
5. `git add` to mark as resolved
6. `git commit` to complete merge

### Q: How to cancel an ongoing merge?
**A:**
```bash
git merge --abort
```

### Q: How to cancel an ongoing rebase?
**A:**
```bash
git rebase --abort
```

## Recovery Questions

### Q: How to restore a deleted branch?
**A:**
```bash
# View reflog to find branch's last commit
git reflog
# Find commit hash
git checkout -b recovered-branch commit-hash
```

### Q: How to restore accidentally deleted file?
**A:**
```bash
# Restore to last commit state
git checkout HEAD -- filename

# Or use restore (Git 2.23+)
git restore filename
```

### Q: How to restore a pushed deletion?
**A:**
```bash
# Find commit before deletion
git reflog
# Restore
git checkout commit-hash -- filename
git commit -m "Restore file"
```

## Authentication Questions

### Q: How to avoid entering password every time?
**A:**
```bash
# Use SSH (recommended)
git clone git@github.com:user/repo.git

# Or use credential helper
git config --global credential.helper cache
```

### Q: How to use Token authentication?
**A:**
```bash
git clone https://<token>@github.com/user/repo.git
```

## Performance Questions

### Q: How to speed up large repository cloning?
**A:**
```bash
# Shallow clone
git clone --depth 1 url

# Clone specific branch only
git clone --single-branch --branch main url
```

### Q: How to clean up local repository?
**A:**
```bash
# Garbage collection
git gc

# Clean untracked files
git clean -fd
```

## Next Step

[Recommended Learning Resources →](D-resources.md)