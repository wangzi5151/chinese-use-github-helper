# GitHub Project Practical Examples

## Example 1: Personal Blog

### Goal
Build a personal blog using GitHub Pages.

### Tech Stack
- HTML/CSS/JavaScript
- GitHub Pages
- Jekyll (optional)

### Steps

#### 1. Create Repository
```bash
gh repo create my-blog --public --description "My personal blog"
```

#### 2. Create Basic Structure
```
my-blog/
├── index.html
├── css/
│   └── style.css
├── js/
│   └── main.js
├── posts/
│   └── 2024-01-01-hello.md
└── images/
```

#### 3. Write Homepage
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Blog</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <header>
        <h1>My Blog</h1>
        <nav>
            <a href="/">Home</a>
            <a href="/posts">Articles</a>
            <a href="/about">About</a>
        </nav>
    </header>
    <main>
        <article>
            <h2>Welcome to My Blog</h2>
            <p>This is my first article.</p>
        </article>
    </main>
</body>
</html>
```

#### 4. Enable GitHub Pages
1. Go to repository Settings → Pages
2. Select main branch
3. Click Save

#### 5. Visit Website
```
https://your-username.github.io/my-blog/
```

---

## Example 2: Open Source Utility Library

### Goal
Create a reusable JavaScript utility library.

### Steps

#### 1. Initialize Project
```bash
mkdir my-utils
cd my-utils
git init
npm init -y
```

#### 2. Create Directory Structure
```
my-utils/
├── src/
│   ├── index.js
│   ├── string.js
│   └── array.js
├── test/
│   ├── string.test.js
│   └── array.test.js
├── package.json
├── README.md
├── LICENSE
└── .gitignore
```

#### 3. Write Utility Functions
```javascript
// src/string.js
export const capitalize = (str) => {
  return str.charAt(0).toUpperCase() + str.slice(1);
};

export const camelCase = (str) => {
  return str.replace(/-([a-z])/g, (g) => g[1].toUpperCase());
};
```

#### 4. Write Tests
```javascript
// test/string.test.js
import { capitalize, camelCase } from '../src/string.js';

describe('String Utils', () => {
  test('capitalize', () => {
    expect(capitalize('hello')).toBe('Hello');
  });
  
  test('camelCase', () => {
    expect(camelCase('hello-world')).toBe('helloWorld');
  });
});
```

#### 5. Publish to npm
```bash
npm login
npm publish
```

---

## Example 3: Team Collaboration Project

### Goal
Create a project template suitable for team collaboration.

### Steps

#### 1. Create Repository and Configure
```bash
gh repo create team-project --private --description "Team project"
```

#### 2. Set Branch Protection
1. Settings → Branches → Add rule
2. Configure:
   - Branch name pattern: `main`
   - ✅ Require pull request reviews
   - ✅ Require status checks

#### 3. Create Project Structure
```
team-project/
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   └── feature_request.md
│   ├── PULL_REQUEST_TEMPLATE.md
│   ├── CODEOWNERS
│   └── workflows/
│       └── ci.yml
├── src/
├── docs/
├── tests/
├── README.md
├── CONTRIBUTING.md
└── LICENSE
```

#### 4. Configure CI/CD
```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - run: npm install
    - run: npm test
```

#### 5. Create Issue Template
```markdown
<!-- .github/ISSUE_TEMPLATE/bug_report.md -->
---
name: Bug Report
about: Report a problem
labels: bug
---

## Description
Briefly describe the problem

## Steps to Reproduce
1. 
2. 
3. 

## Expected Behavior
Describe expected behavior

## Actual Behavior
Describe actual behavior

## Environment
- OS: 
- Browser: 
- Version: 
```

---

## Example 4: GitHub Actions Automation

### Goal
Create a workflow for automatic npm package release.

### Steps

#### 1. Create Workflow File
```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    
    permissions:
      contents: read
      packages: write
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
        registry-url: 'https://npm.pkg.github.com'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Build
      run: npm run build
    
    - name: Test
      run: npm test
    
    - name: Publish to GitHub Packages
      run: npm publish
      env:
        NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    
    - name: Create Release
      uses: softprops/action-gh-release@v2
      with:
        generate_release_notes: true
```

#### 2. Use Workflow
```bash
# Create tag
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0
```

---

## Example 5: Full Stack Application

### Goal
Use GitHub to manage full stack application development.

### Tech Stack
- Frontend: React
- Backend: Node.js
- Database: MongoDB

### Steps

#### 1. Create Repository Structure
```
fullstack-app/
├── client/          # Frontend code
│   ├── src/
│   └── package.json
├── server/          # Backend code
│   ├── src/
│   └── package.json
├── .github/
│   └── workflows/
│       ├── frontend.yml
│       └── backend.yml
├── docker-compose.yml
├── README.md
└── .gitignore
```

#### 2. Configure Frontend CI
```yaml
# .github/workflows/frontend.yml
name: Frontend CI

on:
  push:
    paths:
      - 'client/**'

jobs:
  test:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: client
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
    
    - run: npm ci
    - run: npm test
    - run: npm run build
```

#### 3. Configure Backend CI
```yaml
# .github/workflows/backend.yml
name: Backend CI

on:
  push:
    paths:
      - 'server/**'

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      mongodb:
        image: mongo:5.0
        ports:
          - 27017:27017
    
    defaults:
      run:
        working-directory: server
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
    
    - run: npm ci
    - run: npm test
    
    env:
      MONGODB_URI: mongodb://localhost:27017/test
```

---

## Summary

These examples demonstrate GitHub's application in different scenarios:

1. **Personal Blog**: GitHub Pages
2. **Open Source Tools**: npm publishing
3. **Team Collaboration**: Branch protection, Issue templates
4. **Automation**: GitHub Actions
5. **Full Stack Applications**: Monorepo management