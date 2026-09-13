# Monorepo Management

## What is Monorepo?

Monorepo is a management approach that puts multiple project code in the same repository.

## Tool Comparison

| Tool | Language | Features |
|------|----------|----------|
| pnpm workspaces | Node.js | Lightweight, efficient |
| Turborepo | Any | Incremental build, cache |
| Nx | Any | Smart build, dependency graph |
| Lerna | Node.js | Traditional tool |

## pnpm Workspaces

### Configuration

```json
// package.json
{
  "name": "monorepo",
  "private": true,
  "scripts": {
    "build": "pnpm -r build",
    "test": "pnpm -r test",
    "dev": "pnpm -r --parallel dev"
  },
  "pnpm": {
    "workspace": "packages/*"
  }
}
```

### Project Structure

```
monorepo/
├── package.json
├── pnpm-workspace.yaml
├── packages/
│   ├── shared/          # Shared library
│   │   ├── package.json
│   │   └── src/
│   ├── web/             # Frontend application
│   │   ├── package.json
│   │   └── src/
│   └── api/             # Backend API
│       ├── package.json
│       └── src/
└── pnpm-lock.yaml
```

### pnpm-workspace.yaml

```yaml
packages:
  - 'packages/*'
  - 'apps/*'
```

## Turborepo

### Configuration

```json
// turbo.json
{
  "$schema": "https://turbo.build/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "test": {
      "dependsOn": ["build"]
    },
    "lint": {},
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

### Usage

```bash
# Build all packages
turbo run build

# Build specific package only
turbo run build --filter=web

# Run tests
turbo run test

# Development mode
turbo run dev
```

### Cache

```bash
# View cache
turbo run build --dry

# Clear cache
turbo prune

# Remote cache
turbo login
turbo link
```

## Nx

### Configuration

```json
// nx.json
{
  "targetDefaults": {
    "build": {
      "dependsOn": ["^build"],
      "inputs": ["production", "^production"]
    }
  },
  "defaultBase": "main"
}
```

### Usage

```bash
# Build all
nx run-many -t build

# Build only affected
nx affected -t build

# Run tests
nx run-many -t test

# View dependency graph
nx graph
```

### Dependency Graph

```bash
# Generate dependency graph
nx graph

# Visualize specific project
nx graph --affected
```

## GitHub Actions Integration

```yaml
# .github/workflows/monorepo.yml
name: Monorepo CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  detect-changes:
    runs-on: ubuntu-latest
    outputs:
      packages: ${{ steps.changes.outputs.packages }}
    steps:
    - uses: actions/checkout@v4
    
    - name: Detect changes
      id: changes
      run: |
        PACKAGES=$(git diff --name-only HEAD~1 | grep -oP 'packages/\K[^/]+' | sort -u | jq -R -s -c 'split("\n")[:-1]')
        echo "packages=$PACKAGES" >> $GITHUB_OUTPUT

  build:
    needs: detect-changes
    runs-on: ubuntu-latest
    strategy:
      matrix:
        package: ${{ fromJson(needs.detect-changes.outputs.packages) }}
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
    
    - run: pnpm install --frozen-lockfile
    - run: pnpm --filter ${{ matrix.package }} build
    - run: pnpm --filter ${{ matrix.package }} test
```

## Best Practices

1. **Split Packages Reasonably**: Split by functional boundaries
2. **Use Shared Libraries**: Avoid code duplication
3. **Incremental Build**: Only build affected packages
4. **Remote Cache**: Accelerate CI/CD
5. **Dependency Management**: Use workspace protocol

## Related Resources

- [pnpm Workspaces Documentation](https://pnpm.io/workspaces)
- [Turborepo Documentation](https://turbo.build/repo/docs)
- [Nx Documentation](https://nx.dev/docs)