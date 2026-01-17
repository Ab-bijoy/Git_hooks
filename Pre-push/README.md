# 🚀 Git Pre-push Hooks Guide

A guide to understanding and using **pre-push hooks** for security scanning and quality control before code leaves your machine.

---

## 🤔 What is Pre-push?

**Pre-push** is a specific type of Git Hook (script) that runs automatically right after you type `git push`, but **before your code actually leaves your computer** and goes to the server (like GitHub or GitLab).

> 💡 **Think of it as the final security checkpoint at the airport.**

| Hook | Analogy |
|------|---------|
| **pre-commit** | Packing your suitcase at home. You check if you have your toothbrush and socks while closing the bag. |
| **pre-push** | The airport scanner. Even if your bag is packed, this checkpoint stops you if you have a prohibited item before you get on the plane. |

---

## ❓ Why is Pre-push Needed?

We need pre-push mainly because **once code leaves your computer, it is very hard to take back**.

### 1. 🔐 The "Once it's out, it's out" Rule (Security)

If you accidentally commit a password or an API key, it is saved in your local Git history.

- ✅ If you catch it with pre-commit, great!
- ❌ If you miss it and push it to GitHub, that password is now **compromised**

> [!CAUTION]
> Even if you delete a secret 5 seconds later, bots scanning GitHub have already stolen it, and it lives forever in the hidden Git history.

**How pre-push helps:** It scans your commits one last time before uploading. If it finds a secret, it blocks the upload completely, keeping the secret safe on your laptop.

### 2. 🛡️ Avoiding "Broken Builds" for the Team

Imagine you are working on a team:
- You make 10 commits locally. Everything looks fine.
- You push them to the shared repository.
- 💥 Suddenly, your code breaks the whole project for everyone else!

**How pre-push helps:** You can set it to run your full test suite only when you push, ensuring that whatever you send to the team is actually working code.

### 3. ⚡ Efficiency (Don't check too often)

Some checks are too slow to run every time you commit:

| Check Type | Speed | When to Run |
|------------|-------|-------------|
| Formatting, syntax | Fast ⚡ | pre-commit |
| Deep security scans, integration tests | Slow 🐢 | pre-push |

---

## 📊 Pre-commit vs Pre-push Comparison

| Feature | Pre-commit | Pre-push |
|---------|------------|----------|
| **When it runs** | Every `git commit` | Only on `git push` |
| **Frequency** | Very Often (10-20 times/day) | Less Often (2-5 times/day) |
| **Best used for** | Formatting, indentation, typos | Catching secrets, running heavy tests |
| **Analogy** | Proofreading a letter before putting it in an envelope | Checking the address before dropping it in the mailbox |

---

## 📖 Example Scenario

### ❌ Without Pre-push:

```
You finish your work and type: git push
                    ↓
        Code goes to GitHub 📤
                    ↓
    5 minutes later, your boss messages:
    "Why is the server down? Did you push 
     code that crashes the app?" 😱
                    ↓
    You realize you didn't run the tests...
```

### ✅ With Pre-push:

```
You finish your work and type: git push
                    ↓
    Terminal says: "Running pre-push hook..."
                    ↓
    ┌─────────────────────────────────┐
    │  Tests FAILED ❌                │
    │  Push is CANCELLED             │
    │  Bad code stays on your laptop │
    └─────────────────────────────────┘
                    ↓
    You fix the bug, commit, and push again
    Server stays safe! ✅
```

---

## 🛠️ Setting Up Pre-push with ggshield (Secret Detection)

### Step 1: Install ggshield

```bash
pip install ggshield
```

### Step 2: Configure Pre-commit Config

Add this to your `.pre-commit-config.yaml`:

```yaml
repos:
  # ... your other hooks (black, isort, flake8, etc.)
  
  - repo: https://github.com/gitguardian/ggshield
    rev: v1.46.0
    hooks:
      - id: ggshield-push
        language_version: python3
        stages: [pre-push]
```

### Step 3: Install the Hooks

```bash
pre-commit install --hook-type pre-push
```

---

## 📸 Real Example: Catching Secrets

### The Dangerous Code

Suppose you accidentally create a config file with AWS credentials:

```python
# config.py - DON'T DO THIS!
[default]
aws_access_key_id = YOUR_ACCESS_KEY_HERE
aws_secret_access_key = YOUR_SECRET_KEY_HERE
output = json
region = 
```

### What Happens When You Push

```bash
$ git add config.ini
$ git commit -m "add config"
$ git push origin main
```

**ggshield blocks the push:**

```
🔍 ggshield: Scanning commits...

❌ INCIDENT DETECTED!

>>> Incident 1: AWS Keys
    Validity: Valid
    Occurrences: 2
    
    config.py:2
    aws_access_key_id = YOUR_ACCESS_KEY_HERE
    
    config.py:3  
    aws_secret_access_key = YOUR_SECRET_KEY...

🚫 Push blocked! Remove the secrets before pushing.
```

### The Fix

1. Remove the secrets from your code
2. Add credentials to `.gitignore`
3. Use environment variables instead:

```python
# config.py - The SAFE way
import os

aws_access_key_id = os.environ.get("AWS_ACCESS_KEY_ID")
aws_secret_access_key = os.environ.get("AWS_SECRET_ACCESS_KEY")
region = os.environ.get("AWS_REGION", "")
```

---

## 🎯 Command Cheat Sheet

| Command | Purpose |
|---------|---------|
| `pre-commit install --hook-type pre-push` | Install pre-push hooks |
| `pre-commit run --hook-stage pre-push` | Run pre-push hooks manually |
| `ggshield auth login` | Authenticate with GitGuardian |
| `ggshield secret scan repo .` | Scan entire repository for secrets |
| `git push --no-verify` | ⚠️ Skip hooks (use with caution!) |

---

## 📁 Project Structure

```
my-project/
├── .pre-commit-config.yaml   ← Hook configuration (pre-commit + pre-push)
├── .git/
│   └── hooks/
│       ├── pre-commit        ← Runs on commit
│       └── pre-push          ← Runs on push
├── .gitignore                ← Exclude sensitive files
├── .env                      ← Store secrets here (gitignored!)
└── src/
    └── main.py
```

---

## ✨ Best Practices

| Practice | Description |
|----------|-------------|
| **Never commit secrets** | Use `.env` files and environment variables |
| **Add secrets to .gitignore** | Prevent accidental commits |
| **Use ggshield** | Automatic secret detection as a safety net |
| **Run tests on push** | Ensure code quality before sharing |
| **Don't skip hooks** | Avoid `--no-verify` unless absolutely necessary |

---

> 💡 **Pro Tip:** If ggshield blocks your push, it means it saved you from a potential security breach! Take the time to properly remove the secret rather than skipping the hook.

Happy and secure coding! 🔒🚀
