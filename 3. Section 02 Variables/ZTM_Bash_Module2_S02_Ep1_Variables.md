# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 02 — Variables
> **Episode:** 1. Variables in Bash
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

This episode introduces one of the most fundamental building blocks of any programming language — **variables**. You'll learn how to create, use, list, remove, and protect variables in Bash, and understand the naming conventions that keep your scripts clean and professional.

---

## 🧠 Theory

### 1. What is a Variable?

A **variable** is a label — a name — for a **memory location** where a value is stored. That value can be read, used, and changed throughout a script.

```
Variable Name  →  Points to a Memory Location  →  Holds a Value
    os                   (RAM address)                "linux"
```

> 💡 Think of a variable like a labelled box — you give the box a name, put something inside it, and can open it anytime to read or change what's in it.

---

### 2. Creating a Variable

#### Syntax:
```bash
variable_name=value
```

#### Rules — NO spaces around `=`:
```bash
os=linux          # correct
os = linux        # WRONG — bash treats 'os' as a command
os= linux         # WRONG — assigns empty string, 'linux' treated as command
```

> ⚠️ This is one of the most common beginner mistakes. Bash is strict — **no spaces around `=`**, ever.

#### What happens with spaces?
```bash
os = linux
# bash reads 'os' as a command, '=' and 'linux' as its arguments
# Error: os: command not found
```

---

### 3. Variable Values — Types

Bash is a **weakly typed** language — you do NOT declare types explicitly.

| Value assigned | Bash treats it as |
|---|---|
| `age=30` | Integer |
| `name="Alice"` | String |
| `os=linux` | String (no quotes needed if no spaces) |
| `distro="Kali Linux"` | String (quotes required — has a space) |

> 📌 **Important:** Bash has **no floating point numbers** — only integers. For decimal arithmetic you need external tools like `bc` or `awk`.

#### Strings with spaces — use double quotes:
```bash
distro="Kali Linux"    # correct — space inside value requires quotes
distro=Kali Linux      # WRONG — 'Linux' treated as a command
```

---

### 4. Reading / Using a Variable

To read the value stored in a variable, prefix its name with `$`:

```bash
os=linux
echo $os          # → linux
echo "My OS is $os"  # → My OS is linux
```

---

### 5. Composing Variables from Other Variables

You can build a variable's value using other variables:

```bash
os=Linux
distro=Ubuntu
my_distro="$os $distro"

echo $my_distro    # → Linux Ubuntu
```

> 💡 Use **double quotes** when combining variables — this ensures the values are expanded correctly and spaces are handled safely.

---

### 6. Listing All Variables — `set`

To see **all currently defined variables and functions** in the shell:

```bash
set
```

The output is very long. Use `grep` to filter for a specific variable:

```bash
set | grep distro
set | grep os
```

---

### 7. Removing a Variable — `unset`

To **delete** a variable and free its value:

```bash
distro="Ubuntu"
echo $distro      # → Ubuntu

unset distro
echo $distro      # → (empty — variable is gone)
```

> ✅ After `unset`, the variable no longer exists. Using it prints nothing (empty string).

---

### 8. Read-Only Variables (Constants) — `declare -r`

Sometimes you want a variable whose value **can never change** — a constant. Use `declare -r`:

```bash
declare -r LOGDIR=/var/log

ls $LOGDIR        # → works fine — reading is allowed
```

**Trying to change or unset it causes an error:**
```bash
LOGDIR=/etc       # Error: LOGDIR: readonly variable
unset LOGDIR      # Error: LOGDIR: cannot unset: readonly variable
```

> 📌 **Convention:** Constants are written in **ALL_CAPS** with underscores. Declare them at the **top of the script**.

```bash
declare -r SECONDS_IN_HOUR=3600
declare -r MAX_RETRIES=5
declare -r CONFIG_FILE="/etc/myapp/config.conf"
```

---

### 9. Variable Naming Conventions

This is critical for writing clean, professional scripts. Follow these rules:

#### Regular Variables — lowercase with underscores:
```bash
# Good
first_name="Alice"
backup_dir="/backup"
server_name="web01"
retry_count=3

# Bad
FirstName="Alice"       # avoid CamelCase in bash
BACKUP_DIR="/backup"    # uppercase is reserved for constants/env vars
```

#### Constants & Environment Variables — ALL_CAPS with underscores:
```bash
declare -r MAX_SIZE=100
declare -r LOG_FILE="/var/log/app.log"
PATH="/usr/bin:/bin"    # built-in env variable — already uppercase
```

#### Naming Rules — what is and isn't allowed:

| Rule | Valid | Invalid |
|---|---|---|
| Can start with a letter | `name=x` ✅ | `4name=x` ❌ (starts with digit) |
| Can start with underscore | `_name=x` ✅ | `@name=x` ❌ (special char) |
| Can contain letters, digits, underscores | `server_01=x` ✅ | `a.b=x` ❌ (dot not allowed) |
| No spaces in name | `my_var=x` ✅ | `my var=x` ❌ (space not allowed) |
| No special characters | `count=5` ✅ | `n@me=x` ❌ (@ not allowed) |

#### Valid name examples:
```bash
u4=false
server_name="apex12.5"
A=67
_private_var="hidden"
```

#### Invalid name examples:
```bash
4u=true          # starts with a digit
a.b=100          # contains a dot
n@mary=100       # contains @
my var=hello     # contains a space
```

---

### 10. Built-in Environment Variables

The OS, shell, and startup scripts define many variables already — all in **UPPERCASE**:

```bash
echo $PATH       # directories searched for commands
echo $USER       # current logged-in username
echo $HOME       # current user's home directory
echo $HISTFILE   # file where command history is saved
echo $HISTSIZE   # number of commands kept in history
echo $SHELL      # current shell
echo $PWD        # current working directory
echo $HOSTNAME   # name of the computer
```

> ⚠️ **Never name your own variables** `PATH`, `USER`, `HOME`, etc. — you'll overwrite critical system variables and break things. This is exactly why the convention is to use **lowercase for your own variables**.

---

## 🔑 Key Takeaways

- A variable is a **named memory location** that stores a value.
- Syntax: `variable_name=value` — **no spaces around `=`**, ever.
- To **read** a variable's value, prefix with `$`: `echo $variable_name`.
- Use **double quotes** for values that contain spaces: `distro="Kali Linux"`.
- Bash is **weakly typed** — no need to declare types. Numbers are integers, text is strings.
- **No floating point** in Bash — integers only (use `bc` for decimals).
- Compose variables from other variables: `full="$first $last"`.
- List all variables with `set` — pipe to `grep` to filter: `set | grep varname`.
- Remove a variable with `unset varname`.
- Declare a **read-only constant** with `declare -r VARNAME=value`.
- Constants and env vars use **ALL_CAPS**; your own variables use **lowercase_with_underscores**.
- Variable names cannot start with a digit or contain special characters (`.`, `@`, `!`, etc.).
- Never overwrite built-in env vars like `$PATH`, `$HOME`, `$USER`.

---

## 💡 Tips & Tricks

**🔹 Always quote variable references to avoid word splitting:**
```bash
filename="my file.txt"
cat $filename       # BROKEN — bash sees 'my' and 'file.txt' as two args
cat "$filename"     # CORRECT — quotes preserve the space
```
Get into the habit of always writing `"$variable"` instead of `$variable`.

**🔹 Check if a variable is defined:**
```bash
if [ -z "$my_var" ]; then
  echo "my_var is empty or not set"
fi
```

**🔹 See the value of a variable without echo:**
```bash
# Just type the variable name with $ in an interactive shell
declare -p distro    # shows: declare -- distro="Ubuntu"
declare -p LOGDIR    # shows: declare -r LOGDIR="/var/log"
```
`declare -p` is great for debugging — it shows the variable and its attributes.

**🔹 Assign default value if variable is empty:**
```bash
name=${name:-"default_name"}
# If $name is empty or unset, use "default_name"
```

**🔹 Constants at the top of every script — professional pattern:**
```bash
#!/bin/bash
# ── CONSTANTS ──────────────────────────────
declare -r SCRIPT_NAME="backup.sh"
declare -r VERSION="1.0"
declare -r LOG_DIR="/var/log"

# ── VARIABLES ──────────────────────────────
backup_count=0
source_dir="/home/user/documents"
```

**🔹 Bash has no float — use `bc` for decimals:**
```bash
result=$(echo "scale=2; 10 / 3" | bc)
echo $result    # → 3.33
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Create and Read Variables

**Goal:** Get comfortable with the basic syntax of defining and reading variables.

```bash
# Open a terminal and try these one by one:

# String variable (no spaces in value)
os=linux
echo $os
echo "My operating system is: $os"

# String variable with spaces — must use quotes
distro="Kali Linux"
echo $distro
echo "My distro is: $distro"

# Integer variable
age=30
echo "Age: $age"

# Try the WRONG way — observe the error
os = linux     # what error do you get?
```

**Questions:**
- What error message do you see when you add spaces around `=`?
- What does Bash interpret `os` as when there is a space after it?

---

### Exercise 2 — Compose Variables from Other Variables

**Goal:** Build a variable using the values of existing variables.

```bash
first_name="John"
last_name="Doe"
full_name="$first_name $last_name"

echo $full_name         # → John Doe

os="Linux"
distro="Ubuntu"
my_os="$os - $distro"

echo $my_os             # → Linux - Ubuntu
echo "I use $my_os"     # → I use Linux - Ubuntu
```

---

### Exercise 3 — List and Search Variables

**Goal:** Use `set` and `grep` to find variables in the current shell.

```bash
# Create some variables first
city="Chennai"
country="India"
age=25

# List ALL shell variables (very long output)
set

# Search for your specific variables
set | grep city
set | grep country
set | grep age

# Search for built-in env variables
set | grep HOME
set | grep PATH
```

---

### Exercise 4 — Remove Variables with `unset`

**Goal:** Practice deleting variables and observe what happens after.

```bash
# Create a variable
distro="Ubuntu"
echo "Before unset: $distro"    # → Ubuntu

# Remove it
unset distro
echo "After unset: $distro"     # → (empty)

# Try to unset a variable that doesn't exist — safe, no error
unset nonexistent_var
echo "No error from unsetting a non-existent variable"
```

---

### Exercise 5 — Read-Only Constants

**Goal:** Declare constants and observe the protection they provide.

```bash
# Declare a read-only constant
declare -r MAX_CONNECTIONS=100
declare -r LOG_DIR="/var/log"
declare -r APP_NAME="MyApp"

# Read the constants — works fine
echo "App: $APP_NAME"
echo "Max connections: $MAX_CONNECTIONS"
echo "Logs at: $LOG_DIR"
ls $LOG_DIR

# Try to change a constant — observe the error
MAX_CONNECTIONS=200       # Error: readonly variable
LOG_DIR="/tmp"            # Error: readonly variable

# Try to unset a constant — observe the error
unset MAX_CONNECTIONS     # Error: cannot unset readonly variable
```

---

### Exercise 6 — Write a Variables Script

**Goal:** Write a complete script that puts everything together.

Create `~/scripts/variables_demo.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : variables_demo.sh
# Purpose : Demonstrate variable concepts in Bash
# ─────────────────────────────────────────────────────

# ── CONSTANTS (read-only, UPPERCASE, declared at top)
declare -r SCRIPT_VERSION="1.0"
declare -r AUTHOR="Your Name"
declare -r MAX_BACKUPS=7

# ── VARIABLES (lowercase with underscores)
user_name=$(whoami)
hostname_val=$(hostname)
current_date=$(date '+%Y-%m-%d')
os_name="Linux"
distro_name="Ubuntu"
full_os="$os_name - $distro_name"

# ── OUTPUT
echo "========================================"
echo "  Variables Demo — v$SCRIPT_VERSION"
echo "  Author: $AUTHOR"
echo "========================================"
echo ""
echo "User       : $user_name"
echo "Hostname   : $hostname_val"
echo "Date       : $current_date"
echo "OS         : $full_os"
echo "Max Backups: $MAX_BACKUPS"
echo ""

# Try to change a constant (uncomment to test the error):
# MAX_BACKUPS=10

# Remove a variable and show it's gone
echo "Before unset: distro_name = $distro_name"
unset distro_name
echo "After unset : distro_name = '$distro_name'"
echo ""
echo "========================================"
```

```bash
chmod +x ~/scripts/variables_demo.sh
./variables_demo.sh
```

---

### Exercise 7 — Naming Convention Practice

**Goal:** Identify valid and invalid variable names.

For each name below, decide if it's VALID or INVALID and explain why:

```
1u=5              → ?
user_name=Alice   → ?
_temp=99          → ?
my.var=hello      → ?
SERVER_IP=1.2.3.4 → ?
n@me=test         → ?
A=67              → ?
4ever=true        → ?
first-name=Bob    → ?    (hint: dash is a special char)
backup_2=yes      → ?
```

Then write 5 valid variable names of your own for a fictional backup script.

---

### 🧠 Quiz — Test Yourself

1. What is a variable in Bash?
2. What is the correct syntax to create a variable named `city` with the value `Chennai`?
3. Why are spaces NOT allowed around the `=` sign?
4. How do you assign a value with spaces to a variable?
5. How do you read/print the value of a variable called `city`?
6. Is Bash strongly or weakly typed? What does that mean?
7. Does Bash support floating-point numbers natively?
8. What command lists all current shell variables?
9. How do you delete a variable called `temp`?
10. How do you declare a read-only constant called `MAX_SIZE` with value `500`?
11. Can you `unset` a read-only variable? What happens if you try?
12. What naming convention should your own variables follow? What about constants?
13. Name three built-in environment variables and what they store.
14. Why is it dangerous to name your variable `PATH`?

---

## 📎 Quick Reference Card

```
CREATING VARIABLES
──────────────────────────────────────────────────────────────
name=value               No spaces around = (REQUIRED)
distro="Kali Linux"      Quotes required if value has spaces
age=30                   Integer (no type declaration needed)
full="$first $last"      Compose from other variables

READING VARIABLES
──────────────────────────────────────────────────────────────
echo $name               Print value
echo "$name"             Print value (safer — always quote)
echo "Hello $name"       Use inside a string

LISTING & REMOVING
──────────────────────────────────────────────────────────────
set                      List ALL variables and functions
set | grep varname       Search for a specific variable
unset varname            Delete a variable
declare -p varname       Show variable + its attributes

READ-ONLY CONSTANTS
──────────────────────────────────────────────────────────────
declare -r VARNAME=value     Declare a constant (cannot change or unset)
declare -r MAX=100
declare -r LOG_DIR="/var/log"

NAMING CONVENTIONS
──────────────────────────────────────────────────────────────
my_variable=value        Your variables → lowercase_with_underscores
MY_CONSTANT=value        Constants/env vars → ALL_CAPS_WITH_UNDERSCORES
$PATH $HOME $USER        Built-in env vars — already uppercase — DO NOT overwrite

VALID vs INVALID NAMES
──────────────────────────────────────────────────────────────
Valid   : name, _name, my_var, server01, A, u4
Invalid : 4name (digit start), my.var (dot), n@me (@), my var (space)

BUILT-IN ENVIRONMENT VARIABLES
──────────────────────────────────────────────────────────────
$PATH       Directories searched for commands
$HOME       Current user's home directory
$USER       Current logged-in username
$SHELL      Current shell
$PWD        Current working directory
$HOSTNAME   Computer hostname
$HISTFILE   Command history file location
$HISTSIZE   Number of commands kept in history
```

---

## 🗂️ Notion Tags (suggested)

`bash` `variables` `constants` `declare` `unset` `naming-conventions` `shell-scripting` `ZTM` `module-2` `section-02`

---

*← Previous: Module 2 | Section 01 — Running Scripts (Section 01 Complete)*
*→ Next Episode: Module 2 | Section 02 — Variable Expansion & Quoting*
