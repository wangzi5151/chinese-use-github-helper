# GitHub 国内加速指南

> 本章将详细介绍国内开发者访问 GitHub 的各种加速方法，包括修改 Hosts 文件、使用加速代理、配置 Git 代理、使用国内镜像、SSH 连接优化等。

---

## 目录

1. [为什么需要加速？](#为什么需要加速)
2. [方法一：修改 Hosts 文件](#方法一修改-hosts-文件)
3. [方法二：使用 GitHub 加速代理](#方法二使用-github-加速代理)
4. [方法三：配置 Git 代理](#方法三配置-git-代理)
5. [方法四：使用国内镜像](#方法四使用国内镜像)
6. [方法五：使用 SSH 连接](#方法五使用-ssh-连接)
7. [方法六：优化 Git 配置](#方法六优化-git-配置)
8. [方法七：使用 CDN 加速](#方法七使用-cdn-加速)
9. [方法八：使用 GitHub Actions 代理](#方法八使用-github-actions-代理)
10. [企业级解决方案](#企业级解决方案)
11. [常见问题解答](#常见问题解答)
12. [推荐工具](#推荐工具)
13. [相关资源](#相关资源)

---

## 为什么需要加速？

由于网络原因，国内访问 GitHub 有时会较慢，主要原因包括：

### 网络问题分析

**DNS 污染**：
- 国内 DNS 服务器可能返回错误的 IP 地址
- 导致访问 GitHub 时连接到错误的服务器
- 解决方案：使用国外 DNS 服务器或修改 Hosts 文件

**网络延迟**：
- 国内到 GitHub 服务器的物理距离较远
- 网络路由可能不是最优路径
- 解决方案：使用 CDN 加速或代理服务

**带宽限制**：
- 国际带宽有限，高峰期可能拥堵
- 大文件下载速度慢
- 解决方案：使用镜像站点或分片下载

**连接不稳定**：
- 网络波动导致连接中断
- 长时间操作容易失败
- 解决方案：使用 SSH 连接或配置重试机制

### 加速效果对比

| 加速方法 | 速度提升 | 稳定性 | 易用性 | 适用场景 |
|----------|----------|--------|--------|----------|
| 修改 Hosts | 中等 | 中等 | 简单 | 临时使用 |
| 加速代理 | 高 | 高 | 简单 | 日常使用 |
| Git 代理 | 高 | 高 | 中等 | 有代理服务器 |
| 国内镜像 | 高 | 高 | 简单 | 只读操作 |
| SSH 连接 | 中等 | 高 | 中等 | 推送代码 |
| CDN 加速 | 高 | 高 | 复杂 | 企业使用 |

## 方法一：修改 Hosts 文件

### 原理
通过修改 hosts 文件，直接指定 GitHub 的 IP 地址，避免 DNS 污染。

### 步骤

1. **获取 GitHub IP 地址**

访问以下网站获取最新 IP：
- https://github.com/ipaddresses
- https://www.ipaddress.com/
- https://ip.tool.lu/

需要获取的域名：
- `github.com`
- `github.global.ssl.fastly.net`
- `assets-cdn.github.com`
- `github.io`
- `api.github.com`
- `raw.githubusercontent.com`
- `gist.github.com`

2. **修改 hosts 文件**

**Windows**：
```cmd
# 以管理员身份打开记事本
notepad C:\Windows\System32\drivers\etc\hosts

# 添加以下内容
# GitHub
140.82.114.4 github.com
199.232.69.194 github.global.ssl.fastly.net
185.199.108.153 assets-cdn.github.com
140.82.114.20 github.io
140.82.113.22 api.github.com
140.82.114.6 nodeload.github.com
185.199.108.133 raw.githubusercontent.com
140.82.114.10 gist.github.com
```

**macOS/Linux**：
```bash
# 编辑 hosts 文件
sudo nano /etc/hosts

# 添加以下内容
# GitHub
140.82.114.4 github.com
199.232.69.194 github.global.ssl.fastly.net
185.199.108.153 assets-cdn.github.com
140.82.114.20 github.io
140.82.113.22 api.github.com
140.82.114.6 nodeload.github.com
185.199.108.133 raw.githubusercontent.com
140.82.114.10 gist.github.com
```

3. **刷新 DNS 缓存**

**Windows**：
```cmd
ipconfig /flushdns
```

**macOS**：
```bash
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

**Linux**：
```bash
sudo systemd-resolve --flush-caches
# 或
sudo /etc/init.d/networking restart
```

### 自动更新脚本

**Python 脚本**：
```python
#!/usr/bin/env python3
import requests
import re
import platform
import subprocess

def get_github_ips():
    """获取 GitHub IP 地址"""
    domains = [
        'github.com',
        'github.global.ssl.fastly.net',
        'assets-cdn.github.com',
        'github.io',
        'api.github.com',
        'raw.githubusercontent.com'
    ]
    
    ips = {}
    for domain in domains:
        try:
            response = requests.get(f'https://ip.tool.lu/{domain}')
            ip = response.text.strip()
            ips[domain] = ip
        except:
            print(f"无法获取 {domain} 的 IP 地址")
    
    return ips

def update_hosts(ips):
    """更新 hosts 文件"""
    system = platform.system()
    
    if system == 'Windows':
        hosts_path = r'C:\Windows\System32\drivers\etc\hosts'
    else:
        hosts_path = '/etc/hosts'
    
    # 读取现有内容
    with open(hosts_path, 'r') as f:
        content = f.read()
    
    # 移除旧的 GitHub 条目
    content = re.sub(r'# GitHub\n.*?\n\n', '', content, flags=re.DOTALL)
    
    # 添加新的条目
    new_entries = '# GitHub\n'
    for domain, ip in ips.items():
        new_entries += f'{ip} {domain}\n'
    new_entries += '\n'
    
    # 写入文件
    with open(hosts_path, 'w') as f:
        f.write(content + new_entries)
    
    print("hosts 文件已更新")

def flush_dns():
    """刷新 DNS 缓存"""
    system = platform.system()
    
    if system == 'Windows':
        subprocess.run(['ipconfig', '/flushdns'], check=True)
    elif system == 'Darwin':
        subprocess.run(['sudo', 'dscacheutil', '-flushcache'], check=True)
        subprocess.run(['sudo', 'killall', '-HUP', 'mDNSResponder'], check=True)
    else:
        subprocess.run(['sudo', 'systemd-resolve', '--flush-caches'], check=True)
    
    print("DNS 缓存已刷新")

if __name__ == '__main__':
    print("正在获取 GitHub IP 地址...")
    ips = get_github_ips()
    
    if ips:
        print("正在更新 hosts 文件...")
        update_hosts(ips)
        
        print("正在刷新 DNS 缓存...")
        flush_dns()
        
        print("完成！")
    else:
        print("无法获取 IP 地址，请手动更新")
```

**Shell 脚本**：
```bash
#!/bin/bash

# GitHub IP 更新脚本

# 获取 GitHub IP
get_ip() {
    local domain=$1
    curl -s "https://ip.tool.lu/$domain" | head -1
}

# 更新 hosts 文件
update_hosts() {
    local hosts_file="/etc/hosts"
    
    # 备份原文件
    sudo cp "$hosts_file" "$hosts_file.bak"
    
    # 移除旧的 GitHub 条目
    sudo sed -i '/# GitHub/,/^$/d' "$hosts_file"
    
    # 添加新的条目
    echo "# GitHub" | sudo tee -a "$hosts_file"
    echo "$(get_ip github.com) github.com" | sudo tee -a "$hosts_file"
    echo "$(get_ip github.global.ssl.fastly.net) github.global.ssl.fastly.net" | sudo tee -a "$hosts_file"
    echo "$(get_ip assets-cdn.github.com) assets-cdn.github.com" | sudo tee -a "$hosts_file"
    echo "$(get_ip github.io) github.io" | sudo tee -a "$hosts_file"
    echo "$(get_ip api.github.com) api.github.com" | sudo tee -a "$hosts_file"
    echo "$(get_ip raw.githubusercontent.com) raw.githubusercontent.com" | sudo tee -a "$hosts_file"
    echo "" | sudo tee -a "$hosts_file"
}

# 刷新 DNS 缓存
flush_dns() {
    if [[ "$OSTYPE" == "darwin"* ]]; then
        sudo dscacheutil -flushcache
        sudo killall -HUP mDNSResponder
    else
        sudo systemd-resolve --flush-caches
    fi
}

echo "正在更新 GitHub IP 地址..."
update_hosts
echo "正在刷新 DNS 缓存..."
flush_dns
echo "完成！"
```

## 方法二：使用 GitHub 加速代理

### 公共代理服务

| 服务 | 地址 | 说明 |
|------|------|------|
| GHProxy | https://ghproxy.com | 免费，支持多种操作 |
| GitHub Mirror | https://mirror.ghproxy.com | 稳定可靠 |
| Dev-sidecar | https://github.com/docmirror/dev-sidecar | 开源客户端 |

### 使用 GHProxy 加速下载

```bash
# 加速克隆
git clone https://ghproxy.com/https://github.com/user/repo.git

# 加速下载文件
wget https://ghproxy.com/https://github.com/user/repo/raw/main/file.zip
```

### 使用 Dev-sidecar

1. 下载安装 Dev-sidecar
2. 启动服务
3. 自动加速 GitHub 访问

## 方法三：配置 Git 代理

如果你有代理服务器：

```bash
# 配置 HTTP 代理
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890

# 配置 SOCKS5 代理
git config --global http.proxy socks5://127.0.0.1:7890
git config --global https.proxy socks5://127.0.0.1:7890

# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy
```

## 方法四：使用国内镜像

### GitHub 镜像站

| 镜像 | 地址 | 说明 |
|------|------|------|
| Gitee | https://gitee.com | 国内最大代码托管平台 |
| GitCode | https://gitcode.com | CSDN 旗下 |
| CODING | https://coding.net | 腾讯云 DevOps |

### 从镜像克隆

```bash
# 从 Gitee 镜像克隆
git clone https://gitee.com/mirrors/user-repo.git
```

## 方法五：使用 SSH 连接

SSH 连接通常比 HTTPS 更稳定：

```bash
# 使用 SSH 克隆
git clone git@github.com:user/repo.git
```

## 方法六：配置 Git 使用 SSH

```bash
# 将 HTTPS 转换为 SSH
git config --global url."git@github.com:".insteadOf "https://github.com/"
```

## 常见问题

### Q: 克隆速度很慢？
**A:** 尝试以下方法：
1. 使用 SSH 代替 HTTPS
2. 使用浅克隆：`git clone --depth 1`
3. 使用加速代理

### Q: push 失败？
**A:** 检查：
1. SSH 密钥是否正确配置
2. 网络连接是否正常
3. 尝试使用代理

### Q: GitHub Pages 无法访问？
**A:** 可能是被墙，尝试：
1. 使用代理访问
2. 配置自定义域名
3. 使用 CDN 加速

## 推荐工具

| 工具 | 说明 |
|------|------|
| Dev-sidecar | 开源 GitHub 加速工具 |
| Watt Toolkit | 原 Steam++，支持 GitHub 加速 |
| Proxifier | 代理客户端 |

## 相关资源

- [GitHub 官方文档](https://docs.github.com)
- [GitHub 状态页面](https://www.githubstatus.com)
