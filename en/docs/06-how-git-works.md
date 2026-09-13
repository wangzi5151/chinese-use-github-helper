# Git Working Principle Deep Dive

> Understanding Git's internal working principles will make you more proficient when using Git. This chapter will start from the underlying mechanism and comprehensively introduce how Git manages code versions.

---

## Table of Contents

- [Git's Distributed Version Control Principle](#gits-distributed-version-control-principle)
- [Git's Three Working Areas](#gits-three-working-areas)
- [Four States of Git Files](#four-states-of-git-files)
- [Git Object Model](#git-object-model)
- [Git References](#git-references)
- [SHA-1 Hash and Content Addressing](#sha-1-hash-and-content-addressing)
- [Git's Snapshot Mechanism vs Difference Mechanism](#gits-snapshot-mechanism-vs-difference-mechanism)
- [Git Directory Structure Details](#git-directory-structure-details)
- [Git's Garbage Collection Mechanism](#gits-garbage-collection-mechanism)
- [Understanding Git's Optimistic Locking Strategy](#understanding-gits-optimistic-locking-strategy)
- [Git vs Other Version Control Systems](#git-vs-other-version-control-systems)
- [Git Workflow Diagrams](#git-workflow-diagrams)

---

## Git's Distributed Version Control Principle

### What is Distributed Version Control

Before Git, mainstream version control systems (like SVN, CVS) used **centralized** architecture. Git uses completely different **distributed** architecture, which is the foundation for understanding all Git behaviors.

```
┌─────────────────────────────────────────────────────────────────┐
│                    Centralized vs Distributed                    │
├────────────────────────────┬────────────────────────────────────┤
│       Centralized (SVN)    │         Distributed (Git)          │
│                            │                                    │
│    ┌──────────┐            │   ┌──────────┐  ┌──────────┐      │
│    │ Central  │            │   │Developer A│  │Developer B│      │
│    │ Server   │            │   │(Complete) │  │(Complete) │      │
│    └─────┬────┘            │   └─────┬────┘  └─────┬────┘      │
│      ┌───┼───┐             │         │             │            │
│      │   │   │             │         └──────┬──────┘            │
│      ▼   ▼   ▼             │                ▼                   │
│    ┌─┐ ┌─┐ ┌─┐            │          ┌──────────┐             │
│    │A│ │B│ │C│            │          │ Remote   │             │
│    └─┘ └─┘ └─┘            │          │ (Optional│             │
│   Developers               │          └──────────┘             │
│                            │                                    │
│  • Must be online to commit │  • Everyone has complete repo    │
│  • Server down = all stop   │  • Can commit offline            │
│  • Only saves differences   │  • Saves complete snapshots      │
└────────────────────────────┴────────────────────────────────────┘
```

### Core Advantages of Distributed

**1. Offline Work Capability**

Git allows you to perform almost all operations without network connection:

```bash
# All following operations can be done offline
git add .                    # Stage files
git commit -m "New feature"  # Commit changes
git log                      # View commit history
git branch feature           # Create branch
git checkout feature         # Switch branch
git diff                     # View differences
git blame file.txt           # View file modification history
```

**2. Every Developer Has Complete Repository Copy**

When you execute `git clone`, you don't just get the latest files, but the entire project history:

```
Developer A's Computer    Developer B's Computer    Remote Server
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│ .git/       │      │ .git/       │      │ .git/       │
│  ├── Complete│      │  ├── Complete│      │  ├── Complete│
│  │   History │      │  │   History │      │  │   History │
│  ├── All     │      │  ├── All     │      │  ├── All     │
│  │   Branches│      │  │   Branches│      │  │   Branches│
│  └── ...     │      │  └── ...     │      │  └── ...     │
└─────────────┘      └─────────────┘      └─────────────┘
```

**3. Fast Speed**

Most operations are local, no network required:

```bash
# These operations are all local, very fast
git status
git diff
git log
git branch
git commit
```

---

## Git's Three Working Areas

Git has three important working areas that you must understand:

```
┌─────────────────────────────────────────────────────────────────┐
│                    Git Three Working Areas                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Working Directory      Staging Area         Repository        │
│   (Working Directory)    (Staging Area)       (Repository)      │
│                                                                 │
│   ┌─────────────┐       ┌─────────────┐      ┌─────────────┐  │
│   │ file1.txt   │       │ file1.txt   │      │ Commit      │  │
│   │ file2.txt   │──────▶│ file2.txt   │─────▶│ (Snapshot)  │  │
│   │ file3.txt   │ git   │             │ git  │             │  │
│   │             │ add   │             │commit│             │  │
│   └─────────────┘       └─────────────┘      └─────────────┘  │
│                                                                 │
│   Your working files     Files ready to        Permanent        │
│                          commit               storage           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Working Directory

The working directory is where you edit files. It's your project folder.

```bash
# View files in working directory
ls -la

# View working directory status
git status
```

### Staging Area

The staging area is a middle layer between working directory and repository. It stores files that will be committed.

```bash
# Add files to staging area
git add file.txt
git add .

# View staging area status
git status
```

### Repository

The repository stores all committed snapshots. It's the permanent storage.

```bash
# Commit files from staging area to repository
git commit -m "Commit message"

# View repository history
git log
```

---

## Four States of Git Files

Files in Git have four possible states:

```
┌─────────────────────────────────────────────────────────────────┐
│                    Four States of Git Files                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Untracked      Unmodified     Modified        Staged          │
│   (Untracked)    (Unmodified)   (Modified)      (Staged)        │
│                                                                 │
│   ┌─────────┐   ┌─────────┐   ┌─────────┐    ┌─────────┐     │
│   │ New     │   │ No      │   │ Changed │    │ Ready   │     │
│   │ File    │   │ Change  │   │ File    │    │ to      │     │
│   │         │   │         │   │         │    │ Commit  │     │
│   └────┬────┘   └────┬────┘   └────┬────┘    └────┬────┘     │
│        │             │             │              │            │
│        │ git add     │ Edit file   │ git add      │ git commit │
│        └────────────▶│◀────────────│◀─────────────│            │
│                      │             │              │            │
│                      │             │              │            │
│                      └─────────────┴──────────────┘            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Untracked

New files that Git doesn't track yet.

```bash
# Create new file
echo "Hello" > newfile.txt

# Check status
git status
# Shows: newfile.txt (Untracked)
```

### Unmodified

Files that haven't been modified since last commit.

```bash
# After commit
git commit -m "Add newfile.txt"
# newfile.txt is now Unmodified
```

### Modified

Files that have been modified but not staged.

```bash
# Edit file
echo "World" >> newfile.txt

# Check status
git status
# Shows: newfile.txt (Modified)
```

### Staged

Files that are ready to be committed.

```bash
# Stage file
git add newfile.txt

# Check status
git status
# Shows: newfile.txt (Staged)
```

---

## Git Object Model

Git uses four types of objects to store data:

```
┌─────────────────────────────────────────────────────────────────┐
│                    Git Object Model                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Blob Object     Tree Object     Commit Object    Tag Object   │
│   (File Content)  (Directory)     (Snapshot)       (Label)      │
│                                                                 │
│   ┌─────────┐    ┌─────────┐    ┌─────────┐     ┌─────────┐   │
│   │ File    │    │ Directory│    │ Commit  │     │ Tag     │   │
│   │ Content │    │ Structure│    │ Info    │     │ Info    │   │
│   └─────────┘    └─────────┘    └─────────┘     └─────────┘   │
│                                                                 │
│   Stores file     Stores file    Stores snapshot  Stores        │
│   content only    names and      with author,     annotated     │
│                   permissions    message, parent  tags          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Blob Object

Stores file content only, no filename or permissions.

```bash
# View blob object
git cat-file -p <blob-hash>
```

### Tree Object

Stores directory structure, including filenames and permissions.

```bash
# View tree object
git cat-file -p <tree-hash>
```

### Commit Object

Stores commit information, including author, message, and parent commit.

```bash
# View commit object
git cat-file -p <commit-hash>
```

### Tag Object

Stores annotated tag information.

```bash
# View tag object
git cat-file -p <tag-hash>
```

---

## Git References

Git references are pointers to commits:

```
┌─────────────────────────────────────────────────────────────────┐
│                    Git References                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Branch Reference    Tag Reference     HEAD Reference          │
│   (Branch)           (Tag)             (Current Branch)         │
│                                                                 │
│   ┌─────────┐       ┌─────────┐       ┌─────────┐             │
│   │ main    │       │ v1.0.0  │       │ HEAD    │             │
│   │ ──────▶ │       │ ──────▶ │       │ ──────▶ │             │
│   │ Commit  │       │ Commit  │       │ main    │             │
│   └─────────┘       └─────────┘       └─────────┘             │
│                                                                 │
│   Points to latest   Points to        Points to current        │
│   commit of branch   tagged commit    branch                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Branch References

Branches are pointers to the latest commit of a branch.

```bash
# View branches
git branch

# Create branch
git branch feature

# Delete branch
git branch -d feature
```

### Tag References

Tags are pointers to specific commits.

```bash
# View tags
git tag

# Create tag
git tag v1.0.0

# Delete tag
git tag -d v1.0.0
```

### HEAD Reference

HEAD points to the current branch.

```bash
# View HEAD
cat .git/HEAD

# View current branch
git branch --show-current
```

---

## SHA-1 Hash and Content Addressing

Git uses SHA-1 hash to identify objects:

```
┌─────────────────────────────────────────────────────────────────┐
│                    SHA-1 Hash                                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Content ──────────▶ SHA-1 Hash ──────────▶ Object Storage     │
│                                                                 │
│   "Hello World" ───▶ 5e1c345e... ──────────▶ .git/objects/5e/  │
│                                                                 │
│   • Same content always produces same hash                      │
│   • Different content produces different hash                   │
│   • Hash is40 characters long                                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### View Hash

```bash
# View commit hash
git log --oneline

# View object hash
git hash-object file.txt
```

---

## Git's Snapshot Mechanism vs Difference Mechanism

Git uses snapshot mechanism, not difference mechanism:

```
┌─────────────────────────────────────────────────────────────────┐
│                    Snapshot vs Difference                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Difference Mechanism (SVN)      Snapshot Mechanism (Git)      │
│                                                                 │
│   ┌─────────┐                    ┌─────────┐                   │
│   │ Version1│                    │Snapshot1│                   │
│   └────┬────┘                    └────┬────┘                   │
│        │ +diff                        │                         │
│        ▼                              ▼                         │
│   ┌─────────┐                    ┌─────────┐                   │
│   │ Version2│                    │Snapshot2│                   │
│   └────┬────┘                    └────┬────┘                   │
│        │ +diff                        │                         │
│        ▼                              ▼                         │
│   ┌─────────┐                    ┌─────────┐                   │
│   │ Version3│                    │Snapshot3│                   │
│   └─────────┘                    └─────────┘                   │
│                                                                 │
│   • Stores differences             • Stores complete snapshots  │
│   • Must calculate current state   • Direct access to any version│
│   • Slower for old versions        • Fast access to any version │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Git Directory Structure Details

```
.git/
├── HEAD              # Points to current branch
├── config            # Repository configuration
├── description       # Repository description
├── hooks/            # Hook scripts
│   ├── pre-commit.sample
│   ├── post-commit.sample
│   └── ...
├── objects/          # Object storage
│   ├── pack/
│   └── info/
├── refs/             # References
│   ├── heads/        # Branch references
│   ├── tags/         # Tag references
│   └── remotes/      # Remote references
├── index             # Staging area index
├── logs/             # Ref logs
├── info/             # Additional info
│   └── exclude       # Local exclude patterns
└── packed-refs       # Packed references
```

---

## Git's Garbage Collection Mechanism

Git periodically runs garbage collection to clean up unnecessary objects:

```bash
# Manual garbage collection
git gc

# View garbage collection status
git gc --dry-run
```

---

## Understanding Git's Optimistic Locking Strategy

Git uses optimistic locking strategy for concurrency control:

```
┌─────────────────────────────────────────────────────────────────┐
│                    Optimistic Locking Strategy                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Developer A: Read ──▶ Modify ──▶ Write                        │
│                                                                 │
│   Developer B: Read ──▶ Modify ──▶ Write ──▶ Conflict!          │
│                                                                 │
│   • No locking during read                                      │
│   • Check for conflicts at write time                           │
│   • Resolve conflicts manually                                  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Git vs Other Version Control Systems

| Feature | Git | SVN | CVS |
|---------|-----|-----|-----|
| Architecture | Distributed | Centralized | Centralized |
| Offline Work | Yes | No | No |
| Speed | Fast | Slow | Slow |
| Branching | Cheap | Expensive | Expensive |
| Merging | Excellent | Good | Basic |
| Data Integrity | SHA-1 hash | Checksum | Checksum |

---

## Git Workflow Diagrams

### Basic Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    Basic Git Workflow                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  1. Edit ──▶2. Stage ──▶3. Commit ──▶4. Push                   │
│                                                                 │
│   Working     Staging     Local        Remote                   │
│   Directory   Area        Repository   Repository               │
│                                                                 │
│   Edit files  git add     git commit   git push                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Branch Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    Branch Workflow                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   main ─────●─────●─────●─────●─────●─────▶                    │
│              \         /         \                               │
│   feature ────●─────●───────●─────●─────▶                       │
│                                                                 │
│   • Create feature branch from main                             │
│   • Work on feature branch                                      │
│   • Merge back to main                                          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

**Next: [Create and Clone Repositories →](07-init-clone.md)**