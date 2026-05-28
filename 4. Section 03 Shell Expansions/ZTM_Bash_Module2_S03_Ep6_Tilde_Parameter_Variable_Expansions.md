# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 03 — Shell Expansions
> **Episode:** 6. Tilde, Parameter and Variable Expansions
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

This episode covers two expansion types from the pipeline: **Tilde Expansion** (step 2) and an advanced deep-dive into **Parameter & Variable Expansion** (step 3). You already know the basics of `$variable` and `${variable}` — this episode introduces powerful operators that modify, default, assign, and transform variable values directly inside the expansion syntax.

---

## 🧠 Theory

### 1. Tilde Expansion (`~`)

Tilde expansion runs **second** in the pipeline (right after brace expansion). The `~` character expands to specific directory paths depending on what follows it.

| Syntax | Expands to | Example output |
|---|---|---|
| `~` | `$HOME` — current user's home directory | `/home/student` |
| `~username` | Home directory of the given user | `/root` (for `~root`) |
| `~+` | `$PWD` — current working directory | `/var/log` |
| `~-` | `$OLDPWD` — previous working directory | `/home/student` |

```bash
# ~ expands to your home directory
echo ~
# → /home/student

# Navigate somewhere then back home
cd /var
cd ~       # same as: cd $HOME
pwd        # → /home/student

# ~username expands to that user's home dir
echo ~root
# → /root

echo ~student
# → /home/student

# ~+ expands to current working directory ($PWD)
cd /var/log
echo ~+
# → /var/log

# ~- expands to PREVIOUS working directory ($OLDPWD)
cd /tmp
echo ~-
# → /var/log  (where we were before)
```

> ⚠️ If `$OLDPWD` is unset (no previous directory navigation in the session), `~-` is **not expanded** — it stays as the literal text `~-`.

---

### 2. Parameter & Variable Expansion — Recap

The `$` character triggers parameter expansion. Both forms work identically in simple cases:

```bash
os=linux

echo $os           # → linux
echo ${os}         # → linux (curly braces optional here)
echo ${os}mint     # → linuxmint  (braces REQUIRED to separate name from suffix)
echo $osmint       # → (empty — bash looks for variable named 'osmint')
```

> 📌 Curly braces are **required** when you need to append text directly after the variable name without a space.

---

### 3. Case Modification Operators

Parameter expansion includes operators that **change the case** of a variable's value directly in the expansion — without modifying the stored variable:

| Operator | Effect | Example |
|---|---|---|
| `${var^}` | First character → UPPERCASE | `${name^}` → `Alice` |
| `${var^^}` | ALL characters → UPPERCASE | `${os^^}` → `LINUX` |
| `${var,}` | First character → lowercase | `${NAME,}` → `aLICE` |
| `${var,,}` | ALL characters → lowercase | `${OS,,}` → `linux` |

```bash
os=linux
name=ALICE

# Uppercase all
echo ${os^^}       # → LINUX

# Lowercase all
echo ${name,,}     # → alice

# Uppercase first letter only
greeting="hello world"
echo ${greeting^}  # → Hello world

# Lowercase first letter only
title="HELLO"
echo ${title,}     # → hELLO
```

> 💡 The variable itself is **not changed** — these operators only affect what is printed/used in that expansion.

---

### 4. Default Value Operators — Handle Unset/Empty Variables

These operators provide fallback values when a variable is not set or is empty — protecting scripts from crashing on undefined variables.

#### `${var:-default}` — Use default if unset or empty

```bash
# If $ip is unset or empty, use "1.1.1.1" as the value
ping -c 3 ${ip:-1.1.1.1}

# If $ip exists and has a value, use it
ip="8.8.8.8"
ping -c 3 ${ip:-1.1.1.1}    # → pings 8.8.8.8 (var exists, default ignored)

# The variable ip is NOT assigned — just substituted for this expansion
unset ip
ping -c 3 ${ip:-1.1.1.1}    # → pings 1.1.1.1
echo $ip                     # → (empty — ip still unset!)
```

> 📌 **`:-` does NOT assign the default to the variable.** It only substitutes it for this one expansion.

#### `${var:=default}` — Assign default if unset or empty

```bash
# If $ip is unset or empty, ASSIGN "8.8.4.4" to it AND use it
ping -c 3 ${ip:=8.8.4.4}    # → pings 8.8.4.4

echo $ip                     # → 8.8.4.4  (NOW the variable is set!)
```

> ✅ **`:=` DOES assign the default to the variable** — the variable now holds the default value for future use too.

#### Side-by-side comparison:

| Operator | Variable unset? | Behaviour | Variable after? |
|---|---|---|---|
| `${var:-default}` | Yes | Substitutes default for this expansion only | Still unset |
| `${var:=default}` | Yes | Substitutes default AND assigns it to `$var` | Now set to default |
| `${var:-default}` | No | Uses variable's own value | Unchanged |
| `${var:=default}` | No | Uses variable's own value | Unchanged |

---

### 5. More Advanced Parameter Expansion Operators

| Operator | What it does |
|---|---|
| `${var:-default}` | Use default if var unset/empty |
| `${var:=default}` | Assign and use default if var unset/empty |
| `${var:?message}` | Print error and exit if var unset/empty |
| `${var:+value}` | Use `value` only if var IS set/non-empty |
| `${#var}` | Length of the variable's value |
| `${var:offset}` | Substring from `offset` to end |
| `${var:offset:length}` | Substring from `offset`, `length` chars |
| `${var#pattern}` | Remove shortest match from beginning |
| `${var##pattern}` | Remove longest match from beginning |
| `${var%pattern}` | Remove shortest match from end |
| `${var%%pattern}` | Remove longest match from end |
| `${var/pattern/replace}` | Replace first match |
| `${var//pattern/replace}` | Replace all matches |

```bash
# Error if unset — great for required parameters
file=${1:?"Error: No filename provided. Usage: $0 <filename>"}

# Use alternative value only if var IS set
debug=true
echo ${debug:+"[DEBUG MODE ON]"}   # → [DEBUG MODE ON]
unset debug
echo ${debug:+"[DEBUG MODE ON]"}   # → (empty — var not set)

# String length
name="Alice"
echo ${#name}         # → 5

# Substrings
str="Hello World"
echo ${str:6}         # → World       (from position 6)
echo ${str:0:5}       # → Hello       (pos 0, length 5)
echo ${str:6:3}       # → Wor         (pos 6, length 3)

# Remove patterns
filename="report.tar.gz"
echo ${filename#*.}   # → tar.gz      (remove up to first dot)
echo ${filename##*.}  # → gz          (remove up to last dot)
echo ${filename%.*}   # → report.tar  (remove from last dot)
echo ${filename%%.*}  # → report      (remove from first dot)

# String replacement
path="/usr/local/bin"
echo ${path/local/global}    # → /usr/global/bin  (first match)

sentence="the cat sat on the mat"
echo ${sentence//the/a}      # → a cat sat on a mat  (all matches)
```

---

### 6. The Full Parameter Expansion Reference

```bash
# Case modification
${var^}     # first char uppercase
${var^^}    # all chars uppercase
${var,}     # first char lowercase
${var,,}    # all chars lowercase

# Default values
${var:-default}   # use default if var unset/empty (no assignment)
${var:=default}   # use default AND assign if var unset/empty
${var:?msg}       # print msg and exit if var unset/empty
${var:+alt}       # use alt only if var IS set and non-empty

# String operations
${#var}              # length
${var:offset}        # substring from offset
${var:offset:len}    # substring from offset, length len
${var#pattern}       # strip shortest prefix match
${var##pattern}      # strip longest prefix match
${var%pattern}       # strip shortest suffix match
${var%%pattern}      # strip longest suffix match
${var/pat/rep}       # replace first occurrence
${var//pat/rep}      # replace all occurrences
${var/#pat/rep}      # replace if at start
${var/%pat/rep}      # replace if at end
```

---

## 🔑 Key Takeaways

- **Tilde `~`** expands to `$HOME` — the current user's home directory.
- **`~username`** expands to that user's home directory.
- **`~+`** expands to `$PWD` (current directory); **`~-`** expands to `$OLDPWD` (previous directory).
- **`${var^^}`** converts to UPPERCASE; **`${var,,}`** converts to lowercase — value is not changed permanently.
- **`${var:-default}`** substitutes a default if the variable is unset/empty — does **NOT** assign it.
- **`${var:=default}`** substitutes a default AND **assigns it** to the variable if unset/empty.
- **`${#var}`** returns the **length** of the variable's value.
- **`${var:offset:length}`** extracts a **substring**.
- **`${var#pattern}`** and **`${var##pattern}`** strip prefixes; **`${var%pattern}`** and **`${var%%pattern}`** strip suffixes.
- **`${var/pat/rep}`** replaces the first match; **`${var//pat/rep}`** replaces all matches.

---

## 💡 Tips & Tricks

**🔹 `:-` for safe default in scripts — use everywhere:**
```bash
#!/bin/bash
# Safe defaults — script won't crash if args are missing
input_file="${1:-/dev/stdin}"
output_dir="${2:-$HOME/output}"
max_lines="${3:-100}"
```

**🔹 `:=` for initialise-once pattern:**
```bash
# Initialise only if not already set (useful in sourced scripts)
: ${CONFIG_FILE:=/etc/myapp/config.conf}
: ${LOG_LEVEL:=info}
echo "Config: $CONFIG_FILE"
```
The `:` (null command) at the start runs the expansion for its side effect (assignment) while discarding the output.

**🔹 `:?` for required variables — fail fast:**
```bash
#!/bin/bash
# Crash immediately with a clear message if required vars are missing
db_host="${DB_HOST:?DB_HOST environment variable is required}"
db_pass="${DB_PASS:?DB_PASS environment variable is required}"
```

**🔹 `##` and `%%` for extracting filenames and extensions:**
```bash
filepath="/home/user/documents/report.tar.gz"

filename="${filepath##*/}"    # → report.tar.gz  (remove path)
dirname="${filepath%/*}"      # → /home/user/documents  (remove filename)
extension="${filename##*.}"   # → gz  (just the final extension)
basename="${filename%%.*}"    # → report  (name without any extension)

echo "File     : $filename"
echo "Dir      : $dirname"
echo "Extension: $extension"
echo "Basename : $basename"
```

**🔹 Case conversion for normalising input:**
```bash
read -rp "Continue? (yes/no): " answer
answer="${answer,,}"    # convert to lowercase
if [[ "$answer" == "yes" ]]; then
    echo "Proceeding..."
fi
```

**🔹 String length for validation:**
```bash
read -rsp "Enter password: " password
echo ""
if [[ "${#password}" -lt 8 ]]; then
    echo "Error: Password must be at least 8 characters."
    exit 1
fi
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Tilde Expansion

**Goal:** Explore all four forms of tilde expansion.

```bash
# Basic ~ expansion
echo ~
echo "Home: ~"    # no expansion inside quotes!
echo "Home: $HOME"

# Navigate and use ~+ and ~-
cd /var/log
echo "Current (~+): ~+"
echo "Current (pwd): $(pwd)"

cd /tmp
echo "Now in: $(pwd)"
echo "Previous (~-): ~-"
echo "Previous (OLDPWD): $OLDPWD"

# ~username (check root's home)
echo ~root
echo ~$(whoami)

# Use ~ in a command
ls ~/scripts | head -5
```

---

### Exercise 2 — Case Modification

**Goal:** Practice all four case operators.

```bash
os="linux mint"
name="aLiCe WoNdErLaNd"
shout="HELLO WORLD"

# All uppercase
echo ${os^^}
echo ${name^^}

# All lowercase
echo ${shout,,}
echo ${name,,}

# First character only — uppercase
echo ${os^}
echo ${name^}

# First character only — lowercase
echo ${shout,}
echo ${name,}

# Practical: normalise user input
read -rp "Enter your OS name: " user_os
echo "You entered: ${user_os,,}"    # always lowercase for comparison
```

---

### Exercise 3 — `:-` vs `:=` Default Operators

**Goal:** See the difference between substituting and assigning a default.

```bash
# Test ${var:-default} — substitutes but does NOT assign
unset ip
echo "Value: ${ip:-1.1.1.1}"    # → 1.1.1.1
echo "ip is now: '$ip'"          # → '' — still empty!

# Test ${var:=default} — substitutes AND assigns
unset ip
echo "Value: ${ip:=8.8.4.4}"   # → 8.8.4.4
echo "ip is now: '$ip'"          # → 8.8.4.4 — variable was set!

# When variable IS set — both use the existing value
ip="192.168.1.1"
echo ${ip:-1.1.1.1}    # → 192.168.1.1  (existing value used)
echo ${ip:=1.1.1.1}    # → 192.168.1.1  (existing value used, no change)
```

---

### Exercise 4 — String Operations

**Goal:** Practice `${#var}`, substrings, stripping, and replacing.

```bash
sentence="The quick brown fox jumps over the lazy dog"
filepath="/home/student/scripts/my_script.sh"

# Length
echo "Length: ${#sentence}"

# Substrings
echo "${sentence:4}"        # from position 4
echo "${sentence:4:5}"      # pos 4, 5 chars
echo "${sentence: -3}"      # last 3 chars (note the space before -)

# Strip path components
echo "Full path  : $filepath"
echo "Filename   : ${filepath##*/}"    # strip longest prefix up to /
echo "Directory  : ${filepath%/*}"     # strip from last /
echo "Extension  : ${filepath##*.}"    # strip up to last dot
echo "No ext     : ${filepath%.*}"     # strip from last dot

# Replace
echo "${sentence/fox/cat}"     # replace first 'fox'
echo "${sentence//the/a}"      # replace all 'the'
```

---

### Exercise 5 — `:?` for Required Variables

**Goal:** Use `:?` to build a script that fails fast with clear error messages.

Create `~/scripts/require_vars.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : require_vars.sh
# Purpose : Demonstrate :? for required variable checking
# Usage   : DB_HOST=myhost DB_USER=admin ./require_vars.sh
# ─────────────────────────────────────────────────────

# These will exit with an error message if not set
db_host="${DB_HOST:?Error: DB_HOST is required. Set it with: export DB_HOST=hostname}"
db_user="${DB_USER:?Error: DB_USER is required. Set it with: export DB_USER=username}"
db_pass="${DB_PASS:?Error: DB_PASS is required. Set it with: export DB_PASS=password}"

# Optional with defaults
db_port="${DB_PORT:-5432}"
db_name="${DB_NAME:-production}"

echo "======================================"
echo "  Database Connection Config"
echo "======================================"
echo "  Host     : $db_host"
echo "  User     : $db_user"
echo "  Password : [${#db_pass} chars — hidden]"
echo "  Port     : $db_port"
echo "  Database : $db_name"
echo "======================================"
```

```bash
chmod +x ~/scripts/require_vars.sh

# Test 1: Run without required vars — should exit with error
./require_vars.sh

# Test 2: Set required vars and run
DB_HOST=localhost DB_USER=admin DB_PASS=secret123 ./require_vars.sh

# Test 3: Use all variables including optional
DB_HOST=localhost DB_USER=admin DB_PASS=secret DB_PORT=3306 DB_NAME=mydb ./require_vars.sh
```

---

### Exercise 6 — Practical File Path Processing Script

**Goal:** Build a script using `##`, `%`, and `${#var}` to process file paths.

Create `~/scripts/file_info.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : file_info.sh
# Purpose : Display detailed info about a file path
# Usage   : ./file_info.sh <filepath>
# ─────────────────────────────────────────────────────

filepath="${1:?Error: Please provide a file path. Usage: $0 <filepath>}"

# Extract components using parameter expansion
full_path="$filepath"
directory="${filepath%/*}"
filename="${filepath##*/}"
extension="${filename##*.}"
basename="${filename%.*}"
path_length="${#filepath}"

echo "======================================"
echo "  File Path Analysis"
echo "======================================"
echo "  Full path  : $full_path"
echo "  Directory  : $directory"
echo "  Filename   : $filename"
echo "  Basename   : $basename"
echo "  Extension  : $extension"
echo "  Path length: $path_length characters"
echo ""

# Existence checks
if [[ -f "$filepath" ]]; then
    echo "  Status     : File EXISTS"
    echo "  Size       : $(du -h "$filepath" | cut -f1)"
elif [[ -d "$filepath" ]]; then
    echo "  Status     : Is a DIRECTORY"
else
    echo "  Status     : Does not exist"
fi
echo "======================================"
```

```bash
chmod +x ~/scripts/file_info.sh

# Test with different paths
./file_info.sh ~/scripts/file_info.sh
./file_info.sh /etc/passwd
./file_info.sh /home/user/documents/report.tar.gz
./file_info.sh ~/scripts   # a directory
```

---

### 🧠 Quiz — Test Yourself

1. What does `~` expand to?
2. What does `~root` expand to?
3. What does `~+` expand to?
4. What does `~-` expand to? What if `$OLDPWD` is unset?
5. What is the difference between `${var}` and `$var`? When are braces required?
6. What does `${os^^}` do to the variable `os="linux"`?
7. What does `${NAME,,}` do to `NAME="ALICE"`?
8. What is the difference between `${var:-default}` and `${var:=default}`?
9. After `echo ${ip:-1.1.1.1}`, is the variable `ip` set? What about after `echo ${ip:=1.1.1.1}`?
10. What does `${var:?message}` do when the variable is unset?
11. What does `${#name}` return for `name="Alice"`?
12. Given `filepath="/home/user/report.tar.gz"`, what does `${filepath##*/}` return?
13. Given the same path, what does `${filepath%/*}` return?
14. What does `${sentence//the/a}` do?

---

## 📎 Quick Reference Card

```
TILDE EXPANSION
──────────────────────────────────────────────────────────────
~           → $HOME (current user's home dir)
~username   → home dir of that user  (e.g. ~root → /root)
~+          → $PWD  (current working directory)
~-          → $OLDPWD (previous working directory)

CASE MODIFICATION
──────────────────────────────────────────────────────────────
${var^}     First char → UPPERCASE
${var^^}    All chars  → UPPERCASE
${var,}     First char → lowercase
${var,,}    All chars  → lowercase

DEFAULT VALUE OPERATORS
──────────────────────────────────────────────────────────────
${var:-default}   Use default if var unset/empty   (no assignment)
${var:=default}   Use default AND assign to var    (assigns!)
${var:?message}   Print error + exit if unset/empty
${var:+alt}       Use alt value ONLY if var is set and non-empty

STRING OPERATIONS
──────────────────────────────────────────────────────────────
${#var}              Length of value
${var:N}             Substring from position N
${var:N:L}           Substring from N, length L
${var: -N}           Last N characters (space before -)

PREFIX STRIPPING
${var#pattern}       Remove shortest prefix match
${var##pattern}      Remove longest prefix match

SUFFIX STRIPPING
${var%pattern}       Remove shortest suffix match
${var%%pattern}      Remove longest suffix match

REPLACEMENT
${var/pat/rep}       Replace first match
${var//pat/rep}      Replace all matches
${var/#pat/rep}      Replace at start
${var/%pat/rep}      Replace at end

COMMON PATTERNS
──────────────────────────────────────────────────────────────
filename="${path##*/}"       Extract filename from path
dirname="${path%/*}"         Extract directory from path
extension="${file##*.}"      Extract last extension
basename="${file%.*}"        Remove last extension
lowercase="${str,,}"         Normalise to lowercase for comparison
```

---

## 🗂️ Notion Tags (suggested)

`bash` `tilde-expansion` `parameter-expansion` `variable-expansion` `default-values` `string-operations` `case-modification` `shell-expansions` `ZTM` `module-2` `section-03`

---

*← Previous: Module 2 | Section 03 — Imposter Syndrome*
*→ Next Episode: Module 2 | Section 03 — Command Substitution*
