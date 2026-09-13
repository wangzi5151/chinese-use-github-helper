# GitHub API Usage Guide

## What is GitHub API?

GitHub API allows you to interact with GitHub programmatically, can be used to automate operations, get data, integrate into other systems.

## API Types

| Type | Description | Endpoint |
|------|-------------|----------|
| REST API | Traditional RESTful API | `https://api.github.com` |
| GraphQL API | Flexible query language | `https://api.github.com/graphql` |

## Authentication Methods

### 1. Personal Access Token (PAT)

```bash
# Using curl
curl -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/user

# Using gh command
gh api user
```

### 2. OAuth

```javascript
// OAuth authentication flow
// 1. Redirect to GitHub authorization page
// 2. User authorizes
// 3. Get access token
// 4. Use token to call API
```

### 3. GitHub App

```javascript
// Using GitHub App authentication
// 1. Generate JWT
// 2. Get installation token
// 3. Use token to call API
```

## REST API Examples

### Get User Information

```bash
# Using curl
curl https://api.github.com/users/octocat

# Using gh
gh api users/octocat
```

### Get Repository Information

```bash
# Get repository
curl https://api.github.com/repos/octocat/Hello-World

# Using gh
gh api repos/octocat/Hello-World
```

### Create Issue

```bash
curl -X POST \
  -H "Authorization: token YOUR_TOKEN" \
  -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/repos/OWNER/REPO/issues \
  -d '{"title":"Bug report","body":"Description of the bug"}'
```

### Using gh Command

```bash
# Create Issue
gh api repos/OWNER/REPO/issues \
  -f title='Bug report' \
  -f body='Description of the bug'

# Create PR
gh api repos/OWNER/REPO/pulls \
  -f title='New feature' \
  -f head='feature-branch' \
  -f base='main'
```

## GraphQL API Examples

### Query Repository Information

```graphql
query {
  repository(owner: "octocat", name: "Hello-World") {
    name
    description
    stargazerCount
    issues(first: 5) {
      edges {
        node {
          title
          state
        }
      }
    }
  }
}
```

### Using gh Command

```bash
gh api graphql -f query='
{
  repository(owner: "octocat", name: "Hello-World") {
    name
    description
    stargazerCount
  }
}'
```

## Common API Endpoints

### User Related

| Endpoint | Description |
|----------|-------------|
| `GET /user` | Get current user |
| `GET /users/{username}` | Get user information |
| `GET /user/repos` | Get user repositories |

### Repository Related

| Endpoint | Description |
|----------|-------------|
| `GET /repos/{owner}/{repo}` | Get repository information |
| `POST /repos/{owner}/{repo}/issues` | Create Issue |
| `GET /repos/{owner}/{repo}/pulls` | Get PR list |
| `POST /repos/{owner}/{repo}/pulls` | Create PR |

### Organization Related

| Endpoint | Description |
|----------|-------------|
| `GET /orgs/{org}` | Get organization information |
| `GET /orgs/{org}/repos` | Get organization repositories |
| `GET /orgs/{org}/members` | Get organization members |

## Rate Limits

### Authenticated Requests

- **5,000 requests per hour**

### Unauthenticated Requests

- **60 requests per hour**

### Check Rate Limits

```bash
# Using curl
curl -I https://api.github.com/users/octocat | grep -i 'x-ratelimit'

# Using gh
gh api rate_limit
```

## Error Handling

### Common Error Codes

| Error Code | Description |
|------------|-------------|
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 422 | Unprocessable Entity |
| 403 | Rate Limited |

### Error Response Example

```json
{
  "message": "Not Found",
  "documentation_url": "https://docs.github.com/rest"
}
```

## Usage Scenarios

### 1. Automated Workflows

```yaml
# Using API in GitHub Actions
- name: Create Issue
  run: |
    gh api repos/${{ github.repository }}/issues \
      -f title='Build failed' \
      -f body='Build ${{ github.run_id }} failed'
```

### 2. Data Analysis

```python
import requests

# Get repository statistics
response = requests.get(
    'https://api.github.com/repos/octocat/Hello-World',
    headers={'Authorization': 'token YOUR_TOKEN'}
)

data = response.json()
print(f"Stars: {data['stargazers_count']}")
```

### 3. Integrate into Other Systems

```javascript
// Integrate into Slack
const { WebClient } = require('@slack/web-api');
const github = require('@octokit/rest');

// Notify Slack when new PR is created
```

## Best Practices

1. **Use Authenticated Requests**: Get higher rate limits
2. **Cache Responses**: Reduce API calls
3. **Handle Rate Limits**: Implement backoff retry
4. **Use Pagination**: Use pagination when getting large amounts of data
5. **Validate Input**: Ensure request data is valid
6. **Error Handling**: Properly handle API errors

## Related Resources

- [GitHub REST API Documentation](https://docs.github.com/en/rest)
- [GitHub GraphQL API Documentation](https://docs.github.com/en/graphql)