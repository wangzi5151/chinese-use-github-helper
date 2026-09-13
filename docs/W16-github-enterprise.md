# GitHub Enterprise 企业版功能

> 本章将详细介绍 GitHub Enterprise 的企业级功能，包括版本对比、SAML SSO 配置、审计日志、IP 允许列表、合规和治理、安全功能、管理 API 以及最佳实践。

---

## 目录

1. [Enterprise 版本对比](#enterprise-版本对比)
2. [SAML SSO 配置](#saml-sso-配置)
3. [审计日志](#审计日志)
4. [IP 允许列表](#ip-允许列表)
5. [合规和治理](#合规和治理)
6. [安全功能](#安全功能)
7. [管理 API](#管理-api)
8. [企业级部署](#企业级部署)
9. [成本优化](#成本优化)
10. [最佳实践](#最佳实践)
11. [相关资源](#相关资源)

---

## Enterprise 版本对比

GitHub 提供三种主要版本，满足不同规模团队的需求。

### 版本功能对比

| 功能 | Free | Team ($4/月) | Enterprise ($21/月) |
|------|------|--------------|---------------------|
| 仓库 | 无限 | 无限 | 无限 |
| 协作者 | 无限 | 无限 | 无限 |
| GitHub Pages | ✅ | ✅ | ✅ |
| GitHub Actions | 2000分钟/月 | 3000分钟/月 | 50000分钟/月 |
| 容量限制 | 1GB | 2GB | 50GB |
| SAML SSO | ❌ | ❌ | ✅ |
| 审计日志 | ❌ | ❌ | ✅ |
| IP 允许列表 | ❌ | ❌ | ✅ |
| 高级安全 | ❌ | ❌ | ✅ |
| 支持 | 社区 | 优先支持 | 24/7 支持 |
| SLA | ❌ | ❌ | 99.9% |

### 选择合适的版本

**Free 版本适合**：
- 个人开发者
- 开源项目
- 小型团队（<10人）
- 学习和实验

**Team 版本适合**：
- 中小型团队（10-50人）
- 需要优先支持
- 需要更多 Actions 分钟数
- 需要代码审查功能

**Enterprise 版本适合**：
- 大型企业（>50人）
- 需要 SSO/SAML
- 需要审计日志
- 需要合规性支持
- 需要高级安全功能

### 成本计算

**Actions 分钟数成本**：
```
Free: 2000分钟/月（免费）
Team: 3000分钟/月（$4/用户/月）
Enterprise: 50000分钟/月（$21/用户/月）

额外分钟数：$0.008/分钟
```

**存储成本**：
```
Free: 1GB（免费）
Team: 2GB（$4/用户/月）
Enterprise: 50GB（$21/用户/月）

额外存储：$0.008/MB/月
```

**示例计算**：
```
团队规模：100人
Actions 分钟数：100,000分钟/月
存储需求：100GB

Enterprise 成本：
- 基础费用：100 × $21 = $2,100/月
- 额外分钟数：(100,000 - 50,000) × $0.008 = $400/月
- 额外存储：(100GB - 50GB) × $0.008 × 1024 = $409.6/月
- 总计：$2,100 + $400 + $409.6 = $2,909.6/月
```

## SAML SSO 配置

SAML SSO（安全断言标记语言单点登录）允许企业使用现有的身份提供商（IdP）管理 GitHub 访问。

### 配置 SAML

**手动配置步骤**：
1. **Settings** → **Authentication security** → **SAML single sign-on**
2. 配置以下信息：
   - **Sign on URL**：身份提供商的登录 URL
   - **Issuer**：身份提供商的实体 ID
   - **Public certificate**：上传身份提供商的公共证书
   - **Signature method**：选择签名算法（推荐 SHA-256）
   - **Digest method**：选择摘要算法（推荐 SHA-256）

**支持的身份提供商**：
- Azure Active Directory
- Okta
- OneLogin
- PingIdentity
- Google Workspace
- LDAP（通过 SAML 桥接）

### 自动配置 SCIM

SCIM（跨域身份管理）可以自动同步用户和团队信息。

**使用 GitHub CLI 配置**：
```bash
# 配置 SAML SSO
gh api orgs/{org}/identity-provider \
  --method PUT \
  -f type='saml' \
  -f sso_url='https://your-idp.com/saml/sso' \
  -f issuer='your-entity-id' \
  -f certificate='-----BEGIN CERTIFICATE-----\n...\n-----END CERTIFICATE-----'

# 配置 SCIM
gh api orgs/{org}/scim/v2 \
  --method PUT \
  -f scim_url='https://your-idp.com/scim/v2' \
  -f token='your-scim-token'
```

**SCIM 功能**：
- 自动创建用户账户
- 自动禁用用户账户
- 自动同步团队成员
- 自动更新用户信息
- 支持组映射

### SAML SSO 最佳实践

**安全配置**：
- 使用强加密算法（SHA-256）
- 定期轮换证书
- 启用双因素认证
- 限制 SSO 管理员权限

**用户管理**：
- 使用 SCIM 自动同步
- 定期审查用户权限
- 及时禁用离职员工账户
- 使用团队管理权限

**故障排除**：
```bash
# 测试 SAML 配置
gh api orgs/{org}/identity-provider

# 查看 SAML 事件
gh api orgs/{org}/audit-log --jq '.[] | select(.action | startswith("saml"))'

# 重置 SAML 配置
gh api orgs/{org}/identity-provider --method DELETE
```

### SAML SSO 集成示例

**Azure Active Directory 集成**：
```yaml
# Azure AD 应用配置
Application ID: your-app-id
Entity ID: https://github.com/orgs/your-org
Reply URL: https://github.com/orgs/your-org/saml/consume
Sign on URL: https://github.com/login
```

**Okta 集成**：
```yaml
# Okta 应用配置
Single sign-on URL: https://github.com/orgs/your-org/saml/consume
Audience URI: https://github.com/orgs/your-org
Name ID format: emailAddress
Application username: Email
```

## 审计日志

审计日志记录了组织中的所有重要操作，用于安全监控和合规性检查。

### 访问审计日志

**使用 GitHub CLI**：
```bash
# 获取所有审计日志
gh api orgs/{org}/audit-log

# 按事件类型过滤
gh api orgs/{org}/audit-log \
  --method GET \
  -f phrase='action:repo.create' \
  -f created='>=2024-01-01'

# 按用户过滤
gh api orgs/{org}/audit-log \
  --method GET \
  -f phrase='actor:username'

# 按仓库过滤
gh api orgs/{org}/audit-log \
  --method GET \
  -f phrase='repo:org/repo'
```

**使用 API**：
```bash
# 使用 curl
curl -H "Authorization: Bearer $TOKEN" \
  "https://api.github.com/orgs/{org}/audit-log?phrase=action:repo.create"

# 使用分页
curl -H "Authorization: Bearer $TOKEN" \
  "https://api.github.com/orgs/{org}/audit-log?per_page=100&page=1"
```

**使用 Web 界面**：
1. 访问组织设置
2. 点击 **Audit log**
3. 使用过滤器搜索事件

### 审计日志事件类型

**仓库事件**：
| 事件 | 说明 |
|------|------|
| `repo.create` | 创建仓库 |
| `repo.destroy` | 删除仓库 |
| `repo.rename` | 重命名仓库 |
| `repo.transfer` | 转移仓库所有权 |
| `repo.visibility_change` | 更改仓库可见性 |
| `repo.archived` | 归档仓库 |
| `repo.unarchived` | 取消归档仓库 |

**成员事件**：
| 事件 | 说明 |
|------|------|
| `org.invite_member` | 邀请成员 |
| `org.remove_member` | 移除成员 |
| `member.role_change` | 更改成员角色 |
| `member.invite` | 邀请成员 |
| `member.remove` | 移除成员 |

**团队事件**：
| 事件 | 说明 |
|------|------|
| `team.create` | 创建团队 |
| `team.destroy` | 删除团队 |
| `team.rename` | 重命名团队 |
| `team.member_add` | 添加团队成员 |
| `team.member_remove` | 移除团队成员 |

**安全事件**：
| 事件 | 说明 |
|------|------|
| `protected_branch.create` | 创建保护分支 |
| `protected_branch.destroy` | 删除保护分支 |
| `protected_branch.update_config` | 更新保护分支配置 |
| `secret_scanning_alert.create` | 创建密钥扫描警报 |
| `dependabot_alert.create` | 创建 Dependabot 警报 |

### 导出审计日志

**使用 GitHub Actions 自动导出**：
```yaml
# .github/workflows/audit-export.yml
name: Export Audit Log

on:
  schedule:
    - cron: '0 0 * * *'  # 每天执行
  workflow_dispatch:  # 手动触发

jobs:
  export:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Export audit log
      env:
        GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        # 导出当天的审计日志
        gh api orgs/{org}/audit-log \
          --method GET \
          -f phrase='created:>=2024-01-01' \
          -q '.[] | @json' > audit-log-$(date +%Y%m%d).json
        
        # 压缩文件
        gzip audit-log-$(date +%Y%m%d).json

    - name: Upload artifact
      uses: actions/upload-artifact@v4
      with:
        name: audit-log
        path: audit-log-*.json.gz
```

**使用 Python 脚本导出**：
```python
#!/usr/bin/env python3
import requests
import json
import gzip
from datetime import datetime, timedelta

# 配置
ORG = "your-org"
TOKEN = "your-token"
HEADERS = {
    "Authorization": f"Bearer {TOKEN}",
    "Accept": "application/vnd.github.v3+json"
}

def export_audit_log(start_date, end_date):
    """导出审计日志"""
    url = f"https://api.github.com/orgs/{ORG}/audit-log"
    params = {
        "phrase": f"created:{start_date}..{end_date}",
        "per_page": 100
    }
    
    all_events = []
    page = 1
    
    while True:
        params["page"] = page
        response = requests.get(url, headers=HEADERS, params=params)
        events = response.json()
        
        if not events:
            break
            
        all_events.extend(events)
        page += 1
    
    # 保存到文件
    filename = f"audit-log-{start_date}-{end_date}.json.gz"
    with gzip.open(filename, 'wt', encoding='utf-8') as f:
        json.dump(all_events, f, indent=2)
    
    print(f"导出了 {len(all_events)} 个事件到 {filename}")
    return all_events

if __name__ == "__main__":
    # 导出最近30天的日志
    end_date = datetime.now().strftime("%Y-%m-%d")
    start_date = (datetime.now() - timedelta(days=30)).strftime("%Y-%m-%d")
    export_audit_log(start_date, end_date)
```

### 审计日志分析

**使用 jq 分析**：
```bash
# 统计事件类型
gh api orgs/{org}/audit-log --jq '.[] | .action' | sort | uniq -c | sort -rn

# 统计活跃用户
gh api orgs/{org}/audit-log --jq '.[] | .actor.login' | sort | uniq -c | sort -rn

# 查找失败事件
gh api orgs/{org}/audit-log --jq '.[] | select(.status == "failed")'

# 导出为 CSV
gh api orgs/{org}/audit-log --jq '.[] | [.created_at, .action, .actor.login, .repo.name] | @csv'
```

**使用 Python 分析**：
```python
import json
import pandas as pd
from collections import Counter

def analyze_audit_log(filename):
    """分析审计日志"""
    with open(filename, 'r') as f:
        events = json.load(f)
    
    # 转换为 DataFrame
    df = pd.DataFrame(events)
    
    # 统计事件类型
    event_counts = df['action'].value_counts()
    print("事件类型统计:")
    print(event_counts.head(10))
    
    # 统计活跃用户
    user_counts = df['actor'].apply(lambda x: x['login']).value_counts()
    print("\n活跃用户统计:")
    print(user_counts.head(10))
    
    # 统计时间分布
    df['created_at'] = pd.to_datetime(df['created_at'])
    daily_counts = df.groupby(df['created_at'].dt.date).size()
    print("\n每日事件统计:")
    print(daily_counts.tail(7))
    
    return df
```

### 审计日志最佳实践

**存储策略**：
- 保留至少 180 天的审计日志
- 定期归档旧日志
- 使用压缩存储节省空间
- 建立日志索引便于查询

**监控策略**：
- 设置关键事件警报
- 监控异常活动模式
- 定期审查安全事件
- 建立事件响应流程

**合规要求**：
- 满足 GDPR 要求
- 满足 SOC 2 要求
- 满足 HIPAA 要求（如适用）
- 定期进行合规性审计

## IP 允许列表

IP 允许列表可以限制只有特定 IP 地址才能访问组织资源。

### 配置 IP 允许列表

**手动配置步骤**：
1. **Settings** → **Authentication security** → **IP allow list**
2. 添加 IP 地址或 CIDR 范围
3. 启用 **Enable IP allow list**

**支持的 IP 格式**：
- 单个 IP 地址：`192.168.1.1`
- CIDR 范围：`192.168.1.0/24`
- IPv6 地址：`2001:db8::1`
- IPv6 范围：`2001:db8::/32`

**常见配置示例**：
```
# 办公网络
192.168.1.0/24
10.0.0.0/8

# VPN 网络
172.16.0.0/12

# 云服务 IP
52.167.144.0/20  # Azure
35.180.0.0/16    # AWS
```

### API 配置

**使用 GitHub CLI**：
```bash
# 启用 IP 允许列表
gh api orgs/{org}/actions/allowed-actions \
  --method PUT \
  -f enabled_all=true

# 只允许已验证的 Action
gh api orgs/{org}/actions/allowed-actions \
  --method PUT \
  -f enabled_verified_only=true

# 添加 IP 到允许列表
gh api orgs/{org}/ip-allowlist \
  --method POST \
  -f ip="192.168.1.0/24" \
  -f name="Office Network"

# 删除 IP 从允许列表
gh api orgs/{org}/ip-allowlist/{id} \
  --method DELETE
```

**使用 API 批量管理**：
```python
import requests

def manage_ip_allowlist(org, token, action, ips):
    """管理 IP 允许列表"""
    headers = {
        "Authorization": f"Bearer {token}",
        "Accept": "application/vnd.github.v3+json"
    }
    
    for ip in ips:
        if action == "add":
            url = f"https://api.github.com/orgs/{org}/ip-allowlist"
            data = {"ip": ip, "name": f"Auto-added {ip}"}
            response = requests.post(url, headers=headers, json=data)
        elif action == "remove":
            # 先获取 ID
            url = f"https://api.github.com/orgs/{org}/ip-allowlist"
            response = requests.get(url, headers=headers)
            for item in response.json():
                if item["ip"] == ip:
                    delete_url = f"{url}/{item['id']}"
                    requests.delete(delete_url, headers=headers)
        
        print(f"{action}: {ip} - {response.status_code}")

# 使用示例
ips = ["192.168.1.0/24", "10.0.0.0/8"]
manage_ip_allowlist("your-org", "your-token", "add", ips)
```

### IP 允许列表最佳实践

**安全建议**：
- 只允许必要的 IP 范围
- 定期审查和更新 IP 列表
- 使用 VPN 集中管理访问
- 监控异常访问尝试

**组织网络架构**：
```
┌─────────────────────────────────────────┐
│           企业网络架构                     │
├─────────────────────────────────────────┤
│  办公网络 (192.168.1.0/24)               │
│    ├── 开发团队                          │
│    ├── 测试团队                          │
│    └── 管理团队                          │
├─────────────────────────────────────────┤
│  VPN 网络 (172.16.0.0/12)                │
│    ├── 远程办公                          │
│    └── 外部合作伙伴                      │
├─────────────────────────────────────────┤
│  云服务网络                              │
│    ├── AWS (52.167.144.0/20)             │
│    ├── Azure (35.180.0.0/16)            │
│    └── GCP (35.190.0.0/16)             │
└─────────────────────────────────────────┘
```

## 合规和治理

合规和治理功能帮助企业满足法规要求和内部政策。

### 配置分支保护

**组织级分支保护**：
```yaml
# 使用 Rulesets（推荐）
gh api orgs/{org}/rulesets \
  --method POST \
  -f name='Production Branch Protection' \
  -f target='branch' \
  -f enforcement='active' \
  -f conditions='{"ref_name":{"include":["refs/heads/main"],"exclude":[]}}' \
  -f rules='[
    {"type":"pull_request","parameters":{"required_approving_review_count":2}},
    {"type":"required_status_checks","parameters":{"required_status_checks":[{"context":"ci/test"}]}},
    {"type":"non_fast_forward"}
  ]'
```

**仓库级分支保护**：
```yaml
# 使用 Branch Protection Rules
gh api repos/{owner}/{repo}/branches/main/protection \
  --method PUT \
  -f required_status_checks='{"strict":true,"contexts":["ci/test"]}' \
  -f enforce_admins=true \
  -f required_pull_request_reviews='{"required_approving_review_count":2,"dismiss_stale_reviews":true}' \
  -f restrictions='{"users":["admin-user"],"teams":["core-team"]}'
```

### 代码所有者

**CODEOWNERS 文件配置**：
```yaml
# .github/CODEOWNERS

# 默认所有者
* @your-org/core-team

# 文档团队
/docs/ @your-org/docs-team
*.md @your-org/docs-team

# 安全团队
/src/security/ @your-org/security-team
/security/ @your-org/security-team

# DevOps 团队
/.github/ @your-org/devops-team
Dockerfile @your-org/devops-team
docker-compose.yml @your-org/devops-team
*.yml @your-org/devops-team

# 前端团队
/src/frontend/ @your-org/frontend-team
*.js @your-org/frontend-team
*.ts @your-org/frontend-team
*.jsx @your-org/frontend-team
*.tsx @your-org/frontend-team

# 后端团队
/src/backend/ @your-org/backend-team
*.py @your-org/backend-team
*.java @your-org/backend-team
```

### 合规性检查

**自动合规性检查**：
```yaml
# .github/workflows/compliance-check.yml
name: Compliance Check

on:
  pull_request:
    branches: [main]

jobs:
  compliance:
    runs-on: ubuntu-latest
    steps:
    - name: Check branch protection
      run: |
        # 检查分支保护规则
        gh api repos/{owner}/{repo}/branches/main/protection
        
    - name: Check CODEOWNERS
      run: |
        # 检查 CODEOWNERS 文件
        if [ ! -f .github/CODEOWNERS ]; then
          echo "ERROR: CODEOWNERS file missing"
          exit 1
        fi
        
    - name: Check security policy
      run: |
        # 检查安全策略
        if [ ! -f SECURITY.md ]; then
          echo "WARNING: SECURITY.md file missing"
        fi
```

### 治理策略

**组织治理框架**：
```
┌─────────────────────────────────────────┐
│           组织治理框架                     │
├─────────────────────────────────────────┤
│  策略层                                  │
│    ├── 安全策略                          │
│    ├── 访问控制策略                      │
│    ├── 数据保护策略                      │
│    └── 合规性策略                        │
├─────────────────────────────────────────┤
│  执行层                                  │
│    ├── 权限管理                          │
│    ├── 分支保护                          │
│    ├── 代码审查                          │
│    └── 安全扫描                          │
├─────────────────────────────────────────┤
│  监控层                                  │
│    ├── 审计日志                          │
│    ├── 安全警报                          │
│    ├── 合规性报告                        │
│    └── 性能监控                          │
└─────────────────────────────────────────┘
```

**治理最佳实践**：
- 建立清晰的治理策略
- 自动化合规性检查
- 定期审查和更新策略
- 培训团队成员
- 建立事件响应流程

## 安全功能

### Secret Scanning

**启用 Secret Scanning**：
```bash
# 启用 Push Protection
gh api repos/{org}/{repo}/secret-scanning/push-protection \
  --method PUT \
  -f status='enabled'

# 启用 Secret Scanning
gh api repos/{org}/{repo}/secret-scanning \
  --method PUT \
  -f status='enabled'
```

**自定义 Secret Scanning 模式**：
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

### 依赖图

**启用依赖图**：
```bash
# 启用依赖图
gh api repos/{org}/{repo}/vulnerability-alerts \
  --method PUT \
  -f enabled='true'

# 启用自动安全更新
gh api repos/{org}/{repo}/automated-security-fixes \
  --method PUT \
  -f enabled='true'
```

### 代码扫描

**配置 CodeQL**：
```yaml
# .github/workflows/codeql.yml
name: "CodeQL"

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 0 * * 1'  # 每周一执行

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
        queries: security-extended

    - name: Autobuild
      uses: github/codeql-action/autobuild@v3

    - name: Perform CodeQL Analysis
      uses: github/codeql-action/analyze@v3
      with:
        category: "/language:${{matrix.language}}"
```

## 管理 API

### 组织管理

**获取组织信息**：
```bash
# 获取组织详细信息
gh api orgs/{org}

# 获取组织设置
gh api orgs/{org}/settings/billing

# 获取组织成员
gh api orgs/{org}/members

# 获取组织团队
gh api orgs/{org}/teams

# 获取组织仓库
gh api orgs/{org}/repos --paginate
```

**管理组织成员**：
```bash
# 邀请成员
gh api orgs/{org}/invitations \
  --method POST \
  -f email='user@example.com' \
  -f role='direct_member'

# 移除成员
gh api orgs/{org}/members/{username} \
  --method DELETE

# 更改成员角色
gh api orgs/{org}/memberships/{username} \
  --method PUT \
  -f role='admin'
```

### 仓库管理

**获取仓库信息**：
```bash
# 列出仓库
gh api orgs/{org}/repos --paginate

# 获取仓库详细信息
gh api repos/{owner}/{repo}

# 获取仓库安全功能
gh api repos/{owner}/{repo}/vulnerability-alerts

# 获取仓库密钥
gh api repos/{owner}/{repo}/actions/secrets

# 获取仓库变量
gh api repos/{owner}/{repo}/actions/variables
```

**管理仓库设置**：
```bash
# 更新仓库设置
gh api repos/{owner}/{repo} \
  --method PATCH \
  -f has_issues=true \
  -f has_projects=true \
  -f has_wiki=true

# 设置仓库可见性
gh api repos/{owner}/{repo} \
  --method PATCH \
  -f visibility='private'

# 归档仓库
gh api repos/{owner}/{repo} \
  --method PATCH \
  -f archived=true
```

### 团队管理

**管理团队**：
```bash
# 创建团队
gh api orgs/{org}/teams \
  --method POST \
  -f name='new-team' \
  -f description='New team description' \
  -f privacy='closed'

# 删除团队
gh api orgs/{org}/teams/{team_slug} \
  --method DELETE

# 添加团队成员
gh api orgs/{org}/teams/{team_slug}/memberships/{username} \
  --method PUT \
  -f role='member'

# 获取团队仓库
gh api orgs/{org}/teams/{team_slug}/repos
```

## 企业级部署

### 部署选项

**GitHub Enterprise Cloud**：
- 托管在 GitHub 云上
- 无需维护基础设施
- 自动扩展和更新
- 适合大多数企业

**GitHub Enterprise Server**：
- 自托管部署
- 完全控制基础设施
- 需要自行维护
- 适合有严格合规要求的企业

**GitHub AE（GitHub Enterprise 的 Azure 版本）：
- 托管在 Azure 上
- 与 Azure 服务集成
- 适合 Azure 用户

### 部署架构

**企业级部署架构**：
```
┌─────────────────────────────────────────┐
│           企业级部署架构                   │
├─────────────────────────────────────────┤
│  用户层                                  │
│    ├── 开发人员                          │
│    ├── 测试人员                          │
│    ├── 运维人员                          │
│    └── 管理人员                          │
├─────────────────────────────────────────┤
│  访问层                                  │
│    ├── SSO/SAML                         │
│    ├── IP 允许列表                      │
│    ├── VPN                              │
│    └── 防火墙                           │
├─────────────────────────────────────────┤
│  应用层                                  │
│    ├── GitHub Enterprise                │
│    ├── GitHub Actions                   │
│    ├── GitHub Packages                  │
│    └── GitHub Pages                     │
├─────────────────────────────────────────┤
│  数据层                                  │
│    ├── Git 仓库                         │
│    ├── 数据库                           │
│    ├── 缓存                             │
│    └── 存储                             │
├─────────────────────────────────────────┤
│  基础设施层                              │
│    ├── 计算资源                         │
│    ├── 网络资源                         │
│    ├── 存储资源                         │
│    └── 安全资源                         │
└─────────────────────────────────────────┘
```

### 部署最佳实践

**高可用性**：
- 部署多个实例
- 使用负载均衡
- 配置故障转移
- 定期备份数据

**性能优化**：
- 优化网络配置
- 使用 CDN
- 配置缓存
- 监控性能指标

**安全加固**：
- 启用所有安全功能
- 配置网络隔离
- 定期安全审计
- 建立安全监控

## 成本优化

### 成本分析

**主要成本项**：
- 用户许可证费用
- Actions 分钟数费用
- 存储费用
- 带宽费用
- 支持费用

**成本优化策略**：
1. **用户管理**：
   - 定期审查用户权限
   - 移除不活跃用户
   - 使用团队管理权限
   - 优化许可证分配

2. **Actions 优化**：
   - 使用缓存减少构建时间
   - 优化工作流配置
   - 使用自托管运行器
   - 监控使用情况

3. **存储优化**：
   - 定期清理旧数据
   - 使用 Git LFS 管理大文件
   - 压缩构建产物
   - 监控存储使用

### 成本监控

**使用 GitHub API 监控成本**：
```bash
# 获取计费信息
gh api orgs/{org}/settings/billing

# 获取 Actions 使用情况
gh api orgs/{org}/settings/billing/actions

# 获取 Packages 使用情况
gh api orgs/{org}/settings/billing/packages

# 获取存储使用情况
gh api orgs/{org}/settings/billing/storage
```

**自动化成本报告**：
```yaml
# .github/workflows/cost-report.yml
name: Cost Report

on:
  schedule:
    - cron: '0 0 1 * *'  # 每月1日执行

jobs:
  report:
    runs-on: ubuntu-latest
    steps:
    - name: Generate cost report
      env:
        GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        # 获取计费信息
        gh api orgs/{org}/settings/billing > billing.json
        
        # 生成报告
        echo "成本报告 - $(date +%Y-%m)" > cost-report.md
        echo "=========================" >> cost-report.md
        echo "" >> cost-report.md
        
        # 解析 JSON 并生成报告
        jq -r '.actions_minutes_used_used // 0' billing.json >> cost-report.md
        
        # 发送报告
        gh api repos/{owner}/{repo}/issues \
          --method POST \
          -f title="成本报告 - $(date +%Y-%m)" \
          -f body="$(cat cost-report.md)"
```

## 最佳实践

1. **启用 SSO**：统一身份认证，提高安全性
2. **配置审计日志**：跟踪所有操作，满足合规要求
3. **设置 IP 允许列表**：限制访问来源，增强安全性
4. **使用 CODEOWNERS**：明确代码责任，提高代码质量
5. **定期审查权限**：确保最小权限原则，降低安全风险
6. **优化成本**：定期审查使用情况，优化资源分配
7. **建立治理策略**：制定清晰的治理策略，确保合规性
8. **培训团队**：定期培训团队成员，提高安全意识

## 相关资源

- [GitHub Enterprise 文档](https://docs.github.com/en/enterprise-cloud@latest)
- [SAML SSO 文档](https://docs.github.com/en/enterprise-cloud@latest/organizations/managing-saml-single-sign-on-for-your-organization)
- [审计日志文档](https://docs.github.com/en/enterprise-cloud@latest/admin/monitoring-activity-in-your-enterprise/reviewing-the-audit-log-for-your-enterprise)
- [IP 允许列表文档](https://docs.github.com/en/enterprise-cloud@latest/organizations/keeping-your-organization-secure/managing-allowed-ip-addresses-for-your-organization)
- [成本优化指南](https://docs.github.com/en/enterprise-cloud@latest/billing/managing-billing-for-your-github-account/about-billing-for-github)

---

**上一篇：[DevOps 实战](W15-devops.md) | 下一篇：[Docker + GitHub Actions 容器化](W17-docker-actions.md)**
