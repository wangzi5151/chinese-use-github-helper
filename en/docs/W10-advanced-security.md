# GitHub Advanced Security

## Feature Overview

| Feature | Free | Team | Enterprise |
|---------|------|------|------------|
| Secret Scanning | ✅ | ✅ | ✅ |
| Secret Scanning Push Protection | ❌ | ✅ | ✅ |
| Code Scanning (SAST) | ✅ Limited | ✅ | ✅ |
| CodeQL | ✅ Limited | ✅ | ✅ |
| Dependency Review | ✅ | ✅ | ✅ |
| Dependabot | ✅ | ✅ | ✅ |
| Security Overview | ❌ | ✅ | ✅ |

## Secret Scanning

### Automatic Scanning

GitHub automatically scans repositories for sensitive information:

```yaml
# .github/workflows/secret-scan.yml
name: Secret Scan

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
      with:
        fetch-depth: 0
    
    - name: Run TruffleHog
      uses: trufflesecurity/trufflehog@main
      with:
        extra_args: --only-verified
```

### Push Protection

Block commits containing sensitive information during push:

```yaml
# Enable Push Protection
# Settings → Code security and analysis → Push protection
```

### Custom Secret Patterns

```yaml
# .github/secret-scanning.yml
custom-patterns:
  - name: Internal API Key
    pattern: 'internal-api-key-[a-zA-Z0-9]{32}'
    description: Internal API keys
```

## Code Scanning

### CodeQL Analysis

```yaml
# .github/workflows/codeql.yml
name: CodeQL

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 6 * * 1'  # Every Monday

jobs:
  analyze:
    runs-on: ubuntu-latest
    permissions:
      security-events: write
    
    strategy:
      fail-fast: false
      matrix:
        language: ['javascript', 'python']
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Initialize CodeQL
      uses: github/codeql-action/init@v3
      with:
        languages: ${{ matrix.language }}
    
    - name: Autobuild
      uses: github/codeql-action/autobuild@v3
    
    - name: Perform CodeQL Analysis
      uses: github/codeql-action/analyze@v3
      with:
        category: "/language:${{ matrix.language }}"
```

### Semgrep (SAST)

```yaml
# .github/workflows/semgrep.yml
name: Semgrep

on:
  push:
    branches: [main]
  pull_request:

jobs:
  semgrep:
    runs-on: ubuntu-latest
    container:
      image: semgrep/semgrep
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Run Semgrep
      run: semgrep scan --config=auto --sarif -o results.sarif
    
    - name: Upload SARIF
      uses: github/codeql-action/upload-sarif@v3
      with:
        sarif_file: results.sarif
```

## Dependency Review

### PR Dependency Review

```yaml
# .github/workflows/dependency-review.yml
name: Dependency Review

on:
  pull_request:

permissions:
  contents: read
  pull-requests: write

jobs:
  dependency-review:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Dependency Review
      uses: actions/dependency-review-action@v4
      with:
        fail-on-severity: high
        deny-licenses: GPL-3.0, AGPL-3.0
```

### Configure Review Policy

```yaml
# .github/dependency-review.yml
comment-summary-in-pr: always
fail-on-severity: high
license-check: true
deny-licenses:
  - GPL-3.0
  - AGPL-3.0
allow-dependencies-licenses:
  - package: mit
  - package: apache-2.0
```

## Dependabot

### Auto-update Configuration

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
    open-pull-requests-limit: 10
    labels:
      - "dependencies"
      - "automated"
    groups:
      minor-and-patch:
        update-types:
          - "minor"
          - "patch"
  
  - package-ecosystem: "docker"
    directory: "/"
    schedule:
      interval: "weekly"
  
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
  
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "monthly"
```

### Dependabot Alerts

```yaml
# Enable Dependabot Alerts
# Settings → Code security and analysis → Dependabot alerts
```

## Security Overview

### Security Dashboard

```
Organization → Security → Security overview

Displays:
- Repository security status
- Vulnerability count
- Secret leaks
- Dependency issues
```

### Security Policy

```markdown
# SECURITY.md
## Security Policy

### Report Vulnerabilities
Please report security vulnerabilities to security@yourcompany.com

### Response Time
- Critical vulnerabilities: Response within 24 hours
- High vulnerabilities: Response within 72 hours
- Medium vulnerabilities: Response within 1 week
```

## Supply Chain Security

### SLSA Compliance

```yaml
# .github/workflows/slsa.yml
name: SLSA Build

on:
  push:
    tags: ['v*']

jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      hashes: ${{ steps.hash.outputs.hashes }}
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Build
      run: npm run build
    
    - name: Generate SLSA provenance
      uses: slsa-framework/slsa-github-generator@v1.9.0
      with:
        base64-subjects: "${{ steps.hash.outputs.hashes }}"
```

### SBOM Generation

```yaml
# .github/workflows/sbom.yml
name: Generate SBOM

on:
  push:
    branches: [main]

jobs:
  sbom:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Generate SBOM
      uses: anchore/sbom-action@v0
      with:
        artifact-name: sbom.spdx.json
        output-file: sbom.spdx.json
```

## Best Practices

1. **Enable All Security Features**: Secret Scanning, Code Scanning, Dependabot
2. **Configure Push Protection**: Prevent sensitive information leakage
3. **Regularly Review Dependencies**: Timely update vulnerable dependencies
4. **Use SLSA**: Ensure build integrity
5. **Generate SBOM**: Track software composition

## Related Resources

- [GitHub Advanced Security Documentation](https://docs.github.com/en/github-security)
- [CodeQL Documentation](https://codeql.github.com/)
- [SLSA Documentation](https://slsa.dev/)