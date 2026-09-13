# Supply Chain Security

## Why is Supply Chain Security Important?

Modern software heavily relies on third-party dependencies, supply chain attacks have become one of the main threats.

## SLSA (Supply chain Levels for Software Artifacts)

### SLSA Levels

| Level | Description | Requirements |
|-------|-------------|--------------|
| Level 1 | Build process documented | Build scripts, version control |
| Level 2 | Uses hosted build service | Tamper-proof build, audit logs |
| Level 3 | Build platform protected | Environment isolation, unfalsifiable metadata |
| Level 4 | Dual-person review | Highest security level |

### SLSA Workflow

```yaml
# .github/workflows/slsa-build.yml
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
      run: |
        npm ci
        npm run build
    
    - name: Generate artifact hashes
      id: hash
      run: |
        find dist -type f -exec sha256sum {} \; | base64 -w0 > hashes.txt
        echo "hashes=$(cat hashes.txt)" >> $GITHUB_OUTPUT
    
    - name: Upload artifacts
      uses: actions/upload-artifact@v4
      with:
        name: release-artifacts
        path: dist/

  provenance:
    needs: build
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      actions: read
    
    steps:
    - name: Generate SLSA provenance
      uses: slsa-framework/slsa-github-generator@v1.9.0
      with:
        base64-subjects: "${{ needs.build.outputs.hashes }}"
```

## SBOM (Software Bill of Materials)

### Generate SBOM

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
    
    - name: Generate SBOM (SPDX)
      uses: anchore/sbom-action@v0
      with:
        artifact-name: sbom.spdx.json
        output-file: sbom.spdx.json
    
    - name: Generate SBOM (CycloneDX)
      uses: CycloneDX/gh-github-dx-action@master
      with:
        path: .
        output: sbom.cdx.json
    
    - name: Upload SBOM
      uses: actions/upload-artifact@v4
      with:
        name: sbom
        path: |
          sbom.spdx.json
          sbom.cdx.json
```

### Generate with Syft

```bash
# Install Syft
curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b /usr/local/bin

# Generate SPDX format
syft dir:. -o spdx-json > sbom.spdx.json

# Generate CycloneDX format
syft dir:. -o cyclonedx-json > sbom.cdx.json
```

## Artifact Attestation

### Sign Build Artifacts

```yaml
# .github/workflows/sign-artifacts.yml
name: Sign Artifacts

on:
  release:

permissions:
  id-token: write
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      hashes: ${{ steps.hash.outputs.hashes }}
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Build
      run: npm run build
    
    - name: Generate hashes
      id: hash
      run: |
        sha256sum dist/* > hashes.txt
        echo "hashes<<EOF" >> $GITHUB_OUTPUT
        cat hashes.txt | base64 -w0 >> $GITHUB_OUTPUT
        echo "EOF" >> $GITHUB_OUTPUT
    
    - name: Upload artifacts
      uses: actions/upload-artifact@v4
      with:
        name: release
        path: dist/

  attest:
    needs: build
    runs-on: ubuntu-latest
    
    steps:
    - name: Attest
      uses: actions/attest-build-provenance@v1
      with:
        subject-name: my-app
        subject-digest: sha256:${{ needs.build.outputs.hashes }}
        push-to-registry: true
```

### Verify Signatures

```bash
# Install ghattest
go install github.com/sigstore/ghattest@latest

# Verify artifact
ghattest verify \
  --artifact dist/my-app \
  --subject-name my-app \
  --source-repo owner/repo
```

## Dependabot Security Updates

### Configuration

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "daily"
    open-pull-requests-limit: 10
    reviewers:
      - "security-team"
    labels:
      - "security"
      - "dependencies"
    groups:
      security-updates:
        patterns:
          - "*"
        update-types:
          - "patch"
```

### Auto-merge Security Updates

```yaml
# .github/workflows/auto-merge.yml
name: Auto Merge Dependabot

on:
  pull_request:

permissions:
  contents: write
  pull-requests: write

jobs:
  auto-merge:
    if: github.actor == 'dependabot[bot]'
    runs-on: ubuntu-latest
    
    steps:
    - name: Fetch Dependabot metadata
      id: metadata
      uses: dependabot/fetch-metadata@v1
      with:
        github-token: "${{ secrets.GITHUB_TOKEN }}"
    
    - name: Auto-merge minor and patch updates
      if: steps.metadata.outputs.update-type != 'version-update:semver-major'
      run: gh pr merge --auto --squash "$PR_URL"
      env:
        PR_URL: ${{github.event.pull_request.html_url}}
        GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Container Security

### Scan Container Images

```yaml
# .github/workflows/container-scan.yml
name: Container Security

on:
  push:
    branches: [main]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Build image
      run: docker build -t my-app:${{ github.sha }} .
    
    - name: Scan with Trivy
      uses: aquasecurity/trivy-action@master
      with:
        image-ref: my-app:${{ github.sha }}
        format: sarif
        output: trivy-results.sarif
    
    - name: Upload scan results
      uses: github/codeql-action/upload-sarif@v3
      with:
        sarif_file: trivy-results.sarif
```

## Best Practices

1. **Generate SBOM**: Track all dependencies
2. **Sign Artifacts**: Ensure build integrity
3. **Use SLSA**: Improve supply chain security level
4. **Auto-update Dependencies**: Use Dependabot
5. **Scan Containers**: Detect image vulnerabilities
6. **Audit Logs**: Track all changes

## Related Resources

- [SLSA Documentation](https://slsa.dev/)
- [SBOM Documentation](https://spdx.org/)
- [Sigstore Documentation](https://www.sigstore.dev/)