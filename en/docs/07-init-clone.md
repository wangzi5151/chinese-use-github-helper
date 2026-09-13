# Create and Clone Repositories Complete Guide

> This chapter is an in-depth tutorial on Git repository creation and cloning, covering various ways and advanced techniques for creating local repositories from scratch and cloning remote repositories. Whether you are a beginner or an experienced developer, you can find practical operation guides here.

---

## Table of Contents

- [git init Details](#git-init-details)
- [git clone Details](#git-clone-details)
- [Clone Method Comparison](#clone-method-comparison)
- [Shallow Clone and Partial Clone](#shallow-clone-and-partial-clone)
- [Clone Specific Branch](#clone-specific-branch)
- [Submodule Clone](#submodule-clone)
- [Git Mirror Repository](#git-mirror-repository)
- [China Acceleration Solutions](#china-acceleration-solutions)
- [Large Repository Optimization](#large-repository-optimization)
- [Repository Migration and Import](#repository-migration-and-import)
- [Bare Repository and Mirror](#bare-repository-and-mirror)
- [Common Clone Issues](#common-clone-issues)
- [Practice: Create Your First Repository](#practice-create-your-first-repository)

---

## git init Details

### What is git init

`git init` is one of the most basic commands in Git. Its purpose is to create a new Git repository in the current directory. After executing this command, Git will generate a hidden directory named `.git` in the current directory, which contains all metadata and configuration information needed for version control.

The internal structure of `.git` directory:

```
.git/
├── HEAD              # Pointer to current branch
├── config            # Repository level configuration file
├── description       # Repository description (for GitWeb)
├── hooks/            # Git hook scripts directory
├── info/             # Global exclude file information
├── objects/          # Store all data objects (commits, trees, blobs)
│   ├── info/
│   └── pack/
├── refs/             # Store branch and tag pointers
│   ├── heads/        # Local branches
│   └── tags/         # Tags
└── logs/             # Reference logs
```

### Basic Usage

```bash
# Initialize Git repository in current directory
git init

# Create new directory and initialize
git init my-project

# Initialize empty Git repository (without template files in .git directory)
git init --bare my-project.git
```

### Various Parameters of git init

```bash
# Specify initial branch name (Git 2.28+)
git init -b main
git init --initial-branch=main

# Use specified template directory
git init --template=/path/to/template

# Initialize repository as shared repository
git init --shared=group

# Re-initialize in existing directory (safe operation, won't overwrite existing data)
git init
```

---

## git clone Details

### What is git clone

`git clone` is used to create a local copy of a remote repository. It not only downloads all files, but also downloads the entire commit history, branches, tags, and all Git metadata.

### Basic Usage

```bash
# Clone via HTTPS
git clone https://github.com/user/repo.git

# Clone via SSH
git clone git@github.com:user/repo.git

# Clone to specified directory
git clone https://github.com/user/repo.git my-folder

# Clone using GitHub CLI
gh repo clone user/repo
```

### Clone Process

```
┌─────────────────────────────────────────────────────────────────┐
│                    Git Clone Process                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  1. Connect to remote server                                    │
│  2. Download all objects (commits, trees, blobs)                │
│  3. Create .git directory                                       │
│  4. Checkout working directory (latest version)                 │
│  5. Set remote tracking branches                                │
│  6. Set HEAD to default branch                                  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Clone Method Comparison

### HTTPS vs SSH vs GitHub CLI

| Feature | HTTPS | SSH | GitHub CLI |
|---------|-------|-----|------------|
| Setup | Easy | Medium | Easy |
| Authentication | Token/Password | SSH Key | Token |
| Security | Good | Best | Good |
| Speed | Fast | Fast | Fast |
| Behind Firewall | Yes | Sometimes | Yes |

### HTTPS Clone

```bash
# Clone via HTTPS
git clone https://github.com/user/repo.git

# Configure credential storage
git config --global credential.helper store
```

### SSH Clone

```bash
# Clone via SSH
git clone git@github.com:user/repo.git

# Test SSH connection
ssh -T git@github.com
```

### GitHub CLI Clone

```bash
# Install GitHub CLI
brew install gh  # macOS
winget install GitHub.cli  # Windows

# Login
gh auth login

# Clone
gh repo clone user/repo
```

---

## Shallow Clone and Partial Clone

### Shallow Clone

Shallow clone downloads only the latest commits, not the entire history.

```bash
# Clone with depth1
git clone --depth 1 https://github.com/user/repo.git

# Clone with depth10
git clone --depth 10 https://github.com/user/repo.git
```

**Use Cases:**
- CI/CD environments
- Quick testing
- Large repositories with long history

### Partial Clone

Partial clone downloads only needed objects.

```bash
# Clone without blobs (download on demand)
git clone --filter=blob:none https://github.com/user/repo.git

# Clone without trees
git clone --filter=tree:0 https://github.com/user/repo.git
```

---

## Clone Specific Branch

```bash
# Clone specific branch
git clone -b feature https://github.com/user/repo.git

# Clone and track specific branch
git clone -b feature --single-branch https://github.com/user/repo.git
```

---

## Submodule Clone

```bash
# Clone with submodules
git clone --recurse-submodules https://github.com/user/repo.git

# Initialize submodules after clone
git submodule init
git submodule update
```

---

## Git Mirror Repository

```bash
# Create bare mirror
git clone --mirror https://github.com/user/repo.git

# Push mirror to new remote
cd repo.git
git push --mirror https://github.com/new-user/new-repo.git
```

---

## China Acceleration Solutions

### Using Proxy

```bash
# Configure HTTP proxy
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890

# Configure SOCKS5 proxy
git config --global http.proxy socks5://127.0.0.1:7890
git config --global https.proxy socks5://127.0.0.1:7890
```

### Using Mirror

```bash
# Clone from Gitee mirror
git clone https://gitee.com/mirrors/repo.git

# Using ghproxy
git clone https://ghproxy.com/https://github.com/user/repo.git
```

### Configure SSH Acceleration

```bash
# Edit ~/.ssh/config
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    ProxyCommand nc -v -x 127.0.0.1:7890 %h %p
```

---

## Large Repository Optimization

### Git LFS

```bash
# Install Git LFS
git lfs install

# Track large files
git lfs track "*.psd"
git lfs track "*.zip"
git lfs track "*.mp4"

# Clone with LFS
git lfs clone https://github.com/user/repo.git
```

### Sparse Checkout

```bash
# Enable sparse checkout
git clone --sparse https://github.com/user/repo.git
cd repo
git sparse-checkout init
git sparse-checkout set dir1 dir2
```

---

## Repository Migration and Import

### Import Existing Project

```bash
# Navigate to project directory
cd my-project

# Initialize Git
git init

# Add all files
git add .

# Initial commit
git commit -m "Initial commit"

# Add remote
git remote add origin https://github.com/user/repo.git

# Push
git push -u origin main
```

### Import from Other VCS

```bash
# Import from SVN
git svn clone https://svn.example.com/repo

# Import from Mercurial
hg fast-export --import-marks=.git/hg-export.marks | git fast-import
```

---

## Bare Repository and Mirror

### Bare Repository

A bare repository has no working directory, only the .git contents.

```bash
# Create bare repository
git init --bare my-repo.git

# Use cases:
# - Central server repository
# - Shared repository
# - CI/CD triggers
```

### Mirror Repository

```bash
# Clone as mirror
git clone --mirror https://github.com/user/repo.git

# Push mirror
git push --mirror https://github.com/new-user/new-repo.git
```

---

## Common Clone Issues

### Q: Clone timeout?

**Solution:**
```bash
# Increase buffer size
git config --global http.postBuffer 524288000

# Configure proxy
git config --global http.proxy http://127.0.0.1:7890

# Use shallow clone
git clone --depth 1 https://github.com/user/repo.git
```

### Q: Permission denied?

**Solution:**
```bash
# Check SSH key
ssh -T git@github.com

# Check credential
git config --list | grep credential
```

### Q: Repository too large?

**Solution:**
```bash
# Shallow clone
git clone --depth 1 https://github.com/user/repo.git

# Partial clone
git clone --filter=blob:none https://github.com/user/repo.git

# Sparse checkout
git clone --sparse https://github.com/user/repo.git
```

---

## Practice: Create Your First Repository

### Step 1: Create Repository on GitHub

1. Login to GitHub
2. Click **+** in top right corner
3. Select **New repository**
4. Enter repository name
5. Select **Public** or **Private**
6. Check **Add a README file**
7. Click **Create repository**

### Step 2: Clone Repository

```bash
# Clone repository
git clone https://github.com/your-username/my-first-repo.git

# Enter directory
cd my-first-repo
```

### Step 3: Edit Files

```bash
# Create new file
echo "# My Project" > README.md

# View status
git status
```

### Step 4: Commit Changes

```bash
# Add files
git add .

# Commit
git commit -m "Initial commit"

# Push
git push origin main
```

---

**Next: [Stage and Commit →](08-add-commit.md)**