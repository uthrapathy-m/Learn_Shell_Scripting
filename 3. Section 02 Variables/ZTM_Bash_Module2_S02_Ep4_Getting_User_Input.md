# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 02 — Variables
> **Episode:** 4. Getting User Input
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

Almost every real-world script needs to interact with the user — asking for a filename, an IP address, a password, or a confirmation. This episode covers the `read` built-in command in full: reading single values, multiple values at once, prompting with a message, reading silently for passwords, and putting it all together in a practical firewall script.

---

## 🧠 Theory

### 1. Why User Input Matters

Most programs need **input** — data to work with. In shell scripts, that input usually comes from:

- The **keyboard** (interactive input via `read`)
- **Command-line arguments** (positional parameters — covered in the next episode)
- **Files** or **pipelines**

This episode focuses on **keyboard input** using the `read` built-in.

---

### 2. The `read` Command — Core Concept

`read` is a **built-in Bash command** that:
1. **Pauses** script execution
2. **Waits** for the user to type something and press Enter
3. **Stores** what the user typed into a variable

```bash
read variable_name
```

**Basic example:**
```bash
read name
# → (script pauses, cursor blinks, waiting for input)
# User types: John  ← and presses Enter
echo $name
# → John
```

---

### 3. Reading Into Multiple Variables at Once

`read` can split a single line of input into **multiple variables** simultaneously. It uses whitespace (spaces/tabs) as the delimiter by default — this is controlled by the `$IFS` (Internal Field Separator) variable.

```bash
read name age city
```

**How splitting works:**

| User types | `name` gets | `age` gets | `city` gets |
|---|---|---|---|
| `John 30 London` | `John` | `30` | `London` |
| `John 30 London UK` | `John` | `30` | `London UK` ← leftover goes to last var |
| `John 30` | `John` | `30` | `` ← empty |

> 🔑 **Key rule:** If there are **more words than variables**, all leftover words pile into the **last variable**. If there are **fewer words than variables**, the remaining variables get empty strings.

```bash
read first second rest
# User types: one two three four five
echo $first    # → one
echo $second   # → two
echo $rest     # → three four five  (all leftovers go here)
```

---

### 4. The `$REPLY` Variable — Default When No Variable Given

If you run `read` **without specifying a variable name**, the input is automatically stored in the built-in variable `$REPLY`:

```bash
read
# User types: 100
echo $REPLY    # → 100
```

> 💡 `$REPLY` is always available in Bash — it's a pre-defined special variable used as the default target for `read`.

---

### 5. `-p` Flag — Display a Prompt Message

Without a prompt, the user just sees a blinking cursor with no indication of what to type. The `-p` flag lets you display a **message on the same line** before reading input:

```bash
read -p "Enter the IP address: " ip
```

**Breakdown:**
| Part | What it does |
|---|---|
| `read` | The command |
| `-p` | "prompt" flag — display text before reading |
| `"Enter the IP address: "` | The message shown to the user |
| `ip` | The variable that stores the typed input |

> 📌 **Note the trailing space** inside the quotes — `"Enter IP: "` — this gives visual breathing room between the prompt and where the user types.

```bash
read -p "Enter your name: " name
read -p "Enter your age: " age
read -p "Enter your city: " city

echo "Hello $name! You are $age years old and live in $city."
```

---

### 6. `-s` Flag — Silent Input (Passwords)

When reading **sensitive information** like passwords, you don't want the typed characters to appear on screen. The `-s` flag enables **silent mode** — input is read but not displayed (echoed) on the terminal.

```bash
read -s -p "Enter password: " pswd
echo ""   # print a newline after the hidden input
echo "Password stored (not shown): ${#pswd} characters"
```

> ⚠️ After using `-s`, always add `echo ""` on the next line — because the user's Enter key press doesn't produce a newline visually when silent mode is on.

**Combining `-s` and `-p`:**
```bash
read -sp "Enter your password: " password
# -sp is shorthand for -s -p combined
```

---

### 7. Other Useful `read` Flags

| Flag | Meaning | Example |
|---|---|---|
| `-p "msg"` | Display a prompt before reading | `read -p "Enter name: " name` |
| `-s` | Silent — don't echo typed characters | `read -s -p "Password: " pass` |
| `-n N` | Read at most N characters (no Enter needed) | `read -n 1 -p "Press any key..." key` |
| `-t N` | Timeout — stop waiting after N seconds | `read -t 10 -p "Answer (10s): " ans` |
| `-r` | Raw mode — don't treat backslash as escape | `read -r line` |
| `-a` | Read input into an **array** | `read -a my_array` |

> 💡 `-r` is considered best practice for `read` in scripts — without it, a backslash at the end of a line is treated as a line continuation, which can cause surprises.

---

### 8. Real-World Example — IP Blocking Script

The instructor demonstrates a practical use case: a script that asks the user for an IP address and then uses `iptables` to block all traffic from it.

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : block_ip.sh
# Purpose : Block all incoming packets from a given IP
# Usage   : sudo ./block_ip.sh
# Note    : Requires root privileges (iptables)
# ─────────────────────────────────────────────────────

read -p "Enter the IP address or domain to block: " ip

# Drop all incoming packets from the given IP
iptables -I INPUT -s "$ip" -j DROP

echo "All packets from $ip are now being dropped."
```

**How `iptables` works here (focus on `read`):**
| Part | What it does |
|---|---|
| `iptables` | Linux firewall tool |
| `-I INPUT` | Insert rule at the top of the INPUT chain |
| `-s "$ip"` | Match packets from this **source** IP/domain |
| `-j DROP` | **Action:** silently drop the packet |

> ⚠️ Don't worry about `iptables` deeply — the key lesson here is how `read -p` captures user input and how `"$ip"` is then safely used in the command below it.

---

### 9. The `$IFS` Variable — Delimiter Control

`$IFS` stands for **Internal Field Separator**. It defines what characters `read` uses to split input into multiple variables. By default it's **whitespace** (space, tab, newline).

```bash
echo $IFS    # (likely blank — whitespace isn't visible)
cat -A <<< "$IFS"   # shows: ^ I$ (space, tab, newline)
```

You can temporarily change `$IFS` to split on a different character:
```bash
IFS=: read -r user pass uid gid info home shell <<< "root:x:0:0:root:/root:/bin/bash"
echo "User: $user, Shell: $shell"
# → User: root, Shell: /bin/bash
```

> 📌 This is an advanced use case — come back to it once you're comfortable with basic `read`.

---

## 🔑 Key Takeaways

- `read` is a **built-in Bash command** that pauses execution and waits for keyboard input.
- Basic syntax: `read variable_name` — stores the typed line into the variable.
- `read var1 var2 var3` — splits input on whitespace into multiple variables; leftovers go to the last variable.
- If **no variable is given**, input is stored in `$REPLY`.
- `-p "message"` displays a **prompt** on the same line before reading.
- `-s` enables **silent mode** — characters typed are not shown (for passwords).
- Always add `echo ""` after a `-s` read to move to a new line.
- `-r` is best practice in scripts — prevents backslash from being treated as a line continuation.
- `-n N` reads exactly N characters without waiting for Enter.
- `-t N` sets a timeout — stops waiting after N seconds.
- Always wrap the variable in **double quotes** when using it: `"$ip"` not `$ip`.

---

## 💡 Tips & Tricks

**🔹 Always use `-r` in scripts:**
```bash
read -r line    # raw mode — backslash is treated as a literal character
```
Without `-r`, a user typing `hello\` followed by Enter would trigger line continuation — not what you want.

**🔹 Add a newline after silent input:**
```bash
read -sp "Password: " password
echo ""    # ← always do this — moves cursor to next line
```

**🔹 Combine flags cleanly:**
```bash
read -rp "Enter username: " username
read -rsp "Enter password: " password
echo ""
```

**🔹 Read with a timeout and provide a default:**
```bash
read -t 5 -p "Continue? (y/n, default=y): " choice
choice=${choice:-y}     # use 'y' as default if user didn't respond in time
echo "Choice: $choice"
```

**🔹 Simple yes/no confirmation pattern:**
```bash
read -rp "Are you sure? (y/n): " confirm
if [[ "$confirm" == "y" || "$confirm" == "Y" ]]; then
    echo "Proceeding..."
else
    echo "Cancelled."
fi
```

**🔹 Read a single keypress (no Enter needed):**
```bash
read -rn 1 -p "Press any key to continue..."
echo ""
```

**🔹 Always quote the variable when using it after `read`:**
```bash
read -rp "Enter filename: " filename
cat "$filename"     # quoted — safe if filename has spaces
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Basic `read` Usage

**Goal:** Get comfortable with the basic `read` command and how it stores input.

```bash
# In your terminal — try these one by one:

# Read into a single variable
read name
# Type: Alice  → press Enter
echo "You entered: $name"

# Read without a variable — goes to $REPLY
read
# Type: 12345  → press Enter
echo "REPLY contains: $REPLY"

# Read multiple variables at once
read first_name last_name age
# Type: John Doe 30  → press Enter
echo "Name: $first_name $last_name, Age: $age"
```

---

### Exercise 2 — Word Splitting with Multiple Variables

**Goal:** Understand how `read` distributes words across variables.

```bash
# Exactly 3 words → 3 variables filled perfectly
read a b c
# Type: one two three
echo "a=$a, b=$b, c=$c"
# → a=one, b=two, c=three

# More words than variables → leftovers go to last variable
read a b c
# Type: one two three four five
echo "a=$a, b=$b, c=$c"
# → a=one, b=two, c=three four five

# Fewer words than variables → last variables are empty
read a b c
# Type: one two
echo "a=$a, b=$b, c='$c'"
# → a=one, b=two, c=''
```

**Questions:**
- What happened to words 4 and 5 in the second example?
- What value does `c` hold in the third example?

---

### Exercise 3 — Prompting with `-p`

**Goal:** Make your `read` commands user-friendly with prompts.

```bash
# Prompt for a single value
read -rp "Enter your name: " user_name
echo "Hello, $user_name!"

# Prompt for multiple values (one at a time)
read -rp "Enter your city: " city
read -rp "Enter your country: " country
echo "You live in $city, $country."

# Prompt with a combined greeting
read -rp "Enter your favourite OS: " fav_os
echo "$fav_os is a great choice!"
```

---

### Exercise 4 — Silent Input with `-s`

**Goal:** Read sensitive input without it appearing on screen.

```bash
# Read a password silently
read -sp "Enter your password: " password
echo ""     # move to new line after hidden input
echo "Password length: ${#password} characters"
echo "First character: ${password:0:1}"

# Side-by-side comparison: normal vs silent
read -rp "Normal input (visible): " visible_input
echo "You typed: $visible_input"

read -sp "Silent input (hidden): " hidden_input
echo ""
echo "Hidden input stored — length: ${#hidden_input}"
```

---

### Exercise 5 — Timeout and Default Value

**Goal:** Use `-t` to limit how long the script waits for input.

```bash
echo "You have 5 seconds to respond..."
read -rt 5 -p "Enter your name (5s timeout): " name

if [[ -z "$name" ]]; then
    name="Guest"
    echo "No input — using default: $name"
else
    echo "Hello, $name!"
fi
```

---

### Exercise 6 — Write a User Registration Script

**Goal:** Combine all `read` features into a practical script.

Create `~/scripts/user_registration.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : user_registration.sh
# Purpose : Collect user registration details interactively
# ─────────────────────────────────────────────────────

echo "========================================="
echo "         USER REGISTRATION FORM"
echo "========================================="
echo ""

# Collect basic info
read -rp "  Full Name    : " full_name
read -rp "  Email        : " email
read -rp "  City         : " city
read -rp "  Country      : " country

# Collect password silently
read -rsp "  Password     : " password
echo ""
read -rsp "  Confirm Pass : " password_confirm
echo ""

# Validate password match
if [[ "$password" != "$password_confirm" ]]; then
    echo ""
    echo "  ERROR: Passwords do not match. Exiting."
    exit 1
fi

# Collect optional preference
read -rt 5 -p "  Favourite OS (5s): " fav_os
echo ""
fav_os=${fav_os:-"Not specified"}

# Summary
echo ""
echo "========================================="
echo "         REGISTRATION SUMMARY"
echo "========================================="
echo "  Name    : $full_name"
echo "  Email   : $email"
echo "  Location: $city, $country"
echo "  OS      : $fav_os"
echo "  Password: [${#password} characters — stored securely]"
echo "========================================="
echo ""
echo "Registration complete! Welcome, $full_name."
```

```bash
chmod +x ~/scripts/user_registration.sh
./user_registration.sh
```

---

### Exercise 7 — Build the IP Blocker Script

**Goal:** Re-create the instructor's `block_ip.sh` script with improvements.

Create `~/scripts/block_ip.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : block_ip.sh
# Purpose : Block incoming packets from a given IP/domain
# Usage   : sudo ./block_ip.sh
# Requires: Root privileges (iptables)
# ─────────────────────────────────────────────────────

# Check that the script is run as root
if [[ "$EUID" -ne 0 ]]; then
    echo "Error: This script must be run as root (use sudo)."
    exit 1
fi

echo "============================================="
echo "        IP / DOMAIN BLOCKER"
echo "============================================="
echo ""

read -rp "Enter the IP address or domain to block: " ip

# Validate input is not empty
if [[ -z "$ip" ]]; then
    echo "Error: No IP address entered. Exiting."
    exit 1
fi

# Confirm with user before applying
read -rp "Block all traffic from '$ip'? (y/n): " confirm
if [[ "$confirm" != "y" && "$confirm" != "Y" ]]; then
    echo "Cancelled. No rules applied."
    exit 0
fi

# Apply the firewall rule
iptables -I INPUT -s "$ip" -j DROP
echo ""
echo "SUCCESS: All incoming packets from '$ip' are now being dropped."
echo "To undo: sudo iptables -D INPUT -s $ip -j DROP"
```

```bash
chmod +x ~/scripts/block_ip.sh
# To run: sudo ./block_ip.sh
```

> ⚠️ Don't run this without knowing what it does — it modifies your firewall rules. Test it in a safe/lab environment.

---

### 🧠 Quiz — Test Yourself

1. What does the `read` command do in a Bash script?
2. What happens to script execution when `read` is encountered?
3. Write the command to read user input into a variable called `city`.
4. If you run `read first second rest` and the user types `a b c d e`, what are the values of `first`, `second`, and `rest`?
5. What variable stores input when `read` is used without a variable name?
6. What does the `-p` flag do?
7. Write a `read` command that displays the prompt `"Enter your age: "` and stores the result in `age`.
8. What does the `-s` flag do and when would you use it?
9. Why should you add `echo ""` after a `read -s` command?
10. What does the `-n 1` flag do?
11. What does the `-t 10` flag do?
12. Why should you always quote the variable after reading into it: `"$variable"` instead of `$variable`?
13. What does `-r` do, and why is it considered best practice in scripts?
14. What is `$IFS` and how does it affect `read`?

---

## 📎 Quick Reference Card

```
BASIC SYNTAX
──────────────────────────────────────────────────────────────
read var                  Read input into var
read var1 var2 var3       Read input into multiple vars (split on whitespace)
read                      Read input → stored in $REPLY (default)

READ FLAGS
──────────────────────────────────────────────────────────────
-p "message"    Display prompt before reading
-s              Silent mode — don't echo typed characters (passwords)
-r              Raw mode — backslash is literal (BEST PRACTICE in scripts)
-n N            Read exactly N characters (no Enter needed)
-t N            Timeout — stop waiting after N seconds
-a array_name   Read words into an array

COMMON COMBINATIONS
──────────────────────────────────────────────────────────────
read -rp "Enter name: " name          Prompt + raw mode
read -rsp "Enter password: " pass     Silent + prompt + raw
read -rn 1 -p "Press any key..." k    Single keypress
read -rt 10 -p "Answer (10s): " ans   Prompt + timeout

PATTERNS
──────────────────────────────────────────────────────────────
# Default value if no input or timeout:
choice=${choice:-"default_value"}

# Check if input is empty:
if [[ -z "$var" ]]; then echo "empty"; fi

# Yes/no confirmation:
read -rp "Are you sure? (y/n): " confirm
[[ "$confirm" == [yY] ]] && echo "Yes" || echo "No"

# Always add newline after silent read:
read -sp "Password: " pass
echo ""

SPECIAL VARIABLES
──────────────────────────────────────────────────────────────
$REPLY          Default variable when read has no variable name
$IFS            Internal Field Separator — controls word splitting
                Default: space, tab, newline
${#var}         Length of variable value (e.g. password length check)
```

---

## 🗂️ Notion Tags (suggested)

`bash` `user-input` `read` `interactive` `password` `prompt` `shell-scripting` `ZTM` `module-2` `section-02`

---

*← Previous: Module 2 | Section 02 — Environment and Shell Local Variables*
*→ Next Episode: Module 2 | Section 02 — Special Variables and Positional Parameters*
