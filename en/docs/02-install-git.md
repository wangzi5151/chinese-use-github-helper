# Install and Configure Git

## Windows Installation (Detailed with Screenshots)

### Method 1: Official Download (Recommended)

**Step 1: Open Download Page**
1. Open browser and visit **https://git-scm.com/download/win**
2. The page will automatically detect your system and start downloading
3. If it doesn't download automatically, click **Click here to download manually**

**Step 2: Run Installer**
1. Find the downloaded file (usually in "Downloads" folder)
2. Double-click to run `Git-xxx-xxx-bit.exe`

**Step 3: Installation Wizard**

Click through the following steps:

```
┌─────────────────────────────────────────────┐
│  Git Setup                                   │
│                                             │
│  Welcome to Git Setup                       │
│                                             │
│               [Next >]                       │
└─────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────┐
│  GNU General Public License                 │
│                                             │
│  [Read license agreement]                   │
│                                             │
│               [Next >]                       │
└─────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────┐
│  Select Components                           │
│                                             │
│  ☑ Git Bash        ← Command line tool, must select │
│  ☑ Git GUI         ← Graphical interface tool │
│  ☑ Git LFS         ← Large file support │
│  ☑ Associate .git  ← Associate .git files │
│  ☑ Add Git Bash to ← Add to terminal │
│    Terminal Explorer                        │
│                                             │
│               [Next >]                       │
└─────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────┐
│  Adjusting your PATH environment            │
│                                             │
│  ● Use Git from the Windows Command Prompt  │
│    ← Recommended for most users │
│  ○ Use Git and optional Unix tools │
│  ○ Use Git from Git Bash only │
│                                             │
│               [Next >]                       │
└─────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────┐
│  Choosing the SSH executable                 │
│                                             │
│  ● Use bundled OpenSSH                      │
│    ← Recommended │
│  ○ Use external OpenSSH │
│                                             │
│               [Next >]                       │
└─────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────┐
│  Configuring the line ending conversions     │
│                                             │
│  ● Checkout Windows-style, commit Unix-style│
│    ← Recommended for Windows users │
│  ○ Checkout as-is, commit Unix-style │
│  ○ Checkout as-is, commit as-is │
│                                             │
│               [Next >]                       │
└─────────────────────────────────────────────┘
```

**Step 4: Complete Installation**
1. Click **Install** to start installation
2. Wait for installation to complete
3. Click **Finish**

### Method 2: Using Winget

```powershell
# Open Command Prompt or PowerShell
winget install Git.Git
```

### Method 3: Using Chocolatey

```powershell
choco install git
```

## macOS Installation

### Method 1: Homebrew (Recommended)

**Step 1: Install Homebrew**

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

**Step 2: Install Git**

```bash
brew install git
```

### Method 2: Xcode Command Line Tools

```bash
xcode-select --install
```

Click **Install** when the dialog appears.

### Method 3: Official Download

1. Visit **https://git-scm.com/download/mac**
2. Download the installer
3. Double-click to install

## Linux Installation

### Ubuntu/Debian

```bash
sudo apt update
sudo apt install git
```

### CentOS/RHEL

```bash
sudo yum install git
```

### Fedora

```bash
sudo dnf install git
```

### Arch Linux

```bash
sudo pacman -S git
```

## Verify Installation

### Open Terminal

**Windows:**
- Press `Win + R`
- Type `cmd`
- Press Enter

**macOS:**
- Press `Command + Space`
- Type `Terminal`
- Press Enter

**Linux:**
- Press `Ctrl + Alt + T`

### Enter Command

```bash
git --version
```

### Expected Output

```
git version 2.43.0.windows.1
```

Seeing the version number means installation was successful!

## Initial Configuration

### Why Configure?

Git needs to know who you are so it can record your information with each commit.

### Set Username

```bash
git config --global user.name "Your Name"
```

**Example:**
```bash
git config --global user.name "John Doe"
```

### Set Email

```bash
git config --global user.email "your@email.com"
```

**Example:**
```bash
git config --global user.email "johndoe@example.com"
```

⚠️ **Important: The email must match your GitHub account email!**

### Set Default Branch Name

```bash
git config --global init.defaultBranch main
```

### Set Default Editor

```bash
# If using VS Code
git config --global core.editor "code --wait"

# If using Vim
git config --global core.editor "vim"

# If using Nano
git config --global core.editor "nano"
```

### Enable Color Output

```bash
git config --global color.ui auto
```

### Configure Line Ending Handling

```bash
# Windows users
git config --global core.autocrlf true

# macOS/Linux users
git config --global core.autocrlf input
```

### Configure Chinese Filenames

```bash
# Prevent Chinese filenames from displaying as garbled text
git config --global core.quotepath false
```

## View Configuration

### View All Configuration

```bash
git config --list
```

### View Specific Configuration

```bash
# View username
git config user.name

# View email
git config user.email

# View editor
git config core.editor
```

### Configuration File Locations

| Level | File Location | Description |
|-------|--------------|-------------|
| `--system` | `/etc/gitconfig` | System-level configuration |
| `--global` | `~/.gitconfig` | User-level configuration (recommended) |
| `--local` | `.git/config` | Repository-level configuration |

**Priority:** `local` > `global` > `system`

## Advanced Configuration

### Set Git Aliases

```bash
# Common aliases
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.lg "log --oneline --graph --all"
git config --global alias.last "log -1 HEAD"
git config --global alias.unstage "reset HEAD --"
```

### Configure Proxy

```bash
# HTTP proxy
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890

# SOCKS5 proxy
git config --global http.proxy socks5://127.0.0.1:7890
git config --global https.proxy socks5://127.0.0.1:7890

# Remove proxy
git config --global --unset http.proxy
git config --global --unset https.proxy
```

## Common Issues

### Q: Can't find git command after Windows installation?

**Solution:**
1. Reopen Command Prompt
2. Check PATH environment variable
3. Reinstall Git, make sure to check "Add Git to PATH"

### Q: macOS shows "command not found: git"?

**Solution:**
```bash
# Install Xcode Command Line Tools
xcode-select --install

# Or install using Homebrew
brew install git
```

### Q: How to delete configuration?

```bash
# Delete global configuration
git config --global --unset user.name
git config --global --unset user.email

# Edit configuration file
git config --global --edit
```

## Best Practices

1. **Use Real Information**: Username and email should match GitHub account
2. **Use Global Configuration**: Unless special needs, use `--global` configuration
3. **Configure Editor**: Choose an editor you're familiar with
4. **Configure Proxy**: If having issues accessing GitHub, configure proxy
5. **Configure Aliases**: Set aliases for commonly used commands to improve efficiency

---

**Next: [Register GitHub Account →](03-signup-github.md)**