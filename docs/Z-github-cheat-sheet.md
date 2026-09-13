# GitHub 速查表

> 本章提供 GitHub 常用命令和操作的快速参考。

---

## 目录

1. [Git 基础命令](#git-基础命令)
2. [GitHub CLI 命令](#github-cli-命令)
3. [GitHub Actions 语法](#github-actions-语法)
4. [GitHub API 端点](#github-api-端点)
5. [GitHub 快捷键](#github-快捷键)
6. [GitHub Markdown 语法](#github-markdown-语法)
7. [GitHub 配置文件](#github-配置文件)
8. [GitHub 模板](#github-模板)

---

## Git 基础命令

### 配置

```bash
# 设置用户信息
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# 查看配置
git config --list

# 设置默认编辑器
git config --global core.editor "code --wait"

# 设置默认分支名
git config --global init.defaultBranch main
```

### 仓库操作

```bash
# 初始化仓库
git init

# 克隆仓库
git clone https://github.com/user/repo.git
git clone git@github.com:user/repo.git

# 添加远程仓库
git remote add origin https://github.com/user/repo.git

# 查看远程仓库
git remote -v

# 更新远程仓库
git remote update

# 删除远程仓库
git remote remove origin
```

### 分支操作

```bash
# 查看分支
git branch
git branch -a
git branch -r

# 创建分支
git branch feature/new-feature

# 切换分支
git checkout feature/new-feature

# 创建并切换分支
git checkout -b feature/new-feature

# 删除分支
git branch -d feature/new-feature
git branch -D feature/new-feature

# 重命名分支
git branch -m old-name new-name

# 合并分支
git merge feature/new-feature

# 变基分支
git rebase main
```

### 提交操作

```bash
# 查看状态
git status

# 添加文件
git add file.txt
git add .
git add *.js

# 提交
git commit -m "commit message"
git commit -am "commit message"

# 修改最后一次提交
git commit --amend

# 查看提交历史
git log
git log --oneline
git log --graph
git log --author="Author Name"
```

### 推送和拉取

```bash
# 推送
git push origin main
git push -u origin main
git push --force

# 拉取
git pull origin main
git pull --rebase origin main

# 获取
git fetch origin
git fetch --all
```

### 撤销操作

```bash
# 撤销工作区更改
git checkout -- file.txt

# 撤销暂存
git reset HEAD file.txt

# 撤销提交
git reset --soft HEAD~1
git reset --hard HEAD~1

# 撤销远程提交
git revert commit-hash

# 清理未跟踪文件
git clean -fd
```

### 标签操作

```bash
# 查看标签
git tag

# 创建标签
git tag v1.0.0
git tag -a v1.0.0 -m "Version 1.0.0"

# 推送标签
git push origin v1.0.0
git push origin --tags

# 删除标签
git tag -d v1.0.0
git push origin :refs/tags/v1.0.0
```

### 储藏操作

```bash
# 储藏更改
git stash
git stash push -m "stash message"

# 查看储藏
git stash list

# 恢复储藏
git stash pop
git stash apply stash@{0}

# 删除储藏
git stash drop stash@{0}
git stash clear
```

## GitHub CLI 命令

### 认证

```bash
# 登录
gh auth login

# 查看认证状态
gh auth status

# 刷新令牌
gh auth refresh

# 退出登录
gh auth logout
```

### 仓库操作

```bash
# 克隆仓库
gh repo clone owner/repo

# 创建仓库
gh repo create repo-name

# 查看仓库
gh repo view owner/repo

# 编辑仓库
gh repo edit owner/repo

# 删除仓库
gh repo delete owner/repo

# Fork 仓库
gh repo fork owner/repo
```

### Issue 操作

```bash
# 创建 Issue
gh issue create

# 查看 Issue
gh issue view 123

# 列出 Issue
gh issue list

# 关闭 Issue
gh issue close 123

# 重新打开 Issue
gh issue reopen 123

# 编辑 Issue
gh issue edit 123
```

### Pull Request 操作

```bash
# 创建 PR
gh pr create

# 查看 PR
gh pr view 123

# 列出 PR
gh pr list

# 合并 PR
gh pr merge 123

# 关闭 PR
gh pr close 123

# 检出 PR
gh pr checkout 123

# 审查 PR
gh pr review 123
```

### Actions 操作

```bash
# 查看工作流
gh workflow list

# 查看工作流运行
gh run list

# 查看运行详情
gh run view 123

# 查看运行日志
gh run view 123 --log

# 重新运行
gh run rerun 123

# 手动触发工作流
gh workflow run workflow-name
```

### Release 操作

```bash
# 创建 Release
gh release create v1.0.0

# 查看 Release
gh release view v1.0.0

# 列出 Release
gh release list

# 删除 Release
gh release delete v1.0.0

# 下载 Release 资产
gh release download v1.0.0
```

### API 操作

```bash
# 调用 API
gh api repos/{owner}/{repo}

# 使用 GraphQL
gh api graphql -f query='{ viewer { login } }'

# 使用 jq 过滤
gh api repos/{owner}/{repo} --jq '.name'
```

## GitHub Actions 语法

### 工作流语法

```yaml
# .github/workflows/workflow.yml
name: Workflow Name

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 0 * * *'
  workflow_dispatch:

env:
  GLOBAL_VAR: value

jobs:
  job-name:
    runs-on: ubuntu-latest
    needs: previous-job
    
    strategy:
      matrix:
        node-version: [14, 16, 18]
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      
      - name: Run commands
        run: |
          echo "Hello World"
          npm install
      
      - name: Use environment variables
        env:
          MY_VAR: value
        run: echo $MY_VAR
      
      - name: Use secrets
        env:
          API_KEY: ${{ secrets.API_KEY }}
        run: echo $API_KEY
```

### 常用 Actions

```yaml
# 代码检出
- uses: actions/checkout@v4

# 设置 Node.js
- uses: actions/setup-node@v4
  with:
    node-version: '18'

# 设置 Python
- uses: actions/setup-python@v4
  with:
    python-version: '3.9'

# 缓存依赖
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

# 上传产物
- uses: actions/upload-artifact@v3
  with:
    name: my-artifact
    path: path/to/artifact

# 下载产物
- uses: actions/download-artifact@v3
  with:
    name: my-artifact

# 创建 Release
- uses: actions/create-release@v1
  with:
    tag_name: ${{ github.ref }}
    release_name: Release ${{ github.ref }}

# 部署到 GitHub Pages
- uses: peaceiris/actions-gh-pages@v3
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: ./public
```

## GitHub API 端点

### REST API

```bash
# 用户信息
GET /users/{username}
GET /user

# 仓库信息
GET /repos/{owner}/{repo}
GET /user/repos

# Issue
GET /repos/{owner}/{repo}/issues
POST /repos/{owner}/{repo}/issues
PATCH /repos/{owner}/{repo}/issues/{issue_number}

# Pull Request
GET /repos/{owner}/{repo}/pulls
POST /repos/{owner}/{repo}/pulls
PATCH /repos/{owner}/{repo}/pulls/{pull_number}

# Actions
GET /repos/{owner}/{repo}/actions/runs
GET /repos/{owner}/{repo}/actions/workflows

# Release
GET /repos/{owner}/{repo}/releases
POST /repos/{owner}/{repo}/releases
```

### GraphQL API

```graphql
# 获取用户信息
query {
  viewer {
    login
    name
    email
  }
}

# 获取仓库信息
query {
  repository(owner: "owner", name: "repo") {
    name
    description
    stargazerCount
  }
}

# 获取 Issue
query {
  repository(owner: "owner", name: "repo") {
    issues(first: 10) {
      nodes {
        title
        body
      }
    }
  }
}
```

## GitHub 快捷键

### 全局快捷键

| 快捷键 | 功能 |
|--------|------|
| `s` 或 `/` | 聚焦搜索框 |
| `g` then `n` | 通知 |
| `g` then `c` | 代码 |
| `g` then `i` | Issues |
| `g` then `p` | Pull Requests |
| `g` then `a` | Actions |
| `g` then `b` | Projects |

### 代码查看快捷键

| 快捷键 | 功能 |
|--------|------|
| `b` | 查看 blame |
| `y` | 获取永久链接 |
| `t` | 文件查找器 |
| `l` | 跳转到行 |
| `w` | 切换分支 |

### Issue 和 PR 快捷键

| 快捷键 | 功能 |
|--------|------|
| `c` | 创建评论 |
| `ctrl+enter` | 提交评论 |
| `r` | 回复评论 |
| `l` | 添加标签 |
| `a` | 添加指派 |
| `m` | 添加里程碑 |

## GitHub Markdown 语法

### 基础语法

```markdown
# 标题 1
## 标题 2
### 标题 3

**粗体**
*斜体*
~~删除线~~

- 无序列表
1. 有序列表

[链接](https://example.com)
![图片](image.png)

> 引用

`代码`
```

代码块
```

### 任务列表

```markdown
- [x] 已完成任务
- [ ] 未完成任务
```

### 表格

```markdown
| 列1 | 列2 | 列3 |
|-----|-----|-----|
| 内容1 | 内容2 | 内容3 |
```

### 折叠内容

```markdown
<details>
<summary>点击展开</summary>

隐藏的内容

</details>
```

### 提示框

```markdown
> [!NOTE]
> 这是一个提示

> [!TIP]
> 这是一个技巧

> [!IMPORTANT]
> 这很重要

> [!WARNING]
> 这是一个警告

> [!CAUTION]
> 这是一个警告
```

## GitHub 配置文件

### .gitignore

```gitignore
# 环境变量
.env
.env.local

# 依赖
node_modules/
vendor/

# 构建产物
dist/
build/

# 日志
*.log

# IDE
.vscode/
.idea/
```

### .github/dependabot.yml

```yaml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
```

### .github/workflows/ci.yml

```yaml
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
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
      - run: npm ci
      - run: npm test
```

### .github/CODEOWNERS

```markdown
* @team-leads
/frontend/ @frontend-team
/backend/ @backend-team
```

### .github/PULL_REQUEST_TEMPLATE.md

```markdown
## 描述

简要描述这个 PR 的目的。

## 更改类型

- [ ] 新功能
- [ ] Bug 修复
- [ ] 文档更新

## 检查清单

- [ ] 代码遵循项目规范
- [ ] 已添加测试
- [ ] 已更新文档
```

## GitHub 模板

### Issue 模板

**Bug 报告**：

```markdown
---
name: Bug 报告
about: 报告一个 bug
title: '[BUG] '
labels: bug
assignees: ''
---

## 描述

简要描述 bug。

## 复现步骤

1. 访问 '...'
2. 点击 '...'
3. 看到错误

## 预期行为

描述你期望发生什么。

## 实际行为

描述实际发生了什么。

## 环境

- 操作系统: [例如 iOS]
- 浏览器: [例如 chrome, safari]
- 版本: [例如 22]
```

**功能请求**：

```markdown
---
name: 功能请求
about: 建议一个新功能
title: '[FEATURE] '
labels: enhancement
assignees: ''
---

## 描述

简要描述你想要的功能。

## 使用场景

描述这个功能的使用场景。

## 建议的解决方案

描述你建议的实现方式。

## 其他信息

添加任何其他有关功能的信息。
```

### PR 模板

```markdown
## 描述

简要描述这个 PR 的目的。

## 更改类型

- [ ] 新功能
- [ ] Bug 修复
- [ ] 文档更新
- [ ] 代码重构
- [ ] 性能优化
- [ ] 测试

## 测试

描述如何测试这些更改。

## 相关 Issue

关闭 #123

## 检查清单

- [ ] 代码遵循项目规范
- [ ] 已添加测试
- [ ] 已更新文档
- [ ] 已通过 CI 检查
```

---

**上一篇：[附录 F：GitHub 快捷键指南](F-shortcuts.md) | 下一篇：[附录 B：Git 别名配置](B-git-aliases.md)**