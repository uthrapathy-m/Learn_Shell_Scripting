# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 01 — Intro to Bash Shell Scripting
> **Episode:** 3. Shell and Scripts
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

This episode builds the **conceptual foundation** for everything in the course. You'll understand exactly what a shell is, how it fits between you and the operating system, the different shells available, and — most importantly — what a **shell script** is and why it's such a powerful tool for automation.

---

## 🧠 Theory

### 1. What is a Shell?

A **shell** is a program that acts as a **bridge between the user and the operating system kernel**.

```
You (User)
    ↓  type a command
  Shell  ← reads commands from keyboard OR from a file
    ↓  checks syntax → if valid, passes to kernel
  Kernel (OS Core)
    ↓  executes the command
  Hardware / System Resources
```

The shell has two important roles:

| Role | Description |
|---|---|
| **Command Interpreter** | Reads what you type, checks if it's syntactically correct, and passes it to the kernel |
| **Scripting Environment** | Executes commands from a file (a script) — enabling automation |

> 💡 **Analogy:** Think of the kernel as the engine of a car, and the shell as the steering wheel and dashboard — the interface that lets you control the engine without needing to know how it works internally.

---

### 2. When Does the Shell Start?

The shell **starts automatically** when you:
- Log in to a Linux system
- Open a terminal window

---

### 3. How to Check Your Current Shell

```bash
# Method 1: Shows the name of the current shell process
echo $0

# Method 2: Shows the default login shell for your user
echo $SHELL
```

**Example output:**
```
/bin/bash
```

---

### 4. Types of Shells Available

Bash is the most common, but it's not the only shell. Here's a quick overview:

| Shell | Name | Notes |
|---|---|---|
| `sh` | Bourne Shell | The original Unix shell, developed by Stephen Bourne at AT&T |
| `bash` | Bourne Again Shell | Improved `sh`; default on most Linux distros |
| `dash` | Debian Almquist Shell | Lightweight, faster than bash; used for system scripts |
| `csh` | C Shell | Syntax inspired by C language |
| `zsh` | Z Shell | Feature-rich; default on macOS since Catalina |
| `fish` | Friendly Interactive Shell | User-friendly, modern syntax |

> 📌 **Bash is the default shell** on most major Linux distributions: Ubuntu, Mint, MX Linux, Debian, Red Hat, Fedora, Kali Linux, and more.

> 🏛️ **History note:** Bash is named "Bourne **Again** Shell" as a tribute to Stephen Bourne, who wrote the original `sh` shell at AT&T Bell Labs.

---

### 5. Listing All Available Shells

```bash
cat /etc/shells
```

**Example output:**
```
/bin/sh
/bin/bash
/usr/bin/bash
/bin/dash
/usr/bin/dash
/bin/zsh
/usr/bin/zsh
```

---

### 6. Changing a User's Default Shell

```bash
chsh -s /path/to/new/shell
```

**Example — change to zsh:**
```bash
chsh -s /bin/zsh
```

> ⚠️ You'll need to **log out and back in** (or restart the terminal) for this to take effect.

---

### 7. Viewing a User's Default Shell

Every user's default shell is stored in `/etc/passwd` — in the **last column** of each user's entry:

```bash
cat /etc/passwd
```

**Example line:**
```
student:x:1001:1001::/home/student:/bin/bash
                                        ↑
                               default shell = bash
```

> 🔍 System users (like `daemon`, `www-data`) typically show `/usr/sbin/nologin` — meaning they have **no valid interactive shell** (they can't log in).

---

### 8. What is a Shell Script?

A **shell script** is an **executable text file** that contains:
- Shell commands
- Programming structures: variables, parameters, functions, loops, conditionals

These are executed **sequentially** — line by line, from top to bottom.

```
Shell Script = Text File + Shell Commands + Logic + Execute Permission
```

**A simple example of what a script looks like:**
```bash
#!/bin/bash
# This is a shell script

echo "Starting backup..."
cp -r /home/user/documents /backup/
echo "Backup complete!"
```

---

### 9. Why Use Shell Scripts? — The Power of Automation

#### ✅ Repetitive Task Automation
> If you find yourself doing the same task over and over — **that's a script waiting to be written.**

Instead of running 10 commands manually every morning, write a script that runs them all with a single command.

#### ✅ Create Your Own Power Tools
Scripts are custom tools built exactly for your needs — no third-party software required.

#### ✅ Reduce Human Error
A well-tested script runs the same way **every single time** — no typos, no forgotten steps.

#### ✅ Abstraction for Other Users
A junior sysadmin doesn't need to know how a SQL backup works — they just run:
```bash
./backup_database.sh
```
The script handles all the complexity internally.

---

### 10. Real-World Use Cases for Shell Scripts

| Use Case | Example |
|---|---|
| **System Monitoring** | Check CPU, RAM, disk usage and alert when thresholds are breached |
| **Data Backup & Restore** | Automatically back up files/databases on a schedule |
| **Email Alerts** | Send an email when a service goes down or disk is nearly full |
| **User Administration** | Bulk-create users, set passwords, assign groups |
| **Security Auditing** | Scan for failed login attempts, open ports, file permission issues |
| **Application Deployment** | Automate build, test, and deploy pipelines |
| **Log Management** | Rotate, compress, and archive log files automatically |

---

### 11. Shell vs Shell Script — The Key Difference

| | Shell | Shell Script |
|---|---|---|
| **What it is** | A running program (process) | A text file with commands |
| **How you use it** | Interactive — type commands live | Non-interactive — commands stored in a file |
| **Execution** | One command at a time | Many commands, all at once |
| **Persistence** | Commands are lost after the session | Script file stays on disk, reusable |

---

## 🔑 Key Takeaways

> 📝 Save these — they form the conceptual backbone of the entire course.

- A **shell** is a **command interpreter** — the interface between you and the OS kernel.
- The shell starts when you **open a terminal or log in**.
- Use `echo $0` or `echo $SHELL` to see **which shell you're running**.
- **Bash** (Bourne Again Shell) is the default on most Linux distros — named as a tribute to the original `sh` by Stephen Bourne.
- Use `cat /etc/shells` to see **all valid shells** on the system.
- Use `chsh -s /bin/zsh` to **change your default shell**.
- User default shells are stored in `/etc/passwd` — last column.
- A **shell script** = executable text file with shell commands + programming logic.
- Scripts execute **sequentially** — line by line.
- The biggest reason to write scripts: **automate repetitive tasks** and **reduce human error**.

---

## 💡 Tips & Tricks

**🔹 `$0` vs `$SHELL` — what's the difference?**
```bash
echo $0      # name of the CURRENT running shell/script
echo $SHELL  # your DEFAULT login shell (set in /etc/passwd)
```
They may differ if you've switched shells temporarily.

**🔹 Check a specific user's default shell:**
```bash
grep username /etc/passwd
# or using getent:
getent passwd username
```

**🔹 `nologin` in `/etc/passwd` means:**
That user account exists for system services but **cannot open an interactive shell** — a security measure to prevent login.

**🔹 Scripts need execute permission to run:**
```bash
chmod +x script.sh   # give execute permission
./script.sh          # run it
```
Without `chmod +x`, you'll get a "Permission denied" error.

**🔹 You can also run a script without making it executable:**
```bash
bash script.sh    # explicitly use bash to run it
```
This bypasses the need for execute permission.

**🔹 VSCode Tip — Run scripts directly from the integrated terminal:**
- Open your script in the editor
- Open the terminal panel (`Ctrl + `` ` ``)
- Run `bash script.sh` or `./script.sh`

---

## 🏋️ Practice Exercises

> Work through these on your Linux PC in VSCode. Each exercise reinforces a concept from the episode.

---

### Exercise 1 — Identify Your Shell

**Goal:** Confirm which shell you're using and explore the shells available on your system.

```bash
# Check your current shell process
echo $0

# Check your default login shell
echo $SHELL

# List all valid shells installed
cat /etc/shells
```

**Questions:**
- Are `$0` and `$SHELL` showing the same shell?
- How many shells are installed on your system?

---

### Exercise 2 — Inspect `/etc/passwd`

**Goal:** Understand how user shells are stored and identify system vs regular users.

```bash
# View all user entries
cat /etc/passwd

# Filter just your own user entry (replace 'yourusername')
grep yourusername /etc/passwd

# Or use whoami to get your username automatically
grep $(whoami) /etc/passwd
```

**What to observe:**
- Find the last column — that's your shell
- Find at least 2 system users that have `/usr/sbin/nologin` or `/bin/false`

---

### Exercise 3 — Explore Available Shells

**Goal:** See all installed shells and understand the difference.

```bash
# List all valid login shells
cat /etc/shells

# Check if zsh is installed
which zsh

# Temporarily switch to sh (to feel the difference)
sh

# Try some commands, then exit back to bash
echo "I'm in sh now"
exit

# Confirm you're back in bash
echo $0
```

---

### Exercise 4 — Write Your First Real Shell Script

**Goal:** Create a script that demonstrates the "sequential execution" nature of shell scripts.

**Step 1:** Create the file
```bash
mkdir -p ~/bash-practice
cd ~/bash-practice
touch system_info.sh
```

**Step 2:** Open in VSCode and write:
```bash
#!/bin/bash
# Script: system_info.sh
# Purpose: Display basic system information

echo "==============================="
echo "       SYSTEM INFORMATION      "
echo "==============================="
echo ""
echo "Hostname     : $(hostname)"
echo "Current User : $(whoami)"
echo "Shell        : $SHELL"
echo "Date & Time  : $(date '+%Y-%m-%d %H:%M:%S')"
echo "Uptime       : $(uptime -p)"
echo "Disk Usage   : $(df -h / | awk 'NR==2 {print $3 " used of " $2}')"
echo "Memory       : $(free -h | awk 'NR==2 {print $3 " used of " $2}')"
echo ""
echo "==============================="
```

**Step 3:** Make it executable and run it
```bash
chmod +x system_info.sh
./system_info.sh
```

**Alternative — run without execute permission:**
```bash
bash system_info.sh
```

> 🎉 You've just written a practical, real-world shell script!

---

### Exercise 5 — Understand Sequential Execution

**Goal:** See that scripts execute line by line, in order.

```bash
# Create a new file
touch sequence_demo.sh
```

Write this inside:
```bash
#!/bin/bash

echo "Step 1: Starting..."
sleep 1
echo "Step 2: Processing..."
sleep 1
echo "Step 3: Done!"
```

Run it:
```bash
bash sequence_demo.sh
```

**Observe:** Each line runs one after the other, with a 1-second pause between them. This is **sequential execution**.

---

### Exercise 6 — The Permission Experiment

**Goal:** Understand why `chmod +x` is needed.

```bash
# Create a script
echo '#!/bin/bash
echo "Hello from script!"' > test_perm.sh

# Try running WITHOUT execute permission
./test_perm.sh     # → should give: Permission denied

# Check current permissions
ls -l test_perm.sh

# Add execute permission
chmod +x test_perm.sh

# Check permissions again — notice the 'x'
ls -l test_perm.sh

# Now run it
./test_perm.sh     # → should work now ✅
```

---

### 🧠 Quiz — Test Yourself

1. What is a shell's primary role in the operating system?
2. What is the difference between `$0` and `$SHELL`?
3. Name three shells other than Bash.
4. Which file on Linux lists all valid login shells?
5. Where is a user's default shell stored on the system?
6. What does `nologin` mean in `/etc/passwd`?
7. What command changes your default shell to zsh?
8. What is a shell script?
9. Name three real-world use cases for shell scripts.
10. What's the difference between `./script.sh` and `bash script.sh`?

---

## 📎 Quick Reference Card

```
COMMAND                              WHAT IT DOES
────────────────────────────────────────────────────────────────
echo $0                              Show current shell process name
echo $SHELL                          Show default login shell
cat /etc/shells                      List all valid shells on the system
cat /etc/passwd                      View all users and their default shells
grep $(whoami) /etc/passwd           View your own user entry
chsh -s /bin/zsh                     Change default shell to zsh

chmod +x script.sh                   Give execute permission to a script
./script.sh                          Run an executable script
bash script.sh                       Run script without execute permission

SHELL COMPARISON
────────────────────────────────────────────────────────────────
sh       → Original Bourne Shell (minimal)
bash     → Bourne Again Shell (default on most Linux)
dash     → Lightweight, fast (used for system scripts)
zsh      → Feature-rich (default on macOS)
csh      → C-like syntax
fish     → User-friendly, modern
```

---

## 🗂️ Notion Tags (suggested)

`bash` `shell` `shell-scripting` `linux` `kernel` `scripts` `automation` `ZTM` `module-2` `section-01`

---

*← Previous: Module 2 | Section 01 — Bash Aliases*
*→ Next Episode: Module 2 | Section 01 — First Script (Hands-on)*
