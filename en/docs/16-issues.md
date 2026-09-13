# Issue Tracking

## What is Issue?

Issue is GitHub's issue tracking system, used for:
- Report bugs
- Propose feature requests
- Discuss problems
- Track tasks

## Create Issue (Detailed with Screenshots)

### Step 1: Enter Issues Page

1. Open repository page
2. Click **Issues** tab
3. Click green **New issue** button

```
┌─────────────────────────────────────────────┐
│  Issues                                      │
│                                             │
│  [New issue]  ← Click this button           │
│                                             │
│  Filters: [Open ▼] [Labels ▼] [Assignee ▼] │
│                                             │
│  No issues found                             │
└─────────────────────────────────────────────┘
```

### Step 2: Fill in Issue Information

```
┌─────────────────────────────────────────────┐
│  New issue                                   │
│                                             │
│  Title: [Bug: Homepage loading failed   ]     │
│                                             │
│  Leave a comment:                            │
│  ┌─────────────────────────────────────┐    │
│  │ ## Problem Description              │    │
│  │ Homepage cannot load normally in some cases │
│  │                                     │    │
│  │ ## Steps to Reproduce               │    │
│  │ 1. Open homepage                    │    │
│  │ 2. Click login button               │    │
│  │ 3. Page shows blank                 │    │
│  │                                     │    │
│  │ ## Expected Behavior                │    │
│  │ Should display login form           │    │
│  │                                     │    │
│  │ ## Environment Info                 │    │
│  │ - OS: Windows 11                    │    │
│  │ - Browser: Chrome 120               │    │
│  └─────────────────────────────────────┘    │
│                                             │
│  ☑ Assignees: [Select assignee]             │
│  ☑ Labels: [bug] [help wanted]              │
│  ☑ Milestone: [v1.0]                       │
│                                             │
│        [Submit new issue]                   │
└─────────────────────────────────────────────┘
```

**Fill Instructions:**

| Field | Description | Suggestion |
|-------|-------------|------------|
| **Title** | Issue title | Concise description of problem |
| **Comment** | Detailed description | Provide steps to reproduce |
| **Assignees** | Assignee | Select person to handle this Issue |
| **Labels** | Labels | Classification management |
| **Milestone** | Milestone | Associate version plan |

### Step 3: Submit Issue

1. After filling in
2. Click green **Submit new issue** button
3. Issue created successfully

## Using Issue Templates

Many repositories provide Issue templates for faster creation of standard Issues:

```
┌─────────────────────────────────────────────┐
│  Choose a template                           │
│                                             │
│  ┌─────────────┐  ┌─────────────┐          │
│  │ 🐛 Bug     │  │ ✨ Feature  │          │
│  │ Report      │  │ Request     │          │
│  │             │  │             │          │
│  │ [Use        │  │ [Use        │          │
│  │  template]  │  │  template]  │          │
│  └─────────────┘  └─────────────┘          │
│                                             │
│  ┌─────────────┐  ┌─────────────┐          │
│  │ ❓ Question │  │ 📝 Docs     │          │
│  │             │  │             │          │
│  │ [Use        │  │ [Use        │          │
│  │  template]  │  │  template]  │          │
│  └─────────────┘  └─────────────┘          │
└─────────────────────────────────────────────┘
```

## Issue Labels

### Default Labels

| Label | Color | Description |
|-------|-------|-------------|
| `bug` | Red | Bug report |
| `enhancement` | Blue | Feature enhancement |
| `documentation` | Light blue | Documentation related |
| `good first issue` | Green | Suitable for beginners |
| `help wanted` | Green | Need help |
| `question` | Purple | Question |
| `wontfix` | Grey | Won't fix |
| `duplicate` | Grey | Duplicate |

### Add Labels to Issue

**Method 1: Add when creating**
1. When creating Issue
2. Click **Labels** dropdown
3. Select or create label

**Method 2: Add after creation**
1. Open Issue page
2. Find **Labels** on right side
3. Click gear icon
4. Select label

```
┌─────────────────────────────────────┐
│  Labels  ⚙️                          │
│                                     │
│  ☐ bug                              │
│  ☑ enhancement  ← Select this       │
│  ☐ documentation                    │
│  ☐ good first issue                 │
│                                     │
│  Filter: [Search labels...]         │
│                                     │
│  [New label]  ← Create new label    │
└─────────────────────────────────────┘
```

### Create Custom Labels

1. Click **Labels** on Issues page
2. Click **New label**
3. Fill in information:
   - **Label name**: Label name
   - **Description**: Description
   - **Color**: Color (click to select)
4. Click **Add label**

```
┌─────────────────────────────────────┐
│  New label                           │
│                                     │
│  Label name: [priority: high   ]    │
│                                     │
│  Description: [High priority issue] │
│                                     │
│  Color: [🔴]  ← Click to select color │
│                                     │
│     [Add label]                     │
└─────────────────────────────────────┘
```

## Assignees

Assign Issue to specific person:

1. Open Issue page
2. Find **Assignees** on right side
3. Click **Assign yourself** or search to add

```
┌─────────────────────────────────────┐
│  Assignees                           │
│                                     │
│  No one assigned                    │
│                                     │
│  [Assign yourself]  ← Assign to self │
│  [Assign others]   ← Assign to others │
└─────────────────────────────────────┘
```

## Milestones

Associate Issue with version plan:

### Create Milestone

1. Click **Milestones** on Issues page
2. Click **New milestone**
3. Fill in title and description
4. Set due date
5. Click **Create milestone**

### Associate Issue

1. Edit Issue
2. Select **Milestone** on right side
3. Select milestone

```
┌─────────────────────────────────────┐
│  Milestone                           │
│                                     │
│  ○ No milestone                     │
│  ○ v1.0 - First release             │
│  ● v2.0 - Feature enhancement  ← Select this │
│                                     │
└─────────────────────────────────────┘
```

## Close Issue

### Method 1: Close on Issue Page

1. Open Issue page
2. Click **Close issue** button at bottom

### Method 2: Use Keywords to Auto-close

Use keywords in commit message or PR description:

```bash
# Close single Issue
git commit -m "fix: Fix login issue, closes #42"

# Close multiple Issues
git commit -m "fix: Fix multiple issues, fixes #42, fixes #43"
```

**Keywords:**
- `closes #42`
- `fixes #42`
- `resolves #42`

### Method 3: Auto-close in PR

1. Enter `Closes #42` in PR description
2. When PR is merged, associated Issue will be automatically closed

## Issue Best Practices

1. **Use clear title**: Concise description of problem
2. **Provide steps to reproduce**: Let others reproduce the problem
3. **Additional information**: Screenshots, logs, environment info
4. **Use labels**: Classification management
5. **Reply promptly**: Respond to comments and questions

---

## Practice Exercise

### Exercise: Create and Manage Issues

**Task 1: Create Issue**
1. Create a repository (if don't have)
2. Click **Issues** → **New issue**
3. Title: `Bug: Test issue`
4. Description: Fill in issue details
5. Add label `bug`
6. Click **Submit new issue**

**Task 2: Create Issue Using Template**
1. Click **New issue**
2. Select Bug Report template
3. Fill in template content
4. Submit Issue

**Task 3: Manage Issues**
1. Add label to Issue
2. Assign assignee
3. Associate milestone
4. Close Issue

**Verification Method:**
- Issue list shows created Issue
- Labels, assignee, milestone set correctly

## Next Step

[Pull Request Collaboration →](17-pull-requests.md)