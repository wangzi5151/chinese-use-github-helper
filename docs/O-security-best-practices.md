# GitHub 安全最佳实践

> 本章将详细介绍如何保护你的 GitHub 账户和代码仓库安全，涵盖账户安全、仓库安全、CI/CD 安全、依赖安全和团队安全五个方面。

---

## 目录

1. [账户安全](#账户安全)
2. [仓库安全](#仓库安全)
3. [CI/CD 安全](#cicd-安全)
4. [依赖安全](#依赖安全)
5. [团队安全](#团队安全)
6. [安全事件响应](#安全事件响应)
7. [安全检查清单](#安全检查清单)
8. [相关资源](#相关资源)

---

## 账户安全

### 1. 启用两步验证 (2FA)

两步验证是保护账户安全的第一道防线。即使密码泄露，攻击者也无法访问你的账户。

**设置步骤**：
1. 进入 **Settings** → **Password and authentication**
2. 点击 **Enable two-factor authentication**
3. 选择验证方式：
   - 认证器应用（推荐）
   - 短信
   - 安全密钥

**推荐使用**：
- **认证器应用**：Google Authenticator、Authy、1Password
- **安全密钥**：YubiKey、Titan Security Key

**为什么推荐认证器应用？**
- 短信可能被拦截或SIM卡被劫持
- 认证器应用生成基于时间的一次性密码（TOTP）
- 即使手机离线也能使用

**备份恢复代码**：
启用2FA后，GitHub会提供一组恢复代码。务必将其安全存储在多个位置：
- 密码管理器
- 加密的U盘
- 纸质备份（存放在安全位置）

### 2. 使用 SSH 密钥

SSH密钥比密码更安全，且无需每次输入密码。

**生成 SSH 密钥**：
```bash
# 推荐使用 Ed25519（更安全、更快）
ssh-keygen -t ed25519 -C "your@email.com"

# 或使用 RSA（兼容性更好）
ssh-keygen -t rsa -b 4096 -C "your@email.com"
```

**安全建议**：
- 为不同设备使用不同密钥
- 为密钥设置强密码
- 定期轮换密钥（建议每6-12个月）
- 使用ssh-agent管理密钥

**密钥管理最佳实践**：
```bash
# 启动ssh-agent
eval "$(ssh-agent -s)"

# 添加密钥到ssh-agent
ssh-add ~/.ssh/id_ed25519

# 列出已添加的密钥
ssh-add -l

# 删除所有密钥
ssh-add -D
```

### 3. 使用个人访问令牌 (PAT)

PAT用于替代密码进行API认证和Git操作。

**创建令牌**：
1. **Settings** → **Developer settings** → **Personal access tokens**
2. 点击 **Generate new token**
3. 选择最小权限
4. 设置过期时间

**安全建议**：
- 使用 fine-grained token（细粒度令牌）
- 定期轮换（建议每90天）
- 不要在代码中硬编码
- 使用环境变量或密钥管理工具

**Fine-grained token vs Classic token**：
| 特性 | Fine-grained token | Classic token |
|------|-------------------|---------------|
| 权限范围 | 细粒度（特定仓库、特定权限） | 粗粒度（所有仓库或特定仓库） |
| 安全性 | 更高 | 较低 |
| 推荐使用 | 生产环境 | 开发测试 |

### 4. 审查登录活动

定期检查账户的登录活动，发现异常及时处理。

**查看登录活动**：
1. **Settings** → **Security log**
2. 查看最近的登录事件
3. 检查是否有异常IP地址或地理位置

**设置登录通知**：
1. **Settings** → **Password and authentication**
2. 启用 **Login activity notifications**
3. 选择通知方式（邮件、短信）

### 2. 使用 SSH 密钥

**生成 SSH 密钥**：
```bash
# 推荐使用 Ed25519
ssh-keygen -t ed25519 -C "your@email.com"

# 或使用 RSA
ssh-keygen -t rsa -b 4096 -C "your@email.com"
```

**安全建议**：
- 为不同设备使用不同密钥
- 为密钥设置密码
- 定期轮换密钥

### 3. 使用个人访问令牌 (PAT)

**创建令牌**：
1. **Settings** → **Developer settings** → **Personal access tokens**
2. 点击 **Generate new token**
3. 选择最小权限
4. 设置过期时间

**安全建议**：
- 使用 fine-grained token
- 定期轮换
- 不要在代码中硬编码

## 仓库安全

### 1. .gitignore 配置

确保敏感信息不被提交是仓库安全的基础。

**常见的敏感信息类型**：
- API密钥和令牌
- 数据库凭据
- 私钥文件
- 环境变量文件
- 配置文件中的敏感数据

**完整的 .gitignore 模板**：
```gitignore
# 环境变量
.env
.env.local
.env.*.local
.env.development
.env.production
.env.staging

# 密钥文件
*.pem
*.key
*.p12
*.pfx
id_rsa
id_ed25519
id_dsa

# 配置文件
config.json
credentials.json
secrets.yml
application-local.yml

# 依赖目录
node_modules/
vendor/
venv/
__pycache__/

# 日志文件
*.log
logs/

# 操作系统文件
.DS_Store
Thumbs.db

# IDE配置
.idea/
.vscode/
*.swp
*.swo

# 构建产物
dist/
build/
target/
```

**使用模板**：
```bash
# 下载官方模板
curl -o .gitignore https://raw.githubusercontent.com/github/gitignore/main/Node.gitignore

# 或使用GitHub API
gh api repos/github/gitignore/contents/Node.gitignore -q '.content' | base64 -d > .gitignore
```

### 2. 分支保护

分支保护规则可以防止意外的代码变更和强制推送。

**设置步骤**：
1. **Settings** → **Branches**
2. 点击 **Add rule**
3. 配置保护规则：
   - 要求 PR 审查
   - 要求状态检查通过
   - 要求分支最新
   - 禁止强制推送
   - 限制推送权限

**高级保护配置**：
```yaml
# .github/branch-protection.yml（需要GitHub App）
protection:
  main:
    required_pull_request_reviews:
      required_approving_review_count: 2
      dismiss_stale_reviews: true
      require_code_owner_reviews: true
    required_status_checks:
      strict: true
      contexts:
        - "ci/build"
        - "ci/test"
        - "security/scan"
    enforce_admins: true
    restrictions:
      users: ["admin-user"]
      teams: ["core-team"]
```

**保护规则最佳实践**：
- 为`main`和`release`分支设置严格保护
- 要求至少2个代码审查
- 启用状态检查（CI/CD、安全扫描）
- 禁止强制推送
- 定期审查保护规则

### 3. 代码所有者 (CODEOWNERS)

CODEOWNERS文件定义了代码审查的责任人，确保每个代码变更都有合适的审查者。

**文件位置**：`.github/CODEOWNERS`或`docs/CODEOWNERS`

**示例配置**：
```
# 默认所有者
* @team-leads

# 安全相关代码
/security/ @security-team
*.security.* @security-team

# 前端代码
/src/frontend/ @frontend-team
*.js @frontend-team
*.ts @frontend-team
*.jsx @frontend-team
*.tsx @frontend-team

# 后端代码
/src/backend/ @backend-team
*.py @backend-team
*.java @backend-team

# 配置文件
*.yml @devops-team
*.yaml @devops-team
Dockerfile @devops-team
docker-compose.yml @devops-team

# 文档
*.md @docs-team
/docs/ @docs-team

# 依赖文件
package.json @security-team
requirements.txt @security-team
```

**CODEOWNERS最佳实践**：
- 为每个目录和文件类型指定所有者
- 使用团队而非个人作为所有者
- 定期审查和更新所有者
- 在PR模板中提醒审查CODEOWNERS

### 4. Secret Scanning

Secret Scanning可以检测代码中泄露的敏感信息。

**启用步骤**：
1. **Settings** → **Code security**
2. 启用 **Secret scanning**
3. 启用 **Push protection**（防止推送包含敏感信息的代码）

**支持的密钥类型**：
- AWS访问密钥
- GitHub个人访问令牌
- Azure DevOps令牌
- Slack webhook URL
- 数据库连接字符串
- 私钥文件

**自定义模式**：
```yaml
# .github/secret-scanning.yml
patterns:
  - name: "内部API密钥"
    pattern: "internal-api-[a-zA-Z0-9]{32}"
    alert: true
  - name: "数据库密码"
    pattern: "db_password:\\s*[a-zA-Z0-9!@#$%^&*]{16,}"
    alert: true
```

**处理检测到的密钥**：
1. 立即撤销泄露的密钥
2. 生成新的密钥
3. 更新所有使用该密钥的服务
4. 检查是否有其他地方使用相同密钥
5. 更新.gitignore防止再次泄露

### 5. Dependabot

Dependabot可以自动检查和更新项目依赖，修复安全漏洞。

**启用步骤**：
1. **Settings** → **Code security**
2. 启用 **Dependabot alerts**
3. 启用 **Dependabot security updates**
4. 配置自动更新

**配置文件**：`.github/dependabot.yml`
```yaml
version: 2
updates:
  # npm依赖
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
      time: "09:00"
      timezone: "Asia/Shanghai"
    open-pull-requests-limit: 10
    reviewers:
      - "frontend-team"
    labels:
      - "dependencies"
      - "security"
    commit-message:
      prefix: "deps"
      prefix-development: "deps-dev"
    
  # Python依赖
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 5
    
  # Docker镜像
  - package-ecosystem: "docker"
    directory: "/"
    schedule:
      interval: "weekly"
    
  # GitHub Actions
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

**Dependabot最佳实践**：
- 启用安全更新
- 设置合理的更新频率
- 自动合并minor和patch更新
- 定期审查更新日志
- 测试更新后的代码

## CI/CD 安全

CI/CD管道是软件开发的核心，但也可能成为安全漏洞的来源。

### 1. 使用 Secrets

Secrets用于存储敏感信息，如API密钥、密码等。

**在 GitHub Actions 中使用 Secrets**：
```yaml
- name: Deploy
  env:
    API_KEY: ${{ secrets.API_KEY }}
    DATABASE_URL: ${{ secrets.DATABASE_URL }}
  run: ./deploy.sh
```

**Secrets类型**：
- **环境Secrets**：特定环境的密钥
- **仓库Secrets**：仓库级别的密钥
- **组织Secrets**：组织级别的密钥（可跨仓库共享）

**Secrets最佳实践**：
- 使用环境Secrets而非仓库Secrets
- 为不同环境（开发、测试、生产）使用不同密钥
- 定期轮换密钥
- 限制Secrets的访问权限
- 不要在日志中打印Secrets

**防止Secrets泄露**：
```yaml
# 使用masking防止泄露
- name: Use secret
  run: |
    echo "::add-mask::${{ secrets.MY_SECRET }}"
    # 使用密钥的命令
```

### 2. 限制 Actions 权限

遵循最小权限原则，只授予必要的权限。

**权限配置**：
```yaml
# 工作流级别权限
permissions:
  contents: read
  pages: write
  id-token: write
  issues: write
  pull-requests: write

# 作业级别权限
jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pages: write
      id-token: write
    steps:
      - uses: actions/checkout@v4
      - name: Deploy
        run: ./deploy.sh
```

**权限类型**：
- `contents`：仓库内容访问
- `pages`：GitHub Pages部署
- `id-token`：OIDC令牌
- `issues`：Issue管理
- `pull-requests`：PR管理
- `actions`：Actions管理
- `packages`：包管理
- `security-events`：安全事件

### 3. 验证 Action 来源

使用第三方Action时，必须验证其来源和完整性。

**验证方法**：
```yaml
# 使用完整的 commit SHA（最安全）
- uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11

# 使用标签（较安全，但可能被篡改）
- uses: actions/checkout@v4

# 使用分支（不推荐）
- uses: actions/checkout@main
```

**验证工具**：
```bash
# 使用actionlint检查Action语法
actionlint .github/workflows/*.yml

# 使用zizmor检查安全问题
zizmor .github/workflows/*.yml
```

### 4. 审查第三方 Action

在使用第三方Action前，必须进行安全审查。

**审查清单**：
- [ ] 检查Action的源代码
- [ ] 查看Action的权限需求
- [ ] 验证Action的维护状态
- [ ] 检查是否有已知漏洞
- [ ] 查看社区评价和使用情况

**审查步骤**：
1. **查看源代码**：
   ```bash
   # 克隆Action仓库
   git clone https://github.com/action/checkout.git
   cd checkout
   
   # 检查代码
   grep -r "secrets\." .
   grep -r "env\." .
   ```

2. **检查权限需求**：
   ```yaml
   # 查看action.yml
   name: 'Checkout'
   description: 'Checkout a Git repository'
   inputs:
     repository:
       description: 'Repository name'
       required: false
   ```

3. **验证维护状态**：
   - 检查最后更新时间
   - 查看Issue和PR数量
   - 检查版本发布频率

### 5. 使用可信的Action

**官方Action**：
- `actions/checkout`：代码检出
- `actions/setup-node`：Node.js设置
- `actions/setup-python`：Python设置
- `actions/cache`：缓存管理
- `actions/upload-artifact`：上传构建产物

**可信的第三方Action**：
- `docker/build-push-action`：Docker构建
- `aws-actions/configure-aws-credentials`：AWS配置
- `google-github-actions/setup-gcloud`：Google Cloud配置

### 6. 安全扫描集成

在CI/CD管道中集成安全扫描。

**代码扫描**：
```yaml
- name: Run CodeQL Analysis
  uses: github/codeql-action/analyze@v3
  with:
    languages: javascript, python
    queries: security-extended
```

**依赖扫描**：
```yaml
- name: Run Dependency Review
  uses: actions/dependency-review-action@v4
  with:
    fail-on-severity: high
    deny-licenses: GPL-3.0, AGPL-3.0
```

**容器扫描**：
```yaml
- name: Run Trivy vulnerability scanner
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: 'myapp:latest'
    format: 'sarif'
    output: 'trivy-results.sarif'
```

**密钥扫描**：
```yaml
- name: Scan for secrets
  uses: trufflesecurity/trufflehog@main
  with:
    path: ./
    base: ${{ github.event.repository.default_branch }}
```

## 依赖安全

依赖安全是软件安全的重要组成部分，许多安全漏洞都来自第三方依赖。

### 1. 定期更新依赖

定期更新依赖可以修复已知的安全漏洞。

**npm更新**：
```bash
# 更新所有依赖
npm update

# 更新特定包
npm update package-name

# 更新到最新版本（可能包含破坏性变更）
npm install package-name@latest

# 检查过时的依赖
npm outdated

# 使用ncu更新package.json
npx npm-check-updates -u
```

**pip更新**：
```bash
# 更新所有依赖
pip install --upgrade -r requirements.txt

# 更新特定包
pip install --upgrade package-name

# 检查过时的依赖
pip list --outdated

# 使用pip-review更新
pip install pip-review
pip-review --local --interactive
```

**composer更新**：
```bash
# 更新所有依赖
composer update

# 更新特定包
composer update package-name

# 检查过时的依赖
composer outdated
```

**自动化更新**：
使用Dependabot或Renovate自动更新依赖。

### 2. 使用 lock 文件

lock文件可以确保团队成员使用相同版本的依赖。

**常见的lock文件**：
- `package-lock.json`（npm）
- `yarn.lock`（yarn）
- `pnpm-lock.yaml`（pnpm）
- `composer.lock`（composer）
- `Pipfile.lock`（pipenv）
- `poetry.lock`（poetry）
- `Gemfile.lock`（bundler）

**lock文件最佳实践**：
- 将lock文件提交到版本控制
- 不要手动编辑lock文件
- 定期更新lock文件
- 在CI/CD中验证lock文件

**lock文件验证**：
```yaml
# GitHub Actions中验证lock文件
- name: Verify lock file
  run: |
    npm ci
    # 检查是否有未提交的变更
    git diff --exit-code package-lock.json
```

### 3. 扫描依赖漏洞

使用工具扫描依赖中的已知漏洞。

**npm审计**：
```bash
# 运行审计
npm audit

# 自动修复漏洞
npm audit fix

# 强制修复（可能包含破坏性变更）
npm audit fix --force

# 只查看高危漏洞
npm audit --audit-level=high

# 生成审计报告
npm audit --json > audit-report.json
```

**pip审计**：
```bash
# 安装pip-audit
pip install pip-audit

# 运行审计
pip-audit

# 修复漏洞
pip-audit --fix

# 生成报告
pip-audit --format json > audit-report.json
```

**bundler审计**：
```bash
# 安装bundler-audit
gem install bundler-audit

# 更新漏洞数据库
bundler-audit update

# 运行审计
bundler-audit check

# 在CI/CD中运行
bundler-audit check --ignore CVE-2023-XXXXX
```

**综合审计工具**：
```bash
# 使用Snyk
npm install -g snyk
snyk test

# 使用OWASP Dependency-Check
dependency-check --project "My Project" --scan ./src

# 使用Trivy
trivy fs --security-checks vuln .
```

### 4. 使用依赖白名单

只允许使用经过审查的依赖。

**npm白名单**：
```json
{
  "dependencies": {
    "lodash": "^4.17.21",
    "express": "^4.18.2"
  },
  "overrides": {
    "minimatch": "^5.0.0"
  }
}
```

**pip白名单**：
```txt
# requirements.txt
# 只允许特定版本
package==1.2.3
another-package>=2.0.0,<3.0.0
```

**依赖审查**：
```yaml
# GitHub Actions中审查依赖
- name: Review dependency changes
  uses: actions/dependency-review-action@v4
  with:
    fail-on-severity: high
    deny-licenses: GPL-3.0, AGPL-3.0
    config-file: ./.github/dependency-review.yml
```

### 5. 监控安全公告

及时了解依赖中的安全漏洞。

**安全公告来源**：
- [GitHub Advisory Database](https://github.com/advisories)
- [National Vulnerability Database](https://nvd.nist.gov/)
- [Snyk Vulnerability Database](https://snyk.io/vuln/)
- [npm Security Advisories](https://www.npmjs.com/advisories)
- [PyPI Security Advisories](https://pypi.org/security/)

**自动化监控**：
```yaml
# 使用Dependabot监控
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "daily"
    open-pull-requests-limit: 10
    security-updates-only: true
```

### 6. 依赖安全最佳实践

**选择依赖**：
- 选择活跃维护的项目
- 检查项目的Issue和PR
- 查看项目的贡献者数量
- 检查项目的许可证
- 避免使用已弃用的项目

**管理依赖**：
- 定期审查依赖
- 移除未使用的依赖
- 使用依赖分析工具
- 建立依赖审查流程

**安全配置**：
```json
// package.json
{
  "scripts": {
    "audit": "npm audit",
    "audit:fix": "npm audit fix",
    "outdated": "npm outdated",
    "update": "npm update"
  }
}
```

## 团队安全

团队安全是确保整个团队遵循安全最佳实践。

### 1. 最小权限原则

只授予完成工作所必需的最小权限。

**权限管理**：
- 为不同角色定义不同的权限级别
- 定期审查权限（建议每季度）
- 及时移除不再需要的权限
- 使用团队而非个人权限

**GitHub权限级别**：
- **Read**：只读访问
- **Triage**：Issue和PR管理
- **Write**：代码推送和分支管理
- **Maintain**：仓库管理（不含危险操作）
- **Admin**：完全管理权限

**权限审查流程**：
```bash
# 使用GitHub CLI查看团队权限
gh api repos/{owner}/{repo}/collaborators --jq '.[].login'

# 查看团队成员
gh api orgs/{org}/teams/{team}/members --jq '.[].login'

# 审计权限
gh api repos/{owner}/{repo}/collaborators --jq '.[] | {login, permissions}'
```

### 2. 安全培训

定期进行安全培训，提高团队的安全意识。

**培训内容**：
- 安全编码实践
- 常见安全漏洞（OWASP Top 10）
- 密钥管理
- 社会工程学防范
- 安全事件响应

**培训资源**：
- [GitHub Security Best Practices](https://docs.github.com/en/code-security)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/)
- [Snyk Learn](https://learn.snyk.io/)
- [Secure Code Warrior](https://www.securecodewarrior.com/)

**培训计划**：
- 新员工入职培训
- 季度安全培训
- 年度安全意识测试
- 安全事件后复盘培训

### 3. 代码审查

代码审查是发现安全漏洞的重要手段。

**安全审查清单**：
- [ ] 输入验证
- [ ] 输出编码
- [ ] 身份验证和授权
- [ ] 密钥和敏感信息处理
- [ ] 错误处理
- [ ] 日志记录
- [ ] 依赖安全
- [ ] 配置安全

**安全审查工具**：
```bash
# 使用ESLint安全插件
npm install eslint-plugin-security

# 使用Semgrep
semgrep --config=auto .

# 使用Bandit（Python）
pip install bandit
bandit -r src/

# 使用SonarQube
sonar-scanner -Dsonar.projectKey=my-project
```

**安全审查流程**：
1. 开发者提交代码
2. 自动化安全扫描
3. 人工安全审查
4. 修复安全问题
5. 再次审查
6. 合并代码

### 4. 安全策略

建立明确的安全策略和流程。

**安全策略内容**：
- 密码策略
- 访问控制策略
- 数据保护策略
- 事件响应策略
- 合规要求

**安全策略模板**：
```markdown
# 安全策略

## 报告安全漏洞

如果您发现安全漏洞，请通过以下方式报告：
- 邮件：security@example.com
- GitHub Security Advisories

## 支持版本

| 版本 | 支持状态 |
|------|----------|
| 2.x  | ✅ 支持 |
| 1.x  | ❌ 不支持 |

## 安全更新

我们会在发现安全漏洞后尽快发布更新。
```

### 5. 安全工具集成

集成安全工具到开发流程中。

**IDE安全插件**：
- VS Code：Security Scanner、SonarLint
- IntelliJ：SonarLint、Find Security Bugs

**CI/CD安全工具**：
```yaml
# 代码扫描
- name: Run CodeQL
  uses: github/codeql-action/analyze@v3

# 依赖扫描
- name: Run Dependency Review
  uses: actions/dependency-review-action@v4

# 密钥扫描
- name: Scan for secrets
  uses: trufflesecurity/trufflehog@main

# 容器扫描
- name: Run Trivy
  uses: aquasecurity/trivy-action@master
```

---

## 安全事件响应

即使采取了所有预防措施，安全事件仍可能发生。建立有效的事件响应机制至关重要。

### 1. 事件响应计划

**响应团队**：
- 安全负责人
- 开发负责人
- 运维负责人
- 法律顾问
- 公关负责人

**响应流程**：
1. **检测**：发现安全事件
2. **评估**：评估事件严重程度
3. **遏制**：限制事件影响范围
4. **修复**：修复安全漏洞
5. **恢复**：恢复正常服务
6. **总结**：总结经验教训

### 2. 事件分类

**严重程度**：
- **P0（严重）**：数据泄露、服务中断
- **P1（高）**：未授权访问、权限提升
- **P2（中）**：信息泄露、拒绝服务
- **P3（低）**：安全配置问题、弱密码

**响应时间**：
- P0：立即响应（15分钟内）
- P1：1小时内响应
- P2：24小时内响应
- P3：7天内响应

### 3. 事件响应模板

```markdown
# 安全事件响应报告

## 事件概述
- **事件ID**：INC-2024-001
- **发现时间**：2024-01-15 10:30 UTC
- **报告人**：张三
- **严重程度**：P1

## 事件描述
[详细描述事件]

## 影响范围
- 受影响系统：[系统列表]
- 受影响用户：[用户数量]
- 数据泄露：[是/否]

## 响应措施
1. [措施1]
2. [措施2]
3. [措施3]

## 根本原因
[分析根本原因]

## 改进措施
1. [改进1]
2. [改进2]
3. [改进3]

## 时间线
- 10:30：发现事件
- 10:45：启动响应流程
- 11:00：遏制事件
- 12:00：修复漏洞
- 13:00：恢复服务
```

### 4. 事件响应工具

**监控工具**：
- GitHub Security Alerts
- GitHub Audit Log
- GitHub Advanced Security

**响应工具**：
- GitHub Security Advisories
- GitHub Incident Response
- GitHub Status Page

**通信工具**：
- Slack/Teams安全频道
- 邮件列表
- 电话会议

## 安全检查清单

### 账户安全
- [ ] 启用两步验证
- [ ] 使用 SSH 密钥
- [ ] 配置个人访问令牌
- [ ] 定期审查登录活动

### 仓库安全
- [ ] 配置 .gitignore
- [ ] 设置分支保护
- [ ] 启用 Secret Scanning
- [ ] 启用 Dependabot
- [ ] 配置 CODEOWNERS

### CI/CD 安全
- [ ] 使用 Secrets
- [ ] 限制 Actions 权限
- [ ] 验证 Action 来源
- [ ] 审查第三方 Action

### 依赖安全
- [ ] 定期更新依赖
- [ ] 使用 lock 文件
- [ ] 扫描依赖漏洞
- [ ] 监控安全公告

## 相关资源

- [GitHub 安全文档](https://docs.github.com/en/security)
- [GitHub 安全最佳实践](https://docs.github.com/en/code-security)
- [GitHub 安全公告](https://github.com/advisories)
