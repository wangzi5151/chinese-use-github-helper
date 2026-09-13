# Domestic Git Hosting Platforms

## Platform Comparison

| Platform | URL | Features | Free Private Repos |
|----------|-----|----------|-------------------|
| Gitee | gitee.com | Largest in China, Chinese-friendly | ✅ |
| GitCode | gitcode.com | Under CSDN, active community | ✅ |
| CODING | coding.net | Tencent Cloud DevOps | ✅ |
| Coding.net | coding.net | Enterprise DevOps | ✅ |
| Huawei Cloud CodeHub | codehub.huaweicloud.com | Huawei Cloud ecosystem | ✅ |
| Alibaba Cloud Codeup | codeup.aliyun.com | Alibaba Cloud ecosystem | ✅ |

---

## Gitee

### Introduction
Gitee is the largest code hosting platform in China, providing Git repository hosting, code collaboration and other features.

### Advantages
- Fast access speed
- Chinese interface
- Markdown support
- CI/CD provided
- Pages service support

### Registration and Usage

1. Visit https://gitee.com
2. Register account
3. Create repository
4. Push code

### Basic Operations

```bash
# Clone repository
git clone https://gitee.com/user/repo.git

# Push code
git remote add origin https://gitee.com/user/repo.git
git push -u origin main
```

### Gitee Pages

```bash
# Deploy static website
# 1. Enable Pages in repository settings
# 2. Select deployment branch and directory
# 3. Click update
```

---

## GitCode

### Introduction
GitCode is a code hosting platform under CSDN, focused on developer community.

### Advantages
- CSDN community integration
- Copilot support
- Code hosting provided
- Pages support

### Basic Operations

```bash
# Clone repository
git clone https://gitcode.com/user/repo.git
```

---

## CODING

### Introduction
CODING is a DevOps platform under Tencent Cloud, providing code hosting, CI/CD, project management and other features.

### Advantages
- Enterprise-level features
- Complete DevOps toolchain
- Tencent Cloud integration
- Kubernetes support

### Basic Operations

```bash
# Clone repository
git clone https://coding.net/user/project/repo.git
```

---

## Huawei Cloud CodeHub

### Introduction
Huawei Cloud CodeHub is a code hosting service provided by Huawei Cloud.

### Advantages
- Huawei Cloud integration
- DevOps support
- Enterprise-level security

### Basic Operations

```bash
# Clone repository
git clone https://codehub.huaweicloud.com/user/repo.git
```

---

## Alibaba Cloud Codeup

### Introduction
Alibaba Cloud Codeup is a code hosting service provided by Alibaba Cloud.

### Advantages
- Alibaba Cloud integration
- Enterprise collaboration support
- Code scanning provided

### Basic Operations

```bash
# Clone repository
git clone https://codeup.aliyun.com/user/repo.git
```

---

## Selection Suggestions

### Individual Developers
- **Recommended**: Gitee
- **Reason**: Chinese-friendly, fast access, complete features

### Open Source Projects
- **Recommended**: GitHub + Gitee dual hosting
- **Reason**: GitHub for international, Gitee for domestic

### Enterprise Teams
- **Recommended**: CODING or Alibaba Cloud Codeup
- **Reason**: Enterprise-level features, secure and reliable

### Learning Practice
- **Recommended**: Gitee
- **Reason**: Simple and easy to use, Chinese documentation

---

## Sync to Domestic Platform

### Method 1: Manual Sync

```bash
# Add domestic platform as remote repository
git remote add gitee https://gitee.com/user/repo.git

# Push to domestic platform
git push gitee main
```

### Method 2: Use Gitee Sync Feature

1. Import GitHub repository on Gitee
2. Set up auto sync
3. Regular updates

### Method 3: Use GitHub Actions

```yaml
name: Sync to Gitee

on:
  push:
    branches: [main]

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
      with:
        fetch-depth: 0
        
    - name: Push to Gitee
      uses: wearerequired/git-mirror-action@master
      env:
        SSH_PRIVATE_KEY: ${{ secrets.GITEE_RSA_PRIVATE_KEY }}
      with:
        source-repo: git@github.com:user/repo.git
        destination-repo: git@gitee.com:user/repo.git
```

---

## Related Resources

- [Gitee Official Website](https://gitee.com)
- [GitCode Official Website](https://gitcode.com)