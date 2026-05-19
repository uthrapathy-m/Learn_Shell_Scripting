# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 01 — Intro to Bash Shell Scripting
> **Episode:** 7. Comments in Bash
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

Short but important — this episode covers **comments in Bash scripts**: how to write them, why they matter, the one exception to the `#` rule (the shebang!), and a clever trick for multi-line comments. It also introduces a **practical debugging tip** — displaying line numbers in your editor.

---

## 🧠 Theory

### 1. What is a Comment?

A **comment** is a line (or part of a line) in your script that Bash **completely ignores** during execution. It exists purely for humans — to explain what the code does, why it does it, or to leave notes for your future self.

```bash
# This is a comment — Bash skips this line entirely
echo "Hello"   # This is an inline comment — Bash runs echo, ignores the rest
```

---

### 2. The Comment Symbol — `#`

In Bash, **everything after a `#` on a line is a comment**.

```bash
# Full-line comment
echo "This runs"     # Inline comment — only the echo runs
# echo "This does NOT run"
```

> ⚠️ **The one exception:** `#!` on the very **first line** of a script is the **shebang** — it is NOT treated as a comment. It's a special OS directive.

```bash
#!/bin/bash      ← NOT a comment — this is the shebang (OS reads it)
# This IS a comment — Bash ignores this
```

---

### 3. Why Write Comments?

Comments are one of the most underestimated habits of good developers. Here's why they matter:

| Reason | Detail |
|---|---|
| **Future self** | You'll forget what a script does in 2 weeks — comments remind you |
| **Collaboration** | Other sysadmins who maintain your script can understand it immediately |
| **Debugging** | Quickly "disable" a line to test behaviour without deleting it |
| **Documentation** | Describe parameters, expected inputs, outputs, and edge cases |
| **Professional habit** | Clean, commented code is a mark of a skilled developer |

> 💡 **Rule of thumb:** If it took you more than 5 minutes to figure out *how* to write something, write a comment explaining *what* it does.

---

### 4. Single-Line Comments

The standard and most common type of comment in Bash.

```bash
#!/bin/bash

# ─────────────────────────────────────────
# Script  : backup.sh
# Purpose : Backs up the home directory
# Author  : Your Name
# Date    : 2025-04-01
# ─────────────────────────────────────────

# Step 1: Create backup directory
mkdir -p ~/backup

# Step 2: Copy files
cp -r ~/documents ~/backup/    # copy docs folder

# Step 3: Confirm
echo "Backup complete!"
```

> 📌 **Space after `#` is not mandatory, but improves readability.** Use `# comment` not `#comment`.

---

### 5. Multi-Line Comments — The Official Way

Bash does **not officially support multi-line comments** the way Python (`""" """`) or C (`/* */`) do.

The **recommended approach** is to simply stack multiple single-line comments:

```bash
# This is a multi-line comment in Bash.
# It's done by placing a # at the start
# of each individual line.
# This is the correct, standard approach.
echo "Script runs here"
```

> ✅ This is **clean, readable, and universally understood** — always prefer this.

---

### 6. Multi-Line Comments — The Hack (`: '...'`)

There is a **workaround trick** (not officially a comment feature) that uses the `:` (null command) and a quoted string:

```bash
: '
This is a multi-line comment hack.
Bash will not execute these lines.
It works because : is a no-op command
that takes the quoted string as an argument
and does nothing with it.
'
echo "Script continues here"
```

**How it works:**
| Part | What it is |
|---|---|
| `:` | The **null command** — does nothing, returns success (`0`) |
| `' ... '` | A single-quoted string passed as an argument to `:` |
| Result | `:` receives the string, ignores it, does nothing → acts like a comment |

> ⚠️ **Important caveats:**
> - This is a **hack**, not a real comment feature
> - It is **not common** in professional scripts
> - It can cause confusion for readers unfamiliar with the trick
> - **Stick to `#` single-line comments** for everything unless you have a very specific need

---

### 7. Commenting Out Code for Debugging

One of the most practical uses of comments is **temporarily disabling lines** to debug your script:

```bash
#!/bin/bash

echo "Step 1: Creating directory"
mkdir -p ~/test_dir

# echo "Step 2: DISABLED for debugging"   ← this line is temporarily off

echo "Step 3: Done"
```

Instead of deleting code you're unsure about, comment it out. Easy to re-enable with one keystroke.

---

### 8. Displaying Line Numbers in Vim

When you have an error on "line 47" of your script, you need to find it fast. The instructor shows how to enable **line numbers in Vim** — useful for debugging.

#### Temporarily (current session only):
```vim
:set nu        ← type in Vim's last-line (command) mode
:set nonu      ← to turn line numbers OFF
```
> `nu` is short for `number` — as in "set numbers"

#### Permanently (every time Vim opens):
Add to `~/.vimrc`:
```vim
set nu
```

```bash
# Create or edit your .vimrc
nano ~/.vimrc
# Add this line:
set nu
# Save and exit
```

From now on, every file you open in Vim will show line numbers automatically.

> 💡 **VSCode users:** Line numbers are ON by default. No setup needed — look at the left gutter of any open file.

---

## 🔑 Key Takeaways

> 📝 Short episode — but these habits compound over a career.

- In Bash, **`#` marks a comment** — everything after it on that line is ignored.
- The **only exception** to the `#` rule is `#!` on line 1 — that's the shebang, not a comment.
- **Always add a space** after `#` for readability: `# comment` not `#comment`.
- Comments are for **humans, not computers** — they improve maintainability massively.
- Bash has **no native multi-line comment** syntax.
- The **recommended approach** for multi-line comments is stacking `#` on each line.
- The `: '...'` hack exists but is **not common** — avoid in professional scripts.
- Use comments to **temporarily disable lines** during debugging.
- Enable **line numbers in Vim** with `:set nu` (temporary) or `set nu` in `~/.vimrc` (permanent).

---

## 💡 Tips & Tricks

**🔹 Script header template — use this at the top of every script:**
```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : script_name.sh
# Purpose : Brief description of what the script does
# Author  : Your Name
# Date    : YYYY-MM-DD
# Version : 1.0
# Usage   : ./script_name.sh [arguments]
# ─────────────────────────────────────────────────────
```
This takes 30 seconds to fill in and saves hours of confusion later.

**🔹 Comment WHY, not WHAT:**
```bash
# BAD comment — obvious from the code itself:
# Run ls command
ls -la

# GOOD comment — explains the reasoning:
# List all files including hidden ones to catch dot-files during cleanup
ls -la
```

**🔹 Section dividers make long scripts scannable:**
```bash
# ── CONFIGURATION ─────────────────────────────────
BACKUP_DIR="/backup"
MAX_FILES=100

# ── FUNCTIONS ─────────────────────────────────────
function do_backup() { ... }

# ── MAIN ──────────────────────────────────────────
do_backup
```

**🔹 VSCode line numbers:**
Line numbers are always visible in VSCode by default. You can also jump directly to a line:
- Press `Ctrl+G` → type the line number → Enter

**🔹 Quick comment/uncomment in VSCode:**
- Select one or more lines
- Press `Ctrl+/` to toggle comments on/off instantly

**🔹 `#TODO` and `#FIXME` are standard comment conventions:**
```bash
# TODO: Add error handling here
# FIXME: This breaks if the directory doesn't exist
# NOTE: This assumes root privileges
```

---

## 🏋️ Practice Exercises

> These exercises focus on building the habit of clean, well-commented code — a professional skill.

---

### Exercise 1 — Add a Header Comment Block to Your Scripts

**Goal:** Go back to the scripts you've already written and add a proper header.

```bash
code ~/scripts/first_script.sh
```

Add this at the top (after the shebang):
```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : first_script.sh
# Purpose : Creates a directory and file, then lists them
# Author  : Your Name
# Date    : 2025-04-01
# Version : 1.0
# ─────────────────────────────────────────────────────

mkdir -p ~/drun
echo "hello-bash-world" > ~/drun/drun-file.txt
ls -a .
cat ~/drun/drun-file.txt
```

Repeat this for `my_info.sh` and any other scripts you've created.

---

### Exercise 2 — Practice Single-Line Comments

**Goal:** Write a well-commented version of a simple script.

Create `~/scripts/commented_demo.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : commented_demo.sh
# Purpose : Demonstrate good commenting practices
# ─────────────────────────────────────────────────────

# Print a welcome message to the user
echo "Welcome to the Bash Scripting Demo!"

# Show the current logged-in user
echo "Logged in as: $(whoami)"

# Show the current date and time in a readable format
echo "Current date: $(date '+%A, %d %B %Y')"

# Show how much disk space is free on the root partition
echo "Free disk space:"
df -h /

# End of script
echo "Done!"
```

```bash
chmod +x ~/scripts/commented_demo.sh
./commented_demo.sh
```

---

### Exercise 3 — Comparing Comment Styles (Multi-line)

**Goal:** See both multi-line comment approaches side-by-side.

Create `~/scripts/comment_styles.sh`:

```bash
#!/bin/bash

# ── METHOD 1: Recommended — stacked single-line comments ──
# This is the standard and most readable way to write
# multi-line comments in Bash.
# Use this in all your scripts.
echo "After method 1 comment"

# ── METHOD 2: The hack — colon with quoted string ──
: '
This is the : hack for multi-line comments.
It works but is not common in professional code.
Use method 1 instead.
'
echo "After method 2 comment"
```

Run it:
```bash
bash ~/scripts/comment_styles.sh
```

**Observe:** Both comment blocks are silently skipped — only the `echo` lines produce output.

---

### Exercise 4 — Debugging with Comments

**Goal:** Practice commenting out lines to debug a script.

Create `~/scripts/debug_practice.sh`:

```bash
#!/bin/bash

echo "Line 1: Starting script"
echo "Line 2: About to create directory"
mkdir -p ~/debug_test
echo "Line 3: Directory created"

# Temporarily disable the next line to test without it:
# rm -rf ~/debug_test    ← This line is "off" — safe to test

echo "Line 4: Script finished"
echo "Directory exists: $(ls -d ~/debug_test 2>/dev/null && echo YES || echo NO)"
```

Run it, then **uncomment** the `rm` line, run again, and observe the difference.

---

### Exercise 5 — Enable Line Numbers in Your Editor

**Goal:** Set up line numbers for easier debugging.

**In VSCode** (already on by default — just verify):
- Open any `.sh` file — line numbers should be visible on the left
- Press `Ctrl+G`, type `3`, hit Enter → jumps to line 3

**In Vim** (if you use it):
```bash
# Temporarily enable line numbers
vim ~/scripts/first_script.sh
# In Vim, type:   :set nu   then Enter

# Make it permanent:
echo "set nu" >> ~/.vimrc

# Verify .vimrc:
cat ~/.vimrc
```

**In Nano:**
```bash
# Nano shows line numbers in the status bar at the bottom by default
nano ~/scripts/first_script.sh
# Look at the bottom: "line X, col Y"
```

---

### 🧠 Quiz — Test Yourself

1. What character starts a comment in Bash?
2. What is the **only exception** to the `#` comment rule?
3. Is the space after `#` required? Does it affect anything?
4. Name **three reasons** why writing comments is good practice.
5. Does Bash officially support multi-line comments?
6. What is the **recommended** way to write a multi-line comment in Bash?
7. What is the `: '...'` trick, and should you use it in professional scripts?
8. How do you **temporarily disable** a line of code without deleting it?
9. In Vim, what command enables line numbers (temporarily)?
10. How do you make line numbers permanent in Vim?

---

## 📎 Quick Reference Card

```
COMMENT SYNTAX
──────────────────────────────────────────────────────────────
# This is a comment               Single-line comment
echo "runs"  # inline comment     Inline comment (after code)
# echo "disabled"                 Commented-out code (debugging)

MULTI-LINE COMMENTS
──────────────────────────────────────────────────────────────
# Line one of comment             ✅ RECOMMENDED — stack # lines
# Line two of comment
# Line three of comment

: '                               ⚠️  HACK — works but not common
Multi-line
comment block
'

SCRIPT HEADER TEMPLATE
──────────────────────────────────────────────────────────────
#!/bin/bash
# ─────────────────────────────────────────────────
# Script  : name.sh
# Purpose : What does this script do?
# Author  : Your Name
# Date    : YYYY-MM-DD
# Version : 1.0
# Usage   : ./name.sh [args]
# ─────────────────────────────────────────────────

VIM LINE NUMBERS
──────────────────────────────────────────────────────────────
:set nu          Enable line numbers (current session)
:set nonu        Disable line numbers (current session)
~/.vimrc         Add "set nu" here to make it permanent

VSCODE SHORTCUTS
──────────────────────────────────────────────────────────────
Ctrl+/           Toggle comment on selected line(s)
Ctrl+G           Go to specific line number
```

---

## 🗂️ Notion Tags (suggested)

`bash` `comments` `code-style` `best-practices` `debugging` `vim` `shell-scripting` `ZTM` `module-2` `section-01`

---

*← Previous: Module 2 | Section 01 — The Bash Shebang*
*→ Next Episode: Module 2 | Section 01 — (Next transcript)*
