# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 03 — Shell Expansions
> **Episode:** 8. Command Substitution
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

Command substitution is described by the instructor as **"probably one of the most useful Bash features."** It allows you to run any shell command and capture its output — storing it in a variable, embedding it in a string, or using it as an argument to another command. This episode covers both syntax forms, best practices, and a practical real-world backup script that embeds a timestamp in the filename.

---

## 🧠 Theory

### 1. What is Command Substitution?

Command substitution means **running a shell command and using its output as a value** — substituting the command with what it prints.

```
$(command)
    ↓
command runs → output captured → output replaces $(command) in the line
```

```bash
# Instead of:
date        # prints the date to the screen

# With command substitution:
now=$(date)     # runs date, captures output, stores in $now
echo $now       # → Mon Mar 17 10:30:00 IST 2026
```

---

### 2. Two Syntax Forms

There are **two ways** to perform command substitution — they are functionally identical:

| Form | Syntax | Example |
|---|---|---|
| **Modern (recommended)** | `$(command)` | `now=$(date)` |
| **Legacy (backticks)** | `` `command` `` | `` now=`date` `` |

```bash
# Modern form — dollar + parentheses
now=$(date)
echo $now

# Legacy form — backticks (the key below Escape)
now=`date`
echo $now
```

> ✅ **Always use `$(command)`** — the modern form. Backticks are harder to read, can't be nested easily, and are considered legacy. The `$(...)` form is the universal standard.

---

### 3. Why `$(...)` is Superior to Backticks

| Feature | `$(command)` | `` `command` `` |
|---|---|---|
| Readability | ✅ Clear and unambiguous | ❌ Easy to confuse with single quotes |
| Nesting | ✅ `$(cmd1 $(cmd2))` — easy | ❌ Must escape inner backticks: `` `cmd1 \`cmd2\`` `` |
| Portability | ✅ POSIX standard | ⚠️ Also POSIX but discouraged |
| Visual clarity | ✅ Matching parentheses | ❌ Two identical characters — opening/closing look the same |

---

### 4. Storing Command Output in a Variable

The most common use — capture a command's output for later use in the script:

```bash
# Capture single command output
now=$(date)
hostname=$(hostname)
current_user=$(whoami)
disk_usage=$(df -h / | awk 'NR==2{print $5}')

echo "Time    : $now"
echo "Host    : $hostname"
echo "User    : $current_user"
echo "Disk use: $disk_usage"
```

---

### 5. Capturing Pipeline Output

Command substitution works with **entire pipelines** — not just single commands:

```bash
# Capture the output of a piped command chain
bash_processes=$(ps -ef | grep bash)
echo "$bash_processes"

# Count lines in a file
line_count=$(wc -l < /etc/passwd)
echo "Users in /etc/passwd: $line_count"

# Get only the top 3 processes by CPU
top_processes=$(ps aux --sort=-%cpu | head -4 | tail -3)
echo "$top_processes"

# Extract just the IP addresses from ifconfig
ip_addresses=$(hostname -I)
echo "IP addresses: $ip_addresses"
```

> 📌 The entire pipeline runs, and the final combined output is what gets captured.

---

### 6. Embedding Command Substitution Inside Strings

You can embed `$(...)` directly inside a double-quoted string — this is extremely powerful for building dynamic strings:

```bash
echo "Today is $(date +%A), $(date +%d %B %Y)"
# → Today is Monday, 17 March 2026

echo "You are logged in as $(whoami) on $(hostname)"
# → You are logged in as student on myserver

echo "There are $(ls /home | wc -l) users on this system"
# → There are 3 users on this system
```

> ⚠️ Command substitution inside **single quotes** does NOT work — single quotes suppress all expansions.

```bash
echo '$(date)'      # → $(date)   (literal — no substitution)
echo "$(date)"      # → Mon Mar 17 10:30:00 IST 2026  ✅
```

---

### 7. Always Quote Command Substitution Results

When using command substitution, **always wrap the result in double quotes** to prevent word splitting and glob expansion on the output:

```bash
# DANGEROUS — if output contains spaces or globs, word splitting occurs
files=$(ls /home)
echo $files       # word-split — each filename is a separate word

# SAFE — double quotes preserve the output as-is
files=$(ls /home)
echo "$files"     # → each filename on its own line, preserved
```

---

### 8. The Practical Project — Timestamped Backup Archive

The instructor's demonstration: creating a `tar.gz` backup of `/etc` where the filename **automatically includes the current date and time**.

**The problem without command substitution:**

```bash
# Static filename — gets OVERWRITTEN every time you run it!
sudo tar -czf etc.tar.gz /etc 2>/dev/null
ls -l etc.tar.gz
# Run again → same filename → previous backup is GONE
```

**The solution with command substitution:**

```bash
# Dynamic filename — UNIQUE every time (includes date and time)
sudo tar -czf "etc_$(date +%F_%H%M).tar.gz" /etc 2>/dev/null
ls -l etc_*.tar.gz
```

**What `date +%F_%H%M` produces:**

| Format code | Meaning | Example |
|---|---|---|
| `%F` | Full date: `YYYY-MM-DD` | `2026-03-17` |
| `%H` | Hour (24h): `00`–`23` | `10` |
| `%M` | Minute: `00`–`59` | `30` |
| Combined `%F_%H%M` | `YYYY-MM-DD_HHMM` | `2026-03-17_1030` |

```bash
# Result filename examples:
etc_2026-03-17_1030.tar.gz
etc_2026-03-17_1031.tar.gz    # run one minute later — new file, not overwritten!
etc_2026-03-17_1045.tar.gz    # run 15 minutes later — another unique file
```

> 💡 **This is a real professional pattern** used in backup scripts, log rotation, and deployment systems worldwide.

---

### 9. Useful `date` Format Codes

Since `date` is so commonly used with command substitution, here are the most useful format codes:

| Code | Meaning | Example |
|---|---|---|
| `%F` | Full date (`YYYY-MM-DD`) | `2026-03-17` |
| `%Y` | Year (4-digit) | `2026` |
| `%m` | Month (2-digit) | `03` |
| `%d` | Day (2-digit) | `17` |
| `%H` | Hour 24h (2-digit) | `10` |
| `%M` | Minute (2-digit) | `30` |
| `%S` | Second (2-digit) | `45` |
| `%T` | Time `HH:MM:SS` | `10:30:45` |
| `%A` | Full weekday name | `Monday` |
| `%a` | Short weekday name | `Mon` |
| `%B` | Full month name | `March` |
| `%b` | Short month name | `Mar` |
| `%s` | Unix epoch (seconds since 1970) | `1742209845` |

```bash
# Examples
date +%F                # → 2026-03-17
date +%F_%T             # → 2026-03-17_10:30:45
date +%F_%H%M%S         # → 2026-03-17_103045  (no colons — safe for filenames)
date +"%A, %d %B %Y"    # → Monday, 17 March 2026
```

---

### 10. Nested Command Substitution

`$(...)` can be nested — the inner command runs first:

```bash
# Get the home directory of the user who owns a process
echo "Root's shell: $(grep root /etc/passwd | cut -d: -f7)"

# Count files matching a pattern
echo "Bash scripts: $(ls $(echo ~/scripts)/*.sh 2>/dev/null | wc -l)"

# Find and count
echo "Large files: $(find /var/log -size +1M 2>/dev/null | wc -l)"
```

> 💡 With nested substitution, inner `$(...)` expands first, and its output becomes part of the outer command.

---

## 🔑 Key Takeaways

- Command substitution **runs a command and replaces it with its output**.
- Two forms: modern `$(command)` and legacy `` `command` `` — **always use `$(command)`**.
- `$(...)` is superior: readable, nestable, unambiguous.
- Store the output: `variable=$(command)`.
- Works with **entire pipelines**: `output=$(cmd1 | cmd2 | cmd3)`.
- Embed inline in strings: `"Today is $(date)"` — works inside double quotes, not single quotes.
- **Always quote the result**: `"$(command)"` prevents word splitting on the output.
- The timestamp backup pattern `"archive_$(date +%F_%H%M).tar.gz"` creates unique, never-overwritten backups.
- `date +%F_%H%M` is the most useful format for filenames (no special chars, sorts chronologically).

---

## 💡 Tips & Tricks

**🔹 The timestamp pattern — memorise this:**
```bash
$(date +%F_%H%M%S)    # → 2026-03-17_103045
# Safe for filenames: no spaces, no colons, sorts correctly
```

**🔹 Always double-quote to prevent word splitting:**
```bash
result="$(command)"    # SAFE — always do this
result=$(command)      # RISKY — word splitting may occur on output
```

**🔹 Redirect stderr when you only want clean output:**
```bash
output=$(command 2>/dev/null)    # suppress errors — only capture stdout
```

**🔹 Check if a command returned anything:**
```bash
result=$(some_command 2>/dev/null)
if [[ -z "$result" ]]; then
    echo "Command produced no output"
fi
```

**🔹 Use `$()` to build dynamic filenames for any output:**
```bash
logfile="app_$(date +%F).log"          # daily log
backup="db_$(date +%F_%H%M).sql.gz"   # timestamped backup
report="report_$(hostname)_$(date +%F).txt"  # machine + date
```

**🔹 Assign multiple values from one command:**
```bash
read host user port <<< "$(echo 'server01 admin 22')"
echo "Host: $host, User: $user, Port: $port"
```

**🔹 Check `date` format before using in a script:**
```bash
# Always test your date format interactively first:
date +%F_%H%M%S
# Then use it in the script with confidence
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Basic Command Substitution

**Goal:** Capture command output into variables and use them.

```bash
# Capture basic info
current_date=$(date)
current_user=$(whoami)
current_host=$(hostname)
current_dir=$(pwd)

echo "Date    : $current_date"
echo "User    : $current_user"
echo "Host    : $current_host"
echo "Dir     : $current_dir"

# Inline embedding in strings
echo "Hello $(whoami)! Today is $(date +%A)."
echo "You have $(ls ~ | wc -l) items in your home directory."
```

---

### Exercise 2 — Modern `$()` vs Legacy Backticks

**Goal:** See both forms and confirm they produce identical output.

```bash
# Modern form
result1=$(date +%F)
echo "Modern: $result1"

# Legacy backtick form
result2=`date +%F`
echo "Legacy: $result2"

# Are they identical?
if [[ "$result1" == "$result2" ]]; then
    echo "Identical output ✅"
fi

# Nested — modern form is much cleaner
echo "$(echo $(date +%Y))"          # nested modern
# echo "`echo \`date +%Y\``"        # nested backticks — ugly and confusing
```

---

### Exercise 3 — Pipeline Command Substitution

**Goal:** Capture the output of a piped command chain.

```bash
# Count bash processes
bash_count=$(ps aux | grep -c bash)
echo "Bash processes: $bash_count"

# Get top memory-consuming process name
top_mem=$(ps aux --sort=-%mem | awk 'NR==2{print $11}')
echo "Top memory process: $top_mem"

# Get number of logged-in users
user_count=$(who | wc -l)
echo "Users logged in: $user_count"

# Get disk usage percentage for root partition
disk_pct=$(df -h / | awk 'NR==2{print $5}')
echo "Root disk usage: $disk_pct"

# Get the number of lines in a system file
passwd_lines=$(wc -l < /etc/passwd)
echo "Entries in /etc/passwd: $passwd_lines"
```

---

### Exercise 4 — The `date` Format Deep-Dive

**Goal:** Explore `date` format codes used with command substitution.

```bash
echo "=== Date Format Codes ==="
echo "Full date (%F)     : $(date +%F)"
echo "Year (%Y)          : $(date +%Y)"
echo "Month (%m)         : $(date +%m)"
echo "Day (%d)           : $(date +%d)"
echo "Hour (%H)          : $(date +%H)"
echo "Minute (%M)        : $(date +%M)"
echo "Second (%S)        : $(date +%S)"
echo "Full time (%T)     : $(date +%T)"
echo "Weekday (%A)       : $(date +%A)"
echo "Short weekday (%a) : $(date +%a)"
echo "Month name (%B)    : $(date +%B)"
echo ""
echo "=== Combined Formats ==="
echo "Filename-safe      : $(date +%F_%H%M%S)"
echo "Human readable     : $(date +'%A, %d %B %Y at %H:%M')"
echo "Unix epoch         : $(date +%s)"
```

---

### Exercise 5 — Build the Timestamped Backup Script

**Goal:** Re-create the instructor's backup script with improvements.

Create `~/scripts/backup_etc.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : backup_etc.sh
# Purpose : Create a timestamped compressed backup of /etc
# Usage   : sudo ./backup_etc.sh [destination_dir]
# ─────────────────────────────────────────────────────

# Configuration
source_dir="${1:-/etc}"
backup_dir="${2:-$HOME/backups}"
timestamp=$(date +%F_%H%M%S)
archive_name="$(basename "$source_dir")_${timestamp}.tar.gz"
full_path="$backup_dir/$archive_name"

echo "============================================"
echo "  Timestamped Backup Script"
echo "============================================"
echo "  Source    : $source_dir"
echo "  Dest dir  : $backup_dir"
echo "  Archive   : $archive_name"
echo "  Timestamp : $timestamp"
echo "============================================"
echo ""

# Create backup directory if needed
mkdir -p "$backup_dir"

# Create the backup
echo "Creating backup..."
tar -czf "$full_path" "$source_dir" 2>/dev/null

# Verify the backup was created
if [[ -f "$full_path" ]]; then
    size=$(du -h "$full_path" | cut -f1)
    echo "SUCCESS: Backup created"
    echo "  File : $full_path"
    echo "  Size : $size"
else
    echo "ERROR: Backup failed!"
    exit 1
fi

echo ""
echo "============================================"
echo "  Existing backups in $backup_dir:"
ls -lh "$backup_dir"/*.tar.gz 2>/dev/null || echo "  (none yet)"
echo "============================================"
```

```bash
chmod +x ~/scripts/backup_etc.sh

# Test with a small directory (no sudo needed)
./backup_etc.sh ~/scripts ~/bash-practice/backups

# Run again after a few seconds — observe a new unique file is created
sleep 2
./backup_etc.sh ~/scripts ~/bash-practice/backups

# List the backups
ls -lh ~/bash-practice/backups/

# Clean up
rm -rf ~/bash-practice/backups
```

---

### Exercise 6 — System Report Script Using Command Substitution

**Goal:** Build a script that uses command substitution throughout for a full system report.

Create `~/scripts/system_report.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : system_report.sh
# Purpose : Generate a system report using command substitution
# Usage   : ./system_report.sh
# ─────────────────────────────────────────────────────

# Capture all values via command substitution
report_time="$(date +'%A, %d %B %Y at %H:%M:%S')"
hostname_val="$(hostname)"
kernel_ver="$(uname -r)"
os_info="$(cat /etc/os-release | grep PRETTY_NAME | cut -d= -f2 | tr -d '"')"
uptime_val="$(uptime -p)"
logged_users="$(who | wc -l)"
cpu_model="$(grep 'model name' /proc/cpuinfo | head -1 | cut -d: -f2 | xargs)"
cpu_cores="$(nproc)"
total_mem="$(free -h | awk 'NR==2{print $2}')"
used_mem="$(free -h | awk 'NR==2{print $3}')"
disk_total="$(df -h / | awk 'NR==2{print $2}')"
disk_used="$(df -h / | awk 'NR==2{print $3}')"
disk_pct="$(df -h / | awk 'NR==2{print $5}')"
load_avg="$(uptime | awk -F'load average:' '{print $2}')"

echo "=============================================="
echo "  SYSTEM REPORT — $report_time"
echo "=============================================="
echo ""
echo "── System ────────────────────────────────────"
echo "  Hostname    : $hostname_val"
echo "  OS          : $os_info"
echo "  Kernel      : $kernel_ver"
echo "  Uptime      : $uptime_val"
echo "  Load Avg    : $load_avg"
echo ""
echo "── Users ─────────────────────────────────────"
echo "  Logged in   : $logged_users user(s)"
echo "  Current     : $(whoami)"
echo ""
echo "── CPU ───────────────────────────────────────"
echo "  Model       : $cpu_model"
echo "  Cores       : $cpu_cores"
echo ""
echo "── Memory ────────────────────────────────────"
echo "  Total       : $total_mem"
echo "  Used        : $used_mem"
echo ""
echo "── Disk (/) ──────────────────────────────────"
echo "  Total       : $disk_total"
echo "  Used        : $disk_used ($disk_pct)"
echo ""
echo "=============================================="
echo "  Report generated: $(date +%F_%H%M%S)"
echo "=============================================="
```

```bash
chmod +x ~/scripts/system_report.sh
./system_report.sh

# Save the report to a file with a timestamp
./system_report.sh > ~/bash-practice/report_$(date +%F_%H%M).txt
cat ~/bash-practice/report_*.txt
```

---

### 🧠 Quiz — Test Yourself

1. What is command substitution in one sentence?
2. What are the **two syntax forms** for command substitution?
3. Which form is recommended and why?
4. Write the command to store today's date in a variable called `today`.
5. Can command substitution be used inside a double-quoted string?
6. Can command substitution be used inside a single-quoted string?
7. Write the command to store the output of `ps -ef | grep nginx` in a variable.
8. Why should you always use double quotes around a command substitution result?
9. What does `date +%F_%H%M` produce and why is it good for filenames?
10. What is the problem with a static backup filename, and how does command substitution solve it?
11. Write the `tar` command that creates a timestamped backup of `/home/user/documents`.
12. Can `$(...)` be nested? Give an example.

---

## 📎 Quick Reference Card

```
COMMAND SUBSTITUTION
──────────────────────────────────────────────────────────────
$(command)          Modern form — ALWAYS USE THIS
`command`           Legacy backtick form — avoid

STORE IN VARIABLE
──────────────────────────────────────────────────────────────
var=$(command)                   Single command
var=$(cmd1 | cmd2 | cmd3)        Pipeline
var="$(command)"                 With quotes (safest)
var=$(command 2>/dev/null)       Suppress errors

EMBED IN STRING (double quotes only)
──────────────────────────────────────────────────────────────
echo "Hello $(whoami)"
echo "Today is $(date +%A)"
echo "$(cmd1) and $(cmd2)"

DATE FORMAT CODES (most useful for scripts)
──────────────────────────────────────────────────────────────
$(date +%F)             → 2026-03-17       (YYYY-MM-DD)
$(date +%F_%H%M%S)      → 2026-03-17_103045  (filename-safe)
$(date +%T)             → 10:30:45         (HH:MM:SS)
$(date +%s)             → 1742209845       (Unix epoch)
$(date +'%A, %d %B %Y') → Monday, 17 March 2026

TIMESTAMPED BACKUP PATTERN
──────────────────────────────────────────────────────────────
tar -czf "backup_$(date +%F_%H%M%S).tar.gz" /path/to/dir

COMMON PATTERNS
──────────────────────────────────────────────────────────────
$(whoami)               Current username
$(hostname)             Machine hostname
$(pwd)                  Current directory
$(date +%F)             Today's date
$(uname -r)             Kernel version
$(wc -l < file)         Count lines in file
$(ls | wc -l)           Count items in directory
$(du -h file | cut -f1) File size human-readable
```

---

## 🗂️ Notion Tags (suggested)

`bash` `command-substitution` `shell-expansions` `date` `backup` `timestamp` `automation` `ZTM` `module-2` `section-03`

---

*← Previous: Module 2 | Section 03 — Tilde, Parameter & Variable Expansions*
*→ Next Episode: Module 2 | Section 03 — Arithmetic Expansion*
