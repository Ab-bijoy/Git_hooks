# 🪝 Git Hooks - Complete Guide

A comprehensive repository demonstrating **Git Hooks** for automating code quality, security scanning, and deployment workflows.

---

## 🤔 What Are Git Hooks?

Git Hooks are **scripts that run automatically** at specific points in the Git workflow. They act as checkpoints that can:

- ✅ Format your code before committing
- 🔐 Scan for secrets before pushing
- 🚫 Block bad code from reaching the server
- 🚀 Trigger deployments after code is accepted

| Type | Location | Purpose |
|------|----------|---------|
| **Client-side** | Your laptop | Run before/after commits, pushes |
| **Server-side** | GitHub/GitLab server | Enforce rules, trigger CI/CD |

---

## 📁 Repository Structure

```
Git_hooks/
├── 📄 README.md                    ← You are here!
├── 📄 .pre-commit-config.yaml      ← Hook configuration
├── 📄 .gitignore                   ← Exclude sensitive files
├── 📄 .env                         ← Environment variables (gitignored)
│
├── 📂 Pre-commit/                  ← Client-side: runs on every commit
│   └── README.md                   ← Guide to pre-commit hooks
│
├── 📂 Pre-push/                    ← Client-side: runs before push
│   └── README.md                   ← Guide to pre-push hooks
│
├── 📂 Pre-receive/                 ← Server-side: runs on remote
│   └── README.md                   ← Guide to server-side hooks
│
└── 📂 test/                        ← Example Python files
    ├── app.py                      ← Sample code for hook demos
    └── config.ini                  ← Configuration file
```

---

## 🔄 The Three Stages of Git Hooks

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           YOUR LAPTOP                                       │
│                                                                             │
│   ┌─────────────┐         ┌─────────────┐         ┌─────────────┐          │
│   │   COMMIT    │         │    PUSH     │         │   SERVER    │          │
│   │             │  ────►  │             │  ────►  │  RECEIVES   │          │
│   │ pre-commit  │         │  pre-push   │         │ pre-receive │          │
│   │  runs here  │         │  runs here  │         │  runs here  │          │
│   └─────────────┘         └─────────────┘         └─────────────┘          │
│                                                          │                  │
│   📝 Formats code         🔐 Scans for secrets    🚫 Blocks bad code       │
│   📚 Sorts imports        🧪 Runs full tests      🚀 Deploys if OK         │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 📚 Documentation

Explore each hook type in detail:

| Guide | Description | Key Tools |
|-------|-------------|-----------|
| [**Pre-commit**](Pre-commit/README.md) | Code formatting & linting on every commit | Black, isort, flake8, Bandit |
| [**Pre-push**](Pre-push/README.md) | Security scanning before code leaves your machine | ggshield (GitGuardian) |
| [**Pre-receive**](Pre-receive/README.md) | Server-side gatekeeping & deployment automation | Custom scripts |

---

## 🛠️ Tools Used

### Code Quality (Pre-commit)

| Tool | Purpose | What It Does |
|------|---------|--------------|
| **Black** | Auto-formatter | Rewrites code to follow PEP 8 |
| **isort** | Import sorter | Organizes imports by type |
| **flake8** | Linter | Catches unused variables, imports |
| **Bandit** | Security scanner | Finds hardcoded passwords, dangerous functions |

### Security (Pre-push)

| Tool | Purpose | What It Does |
|------|---------|--------------|
| **ggshield** | Secret detection | Blocks API keys, passwords from being pushed |

---

## ⚡ Quick Start

### 1. Install Dependencies

```bash
pip install pre-commit black isort flake8 bandit ggshield
```

### 2. Install Hooks

```bash
# Install pre-commit hooks (runs on every commit)
pre-commit install

# Install pre-push hooks (runs before push)
pre-commit install --hook-type pre-push
```

### 3. Test the Hooks

```bash
# Run all hooks manually on all files
pre-commit run --all-files
```

---

## 📋 Current Configuration

This repository uses the following `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/psf/black
    rev: 23.12.1
    hooks:
      - id: black

  - repo: https://github.com/pycqa/isort
    rev: 5.13.2
    hooks:
      - id: isort

  - repo: https://github.com/pycqa/flake8
    rev: 7.0.0
    hooks:
      - id: flake8

  - repo: https://github.com/PyCQA/bandit
    rev: 1.7.7
    hooks:
      - id: bandit
        args: ["-iii", "-ll"]  # Skip low severity issues

  - repo: https://github.com/gitguardian/ggshield
    rev: v1.46.0
    hooks:
      - id: ggshield-push
        language_version: python3
        stages: [pre-push]
```

---

## 🎯 Command Cheat Sheet

| Command | Purpose |
|---------|---------|
| `pre-commit install` | Install pre-commit hooks |
| `pre-commit install --hook-type pre-push` | Install pre-push hooks |
| `pre-commit run --all-files` | Run all hooks on all files |
| `pre-commit autoupdate` | Update hook versions |
| `black .` | Format all Python files |
| `isort .` | Sort all imports |
| `flake8 .` | Lint all files |
| `bandit -r .` | Security scan all files |
| `ggshield secret scan repo .` | Scan for secrets |

---

## 🔒 Security Best Practices

> [!CAUTION]
> Never commit secrets like API keys, passwords, or tokens to Git!

| ✅ Do | ❌ Don't |
|------|---------|
| Use `.env` files for secrets | Hardcode passwords in code |
| Add `.env` to `.gitignore` | Push credentials to GitHub |
| Use environment variables | Store secrets in config files |
| Let ggshield scan your pushes | Skip security hooks |

---

## 📖 Learning Path

1. **Start with Pre-commit** → [Pre-commit/README.md](Pre-commit/README.md)
   - Understand automated code formatting
   - Set up Black, isort, flake8, and Bandit

2. **Move to Pre-push** → [Pre-push/README.md](Pre-push/README.md)
   - Learn about secret detection
   - Configure ggshield for security scanning

3. **Explore Server-side** → [Pre-receive/README.md](Pre-receive/README.md)
   - Understand how servers enforce rules
   - Learn about deployment automation

---

## ✨ Why Use Git Hooks?

| Benefit | Description |
|---------|-------------|
| **Automation** | No manual formatting or checking |
| **Consistency** | Same standards for everyone on the team |
| **Security** | Catch secrets before they leak |
| **Quality** | Only clean, tested code reaches the repository |
| **Speed** | Fast feedback loop on code issues |

---

> 💡 **Pro Tip:** Run `pre-commit run --all-files` after cloning this repo to ensure all hooks are working correctly!

Happy coding! 🚀
