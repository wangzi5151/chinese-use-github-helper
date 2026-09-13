# Git Alias Configuration

## Common Aliases

```bash
# Basic operations
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.sw switch

# Log
git config --global alias.lg "log --oneline --graph --all"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.today "log --since='1 day ago' --oneline"

# Diff
git config --global alias.df "diff"
git config --global alias.dfs "diff --staged"

# Unstage
git config --global alias.unstage "reset HEAD --"

# Undo
git config --global alias.undo "reset --soft HEAD~1"

# Cleanup
git config --global alias.cleanup "!git branch --merged | grep -v '\\*\\|main\\|master' | xargs -n 1 git branch -d"
```

## Useful Combined Aliases

```bash
# Show last commit of all branches
git config --global alias.branches "branch -a --v"

# Show concise status
git config --global alias.s "status -sb"

# Add all changes and show status
git config --global alias.aa "!git add -A && git status"

# Commit all changes
git config --global alias.ac "!git add -A && git commit"

# Push to current branch
git config --global alias.ps "push origin HEAD"

# Pull and rebase
git config --global alias.pull "!git pull --rebase"

# Show Git aliases
git config --global alias.aliases "config --get-regexp alias"
```

## View Aliases

```bash
# List all aliases
git config --global --list | grep alias

# Or directly view config file
cat ~/.gitconfig
```

## Remove Alias

```bash
git config --global --unset alias.st
```

## Configuration File

Directly edit `~/.gitconfig`:

```ini
[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
    lg = log --oneline --graph --all
    last = log -1 HEAD --stat
    unstage = reset HEAD --
    undo = reset --soft HEAD~1
    s = status -sb
```

## Alias Scripts

When creating complex aliases, can use functions:

```bash
git config --global alias.cleanup "!f() { git branch --merged | grep -v '\\*\\|main\\|master' | xargs -n 1 git branch -d; }; f"
```

## Recommended Configuration

```bash
# Complete recommended alias configuration
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.sw switch
git config --global alias.lg "log --oneline --graph --all"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.df "diff"
git config --global alias.dfs "diff --staged"
git config --global alias.unstage "reset HEAD --"
git config --global alias.undo "reset --soft HEAD~1"
git config --global alias.s "status -sb"
git config --global alias.aa "!git add -A && git status"
git config --global alias.ac "!git add -A && git commit"
git config --global alias.ps "push origin HEAD"
git config --global alias.pull "!git pull --rebase"
```