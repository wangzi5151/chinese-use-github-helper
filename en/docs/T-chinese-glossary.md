# Chinese Git/GitHub Terminology Reference

## Basic Concepts

| English | Chinese | Description |
|---------|---------|-------------|
| Repository | 仓库 | Project's storage space |
| Clone | 克隆 | Copy remote repository to local |
| Commit | 提交 | Save changes to local repository |
| Branch | 分支 | Independent development line |
| Merge | 合并 | Combine changes from two branches |
| Stage | 暂存 | Mark changes as ready to commit |
| Remote | 远程 | Repository hosted on server |
| Working Directory | 工作区 | Directory where you're editing files |
| Index | 索引 | Another name for staging area |

## Git Operations

| English | Chinese | Description |
|---------|---------|-------------|
| Rebase | 变基 | Replay current branch commits onto target branch |
| Reset | 回退 | Undo commit or staging |
| Revert | 反转 | Create new commit to undo previous changes |
| Checkout | 检出 | Switch branch or restore file |
| Stash | 暂存 | Temporarily save uncommitted changes |
| Cherry-pick | 挑选 | Pick specific commit from another branch |
| Squash | 压缩 | Combine multiple commits into one |
| Amend | 修改 | Modify most recent commit |
| Diff | 差异 | View code changes |
| Log | 日志 | View commit history |
| Tag | 标签 | Mark specific commit |

## GitHub Features

| English | Chinese | Description |
|---------|---------|-------------|
| Pull Request (PR) | 拉取请求 | Request to merge code with review process |
| Issue | 问题 | Track bugs, feature requests, etc. |
| Fork | 分叉 | Copy someone's repository to your account |
| Code Review | 代码审查 | Review code quality and security |
| Actions | 动作 | GitHub's CI/CD automation platform |
| Pages | 页面 | Static website hosting service |
| Projects | 项目 | Kanban-style project management tool |
| Discussions | 讨论 | Community discussion forum |
| Packages | 包 | Package management service |
| Release | 发布 | Software version release |
| Marketplace | 市场 | Tools and services marketplace |
| Sponsors | 赞助 | Developer sponsorship platform |
| Copilot | 副驾驶 | AI programming assistant |

## Security Related

| English | Chinese | Description |
|---------|---------|-------------|
| SSH | 安全外壳 | Secure remote connection protocol |
| Key | 密钥 | Encrypted credential for authentication |
| Token | 令牌 | String used for API authentication |
| 2FA | 两步验证 | Two-Factor Authentication |
| Dependencies | 依赖项 | Other packages that project relies on |
| Vulnerability | 漏洞 | Security weakness |
| CODEOWNERS | 代码所有者 | File defining code review responsibilities |

## Workflow Terms

| English | Chinese | Description |
|---------|---------|-------------|
| Trunk-Based Development | 主干开发 | Everyone commits directly to main branch |
| Feature Flags | 功能开关 | Control feature enable/disable at runtime |
| CI | 持续集成 | Automated build and testing |
| CD | 持续部署 | Automated deployment to production |
| Pipeline | 管道 | CI/CD workflow |
| Workflow | 工作流 | GitHub Actions automation configuration |
| Runner | 运行器 | Server that executes GitHub Actions |

## Version Control

| English | Chinese | Description |
|---------|---------|-------------|
| Commit Message | 提交信息 | Text describing commit content |
| Hash | 哈希 | Commit's unique identifier |
| HEAD | 头指针 | Pointer to current branch's latest commit |
| History | 历史 | Ordered list of commits |
| Conflict | 冲突 | Differences that can't be auto-resolved during merge |
| Base | 基准 | Branch used for comparison or merge |
| Upstream | 上游 | Original repository (relative to Fork) |
| Downstream | 下游 | Forked repository |

## Common Abbreviations

| English | Chinese | Description |
|---------|---------|-------------|
| PR | 拉取请求 | Pull Request |
| MR | 合并请求 | Merge Request (GitLab usage) |
| CI | 持续集成 | Continuous Integration |
| CD | 持续部署 | Continuous Deployment |
| PT | PR 模板 | Pull Request Template |
| LGTM | 看起来不错 | Looks Good To Me |
| WIP | 进行中 | Work In Progress |
| TBD | 待定 | To Be Determined |
| N/A | 不适用 | Not Applicable |

## Git Command Reference

| English Command | Chinese Description | Example |
|-----------------|---------------------|---------|
| init | 初始化 | `git init` |
| clone | 克隆 | `git clone <url>` |
| add | 添加 | `git add <file>` |
| commit | 提交 | `git commit -m "msg"` |
| push | 推送 | `git push origin main` |
| pull | 拉取 | `git pull origin main` |
| fetch | 获取 | `git fetch origin` |
| branch | 分支 | `git branch <name>` |
| checkout | 检出 | `git checkout <branch>` |
| merge | 合并 | `git merge <branch>` |
| rebase | 变基 | `git rebase <branch>` |
| stash | 暂存 | `git stash` |
| log | 日志 | `git log --oneline` |
| diff | 差异 | `git diff` |
| status | 状态 | `git status` |
| remote | 远程 | `git remote -v` |
| tag | 标签 | `git tag <name>` |

## GitHub Operations Reference

| English | Chinese | Description |
|---------|---------|-------------|
| Create Repository | 创建仓库 | Create new code repository |
| Fork | 分叉 | Copy repository to your account |
| Star | 收藏 | Bookmark interesting projects |
| Watch | 关注 | Follow project activity |
| Clone | 克隆 | Copy repository to local |
| Pull Request | 拉取请求 | Request to merge code |
| Issue | 问题 | Report problems or request features |
| Release | 发布 | Release software version |
| Action | 动作 | Automated workflow |
| Page | 页面 | Static website hosting |
| Project | 项目 | Project management board |
| Discussion | 讨论 | Community discussion forum |

## Common Status Reference

| English | Chinese | Description |
|---------|---------|-------------|
| Open | 打开 | Issue/PR status |
| Closed | 已关闭 | Issue/PR status |
| Merged | 已合并 | PR status |
| Opened | 已打开 | PR status |
| Approved | 已批准 | Review status |
| Changes Requested | 请求修改 | Review status |
| Commented | 已评论 | Review status |

## Commit Type Reference

| English | Chinese | Description |
|---------|---------|-------------|
| feat | 新功能 | Feature |
| fix | 修复 | Bug Fix |
| docs | 文档 | Documentation |
| style | 格式 | Style |
| refactor | 重构 | Refactor |
| test | 测试 | Test |
| chore | 杂务 | Chore |
| perf | 性能 | Performance |
| ci | CI | Continuous Integration |
| build | 构建 | Build |