# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 1. Introduction
> **Episode:** 1. Bash Scripting Learn Shell Scripting
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

This is the **course introduction** — it sets the stage for what Bash is, why it matters, and what you'll learn throughout the course. No code yet, but the concepts here form the **mental foundation** for everything ahead.

---

## 🧠 Theory

### 1. What is the Command Line & Shell?

Most people interact with computers using a **GUI (Graphical User Interface)** — clicking icons, opening folders, dragging files. It's intuitive, but it's **not the fastest or most powerful** way to control a computer.

The **command line** is a text-based interface where you type commands directly to the operating system. The **shell** is the program that reads those commands and executes them.

```
User  →  types command  →  Shell  →  talks to  →  Operating System (Kernel)
```

> 💡 **Think of it this way:** The GUI is like driving with automatic transmission. The command line is manual — more control, more power, higher ceiling.

---

### 2. What is Bash?

| Property | Detail |
|---|---|
| **Full Name** | **B**ourne **A**gain **Sh**ell |
| **Type** | Command-line interface (CLI) shell program |
| **Primary Use** | Control and automate a computer's operating system |
| **Primary OS** | Linux & macOS (also available on Windows via WSL) |
| **Created as** | An improved replacement for the original Bourne Shell (`sh`) |

**Bash is:**
- A **shell** — it interprets and executes your commands
- A **scripting language** — you can write programs (scripts) with it
- The **default shell** on most Linux distributions and older macOS

---

### 3. Why Learn Bash?

#### ✅ Automation
If you do something repetitive on your computer — renaming hundreds of files, backing up data, restarting services — Bash can automate it. Write the script once, run it forever.

#### ✅ Cross-Language Usefulness
Even if your primary language is **Python, JavaScript, Go**, etc., those languages often need to communicate with the OS. Bash commands are used inside many languages and CI/CD pipelines.

#### ✅ Career Value
Bash is **essential** for:
- 🔧 **DevOps Engineers** — deployment pipelines, server management
- 🖥️ **Sysadmins** — monitoring, user management, backups
- 👨‍💻 **Developers** — build scripts, environment setup, automation

---

### 4. What Will This Course Cover?

The course goes from **zero → expert level**, covering:

| Topic | What You'll Learn |
|---|---|
| Bash Programming Language | Syntax, structure, how scripts work |
| Shell Scripting | Writing and executing `.sh` scripts |
| Variables | Storing and using data |
| Shell Expansions | Powerful string and command substitutions |
| Linux Server Monitoring | Checking system health via scripts |
| Data Backup & Restore | Automated backup scripts |
| Data Security | File permissions, secure scripting |
| Flow Control | `if`, `else`, `case` statements |
| Loops | `for`, `while`, `until` loops |
| Functions | Reusable blocks of code |

---

## 🔑 Key Takeaways

> 📝 Save these in your notes — they're the "why" behind everything you'll learn.

- **GUI = visual, easy, limited. CLI = text, fast, powerful.**
- **Bash = Bourne Again Shell** — a shell AND a scripting language.
- Bash runs primarily on **Linux and macOS**.
- Bash lets you **automate repetitive tasks** — saving you hours of manual work.
- Even non-Bash developers benefit — many tools and languages **call Bash under the hood**.
- Knowing Bash is a **core skill** for DevOps, SysAdmin, and backend development roles.

---

## 💡 Tips & Tricks

> Things worth highlighting early, even before writing your first script.

**🔹 Bash vs Shell — what's the difference?**
A "shell" is any command interpreter (sh, zsh, fish, bash). **Bash is one type of shell** — the most widely used on Linux. When people say "shell scripting," they usually mean Bash.

**🔹 Where does Bash live on your system?**
On Linux/macOS, Bash is located at:
```bash
/bin/bash
```
You can verify this by running in your terminal:
```bash
which bash
```

**🔹 Check your current shell:**
```bash
echo $SHELL
```

**🔹 Check the Bash version installed:**
```bash
bash --version
```

**🔹 VSCode Setup Tip:**
- Install the extension **"Bash IDE"** or **"ShellCheck"** for linting and syntax highlighting.
- Set your default terminal in VSCode to **bash**: open Command Palette → `Terminal: Select Default Profile` → select `bash`.

---

## 🏋️ Practice Exercises

> These are hands-on tasks to reinforce today's concepts. Open your terminal in VSCode and try each one.

---

### Exercise 1 — Verify Bash is Installed

**Goal:** Confirm Bash is available and check its version on your Linux system.

```bash
# Step 1: Find where bash is installed
which bash

# Step 2: Check the version
bash --version

# Step 3: See your current default shell
echo $SHELL
```

**Expected Output (example):**
```
/bin/bash
GNU bash, version 5.x.x
/bin/bash
```

---

### Exercise 2 — Explore Available Shells

**Goal:** See all shells installed on your Linux system.

```bash
cat /etc/shells
```

**What to notice:** You'll likely see `/bin/sh`, `/bin/bash`, `/bin/zsh`, etc. Each is a different shell. Bash is the one we'll master.

---

### Exercise 3 — Your First Shell Command

**Goal:** Talk to your computer via the command line — feel the difference from clicking.

```bash
# Print a message to the screen
echo "Hello, Shell World!"

# Show today's date and time
date

# Show who is logged in
whoami

# Show current working directory
pwd
```

Try each command one by one. Notice how **instantly** the computer responds — no clicking needed.

---

### Exercise 4 — Write & Run Your First Bash Script

**Goal:** Create a simple `.sh` script file and execute it in VSCode terminal.

**Step 1:** Create a new file called `hello.sh`
```bash
# In your terminal, navigate to a folder (e.g., Desktop or a new 'bash-practice' folder)
mkdir ~/bash-practice
cd ~/bash-practice

# Create the file
touch hello.sh
```

**Step 2:** Open `hello.sh` in VSCode and type:
```bash
#!/bin/bash

# My first Bash script
echo "Hello from Bash!"
echo "Today is: $(date)"
echo "I am logged in as: $(whoami)"
```

**Step 3:** Make it executable and run it:
```bash
chmod +x hello.sh
./hello.sh
```

**Expected Output:**
```
Hello from Bash!
Today is: Mon Mar 17 10:30:00 IST 2026
I am logged in as: yourname
```

> 🎉 **You just wrote and ran your first Bash script!**

---

### Exercise 5 — Reflection (No Code Required)

Answer these questions in your notes to solidify your understanding:

1. What does **Bash** stand for?
2. Name **two operating systems** where Bash is commonly used.
3. Give **two real-world examples** where you'd automate something with Bash in your own life or job.
4. What command tells you which shell you're using right now?

---

## 📎 Quick Reference Card

```
COMMAND          WHAT IT DOES
───────────────────────────────────────────
which bash       → Shows the path to bash
bash --version   → Shows bash version
echo $SHELL      → Shows your current shell
cat /etc/shells  → Lists all installed shells
echo "text"      → Prints text to the screen
date             → Shows current date & time
whoami           → Shows current username
pwd              → Shows current directory
chmod +x file    → Makes a file executable
./script.sh      → Runs a bash script
```

---

## 🗂️ Notion Tags (suggested)

`bash` `shell-scripting` `linux` `devops` `introduction` `ZTM` `module-1`

---

*Next Episode →* **Module 1 | Episode 2** *(paste the next transcript to continue!)*
