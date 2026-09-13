# 练习 33：GitHub API 集成

## 目标

学习如何使用 GitHub API 来自动化和扩展 GitHub 功能。

## 前置条件

- 有 GitHub 账号
- 熟悉 REST API 基础
- 有基本的编程能力

## 步骤

### 1. 理解 GitHub API

GitHub API 是一个 RESTful API，允许你以编程方式访问 GitHub 的功能。

**API 版本**：
- REST API v3：传统 API，功能全面
- GraphQL API v4：更灵活，可以一次请求获取多个资源

**认证方式**：
- Personal Access Token（PAT）
- GitHub App
- OAuth App

### 2. 创建 Personal Access Token

**创建步骤**：

1. 访问 [GitHub Settings](https://github.com/settings/tokens)
2. 点击 "Generate new token"
3. 选择 token 类型
4. 选择权限范围
5. 点击 "Generate token"

**权限范围**：
- `repo`：仓库访问
- `admin:org`：组织管理
- `admin:repo_hook`：Webhook 管理
- `user`：用户信息
- `workflow`：GitHub Actions

### 3. 使用 REST API

**示例：获取用户信息**：

```bash
# 使用 curl
curl -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.github.com/users/octocat

# 使用 Python
import requests

headers = {
    'Authorization': f'Bearer {YOUR_TOKEN}',
    'Accept': 'application/vnd.github.v3+json'
}

response = requests.get('https://api.github.com/users/octocat', headers=headers)
print(response.json())
```

**示例：获取仓库列表**：

```bash
# 使用 curl
curl -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.github.com/user/repos

# 使用 Python
import requests

headers = {
    'Authorization': f'Bearer {YOUR_TOKEN}',
    'Accept': 'application/vnd.github.v3+json'
}

response = requests.get('https://api.github.com/user/repos', headers=headers)
for repo in response.json():
    print(repo['name'])
```

**示例：创建 Issue**：

```bash
# 使用 curl
curl -X POST \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/repos/owner/repo/issues \
  -d '{"title":"New Issue","body":"This is a new issue"}'

# 使用 Python
import requests

headers = {
    'Authorization': f'Bearer {YOUR_TOKEN}',
    'Accept': 'application/vnd.github.v3+json'
}

data = {
    'title': 'New Issue',
    'body': 'This is a new issue'
}

response = requests.post(
    'https://api.github.com/repos/owner/repo/issues',
    headers=headers,
    json=data
)
print(response.json())
```

### 4. 使用 GraphQL API

**示例：获取用户信息**：

```bash
# 使用 curl
curl -X POST \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/graphql \
  -d '{"query":"query { viewer { login name email } }"}'

# 使用 Python
import requests

headers = {
    'Authorization': f'Bearer {YOUR_TOKEN}',
    'Content-Type': 'application/json'
}

query = '''
query {
  viewer {
    login
    name
    email
  }
}
'''

response = requests.post(
    'https://api.github.com/graphql',
    headers=headers,
    json={'query': query}
)
print(response.json())
```

**示例：获取仓库信息**：

```bash
# 使用 Python
import requests

headers = {
    'Authorization': f'Bearer {YOUR_TOKEN}',
    'Content-Type': 'application/json'
}

query = '''
query {
  repository(owner: "octocat", name: "Hello-World") {
    name
    description
    stargazerCount
    forkCount
    issues(first: 5) {
      nodes {
        title
        body
      }
    }
  }
}
'''

response = requests.post(
    'https://api.github.com/graphql',
    headers=headers,
    json={'query': query}
)
print(response.json())
```

### 5. 使用 GitHub CLI

**示例：使用 GitHub CLI 调用 API**：

```bash
# 获取用户信息
gh api users/octocat

# 获取仓库列表
gh api user/repos

# 创建 Issue
gh api repos/owner/repo/issues -f title="New Issue" -f body="This is a new issue"

# 使用 GraphQL
gh api graphql -f query='query { viewer { login name email } }'
```

### 6. 使用 Webhook

**创建 Webhook**：

1. 访问仓库设置
2. 点击 "Webhooks"
3. 点击 "Add webhook"
4. 配置 Webhook

**Webhook 事件**：
- `push`：代码推送
- `pull_request`：Pull Request 事件
- `issues`：Issue 事件
- `release`：Release 事件

**示例：处理 Webhook**：

```python
from flask import Flask, request, jsonify
import hmac
import hashlib

app = Flask(__name__)

WEBHOOK_SECRET = 'your-webhook-secret'

@app.route('/webhook', methods=['POST'])
def webhook():
    # 验证签名
    signature = request.headers.get('X-Hub-Signature-256')
    if not verify_signature(request.data, signature):
        return 'Invalid signature', 403
    
    # 处理事件
    event = request.headers.get('X-GitHub-Event')
    payload = request.json()
    
    if event == 'push':
        handle_push(payload)
    elif event == 'pull_request':
        handle_pull_request(payload)
    elif event == 'issues':
        handle_issues(payload)
    
    return 'OK', 200

def verify_signature(payload, signature):
    expected = 'sha256=' + hmac.new(
        WEBHOOK_SECRET.encode(),
        payload,
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(expected, signature)

def handle_push(payload):
    print(f"Push to {payload['repository']['full_name']}")
    print(f"Commits: {len(payload['commits'])}")

def handle_pull_request(payload):
    action = payload['action']
    pr = payload['pull_request']
    print(f"PR {action}: {pr['title']}")

def handle_issues(payload):
    action = payload['action']
    issue = payload['issue']
    print(f"Issue {action}: {issue['title']}")

if __name__ == '__main__':
    app.run(port=5000)
```

### 7. 使用 GitHub App

**创建 GitHub App**：

1. 访问 [GitHub Developer Settings](https://github.com/settings/apps)
2. 点击 "New GitHub App"
3. 配置 App
4. 生成私钥

**示例：使用 GitHub App**：

```python
import jwt
import time
import requests

# 配置
APP_ID = 'your-app-id'
PRIVATE_KEY = open('private-key.pem', 'r').read()

# 生成 JWT
def generate_jwt():
    now = int(time.time())
    payload = {
        'iat': now - 60,
        'exp': now + 600,
        'iss': APP_ID
    }
    return jwt.encode(payload, PRIVATE_KEY, algorithm='RS256')

# 获取安装令牌
def get_installation_token(installation_id):
    jwt_token = generate_jwt()
    
    headers = {
        'Authorization': f'Bearer {jwt_token}',
        'Accept': 'application/vnd.github.v3+json'
    }
    
    response = requests.post(
        f'https://api.github.com/app/installations/{installation_id}/access_tokens',
        headers=headers
    )
    
    return response.json()['token']

# 使用安装令牌
def use_installation_token(token):
    headers = {
        'Authorization': f'Bearer {token}',
        'Accept': 'application/vnd.github.v3+json'
    }
    
    response = requests.get(
        'https://api.github.com/installation/repositories',
        headers=headers
    )
    
    return response.json()
```

### 8. 使用 OAuth

**OAuth 流程**：

1. 重定向用户到 GitHub 授权页面
2. 用户授权后，GitHub 重定向回你的应用
3. 你的应用获取访问令牌

**示例：OAuth 流程**：

```python
from flask import Flask, redirect, request, session
import requests

app = Flask(__name__)
app.secret_key = 'your-secret-key'

CLIENT_ID = 'your-client-id'
CLIENT_SECRET = 'your-client-secret'
REDIRECT_URI = 'http://localhost:5000/callback'

@app.route('/login')
def login():
    return redirect(
        f'https://github.com/login/oauth/authorize'
        f'?client_id={CLIENT_ID}'
        f'&redirect_uri={REDIRECT_URI}'
        f'&scope=repo,user'
    )

@app.route('/callback')
def callback():
    code = request.args.get('code')
    
    # 获取访问令牌
    response = requests.post(
        'https://github.com/login/oauth/access_token',
        json={
            'client_id': CLIENT_ID,
            'client_secret': CLIENT_SECRET,
            'code': code
        },
        headers={'Accept': 'application/json'}
    )
    
    token = response.json()['access_token']
    session['token'] = token
    
    return redirect('/profile')

@app.route('/profile')
def profile():
    token = session.get('token')
    if not token:
        return redirect('/login')
    
    headers = {
        'Authorization': f'Bearer {token}',
        'Accept': 'application/vnd.github.v3+json'
    }
    
    response = requests.get('https://api.github.com/user', headers=headers)
    user = response.json()
    
    return f"Hello, {user['login']}!"

if __name__ == '__main__':
    app.run(port=5000)
```

### 9. 使用 GitHub API 进行自动化

**示例：自动合并 Dependabot PR**：

```python
import requests

def auto_merge_dependabot_prs(token, owner, repo):
    headers = {
        'Authorization': f'Bearer {token}',
        'Accept': 'application/vnd.github.v3+json'
    }
    
    # 获取 PR 列表
    response = requests.get(
        f'https://api.github.com/repos/{owner}/{repo}/pulls',
        headers=headers
    )
    
    prs = response.json()
    
    for pr in prs:
        # 检查是否是 Dependabot PR
        if pr['user']['login'] == 'dependabot[bot]':
            # 检查是否通过 CI
            if check_ci_status(token, owner, repo, pr['number']):
                # 合并 PR
                merge_pr(token, owner, repo, pr['number'])

def check_ci_status(token, owner, repo, pr_number):
    headers = {
        'Authorization': f'Bearer {token}',
        'Accept': 'application/vnd.github.v3+json'
    }
    
    response = requests.get(
        f'https://api.github.com/repos/{owner}/{repo}/pulls/{pr_number}/reviews',
        headers=headers
    )
    
    reviews = response.json()
    return any(r['state'] == 'APPROVED' for r in reviews)

def merge_pr(token, owner, repo, pr_number):
    headers = {
        'Authorization': f'Bearer {token}',
        'Accept': 'application/vnd.github.v3+json'
    }
    
    response = requests.put(
        f'https://api.github.com/repos/{owner}/{repo}/pulls/{pr_number}/merge',
        headers=headers,
        json={'merge_method': 'squash'}
    )
    
    return response.json()
```

### 10. 使用 GitHub API 进行报告

**示例：生成贡献报告**：

```python
import requests
from datetime import datetime, timedelta

def generate_contribution_report(token, owner, repo, days=30):
    headers = {
        'Authorization': f'Bearer {token}',
        'Accept': 'application/vnd.github.v3+json'
    }
    
    # 计算日期范围
    end_date = datetime.now()
    start_date = end_date - timedelta(days=days)
    
    # 获取提交
    commits = get_commits(token, owner, repo, start_date, end_date)
    
    # 获取 PR
    prs = get_pull_requests(token, owner, repo, start_date, end_date)
    
    # 获取 Issues
    issues = get_issues(token, owner, repo, start_date, end_date)
    
    # 生成报告
    report = f"""
    # 贡献报告
    
    **时间范围**: {start_date.strftime('%Y-%m-%d')} 到 {end_date.strftime('%Y-%m-%d')}
    
    ## 统计
    
    - 提交数量: {len(commits)}
    - Pull Requests: {len(prs)}
    - Issues: {len(issues)}
    
    ## 活跃贡献者
    
    {get_active_contributors(commits, prs, issues)}
    """
    
    return report

def get_commits(token, owner, repo, start_date, end_date):
    headers = {
        'Authorization': f'Bearer {token}',
        'Accept': 'application/vnd.github.v3+json'
    }
    
    response = requests.get(
        f'https://api.github.com/repos/{owner}/{repo}/commits',
        headers=headers,
        params={
            'since': start_date.isoformat(),
            'until': end_date.isoformat(),
            'per_page': 100
        }
    )
    
    return response.json()

def get_pull_requests(token, owner, repo, start_date, end_date):
    headers = {
        'Authorization': f'Bearer {token}',
        'Accept': 'application/vnd.github.v3+json'
    }
    
    response = requests.get(
        f'https://api.github.com/repos/{owner}/{repo}/pulls',
        headers=headers,
        params={
            'state': 'all',
            'sort': 'created',
            'direction': 'desc',
            'per_page': 100
        }
    )
    
    return [pr for pr in response.json() 
            if start_date.isoformat() <= pr['created_at'] <= end_date.isoformat()]

def get_issues(token, owner, repo, start_date, end_date):
    headers = {
        'Authorization': f'Bearer {token}',
        'Accept': 'application/vnd.github.v3+json'
    }
    
    response = requests.get(
        f'https://api.github.com/repos/{owner}/{repo}/issues',
        headers=headers,
        params={
            'state': 'all',
            'sort': 'created',
            'direction': 'desc',
            'per_page': 100
        }
    )
    
    return [issue for issue in response.json() 
            if start_date.isoformat() <= issue['created_at'] <= end_date.isoformat()]

def get_active_contributors(commits, prs, issues):
    contributors = {}
    
    for commit in commits:
        author = commit['commit']['author']['name']
        contributors[author] = contributors.get(author, 0) + 1
    
    for pr in prs:
        author = pr['user']['login']
        contributors[author] = contributors.get(author, 0) + 1
    
    for issue in issues:
        author = issue['user']['login']
        contributors[author] = contributors.get(author, 0) + 1
    
    # 按贡献数量排序
    sorted_contributors = sorted(contributors.items(), key=lambda x: x[1], reverse=True)
    
    return '\n'.join([f"- {name}: {count} 次贡献" for name, count in sorted_contributors[:10]])
```

## 挑战

1. **挑战 1**：创建一个自动创建 Issue 的脚本
2. **挑战 2**：创建一个自动合并 Dependabot PR 的脚本
3. **挑战 3**：创建一个生成贡献报告的脚本
4. **挑战 4**：创建一个自动处理 Webhook 的服务器
5. **挑战 5**：创建一个 GitHub App 来自动化仓库管理

## 思考

1. GitHub API 的使用场景有哪些？
2. 如何保护 GitHub API 的访问令牌？
3. GitHub API 的速率限制如何处理？
4. 如何选择使用 REST API 还是 GraphQL API？

## 相关资源

- [GitHub REST API 文档](https://docs.github.com/en/rest)
- [GitHub GraphQL API 文档](https://docs.github.com/en/graphql)
- [GitHub CLI 文档](https://cli.github.com/)
- [GitHub Webhooks 文档](https://docs.github.com/en/webhooks)
- [GitHub Apps 文档](https://docs.github.com/en/apps)

---

**上一篇：[练习 32：GitHub Actions 矩阵策略](exercise-32-github-actions-matrix.md) | 下一篇：[练习 34：GitHub 安全最佳实践](exercise-34-github-security-best-practices.md)**