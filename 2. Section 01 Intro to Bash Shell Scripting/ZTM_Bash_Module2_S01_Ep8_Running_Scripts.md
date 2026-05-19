# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 01 — Intro to Bash Shell Scripting
> **Episode:** 8. Running Scripts
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

This is the **final episode of Section 01** — a focused, practical episode that consolidates everything you've learned about running scripts. You'll learn all the different ways to execute a Bash script, understand the critical difference between running a script in a **subshell vs the current shell**, and know exactly which method to use and when.

---

## 🧠 Theory

### 1. Overview — The Four Ways to Run a Script

There are four distinct methods to run a Bash script, each with different behaviour:

| # | Method | Syntax | Requires chmod +x? | Runs in... |
|---|---|---|---|---|
| 1 | **Relative path** | `./script.sh` | Yes | New subshell |
| 2 | **Absolute path** | `/home/user/scripts/script.sh` | Yes | New subshell |
| 3 | **Explicit interpreter** | `bash script.sh` | No | New subshell |
| 4 | **Source command** | `source script.sh` or `. script.sh` | No | **Current shell** |

> The recommended method for most situations: Method 1 — `./script.sh`

---

### 2. Method 1 — Relative Path (`./script.sh`)

The most common and recommended way to run a script.

```bash
# Navigate to the script's directory first
cd ~/scripts

# Run with relative path
./first_script.sh
```

- `./` means "in the **current directory**"
- Requires execute permission: `chmod +x script.sh`
- Script runs in a **new subshell**
- The interpreter used is determined by the **shebang line**

```bash
# If no shebang → bash is used (default shell)
# If #!/bin/bash → bash is used
# If #!/usr/bin/python3 → python3 is used
```

---

### 3. Method 2 — Absolute Path

Run the script from **any directory** by providing the full path.

```bash
# Run from anywhere on the system
/home/student/scripts/first_script.sh

# Using ~ (home directory shortcut)
~/scripts/first_script.sh
```

- Requires execute permission: `chmod +x script.sh`
- Script runs in a **new subshell**
- Interpreter determined by shebang (same as Method 1)
- Useful when **scheduling scripts** (cron jobs) or calling from other scripts

---

### 4. Method 3 — Explicit Interpreter (`bash script.sh`)

Explicitly state which interpreter should run the script.

```bash
bash first_script.sh     # run with bash
python3 my_script        # run with python3
sh first_script.sh       # run with sh
```

Key differences from Methods 1 and 2:

- Execute permission is NOT required
- The shebang is OVERRIDDEN — your explicit interpreter wins
- Still runs in a new subshell

> If your script has `#!/usr/bin/python3` but you run `bash script.sh`, bash will try to interpret Python code — and fail.

---

### 5. Method 4 — Source Command (`source` or `.`)

The most **different** method — runs the script in the **current shell** instead of a new subshell.

```bash
# Using the source keyword
source first_script.sh

# Using the shorthand dot notation (identical behaviour)
. first_script.sh
```

- Does NOT require execute permission
- Does NOT spawn a new subshell
- Script runs inside the current shell session
- `source` and `. ` (dot-space) are exactly the same

---

### 6. The Critical Difference: Subshell vs Current Shell

This is the **most important concept in this episode**.

#### Running with `./` — spawns a subshell:

```
Your Terminal Shell (Parent)
        |
   New Subshell spawned  <- script runs here
        |
   Script: cd /tmp  ->  MY_VAR="hello"
        |
   Subshell exits — ALL changes are gone
        |
Your Terminal Shell (Parent) — unaffected, still in original dir
```

Variables, directory changes, and environment changes made inside a subshell **do not affect the parent shell**.

#### Running with `source` — uses the current shell:

```
Your Terminal Shell
        |
   source script.sh  <- script runs INSIDE this shell
        |
   cd /tmp runs HERE -> MY_VAR="hello" set HERE
        |
Your Terminal Shell — you are now in /tmp, MY_VAR is set
```

**Practical proof:**
```bash
# Script content: env_test.sh
#!/bin/bash
cd /tmp
MY_VAR="set by script"

# Run as subshell:
./env_test.sh
pwd           # still in original dir — subshells cd had no effect
echo $MY_VAR  # empty — variable did not leak out

# Run with source:
source env_test.sh
pwd           # /tmp — current shell was changed!
echo $MY_VAR  # "set by script" — variable IS available
```

---

### 7. When to Use Each Method

| Scenario | Best Method |
|---|---|
| Running a regular automation/utility script | `./script.sh` |
| Running from a cron job or another script | `/full/path/script.sh` |
| Quick test without setting permissions | `bash script.sh` |
| Script needs to change YOUR current directory | `source script.sh` |
| Script defines variables YOU want to use after | `source script.sh` |
| Loading a config or environment file | `source ~/.bashrc` |
| Testing POSIX shell compatibility | `sh script.sh` |

---

### 8. Section 01 Recap

| Episode | Topic |
|---|---|
| Ep 1 — Bash Aliases | Creating, persisting, and removing bash aliases |
| Ep 3 — Shell & Scripts | What a shell is, types of shells, what a script is |
| Ep 4 — First Script & PATH | Creating scripts, chmod, the PATH variable |
| Ep 6 — The Shebang | #! telling the OS which interpreter to use |
| Ep 7 — Comments | Writing clean, well-commented code |
| Ep 8 — Running Scripts | All four execution methods and when to use each |

> Next up: Section 02 — Variables, Parameters, Shell Expansions, User Input, and much more!

---

## 🔑 Key Takeaways

- There are **four ways** to run a Bash script: `./`, absolute path, `bash script.sh`, and `source`.
- **`./script.sh` is the recommended method** — simple, clear, and conventional.
- Methods 1, 2, and 3 all run in a **new subshell** — changes don't affect your current terminal.
- **`source script.sh`** (or `. script.sh`) runs in the **current shell** — changes DO affect your terminal.
- `source` and `.` (dot space) are **completely identical** — just different syntax.
- The `bash script.sh` method **overrides the shebang** — your explicit interpreter wins.
- `bash script.sh` and `source script.sh` both work **without** execute permission.
- Use `source` when a script needs to **modify your current environment**.

---

## 💡 Tips & Tricks

**Mental model for subshell vs current shell:**
Think of a subshell like a disposable glove — you put it on to do work, but when you take it off (subshell exits), nothing persists. `source` is like doing the work bare-handed — everything directly affects you.

**Confirm you are in a subshell using the PID:**
```bash
echo "Your shell PID: $$"
echo 'echo "Script PID: $$"' > /tmp/pid_test.sh
chmod +x /tmp/pid_test.sh
./pid_test.sh           # different PID = new subshell
source /tmp/pid_test.sh # same PID = current shell
```

**Why `source ~/.bashrc` works without restarting the terminal:**
It runs the config file inside your current shell — so new aliases and variables become immediately available.

**Add permission AND run in one line:**
```bash
chmod +x script.sh && ./script.sh
```

**Run a script and save output to a log:**
```bash
./script.sh | tee output.log       # print to screen AND save to file
./script.sh > output.log 2>&1      # save stdout + stderr silently
```

**Check permissions before running:**
```bash
ls -l script.sh   # look for x in permissions e.g. -rwxr-xr-x
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Run Your Script All Four Ways

**Goal:** Experience every method of running a script.

```bash
cd ~/scripts

# Method 1: Relative path
./first_script.sh

# Method 2: Absolute path
/home/$(whoami)/scripts/first_script.sh

# Method 3: Explicit interpreter — remove permission first to prove it works
chmod -x first_script.sh
bash first_script.sh        # works without +x
chmod +x first_script.sh    # restore permission

# Method 4: Source (two ways — identical)
source first_script.sh
. first_script.sh
```

---

### Exercise 2 — Explicit Interpreter Overrides the Shebang

**Goal:** Prove that `bash script.sh` ignores the shebang.

```bash
cat > ~/scripts/shebang_override_test << 'EOF'
#!/usr/bin/python3
print("Hello from Python!")
EOF
chmod +x ~/scripts/shebang_override_test

# Run normally — shebang respected, Python runs it
./shebang_override_test

# Force bash to run it — shebang overridden, bash tries Python code, error
bash shebang_override_test
```

---

### Exercise 3 — Subshell vs Current Shell (The Big Experiment)

**Goal:** See with your own eyes that `./` and `source` behave differently.

**Step 1:** Create a test script
```bash
cat > ~/scripts/env_test.sh << 'EOF'
#!/bin/bash
cd /tmp
MY_VAR="I was set by the script"
echo "Inside script — PWD   : $(pwd)"
echo "Inside script — MY_VAR: $MY_VAR"
EOF
chmod +x ~/scripts/env_test.sh
```

**Step 2:** Run as a subshell
```bash
cd ~/scripts
echo "BEFORE: PWD=$(pwd) | MY_VAR='$MY_VAR'"
./env_test.sh
echo "AFTER ./: PWD=$(pwd) | MY_VAR='$MY_VAR'"
```
Expected: PWD and MY_VAR are **unchanged** after the subshell exits.

**Step 3:** Run with source
```bash
cd ~/scripts
echo "BEFORE: PWD=$(pwd) | MY_VAR='$MY_VAR'"
source env_test.sh
echo "AFTER source: PWD=$(pwd) | MY_VAR='$MY_VAR'"
```
Expected: PWD is now `/tmp` and MY_VAR is set — the current shell was modified.

---

### Exercise 4 — PID Proof (Subshell is a Separate Process)

**Goal:** Use process IDs to confirm subshells are truly separate processes.

```bash
cat > ~/scripts/pid_test.sh << 'EOF'
#!/bin/bash
echo "Script PID: $$"
EOF
chmod +x ~/scripts/pid_test.sh

echo "Your current shell PID: $$"

echo "Running with ./ (subshell):"
./pid_test.sh           # different PID — new process was created

echo "Running with source (current shell):"
source pid_test.sh      # SAME PID — no new process
```

---

### Exercise 5 — No Permission Test

**Goal:** Confirm `bash` and `source` work without execute permission.

```bash
cat > ~/scripts/no_perm_test.sh << 'EOF'
#!/bin/bash
echo "Script ran successfully!"
EOF

# Remove ALL permissions
chmod 000 ~/scripts/no_perm_test.sh
ls -l ~/scripts/no_perm_test.sh   # shows: ----------

./no_perm_test.sh                       # Permission denied
bash ~/scripts/no_perm_test.sh          # works!
source ~/scripts/no_perm_test.sh        # works!

# Restore permissions
chmod +x ~/scripts/no_perm_test.sh
```

---

### Exercise 6 — Section 01 Capstone Script

**Goal:** Write a script that demonstrates all Section 01 concepts.

```bash
cat > ~/scripts/section01_summary.sh << 'EOF'
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : section01_summary.sh
# Purpose : Section 01 capstone — demonstrates all core concepts
# ─────────────────────────────────────────────────────

echo "======================================================"
echo "       Section 01 — Bash Shell Scripting Summary      "
echo "======================================================"

echo ""
echo "── Shell & Environment ──────────────────────────────"
echo "  Shell      : $SHELL"
echo "  Bash ver   : $BASH_VERSION"
echo "  User       : $(whoami)"
echo "  Home       : $HOME"
echo "  Script     : $0"
echo "  Script PID : $$"

echo ""
echo "── PATH Directories ─────────────────────────────────"
echo $PATH | tr ':' '\n' | nl

echo ""
echo "── Four Ways to Run This Script ─────────────────────"
echo "  1. ./section01_summary.sh          (relative path)"
echo "  2. ~/scripts/section01_summary.sh  (absolute path)"
echo "  3. bash section01_summary.sh       (explicit interpreter)"
echo "  4. source section01_summary.sh     (current shell)"

echo ""
echo "======================================================"
echo "  Section 01 Complete! Next: Variables & Parameters  "
echo "======================================================"
EOF
chmod +x ~/scripts/section01_summary.sh
./section01_summary.sh
```

---

### 🧠 Quiz — Test Yourself

1. What are the **four ways** to run a Bash script?
2. Which method is **recommended** for everyday use?
3. Which two methods require `chmod +x`?
4. Which two methods do NOT require execute permission?
5. What does `./` mean before a script name?
6. What is the key difference between `./script.sh` and `source script.sh`?
7. If a script has `#!/usr/bin/python3` but you run `bash script.sh`, which interpreter runs it?
8. What is the shorthand symbol for `source`? Write the full syntax.
9. Why does `source ~/.bashrc` apply alias changes immediately?
10. If a script runs `cd /tmp`, will your terminal be in `/tmp` after `./script.sh`? What about `source script.sh`?

---

## 📎 Quick Reference Card

```
FOUR WAYS TO RUN A BASH SCRIPT
──────────────────────────────────────────────────────────────
./script.sh                   RECOMMENDED — relative path
~/scripts/script.sh           Absolute path (run from anywhere)
bash script.sh                Explicit interpreter (no chmod needed)
source script.sh              Runs in CURRENT shell
. script.sh                   Same as source (dot shorthand)

PERMISSION REQUIREMENTS
──────────────────────────────────────────────────────────────
./script.sh          chmod +x REQUIRED
/path/script.sh      chmod +x REQUIRED
bash script.sh       NO chmod needed
source script.sh     NO chmod needed

SUBSHELL vs CURRENT SHELL
──────────────────────────────────────────────────────────────
./script.sh          NEW SUBSHELL — cd, variables do NOT persist
bash script.sh       NEW SUBSHELL — same behaviour
source script.sh     CURRENT SHELL — cd, variables DO persist

SHEBANG INTERACTION
──────────────────────────────────────────────────────────────
./script.sh          Shebang IS respected
bash script.sh       Shebang is OVERRIDDEN (bash runs it regardless)
source script.sh     Shebang is IGNORED (current shell runs it)

COMMON USE CASES
──────────────────────────────────────────────────────────────
./script.sh          Everyday scripts, automation, utilities
/path/script.sh      Cron jobs, called from other scripts
bash script.sh       Quick testing without chmod
source script.sh     Loading configs (.bashrc), setting env vars
```

---

## 🗂️ Notion Tags (suggested)

`bash` `running-scripts` `subshell` `source` `chmod` `shell-scripting` `execution` `ZTM` `module-2` `section-01`

---

## 🏁 Section 01 Complete!

You have finished **Section 01 — Intro to Bash Shell Scripting**. Here is what you have mastered:

- Bash Aliases — shortcuts for long commands, persisting in `.bashrc`
- Shell & Scripts — what a shell is, types of shells, script definition
- First Script & PATH — creating scripts, chmod, the PATH variable
- The Shebang — `#!` and how the OS selects an interpreter
- Comments — clean code practices, single and multi-line comments
- Running Scripts — all four execution methods and when to use each

*Next: Module 2 | Section 02 — Variables, Parameters, Shell Expansions & User Input*
