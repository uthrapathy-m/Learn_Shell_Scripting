# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 03 — Shell Expansions
> **Episode:** 2. Brace Expansion — String Lists
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

Brace expansion is the **first expansion** Bash performs — before variables are even looked up. This episode covers the **string list** form of brace expansion: a mechanism to generate multiple strings from a single compact expression. Mastering it eliminates repetitive typing and makes many Bash commands dramatically shorter and more elegant.

---

## 🧠 Theory

### 1. What is Brace Expansion?

Brace expansion is a mechanism to **generate arbitrary strings** from a compact pattern. It happens **first** in the expansion pipeline — before tilde, variable, command, or any other expansion.

```bash
echo {old,new,current,backup}
# → old new current backup
```

The shell expands the expression inside `{...}` into a **space-separated list** of strings.

> 💡 Brace expansion has **nothing to do with variables** — no `$` required. It's purely a string generation mechanism.

---

### 2. Two Types of Brace Expansion

| Type | Syntax | Example | Output |
|---|---|---|---|
| **String list** | `{str1,str2,...}` | `{old,new,backup}` | `old new backup` |
| **Sequence/Range** | `{start..end}` | `{1..5}` | `1 2 3 4 5` |

> 📌 This episode covers **string lists**. The next episode covers sequences/ranges.

---

### 3. Full Syntax — Prefix, List, Suffix

The complete form of a string list brace expansion is:

```
[optional_prefix]{item1,item2,...,itemN}[optional_suffix]
```

| Part | Required? | What it does |
|---|---|---|
| `prefix` | Optional | Prepended to **each** expanded item |
| `{item1,item2,...}` | Required | The list to expand |
| `suffix` | Optional | Appended to **each** expanded item |

**Examples:**

```bash
# No prefix, no suffix — just the list
echo {old,new,current,backup}
# → old new current backup

# With suffix only (.txt appended to each)
echo {old,new,current,backup}.txt
# → old.txt new.txt current.txt backup.txt

# With prefix only (01- prepended to each)
echo 01-{old,new,current,backup}
# → 01-old 01-new 01-current 01-backup

# With BOTH prefix and suffix
echo 01-{old,new,current,backup}.txt
# → 01-old.txt 01-new.txt 01-current.txt 01-backup.txt
```

> 🔑 **Expansion direction:** Left to right — prefix is attached first, then the list is expanded, then the suffix is attached.

---

### 4. Using Brace Expansion with Any Command

Brace expansion works with **any command** — not just `echo`. The shell expands the braces before passing the result to the command.

```bash
# Create multiple files at once
touch {old,new,current,backup}.txt
# Creates: old.txt  new.txt  current.txt  backup.txt

# Remove those same files using the same expansion
rm {old,new,current,backup}.txt

# Create multiple directories at once
mkdir -p project/{src,tests,docs,assets}
# Creates: project/src  project/tests  project/docs  project/assets

# Copy a file and create a backup in one command
cp config.cfg{,.backup}
# Expands to: cp config.cfg config.cfg.backup
# (prefix = "config.cfg", list = {"", ".backup"})

# Move a file and rename it
mv report{,_v2}.pdf
# Expands to: mv report.pdf report_v2.pdf
```

---

### 5. Syntax Rules — What Makes a Valid Brace Expansion

These rules are strict — violating any one of them makes Bash treat the expression as a **literal string** rather than expanding it:

| Rule | Valid | Invalid |
|---|---|---|
| **No spaces** around commas | `{a,b,c}` ✅ | `{a, b, c}` ❌ |
| **No spaces** after opening `{` | `{a,b}` ✅ | `{ a,b}` ❌ |
| **No spaces** before closing `}` | `{a,b}` ✅ | `{a,b }` ❌ |
| **Braces must be unquoted** | `{a,b}` ✅ | `"{a,b}"` ❌ (single string) |
| **At least one unquoted comma** | `{a,b}` ✅ | `{abc}` ❌ (no comma = no expansion) |

```bash
# WRONG — space after comma — treated as literal string
echo {old, new, backup}
# → {old, new, backup}   (no expansion!)

# WRONG — quoted — treated as literal string
echo "{old,new,backup}"
# → {old,new,backup}   (no expansion!)

# WRONG — no comma
echo {single}
# → {single}   (no expansion!)

# CORRECT
echo {old,new,backup}
# → old new backup   ✅
```

> ⚠️ **Key rule:** Any **incorrectly formed** brace expansion is left unchanged — Bash does NOT produce an error. It silently passes the literal text through. This is a common source of quiet bugs.

---

### 6. Nested Brace Expansions

Brace expansions can be **nested inside each other** for powerful combinatorial output:

```bash
# Two separate brace expansions on the same line
echo {old,new,current,backup}-{10,20,30}
# → old-10 old-20 old-30 new-10 new-20 new-30 current-10 current-20 current-30 backup-10 backup-20 backup-30

# Nested brace expansion
echo {a,b{1,2},c}
# → a b1 b2 c
```

> 💡 Nested expansions are evaluated **inside out** — the innermost braces expand first.

---

### 7. Real-World Use Cases

```bash
# 1. Batch file creation
touch report_{jan,feb,mar,apr,may,jun}.csv
# Creates 6 CSV files

# 2. Batch directory structure creation
mkdir -p myapp/{frontend,backend,database}/{src,tests,docs}
# Creates 9 directories in one command!

# 3. Create a backup before editing a config file
cp /etc/nginx/nginx.conf{,.bak}
# Creates a .bak copy without retyping the full path

# 4. Rename a file (add/change extension)
mv script{.sh,.bash}
# Renames script.sh → script.bash

# 5. Download multiple numbered files
curl -O https://example.com/file{1,2,3,4,5}.pdf

# 6. Quick undo — restore from backup
cp /etc/nginx/nginx.conf{.bak,}
# Restores the .bak to the original (reversed from backup pattern)
```

---

## 🔑 Key Takeaways

- Brace expansion is the **first expansion** Bash performs — before variable lookups.
- It generates **arbitrary strings** — no `$` needed, no variables involved.
- Two types: **string lists** `{a,b,c}` and **sequences** `{1..10}` (next episode).
- Full syntax: `[prefix]{item1,item2,...}[suffix]` — prefix and suffix are optional and attach to every item.
- **No spaces** around commas or inside braces — spaces break the expansion silently.
- **Braces must be unquoted** — quoting prevents expansion.
- **At least one comma** is required — `{single}` does NOT expand.
- An incorrectly formed brace expansion is left as **literal text** — no error is shown.
- Brace expansions can be **nested** — inner braces expand first.
- Works with **any command**: `touch`, `mkdir`, `cp`, `rm`, `curl`, etc.

---

## 💡 Tips & Tricks

**🔹 The backup file trick — the most useful brace expansion:**
```bash
cp important_file.conf{,.bak}
# Expands to: cp important_file.conf important_file.conf.bak
# One command, no need to type the filename twice!
```

**🔹 Restore from backup (reverse of the above):**
```bash
cp important_file.conf{.bak,}
# Expands to: cp important_file.conf.bak important_file.conf
```

**🔹 Always test with `echo` first:**
```bash
# Before running a destructive command, preview the expansion with echo:
echo rm {old,new,backup}.txt
# → rm old.txt new.txt backup.txt
# Looks right? Then run it for real.
```

**🔹 Create a full project scaffold in seconds:**
```bash
mkdir -p myproject/{src,tests,docs}/{main,utils,helpers}
# 9 directories created instantly
```

**🔹 Empty string as one of the items = original + modified:**
```bash
# The empty string item "" means "the filename as-is"
cp file.txt{,.backup}  # "" and ".backup"
# → cp file.txt file.txt.backup
```

**🔹 Multiple brace expansions in one command:**
```bash
echo {a,b,c}-{1,2,3}
# → a-1 a-2 a-3 b-1 b-2 b-3 c-1 c-2 c-3
# Every combination of left × right is generated
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Basic String List Expansion

**Goal:** Get comfortable with the basic syntax and see the output.

```bash
# Simple list — no prefix or suffix
echo {old,new,current,backup}

# With suffix
echo {old,new,current,backup}.txt

# With prefix
echo file_{alpha,beta,gamma,delta}

# With both prefix and suffix
echo 01-{old,new,current,backup}.conf

# Multiple words in items (no spaces in items!)
echo {start,middle,end}-section
```

---

### Exercise 2 — Brace Expansion Syntax Rules

**Goal:** See what happens when you break each rule.

```bash
# RULE 1: No spaces around commas (spaces BREAK it)
echo {old,new,backup}        # correct
echo {old, new, backup}      # broken — what output do you get?
echo {old ,new,backup}       # broken — what output do you get?

# RULE 2: Braces must be unquoted
echo {old,new,backup}        # correct
echo "{old,new,backup}"      # broken — treated as literal

# RULE 3: At least one comma required
echo {single}                # no comma — no expansion
echo {a,}                    # one comma — valid (expands to "a" and "")
```

**Observe:** No error is shown for invalid brace expressions — they pass through as-is.

---

### Exercise 3 — Use With Real Commands

**Goal:** Apply brace expansion to `touch`, `mkdir`, and `rm`.

```bash
# Step 1: Create multiple files at once
touch report_{jan,feb,mar,apr,may,jun}.csv
ls report_*.csv

# Step 2: Create a directory tree
mkdir -p ~/bash-practice/project/{src,tests,docs,assets}/{main,utils}
tree ~/bash-practice/project 2>/dev/null || find ~/bash-practice/project -type d

# Step 3: Clean up the files using the same expansion
rm report_{jan,feb,mar,apr,may,jun}.csv
ls report_*.csv 2>/dev/null || echo "All removed"
```

---

### Exercise 4 — The Backup File Pattern

**Goal:** Master the most useful real-world brace expansion trick.

```bash
# Create a test config file
echo "server_name=myapp" > ~/bash-practice/app.conf
echo "port=8080" >> ~/bash-practice/app.conf

# Create a backup before editing (the key trick!)
cp ~/bash-practice/app.conf{,.bak}
ls ~/bash-practice/app.conf*
# Should show: app.conf  app.conf.bak

# Edit the original
echo "port=9090" >> ~/bash-practice/app.conf

# Compare original vs backup
diff ~/bash-practice/app.conf{,.bak}

# Restore from backup (reverse the pattern)
cp ~/bash-practice/app.conf{.bak,}
diff ~/bash-practice/app.conf{,.bak}   # no diff — restored!

# Clean up
rm ~/bash-practice/app.conf{,.bak}
```

---

### Exercise 5 — Nested Brace Expansions

**Goal:** Understand how nested braces create combinatorial output.

```bash
# Two separate expansions (cross product)
echo {a,b,c}-{1,2,3}
# How many items does this produce?

# Nested expansion (inner expands first)
echo {a,b{1,2,3},c}
# What is the output?

# Combine both
echo server{01,02,03}.{prod,dev}.example.com
# What does this generate?

# Real use: create a structured backup
echo backup-{mon,tue,wed,thu,fri}-{am,pm}
```

---

### Exercise 6 — Build a Project Scaffold Script

**Goal:** Write a script that uses brace expansion to scaffold a complete project structure.

Create `~/scripts/scaffold_project.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : scaffold_project.sh
# Purpose : Create a complete project directory structure
# Usage   : ./scaffold_project.sh <project_name>
# ─────────────────────────────────────────────────────

if [[ -z "$1" ]]; then
    echo "Usage: $0 <project_name>"
    exit 1
fi

project_name="$1"
base_dir="$HOME/projects/$project_name"

echo "Creating project: $project_name"
echo "Location: $base_dir"
echo ""

# Create the full directory tree using brace expansion
mkdir -p "$base_dir"/{src,tests,docs,config,scripts,assets/{images,css,js}}

# Create starter files using brace expansion
touch "$base_dir"/src/{main,utils,helpers}.sh
touch "$base_dir"/tests/{unit,integration,e2e}_tests.sh
touch "$base_dir"/docs/{README,CHANGELOG,CONTRIBUTING}.md
touch "$base_dir"/config/{dev,staging,prod}.conf

# Create a .gitignore
echo "*.log
*.tmp
*.bak" > "$base_dir"/.gitignore

echo "Project structure created:"
find "$base_dir" -print | sed 's|[^/]*/|  |g'
echo ""
echo "Done! Start working: cd $base_dir"
```

```bash
chmod +x ~/scripts/scaffold_project.sh
./scripts/scaffold_project.sh myapp
```

---

### 🧠 Quiz — Test Yourself

1. When does brace expansion happen in the 8-step expansion pipeline?
2. What are the **two types** of brace expansion?
3. What is the full syntax form including optional prefix and suffix?
4. What does `echo 01-{old,new,backup}.conf` produce?
5. What happens if you put a space after a comma: `echo {a, b, c}`?
6. What happens if you quote the braces: `echo "{a,b,c}"`?
7. What happens if there is only one item (no comma): `echo {abc}`?
8. Does an invalid brace expansion produce an error?
9. What does `cp config.conf{,.bak}` expand to?
10. What does `cp config.conf{.bak,}` expand to?
11. What does `echo {a,b}-{1,2}` produce?
12. How many directories does `mkdir -p project/{src,tests,docs}` create?

---

## 📎 Quick Reference Card

```
BRACE EXPANSION — STRING LISTS
──────────────────────────────────────────────────────────────
SYNTAX:  [prefix]{item1,item2,...,itemN}[suffix]

BASIC EXAMPLES
──────────────────────────────────────────────────────────────
echo {a,b,c}                → a b c
echo {old,new,backup}.txt   → old.txt new.txt backup.txt
echo file_{a,b,c}           → file_a file_b file_c
echo 01-{a,b,c}.conf        → 01-a.conf 01-b.conf 01-c.conf

WITH COMMANDS
──────────────────────────────────────────────────────────────
touch {a,b,c}.txt           Create 3 files at once
mkdir -p proj/{src,tests}   Create 2 directories
rm {a,b,c}.txt              Delete 3 files
cp file{,.bak}              Backup (copies file → file.bak)
cp file{.bak,}              Restore (copies file.bak → file)
mv script{.sh,.bash}        Rename by swapping extension

CROSS-PRODUCT (multiple expansions)
──────────────────────────────────────────────────────────────
echo {a,b}-{1,2}            → a-1 a-2 b-1 b-2

NESTED
──────────────────────────────────────────────────────────────
echo {a,b{1,2},c}           → a b1 b2 c

SYNTAX RULES — MUST FOLLOW ALL
──────────────────────────────────────────────────────────────
✅ No spaces around commas:   {a,b,c}     NOT {a, b, c}
✅ No spaces inside braces:   {a,b,c}     NOT { a,b,c }
✅ Braces must be unquoted:   {a,b,c}     NOT "{a,b,c}"
✅ At least one comma needed: {a,b}       NOT {single}
⚠️ Invalid = left unchanged, NO error shown
```

---

## 🗂️ Notion Tags (suggested)

`bash` `brace-expansion` `string-lists` `shell-expansions` `productivity` `shell-scripting` `ZTM` `module-2` `section-03`

---

*← Previous: Module 2 | Section 03 — Intro to Expansions*
*→ Next Episode: Module 2 | Section 03 — Brace Expansion: Ranges & Sequences*
