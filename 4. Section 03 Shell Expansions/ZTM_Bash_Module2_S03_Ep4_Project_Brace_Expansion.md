# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 03 — Shell Expansions
> **Episode:** 4. Project — Brace Expansion
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

This is the **brace expansion project** — a real-world DevOps scenario where you must create an entire application log directory structure for three months using **only two commands**. Without brace expansion, this task would require 183 separate commands. With it, two commands do the entire job. This episode is a masterclass in turning a time-consuming chore into an elegant one-liner.

---

## 🧠 Theory

### 1. The Real-World Scenario

> A DevOps team is deploying a new application that needs **heavy monitoring** during its first phase. The application must write to a **separate log file for each day**, so logs are easy to locate and isolate by date.
>
> Your task: Create the complete directory and file structure under `/var/log/app/`.

---

### 2. The Required Structure

```
/var/log/app/
├── january/
│   ├── 01/
│   │   └── log.daily
│   ├── 02/
│   │   └── log.daily
│   ├── ...
│   └── 31/
│       └── log.daily
├── february/
│   ├── 01/
│   │   └── log.daily
│   ├── ...
│   └── 28/
│       └── log.daily
└── march/
    ├── 01/
    │   └── log.daily
    ├── ...
    └── 31/
        └── log.daily
```

**Numbers at a glance:**
| Month | Directories | Log files |
|---|---|---|
| January | 31 | 31 |
| February | 28 | 28 |
| March | 31 | 31 |
| **Total** | **90 day dirs + 3 month dirs = 93** | **90** |

---

### 3. Without Brace Expansion — 183 Commands

Doing this the naive way:

```bash
# 3 commands for month directories
mkdir /var/log/app/january
mkdir /var/log/app/february
mkdir /var/log/app/march

# 31 commands for January day directories
mkdir /var/log/app/january/01
mkdir /var/log/app/january/02
# ... (31 total)

# 31 commands to create January log files
touch /var/log/app/january/01/log.daily
touch /var/log/app/january/02/log.daily
# ... (31 total)

# Repeat for February (28+28) and March (31+31)
# Grand total: 183 commands!
```

> ⏱️ Even at 10 seconds per command, that's **30 minutes of repetitive typing** — and one typo anywhere corrupts the structure.

---

### 4. With Brace Expansion — 2 Commands ✅

#### Command 1 — Create ALL directories:

```bash
sudo mkdir -p /var/log/app/{january/{01..31},february/{01..28},march/{01..31}}
```

**Expansion breakdown:**
```
/var/log/app/{january/{01..31},february/{01..28},march/{01..31}}
                  ↓ outer brace expands to three branches:
/var/log/app/january/{01..31}        → 31 dirs inside january/
/var/log/app/february/{01..28}       → 28 dirs inside february/
/var/log/app/march/{01..31}          → 31 dirs inside march/
                  ↓ inner sequences expand:
/var/log/app/january/01
/var/log/app/january/02
...
/var/log/app/march/31
```

> 🔑 **Nested brace expansion in action:** The outer `{january/...,february/...,march/...}` is a string list. Each item contains an inner `{01..31}` or `{01..28}` sequence. Both expand together.

#### Command 2 — Create ALL log files:

```bash
sudo touch /var/log/app/{january/{01..31},february/{01..28},march/{01..31}}/log.daily
```

**The only difference from Command 1:** the suffix `/log.daily` is appended after the closing `}`. This creates a `log.daily` file inside every day directory.

---

### 5. Why Zero-Padding (`01` not `1`)

The day directories are named `01`, `02`, ... `31` (with leading zeros) rather than `1`, `2`, ... `31`. This ensures:

- Alphabetical sorting = chronological sorting
- `ls` shows `01` before `02` before `10` — correct date order
- Tools that process these directories alphabetically work correctly

```bash
# Wrong — sorts as: 1, 10, 11, 12, ..., 19, 2, 20, ...
mkdir -p january/{1..31}

# Right — sorts as: 01, 02, 03, ..., 31
mkdir -p january/{01..31}
```

---

### 6. Always Preview with `echo` First

Before running a command that creates files, **test your brace expansion with `echo`**:

```bash
# Preview Command 1 — no files created
echo mkdir -p /var/log/app/{january/{01..31},february/{01..28},march/{01..31}}

# Preview Command 2 — no files created
echo touch /var/log/app/{january/{01..31},february/{01..28},march/{01..31}}/log.daily
```

This shows you **exactly** what will be created before committing.

---

### 7. Verification — Confirming the Structure

After running the commands, verify the result:

```bash
# Count total directories created
find /var/log/app -type d | wc -l
# → 94  (93 dirs + /var/log/app itself = 94)

# Count total log files created
find /var/log/app -type f | wc -l
# → 90  (31 + 28 + 31 = 90 files)

# View the top-level structure
ls /var/log/app/

# View January's directories
ls /var/log/app/january/

# Verify a specific log file exists
ls /var/log/app/february/15/
# → log.daily

# View structure as a tree (if tree is installed)
tree /var/log/app/ | head -30
# or use find:
find /var/log/app -print | head -30 | sed 's|[^/]*/|  |g'
```

---

## 🔑 Key Takeaways

- Brace expansion turns **183 manual commands into 2** — this is the power of shell scripting.
- **Nested brace expansions** (`{january/{01..31},...}`) are the key technique — outer list controls months, inner sequences control days.
- The **suffix** `/log.daily` after the closing `}` creates the log file inside every day directory.
- **Always use zero-padding** (`{01..31}` not `{1..31}`) for day directories so sorting works correctly.
- **Always preview** with `echo` before running `mkdir` or `touch` — one second of checking saves minutes of cleanup.
- `/var/log/` requires **root privileges** — use `sudo`.
- `-p` flag on `mkdir` creates **all intermediate directories** in one shot and suppresses errors if they already exist.
- This pattern (month/day directory trees) is common in **log rotation**, **backup systems**, and **monitoring infrastructure**.

---

## 💡 Tips & Tricks

**🔹 Always do a dry run with `echo` first:**
```bash
# Before this:
sudo mkdir -p /var/log/app/{january/{01..31},february/{01..28},march/{01..31}}

# Run this first:
echo mkdir -p /var/log/app/{january/{01..31},february/{01..28},march/{01..31}}
# Scan the output — does it look right? Then run the real command.
```

**🔹 Extend to a full year in seconds:**
```bash
# Create a full year of daily log directories
sudo mkdir -p /var/log/app/{january/{01..31},february/{01..28},march/{01..31},april/{01..30},may/{01..31},june/{01..30},july/{01..31},august/{01..31},september/{01..30},october/{01..31},november/{01..30},december/{01..31}}
```

**🔹 Use the same pattern for any date-based structure:**
```bash
# Weekly reports by year
mkdir -p reports/{2023,2024,2025}/week_{01..52}

# Backups by month and day
mkdir -p backups/2025/{jan,feb,mar,apr,may,jun,jul,aug,sep,oct,nov,dec}/{01..31}
```

**🔹 Verify your structure with `find` and `wc`:**
```bash
find /var/log/app -type d | wc -l    # count directories
find /var/log/app -type f | wc -l    # count files
find /var/log/app -name "log.daily" | wc -l  # count log files specifically
```

**🔹 Clean up the test structure safely:**
```bash
# CAREFUL — double-check the path before rm -rf!
sudo rm -rf /var/log/app
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Attempt the Challenge Yourself

**Goal:** Re-create the exact project structure from scratch — from memory.

Requirements:
- Base path: `~/bash-practice/var/log/app/` (use home dir to avoid needing sudo)
- Months: `january` (31 days), `february` (28 days), `march` (31 days)
- Each day directory named: `01`, `02`, ... (zero-padded)
- Each day directory contains: `log.daily`

**Try to write both commands without looking at the solution. Then verify.**

---

### Exercise 2 — The Solution Step by Step

**Goal:** Execute the instructor's solution and verify each step.

```bash
# Use ~/bash-practice to avoid needing sudo
BASE="$HOME/bash-practice/var/log/app"

# Step 1: Preview the directory structure (dry run)
echo mkdir -p "$BASE"/{january/{01..31},february/{01..28},march/{01..31}}

# Step 2: Create all directories
mkdir -p "$BASE"/{january/{01..31},february/{01..28},march/{01..31}}

# Step 3: Verify directories
echo "--- Month directories ---"
ls "$BASE"/

echo "--- January days ---"
ls "$BASE"/january/

echo "--- Directory count ---"
find "$BASE" -type d | wc -l

# Step 4: Preview log file creation (dry run)
echo touch "$BASE"/{january/{01..31},february/{01..28},march/{01..31}}/log.daily

# Step 5: Create all log files
touch "$BASE"/{january/{01..31},february/{01..28},march/{01..31}}/log.daily

# Step 6: Verify log files
echo "--- Log file count ---"
find "$BASE" -type f -name "log.daily" | wc -l

echo "--- A sample day directory ---"
ls "$BASE"/february/15/

# Step 7: Full count summary
echo ""
echo "=== Structure Summary ==="
echo "Directories : $(find "$BASE" -type d | wc -l)"
echo "Log files   : $(find "$BASE" -type f | wc -l)"
echo "Expected    : 94 dirs (93 + base), 90 files"
```

---

### Exercise 3 — Understand the Nested Expansion

**Goal:** Break down and understand the nested brace expansion piece by piece.

```bash
# First — understand the OUTER expansion alone
echo {january,february,march}
# → january february march

# Add the INNER sequence for january
echo january/{01..05}
# → january/01 january/02 january/03 january/04 january/05

# Now combine into the outer list
echo {january/{01..05},february/{01..03},march/{01..05}}
# → january/01 january/02 ... february/01 ... march/05

# Add a prefix and suffix
echo /var/log/app/{january/{01..03},february/{01..02}}/log.daily
# Trace this: how many paths are generated?
```

---

### Exercise 4 — Extend to a Full Year

**Goal:** Use what you know to create a full 12-month log structure.

```bash
BASE="$HOME/bash-practice/fullyr_logs"

# Create the full year (correct days per month — 2025 is not a leap year)
mkdir -p "$BASE"/{january/{01..31},february/{01..28},march/{01..31},april/{01..30},may/{01..31},june/{01..30},july/{01..31},august/{01..31},september/{01..30},october/{01..31},november/{01..30},december/{01..31}}

# Create log files
touch "$BASE"/{january/{01..31},february/{01..28},march/{01..31},april/{01..30},may/{01..31},june/{01..30},july/{01..31},august/{01..31},september/{01..30},october/{01..31},november/{01..30},december/{01..31}}/log.daily

# Count everything
echo "Directories: $(find "$BASE" -type d | wc -l)"
echo "Log files  : $(find "$BASE" -type f | wc -l)"
# Expected: 365 log files (non-leap year 2025)

# Clean up
rm -rf "$BASE"
```

---

### Exercise 5 — Write a Log Structure Setup Script

**Goal:** Turn the two commands into a reusable, parameterised script.

Create `~/scripts/setup_log_structure.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : setup_log_structure.sh
# Purpose : Create a date-based log directory structure
# Usage   : ./setup_log_structure.sh [base_path]
# Default : ~/bash-practice/app_logs
# ─────────────────────────────────────────────────────

BASE="${1:-$HOME/bash-practice/app_logs}"

echo "=============================================="
echo "  Setting up log structure at:"
echo "  $BASE"
echo "=============================================="
echo ""

# Dry run first
echo "Preview (first 10 paths):"
echo {january/{01..31},february/{01..28},march/{01..31}} | \
  tr ' ' '\n' | head -10 | sed "s|^|  $BASE/|"
echo "  ... (90 total paths)"
echo ""

# Create directory structure
echo "Creating directories..."
mkdir -p "$BASE"/{january/{01..31},february/{01..28},march/{01..31}}

dir_count=$(find "$BASE" -type d | wc -l)
echo "  Created: $dir_count directories"

# Create log files
echo "Creating log files..."
touch "$BASE"/{january/{01..31},february/{01..28},march/{01..31}}/log.daily

file_count=$(find "$BASE" -type f | wc -l)
echo "  Created: $file_count log files"

echo ""
echo "=============================================="
echo "  Summary"
echo "  Directories : $dir_count"
echo "  Log files   : $file_count"
echo "  Base path   : $BASE"
echo "=============================================="
echo ""
echo "To remove: rm -rf \"$BASE\""
```

```bash
chmod +x ~/scripts/setup_log_structure.sh

# Run with default path
./setup_log_structure.sh

# Run with a custom path
./setup_log_structure.sh /tmp/my_app_logs

# Verify
ls ~/bash-practice/app_logs/

# Clean up
rm -rf ~/bash-practice/app_logs /tmp/my_app_logs
```

---

### Exercise 6 — Extend: Add Year Level

**Goal:** Extend the structure to include a year level — `app/2025/january/01/log.daily`.

```bash
BASE="$HOME/bash-practice/yearlog"

# Single year
mkdir -p "$BASE"/2025/{january/{01..31},february/{01..28},march/{01..31}}
touch "$BASE"/2025/{january/{01..31},february/{01..28},march/{01..31}}/log.daily

# Multiple years
mkdir -p "$BASE"/{2024,2025,2026}/{january/{01..31},february/{01..28},march/{01..31}}
touch "$BASE"/{2024,2025,2026}/{january/{01..31},february/{01..28},march/{01..31}}/log.daily

echo "Files created: $(find "$BASE" -type f | wc -l)"
# Expected: 90 × 3 years = 270 log files

# Clean up
rm -rf "$BASE"
```

---

### 🧠 Quiz — Test Yourself

1. How many commands would be needed to create the Q1 log structure **without** brace expansion?
2. How many commands does the brace expansion solution use?
3. Write the command to create all directories for January (31 days), February (28 days), and March (31 days) under `/var/log/app/`.
4. Write the command to create `log.daily` in every day directory.
5. Why is the days directory named `01` instead of `1`?
6. What does the `-p` flag do in `mkdir -p`?
7. Why does creating files in `/var/log/` require `sudo`?
8. What is the purpose of running `echo` before `mkdir` or `touch`?
9. How do you count all files created under a directory?
10. How many total log files are created across all three months (Jan 31, Feb 28, Mar 31)?

---

## 📎 Quick Reference Card

```
THE PROJECT — 2 COMMANDS THAT DO THE WORK OF 183
──────────────────────────────────────────────────────────────

COMMAND 1 — Create all directories:
sudo mkdir -p /var/log/app/{january/{01..31},february/{01..28},march/{01..31}}

COMMAND 2 — Create all log files:
sudo touch /var/log/app/{january/{01..31},february/{01..28},march/{01..31}}/log.daily

ALWAYS PREVIEW FIRST (dry run):
echo mkdir -p /var/log/app/{january/{01..31},february/{01..28},march/{01..31}}
echo touch /var/log/app/{january/{01..31},february/{01..28},march/{01..31}}/log.daily

VERIFY THE RESULT:
find /var/log/app -type d | wc -l      Count directories (expect 94)
find /var/log/app -type f | wc -l      Count files (expect 90)
ls /var/log/app/                       List month directories
ls /var/log/app/january/               List January day directories
ls /var/log/app/february/15/           Check a specific day

FULL YEAR EXTENSION:
mkdir -p /var/log/app/{january/{01..31},february/{01..28},march/{01..31},\
april/{01..30},may/{01..31},june/{01..30},july/{01..31},august/{01..31},\
september/{01..30},october/{01..31},november/{01..30},december/{01..31}}

STRUCTURE SUMMARY:
Month       Days    Dirs    Files
──────────────────────────────────
January     31      31      31
February    28      28      28
March       31      31      31
──────────────────────────────────
Total       90      93+1    90
             ↑       ↑       ↑
          day dirs  +base   log files
```

---

## 🗂️ Notion Tags (suggested)

`bash` `brace-expansion` `project` `devops` `log-management` `mkdir` `touch` `nested-expansion` `ZTM` `module-2` `section-03`

---

## 🏆 Project Complete!

> You just turned **183 commands into 2**. This is precisely why shell scripting exists — not to be clever, but to eliminate repetitive, error-prone manual work and replace it with reliable, repeatable automation.

*← Previous: Module 2 | Section 03 — Brace Expansion: Sequences*
*→ Next Episode: Module 2 | Section 03 — Tilde Expansion*
