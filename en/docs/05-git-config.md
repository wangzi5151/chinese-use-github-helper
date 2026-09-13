# Git Configuration Complete Guide

Git is a highly customizable version control system. Proper Git configuration not only improves development efficiency but also avoids many common collaboration issues. Many beginners start using Git directly after installation and encounter various strange problems: commit records showing wrong name, line ending errors, editor not opening, having to enter password every push... These are all caused by not doing basic configuration properly.

This chapter will systematically introduce all aspects of Git configuration from scratch, helping you create an efficient and convenient Git working environment. Whether you are a beginner or an experienced developer, you can find practical configuration tips here.

---

## 1. Git Configuration Layer Details

Git configuration uses a layered architecture consisting of three layers. Understanding these layers is crucial for managing configurations in different scenarios. When the same configuration item exists in multiple layers, Git will override from high to low priority: **Repository Level > Global Level > System Level**.

You can think of these three configuration layers as "inheritance relationship": system level is base configuration, global level overrides system level, repository level overrides global level. The advantage of this design is that you can have a common global configuration while setting special configurations for specific projects without interfering with each other.

### 1.1 System Level Configuration

System level configuration affects **all users** and **all repositories** on that machine. Configuration file is located at `/etc/gitconfig` (Linux/macOS) or `C:\Program Files\Git\etc\gitconfig` (Windows). In most personal development scenarios, you rarely need to modify this layer. It is usually maintained by system administrators, common in enterprise unified development environments, shared servers, school computer labs, etc.

```bash
# View system level configuration
git config --list --system

# Edit system level configuration (requires admin privileges)
sudo git config --system core.editor "vim"

# Directly edit system level configuration file
sudo nano /etc/gitconfig
```

Typical uses of system level configuration include: unified company code review rules, specifying default diff tools, configuring global line ending strategies, etc. If you are a personal developer, you can usually skip this layer.

### 1.2 Global Level Configuration

Global level configuration is at the **current user** level, affecting all repositories of that user. Configuration file is located at `~/.gitconfig` (Linux/macOS) or `C:\Users\YourUsername\.gitconfig` (Windows). This is the most commonly used configuration layer by developers, and most of your Git configurations should be placed here.

```bash
# View global level configuration
git config --list --global

# Edit global level configuration
git config --global user.name "John Doe"
git config --global user.email "johndoe@example.com"

# Directly edit configuration file (equivalent to command line configuration)
git config --global -e

# Open configuration file with Finder on macOS
open ~/.gitconfig

# Open with default editor on Linux
xdg-open ~/.gitconfig
```

Global level configuration should include your personal general settings: username, email, preferred editor, common aliases, default branch name, etc. These settings will take effect in all your projects unless overridden by repository level configuration.

### 1.3 Repository Level Configuration

Repository level configuration only takes effect for **current repository**. Configuration file is located at `.git/config` in the repository root directory. When you need to use different configurations for a project (e.g., using different emails for company and personal projects, a project needs special collaboration tools), this layer is very useful.

```bash
# View repository level configuration
git config --list --local

# Edit repository level configuration
git config --local user.name "John Doe"
git config --local user.email "john.doe@company.com"

# Directly edit configuration file
git config --local -e

# View full path of repository level configuration file
ls -la .git/config
```

An important feature of repository level configuration is that it can override any setting of global configuration. For example, if you use personal email in global configuration but need to use company email in company projects, just set it in the repository level configuration of that project. This way you don't need to frequently switch global configuration.

### 1.4 Configuration Layer Comparison

| Feature | System Level | Global Level | Repository Level |
|---------|-------------|--------------|------------------|
| Scope | All users | Current user | Current repository |
| File Location | `/etc/gitconfig` | `~/.gitconfig` | `.git/config` |
| Edit Command | `git config --system` | `git config --global` | `git config --local` |
| Priority | Lowest | Middle | Highest |
| Use Case | Enterprise/university | Personal general | Project special |

---

## 2. Must-Configure Items

### 2.1 User Identity Configuration

User identity is the most basic and important configuration. Git will record who made each commit.

```bash
# Set username
git config --global user.name "John Doe"

# Set email
git config --global user.email "johndoe@example.com"
```

**Important Notes:**
- Username should match your GitHub username
- Email must match your GitHub account email
- If using private email, GitHub provides no-reply email option

### 2.2 Editor Configuration

Git needs an editor to write commit messages, merge messages, etc.

```bash
# VS Code (recommended)
git config --global core.editor "code --wait"

# Vim
git config --global core.editor "vim"

# Nano
git config --global core.editor "nano"

# Notepad++ (Windows)
git config --global core.editor "'C:/Program Files/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"

# Sublime Text
git config --global core.editor "subl -n -w"
```

### 2.3 Default Branch Name

When creating a new repository, Git will create a default branch. Traditionally it was `main`, now GitHub uses `main`.

```bash
git config --global init.defaultBranch main
```

### 2.4 Line Ending Configuration

Different operating systems use different line ending characters. This can cause problems when collaborating.

```bash
# Windows users
git config --global core.autocrlf true

# macOS/Linux users
git config --global core.autocrlf input
```

### 2.5 Chinese Filename Configuration

Prevent Chinese filenames from displaying as garbled text.

```bash
git config --global core.quotepath false
```

---

## 3. Common Configuration Items

### 3.1 Color Configuration

Enable color output for better readability.

```bash
git config --global color.ui auto
```

### 3.2 Merge Tool Configuration

Configure merge tool for resolving conflicts.

```bash
# VS Code
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# Vim
git config --global merge.tool vimdiff

# Beyond Compare
git config --global merge.tool bc
git config --global mergetool.bc.path '/usr/local/bin/bcomp'
```

### 3.3 Diff Tool Configuration

Configure diff tool for comparing differences.

```bash
# VS Code
git config --global diff.tool vscode
git config --global difftool.vscode.cmd 'code --wait --diff $LOCAL $REMOTE'

# Vim
git config --global diff.tool vimdiff

# Beyond Compare
git config --global diff.tool bc
git config --global difftool.bc.path '/usr/local/bin/bcomp'
```

### 3.4 Alias Configuration

Aliases can save typing time and improve efficiency.

```bash
# Common aliases
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.lg "log --oneline --graph --all"
git config --global alias.last "log -1 HEAD"
git config --global alias.unstage "reset HEAD --"
git config --global alias.visual "log --graph --oneline --all --decorate"

# Complex aliases
git config --global alias.tree "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

---

## 4. Advanced Configuration

### 4.1 Proxy Configuration

If you have trouble accessing GitHub, you can configure proxy.

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

### 4.2 Large File Configuration

Configure Git LFS for handling large files.

```bash
# Install Git LFS
git lfs install

# Track large files
git lfs track "*.psd"
git lfs track "*.zip"
git lfs track "*.mp4"

# View tracked files
git lfs ls-files
```

### 4.3 Credential Storage Configuration

Configure credential storage to avoid entering password repeatedly.

```bash
# Store credentials permanently
git config --global credential.helper store

# Cache credentials for 15 minutes
git config --global credential.helper 'cache --timeout=900'

# Store credentials in macOS Keychain
git config --global credential.helper osxkeychain

# Store credentials in Windows Credential Manager
git config --global credential.helper manager
```

---

## 5. View Configuration

### 5.1 View All Configuration

```bash
git config --list
```

### 5.2 View Specific Configuration

```bash
# View username
git config user.name

# View email
git config user.email

# View editor
git config core.editor

# View merge tool
git config merge.tool
```

### 5.3 View Configuration File Location

```bash
# View system level configuration file
git config --list --system --show-origin

# View global level configuration file
git config --list --global --show-origin

# View repository level configuration file
git config --list --local --show-origin
```

---

## 6. Modify Configuration

### 6.1 Modify Configuration via Command Line

```bash
# Modify username
git config --global user.name "New Name"

# Modify email
git config --global user.email "new@email.com"

# Modify editor
git config --global core.editor "vim"
```

### 6.2 Modify Configuration via File

```bash
# Edit global configuration file
git config --global -e

# Edit repository configuration file
git config --local -e
```

### 6.3 Delete Configuration

```bash
# Delete global configuration
git config --global --unset user.name

# Delete repository configuration
git config --local --unset user.email
```

---

## 7. Common Issues

### Q: Configuration not taking effect?

**Solution:**
1. Check configuration priority
2. Verify configuration is correct
3. Restart terminal

### Q: How to reset all configurations?

**Solution:**
```bash
# Backup current configuration
cp ~/.gitconfig ~/.gitconfig.backup

# Delete configuration file
rm ~/.gitconfig

# Reconfigure
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

### Q: How to use different configurations for different projects?

**Solution:**
```bash
# In project directory
git config --local user.name "Work Name"
git config --local user.email "work@company.com"
```

---

## 8. Best Practices

1. **Use Real Information**: Username and email should match GitHub account
2. **Configure Editor**: Choose an editor you're familiar with
3. **Configure Aliases**: Set aliases for commonly used commands
4. **Configure Credential Storage**: Avoid entering password repeatedly
5. **Configure Line Endings**: Avoid line ending issues in collaboration
6. **Configure Proxy**: If having issues accessing GitHub

---

**Next: [How Git Works →](06-how-git-works.md)**