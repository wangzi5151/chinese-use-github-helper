# GitHub Domestic Direct Access Complete Guide

## Solution Overview

| Solution | Difficulty | Stability | Applicable Scenario |
|----------|------------|-----------|---------------------|
| GitHub520 Auto Update Hosts | ⭐ | ⭐⭐⭐ | Daily use |
| Dev-sidecar | ⭐ | ⭐⭐⭐⭐ | Daily use |
| Watt Toolkit | ⭐ | ⭐⭐⭐ | Temporary acceleration |
| Git Proxy Configuration | ⭐⭐ | ⭐⭐⭐⭐⭐ | All scenarios |
| Domestic Mirror Acceleration | ⭐ | ⭐⭐⭐ | Clone/Download |
| Cloud Server Relay | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Enterprise/Team |

## Solution 1: GitHub520 (Recommended)

### Principle
Automatically update GitHub's hosts file, bypass DNS pollution.

### Installation and Usage

```bash
# macOS/Linux
pip install github520

# Or directly download script
curl -fsSL https://raw.githubusercontent.com/521xueweihan/GitHub520/master/main.py -o github520.py
python3 github520.py
```

### Auto Update (crontab)

```bash
# Update every hour
crontab -e
# Add:
0 * * * * python3 /path/to/github520.py
```

### Use with SwitchHosts

1. Download [SwitchHosts](https://github.com/oldj/SwitchHosts)
2. Add remote hosts
3. URL: `https://raw.githubusercontent.com/521xueweihan/GitHub520/main/hosts`
4. Enable auto refresh

## Solution 2: Dev-sidecar (Developer Sidecar)

### Features
- Automatically accelerate GitHub access
- Support GitHub mirror
- Support accelerating Release downloads
- Support accelerating raw files
- Support accelerating fork

### Installation

```bash
# Download installer
# https://github.com/docmirror/dev-sidecar/releases

# Or install using npm
npm install -g dev-sidecar
```

### Usage

```bash
# Start service
dev-sidecar

# Or run in background
dev-sidecar --background
```

### Supported Features

| Feature | Description |
|---------|-------------|
| GitHub Web Acceleration | Faster GitHub access |
| Release Download Acceleration | Download Release files |
| raw File Acceleration | Accelerate raw.githubusercontent.com |
| clone/push Acceleration | Git operations acceleration |
| Avatar Acceleration | GitHub avatar display |

## Solution 3: Watt Toolkit (Former Steam++)

### Download and Install

Visit https://steampp.net/

### Usage Steps

1. Install and open Watt Toolkit
2. Select **Network Acceleration**
3. Check **GitHub**
4. Click **One-click Acceleration**

## Solution 4: Git Proxy Configuration

### Temporary Proxy Usage

```bash
# HTTP proxy
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890

# SOCKS5 proxy
git config --global http.proxy socks5://127.0.0.1:7890
git config --global https.proxy socks5://127.0.0.1:7890

# Only for GitHub
git config --global http.https://github.com.proxy http://127.0.0.1:7890
```

### Remove Proxy

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

### Use Environment Variables

```bash
# Temporary setting
export http_proxy=http://127.0.0.1:7890
export https_proxy=http://127.0.0.1:7890

# Remove
unset http_proxy
unset https_proxy
```

## Solution 5: Domestic Mirror Sites

### GitHub Mirrors

| Mirror | URL | Function |
|--------|-----|----------|
| GitHub Mirror | https://ghproxy.com | clone/download |
| gh-proxy.com | https://gh-proxy.com | Full proxy |
| GitHub Proxy | https://github.moeyy.xyz | clone/download |
| kkgithub | https://kkgithub.com | Full mirror |

### Usage

```bash
# Clone acceleration
git clone https://ghproxy.com/https://github.com/user/repo.git

# Download Release acceleration
wget https://ghproxy.com/https://github.com/user/repo/releases/download/v1.0/file.zip

# raw file acceleration
curl https://ghproxy.com/https://github.com/user/repo/raw/main/file.txt
```

### GitHub Mirror Sites (Full Features)

| Mirror | URL | Description |
|--------|-----|-------------|
| kkgithub | https://kkgithub.com | Full feature mirror |
| github.91chi.fun | https://github.91chi.fun | Proxy access |

## Solution 6: Domestic Package Manager Mirrors

### npm Taobao Mirror

```bash
# Temporary use
npm install --registry https://registry.npmmirror.com

# Permanent setting
npm config set registry https://registry.npmmirror.com

# Verify
npm config get registry
```

### Python pip Mirror

```bash
# Temporary use
pip install -i https://mirrors.aliyun.com/pypi/simple/ package

# Permanent setting
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
pip config set global.trusted-host mirrors.aliyun.com
```

### Docker Mirror

```json
// /etc/docker/daemon.json
{
  "registry-mirrors": [
    "https://docker.1ms.run",
    "https://docker.xuanyuan.me",
    "https://docker.m.daocloud.io"
  ]
}
```

```bash
# Restart Docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### Homebrew Mirror (macOS)

```bash
# Replace Homebrew source
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git"
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git"
export HOMEBREW_BOTTLE_DOMAIN="https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles"

# Install
brew install wget
```

### Go Module Mirror

```bash
# Set proxy
go env -w GOPROXY=https://goproxy.cn,direct

# Verify
go env GOPROXY
```

## Solution 7: Cloud Server Relay

### Build Reverse Proxy

```nginx
# nginx configuration
server {
    listen 443 ssl;
    server_name github-proxy.your-domain.com;
    
    location / {
        proxy_pass https://github.com;
        proxy_set_header Host github.com;
        proxy_ssl_server_name on;
    }
}
```

### Use Cloudflare Worker

```javascript
// Cloudflare Worker script
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

async function handleRequest(request) {
  const url = new URL(request.url)
  const githubUrl = `https://github.com${url.pathname}`
  
  return fetch(githubUrl, {
    headers: request.headers,
    method: request.method,
  })
}
```

## Solution 8: SSH Optimization

### Use SSH Instead of HTTPS

```bash
# Convert to SSH
git config --global url."git@github.com:".insteadOf "https://github.com/"

# Clone using SSH
git clone git@github.com:user/repo.git
```

### SSH Connection Optimization

```bash
# ~/.ssh/config
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60
    ServerAliveCountMax 30
    TCPKeepAlive yes
```

## Speed Test Tools

```bash
# Test GitHub connectivity
ping github.com

# Test download speed
curl -o /dev/null -w "speed: %{speed_download} bytes/s\n" https://github.com/user/repo/archive/main.zip

# Use speedtest
speedtest-cli
```

## Best Practices

1. **Combined Use**: Hosts + Proxy dual insurance
2. **Auto Update**: Use GitHub520 to auto-update hosts
3. **Mirror Backup**: Configure domestic package manager mirrors
4. **SSH First**: Use SSH instead of HTTPS
5. **Shallow Clone**: Use `--depth 1` for large repositories

## Recommended Configuration

```bash
# 1. Install GitHub520
pip install github520

# 2. Configure Git SSH
git config --global url."git@github.com:".insteadOf "https://github.com/"

# 3. Configure npm mirror
npm config set registry https://registry.npmmirror.com

# 4. Configure pip mirror
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/

# 5. Configure Go mirror
go env -w GOPROXY=https://goproxy.cn,direct
```

## Related Resources

- [GitHub520](https://github.com/521xueweihan/GitHub520)
- [Dev-sidecar](https://github.com/docmirror/dev-sidecar)
- [Watt Toolkit](https://steampp.net/)
- [Taobao NPM Mirror](https://npmmirror.com/)
- [Alibaba Cloud pip Mirror](https://mirrors.aliyun.com/pypi/simple/)
- [DaoCloud Docker Mirror](https://docker.m.daocloud.io)