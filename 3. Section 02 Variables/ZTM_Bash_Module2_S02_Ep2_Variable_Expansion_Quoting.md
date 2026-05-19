# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 02 — Variables
> **Episode:** 2. Variable Expansion and Quoting
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

This episode dives into two essential Bash concepts that work hand-in-hand: **variable expansion** (how Bash replaces a variable name with its value) and **quoting** (how to control which characters Bash treats as special). Getting these right is the key to writing reliable, bug-free scripts.

---

## 🧠 Theory

### 1. Two Key Terms — Assignment vs Reference

Before going deeper, understand these two complementary operations:

| Operation | What it means | Example |
|---|---|---|
| **Assigning** | Storing a value IN a variable | `os="Kali Linux"` |
| **Referencing** | Getting the value OUT of a variable | `echo $os` |

---

### 2. Variable Expansion — The `$` Character

The `$` character is the trigger for **variable expansion** — it tells Bash to replace the variable name with its stored value.

```bash
age=30
os="Kali Linux"

echo $age        # → 30
echo $os         # → Kali Linux
```

The `$` character actually introduces **three types of expansion** in Bash:

| Expansion Type | Syntax | What it does |
|---|---|---|
| **Parameter/Variable expansion** | `$variable` or `${variable}` | Replaces variable name with its value |
| **Command substitution** | `$(command)` | Replaces with the output of a command |
| **Arithmetic expansion** | `$((expression))` | Replaces with the result of a math expression |

> 📌 This episode focuses on **parameter/variable expansion**. The others come later in the course.

---

### 3. Two Forms of Variable Expansion

There are two syntactically valid ways to expand a variable:

#### Form 1 — Simple (most common):
```bash
echo $variable_name
```

#### Form 2 — Curly brace notation (more powerful):
```bash
echo ${variable_name}
```

Both produce identical output in most cases. However, curly braces are **required** when you need to append text immediately after the variable value without a space:

```bash
os="Windows"

# Problem — Bash looks for a variable called 'os11' (doesn't exist → blank)
echo $os11         # → (empty)

# Solution — curly braces tell Bash where the variable name ends
echo ${os}11       # → Windows11
```

> 🔑 **Rule of thumb:** Use `${variable}` whenever you need to append characters directly to the variable's value. Use `$variable` for standalone references.

---

### 4. Referencing an Undefined Variable — No Error!

Unlike most programming languages, Bash does **not throw an error** if you reference a variable that hasn't been declared. It simply returns an **empty string (blank)**.

```bash
echo $my_undefined_var    # → (prints nothing — empty string)
echo $os11                # → (empty, if os11 was never declared)
```

> ⚠️ This silent behaviour is a common source of bugs. A typo in a variable name will not crash your script — it will silently use an empty value and produce wrong results. Always double-check variable names.

**Protection tip:** Use `set -u` at the top of your script to make Bash treat undefined variables as errors:
```bash
#!/bin/bash
set -u    # treat unset variables as errors
echo $undefined_var    # → Error: undefined_var: unbound variable
```

---

### 5. Quoting — What It Is and Why It Matters

The shell assigns **special meaning** to many characters: `$`, `&`, `*`, `?`, `\`, backticks, spaces, etc.

**Quoting** is the mechanism to **remove or preserve** the special meaning of these characters.

Bash has three quoting mechanisms:

| Mechanism | Character | Behaviour |
|---|---|---|
| **Single quotes** | `'...'` | Preserves literal value of ALL characters inside |
| **Double quotes** | `"..."` | Preserves literal value of most characters EXCEPT `$`, backticks, `\` |
| **Escape character** | `\` | Preserves literal value of the single character that follows |

---

### 6. Single Quotes `'...'` — Literal Everything

Inside single quotes, **every character is taken literally** — no exceptions. No variable expansion, no special characters, nothing.

```bash
os="Linux"
age=30

echo '$os and $age are variables'
# → $os and $age are variables   (no expansion — printed as-is)

echo 'The price is $5'
# → The price is $5   ($ is literal — no expansion)

echo 'Path: C:\Windows\System32'
# → Path: C:\Windows\System32   (backslash is literal)
```

**The one rule — you cannot use a single quote inside single quotes:**
```bash
echo 'it's a test'     # BROKEN — the second ' closes the string early
echo 'it\'s a test'    # STILL BROKEN — backslash doesn't work inside single quotes
```

> ✅ To include a single quote in your text, use double quotes instead, or close the single-quote string and reopen it.

---

### 7. Double Quotes `"..."` — Literal Everything Except `$`, Backtick, `\`

Inside double quotes, most characters are literal — but these three retain their special meaning:

| Character | Inside double quotes | Meaning |
|---|---|---|
| `$` | **Still special** | Variable expansion still happens |
| `` ` `` | **Still special** | Command substitution still happens |
| `\` | **Conditionally special** | Only special when followed by `$`, `` ` ``, `"`, `\`, or newline |

```bash
os="Linux"
age=30

# Variable expansion HAPPENS inside double quotes
echo "My OS is $os and I am $age years old"
# → My OS is Linux and I am 30 years old

# Single quotes inside double quotes — perfectly fine
echo "It's a great day"
# → It's a great day

# Double quote inside double quotes — escape it with backslash
echo "She said \"Hello\""
# → She said "Hello"

# Backslash before a non-special character — left as-is
echo "C:\Windows"
# → C:\Windows   (backslash before W is not special — left unchanged)
```

---

### 8. The Escape Character `\` — Single Character Literal

The backslash `\` removes the special meaning of **the single character immediately following it**.

```bash
os="Linux"

# Escape the $ sign — prevents variable expansion
echo \$os
# → $os   (printed literally, not expanded)

echo \$HOME
# → $HOME   (literal, not the home directory path)
```

**Practical use — the `&` character:**
```bash
# & means "run in background" — a special character
echo me & you
# → runs 'echo me' in background, then tries to run 'you' as a command → error

# Escape it:
echo me \& you
# → me & you   (printed literally)

# Or use quotes:
echo 'me & you'     # → me & you
echo "me & you"     # → me & you
```

**Practical use — the backslash itself:**
```bash
# Printing a Windows path:
echo C:\Windows
# → C:Windows   (backslash is consumed as an escape character — disappears!)

# Fix 1: Double the backslash
echo C:\\Windows
# → C:\Windows

# Fix 2: Use double quotes (backslash before W is not special — left unchanged)
echo "C:\Windows"
# → C:\Windows

# Fix 3: Use single quotes
echo 'C:\Windows'
# → C:\Windows
```

---

### 9. Summary — The Three Quoting Mechanisms Side by Side

```bash
name="Alice"
price=5

# Single quotes — everything is literal, NO expansion
echo '$name costs $price dollars'
# → $name costs $price dollars

# Double quotes — expansion happens, most else is literal
echo "$name costs $price dollars"
# → Alice costs 5 dollars

# Backslash — escape individual characters
echo \$name costs \$price dollars
# → $name costs $price dollars
```

---

### 10. When to Use Which Quote

| Situation | Use |
|---|---|
| Value has spaces and you want expansion | Double quotes `"..."` |
| You want the string 100% literal (no expansion) | Single quotes `'...'` |
| You need to escape just one special character | Backslash `\` |
| Printing literal `$`, `&`, `!` in a string | Single quotes or `\` |
| Printing a Windows path | Double quotes or single quotes |
| Variable references in scripts (general habit) | Always wrap in double quotes: `"$var"` |

---

## 🔑 Key Takeaways

- **Assigning** = storing a value. **Referencing** = reading a value with `$`.
- `$variable` and `${variable}` are equivalent — use `${variable}` when appending text directly.
- `${var}text` is required to separate variable name from surrounding text (e.g. `${os}11`).
- Referencing an **undefined variable returns an empty string** — no error. Use `set -u` to catch this.
- The `$` character triggers: **variable expansion**, **command substitution**, **arithmetic expansion**.
- **Single quotes** `'...'` — everything inside is 100% literal. No exceptions. Cannot contain a single quote.
- **Double quotes** `"..."` — everything is literal EXCEPT `$`, backtick, and `\`. Variable expansion still works.
- **Backslash** `\` — escapes the single character immediately following it.
- Inside double quotes, `\` is only special before: `$`, `` ` ``, `"`, `\`, or newline.
- A **backslash before a non-special character** inside double quotes is left unchanged.
- **Best practice:** Always wrap variable references in double quotes: `"$variable"`.

---

## 💡 Tips & Tricks

**🔹 The golden rule of quoting in Bash:**
> Always use double quotes around variable references: `"$variable"` not `$variable`.
> This prevents **word splitting** and **glob expansion** on variable values.

```bash
filename="my report.txt"
cat $filename       # BROKEN — bash sees 'my' and 'report.txt' as two arguments
cat "$filename"     # CORRECT — the space is preserved inside the value
```

**🔹 Use `set -u` to catch undefined variable bugs early:**
```bash
#!/bin/bash
set -u    # Bash will error on any undefined variable
```
Add this to every script during development — it catches typos in variable names immediately.

**🔹 Use `${var}` notation as a habit for clarity:**
```bash
# Ambiguous — is the variable 'PREFIX' or 'PREFIX_name'?
echo $PREFIX_name

# Clear — variable is 'PREFIX', append '_name' as text
echo ${PREFIX}_name
```

**🔹 Mixing quote types is allowed:**
```bash
# Close single quotes, add a literal quote, reopen single quotes
echo 'it'"'"'s working'
# → it's working

# Or just use double quotes:
echo "it's working"
# → it's working
```

**🔹 Use `printf` instead of `echo` for more control:**
```bash
printf "Name: %s\nAge: %d\n" "$name" "$age"
# More precise formatting — no surprises with special characters
```

**🔹 Test quoting interactively in the terminal before putting in scripts:**
```bash
# Try it out first:
echo '$HOME'     # should print literal $HOME
echo "$HOME"     # should print your home path
echo \$HOME      # should print literal $HOME
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Basic Variable Expansion

**Goal:** Practice both forms of variable expansion and understand the difference.

```bash
# Create variables
age=30
os="Kali Linux"

# Form 1 — Simple expansion
echo $age
echo $os

# Form 2 — Curly brace expansion (identical result here)
echo ${age}
echo ${os}

# Now append text — see where curly braces are REQUIRED
version="Windows"

echo $version10        # what happens? (looks for variable 'version10')
echo ${version}10      # what happens? (appends 10 to 'Windows')
```

**Questions:**
- What did `$version10` print? Why?
- What did `${version}10` print? Why?

---

### Exercise 2 — The Undefined Variable Trap

**Goal:** See that Bash silently returns empty for undefined variables, and learn how to protect against it.

```bash
# Reference a variable that was never declared
echo "Value: $undefined_var"      # → Value:  (empty — no error!)
echo "Value: ${also_undefined}"   # → Value:  (empty — no error!)

# Now protect yourself with set -u
set -u
echo $another_undefined           # → Error: unbound variable

# Turn it off again for the rest of exercises
set +u
```

---

### Exercise 3 — Single Quotes: Everything is Literal

**Goal:** See that single quotes prevent ALL expansion.

```bash
name="Alice"
age=30

# With single quotes — no expansion
echo 'My name is $name and I am $age years old'
# → My name is $name and I am $age years old

echo 'The price is $5 and $10 respectively'
# → The price is $5 and $10 respectively

echo 'Path: C:\Users\alice\Documents'
# → Path: C:\Users\alice\Documents  (backslash literal)

# Try to put a single quote inside single quotes — observe the error
echo 'it's broken'     # try it — what happens?
```

---

### Exercise 4 — Double Quotes: Expansion Works, Most Else Literal

**Goal:** See that double quotes allow variable expansion but preserve other characters.

```bash
name="Alice"
age=30
os="Linux"

# Variable expansion WORKS inside double quotes
echo "My name is $name and I am $age years old"
# → My name is Alice and I am 30 years old

# Single quotes inside double quotes — fine
echo "It's a great day"

# Escape a double quote inside double quotes
echo "She said \"Hello, $name!\""
# → She said "Hello, Alice!"

# Backslash before non-special char — left unchanged
echo "Path: C:\Windows\System32"
# → Path: C:\Windows\System32

# Compare single vs double quotes:
echo 'OS: $os'     # → OS: $os        (literal)
echo "OS: $os"     # → OS: Linux      (expanded)
```

---

### Exercise 5 — The Backslash Escape Character

**Goal:** Practice using `\` to escape individual special characters.

```bash
# Escape $ to prevent variable expansion
echo \$HOME          # → $HOME  (not your home directory)
echo \$USER          # → $USER  (literal)

# Escape & (which means 'run in background')
echo me & you        # observe what happens — background + error
echo me \& you       # → me & you  (literal)

# Printing a backslash itself
echo C:\Windows      # → C:Windows  (backslash disappears!)
echo C:\\Windows     # → C:\Windows  (double backslash = one literal backslash)
echo "C:\Windows"    # → C:\Windows  (in double quotes, \ before W is not special)
echo 'C:\Windows'    # → C:\Windows  (single quotes — all literal)
```

---

### Exercise 6 — Quoting Comparison Side by Side

**Goal:** Run all three quoting mechanisms on the same string and compare output.

```bash
greeting="Hello"
user="World"

echo '--- Single Quotes ---'
echo '$greeting, $user!'
# → $greeting, $user!

echo '--- Double Quotes ---'
echo "$greeting, $user!"
# → Hello, World!

echo '--- Backslash ---'
echo \$greeting, \$user\!
# → $greeting, $user!

echo '--- No Quotes (with expansion) ---'
echo $greeting, $user!
# → Hello, World!
```

---

### Exercise 7 — Real-World Quoting Script

**Goal:** Write a script that shows practical quoting scenarios.

Create `~/scripts/quoting_demo.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : quoting_demo.sh
# Purpose : Demonstrate variable expansion and quoting
# ─────────────────────────────────────────────────────

user_name="Alice"
file_path="/home/alice/my documents/report.txt"
price=25
os_version="Ubuntu 22.04"

echo "========================================="
echo "  Variable Expansion & Quoting Demo"
echo "========================================="

# Safe variable reference with double quotes
echo ""
echo "User     : $user_name"
echo "File     : $file_path"
echo "OS       : $os_version"

# Curly brace expansion to append text
major=22
echo "Release  : Ubuntu ${major}LTS"

# Literal dollar signs with single quotes
echo ""
echo "Price tag: '$price dollars'"
echo 'Ad copy  : "Save $50 today!"'

# Literal backslash — Windows path example
echo ""
echo "Win Path : C:\\Users\\${user_name}\\Documents"
echo 'Win Path : C:\Users\Alice\Documents'

# Mixing quotes
echo ""
echo "Mix test : It's an \"${os_version}\" system"

echo ""
echo "========================================="
```

```bash
chmod +x ~/scripts/quoting_demo.sh
./quoting_demo.sh
```

---

### 🧠 Quiz — Test Yourself

1. What is the difference between **assigning** and **referencing** a variable?
2. What are the **three things** the `$` character triggers in Bash?
3. What is the difference between `$variable` and `${variable}`?
4. When is the `${variable}` curly brace form **required**?
5. What does Bash return when you reference an undefined variable?
6. How can you make Bash throw an error for undefined variables?
7. Name the **three quoting mechanisms** in Bash.
8. Inside **single quotes**, which characters are expanded or treated as special?
9. Inside **double quotes**, which three characters retain their special meaning?
10. What does the backslash `\` do in Bash?
11. How do you print the literal text `$HOME` (not the path)?
12. How do you print a Windows path like `C:\Users\Alice` in Bash?
13. Can you include a single quote inside single quotes? What's the alternative?
14. Inside double quotes, what happens when `\` precedes a non-special character?

---

## 📎 Quick Reference Card

```
VARIABLE EXPANSION
──────────────────────────────────────────────────────────────
$variable           Simple expansion
${variable}         Curly brace expansion (more powerful)
${variable}text     Append text directly (curly braces REQUIRED)
$undeclared_var     → empty string (no error)

set -u              Make undefined variables cause an error
set +u              Turn off the above

THE THREE $ TRIGGERS
──────────────────────────────────────────────────────────────
$variable           Parameter/variable expansion
$(command)          Command substitution
$((expression))     Arithmetic expansion

THREE QUOTING MECHANISMS
──────────────────────────────────────────────────────────────
'single quotes'     EVERYTHING literal — no expansion at all
                    Cannot contain a single quote
"double quotes"     Most things literal EXCEPT: $ backtick \
                    Variable expansion still works inside
\character          Escapes ONE character — makes it literal

WHAT EACH QUOTE DOES TO SPECIAL CHARACTERS
──────────────────────────────────────────────────────────────
          | $ (expand) | & (background) | \ (escape) | ' (quote)
──────────────────────────────────────────────────────────────
No quotes |  expands   |   runs in bg   |  special  |  special
Single '' |  literal   |    literal     |  literal  |  N/A (breaks)
Double "" |  expands   |    literal     |  special* |  literal
Backslash |  literal   |    literal     |  literal  |  literal
(*) \ only special before $ ` " \ or newline inside double quotes

COMMON EXAMPLES
──────────────────────────────────────────────────────────────
echo "$name"               Variable expansion (safe — always do this)
echo '$name'               Literal $name — no expansion
echo \$name                Literal $name — backslash escape
echo "${os}11"             Append 11 to value of os
echo "C:\Windows"          Backslash before W = not special → preserved
echo 'C:\Windows'          All literal — same result
echo C:\\Windows           Double backslash → one literal backslash
echo "She said \"Hi\""     Escaped double quote inside double quotes
echo "it's fine"           Single quote inside double quotes — OK
```

---

## 🗂️ Notion Tags (suggested)

`bash` `variables` `expansion` `quoting` `single-quotes` `double-quotes` `escape` `shell-scripting` `ZTM` `module-2` `section-02`

---

*← Previous: Module 2 | Section 02 — Variables in Bash*
*→ Next Episode: Module 2 | Section 02 — Environment Variables*
