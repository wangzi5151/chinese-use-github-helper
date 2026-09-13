# GitHub Packages Introduction

## What is GitHub Packages?

GitHub Packages is a package management service that allows you to securely publish and consume software packages. It is deeply integrated with the GitHub ecosystem.

## Supported Package Managers

| Package Manager | Language/Platform | Repository Type |
|-----------------|-------------------|-----------------|
| npm | JavaScript/Node.js | npm |
| NuGet | .NET | nuget |
| RubyGems | Ruby | gem |
| Maven | Java | maven |
| Gradle | Java/Kotlin | gradle |
| Docker | Container | docker |
| Helm | Kubernetes | helm |
| Swift | iOS/macOS | swift |

## Usage Scenarios

1. **Private Packages**: Publish packages for internal company use
2. **Public Packages**: Publish open source packages for community use
3. **Dependency Management**: Use GitHub Packages as package source in CI/CD
4. **Container Images**: Store and distribute Docker images

## Install Packages

### npm

```bash
# Configure npm to use GitHub Packages
echo "@your-username:registry=https://npm.pkg.github.com" > .npmrc

# Install package
npm install @your-username/package-name
```

### NuGet

```bash
# Add GitHub Packages source
dotnet nuget add source "https://nuget.pkg.github.com/your-username/index.json" \
  --name "GitHub" \
  --username "your-username" \
  --password "your-token"

# Install package
dotnet add package package-name
```

### Docker

```bash
# Login to GitHub Container Registry
echo "your-token" | docker login ghcr.io -u your-username --password-stdin

# Pull image
docker pull ghcr.io/username/image-name:tag
```

## Publish Packages

### npm

```bash
# Login
npm login --registry=https://npm.pkg.github.com

# Publish
npm publish
```

### Docker

```bash
# Build image
docker build -t ghcr.io/username/image-name:tag .

# Push image
docker push ghcr.io/username/image-name:tag
```

## Configure package.json

```json
{
  "name": "@your-username/package-name",
  "version": "1.0.0",
  "description": "My package",
  "main": "index.js",
  "publishConfig": {
    "registry": "https://npm.pkg.github.com"
  }
}
```

## GitHub Actions Integration

### Publish npm Package

```yaml
name: Publish Package

on:
  release:
    types: [created]

jobs:
  publish:
    runs-on: ubuntu-latest
    
    permissions:
      contents: read
      packages: write
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
        registry-url: 'https://npm.pkg.github.com'
    
    - run: npm ci
    - run: npm publish
      env:
        NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Publish Docker Image

```yaml
name: Publish Docker Image

on:
  push:
    tags:
      - 'v*'

jobs:
  publish:
    runs-on: ubuntu-latest
    
    permissions:
      contents: read
      packages: write
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Log in to Container Registry
      uses: docker/login-action@v3
      with:
        registry: ghcr.io
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}
    
    - name: Build and push
      uses: docker/build-push-action@v5
      with:
        push: true
        tags: ghcr.io/${{ github.repository }}:${{ github.ref_name }}
```

## Permission Management

### Repository Level

Configure in repository **Settings** → **Actions** → **General**:
- **Read**: Read packages
- **Write**: Read and publish packages
- **Admin**: Manage package permissions

### Organization Level

Configure default permissions in organization **Settings** → **Packages**.

## Version Management

### Semantic Versioning

```
MAJOR.MINOR.PATCH
```

- **MAJOR**: Incompatible API changes
- **MINOR**: Backward compatible new features
- **PATCH**: Backward compatible bug fixes

### Tags

Use Git tags to mark versions:
```bash
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0
```

## Best Practices

1. **Use Semantic Versioning**: Let users understand nature of changes
2. **Write Clear Release Notes**: Explain new features and fixes
3. **Automate Release Process**: Use GitHub Actions
4. **Set Appropriate Permissions**: Principle of least privilege
5. **Regularly Update Dependencies**: Maintain package security

## Limits

- **Storage Limits**:
  - GitHub Free: 500 MB
  - GitHub Pro: 2 GB
  - GitHub Team: 2 GB
  - GitHub Enterprise: 50 GB

- **Bandwidth Limits**:
  - GitHub Free: 1 GB/month
  - GitHub Pro: 2 GB/month

## Related Resources

- [GitHub Packages Official Documentation](https://docs.github.com/en/packages)
- [Publish Packages with GitHub Packages](https://docs.github.com/en/packages/working-with-a-github-packages-registry)