# Pull Request Collaboration

## What is Pull Request?

Pull Request (PR) is the standard way to contribute code to a project. It allows:
- Code review
- Discuss changes
- Automated checks
- Safe merging

## PR Workflow Diagram

```
1. Fork repository (external contribution)
     ↓
2. Create feature branch
     ↓
3. Modify code and commit
     ↓
4. Push to remote
     ↓
5. Create Pull Request (web operation)
     ↓
6. Code review (web operation)
     ↓
7. Merge (web operation)
```

## Create Pull Request (Detailed with Screenshots)

### Step 1: Push Branch to Remote

Execute in terminal:

```bash
# Ensure on feature branch
git checkout feature-new-button

# Push to remote
git push -u origin feature-new-button
```

### Step 2: Open PR Creation Page

**Method 1: Create from prompt**
1. After push, terminal will show a link
2. Copy link and open in browser

**Method 2: Manually create**
1. Open repository page
2. Click **Pull requests** tab
3. Click green **New pull request** button

```
┌─────────────────────────────────────────────┐
│  Pull requests                               │
│                                             │
│  [New pull request]  ← Click this button     │
│                                             │
│  No pull requests                            │
└─────────────────────────────────────────────┘
```

### Step 3: Select Branch

1. **base:** Select target branch (usually `main`)
2. **compare:** Select your feature branch

```
┌─────────────────────────────────────────────┐
│  Compare changes                             │
│                                             │
│  base: [main ▼]  ← Target branch            │
│     ...                                     │
│  compare: [feature-new-button ▼]  ← Your branch │
│                                             │
│  [Create pull request]                       │
└─────────────────────────────────────────────┘
```

### Step 4: Fill in PR Information

1. **Title**: Short description of changes
2. **Description**: Detailed explanation of changes
3. Click **Create pull request**

```
┌─────────────────────────────────────────────┐
│  Open a pull request                         │
│                                             │
│  base: main  ←  compare: feature-new-button │
│                                             │
│  Title: [feat: Add new button feature   ]     │
│                                             │
│  Description:                                │
│  ┌─────────────────────────────────────┐    │
│  │ ## Change Description               │    │
│  │ Added a new button                   │    │
│  │                                     │    │
│  │ ## Change Type                       │    │
│  │ - [x] New feature (feat)            │    │
│  │ - [ ] Bug fix (fix)                 │    │
│  └─────────────────────────────────────┘    │
│                                             │
│  ☑ Reviewers: [Add reviewer]                │
│  ☑ Assignees: [Add assignee]                │
│  ☑ Labels: [Add label]                      │
│                                             │
│        [Create pull request]                │
└─────────────────────────────────────────────┘
```

### Step 5: PR Created Successfully

After creation, page will jump to PR detail page, showing:
- PR status
- Code differences
- Review status
- CI check status

## PR Page Details

```
┌─────────────────────────────────────────────┐
│  #1 feat: Add new button feature             │
│                                             │
│  [Open]  ← Status label                      │
│                                             │
│  [Conversation] [Commits] [Checks] [Files]  │
├─────────────────────────────────────────────┤
│                                             │
│  Conversation  ← Default view                │
│                                             │
│  Author avatar  @author opened this pull request │
│          2 hours ago                         │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │ ## Change Description               │    │
│  │ Added a new button                   │    │
│  └─────────────────────────────────────┘    │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │ 💬 Write a comment...               │    │
│  │                                     │    │
│  │        [Comment]  [Review]          │    │
│  └─────────────────────────────────────┘    │
│                                             │
└─────────────────────────────────────────────┘
```

**PR Page Section Description:**

| Section | Description |
|---------|-------------|
| **Conversation** | Discussion and comments |
| **Commits** | View commit records |
| **Checks** | CI/CD check results |
| **Files** | View code differences |
| **Reviewers** | Reviewer list |
| **Labels** | Labels |
| **Milestone** | Milestone |

## Code Review (Web Operation)

### Review PR

1. Click **Files changed** tab on PR page
2. View code differences
3. Click line number to add comment

```
┌─────────────────────────────────────────────┐
│  Files changed  (3)                          │
│                                             │
│  ─ src/button.ts (+5 -2)                    │
│                                             │
│     1  │ const button = () => {             │
│  -   2  │   return <button>Click</button>;  │
│  +   2  │   return <button className="new"> ││
│  +   3  │     Click                         │
│  +   4  │   </button>;                      │
│     5  │ };                                 │
│                                             │
│  Click + next to line number to add comment │
└─────────────────────────────────────────────┘
```

### Add Review Comments

1. Click **+** button next to code line
2. Enter comment in popup
3. Click **Start review** or **Add single comment**

```
┌─────────────────────────────────────┐
│  💬 Leave a comment                  │
│                                     │
│  Suggest using more specific class name here │
│                                     │
│  ○ Comment  ← Comment only          │
│  ○ Approve  ← Approve               │
│  ○ Request changes ← Request changes │
│                                     │
│     [Start review]                  │
└─────────────────────────────────────┘
```

### After Review Complete

1. Click **Review changes** in top right corner of page
2. Select review result:
   - **Comment**: Comment only, doesn't block merge
   - **Approve**: Approve PR
   - **Request changes**: Request changes, blocks merge
3. Click **Submit review**

## Merge PR (Web Operation)

### Merge Conditions

Before merging, ensure:
- ✅ All reviewers have approved
- ✅ CI checks passed
- ✅ No conflicts

### Merge Operation

1. Find merge area at bottom of PR page
2. Select merge method
3. Click **Merge pull request**
4. Enter merge message (optional)
5. Click **Confirm merge**

```
┌─────────────────────────────────────────────┐
│  Pull request successfully merged            │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │  ○ Create a merge commit            │    │
│  │    Preserve complete commit history  │    │
│  │                                     │    │
│  │  ○ Squash and merge                 │    │
│  │    Squash into one commit           │    │
│  │                                     │    │
│  │  ○ Rebase and merge                 │    │
│  │    Linear history                   │    │
│  └─────────────────────────────────────┘    │
│                                             │
│  Merge message:                             │
│  ┌─────────────────────────────────────┐    │
│  │ Merge pull request #1               │    │
│  │ feat: Add new button feature        │    │
│  └─────────────────────────────────────┘    │
│                                             │
│        [Merge pull request]                 │
└─────────────────────────────────────────────┘
```

## Post-merge Cleanup (Web Operation)

After merging PR, can delete feature branch:

1. At bottom of PR page, click **Delete branch**
2. Confirm deletion

```
┌─────────────────────────────────────────────┐
│  Pull request successfully merged            │
│                                             │
│  ✓ branch feature-new-button deleted         │
│                                             │
└─────────────────────────────────────────────┘
```

## PR Best Practices

1. **Keep small and focused**: One PR only does one thing
2. **Clear title and description**: Let reviewers quickly understand
3. **Associate Issue**: Explain what problem it solves
4. **Respond to review promptly**: Don't let PR sit too long
5. **Test thoroughly**: Ensure code quality

---

## Practice Exercise

### Exercise: Create and Merge a PR

**Task 1: Create Repository and Branch**
1. Create a new repository `pr-practice` on GitHub
2. Clone to local
3. Create new branch `feature-add-readme`

**Task 2: Modify and Push**
1. Modify README.md file
2. Commit and push to remote

**Task 3: Create PR**
1. Click **Pull requests** on GitHub page
2. Click **New pull request**
3. Fill in title and description
4. Click **Create pull request**

**Task 4: Review PR**
1. View code differences
2. Add comment
3. Click **Approve**

**Task 5: Merge PR**
1. Click **Merge pull request**
2. Click **Confirm merge**
3. Delete feature branch

**Verification Method:**
- PR status shows "Merged" (purple)
- Code has been merged into main branch

## Next Step

[Code Review →](18-code-review.md)