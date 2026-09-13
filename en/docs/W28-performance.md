# GitHub Performance Optimization

## Repository Optimization

### Clean History

```bash
# Garbage collection
git gc --aggressive --prune=now

# Clean untracked files
git clean -fd

# Compress history
git repack -a -d
```

### Reduce Repository Size

```bash
# View repository size
du -sh .git

# Clean large file history
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch large-file.zip' \
  --prune-empty --tag-name-filter cat -- --all

# Use BFG to clean
bfg --strip-blobs-bigger-than 100M
```

### Use .gitignore

```gitignore
# Editors
.vscode/
.idea/
*.swp

# Dependencies
node_modules/
vendor/

# Build artifacts
dist/
build/
*.log

# Environment variables
.env
.env.local

# System files
.DS_Store
Thumbs.db
```

## CI/CD Optimization

### Cache Strategy

```yaml
# .github/workflows/ci.yml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Cache Node.js
      uses: actions/cache@v4
      with:
        path: ~/.npm
        key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
        restore-keys: |
          ${{ runner.os }}-node-
    
    - name: Cache Docker layers
      uses: actions/cache@v4
      with:
        path: /tmp/.buildx-cache
        key: ${{ runner.os }}-buildx-${{ github.sha }}
        restore-keys: |
          ${{ runner.os }}-buildx-
```

### Parallel Execution

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - run: npm run lint
  
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - run: npm test
  
  build:
    needs: [lint, test]
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - run: npm run build
```

### Conditional Execution

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
    - uses: actions/checkout@v4
    - run: npm run deploy
```

## Clone Optimization

### Shallow Clone

```bash
# Shallow clone (latest 1 commit)
git clone --depth 1 https://github.com/user/repo.git

# Shallow clone specific branch
git clone --depth 1 --single-branch --branch main https://github.com/user/repo.git
```

### Partial Clone

```bash
# Blobless clone
git clone --filter=blob:none https://github.com/user/repo.git

# Treeless clone
git clone --filter=tree:0 https://github.com/user/repo.git
```

### Sparse Checkout

```bash
# Enable sparse checkout
git sparse-checkout init --cone

# Set directories to checkout
git sparse-checkout set src/docs src/utils

# View sparse checkout configuration
git sparse-checkout list
```

## GitHub Actions Performance

### Use Faster Runners

```yaml
jobs:
  build:
    runs-on: ubuntu-latest  # Faster
    # runs-on: ubuntu-22.04  # Specific version
```

### Use Matrix Strategy

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18, 20, 22]
      fail-fast: false
```

### Use Concurrency Control

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

## API Performance

### Pagination

```javascript
// Use pagination to get all data
async function getAllItems() {
  const items = [];
  let page = 1;
  
  while (true) {
    const response = await octokit.rest.issues.listForRepo({
      owner,
      repo,
      per_page: 100,
      page,
    });
    
    if (response.data.length === 0) break;
    items.push(...response.data);
    page++;
  }
  
  return items;
}
```

### Caching

```javascript
// Use cache to reduce API calls
const cache = new Map();

async function getCachedData(key, fetchFn) {
  if (cache.has(key)) {
    return cache.get(key);
  }
  
  const data = await fetchFn();
  cache.set(key, data);
  return data;
}
```

### GraphQL

```graphql
# Use GraphQL to reduce API calls
query {
  repository(owner: "your-org", name: "your-repo") {
    issues(first: 100) {
      edges {
        node {
          title
          state
          labels(first: 5) {
            edges {
              node {
                name
              }
            }
          }
        }
      }
    }
  }
}
```

## Monitoring and Optimization

### Performance Monitoring

```yaml
# .github/workflows/performance.yml
name: Performance Monitor

on:
  schedule:
    - cron: '0 * * * *'  # Every hour

jobs:
  monitor:
    runs-on: ubuntu-latest
    steps:
    - name: Check build time
      run: |
        # Record build time
        echo "Build time: ${{ github.run_duration }}"
```

### Optimization Suggestions

```markdown
## Optimization Checklist

### Repository Optimization
- [ ] Use .gitignore
- [ ] Clean large file history
- [ ] Use shallow clone

### CI/CD Optimization
- [ ] Use cache
- [ ] Parallel execution
- [ ] Conditional execution

### API Optimization
- [ ] Use pagination
- [ ] Use cache
- [ ] Use GraphQL
```

## Best Practices

1. **Regular Cleanup**: Regularly clean repository history
2. **Use Cache**: Fully utilize cache
3. **Parallel Execution**: Execute tasks in parallel when possible
4. **Conditional Execution**: Only execute tasks when necessary
5. **Monitor Performance**: Continuously monitor and optimize performance

## Related Resources

- [Git Performance Optimization](https://git-scm.com/book/en/v2/Git-Tools-Maintenance-and-Data-Recovery)
- [GitHub Actions Performance](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [GitHub API Best Practices](https://docs.github.com/en/rest/overview/resources-in-the-rest-api#rate-limiting)