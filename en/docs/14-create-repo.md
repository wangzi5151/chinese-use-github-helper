# Create and Manage Repositories

## Create Repository on GitHub (Detailed with Screenshots)

### Method 1: Web Creation (Recommended for Beginners)

**Step 1: Login to GitHub**
1. Open browser and visit **github.com**
2. Enter username/email and password to login

**Step 2: Enter Creation Page**
1. After login, on any GitHub page
2. Click **+** in top right corner (left of avatar)
3. Click **New repository** in dropdown menu

```
┌─────────────────────────────────────────────┐
│  +  [🔔]  [Avatar]                           │
│  │                                          │
│  └─→ New repository  ← Click this           │
│      Import repository                      │
│      New organization                        │
└─────────────────────────────────────────────┘
```

**Step 3: Fill in Repository Information**

You will see the repository creation form, fill in item by item:

```
┌─────────────────────────────────────────────┐
│  Create a new repository                     │
│                                             │
│  Owner: [Your username ▼]                    │
│                                             │
│  Repository name: [my-project          ]     │
│  (Required, can only contain letters, numbers, hyphens, underscores) │
│                                             │
│  Description: [This is a sample project]     │
│  (Optional, short description of repository purpose) │
│                                             │
│  ○ Public  ← Anyone can see                  │
│  ○ Private ← Only you and collaborators can see │
│                                             │
│  ☑ Add a README file  ← Strongly recommend checking │
│                                             │
│  Add .gitignore: [None ▼] ← Select template │
│                                             │
│  Choose a license: [None ▼] ← Select license │
│                                             │
│        [Create repository]  ← Click to create │
└─────────────────────────────────────────────┘
```

**Item Description:**

| Option | Description | Suggestion |
|--------|-------------|------------|
| **Repository name** | Repository name | Use English, like `my-project` |
| **Description** | Repository description | Short description of project purpose |
| **Public** | Public repository | Choose this for open source projects |
| **Private** | Private repository | Choose this for personal projects |
| **Add a README** | Add README file | Strongly recommend checking |
| **.gitignore** | Ignore file template | Choose based on project language |
| **License** | Open source license | Need to choose for open source projects |

**Step 4: Creation Successful**
After clicking **Create repository**, page will jump to newly created repository page.

### Method 2: Using Command Line

If you have installed GitHub CLI, you can create in terminal:

```bash
# Create public repository
gh repo create my-repo --public --description "My project"

# Create private repository
gh repo create my-repo --private
```

## Repository Page Details

After creating repository, you will see interface like this:

```
┌─────────────────────────────────────────────┐
│  your-username / my-project                  │
│                                             │
│  [Code]  [Issues]  [Pull requests]          │
│  [Actions]  [Projects]  [Wiki]              │
├─────────────────────────────────────────────┤
│                                             │
│  📁 README.md                               │
│                                             │
│  This is a sample project...                │
│                                             │
└─────────────────────────────────────────────┘
```

**Top Tab Description:**

| Tab | Function |
|-----|----------|
| **Code** | View code files |
| **Issues** | Issue tracking |
| **Pull requests** | Code merge requests |
| **Actions** | Automated workflows |
| **Projects** | Project board |
| **Wiki** | Project documentation |
| **Security** | Security settings |
| **Insights** | Data analysis |
| **Settings** | Repository settings |

## Repository Settings (with Screenshots)

### Basic Settings

**Operation Steps:**
1. Enter repository page
2. Click **Settings** tab at top
3. In **General** tab, can modify:
   - Repository name
   - Description
   - Website URL
   - Topics

```
┌─────────────────────────────────────────────┐
│  Settings                                    │
│                                             │
│  General  ← Current page                     │
│  Access                                       │
│  Branches                                    │
│  Tags                                        │
│  ...                                         │
├─────────────────────────────────────────────┤
│                                             │
│  Repository name: [my-project          ]     │
│  Description:     [This is a sample    ]     │
│  Website:         [https://example.com]      │
│  Topics:          [react] [javascript]       │
│                                             │
│           [Save changes]                    │
└─────────────────────────────────────────────┘
```

### Add Collaborators

**Operation Steps:**
1. Enter **Settings**
2. Select **Collaborators** in left menu
3. Click **Add people**
4. Enter other person's GitHub username
5. Click **Add [username] to this repository**

```
┌─────────────────────────────────────────────┐
│  Collaborators                                │
│                                             │
│  Search by username, full name, or email:    │
│  ┌─────────────────────────────────────┐    │
│  │ username                            │    │
│  └─────────────────────────────────────┘    │
│                                             │
│           [Add [username] to repository]    │
└─────────────────────────────────────────────┘
```

### Delete Repository

**Operation Steps:**
1. Enter **Settings**
2. Scroll to bottom of page
3. Find **Danger Zone** area
4. Click **Delete this repository**
5. Enter repository name to confirm deletion

⚠️ **Warning: Deletion is irreversible, please proceed with caution!**

## Clone Repository Using SSH

### Get Clone Address

1. Enter repository page
2. Click green **Code** button
3. Select **SSH** tab
4. Copy address

```
┌─────────────────────────────────────────────┐
│  Code                                        │
│                                             │
│  HTTPS  SSH  GitHub CLI                      │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │ git@github.com:user/repo.git       │    │
│  │                          📋 Copy    │    │
│  └─────────────────────────────────────┘    │
└─────────────────────────────────────────────┘
```

### Clone to Local

```bash
# Clone repository
git clone git@github.com:user/repo.git

# Enter repository directory
cd repo
```

## Good Repository Should Contain

```
my-repo/
├── README.md          # Project description (required)
├── LICENSE            # License (required for open source)
├── .gitignore         # Ignore files (recommended)
├── CONTRIBUTING.md    # Contributing guide (recommended)
├── CHANGELOG.md       # Changelog (recommended)
└── src/              # Source code directory
```

---

## Practice Exercise

### Exercise: Create Your First Repository

**Task 1: Create Repository on Web**
1. Login to GitHub
2. Click **+** in top right corner → **New repository**
3. Fill in repository name: `my-first-repo`
4. Fill in description: `My first GitHub repository`
5. Select **Public**
6. Check **Add a README file**
7. Click **Create repository**

**Task 2: Explore Repository Page**
1. Click various tabs (Code, Issues, Pull requests, etc.)
2. Click **Settings** to view settings options
3. Click **Code** to get clone address

**Task 3: Clone Repository to Local**
1. Copy SSH clone address
2. Execute `git clone address` in terminal
3. Enter repository directory to view files

**Verification Method:**
- Repository page shows files you created
- Can enter repository directory locally and see files

## Next Step

[README and Documentation →](15-readme-docs.md)