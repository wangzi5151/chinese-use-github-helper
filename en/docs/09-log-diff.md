# View History and Differences: Git Log and Diff Complete Guide

> **Chapter Goal**: Master various commands and techniques for viewing commit history, comparing code differences, and tracing code origins in Git, becoming a code archaeology expert.

## Why View History?

In actual development, we often need to answer the following questions:

- When was this bug introduced?
- Who modified this line of code? Why?
- What's the difference between this branch and the main branch?
- What commits were made in the last week?
- What's the modification history of a file?

Git provides powerful history viewing and difference comparison tools to help us answer these questions.

---

## git log Details

`git log` is the core command for viewing commit history, showing all records from latest to earliest commit.

### Basic Usage

```bash
# View complete commit history (reverse chronological order)
git log

# View last N commits
git log -5
git log -n 10

# View history of specific file
git log -- path/to/file.txt
git log -- src/main.py
```

Default `git log` output includes the following information:

```
commit 7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d3e4f5a6b
Author: John Doe <johndoe@example.com>
Date:   Mon Sep 11 10:30:00 2026 +0800

    Fix user login validation bug

    Detailed description: When user password contains special characters,
    validation logic will have errors.
    Fix method: URL encode the password.

commit 1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b
Author: Jane Smith <janesmith@example.com>
Date:   Sun Sep 10 15:20:00 2026 +0800

    Add user registration feature
```

### Single Line Concise Mode

```bash
# Single line display, one commit per line
git log --oneline

# Output example:
# 7a8b9c0 Fix user login validation bug
# 1a2b3c4 Add user registration feature
# 5e6f7a8 Initial commit
```

`--oneline` compresses each commit into one line, showing only the first few characters of commit hash and commit message, very suitable for quickly browsing history.

### Graphical Branch Display

```bash
# Graphically display branch merge history
git log --oneline --graph

# Graphically display all branches
git log --oneline --graph --all

# Output example:
* 7a8b9c0 (HEAD -> main) Fix user login validation bug
* 1a2b3c4 Add user registration feature
| * 5e6f7a8 (feature) Add new feature
|/
* 9b0c1d2 Initial commit
```

### Log Filtering

```bash
# Filter by author
git log --author="John"
git log --author="John\|Jane"

# Filter by date
git log --since="2024-01-01"
git log --until="2024-12-31"
git log --since="2 weeks ago"

# Filter by commit message
git log --grep="fix"
git log --grep="feat" --grep="fix" --all-match

# Filter by file
git log -- filename.txt
git log -- src/

# Filter by content
git log -S "function_name"
git log -G "regex_pattern"
```

### Custom Log Format

```bash
# Custom format
git log --pretty=format:"%h - %an, %ar : %s"

# Format placeholders:
# %H - Commit hash
# %h - Abbreviated commit hash
# %an - Author name
# %ae - Author email
# %ar - Author date (relative)
# %s - Commit message
# %d - Ref names

# One line with graph and decoration
git log --oneline --graph --decorate --all
```

### View Specific Commit

```bash
# View specific commit details
git show <commit-hash>

# View specific commit file changes
git show <commit-hash> -- filename.txt

# View commit statistics
git show --stat <commit-hash>
```

---

## git diff Details

`git diff` is used to view differences between various states.

### Compare Working Directory and Staging Area

```bash
# View unstaged changes
git diff

# View specific file differences
git diff filename.txt
```

### Compare Staging Area and Repository

```bash
# View staged changes
git diff --staged
git diff --cached

# View specific staged file
git diff --staged filename.txt
```

### Compare Different Commits

```bash
# Compare two commits
git diff commit1 commit2

# Compare specific file between two commits
git diff commit1 commit2 -- filename.txt

# Compare current with specific commit
git diff HEAD~3
```

### Compare Branches

```bash
# Compare two branches
git diff main feature

# Compare specific file between branches
git diff main feature -- filename.txt

# View only file names
git diff --name-only main feature

# View statistics
git diff --stat main feature
```

### Diff Output Format

```bash
# Unified diff format
git diff

# Output example:
diff --git a/file.txt b/file.txt
index 1234567..890abcd 100644
--- a/file.txt
+++ b/file.txt
@@ -1,5 +1,6 @@
 Line 1
-Line 2
+Line 2 modified
+New line
 Line 3
```

### Diff Options

```bash
# Context lines
git diff -U5

# Word diff
git diff --word-diff

# Stat only
git diff --stat

# Name only
git diff --name-only

# Name status
git diff --name-status
```

---

## git blame

`git blame` shows who modified each line of a file and when.

### Basic Usage

```bash
# View file blame
git blame filename.txt

# View specific lines
git blame -L 10,20 filename.txt

# View with email
git blame -e filename.txt
```

### Output Example

```
^1a2b3c4 (John Doe 2024-01-15 10:30:00 +0800 1) # Title
^1a2b3c4 (John Doe 2024-01-15 10:30:00 +0800 2)
5e6f7a8b (Jane Smith 2024-02-20 14:15:00 +0800 3) ## Section
9b0c1d2e (John Doe 2024-03-10 09:45:00 +0800 4) Content here
```

### Blame Options

```bash
# Show line numbers
git blame -n filename.txt

# Show commit hash
git blame -s filename.txt

# Ignore whitespace
git blame -w filename.txt

# Detect line moves
git blame -M filename.txt

# Detect copied lines
git blame -C filename.txt
```

---

## git shortlog

`git shortlog` summarizes commit statistics.

### Basic Usage

```bash
# Summarize by author
git shortlog

# Count commits per author
git shortlog -sn

# Count since specific date
git shortlog --since="2024-01-01"
```

### Output Example

```
John Doe (15):
      Fix user login validation bug
      Add user registration feature
      Initial commit
      ...

Jane Smith (8):
      Update documentation
      Add tests
      ...
```

---

## git pickaxe

`git pickaxe` searches for commits that add or remove specific content.

### Usage

```bash
# Search for commits adding/removing string
git log -S "function_name"

# Search with regex
git log -G "regex_pattern"

# Search in specific file
git log -S "function_name" -- filename.txt
```

---

## Git Log Aliases

### Common Aliases

```bash
# Set aliases
git config --global alias.lg "log --oneline --graph --all --decorate"
git config --global alias.last "log -1 HEAD"
git config --global alias.visual "log --graph --oneline --all"

# Usage
git lg
git last
git visual
```

### Advanced Alias

```bash
# Pretty log
git config --global alias.tree "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

---

## Common Scenarios

### Find When Bug Was Introduced

```bash
# Binary search for bug
git bisect start
git bisect bad HEAD
git bisect good v1.0.0

# Git will checkout middle commit
# Test and mark as good or bad
git bisect good  # or git bisect bad

# Continue until found
git bisect reset
```

### View File Change History

```bash
# View file history
git log -- filename.txt

# View file changes per commit
git log -p -- filename.txt

# View file at specific commit
git show commit:filename.txt
```

### Compare Branches

```bash
# View branch differences
git log main..feature

# View commits in feature not in main
git log main..feature --oneline

# View commits in main not in feature
git log feature..main --oneline
```

---

## Best Practices

1. **Use aliases**: Set up convenient log aliases
2. **Review before commit**: Use `git diff` to review changes
3. **Use graphical view**: `--graph` helps understand branch structure
4. **Filter effectively**: Use `--author`, `--grep` to find specific commits
5. **Use blame wisely**: Understand code history without blaming others

---

**Next: [Branch Operations →](10-branching.md)**