# 🪝 Git Hooks - A Complete Guide

Git Hooks are scripts (small programs) that Git executes **automatically** before or after events like `commit`, `push`, or `merge`. They live inside the hidden `.git/hooks` folder of your project.

## 🔒 Understanding Git Hooks with an Analogy

Think of Git Hooks like a **security guard at a building entrance**:

> Before you are allowed to enter (commit code), the guard checks your ID and makes sure you aren't carrying anything dangerous (bugs or bad formatting).

---

## 📂 Where Do Git Hooks Live?

```
your-project/
├── .git/
│   └── hooks/          ← Git Hooks live here!
│       ├── pre-commit
│       ├── pre-push
│       ├── commit-msg
│       └── ...
├── src/
└── README.md
```

---

## 🏷️ Types of Git Hooks

### 1. Client-Side Hooks
These run on **your local computer**.

| Hook Name | When It Runs | Common Use Case |
|-----------|--------------|-----------------|
| `pre-commit` | Before a commit is created | Linting, formatting, running tests |
| `prepare-commit-msg` | Before commit message editor opens | Auto-populate commit message |
| `commit-msg` | After commit message is entered | Validate commit message format |
| `post-commit` | After commit is created | Notifications, logging |
| `pre-push` | Before pushing to remote | Run full test suite |

### 2. Server-Side Hooks
These run on the **server** (e.g., GitHub, GitLab, Bitbucket).

| Hook Name | When It Runs | Common Use Case |
|-----------|--------------|-----------------|
| `pre-receive` | Before accepting a push | Enforce policies, reject bad commits |
| `update` | Similar to pre-receive, per branch | Branch-specific rules |
| `post-receive` | After push is accepted | Deploy, send notifications |

---

## 💡 Why Do We Use Git Hooks?

We use them to **catch mistakes early**.

### ❌ Without Hooks (The Painful Way)

```
1. You write code with a mistake (e.g., a syntax error)
          ↓
2. You commit and push it to GitHub
          ↓
3. You wait 10 minutes for the server to run tests
          ↓
4. The server fails ❌
          ↓
5. You go back, fix it, and push again
          ↓
6. Repeat... 😫
```

### ✅ With Hooks (The Smart Way)

```
1. You write code with a mistake
          ↓
2. You try to commit
          ↓
3. pre-commit hook runs and catches the error IMMEDIATELY ⚡
          ↓
4. You fix the issue before it even enters Git history
          ↓
5. Clean commit, happy team! 🎉
```

> **Result:** Saves time, keeps history clean, prevents broken code from reaching the server.

---

## 🛠️ Practical Examples

### Example 1: Simple Pre-Commit Hook (Prevent TODO Comments)

Create a file `.git/hooks/pre-commit`:

```bash
#!/bin/bash

# Check for TODO comments in staged files
if git diff --cached --name-only | xargs grep -l "TODO" 2>/dev/null; then
    echo "❌ Error: Found TODO comments in your code!"
    echo "Please resolve them before committing."
    exit 1
fi

echo "✅ No TODO comments found. Proceeding with commit..."
exit 0
```

Make it executable:
```bash
chmod +x .git/hooks/pre-commit
```

**What happens:**
- Try to commit code with `// TODO: fix this later`
- Hook stops you: `❌ Error: Found TODO comments in your code!`

---

### Example 2: Pre-Commit Hook with Code Formatting (JavaScript/Prettier)

```bash
#!/bin/bash

# Run Prettier on staged files
FILES=$(git diff --cached --name-only --diff-filter=ACM | grep -E '\.(js|jsx|ts|tsx)$')

if [ -n "$FILES" ]; then
    echo "🎨 Running Prettier on staged files..."
    
    # Format the files
    echo "$FILES" | xargs npx prettier --write
    
    # Re-add the formatted files
    echo "$FILES" | xargs git add
    
    echo "✅ Files formatted successfully!"
fi

exit 0
```

---

### Example 3: Commit Message Hook (Enforce Format)

Create a file `.git/hooks/commit-msg`:

```bash
#!/bin/bash

COMMIT_MSG_FILE=$1
COMMIT_MSG=$(cat "$COMMIT_MSG_FILE")

# Regex pattern: type(scope): message
PATTERN="^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .{1,50}"

if ! echo "$COMMIT_MSG" | grep -qE "$PATTERN"; then
    echo "❌ Error: Invalid commit message format!"
    echo ""
    echo "Expected format: type(scope): message"
    echo ""
    echo "Examples:"
    echo "  feat(auth): add login functionality"
    echo "  fix(api): resolve timeout issue"
    echo "  docs: update README"
    echo ""
    exit 1
fi

echo "✅ Commit message format is valid!"
exit 0
```

**What happens:**
- ❌ `git commit -m "fixed stuff"` → Rejected!
- ✅ `git commit -m "fix(auth): resolve login bug"` → Accepted!

---

### Example 4: Pre-Push Hook (Run Tests Before Push)

Create a file `.git/hooks/pre-push`:

```bash
#!/bin/bash

echo "🧪 Running tests before push..."

# Run your test command
npm test

if [ $? -ne 0 ]; then
    echo "❌ Tests failed! Push aborted."
    echo "Fix the failing tests and try again."
    exit 1
fi

echo "✅ All tests passed! Pushing to remote..."
exit 0
```

---

## 📦 Using Git Hooks with Husky (Modern Approach)

Managing hooks manually can be tedious. [**Husky**](https://typicode.github.io/husky/) is a popular tool that makes it easy!

### Installation

```bash
# Install Husky
npm install husky --save-dev

# Initialize Husky
npx husky init
```

### Add a Pre-Commit Hook

```bash
# Add lint-staged to pre-commit
echo "npx lint-staged" > .husky/pre-commit
```

### Configure lint-staged in `package.json`

```json
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{css,scss}": [
      "prettier --write"
    ]
  }
}
```

---

## 🎯 Quick Reference

| What You Want | Hook to Use |
|---------------|-------------|
| Lint code before commit | `pre-commit` |
| Format code automatically | `pre-commit` |
| Validate commit message | `commit-msg` |
| Run tests before push | `pre-push` |
| Send notification after commit | `post-commit` |
| Enforce rules on server | `pre-receive` |

---

## ⚠️ Common Pitfalls

1. **Forgetting to make the hook executable**
   ```bash
   chmod +x .git/hooks/pre-commit
   ```

2. **Hooks not being shared with team** - `.git/hooks` is not tracked by Git. Use tools like Husky to share hooks via `package.json`.

3. **Slow hooks** - Keep hooks fast! Long-running tasks should go in `pre-push`, not `pre-commit`.

---

## 📚 Learn More

- [Official Git Hooks Documentation](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)
- [Husky - Modern Git Hooks](https://typicode.github.io/husky/)
- [lint-staged - Run linters on staged files](https://github.com/okonet/lint-staged)

---

## 🏁 Summary

| Concept | Description |
|---------|-------------|
| **What** | Scripts that run automatically on Git events |
| **Where** | `.git/hooks/` folder |
| **Why** | Catch errors early, enforce standards, automate tasks |
| **Types** | Client-side (local) & Server-side (remote) |
| **Popular Hooks** | `pre-commit`, `commit-msg`, `pre-push` |

---

> 💡 **Pro Tip:** Start with a simple `pre-commit` hook that runs your linter. Once you see the benefits, gradually add more hooks!

Happy coding! 
