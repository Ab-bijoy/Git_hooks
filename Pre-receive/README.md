# Git Receive

There isn't a command called `git receive` that you type. Instead, **"Receive"** is a phase that happens on the server (the remote computer) when you run `git push`.

It relies on **Server-Side Hooks** (scripts on the server) to manage incoming code. The two most common ones are:

| Hook | Description |
|------|-------------|
| `pre-receive` | Runs **before** the code is accepted. It acts as a **filter or gatekeeper**. |
| `post-receive` | Runs **after** the code is accepted. It acts as a **notifier** (like sending an email or deploying a website). |

---

## Why Do We Need It?

We need "Receive" hooks to **protect the main project code**. Since multiple people work on the same project, we can't trust everyone to always do the right thing.

- **To Enforce Rules:** Prevent developers from deleting the history or force-pushing to the `main` branch.
- **To Block Bad Data:** Stop people from uploading huge files (like videos) or sensitive data (like passwords).
- **To Automate Deployment:** Automatically update a live website the moment new code is successfully pushed.

---

## Example Scenario

Imagine you are the team lead. You want to ensure no one pushes a file named `passwords.txt` to the server.

You would write a `pre-receive` script on the server.

---

## Step-by-Step Explanation

Here is exactly what happens when you use these hooks:

### Step 1: The Push (User Side)

You are on your laptop. You type:

```bash
git push origin main
```

Your computer sends the data to the server.

### Step 2: The Check (`pre-receive`)

Before the server saves your changes, it runs the `pre-receive` script.

- **The Script Checks:** "Does this new code contain a file called `passwords.txt`?"
- **If YES:** The script exits with an error. The server says, **"Push rejected!"** and your code is not saved.
- **If NO:** The script gives the **"Green Light."**

### Step 3: The Update

Since the gatekeeper (`pre-receive`) said yes, the server actually updates the `main` branch with your new code.

### Step 4: The Notification (`post-receive`)

Now that the code is safe and saved, the `post-receive` script runs.

It might send a Slack message to the team: *"New code received from Anindo!"*

---

## Flow Diagram

```
┌─────────────────┐
│   git push      │  (Your laptop)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  pre-receive    │  ──► Reject if rules violated
│   (Gatekeeper)  │
└────────┬────────┘
         │ ✓ Passed
         ▼
┌─────────────────┐
│  Update refs    │  (Server saves your code)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  post-receive   │  ──► Send notifications, deploy, etc.
│   (Notifier)    │
└─────────────────┘
```
