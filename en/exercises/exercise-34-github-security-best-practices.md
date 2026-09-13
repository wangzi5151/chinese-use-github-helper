# 练习 34：GitHub 安全最佳实践

## 目标

学习如何在 GitHub 项目中实施安全最佳实践，保护代码和数据安全。

## 前置条件

- 有 GitHub 账号
- 有一个测试仓库
- 熟悉基本的 Git 操作

## 步骤

### 1. 启用两步验证

两步验证是保护账户安全的第一道防线。

**启用步骤**：

1. 访问 [GitHub Settings](https://github.com/settings/security)
2. 点击 "Enable two-factor authentication"
3. 选择验证方式：
   - 认证器应用（推荐）
   - 短信
   - 安全密钥

**推荐应用**：
- Google Authenticator
- Authy
- 1Password

### 2. 使用 SSH 密钥

SSH 密钥比密码更安全，且无需每次输入密码。

**生成 SSH 密钥**：

```bash
# 生成 Ed25519 密钥（推荐）
ssh-keygen -t ed25519 -C "your@email.com"

# 生成 RSA 密钥
ssh-keygen -t rsa -b 4096 -C "your@email.com"
```

**添加 SSH 密钥到 GitHub**：

```bash
# 复制公钥
cat ~/.ssh/id_ed25519.pub | pbcopy  # macOS
cat ~/.ssh/id_ed25519.pub | xclip -selection clipboard  # Linux

# 在 GitHub 上添加：
# Settings → SSH and GPG keys → New SSH key
```

**测试 SSH 连接**：

```bash
ssh -T git@github.com
```

### 3. 使用 Personal Access Token

Personal Access Token 用于替代密码进行 API 认证。

**创建 Token**：

1. 访问 [GitHub Settings](https://github.com/settings/tokens)
2. 点击 "Generate new token"
3. 选择权限范围
4. 设置过期时间
5. 点击 "Generate token"

**使用 Token**：

```bash
# 使用 Token 克隆仓库
git clone https://YOUR_TOKEN@github.com/owner/repo.git

# 配置 Git 使用 Token
git config --global credential.helper store
echo "https://YOUR_TOKEN@github.com" > ~/.git-credentials
```

### 4. 配置 .gitignore

确保敏感信息不被提交到仓库。

**常见的 .gitignore 配置**：

```gitignore
# 环境变量
.env
.env.local
.env.*.local

# 密钥文件
*.pem
*.key
id_rsa
id_ed25519

# 配置文件
config.json
credentials.json
secrets.yml

# 依赖目录
node_modules/
vendor/
venv/

# 日志文件
*.log
logs/

# 操作系统文件
.DS_Store
Thumbs.db

# IDE 配置
.idea/
.vscode/
*.swp
```

### 5. 启用 Secret Scanning

Secret Scanning 可以检测代码中泄露的敏感信息。

**启用步骤**：

1. 访问仓库设置
2. 点击 "Code security"
3. 启用 "Secret scanning"
4. 启用 "Push protection"

**测试 Secret Scanning**：

```bash
# 创建一个包含敏感信息的文件
echo "API_KEY=1234567890abcdef" > .env

# 提交并推送
git add .env
git commit -m "Add .env file"
git push

# GitHub 会检测到敏感信息并阻止推送
```

### 6. 启用 Dependabot

Dependabot 可以自动检查和更新项目依赖。

**启用步骤**：

1. 访问仓库设置
2. 点击 "Code security"
3. 启用 "Dependabot alerts"
4. 启用 "Dependabot security updates"

**配置 Dependabot**：

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 10
```

### 7. 配置分支保护

分支保护规则可以防止意外的代码变更。

**配置步骤**：

1. 访问仓库设置
2. 点击 "Branches"
3. 点击 "Add rule"
4. 配置保护规则

**推荐配置**：

```yaml
# 分支保护规则
required_pull_request_reviews:
  required_approving_review_count: 2
  dismiss_stale_reviews: true
  require_code_owner_reviews: true

required_status_checks:
  strict: true
  contexts:
    - "ci/build"
    - "ci/test"

enforce_admins: true

restrictions:
  users: ["admin-user"]
  teams: ["core-team"]
```

### 8. 配置 CODEOWNERS

CODEOWNERS 文件定义了代码审查的责任人。

**创建 CODEOWNERS 文件**：

```bash
# 创建 .github/CODEOWNERS 文件
cat > .github/CODEOWNERS << EOF
# 默认所有者
* @team-leads

# 安全相关代码
/security/ @security-team

# 前端代码
/src/frontend/ @frontend-team

# 后端代码
/src/backend/ @backend-team

# 配置文件
*.yml @devops-team
Dockerfile @devops-team
EOF
```

### 9. 使用 GitHub Advanced Security

GitHub Advanced Security 提供了更高级的安全功能。

**功能包括**：
- Code Scanning（代码扫描）
- Secret Scanning（密钥扫描）
- Dependency Review（依赖审查）
- Security Overview（安全概览）

**启用 Code Scanning**：

```yaml
# .github/workflows/codeql.yml
name: "CodeQL"

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 0 * * 1'

jobs:
  analyze:
    name: Analyze
    runs-on: ubuntu-latest
    permissions:
      actions: read
      contents: read
      security-events: write

    strategy:
      fail-fast: false
      matrix:
        language: ['javascript', 'python']

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Initialize CodeQL
      uses: github/codeql-action/init@v3
      with:
        languages: ${{ matrix.language }}

    - name: Autobuild
      uses: github/codeql-action/autobuild@v3

    - name: Perform CodeQL Analysis
      uses: github/codeql-action/analyze@v3
```

### 10. 安全审计

定期进行安全审计，发现和修复安全漏洞。

**安全审计清单**：

```markdown
# 安全审计清单

## 账户安全
- [ ] 启用两步验证
- [ ] 使用 SSH 密钥
- [ ] 配置 Personal Access Token
- [ ] 定期审查登录活动

## 仓库安全
- [ ] 配置 .gitignore
- [ ] 启用 Secret Scanning
- [ ] 启用 Dependabot
- [ ] 配置分支保护
- [ ] 配置 CODEOWNERS

## CI/CD 安全
- [ ] 使用 Secrets
- [ ] 限制 Actions 权限
- [ ] 验证 Action 来源
- [ ] 审查第三方 Action

## 依赖安全
- [ ] 定期更新依赖
- [ ] 使用 lock 文件
- [ ] 扫描依赖漏洞
- [ ] 监控安全公告
```

**使用 GitHub CLI 进行安全审计**：

```bash
# 检查仓库安全配置
gh api repos/{owner}/{repo} --jq '.security_and_analysis'

# 检查 Secret Scanning 状态
gh api repos/{owner}/{repo}/secret-scanning/alerts

# 检查 Dependabot 警报
gh api repos/{owner}/{repo}/vulnerability-alerts

# 检查分支保护规则
gh api repos/{owner}/{repo}/branches/main/protection
```

## 挑战

1. **挑战 1**：为你的仓库配置完整的安全设置
2. **挑战 2**：创建一个安全审计脚本
3. **挑战 3**：配置 GitHub Advanced Security
4. **挑战 4**：创建一个自动安全扫描工作流
5. **挑战 5**：制定一个安全事件响应计划

## 思考

1. 为什么安全在软件开发中很重要？
2. 如何平衡安全性和开发效率？
3. 如何处理安全漏洞？
4. 如何培训团队成员的安全意识？

## 相关资源

- [GitHub 安全文档](https://docs.github.com/en/code-security)
- [GitHub Advanced Security 文档](https://docs.github.com/en/code-security/advanced-security)
- [GitHub Secret Scanning 文档](https://docs.github.com/en/code-security/secret-scanning)
- [GitHub Dependabot 文档](https://docs.github.com/en/code-security/dependabot)
- [GitHub 分支保护文档](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/managing-a-branch-protection-rule)

---

**上一篇：[练习 33：GitHub API 集成](exercise-33-github-api-integration.md) | 下一篇：[练习 35：GitHub 团队协作](exercise-35-github-team-collaboration.md)**