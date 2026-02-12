# Switching Between GitHub Accounts

Quick guide for switching between multiple GitHub accounts in your terminal.

## Prerequisites

- GitHub CLI installed: `brew install gh`
- Multiple accounts logged in via `gh auth login`

---

## TL;DR - What You Need to Do

**Every time you switch accounts:**
- ✅ Run `gh auth switch` (select the account)

**One time per repository:**
- ✅ Run `git config user.name` and `git config user.email` (persists)

**For HelioStack specifically:**
- ✅ Git config already set - just run `gh auth switch` when needed

---

## Quick Switch Guide

### 1. Switch GitHub Account (Authentication) - Every Time

```bash
# Check which account you're currently using
gh auth status

# Switch to another logged-in account
gh auth switch

# Or login to a new account
gh auth login
```

**What this does:** Changes which GitHub account is used for push/pull/API operations.

---

### 2. Verify Setup

```bash
# Check GitHub authentication
gh auth status

# Check git commit details
git config user.name
git config user.email

# Should match the account you switched to
```
---

## Automatic Switching by Directory

If you frequently work with multiple accounts, you can auto-switch git config based on directory.

### Setup Once

Edit `~/.gitconfig`:

```bash
nano ~/.gitconfig
```

Add:

```ini
# Default account
[user]
    name = Your Name
    email = your-main-email@example.com

# Auto-switch
[includeIf "gitdir:/Users/dir/"]
    path = ~/.gitconfig-username
```

Create `~/.gitconfig-username`:

```bash
nano ~/.gitconfig-username
```

Add:

```ini
[user]
    name = username
    email = username@users.noreply.github.com
```

### Result

Now git will automatically use the correct email when you're in HelioStack repositories!

```bash
cd ~/HelioStack/HelioStack
git config user.email
# Shows: username@users.noreply.github.com

cd ~/other-project
git config user.email
# Shows: your-main-email@example.com
```

You still need to run `gh auth switch` manually, but git config is automatic.

---