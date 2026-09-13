# Common Commands Quick Reference

## Configuration

```bash
git config --global user.name "Name"
git config --global user.email "email@example.com"
git config --global init.defaultBranch main
git config --global core.editor "code --wait"
```

## Create and Clone

```bash
git init                          # Initialize repository
git clone <url>                   # Clone repository
git clone <url> <dir>             # Clone to specified directory
git clone --depth 1 <url>         # Shallow clone
```

## Basic Operations

```bash
git status                        # View status
git add <file>                    # Add file
git add .                         # Add all files
git commit -m "message"           # Commit
git commit -am "message"          # Add and commit (tracked files)
```

## Branches

```bash
git branch                        # View branches
git branch <name>                 # Create branch
git checkout <branch>             # Switch branch
git checkout -b <branch>          # Create and switch
git switch <branch>               # Switch branch (new syntax)
git switch -c <branch>            # Create and switch (new syntax)
git branch -d <branch>            # Delete branch
git branch -D <branch>            # Force delete
```

## Merge and Rebase

```bash
git merge <branch>                # Merge branch
git rebase <branch>               # Rebase
git merge --no-ff <branch>        # Disable fast-forward merge
git merge --abort                 # Abort merge
git rebase --abort                # Abort rebase
```

## Remote Operations

```bash
git remote -v                     # View remotes
git remote add <name> <url>       # Add remote
git remote remove <name>          # Remove remote
git push -u origin <branch>       # Push and set upstream
git push                          # Push
git pull                          # Pull
git fetch                         # Fetch
git push --force                  # Force push (dangerous!)
```

## View History

```bash
git log                           # View log
git log --oneline                 # Concise log
git log --oneline --graph --all   # Graphical log
git log -5                        # Last 5 commits
git diff                          # View differences
git diff --staged                 # Staged differences
git show <commit>                 # View commit
```

## Undo Operations

```bash
git reset --soft HEAD~1           # Soft reset (keep changes)
git reset HEAD~1                  # Mixed reset (default)
git reset --hard HEAD~1           # Hard reset (discard changes)
git revert <commit>               # Revert commit
git checkout -- <file>            # Restore file
git reset HEAD <file>             # Unstage
git commit --amend                # Amend last commit
```

## Tags

```bash
git tag <name>                    # Create tag
git tag -a <name> -m "message"    # Create annotated tag
git push origin <tag>             # Push tag
git push origin --tags            # Push all tags
git tag -d <name>                 # Delete tag
```

## Stash

```bash
git stash                         # Stash changes
git stash list                    # View stash list
git stash pop                     # Restore stash
git stash apply                   # Restore (keep stash)
git stash drop                    # Delete stash
```

## Cleanup

```bash
git clean -f                      # Delete untracked files
git clean -fd                     # Delete untracked files and directories
git gc                            # Garbage collection
```

## GitHub CLI

```bash
gh repo create <name>             # Create repository
gh repo clone <user>/<repo>       # Clone repository
gh issue create                   # Create Issue
gh issue list                     # List Issues
gh pr create                      # Create PR
gh pr list                        # List PRs
gh pr merge <number>              # Merge PR
gh release create <tag>           # Create Release
```