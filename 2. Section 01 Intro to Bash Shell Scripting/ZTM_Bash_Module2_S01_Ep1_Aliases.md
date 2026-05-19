# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 01 — Intro to Bash Shell Scripting
> **Episode:** 1. Bash Aliases
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

Before jumping into actual Bash programming, this episode covers **Bash Aliases** — one of the most immediately useful features of the shell. If you ever find yourself typing the same long command over and over, aliases are the fix. This episode covers creating, using, persisting, and removing aliases.

---

## 🧠 Theory

### 1. What is a Bash Alias?

A **Bash alias** is simply a **shortcut (nickname) for a command**. Instead of typing a long, complex command every time, you define a short name for it and use that instead.

```
alias shortname='the actual long command'
```

> 💡 Think of it like a contact name in your phone — instead of dialling a 10-digit number every time, you just tap "Mom".

---

### 2. Listing All Existing Aliases

Every Linux distro ships with a few built-in aliases. To see them all:

```bash
alias
```

**Example output you might see:**
```
alias grep='grep --color=auto'
alias ll='ls -alF'
alias ls='ls --color=auto'
```

> 🔍 Notice that even `ls` is already an alias on most systems — when you type `ls`, it's actually running `ls --color=auto` behind the scenes!

---

### 3. Running the Original Command (Bypassing an Alias)

Since `ls` itself may be aliased, sometimes you need the **raw, original command** without any alias. Prefix the command with a backslash `\`:

```bash
\ls        # runs the real ls, ignoring any alias
\grep      # runs the real grep, ignoring any alias
```

> ⚠️ **Important:** This is useful for debugging or when alias behaviour is unexpected.

---

### 4. Creating a New Alias

#### Syntax:
```bash
alias aliasName='command to run'
```

#### Rules:
| Rule | Detail |
|---|---|
| **No spaces** around `=` | `alias c = 'clear'` ❌ → `alias c='clear'` ✅ |
| **Quotes are required** when command has spaces | `alias ll='ls -alF'` ✅ |
| **Quotes are optional** when command has no spaces | `alias c=clear` ✅ |

#### Examples:
```bash
# Short alias for clear
alias c='clear'

# Show current date and time in a nice format
alias now='date +"%F\%T"'
```

---

### 5. ⚠️ Aliases Are Session-Only by Default

This is the most important gotcha for beginners:

> **If you create an alias in the terminal, it DISAPPEARS when you close that terminal or open a new session.**

```bash
alias c='clear'    # works NOW in this terminal
# → close terminal → open new terminal → c: command not found ❌
```

---

### 6. Making Aliases Persistent

To make aliases survive terminal restarts and reboots, you must declare them in a **shell configuration file** that is loaded every time a new shell starts.

#### The files to edit (in order of preference):

| File | When it's loaded |
|---|---|
| `~/.bash_profile` | Login shells (SSH sessions, first login) |
| `~/.bashrc` | Interactive non-login shells (new terminal windows) |

> ✅ On most Linux desktops, **`~/.bashrc`** is the right place.

#### How to add a persistent alias:

```bash
# Open .bashrc in a text editor
nano ~/.bashrc

# Scroll to the bottom and add your alias:
alias now='date +"%F\%T"'

# Save and exit (Ctrl+O, Enter, Ctrl+X in nano)
```

---

### 7. Reloading `.bashrc` Without Restarting the Terminal

After saving `.bashrc`, the **current terminal** won't know about the new alias yet — because `.bashrc` is only read when the shell **starts**. You have two options:

```bash
# Option 1: Reload .bashrc manually (no need to close terminal)
source ~/.bashrc

# Option 2: Close and reopen the terminal
```

> 💡 `source` (or its shorthand `.`) executes the file in the current shell session — it's like a soft reload.

---

### 8. Removing an Alias

To remove an alias **in the current session**:

```bash
unalias aliasName

# Example:
unalias now
```

> ⚠️ `unalias` only removes it from the **current session**. If the alias is saved in `~/.bashrc`, you must also delete it from that file to make the removal permanent.

---

### 9. Practical & Useful Alias Examples

Here are real-world aliases from the instructor — great to save in your `.bashrc`:

```bash
# Connect to a remote server via SSH (no more memorising IPs/ports!)
alias server1='ssh -p 2222 user1@192.168.1.100'

# List all open network ports
alias ports='netstat -tupan'

# Quickly become root
alias root='sudo su -'

# Fully update the system (Debian/Ubuntu)
alias update='sudo apt update && sudo apt dist-upgrade && sudo apt clean'

# List directory contents sorted by file size, human-readable, single column
alias lt='ls -hSF --size -1'

# Show current date and time
alias now='date +"%F %T"'

# Clear the terminal
alias c='clear'
```

---

## 🔑 Key Takeaways

> 📝 Core things to remember from this episode.

- An alias is a **shortcut for a command** — type less, do more.
- Run `alias` alone to **list all current aliases**.
- Use `\commandname` to **bypass an alias** and run the original command.
- Alias syntax: `alias name='command'` — **no spaces around `=`**.
- Aliases created in the terminal are **temporary** (session only).
- To make aliases **permanent**, add them to `~/.bashrc` (or `~/.bash_profile`).
- After editing `~/.bashrc`, run `source ~/.bashrc` to apply changes **immediately**.
- Use `unalias name` to **remove** an alias from the current session.

---

## 💡 Tips & Tricks

**🔹 Where to put aliases in `.bashrc`?**
Always add aliases at the **bottom** of the file, after all existing content. This keeps the file clean and your additions easy to find.

**🔹 Group and comment your aliases:**
```bash
# --- My Custom Aliases ---
alias c='clear'
alias now='date +"%F %T"'
alias update='sudo apt update && sudo apt dist-upgrade && sudo apt clean'
```

**🔹 Check if a name is already taken before creating an alias:**
```bash
type aliasname    # shows if it's a built-in, alias, function, or file
# or
alias | grep aliasname
```

**🔹 Double quotes vs Single quotes in aliases:**
```bash
alias now="date"    # double quotes: variables are expanded AT DEFINITION TIME
alias now='date'    # single quotes: variables are expanded AT RUNTIME (usually what you want)
```
> ✅ **Prefer single quotes** in aliases to avoid unexpected variable expansion.

**🔹 Chaining commands in an alias with `&&`:**
```bash
alias update='sudo apt update && sudo apt upgrade'
# && means: run the second command ONLY IF the first succeeds
```

**🔹 VSCode Tip:**
Open `~/.bashrc` directly in VSCode:
```bash
code ~/.bashrc
```

---

## 🏋️ Practice Exercises

> Open your terminal in VSCode and work through each exercise. All changes to `.bashrc` will persist on your Linux PC.

---

### Exercise 1 — Explore Built-in Aliases

**Goal:** See what aliases already exist on your system.

```bash
# List all aliases
alias

# Try running ll (a common built-in alias)
ll

# Now try the original ls without the alias
\ls
```

**Questions to answer:**
- What does `ll` expand to on your system?
- Can you see a difference between `ls` and `\ls` output?

---

### Exercise 2 — Create Temporary Aliases

**Goal:** Create session-only aliases and verify they work.

```bash
# Create a shortcut for clear
alias c='clear'

# Create a date/time alias
alias now='date +"%Y-%m-%d %H:%M:%S"'

# Test them
c
now

# List all aliases — confirm yours appear
alias
```

Now **open a new terminal** and try running `c` and `now`. Observe that they're gone.

---

### Exercise 3 — Make Aliases Persistent

**Goal:** Add aliases to `~/.bashrc` so they survive restarts.

**Step 1:** Open `.bashrc`
```bash
nano ~/.bashrc
# or in VSCode:
code ~/.bashrc
```

**Step 2:** Scroll to the bottom and add:
```bash
# --- My Custom Aliases ---
alias c='clear'
alias now='date +"%Y-%m-%d %H:%M:%S"'
alias ll='ls -alF'
alias ports='netstat -tupan'
alias update='sudo apt update && sudo apt dist-upgrade && sudo apt clean'
```

**Step 3:** Save the file, then reload without closing the terminal:
```bash
source ~/.bashrc
```

**Step 4:** Test:
```bash
now        # should print formatted date & time
c          # should clear the screen
```

**Step 5:** Open a **new terminal** and test again — they should still work! ✅

---

### Exercise 4 — Bypass an Alias

**Goal:** Practice running the original command, ignoring the alias.

```bash
# Run the aliased version
ls

# Run the original command (no colours, no alias effects)
\ls
```

Notice the visual difference in output.

---

### Exercise 5 — Remove an Alias

**Goal:** Remove an alias from the current session.

```bash
# Verify the alias exists
alias now

# Remove it
unalias now

# Try running it — should fail now
now
```

> **Note:** Since you saved `now` in `~/.bashrc`, it'll come back in the next session. To permanently delete it, also remove that line from `~/.bashrc`.

---

### Exercise 6 — Build Your Personal Alias Collection

**Goal:** Create at least 5 aliases that are useful for **your** workflow and add them to `~/.bashrc`.

Some ideas to get started:

```bash
# Go to your bash practice folder quickly
alias bp='cd ~/bash-practice'

# Show disk usage in human-readable format
alias df='df -h'

# Show memory usage
alias mem='free -h'

# Count files in current directory
alias count='ls -1 | wc -l'

# Show top 10 largest files in current dir
alias big='du -ah . | sort -rh | head -10'
```

Add your own! What commands do you type most often?

---

### 🧠 Quiz — Test Yourself

Answer these without looking at your notes:

1. What command lists all current aliases?
2. What is the correct syntax to create an alias called `gs` for `git status`?
3. How do you run the original `ls` command, bypassing its alias?
4. Where do you save aliases to make them **permanent**?
5. What command reloads `.bashrc` without closing the terminal?
6. How do you remove an alias called `now` from the current session?
7. What's the difference between using single quotes `'` vs double quotes `"` in an alias?

---

## 📎 Quick Reference Card

```
COMMAND                          WHAT IT DOES
──────────────────────────────────────────────────────────────
alias                            List all current aliases
alias name='command'             Create a new alias
\commandname                     Run original command (bypass alias)
unalias name                     Remove an alias (current session only)
source ~/.bashrc                 Reload .bashrc without restarting terminal
nano ~/.bashrc                   Edit .bashrc to add persistent aliases
code ~/.bashrc                   Edit .bashrc in VSCode

USEFUL ALIASES TO SAVE
──────────────────────────────────────────────────────────────
alias c='clear'
alias now='date +"%Y-%m-%d %H:%M:%S"'
alias ll='ls -alF'
alias lt='ls -hSF --size -1'
alias ports='netstat -tupan'
alias root='sudo su -'
alias update='sudo apt update && sudo apt dist-upgrade && sudo apt clean'
alias server1='ssh -p PORT USER@IP_ADDRESS'
```

---

## 🗂️ Notion Tags (suggested)

`bash` `aliases` `shell-scripting` `linux` `productivity` `bashrc` `ZTM` `module-2` `section-01`

---

*← Previous: Module 1 | Episode 1 — Course Introduction*
*→ Next Episode: Module 2 | Section 01 — Shells and Scripts*
