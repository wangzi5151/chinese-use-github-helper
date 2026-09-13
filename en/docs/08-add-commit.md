# Git Stage and Commit: From Beginner to Expert

In Git's workflow, staging and committing are the two core operations. Understanding these concepts and mastering the usage of related commands is the foundation of becoming an efficient Git user. This chapter will deeply explain the working principle of Git staging area, various staging and commit command usage, and best practices for commit messages.

---

## Staging Area Concept Details

### Git's Three Trees Model

To understand the staging area, you first need to understand Git's Three Trees model. There are three core areas in a Git-managed project:

**Working Directory**
The working directory is the directory where you actually see and edit files on your computer. When you open a file with a text editor and make changes, these changes first exist in the working directory. Files in the working directory can be in tracked state (managed by Git) or untracked state (newly created, not yet managed by Git).

**Staging Area / Index**
The staging area is an intermediate area used to prepare the content for the next commit. It is essentially a file (usually located at `.git/index`) that records the files and changes that will be included in the next commit. The existence of the staging area is one of the important differences between Git and many other version control systems, allowing you to precisely control the content of each commit.

**Repository / HEAD**
The repository is where Git stores all commit history. The HEAD pointer points to the latest commit of the current branch. When you execute `git commit`, the content of the staging area is permanently recorded as a new commit object.

### Staging Area Workflow

File state transitions in Git follow this workflow:

```
Working Directory --[git add]--> Staging Area --[git commit]--> Repository
    ^                                                            |
    |_____________[git checkout / git reset]_____________________|
```

Specifically:

1. **Edit Files**: Edit files in the working directory, files become "modified" state
2. **Stage Changes**: Use `git add` to add changes to staging area, files become "staged" state
3. **Commit Changes**: Use `git commit` to commit staging area content to repository, files become "committed" state
4. **View Status**: Use `git status` to view file status in various areas at any time

### Why Staging Area is Needed

The staging area provides the following advantages:

**Precise Control of Commit Content**: You can selectively stage part of a file's changes instead of committing all modifications at once. This is useful for organizing related changes into the same commit.

**Review Opportunity Before Commit**: The staging area acts as a buffer, giving you the opportunity to check content about to be committed before committing, avoiding accidental commits of unnecessary changes.

**Support Partial Commits**: When a file contains multiple unrelated changes, you can use interactive staging to commit only part of them.

**Efficient Performance**: The staging area file format is optimized, allowing Git to quickly compare differences between working directory and staging area, improving status check efficiency.

### View Staging Area Status

Use `git status` command to view current staging area status:

```bash
git status
```

Output example:

```
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   src/app.js
        new file:   src/utils.js

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
        modified:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        docs/
```

The three parts in the output respectively represent:

- **Changes to be committed**: Staged changes that will be recorded in the next commit
- **Changes not staged for commit**: Modified but unstaged changes
- **Untracked files**: New files not yet tracked by Git

---

## Stage Files

### Stage Single File

```bash
# Stage specific file
git add filename.txt

# Stage specific directory
git add src/

# Stage all files
git add .
git add -A
git add --all
```

### Stage Multiple Files

```bash
# Stage multiple files
git add file1.txt file2.txt file3.txt

# Stage all .txt files
git add *.txt

# Stage all JavaScript files
git add *.js
```

### Stage Part of File

```bash
# Interactive staging
git add -p filename.txt

# Or
git add --patch filename.txt
```

When using `git add -p`, Git will show each change hunk and ask you what to do:

```bash
Stage this hunk [y,n,q,a,d,s,e,?]?
```

Options:
- `y`: Stage this hunk
- `n`: Don't stage this hunk
- `q`: Quit
- `a`: Stage this and all following hunks
- `d`: Don't stage this or any following hunks
- `s`: Split this hunk into smaller parts
- `e`: Manually edit this hunk

### Stage All Changes

```bash
# Stage all changes (including new files and deletions)
git add -A

# Stage all changes in current directory
git add .

# Stage all modifications and deletions (not new files)
git add -u
```

---

## Unstage Files

### Unstage Files

```bash
# Unstage file (keep changes in working directory)
git restore --staged filename.txt

# Or using reset (older method)
git reset HEAD filename.txt

# Unstage all files
git restore --staged .
```

### Unstage and Discard Changes

```bash
# Discard changes in working directory
git checkout -- filename.txt

# Or using restore
git restore filename.txt

# Discard all changes in working directory
git checkout -- .
git restore .
```

---

## Commit Changes

### Basic Commit

```bash
# Commit with message
git commit -m "Commit message"

# Commit all tracked file changes
git commit -am "Commit message"

# Commit with detailed message
git commit -m "Title" -m "Detailed description"
```

### Commit Message Best Practices

**Conventional Commits Format:**

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Code style (no logic change)
- `refactor`: Refactoring
- `test`: Testing
- `chore`: Build/tool changes
- `perf`: Performance optimization
- `ci`: CI configuration

**Examples:**

```bash
# Simple commit
git commit -m "feat: add user login feature"

# With scope
git commit -m "feat(auth): add OAuth2 support"

# With body
git commit -m "fix: fix login validation issue

- Fix email format validation
- Add password strength check

Closes #123"

# Breaking change
git commit -m "feat!: change API response format

BREAKING CHANGE: API response format changed from XML to JSON"
```

### Commit Message Rules

1. **Use imperative mood**: "Add feature" not "Added feature"
2. **Keep first line short**: Under50 characters
3. **Separate title and body**: Blank line between them
4. **Explain what and why**: Not how

---

## Amend Commit

### Amend Last Commit

```bash
# Amend commit message
git commit --amend -m "New message"

# Add forgotten files to last commit
git add forgotten-file.txt
git commit --amend --no-edit
```

### Amend Author

```bash
# Amend author
git commit --amend --author="Name <email>"
```

---

## View Commit History

### Basic Log

```bash
# View commit history
git log

# View concise log
git log --oneline

# View graphical log
git log --graph --oneline --all

# View detailed log
git log --stat

# View specific file log
git log filename.txt
```

### Log Filtering

```bash
# Filter by author
git log --author="John"

# Filter by date
git log --since="2024-01-01" --until="2024-12-31"

# Filter by message
git log --grep="fix"

# Filter by file
git log -- filename.txt
```

### Log Format

```bash
# Custom format
git log --pretty=format:"%h - %an, %ar : %s"

# One line with graph
git log --oneline --graph --decorate
```

---

## Undo Changes

### Undo Working Directory Changes

```bash
# Discard file changes
git checkout -- filename.txt

# Or using restore
git restore filename.txt

# Discard all changes
git checkout -- .
```

### Unstage Changes

```bash
# Unstage file
git restore --staged filename.txt

# Unstage all
git restore --staged .
```

### Undo Commit

```bash
# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Undo specific commit
git revert <commit-hash>
```

---

## Git Stash

### Basic Stash

```bash
# Stash changes
git stash

# Stash with message
git stash push -m "Stash message"

# Stash including untracked files
git stash -u
```

### View Stash

```bash
# View stash list
git stash list

# View stash content
git stash show

# View stash diff
git stash show -p
```

### Apply Stash

```bash
# Apply latest stash
git stash pop

# Apply specific stash
git stash apply stash@{0}

# Apply and keep stash
git stash apply
```

### Delete Stash

```bash
# Delete latest stash
git stash drop

# Delete specific stash
git stash drop stash@{0}

# Delete all stashes
git stash clear
```

---

## Common Issues

### Q: Accidentally committed to wrong branch?

**Solution:**
```bash
# Move commit to correct branch
git checkout correct-branch
git cherry-pick <commit-hash>

# Remove from wrong branch
git checkout wrong-branch
git reset --hard HEAD~1
```

### Q: How to undo pushed commit?

**Solution:**
```bash
# Revert commit
git revert <commit-hash>
git push origin main
```

### Q: How to split commit?

**Solution:**
```bash
# Reset to before commit
git reset --soft HEAD~1

# Stage and commit separately
git add file1.txt
git commit -m "Part1"

git add file2.txt
git commit -m "Part2"
```

---

## Best Practices

1. **Commit Frequently**: Make small, focused commits
2. **Write Good Messages**: Clear, descriptive commit messages
3. **Use Staging**: Stage related changes together
4. **Test Before Commit**: Ensure code works before committing
5. **Review Changes**: Use `git diff` to review before staging

---

**Next: [View History and Diff →](09-log-diff.md)**