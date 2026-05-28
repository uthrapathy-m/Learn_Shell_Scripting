# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 02 — Variables
> **Episode:** 8. Special Parameters
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

The final piece of the parameters puzzle. After variables (referenced by name) and positional parameters (referenced by number), this episode covers **special parameters** — auto-set, symbol-referenced parameters with specific purposes. These are among the most powerful and most-used tools in any Bash script: checking if a command succeeded, counting arguments, looping over all arguments, and identifying the running process.

---

## 🧠 Theory

### 1. What Are Special Parameters?

Special parameters are **predefined, read-only** parameters set automatically by the Bash shell. Unlike variables or positional parameters:

- They are referenced using **symbols** (not names or numbers)
- You **cannot assign values** to them — they are read-only
- The shell maintains their values automatically

```bash
# You can READ them:
echo $#     # → number of arguments
echo $?     # → exit status of last command

# You CANNOT write to them:
$#=5        # → syntax error
$?=0        # → syntax error
```

---

### 2. The Complete Special Parameters Table

| Parameter | Name | What it expands to |
|---|---|---|
| `$0` | Script name | The name of the script as it was invoked |
| `$#` | Argument count | Number of positional parameters (arguments) passed |
| `$@` | All arguments (array) | All positional parameters as **separate words** |
| `$*` | All arguments (string) | All positional parameters as a **single string** |
| `$?` | Exit status | Exit status of the **most recently** executed foreground command |
| `$$` | Shell PID | Process ID of the **current shell** |
| `$!` | Last background PID | Process ID of the most recent **background** command |

> 📌 **Note:** `$0` was introduced in the positional parameters episode — it's both a positional and a special parameter.

---

### 3. `$#` — The Argument Count

`$#` holds the **total number of arguments** passed to the script (not counting `$0`).

```bash
# Script: ./show_count.sh A B C 1 2 3
echo $#     # → 6
```

**Most common use — validate the correct number of arguments:**
```bash
#!/bin/bash
if [[ "$#" -ne 2 ]]; then
    echo "Error: Expected 2 arguments, got $#"
    echo "Usage: $0 <source> <destination>"
    exit 1
fi
echo "Source: $1, Destination: $2"
```

```bash
./script.sh                     # → Error: Expected 2 arguments, got 0
./script.sh file.txt            # → Error: Expected 2 arguments, got 1
./script.sh file.txt /backup/   # → Source: file.txt, Destination: /backup/
```

---

### 4. `$@` and `$*` — All Arguments

Both expand to **all positional parameters** at once. The difference matters when they are **quoted**:

| | Unquoted | Quoted |
|---|---|---|
| `$@` | All args, split by IFS | `"$@"` → each arg as a **separate quoted word** ✅ |
| `$*` | All args, split by IFS | `"$*"` → all args joined as **one single string** |

```bash
# Script called as: ./script.sh "hello world" foo bar

echo $@     # → hello world foo bar   (word-split — "hello world" broken!)
echo $*     # → hello world foo bar   (same)

echo "$@"   # → "hello world" "foo" "bar"  (3 separate words — CORRECT)
echo "$*"   # → "hello world foo bar"      (1 single string — usually wrong)
```

> ✅ **Best practice: Always use `"$@"` (quoted)** when passing all arguments to another command or looping over them. It preserves arguments that contain spaces.

**Why `"$@"` matters — passing args to another command:**
```bash
# Safely pass ALL arguments to another command:
./my_wrapper.sh "$@"

# Loop over all arguments safely:
for arg in "$@"; do
    echo "Processing: $arg"
done
```

---

### 5. `$?` — The Exit Status

Every command or script that runs in Linux returns an **exit status** (also called a return code) — a number that indicates whether it succeeded or failed.

```
0       = SUCCESS — the command ran without error
1-255   = FAILURE — an error occurred (specific code depends on the command)
```

`$?` captures the exit status of the **most recently executed foreground command**.

```bash
ls /home
echo $?     # → 0  (ls succeeded)

cat /etc/shadow
echo $?     # → 1  (permission denied — error)

cat /nonexistent_file
echo $?     # → 1  (file not found — error)

nonexistent_command
echo $?     # → 127  (command not found)
```

**⚠️ Critical rule — read `$?` IMMEDIATELY:**
`$?` is overwritten by every command. If you run anything between the command and `echo $?`, you'll lose it.

```bash
ls /home
# DON'T DO THIS:
echo "Some other command"     # ← this OVERWRITES $?
echo $?                       # → 0 (exit status of echo, not ls!)

# DO THIS:
ls /home
echo $?                       # → immediately capture it
```

**Common exit status codes:**

| Code | Meaning |
|---|---|
| `0` | Success |
| `1` | General error |
| `2` | Misuse of shell built-ins |
| `126` | Command found but not executable |
| `127` | Command not found |
| `128+N` | Fatal error signal N (e.g. `130` = Ctrl+C) |
| `130` | Script terminated by Ctrl+C |

---

### 6. `$$` — The Shell's Process ID (PID)

`$$` holds the **Process ID** of the current shell. Each running process on Linux has a unique PID.

```bash
echo $$     # → e.g. 12345  (your shell's PID)
```

**Important subshell behaviour:**
In a subshell, `$$` still shows the **parent shell's PID** — not the subshell's:

```bash
echo "Parent PID: $$"
bash -c 'echo "Subshell - $$ (still parent PID!)"'
```

> 💡 To get the actual subshell PID, use `$BASHPID` instead of `$$`.

**Practical use — create unique temp filenames:**
```bash
# Without $$: two simultaneous script runs would overwrite each other
TMPFILE="/tmp/myscript_temp.txt"

# With $$: each run creates a uniquely-named temp file
TMPFILE="/tmp/myscript_$$.txt"
echo "Created temp file: $TMPFILE"
# → /tmp/myscript_12345.txt  (unique per run)

# Clean up when done
rm "$TMPFILE"
```

---

### 7. `$!` — Last Background Process PID

`$!` stores the PID of the **most recently backgrounded command** (commands run with `&`).

```bash
sleep 30 &          # run in background
echo "Background PID: $!"    # → the PID of that sleep command

# You can use it to wait for or kill the background process:
wait $!             # wait for it to finish
kill $!             # kill it
```

---

### 8. Seeing All Special Parameters in a Script

Here's the demo script the instructor builds to show all special parameters at once:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : special_params.sh
# Purpose : Display all special parameter values
# Usage   : ./special_params.sh A B C 1 2 3
# ─────────────────────────────────────────────────────

echo "Script name   (\$0) : $0"
echo "Argument count (\$#) : $#"
echo "All args - \$@      : $@"
echo "All args - \$*      : $*"
echo "Exit status   (\$?) : $?"
echo "Shell PID     (\$\$) : $$"
```

Running with arguments:
```bash
./special_params.sh A B C 1 2 3
```

Output:
```
Script name   ($0) : ./special_params.sh
Argument count ($#) : 6
All args - $@      : A B C 1 2 3
All args - $*      : A B C 1 2 3
Exit status   ($?) : 0
Shell PID     ($$) : 12345
```

---

## 🔑 Key Takeaways

- Special parameters are **read-only** — you can reference them but never assign to them.
- `$#` — number of arguments (use for **argument count validation**).
- `$@` — all arguments as **separate words** — always use as `"$@"` (quoted).
- `$*` — all arguments as **one string** — use `"$@"` instead in almost all cases.
- `$?` — exit status of last command: **`0` = success, non-zero = error**.
- **Read `$?` immediately** after the command — every subsequent command overwrites it.
- `$$` — current shell's PID — useful for **unique temp filenames**.
- In a subshell, `$$` still shows the **parent's PID** — use `$BASHPID` for subshell PID.
- `$!` — PID of last backgrounded command — useful for `wait` and `kill`.
- `$0` — the script name — use `basename "$0"` for a clean name in messages.

---

## 💡 Tips & Tricks

**🔹 The most important special parameter in real scripts is `$?`:**
```bash
cp source.txt /backup/
if [[ "$?" -ne 0 ]]; then
    echo "Error: backup failed!"
    exit 1
fi
echo "Backup succeeded."
```

**🔹 Cleaner way to check exit status — test the command directly:**
```bash
# Instead of checking $?, test the command directly:
if cp source.txt /backup/; then
    echo "Backup succeeded."
else
    echo "Backup failed!"
    exit 1
fi
```
Both are valid — direct testing is cleaner and avoids the "overwrite" issue.

**🔹 `$#` is zero when no arguments are given:**
```bash
./script.sh           # → $# = 0
./script.sh a b c     # → $# = 3
```

**🔹 `"$@"` is the safe way to pass all args:**
```bash
# Safely forward all script arguments to another command:
tar -czvf backup.tar.gz "$@"
```

**🔹 Create unique temp files with `$$`:**
```bash
declare -r TMPFILE="/tmp/${0##*/}_$$.tmp"
# Creates: /tmp/script_name_12345.tmp
trap "rm -f $TMPFILE" EXIT    # auto-delete on exit
```

**🔹 Capture and store `$?` before it's overwritten:**
```bash
some_command
exit_code=$?     # save it NOW before anything else runs
echo "Logging exit code: $exit_code"
if [[ $exit_code -ne 0 ]]; then
    echo "Command failed with exit code: $exit_code"
fi
```

**🔹 Common exit codes worth memorising:**
```bash
exit 0    # success
exit 1    # general error (most common)
exit 2    # misuse of command or invalid argument
exit 127  # command not found
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — See All Special Parameters in Action

**Goal:** Build and run the instructor's demo script with all special parameters.

Create `~/scripts/special_params.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : special_params.sh
# Purpose : Show all special parameter values
# Usage   : ./special_params.sh [arguments...]
# ─────────────────────────────────────────────────────

echo "==========================================="
echo "         SPECIAL PARAMETERS DEMO"
echo "==========================================="
echo ""
echo "  \$0  (script name)       : $0"
echo "  \$#  (argument count)    : $#"
echo "  \$@  (all args - @)      : $@"
echo "  \$*  (all args - *)      : $*"
echo "  \$?  (exit status)       : $?"
echo "  \$\$  (shell PID)         : $$"
echo ""
echo "==========================================="
```

```bash
chmod +x ~/scripts/special_params.sh

# Run with no arguments
./special_params.sh

# Run with several arguments
./special_params.sh A B C 1 2 3

# Run with quoted argument containing spaces
./special_params.sh "hello world" foo bar
```

---

### Exercise 2 — `$#` Argument Count Validation

**Goal:** Use `$#` to write a robust argument-checking script.

Create `~/scripts/count_check.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : count_check.sh
# Purpose : Demonstrate $# for argument validation
# Usage   : ./count_check.sh <arg1> <arg2>
# ─────────────────────────────────────────────────────

echo "You provided $# argument(s)."

# Require exactly 2 arguments
if [[ "$#" -eq 0 ]]; then
    echo "Error: No arguments provided."
    echo "Usage: $0 <first> <second>"
    exit 1
elif [[ "$#" -lt 2 ]]; then
    echo "Error: Too few arguments. Need exactly 2, got $#."
    exit 1
elif [[ "$#" -gt 2 ]]; then
    echo "Error: Too many arguments. Need exactly 2, got $#."
    exit 1
fi

echo "First : $1"
echo "Second: $2"
echo "All OK!"
```

```bash
chmod +x ~/scripts/count_check.sh

# Test all cases
./count_check.sh
./count_check.sh one
./count_check.sh one two
./count_check.sh one two three
```

---

### Exercise 3 — `$@` vs `$*` — The Critical Difference

**Goal:** See the difference between `$@` and `$*` when arguments contain spaces.

Create `~/scripts/at_vs_star.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : at_vs_star.sh
# Purpose : Demonstrate the difference between $@ and $*
# Usage   : ./at_vs_star.sh "hello world" foo bar
# ─────────────────────────────────────────────────────

echo "=== Arguments received: $# ==="
echo ""

echo "--- Unquoted \$@ ---"
for arg in $@; do
    echo "  arg: [$arg]"
done

echo ""
echo "--- Quoted \"\$@\" (CORRECT) ---"
for arg in "$@"; do
    echo "  arg: [$arg]"
done

echo ""
echo "--- Quoted \"\$*\" (WRONG for loops) ---"
for arg in "$*"; do
    echo "  arg: [$arg]"
done
```

```bash
chmod +x ~/scripts/at_vs_star.sh
./at_vs_star.sh "hello world" foo bar
```

**Observe carefully:**
- `"$@"` → 3 separate args: `[hello world]`, `[foo]`, `[bar]`
- `"$*"` → 1 combined string: `[hello world foo bar]`

---

### Exercise 4 — `$?` Exit Status in Practice

**Goal:** Use `$?` to check if commands succeed or fail.

```bash
# In the terminal — try each block:

# SUCCESS case (exit 0)
ls /home
echo "Exit status: $?"     # → 0

# FAILURE case — permission denied
cat /etc/shadow
echo "Exit status: $?"     # → 1

# FAILURE case — file not found
cat /nonexistent_file.txt
echo "Exit status: $?"     # → 1

# FAILURE case — command not found
nonexistent_command
echo "Exit status: $?"     # → 127

# CRITICAL — $? is overwritten every command
ls /home
echo "Some other output"   # ← THIS overwrites $?
echo "Exit status: $?"     # → 0 (of echo, NOT of ls!)
```

Now create a script that saves `$?` before it's lost:

Create `~/scripts/exit_status_demo.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : exit_status_demo.sh
# Purpose : Demonstrate correct $? usage
# ─────────────────────────────────────────────────────

echo "=== Test 1: Successful command ==="
ls /home > /dev/null 2>&1
status=$?     # save IMMEDIATELY
echo "ls /home exit status: $status"
[[ $status -eq 0 ]] && echo "  Result: SUCCESS" || echo "  Result: FAILURE"

echo ""
echo "=== Test 2: Permission denied ==="
cat /etc/shadow > /dev/null 2>&1
status=$?
echo "cat /etc/shadow exit status: $status"
[[ $status -eq 0 ]] && echo "  Result: SUCCESS" || echo "  Result: FAILURE"

echo ""
echo "=== Test 3: File not found ==="
cat /nonexistent_file > /dev/null 2>&1
status=$?
echo "cat /nonexistent exit status: $status"
[[ $status -eq 0 ]] && echo "  Result: SUCCESS" || echo "  Result: FAILURE"

echo ""
echo "=== Test 4: Command not found ==="
fake_command > /dev/null 2>&1
status=$?
echo "fake_command exit status: $status"
[[ $status -eq 0 ]] && echo "  Result: SUCCESS" || echo "  Result: FAILURE"
```

```bash
chmod +x ~/scripts/exit_status_demo.sh
./exit_status_demo.sh
```

---

### Exercise 5 — `$$` Unique Temp Files

**Goal:** Use `$$` to create uniquely-named temporary files.

```bash
# In your terminal:
echo "My shell PID: $$"

# Create a unique temp file
tmpfile="/tmp/mytest_$$.txt"
echo "Temp file: $tmpfile"

echo "This is temp content" > "$tmpfile"
cat "$tmpfile"
ls -l "$tmpfile"

# Clean it up
rm "$tmpfile"
echo "Cleaned up: $tmpfile"
```

Now extend this to a script:

Create `~/scripts/temp_file_demo.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : temp_file_demo.sh
# Purpose : Demonstrate $$ for unique temp files
# ─────────────────────────────────────────────────────

script_name=$(basename "$0")
tmpfile="/tmp/${script_name}_$$.tmp"

echo "Script PID   : $$"
echo "Temp file    : $tmpfile"
echo ""

# Write some data to the temp file
echo "Processing data..." > "$tmpfile"
date >> "$tmpfile"
hostname >> "$tmpfile"
whoami >> "$tmpfile"

echo "=== Temp file contents ==="
cat "$tmpfile"
echo ""

# Clean up
rm "$tmpfile"
echo "Temp file cleaned up."
```

```bash
chmod +x ~/scripts/temp_file_demo.sh

# Run it twice simultaneously to prove uniqueness
./temp_file_demo.sh &
./temp_file_demo.sh &
wait
```

---

### Exercise 6 — Section 02 Capstone: Enhanced dropPackets with Special Parameters

**Goal:** Upgrade the project script from Ep7 using what you now know about special parameters.

Create `~/scripts/dropPackets_final.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : dropPackets_final.sh
# Purpose : Block IPs using iptables — uses all Section 02 concepts
# Usage   : sudo ./dropPackets_final.sh <IP> [IP2] [IP3] ...
# Example : sudo ./dropPackets_final.sh 192.168.1.55 10.0.0.1
# ─────────────────────────────────────────────────────

# Root check
if [[ "$EUID" -ne 0 ]]; then
    echo "Error: Run as root. Usage: sudo $0 <IP...>"
    exit 1
fi

# Argument count check ($#)
if [[ "$#" -lt 1 ]]; then
    echo "Error: No IP addresses provided."
    echo "Usage: $0 <IP1> [IP2] [IP3] ..."
    echo "Example: $0 192.168.1.55 10.0.0.1"
    exit 1
fi

echo "============================================="
echo "  IP Blocker — Script PID: $$"
echo "  Blocking $# IP address(es)..."
echo "============================================="

# Loop over ALL arguments using "$@"
blocked=0
for ip in "$@"; do
    iptables -I INPUT -s "$ip" -j DROP
    status=$?     # capture exit status immediately
    if [[ "$status" -eq 0 ]]; then
        echo "  [OK]    Blocked: $ip"
        ((blocked++))
    else
        echo "  [FAIL]  Could not block: $ip (exit: $status)"
    fi
done

echo ""
echo "Done. $blocked/$# IP(s) successfully blocked."
echo ""
echo "To undo all rules added by this script, run:"
for ip in "$@"; do
    echo "  sudo iptables -D INPUT -s $ip -j DROP"
done
```

```bash
chmod +x ~/scripts/dropPackets_final.sh
# sudo ./dropPackets_final.sh 10.0.0.1 10.0.0.2 10.0.0.3
```

---

### 🧠 Quiz — Test Yourself

1. What makes special parameters different from regular variables?
2. Can you assign a value to `$#`? Why or why not?
3. What does `$#` contain?
4. If a script is called as `./script.sh a b c`, what is `$#`?
5. What is the difference between `"$@"` and `"$*"` when an argument contains spaces?
6. Which is safer to use in scripts — `"$@"` or `"$*"`? Why?
7. What does `$?` store?
8. What does an exit status of `0` mean? What does a non-zero exit status mean?
9. Why must you read `$?` immediately after a command?
10. What common exit code means "command not found"?
11. What does `$$` store?
12. In a subshell, does `$$` show the subshell's PID or the parent's PID?
13. What special parameter holds the PID of the last background command?
14. Write a one-line argument check that exits with an error message if no arguments are given.

---

## 📎 Quick Reference Card

```
SPECIAL PARAMETERS — COMPLETE TABLE
──────────────────────────────────────────────────────────────
$0      Script name as invoked (./script.sh, /path/script.sh, etc.)
$#      Number of positional arguments passed to the script
$@      All arguments as SEPARATE words  → always use as "$@"
$*      All arguments as ONE string      → avoid in most cases
$?      Exit status of last foreground command (0=success, else error)
$$      PID of the CURRENT shell (parent PID even inside subshells)
$BASHPID  PID of the current shell INCLUDING subshells
$!      PID of the most recent BACKGROUND command (&)

ALL PARAMETERS — THE FULL PICTURE
──────────────────────────────────────────────────────────────
$name        Variable — referenced by name (user-defined)
$1 $2 ...    Positional — referenced by number (script arguments)
$# $@ $?     Special — referenced by symbol (auto-set, read-only)

EXIT STATUS ($?)
──────────────────────────────────────────────────────────────
0       Success
1       General error
2       Misuse of shell built-ins
126     Command found but not executable
127     Command not found
128+N   Fatal signal N received
130     Script killed by Ctrl+C (128+2)

PATTERNS
──────────────────────────────────────────────────────────────
# Validate argument count:
if [[ "$#" -ne 2 ]]; then
    echo "Usage: $0 <arg1> <arg2>"; exit 1
fi

# Save $? before it's overwritten:
some_command
exit_code=$?
[[ $exit_code -ne 0 ]] && echo "Failed: $exit_code"

# Loop over all arguments safely:
for arg in "$@"; do echo "$arg"; done

# Pass all arguments to another command:
other_command "$@"

# Unique temp file:
tmpfile="/tmp/$(basename $0)_$$.tmp"
trap "rm -f $tmpfile" EXIT    # auto-cleanup on exit

# Background PID:
sleep 60 &
bg_pid=$!
wait $bg_pid
```

---

## 🗂️ Notion Tags (suggested)

`bash` `special-parameters` `exit-status` `argument-count` `pid` `shell-scripting` `automation` `ZTM` `module-2` `section-02`

---

## 🏁 Section 02 Complete!

> You've finished **Section 02 — Variables**. Here is what you have mastered:
>
> ✅ **Variables in Bash** — creating, reading, listing, removing, constants
> ✅ **Variable Expansion & Quoting** — `$var`, `${var}`, single/double quotes, backslash
> ✅ **Environment & Shell Local Variables** — `export`, `env`, `printenv`, `set`, persistence
> ✅ **Getting User Input** — `read`, `-p`, `-s`, `-r`, `-n`, `-t`, `$REPLY`
> ✅ **Positional Parameters** — `$1`–`$9`, `${10}+`, `$0`, default values
> ✅ **Project** — IP blocker with `iptables` using `read` and `$1`
> ✅ **Special Parameters** — `$#`, `$@`, `$*`, `$?`, `$$`, `$!`

*→ Next: Module 2 | Section 03 — Shell Expansions*
