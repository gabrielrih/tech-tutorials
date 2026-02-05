# Configuring SSH Keys for Multiple Git Accounts

A quick guide to set up and manage multiple SSH keys for different GitHub and Azure DevOps accounts on the same machine.

## Table of Contents
- [Why Multiple SSH Keys?](#why-multiple-ssh-keys)
- [Step 1: Generate SSH Keys](#step-1-generate-ssh-keys)
- [Step 2: Configure SSH Config File](#step-2-configure-ssh-config-file)
- [Step 3: Add Public Keys to Git Providers](#step-3-add-public-keys-to-git-providers)
- [Step 4: Test SSH Connections](#step-4-test-ssh-connections)
- [Step 5: Configure Git User Settings](#step-5-configure-git-user-settings)
- [Step 6: Clone Repositories](#step-6-clone-repositories)

---

## Why Multiple SSH Keys?

When working with multiple Git accounts (personal and work), you need separate SSH keys to authenticate with different accounts on GitHub and Azure DevOps. This setup allows you to:
- Keep personal and work projects separate
- Use different email addresses for commits
- Avoid authentication conflicts

On this tutorial, we'll set up three different accounts:
- A professional account on Azure DevOps
- Another professional account on GitHub
- And a personal account on GitHub

---

## Step 1: Generate SSH Keys

Navigate to your SSH directory and generate keys for each account:

```bash
cd C:\Users\my.user\.ssh\
```

### GitHub Personal Account
```bash
ssh-keygen -t ed25519 -f id_github_personal -C "my.personal.email@provider.com"
```

### GitHub Professional Account
```bash
ssh-keygen -t ed25519 -f id_github_professional -C "my.professional.email@provider.com"
```

### Azure DevOps Professional Account
```bash
ssh-keygen -t rsa-sha2-256 -f id_azuredevops_professional -C "my.professional.email@provider.com"
```

> **Note:** When prompted, you can press Enter to skip setting a passphrase (generate keys without password). This is convenient but less secure.

After generation, you'll have the following files:
- `id_github_personal` and `id_github_personal.pub`
- `id_github_professional` and `id_github_professional.pub`
- `id_azuredevops_professional` and `id_azuredevops_professional.pub`

---

## Step 2: Configure SSH Config File

Create or edit the SSH config file to define host aliases:

**Location:**
- Windows: `C:\Users\my.user\.ssh\config`
- Linux/Mac: `~/.ssh/config`

**Content:**
```
# GitHub Personal Account
Host github.com
    HostName github.com
    User git
    IdentityFile C:\Users\my.user\.ssh\id_github_personal

# GitHub Professional Account
Host github-work.com
    HostName github.com
    User git
    IdentityFile C:\Users\my.user\.ssh\id_github_professional

# Azure DevOps Professional Account
Host ssh.dev.azure.com
    HostName ssh.dev.azure.com
    User git
    IdentityFile C:\Users\gabriel.richter\.ssh\id_azuredevops_professional
```

> Note that for GitHub Professional Account what are using an alias for the host `github-work.com` instead of `github.com`

---

## Step 3: Add Public Keys to Git Providers

Copy the public key content and add it to your Git provider accounts.

### View Public Key Content

**Windows:**
```bash
type C:\Users\gabriel.richter\.ssh\id_github_personal.pub
type C:\Users\gabriel.richter\.ssh\id_github_professional.pub
type C:\Users\gabriel.richter\.ssh\id_azuredevops_professional.pub
```

**Linux/Mac:**
```bash
cat ~/.ssh/id_github_personal.pub
cat ~/.ssh/id_github_abinbev.pub
cat ~/.ssh/id_azuredevops_ambev.pub
```

### Add to GitHub
1. Go to **Settings** → **SSH and GPG keys** → **New SSH key**
2. Paste the public key content
3. Give it a descriptive title (e.g., "Personal Laptop" or "Work Desktop")
4. Click **Add SSH key**

Repeat for both personal and work GitHub accounts.

### Add to Azure DevOps
1. Go to **User Settings** (top right) → **SSH public keys**
2. Click **+ New Key**
3. Paste the public key content
4. Give it a name
5. Click **Add**

---

## Step 4: Test SSH Connections

At this point, you should be able to connect to the Git provider through SSH.

Testing personal GitHub:

```bash
ssh -T git@github.com
```
Expected output: `Hi <your-username>! You've successfully authenticated...`

Testing professional GitHub:

```bash
ssh -T git@github-work.com
```
Expected output: `Hi <your-work-username>! You've successfully authenticated...`

Testing professional Azure DevOps:

```bash
ssh -T git@ssh.dev.azure.com
```
Expected output: `remote: Shell access is not supported.`

You can also use the verbose mode for troubleshooting:

```bash
ssh -vT git@github.com
ssh -vT git@github-work.com
ssh -vT git@ssh.dev.azure.com
```

---

## Step 5: Configure Git User Settings

Set up conditional Git configurations to use different email addresses based on the project directory.

Let's imagine you'll clone all personal repos on a folder and all the profesional repos in another folder. Something like that:

```
C:.
├───git-projects
    ├───personal
    ├───professional
```

### Global Git Config

Edit `C:\Users\gabriel.richter\.gitconfig` (or `~/.gitconfig` on Linux/Mac):

```ini
[user]
    name = My Name
    email = my.professional.email@provider.com

[includeIf "gitdir:C:/git-projects/personal/"]
    path = C:/Users/my.user/.gitconfig-personal
```

### Personal Git Config

Create `C:\Users\my.user\.gitconfig-personal` (or `~/.gitconfig-personal`):

```ini
[user]
    name = My Name
    email = my.personal.email@provider.com
```

This setup ensures:
- Work email is used by default
- But, every repository inside `C:/git-projects/github/personal/` will use the personal email address.

### Verify Configuration

Check which email is being used in a specific repository:
```bash
git config --show-origin user.email
```

---

## Step 6: Clone Repositories

Use the appropriate host alias when cloning repositories.

### Personal GitHub Repository
```bash
git clone git@github.com:username/repo-name.git
```

### Work GitHub Repository
```bash
git clone git@github-work.com:company/repo-name.git
```

> Note the different host `github-work.com`

### Azure DevOps Repository
```bash
git clone git@ssh.dev.azure.com:v3/ORGANIZATION/PROJECT/REPOSITORY
```
