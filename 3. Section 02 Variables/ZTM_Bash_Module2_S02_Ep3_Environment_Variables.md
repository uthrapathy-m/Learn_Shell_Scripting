# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 02 — Variables
> **Episode:** 3. Environment and Shell Local Variables
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

Every time a terminal opens, a whole collection of variables is already waiting for you. This episode explains the **two classes of shell variables** — environment variables (global, inherited by child processes) and shell local variables (private to the current shell) — and teaches you how to view, create, and persist both types at the user and system level.

---

## 🧠 Theory

### 1. The Two Classes of Variables

When you open a terminal, two types of pre-defined variables are automatically set:

| | **Shell Local Variable** | **Environment Variable** |
|---|---|---|
| Also called | Local variable | Global / Exported variable |
| Scope | Current shell **only** | Current shell + all **child shells & processes** |
| Visible to child processes? | ❌ No | ✅ Yes — inherited |
| Created with | `variable=value` | `export variable=value` |
| Listed by `env`? | ❌ No | ✅ Yes |
| Listed by `set`? | ✅ Yes | ✅ Yes |

> 💡 **Analogy:** A shell local variable is like a note on your desk — only you can see it. An environment variable is like a memo sent to your whole team — every process (child) that starts from your shell automatically receives it.

---

### 2. Environment Variables — Global & Inherited

Environment variables are set when the shell starts and are **passed down to every process or child shell** spawned from it.

**Common examples:**

| Variable | What it stores |
|---|---|
| `$PATH` | Directories searched for commands (colon-separated) |
| `$HOME` | Current user's home directory |
| `$USER` | Current logged-in username |
| `$SHELL` | Path to the current shell |
| `$PWD` | Current working directory |
| `$LANG` | System language/locale |
| `$EDITOR` | Default text editor |
| `$TERM` | Terminal type |

```bash
echo $PATH
echo $HOME
echo $USER
```

> 📌 By convention, environment variables are written in **UPPERCASE**.

---

### 3. Viewing Environment Variables

#### Method 1 — `env` command:
Lists **only environment variables** (no shell local variables, no functions).

```bash
env               # print all environment variables
env | less        # scroll through them comfortably
env | grep USER   # search for a specific one
env | grep HOME
```

#### Method 2 — `printenv` command:
Similar to `env` but can also print the value of a **specific variable by name**:

```bash
printenv              # print all environment variables
printenv HOME         # print value of HOME only
printenv USER         # print value of USER only
printenv SHELL PWD    # print multiple variables at once
```

> 💡 `printenv VAR` is cleaner than `echo $VAR` for checking environment variables — it only succeeds if the variable actually exists as an environment variable.

#### Method 3 — `set` command:
Lists **everything**: environment variables + shell local variables + shell functions.

```bash
set               # prints everything (very long output)
set | grep abc    # search for a specific variable
```

**To suppress functions and see only variables:**
```bash
set -o posix      # enable POSIX mode — suppresses function output
set               # now shows only variables
set +o posix      # turn off POSIX mode (restore default behaviour)
```

---

### 4. Shell Local Variables — Private to the Current Shell

A shell local variable is created with plain assignment — **no `export`**:

```bash
abc=123           # shell local variable
echo $abc         # → 123  (works in current shell)
```

**Key behaviour — NOT visible to child processes:**
```bash
abc=123
bash              # open a child shell
echo $abc         # → (empty — abc didn't get passed to the child!)
exit              # return to parent shell
echo $abc         # → 123  (still here in the parent)
```

**Proof using `env` vs `set`:**
```bash
abc=123

env | grep abc    # → (nothing — abc is NOT an environment variable)
set | grep abc    # → abc=123  (found! — set shows ALL variables)
```

---

### 5. Creating Environment Variables — `export`

To make a variable available to child processes, **export** it:

#### Method 1 — Declare then export:
```bash
MY_VAR="hello"
export MY_VAR
```

#### Method 2 — Declare and export in one line (most common):
```bash
export MY_VAR="hello"
```

**Verify it's now an environment variable:**
```bash
env | grep MY_VAR     # → MY_VAR=hello  ✅
```

**Child shell inherits it:**
```bash
export MY_VAR="hello"
bash                  # open child shell
echo $MY_VAR          # → hello  ✅ (inherited!)
exit
```

---

### 6. Where to Persist Variables

Variables created in the terminal are **session-only** — they disappear when the terminal closes. To make them permanent, add them to a configuration file.

#### User-specific variables (applies to ONE user only):

| File | When it's read |
|---|---|
| `~/.bashrc` | Every new interactive terminal session |
| `~/.bash_profile` | Login shells (SSH, first login) |

```bash
# Add to ~/.bashrc for persistence:
nano ~/.bashrc

# Add at the bottom:
export MY_VAR="my_value"
export EDITOR="nano"

# Reload to apply:
source ~/.bashrc
```

#### System-wide variables (applies to ALL users):

| File | Purpose |
|---|---|
| `/etc/environment` | Simple `KEY=value` pairs — no shell syntax |
| `/etc/profile` | Shell script — runs for all login shells |
| `/etc/bash.bashrc` | Shell script — runs for all interactive bash shells |

> ⚠️ Only **root** can edit these files:
```bash
sudo nano /etc/environment
```

**Format of `/etc/environment`** — simple pairs only, no `export` keyword:
```
MY_GLOBAL_VAR="system_wide_value"
APP_HOME="/opt/myapp"
```

---

### 7. The Scope Chain — Visualised

```
/etc/environment  ← system-wide env vars (all users, all shells)
       ↓
/etc/profile      ← system-wide for login shells
       ↓
~/.bash_profile   ← user-specific login shell config
       ↓
~/.bashrc         ← user-specific interactive shell config
       ↓
Current Shell     ← export MY_VAR="value"  (session only)
       ↓
Child Shell/Process ← inherits all exported vars from parent
       (shell local vars are NOT passed down)
```

---

### 8. Summary — Local vs Environment Variable at a Glance

```bash
# Shell LOCAL variable:
city="Chennai"
env | grep city     # → (nothing — not an env var)
set | grep city     # → city=Chennai  ✅

# ENVIRONMENT variable:
export CITY="Chennai"
env | grep CITY     # → CITY=Chennai  ✅
set | grep CITY     # → CITY=Chennai  ✅

# Open a child shell and test inheritance:
bash
echo $city          # → (empty — local var not inherited)
echo $CITY          # → Chennai  ✅ (env var IS inherited)
exit
```

---

## 🔑 Key Takeaways

- There are **two classes** of variables: shell local and environment (global/exported).
- **Shell local** variables exist only in the current shell — child processes cannot see them.
- **Environment variables** are inherited by all child shells and processes spawned from the current shell.
- Both types use **UPPERCASE** naming by convention.
- `env` and `printenv` list **only environment variables**.
- `set` lists **everything** — env vars + local vars + functions. Use `set -o posix` to hide functions.
- Use `export VAR=value` to create an environment variable.
- A plain `VAR=value` (no export) creates a **shell local variable**.
- To make variables **permanent** for one user → add to `~/.bashrc` or `~/.bash_profile`.
- To make variables **permanent system-wide** → add to `/etc/environment` or `/etc/profile` (root only).
- `/etc/environment` uses simple `KEY=value` format — no `export` keyword, no shell syntax.

---

## 💡 Tips & Tricks

**🔹 Quick test: is a variable exported or local?**
```bash
my_var="test"
export | grep my_var    # → (nothing — it's local)
export my_var
export | grep my_var    # → declare -x my_var="test"  (exported!)
```

**🔹 `printenv` vs `echo $VAR` — what's safer?**
```bash
# echo always succeeds — even for undefined variables (prints blank)
echo $UNDEFINED_VAR     # → (blank — no way to tell if it exists)

# printenv exits with error code 1 if the variable doesn't exist
printenv UNDEFINED_VAR  # → (nothing printed, exit code = 1)
```
Use `printenv VAR` in scripts when you need to check if a variable is truly an environment variable.

**🔹 See all exported variables cleanly:**
```bash
export -p             # list all exported variables with declare -x format
export -p | grep MY   # filter for specific ones
```

**🔹 Export and remove in one step:**
```bash
export -n MY_VAR      # un-export a variable (makes it local again, keeps its value)
```

**🔹 Child shell inherits a COPY — not a reference:**
```bash
export GREETING="Hello"
bash
GREETING="Changed in child"
echo $GREETING          # → Changed in child (inside child)
exit
echo $GREETING          # → Hello (parent is unchanged — child got a copy)
```

**🔹 Common environment variables to set in `~/.bashrc`:**
```bash
export EDITOR="nano"           # set default text editor
export VISUAL="code"           # set default visual editor (VSCode)
export HISTSIZE=5000           # keep last 5000 commands in history
export HISTFILESIZE=10000      # store last 10000 commands in history file
export LANG="en_US.UTF-8"     # set system language
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Explore Environment Variables

**Goal:** Get familiar with the commands that list environment variables.

```bash
# List all environment variables
env

# Browse them comfortably
env | less          # press q to quit

# Count how many there are
env | wc -l

# Search for specific ones
env | grep HOME
env | grep USER
env | grep SHELL
env | grep PATH

# Use printenv for specific lookups
printenv HOME
printenv USER
printenv SHELL
printenv PATH HOME USER     # multiple at once
```

---

### Exercise 2 — Local vs Environment Variable (The Key Experiment)

**Goal:** Prove the difference between a local variable and an environment variable.

```bash
# Create a LOCAL variable
my_local="I am local"

# Create an ENVIRONMENT variable
export my_global="I am global"

# Both visible in current shell
echo $my_local       # → I am local
echo $my_global      # → I am global

# Check with env — only environment vars show up
env | grep my_local   # → (nothing)
env | grep my_global  # → my_global=I am global

# Check with set — both show up
set | grep my_local   # → my_local=I am local
set | grep my_global  # → my_global=I am global

# Open a child shell and test inheritance
bash
echo $my_local        # → (empty — local var NOT inherited)
echo $my_global       # → I am global  (env var IS inherited)
exit
```

---

### Exercise 3 — `env` vs `printenv` vs `set`

**Goal:** Understand when to use each listing command.

```bash
# Create test variables
test_local="local_value"
export TEST_GLOBAL="global_value"

echo "=== env output ==="
env | grep -E "test_local|TEST_GLOBAL"
# Only TEST_GLOBAL appears

echo "=== printenv output ==="
printenv TEST_GLOBAL
printenv test_local 2>/dev/null || echo "test_local: NOT an environment variable"

echo "=== set output ==="
set | grep -E "test_local|TEST_GLOBAL"
# Both appear

echo "=== set in POSIX mode (no functions) ==="
(set -o posix; set) | grep -E "test_local|TEST_GLOBAL"
```

---

### Exercise 4 — `export` in Practice

**Goal:** Export a variable and verify a child shell inherits it.

```bash
# Step 1: Create and export a variable
export MY_APP_ENV="production"
export MY_APP_PORT="8080"

# Step 2: Verify it's in the environment
env | grep MY_APP

# Step 3: Open a child bash and check
bash -c 'echo "In child — ENV: $MY_APP_ENV, PORT: $MY_APP_PORT"'

# Step 4: Test that a local variable is NOT inherited
my_secret="top_secret"
bash -c 'echo "Secret in child: $my_secret"'    # → empty

# Step 5: Un-export a variable (make it local again)
export -n MY_APP_ENV
env | grep MY_APP_ENV     # → (gone from env)
echo $MY_APP_ENV          # → still has value in current shell
```

---

### Exercise 5 — Make Variables Persistent in `~/.bashrc`

**Goal:** Add your own environment variables to `~/.bashrc` so they survive terminal restarts.

```bash
# Open ~/.bashrc
code ~/.bashrc
# or: nano ~/.bashrc
```

Add this block at the bottom:
```bash
# ── My Custom Environment Variables ──────────────
export EDITOR="nano"
export MY_NAME="Your Name"
export MY_PROJECTS="$HOME/projects"
export HISTSIZE=5000
export HISTFILESIZE=10000
```

```bash
# Save and reload
source ~/.bashrc

# Verify they are now set
printenv EDITOR
printenv MY_NAME
printenv MY_PROJECTS

# Open a new terminal and verify they still exist
echo $MY_NAME     # should print "Your Name"
```

---

### Exercise 6 — Explore Shell Local Variables with `set`

**Goal:** Use `set` to browse local variables and filter the output.

```bash
# Create a few local variables
fruit="mango"
count=42
city="Chennai"

# Find them with set
set | grep fruit
set | grep count
set | grep city

# They should NOT appear in env
env | grep fruit    # → (nothing)

# Use POSIX mode to reduce noise
set -o posix
set | grep -E "fruit|count|city"
set +o posix        # turn off POSIX mode
```

---

### Exercise 7 — Build an Environment Info Script

**Goal:** Create a script that displays a clean report of key environment variables.

Create `~/scripts/env_report.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : env_report.sh
# Purpose : Display a clean environment variable report
# ─────────────────────────────────────────────────────

echo "============================================="
echo "        ENVIRONMENT VARIABLE REPORT"
echo "============================================="
echo ""

echo "── User & Session ───────────────────────────"
echo "  User        : $(printenv USER)"
echo "  Home        : $(printenv HOME)"
echo "  Shell       : $(printenv SHELL)"
echo "  Terminal    : $(printenv TERM)"
echo ""

echo "── System ───────────────────────────────────"
echo "  Hostname    : $(hostname)"
echo "  PWD         : $(printenv PWD)"
echo "  Language    : $(printenv LANG)"
echo ""

echo "── History Settings ─────────────────────────"
echo "  HISTSIZE    : $(printenv HISTSIZE)"
echo "  HISTFILESIZE: $(printenv HISTFILESIZE)"
echo "  HISTFILE    : $HISTFILE"
echo ""

echo "── PATH (one dir per line) ──────────────────"
echo $PATH | tr ':' '\n' | nl
echo ""

echo "── Total Environment Variables ──────────────"
echo "  Count: $(env | wc -l)"
echo ""
echo "============================================="
```

```bash
chmod +x ~/scripts/env_report.sh
./env_report.sh
```

---

### 🧠 Quiz — Test Yourself

1. What are the **two classes** of shell variables?
2. What is the key difference between a shell local variable and an environment variable?
3. Which command lists **only** environment variables?
4. Which command lists **all** variables including local ones and functions?
5. How do you create an environment variable called `APP_MODE` with value `production`?
6. How do you use `printenv` to get the value of just the `HOME` variable?
7. Why does `env | grep my_local_var` return nothing even though the variable exists?
8. How do you suppress function output from the `set` command?
9. Where do you add a variable to make it permanent for just **your user**?
10. Where do you add a variable to make it permanent **system-wide** (all users)?
11. What format does `/etc/environment` use — does it use the `export` keyword?
12. If you export a variable and open a child shell, then modify the variable in the child — does the parent's copy change?
13. What does `export -n VAR` do?
14. What is the difference between `~/.bashrc` and `~/.bash_profile`?

---

## 📎 Quick Reference Card

```
VARIABLE TYPES
──────────────────────────────────────────────────────────────
var=value              Shell LOCAL variable (current shell only)
export VAR=value       ENVIRONMENT variable (inherited by children)

VIEWING VARIABLES
──────────────────────────────────────────────────────────────
env                    List ONLY environment variables
env | less             Browse env vars with pagination
env | grep NAME        Search for a specific env var
printenv               List all environment variables
printenv VAR           Print value of one specific env var
printenv A B C         Print values of multiple env vars
set                    List ALL vars + functions
set | grep name        Search all vars
set -o posix; set      List all vars WITHOUT functions
set +o posix           Restore default (turn off POSIX mode)
export -p              List all exported variables (declare -x format)

CREATING & MANAGING
──────────────────────────────────────────────────────────────
var=value              Create local variable
export VAR=value       Create environment variable (one step)
VAR=value; export VAR  Create then export (two steps)
export -n VAR          Un-export (make local again, keeps value)
unset VAR              Delete variable entirely

PERSISTENCE
──────────────────────────────────────────────────────────────
~/.bashrc              User-specific — every interactive shell
~/.bash_profile        User-specific — login shells only
/etc/environment       System-wide — KEY=value pairs (NO export keyword)
/etc/profile           System-wide — login shells (shell script format)
/etc/bash.bashrc       System-wide — all interactive bash shells

After editing any config file:
source ~/.bashrc       Reload without restarting terminal

KEY BUILT-IN ENVIRONMENT VARIABLES
──────────────────────────────────────────────────────────────
$PATH          Directories searched for commands
$HOME          User's home directory
$USER          Current username
$SHELL         Current shell path
$PWD           Current working directory
$LANG          System language/locale
$TERM          Terminal type
$EDITOR        Default text editor
$HISTSIZE      Commands kept in memory
$HISTFILESIZE  Commands stored in history file
$HISTFILE      Path to history file
```

---

## 🗂️ Notion Tags (suggested)

`bash` `environment-variables` `shell-variables` `export` `env` `printenv` `set` `bashrc` `persistence` `ZTM` `module-2` `section-02`

---

*← Previous: Module 2 | Section 02 — Variable Expansion and Quoting*
*→ Next Episode: Module 2 | Section 02 — Getting User Input*
