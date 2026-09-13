# Configure SSH Keys

SSH keys allow you to push code without entering password every time, more secure and convenient.

## Complete Operation Steps

### Step 1: Open Terminal

**Windows Users:**
1. Press `Win + R` to open Run window
2. Type `cmd` and press Enter
3. Open Command Prompt

**macOS Users:**
1. Press `Command + Space` to open Spotlight
2. Type `Terminal` and press Enter
3. Open Terminal

**Linux Users:**
1. Press `Ctrl + Alt + T` to open Terminal

### Step 2: Check for Existing Keys

Enter the following command in terminal and press Enter:

```bash
ls -al ~/.ssh
```

**Possible Results:**
- If it shows file list (like `id_rsa.pub`), you already have keys, can skip to Step 4
- If it shows `No such file or directory`, you don't have keys, continue to next step

### Step 3: Generate SSH Key

Enter the following command in terminal and press Enter:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

**Operation Instructions:**
1. Replace `your-email@example.com` with your GitHub email
2. After pressing Enter, it will prompt for save location, just press Enter to use default location
3. When prompted for password, can press Enter (no password) or enter a password
4. Press Enter again to confirm

**Successful Output:**
```
Your identification has been saved in /c/Users/YourUsername/.ssh/id_ed25519
Your public key has been saved in /c/Users/YourUsername/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx your-email@example.com
```

### Step 4: Copy Public Key Content

Enter the corresponding command based on your operating system:

**Windows (Git Bash):**
```bash
cat ~/.ssh/id_ed25519.pub | clip
```

**macOS:**
```bash
cat ~/.ssh/id_ed25519.pub | pbcopy
```

**Linux:**
```bash
cat ~/.ssh/id_ed25519.pub | xclip -selection clipboard
```

After execution, public key content is copied to clipboard.

### Step 5: Add Public Key to GitHub

**Operation Steps (with screenshots):**

1. Open browser, visit **github.com**
2. Click your avatar in top right corner
3. Select **Settings**
4. Click **SSH and GPG keys** in left menu
5. Click **New SSH key** button
6. In **Title** field, enter a descriptive name (e.g., "My Laptop")
7. In **Key** field, paste the public key content you copied
8. Click **Add SSH key** button

```
┌─────────────────────────────────────────────┐
│  Add new SSH key                            │
│                                             │
│  Title:                                     │
│  ┌─────────────────────────────────────┐    │
│  │ My Laptop                           │    │
│  └─────────────────────────────────────┘    │
│                                             │
│  Key:                                       │
│  ┌─────────────────────────────────────┐    │
│  │ ssh-ed25519 AAAA... your@email.com  │    │
│  └─────────────────────────────────────┘    │
│                                             │
│              [Add SSH key]                  │
└─────────────────────────────────────────────┘
```

### Step 6: Test SSH Connection

Enter the following command in terminal:

```bash
ssh -T git@github.com
```

**Possible Outputs:**

**Success:**
```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

**First Connection:**
```
The authenticity of host 'github.com (140.82.121.4)' can't be established.
ECDSA key fingerprint is SHA256:+DiYibw...
Are you sure you want to continue connecting (yes/no)?
```

Type `yes` and press Enter.

## Common Issues

### Q: Permission denied (publickey)?

**Solution:**
1. Check if SSH key is added to ssh-agent:
   ```bash
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ```

2. Check if public key is correctly added to GitHub:
   ```bash
   ssh-add -l
   ```

3. Verify SSH configuration:
   ```bash
   ssh -vT git@github.com
   ```

### Q: Host key verification failed?

**Solution:**
```bash
ssh-keyscan github.com >> ~/.ssh/known_hosts
```

### Q: How to use multiple SSH keys?

**Solution:**
Create or edit `~/.ssh/config` file:

```bash
# Personal GitHub
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal

# Work GitHub
Host github-work.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
```

Then use different hosts:
```bash
# Personal
git clone git@github.com:username/repo.git

# Work
git clone git@github-work.com:company/repo.git
```

### Q: How to change SSH key password?

**Solution:**
```bash
ssh-keygen -p -f ~/.ssh/id_ed25519
```

## SSH Agent Management

### Start SSH Agent

```bash
eval "$(ssh-agent -s)"
```

### Add Key to Agent

```bash
ssh-add ~/.ssh/id_ed25519
```

### List Keys in Agent

```bash
ssh-add -l
```

### Remove All Keys from Agent

```bash
ssh-add -D
```

## Best Practices

1. **Use Ed25519**: More secure and faster than RSA
2. **Set Password**: Add password to SSH key for security
3. **Use SSH Agent**: Avoid entering password repeatedly
4. **Backup Keys**: Backup SSH keys to secure location
5. **Different Keys for Different Purposes**: Use separate keys for personal and work

## Related Resources

- [GitHub SSH Documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
- [SSH Key Generation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

---

**Next: [Git Basic Configuration →](05-git-config.md)**