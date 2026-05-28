# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 02 — Variables
> **Episode:** 6. Positional Parameters
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

In the previous episode, `read` captured input interactively during script execution. This episode introduces a **second, equally important way to pass input** — **positional parameters** — arguments provided directly on the command line when the script is invoked. This is how most Linux commands and professional scripts work.

---

## 🧠 Theory

### 1. What Are Positional Parameters?

When you run a script, you can pass **arguments** (values) after the script name, separated by spaces:

```bash
./script.sh arg1 arg2 arg3
```

Bash automatically captures each argument into **predefined positional parameters** — numbered variables you can use inside the script without declaring them.

```
./script.sh  filename.txt  directory1  192.168.1.1
     $0           $1           $2           $3
```

| Parameter | What it stores |
|---|---|
| `$0` | The **name of the script** as it was invoked |
| `$1` | The **first** argument |
| `$2` | The **second** argument |
| `$3` | The **third** argument |
| `$N` | The **Nth** argument |
| `${10}` | The **tenth** argument (curly braces required for 2+ digits) |

> 💡 **Key insight:** Positional parameters are **pre-defined and automatically populated** — you never need to declare or assign them. They exist the moment the script starts.

---

### 2. Parameters vs Variables — The Bigger Picture

The instructor introduces a precise terminology breakdown here that's worth knowing:

| Type | Referenced by | Example | What it is |
|---|---|---|---|
| **Variable** | A name | `$os`, `$username` | User-defined named storage |
| **Positional Parameter** | A number | `$1`, `$2`, `${10}` | Script argument by position |
| **Special Parameter** | A symbol | `$#`, `$@`, `$?`, `$$` | Auto-set, special meanings (next episode) |

> 📌 A **parameter** is the broader term — it's any entity that stores a value. Variables are parameters referenced by name. Positional parameters are parameters referenced by number.

---

### 3. Positional Parameter Syntax Rules

```bash
$1    # first argument  — no braces needed for single digits
$9    # ninth argument  — no braces needed
${10} # tenth argument  — CURLY BRACES REQUIRED for 10 and above
${11} # eleventh argument
```

> ⚠️ **Critical rule:** From `$10` onwards, you MUST use `${10}`, `${11}`, etc. Without curly braces, `$10` is interpreted as `$1` followed by the literal character `0` — not what you want!

```bash
# If $1 = "hello":
echo $10    # → hello0   (WRONG — bash reads $1 then appends 0)
echo ${10}  # → (the 10th argument — CORRECT)
```

---

### 4. `$0` — The Script Name

`$0` is a special positional parameter that always holds **how the script was called**:

```bash
echo $0

# If run as:
./my_script.sh           → $0 = ./my_script.sh
/home/user/my_script.sh  → $0 = /home/user/my_script.sh
bash my_script.sh        → $0 = my_script.sh
```

> 💡 `$0` is useful for printing usage/help messages that reference the script's own name:
> ```bash
> echo "Usage: $0 <filename> <destination>"
> ```

---

### 5. Default Values for Positional Parameters

If a script is run **without the expected arguments**, the positional parameters will be empty — which can cause errors. You can provide **default values** using the `${N:-default}` syntax:

```bash
# If $1 is not provided, use "default_value"
param1=${1:-"default_value"}

# If $2 is not provided, use "ABC"
param2=${2:-"ABC"}
```

**Full example:**
```bash
#!/bin/bash
filename=${1:-"default.txt"}
echo "Working with file: $filename"
```

```bash
./script.sh              # → Working with file: default.txt  (default used)
./script.sh report.csv   # → Working with file: report.csv   (argument used)
```

> ✅ Using default values makes scripts **robust** — they don't crash when called without arguments.

---

### 6. How Arguments Are Separated — `$IFS`

Arguments on the command line are separated by **whitespace** — the default value of `$IFS` (Internal Field Separator). Each space-separated word becomes a distinct positional parameter.

```bash
./script.sh one two three
# $1 = one
# $2 = two
# $3 = three
```

**Passing arguments with spaces — use quotes:**
```bash
./script.sh "hello world" two three
# $1 = hello world   (treated as one argument because of quotes)
# $2 = two
# $3 = three
```

---

### 7. Practical Script — Display and Compress

The instructor builds a complete, practical script that:
1. Takes a **filename as the first argument** (`$1`)
2. Displays the file's contents with `cat`
3. Creates a **compressed `.tar.gz` archive** of the file

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : display_and_compress.sh
# Purpose : Display file contents and create a compressed copy
# Usage   : ./display_and_compress.sh <filename>
# ─────────────────────────────────────────────────────

echo "Displaying the contents of $1"
sleep 2

cat "$1"

echo ""
echo "Compressing $1..."
sleep 2

tar -czvf "$1.tar.gz" "$1"

echo ""
echo "Done! Archive created: $1.tar.gz"
```

**Running it:**
```bash
chmod +x display_and_compress.sh

# Display and compress a local file
./display_and_compress.sh myfile.txt

# Display and compress a system file (needs root)
sudo ./display_and_compress.sh /etc/passwd
```

**`tar` flags explained:**
| Flag | Meaning |
|---|---|
| `-c` | **C**reate a new archive |
| `-z` | Compress with g**z**ip |
| `-v` | **V**erbose — show files being processed |
| `-f` | Next argument is the archive **f**ilename |

> 📌 Focus on how `$1` is used throughout — it flows from the command line directly into `echo`, `cat`, and `tar` without any additional setup.

---

### 8. When to Use `read` vs Positional Parameters

| Feature | `read` (interactive) | Positional Parameters |
|---|---|---|
| How input arrives | User types during script run | Passed on command line before script starts |
| Use case | Scripts that guide users step-by-step | Scripts run in pipelines, cron jobs, automation |
| Example | `read -rp "Enter IP: " ip` | `./block_ip.sh 8.8.8.8` |
| Scriptable/automatable? | ❌ Requires a human | ✅ Can be automated |
| Good for | User-facing interactive tools | DevOps, admin scripts, automation |

> 💡 Most professional scripts use **positional parameters** because they can be automated (called from other scripts, cron jobs, CI/CD pipelines) without needing a human to type input.

---

## 🔑 Key Takeaways

- **Positional parameters** are pre-defined variables (`$1`, `$2`, ...) automatically populated with command-line arguments.
- `$0` holds the **name of the script** as it was called.
- Arguments are separated by **whitespace** (controlled by `$IFS`).
- Single-digit parameters: `$1` through `$9` — no braces needed.
- **Two-digit and above: MUST use curly braces** — `${10}`, `${11}`, etc.
- Without curly braces, `$10` is misread as `$1` + literal `0`.
- Use `${1:-"default"}` to provide a **default value** when an argument is not passed.
- Positional parameters are **read-only** — you cannot assign to `$1` directly (use a regular variable: `filename=$1`).
- Prefer positional parameters for **automated, scriptable** use cases; use `read` for interactive user-facing scripts.
- Always wrap positional parameters in **double quotes** when using them: `"$1"` not `$1` — especially when filenames may contain spaces.

---

## 💡 Tips & Tricks

**🔹 Always copy `$1`, `$2` etc. into named variables at the top of the script:**
```bash
#!/bin/bash
# Assign positional params to meaningful names immediately
filename="$1"
destination="$2"
max_count="${3:-10}"    # with default

# Use the named variables throughout — clearer and safer
cat "$filename"
cp "$filename" "$destination"
```
This makes the script **self-documenting** and far easier to read.

**🔹 Always quote positional parameters:**
```bash
# DANGEROUS — breaks if filename has spaces
cat $1

# SAFE — always do this
cat "$1"
```

**🔹 Print a usage message when arguments are missing:**
```bash
if [[ -z "$1" ]]; then
    echo "Usage: $0 <filename>"
    echo "Example: $0 /etc/passwd"
    exit 1
fi
```

**🔹 Use `basename $0` for cleaner script names in messages:**
```bash
script_name=$(basename "$0")
echo "Usage: $script_name <filename>"
# Shows just "display_and_compress.sh" instead of "./display_and_compress.sh"
```

**🔹 Check the number of arguments before proceeding:**
```bash
if [[ "$#" -ne 2 ]]; then
    echo "Error: Expected 2 arguments, got $#"
    echo "Usage: $0 <source> <destination>"
    exit 1
fi
```
(`$#` is a special parameter that holds the count of arguments — covered in the next episode.)

**🔹 Loop over all arguments:**
```bash
for arg in "$@"; do
    echo "Processing: $arg"
done
```
(`$@` and `$#` are special parameters — next episode!)

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Explore Positional Parameters Interactively

**Goal:** See exactly how positional parameters map to command-line arguments.

Create `~/scripts/show_params.sh`:

```bash
#!/bin/bash
echo "Script name  : $0"
echo "1st argument : $1"
echo "2nd argument : $2"
echo "3rd argument : $3"
echo "4th argument : $4"
echo "5th argument : $5"
```

```bash
chmod +x ~/scripts/show_params.sh

# Run with no arguments — all $1-$5 will be empty
./show_params.sh

# Run with arguments
./show_params.sh alpha beta gamma delta epsilon

# Run with fewer arguments than parameters
./show_params.sh one two

# Run with a space-containing argument (use quotes)
./show_params.sh "hello world" second third
```

**Observe:** What is `$1` when you pass `"hello world"` as the first argument?

---

### Exercise 2 — The `${10}` Rule

**Goal:** Prove that curly braces are mandatory for two-digit argument positions.

Create `~/scripts/ten_args.sh`:

```bash
#!/bin/bash
echo "Arg  1 : $1"
echo "Arg  9 : $9"
echo "Arg 10 (wrong): $10"     # without braces
echo "Arg 10 (right): ${10}"   # with braces
echo "Arg 11 (right): ${11}"
```

```bash
chmod +x ~/scripts/ten_args.sh
./ten_args.sh a b c d e f g h i j k
```

**Observe:** What does `$10` print vs `${10}`?

---

### Exercise 3 — Default Values for Positional Parameters

**Goal:** Make a script that works gracefully with or without arguments.

Create `~/scripts/greet_user.sh`:

```bash
#!/bin/bash
# Use default values if arguments not provided
name=${1:-"World"}
greeting=${2:-"Hello"}
times=${3:-1}

for i in $(seq 1 $times); do
    echo "$greeting, $name!"
done
```

```bash
chmod +x ~/scripts/greet_user.sh

# No arguments — defaults kick in
./greet_user.sh

# Partial arguments
./greet_user.sh Alice

# All arguments
./greet_user.sh Alice "Good morning" 3
```

---

### Exercise 4 — Assign Positional Params to Named Variables

**Goal:** Practice the professional habit of immediately naming your positional parameters.

Create `~/scripts/backup_file.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : backup_file.sh
# Purpose : Copy a file to a backup destination
# Usage   : ./backup_file.sh <source_file> <backup_dir>
# ─────────────────────────────────────────────────────

# Immediately assign to named variables
source_file="${1:-}"
backup_dir="${2:-$HOME/backup}"

# Validate source file is provided
if [[ -z "$source_file" ]]; then
    echo "Error: No source file specified."
    echo "Usage: $0 <source_file> [backup_dir]"
    exit 1
fi

# Validate source file exists
if [[ ! -f "$source_file" ]]; then
    echo "Error: File '$source_file' does not exist."
    exit 1
fi

# Create backup dir if it doesn't exist
mkdir -p "$backup_dir"

# Perform the backup
cp "$source_file" "$backup_dir/"
echo "Backed up '$source_file' → '$backup_dir/'"
```

```bash
chmod +x ~/scripts/backup_file.sh

# Test with a real file
./backup_file.sh ~/.bashrc
./backup_file.sh ~/.bashrc /tmp/my_backups

# Test error handling
./backup_file.sh                         # no args
./backup_file.sh /nonexistent/file.txt   # file doesn't exist
```

---

### Exercise 5 — Re-Create the Display and Compress Script

**Goal:** Build the instructor's script with improvements.

Create `~/scripts/display_and_compress.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : display_and_compress.sh
# Purpose : Display a file's contents then compress it
# Usage   : ./display_and_compress.sh <filename>
# ─────────────────────────────────────────────────────

# Assign to named variable immediately
target_file="$1"

# Validate argument
if [[ -z "$target_file" ]]; then
    echo "Error: Please provide a filename."
    echo "Usage: $0 <filename>"
    exit 1
fi

if [[ ! -f "$target_file" ]]; then
    echo "Error: File '$target_file' not found."
    exit 1
fi

# Display contents
echo "======================================"
echo "  Contents of: $target_file"
echo "======================================"
sleep 1
cat "$target_file"
echo ""

# Compress it
echo "======================================"
echo "  Compressing: $target_file"
echo "======================================"
sleep 1
tar -czvf "${target_file}.tar.gz" "$target_file"

echo ""
echo "Done! Archive: ${target_file}.tar.gz"
```

```bash
chmod +x ~/scripts/display_and_compress.sh

# Create a test file first
echo "This is test content line 1" > /tmp/test_file.txt
echo "This is test content line 2" >> /tmp/test_file.txt

# Run the script
./display_and_compress.sh /tmp/test_file.txt

# Check the archive was created
ls -lh /tmp/test_file.txt*
```

---

### Exercise 6 — Usage Message Pattern

**Goal:** Write a script that displays helpful usage instructions when called incorrectly.

Create `~/scripts/rename_file.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : rename_file.sh
# Purpose : Rename a file with a usage message
# Usage   : ./rename_file.sh <old_name> <new_name>
# ─────────────────────────────────────────────────────

script_name=$(basename "$0")

# Check correct number of arguments
if [[ "$#" -ne 2 ]]; then
    echo "Error: Wrong number of arguments."
    echo ""
    echo "Usage  : $script_name <old_name> <new_name>"
    echo "Example: $script_name old_file.txt new_file.txt"
    exit 1
fi

old_name="$1"
new_name="$2"

# Check source file exists
if [[ ! -f "$old_name" ]]; then
    echo "Error: '$old_name' does not exist."
    exit 1
fi

mv "$old_name" "$new_name"
echo "Renamed: '$old_name' → '$new_name'"
```

```bash
chmod +x ~/scripts/rename_file.sh

# Test usage message
./rename_file.sh                            # too few args
./rename_file.sh one two three             # too many args

# Test actual rename
touch /tmp/old_name.txt
./rename_file.sh /tmp/old_name.txt /tmp/new_name.txt
ls /tmp/new_name.txt
```

---

### 🧠 Quiz — Test Yourself

1. What are positional parameters in Bash?
2. What does `$0` store?
3. What does `$1` store?
4. If you run `./script.sh apple banana cherry`, what are the values of `$1`, `$2`, and `$3`?
5. Why do you need curly braces for the 10th argument and beyond?
6. What does `$10` expand to if `$1` is `"hello"`?
7. What does `${10}` expand to?
8. How do you set a default value for `$1` if it's not provided?
9. What is the difference between using `read` and positional parameters for input?
10. Why is it a good practice to immediately assign `$1`, `$2` etc. to named variables?
11. Why should you always use double quotes around positional parameters: `"$1"` not `$1`?
12. What does `$0` show when a script is called as `./my_script.sh` vs `bash my_script.sh`?
13. Write a one-line usage check that exits with an error if no argument is given.
14. What `tar` flags create a gzip-compressed archive?

---

## 📎 Quick Reference Card

```
POSITIONAL PARAMETERS
──────────────────────────────────────────────────────────────
$0              Script name (as invoked)
$1              First argument
$2              Second argument
$3 ... $9       Third through ninth arguments
${10}           Tenth argument (BRACES REQUIRED from $10 up)
${11}, ${12}... Same rule for all 2-digit positions

USAGE
──────────────────────────────────────────────────────────────
./script.sh arg1 arg2 arg3      Pass arguments on command line
"./script.sh" "arg with spaces" Use quotes for args with spaces

DEFAULT VALUES
──────────────────────────────────────────────────────────────
${1:-"default"}     Use "default" if $1 is empty/unset
${2:-$HOME}         Use $HOME as default for $2
name=${1:-"World"}  Assign to named var with default

PROFESSIONAL PATTERN
──────────────────────────────────────────────────────────────
# At the top of every script — name your params immediately:
source_file="$1"
destination="$2"
max_retries="${3:-3}"

COMMON VALIDATION PATTERNS
──────────────────────────────────────────────────────────────
# Check argument is provided:
if [[ -z "$1" ]]; then
    echo "Usage: $0 <argument>"
    exit 1
fi

# Check exact number of arguments (uses $# — next episode):
if [[ "$#" -ne 2 ]]; then
    echo "Error: Expected 2 args, got $#"
    echo "Usage: $0 <src> <dest>"
    exit 1
fi

# Clean script name for messages:
script_name=$(basename "$0")

TAR — CREATE A COMPRESSED ARCHIVE
──────────────────────────────────────────────────────────────
tar -czvf archive.tar.gz file_to_compress
     ||||
     |||└─ f: archive filename follows
     ||└── v: verbose output
     |└─── z: gzip compression
     └──── c: create new archive

PARAMETERS OVERVIEW (FULL PICTURE)
──────────────────────────────────────────────────────────────
$name       Variable — referenced by name (user-defined)
$1 $2 ...   Positional parameter — referenced by number
$# $@ $?    Special parameter — referenced by symbol (next episode)
```

---

## 🗂️ Notion Tags (suggested)

`bash` `positional-parameters` `arguments` `command-line` `scripts` `automation` `tar` `shell-scripting` `ZTM` `module-2` `section-02`

---

*← Previous: Module 2 | Section 02 — Getting User Input (read)*
*→ Next Episode: Module 2 | Section 02 — Special Parameters and Variables*
