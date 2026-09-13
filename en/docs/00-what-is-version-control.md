# Chapter 1: What are Git and GitHub

## 1.1 Version Control Overview

### What is Version Control?

Version control is a system that records changes to one or more files over time, so you can recall specific versions later. Simply put, it helps you record every change to a file, making it easy to view history, revert to a specific version, or collaborate with multiple people.

**For example:**

Suppose you are writing a paper, you might encounter this situation:

```
Paper_FirstDraft.docx
Paper_Revision1.docx
Paper_Revision2.docx
Paper_FinalVersion.docx
Paper_FinalVersion_ReallyFinal.docx
Paper_NeverRevisingAgain.docx
```

This approach is very messy and error-prone. With a version control system, you can:

1. Keep only one file
2. Record a version for each change
3. View historical versions anytime
4. Easily revert to any version
5. Multiple people edit the same file simultaneously

### Why Version Control?

| Scenario | Without Version Control | With Version Control |
|----------|------------------------|---------------------|
| Personal Project | More and more files, messy naming | Automatic version management, clear |
| Team Collaboration | File conflicts, overwriting others' code | Automatic merging, preserving change history |
| Reverting Changes | Manual copy and paste | One-click revert to any version |
| Tracing Issues | Don't know who changed what | Complete change history |
| Multi-person Development | Files locked, cannot parallel | Parallel development, intelligent merging |

### Types of Version Control Systems

**1. Local Version Control Systems**

The simplest version control method, using a database to record file changes.

```
Pros: Simple
Cons: Can only be used locally, cannot collaborate
Representative: RCS
```

**2. Centralized Version Control Systems**

Has a central server, all clients connect to the server to get the latest code.

```
Pros: Can collaborate with multiple people
Cons: Single point of failure, needs network
Representative: SVN, CVS
```

**3. Distributed Version Control Systems**

Everyone has a complete copy of the code repository, can perform all operations locally.

```
Pros: Works offline, no central server needed, fast
Cons: Takes up storage space
Representative: Git, Mercurial
```

## 1.2 Git Introduction

### What is Git?

Git is currently the world's most advanced distributed version control system, created by Linus Torvalds (father of Linux) in 2005.

**Git's Features:**

1. **Fast**: Most operations complete locally, extremely fast
2. **Distributed**: Everyone has a complete repository copy
3. **Data Integrity**: All data is SHA-1 hash verified
4. **Branch Support**: Creating and switching branches is very fast
5. **Non-linear Development**: Powerful branching and merging capabilities

### Git's Origin Story

In 2005, Linus Torvalds needed a version control system to manage Linux kernel development. The BitKeeper being used at the time stopped free licensing, so Linus spent about two weeks writing the first version of Git in C.

The name "Git" comes from British slang meaning "unpleasant person" - Linus thought the name was amusing.

### How Git Works

Git manages files through snapshots:

```
First commit: Complete snapshot
Second commit: Only saves changed parts
Third commit: Only saves changed parts
```

**Relationship between Working Directory, Staging Area, and Repository:**

```
Working Directory
    ↓ git add
Staging Area
    ↓ git commit
Repository
```

## 1.3 GitHub Introduction

### What is GitHub?

GitHub is a code hosting platform based on Git, providing a web interface for developers to easily manage code and collaborate.

**GitHub is not just a code repository, it also includes:**

- **Issues**: Issue tracking system
- **Pull Requests**: Code merge requests
- **Actions**: Automated workflows
- **Projects**: Project management boards
- **Discussions**: Community discussions
- **Packages**: Package management
- **Pages**: Static website hosting
- **Copilot**: AI programming assistant

### Git vs GitHub

```
Git (Version Control Tool)  ≠  GitHub (Hosting Platform)
    ↓                              ↓
  Local Use                    Cloud Service
  Command Line Operations      Web Interface + API
  Manage Code History          Collaboration + Social Features
```

**Simply put:**
- **Git** is software installed on your computer for managing code versions
- **GitHub** is a website that uses Git as its underlying tool, providing collaboration and social features

### Why Choose GitHub?

1. **Industry Standard**: Over 100 million developers use it
2. **Free Plan**: Personal repositories are completely free
3. **Rich Ecosystem**: Numerous integrations and plugins
4. **Learning Resources**: Massive open source projects to learn from
5. **Resume Booster**: An active GitHub profile proves technical ability
6. **AI Features**: Built-in Copilot AI programming assistant

### GitHub's Business Model

| Plan | Price | Features |
|------|-------|----------|
| Free | Free | Unlimited public/private repos, 2000 minutes Actions |
| Pro | $4/month | More storage, advanced features |
| Team | $4/user/month | Team collaboration, permission management |
| Enterprise | $21/user/month | Enterprise security, compliance, support |

## 1.4 Other Version Control Platforms

| Platform | Features |
|----------|----------|
| **GitLab** | Better self-hosting support, built-in CI/CD |
| **Bitbucket** | Atlassian ecosystem integration |
| **Gitee** | Faster access in China, good Chinese support |
| **Coding** | Tencent Cloud DevOps |
| **Azure DevOps** | Microsoft Enterprise DevOps |

## 1.5 Learning Roadmap

```
Beginner Stage (1-2 weeks)
├── Install Git
├── Configure GitHub
├── Basic Git commands
└── Create first repository

Foundation Stage (2-4 weeks)
├── Branch management
├── Merge and rebase
├── Resolve conflicts
└── Pull Request workflow

Advanced Stage (1-2 months)
├── GitHub Actions
├── Project management
├── Team collaboration
└── Open source contribution

Expert Stage (Continuous learning)
├── Advanced Git techniques
├── DevOps practices
├── Enterprise applications
└── AI-assisted development
```

## 1.6 Common Misconceptions

**Misconception 1: Git can only use the command line**
Fact: Git has many graphical tools, such as GitHub Desktop, GitKraken, VS Code integration, etc.

**Misconception 2: Git is too hard to learn**
Fact: Git's basic commands can be learned in just a few hours, advanced features can be learned gradually.

**Misconception 3: GitHub can only host code**
Fact: GitHub can host any file, including documents, images, data, etc.

**Misconception 4: Only open source projects need GitHub**
Fact: Many companies also use GitHub internally for project management.

**Misconception 5: Git takes up a lot of space**
Fact: Git uses incremental storage, only saving changed parts, very space-efficient.

## 1.7 Chapter Summary

This chapter introduces the basic concepts of version control, Git and GitHub introductions, and the learning roadmap.

**Key Takeaways:**
- Version control is an important tool for managing code changes
- Git is currently the most popular distributed version control system
- GitHub is a code hosting platform based on Git
- Learning GitHub requires a step-by-step approach

**Next Step:**
[Install and Configure Git →](02-install-git.md)