# GitHub Marketplace

## What is GitHub Marketplace?

GitHub Marketplace is a platform that lets you discover and use various tools to enhance GitHub's functionality.

## Categories

| Category | Description | Examples |
|----------|-------------|----------|
| **Actions** | Automated workflows | CI/CD, deployment, code quality checks |
| **Apps** | GitHub applications | Project management, code review, security scanning |
| **Bots** | Automation bots | PR review, Issue management, notifications |
| **Skills** | Interactive learning | Git tutorials, GitHub usage guides |

## Discover Tools

### Browse by Category

1. Visit [github.com/marketplace](https://github.com/marketplace)
2. Select category of interest
3. Browse tool list

### Search by Function

```
Search keywords:
- CI/CD
- code review
- security
- project management
- documentation
```

## Install and Use

### Install GitHub App

1. Find app in Marketplace
2. Click **Install**
3. Select repository
4. Grant permissions

### Use GitHub Action

```yaml
# Use in workflow
- uses: actions/checkout@v4
- uses: some-action@version
  with:
    parameter: value
```

## Recommended Tools

### CI/CD

| Tool | Description |
|------|-------------|
| GitHub Actions | GitHub official CI/CD |
| CircleCI | Continuous integration service |
| Buildkite | CI/CD platform |

### Code Quality

| Tool | Description |
|------|-------------|
| SonarCloud | Code quality scanning |
| CodeClimate | Code quality analysis |
| Codacy | Automated code review |

### Project Management

| Tool | Description |
|------|-------------|
| ZenHub | Project management |
| Jira | Project tracking |
| Linear | Modern project management |

### Security

| Tool | Description |
|------|-------------|
| Snyk | Security scanning |
| Dependabot | Dependency updates |
| SonarCloud | Security vulnerability scanning |

### Documentation

| Tool | Description |
|------|-------------|
| Read the Docs | Documentation hosting |
| Mintlify | Documentation generation |
| Docusaurus | Documentation site |

## Create Your Own Tool

### Create GitHub Action

1. Create a new repository
2. Add `action.yml` file
3. Write Action code
4. Publish to Marketplace

### Create GitHub App

1. Go to **Settings** → **Developer settings** → **GitHub Apps**
2. Click **New GitHub App**
3. Configure permissions and events
4. Publish to Marketplace

## Best Practices

1. **Choose Known Tools**: Prioritize tools with high stars and active maintenance
2. **Check Permissions**: Review tool's required permissions before installation
3. **Read Documentation**: Understand tool's usage
4. **Regular Updates**: Keep tools at latest version
5. **Monitor Usage**: Review tool's usage statistics

## Related Resources

- [GitHub Marketplace Official Documentation](https://docs.github.com/en/marketplace)
- [Create GitHub Actions](https://docs.github.com/en/actions/creating-actions)
- [Create GitHub App](https://docs.github.com/en/apps/creating-github-apps)