# GitHub Code Search Advanced Search

## Search Syntax

### Basic Search

```
# Search keywords
hello world

# Search specific language
language:javascript

# Search specific path
path:src/components

# Search specific file
filename:package.json

# Combined search
language:typescript path:src/api
```

### Advanced Filtering

```
# Search function definition
def calculate_total

# Search class definition
class User

# Search import statements
import.*from.*react

# Search specific patterns
TODO:.*fix
FIXME:.*
HACK:.*
```

### Regular Expressions

```
# Use regular expressions
/\b\d{3}-\d{4}\b/  # Match phone number format

# Use OR
error OR warning

# Use AND
function AND async

# Use NOT
TODO NOT test
```

## Search Scope

### Personal Search

```bash
# Search my repositories
yourusername search-query

# Search specific repository
owner:yourusername repo:your-repo search-query
```

### Organization Search

```bash
# Search organization repositories
org:your-org search-query

# Search specific repository in organization
org:your-org repo:specific-repo search-query
```

### Global Search

```bash
# Search all public repositories
is:public search-query

# Search all repositories of specific language
is:public language:python search-query
```

## Using GitHub CLI Search

```bash
# Search repositories
gh search repos "machine learning" --language python --stars ">1000"

# Search code
gh search code "function authenticate" --repo owner/repo

# Search issues
gh search issues "bug label:urgent" --state open

# Search users
gh search users "location:beijing" --type user
```

## Code Search API

### REST API

```bash
# Search code
curl -H "Authorization: token $GITHUB_TOKEN" \
  "https://api.github.com/search/code?q=language:python+repo:owner/repo+filename:main.py"

# Search repositories
curl -H "Authorization: token $GITHUB_TOKEN" \
  "https://api.github.com/search/repositories?q=machine+learning+language:python+stars:>1000"

# Search issues
curl -H "Authorization: token $GITHUB_TOKEN" \
  "https://api.github.com/search/issues?q=repo:owner/repo+is:issue+label:bug"
```

### GraphQL API

```graphql
query {
  search(query: "language:python stars:>1000", type: REPOSITORY, first: 10) {
    edges {
      node {
        ... on Repository {
          name
          description
          url
          stargazerCount
          primaryLanguage {
            name
          }
        }
      }
    }
  }
}
```

## Search Tips

### 1. Exact Search

```
# Search exact phrase
"exact phrase"

# Search specific file type
extension:py

# Search specific directory
path:src/components
```

### 2. Combined Conditions

```
# AND condition
language:typescript AND path:src

# OR condition
language:javascript OR language:typescript

# NOT condition
language:python NOT test
```

### 3. Using Wildcards

```
# Wildcard search
test*.js
*.config.*
```

## Search Automation

### GitHub Action Auto Search

```yaml
# .github/workflows/search.yml
name: Code Search

on:
  schedule:
    - cron: '0 0 * * *'

jobs:
  search:
    runs-on: ubuntu-latest
    steps:
    - name: Search for security issues
      run: |
        gh search code "password" --repo owner/repo --json path
        gh search code "secret" --repo owner/repo --json path
        gh search code "api_key" --repo owner/repo --json path
```

### Using Octokit Search

```javascript
const { Octokit } = require("@octokit/rest");

const octokit = new Octokit({ auth: process.env.GITHUB_TOKEN });

async function searchCode(query) {
  const results = await octokit.rest.search.code({
    q: query,
    per_page: 100,
  });
  
  return results.data.items;
}

// Search all code containing TODO
const todos = await searchCode("TODO repo:owner/repo");
```

## Best Practices

1. **Use Exact Search**: Avoid too many search results
2. **Combine Multiple Conditions**: Narrow search scope
3. **Use Regular Expressions**: Handle complex patterns
4. **Save Common Searches**: Improve efficiency
5. **Use API Automation**: Batch process search results

## Related Resources

- [GitHub Code Search Documentation](https://docs.github.com/en/search-github/github-code-search)
- [Search Syntax Documentation](https://docs.github.com/en/search-github/github-code-search/understanding-github-code-search-syntax)
- [Search API Documentation](https://docs.github.com/en/rest/search)