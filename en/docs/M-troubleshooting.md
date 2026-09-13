# Common Error Troubleshooting Guide

## Git Errors

### 1. "Permission denied (publickey)"

**Cause**: SSH key configuration error

**Solution**:
```bash
# Check if SSH key exists
ls -al ~/.ssh

# Test SSH connection
ssh -T git@github.com

# If no key exists, generate new one
ssh-keygen -t ed25519 -C "your@email.com"

# Add public key to GitHub
cat ~/.ssh/id_ed25519.pub | pbcopy  # macOS
cat ~/.ssh/id_ed25519.pub | xclip -selection clipboard  # Linux
```

### 2. "fatal: remote origin already exists"

**Cause**: Remote named origin already exists

**Solution**:
```bash
# View remotes
git remote -v

# Change remote URL
git remote set-url origin git@github.com:user/repo.git

# Or remove and re-add
git remote remove origin
git remote add origin git@github.com:user/repo.git
```

### 3. "error: failed to push some refs"

**Cause**: Local branch inconsistent with remote branch

**Solution**:
```bash
# If sure you want to overwrite remote
git push --force origin branch

# Safer option
git push --force-with-lease origin branch
```

### 4. "CONFLICT (content): Merge conflict"

**Cause**: Conflict occurred during merge

**Solution**:
```bash
# 1. View conflicted files
git status

# 2. Manually edit conflicted files, resolve conflicts

# 3. Mark as resolved
git add .

# 4. Complete merge
git commit -m "Resolve merge conflict"
```

### 5. "fatal: refusing to merge unrelated histories"

**Cause**: Attempting to merge branches without common ancestor

**Solution**:
```bash
git merge --allow-unrelated-histories branch-name
```

### 6. "error: pathspec 'xxx' did not match"

**Cause**: File doesn't exist or wrong path

**Solution**:
```bash
# Check if file exists
ls -la

# Check current branch
git branch

# Ensure in correct directory
pwd
```

### 7. "warning: LF will be replaced by CRLF"

**Cause**: Line ending issue (Windows/Linux/Mac)

**Solution**:
```bash
# Windows users
git config --global core.autocrlf true

# Linux/Mac users
git config --global core.autocrlf input
```

## GitHub Errors

### 1. "403 Forbidden"

**Cause**: Insufficient permissions or API limits

**Solution**:
```bash
# Check auth status
gh auth status

# Re-login
gh auth login

# Check API limits
gh api rate_limit
```

### 2. "404 Not Found"

**Cause**: Repository doesn't exist or no access

**Solution**:
```bash
# Check if repository exists
gh repo view user/repo

# Check permissions
gh auth status

# If private repo, ensure logged in
gh auth login
```

### 3. "repository not found"

**Cause**: Wrong repository name or no access

**Solution**:
```bash
# Confirm repository name
gh repo list

# Check if logged in
gh auth status
```

### 4. "remote: Repository not found"

**Cause**: Remote repository doesn't exist

**Solution**:
```bash
# Check remote URL
git remote -v

# Change to correct URL
git remote set-url origin git@github.com:user/repo.git
```

## GitHub Actions Errors

### 1. "Error: Process completed with exit code 1"

**Cause**: Workflow step failed

**Solution**:
1. View failed step logs
2. Check if commands are correct
3. Check if environment variables are set

### 2. "Error: No space left on device"

**Cause**: Runner disk space insufficient

**Solution**:
```yaml
# Free disk space
- name: Free disk space
  uses: jlumbroso/free-disk-space@main
  with:
    tool-cache: false
    android: true
    dotnet: true
    haskell: true
    large-packages: true
    docker-images: true
    swap-storage: true
```

### 3. "Error: Timeout"

**Cause**: Workflow run timeout

**Solution**:
```yaml
# Increase timeout
jobs:
  build:
    runs-on: ubuntu-latest
    timeout-minutes: 30
```

### 4. "Error: The following actions uses node12"

**Cause**: Using outdated Action

**Solution**:
```yaml
# Use latest Action versions
- uses: actions/checkout@v4
- uses: actions/setup-node@v4
```

## Common Troubleshooting Steps

### 1. Check Git Configuration

```bash
# View all configuration
git config --list

# Check user info
git config user.name
git config user.email
```

### 2. Check Remote Repository

```bash
# View remotes
git remote -v

# Test connection
ssh -T git@github.com
```

### 3. Check Branch Status

```bash
# View current branch
git branch

# View all branches
git branch -a

# View status
git status
```

### 4. View Logs

```bash
# View recent commits
git log --oneline -10

# View detailed log
git log --stat
```

## Debugging Tips

### 1. Use Verbose Mode

```bash
# View detailed output
git -v push

# View HTTP details
GIT_CURL_VERBOSE=1 git push
```

### 2. Check Network Connection

```bash
# Test GitHub connection
ping github.com

# Test SSH connection
ssh -vT git@github.com
```

### 3. Check Git Version

```bash
git --version
```

## Getting Help

1. **GitHub Documentation**: https://docs.github.com
2. **Stack Overflow**: https://stackoverflow.com/questions/tagged/git
3. **GitHub Community**: https://github.com/community