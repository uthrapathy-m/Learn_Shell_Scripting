# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 03 — Shell Expansions
> **Episode:** 12. Word Splitting
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

Word splitting is the **seventh expansion** in the pipeline — and also one of the most confusing concepts for beginners. It explains why unquoted variable expansions can silently break into multiple words and cause unexpected behaviour. This episode clarifies exactly when it happens, what controls it (`$IFS`), and the single most important defence against it: **double quotes**.

---

## 🧠 Theory

### 1. What is Word Splitting?

Word splitting is the process by which Bash **breaks the results of certain unquoted expansions into separate words** (tokens) before passing them to the command being executed.

```bash
dirs="d1 d2 d3"

mkdir $dirs      # ← unquoted — word splitting fires → creates 3 dirs: d1, d2, d3
mkdir "$dirs"    # ← quoted   — no word splitting    → creates 1 dir: "d1 d2 d3"
```

> 💡 Word splitting is automatic and invisible — Bash does it silently, which is why it causes so much confusion. You don't see it happening; you just see the unexpected result.

---

### 2. When Does Word Splitting Happen?

Word splitting is performed on the **results of unquoted expansions** after:

```
1. Brace Expansion
2. Tilde Expansion
3. Parameter & Variable Expansion    ← splits here if unquoted
4. Command Substitution              ← splits here if unquoted
5. Arithmetic Expansion              ← splits here if unquoted
6. Process Substitution
7. WORD SPLITTING                    ← step 7
8. Filename Expansion
```

> ✅ **Key rule:** Word splitting ONLY happens on the **results of expansions**. Literal text (words you type directly, not through a `$`) is never word-split.

**NOT split:**
```bash
mkdir d1 d2 d3     # literal words — already separate, no splitting
```

**SPLIT (because it's an expansion result):**
```bash
dirs="d1 d2 d3"
mkdir $dirs        # $dirs expands → "d1 d2 d3" → word-split → d1, d2, d3
```

---

### 3. The `$IFS` Variable — The Delimiter

Word splitting uses `$IFS` (**Internal Field Separator**) to decide where to split.

**Default value of `$IFS`:** space, tab, and newline (three whitespace characters).

```bash
# View $IFS — looks blank because whitespace is invisible
echo $IFS

# View $IFS with invisible chars made visible (${var@Q} quoting format)
echo "${IFS@Q}"
# → $' \t\n'   (shows: space, \t = tab, \n = newline)
```

**The three default delimiters:**

| Character | Visible representation | Meaning |
|---|---|---|
| ` ` (space) | `(space)` | Most common delimiter |
| `\t` | Tab | Horizontal tab |
| `\n` | Newline | Line break |

---

### 4. The Default IFS in Action — Splitting on Spaces

```bash
dirs="d1 d2 d3"

# UNQUOTED expansion — word splitting fires on spaces → 3 words
mkdir $dirs
# Bash sees: mkdir d1 d2 d3
# Result: 3 directories created: d1/, d2/, d3/

ls -d d?
# → d1  d2  d3  ✅

# QUOTED expansion — no word splitting → 1 word
mkdir "$dirs"
# Bash sees: mkdir "d1 d2 d3"
# Result: 1 directory created with a space in its name: "d1 d2 d3"/

ls -d "d1 d2 d3"
# → d1 d2 d3/  (one directory with spaces)
```

---

### 5. Changing `$IFS` — Custom Delimiter

You can change `$IFS` to split on any character:

```bash
# Change IFS to colon (:)
IFS=:

dirs="d1:d2:d3"
mkdir $dirs
# Word splitting now splits on : → creates 3 dirs: d1, d2, d3
ls -d d?
# → d1  d2  d3  ✅

# Now spaces NO LONGER split (not in IFS)
dirs="d1 d2 d3"    # spaces as separator
mkdir $dirs
# Word splitting does NOT split on spaces → creates 1 dir: "d1 d2 d3"
ls -d "d1 d2 d3"
# → d1 d2 d3/   (one directory! spaces were not the delimiter)
```

> ⚠️ Always **restore `$IFS`** after changing it, or unexpected splitting will break other parts of your script.

```bash
# Save and restore pattern — professional approach
OLD_IFS="$IFS"
IFS=:
# ... do work with custom IFS ...
IFS="$OLD_IFS"   # restore

# Or unset to restore default:
unset IFS        # IFS returns to default (space, tab, newline)
```

---

### 6. The Golden Rule — Always Quote Your Variables

The single most important takeaway from this episode:

> **Wrap variable expansions in double quotes `"$var"` to prevent word splitting.**

```bash
filename="my report.txt"

# WRONG — word splitting breaks filename into 2 words
cat $filename
# Bash sees: cat my report.txt
# Error: my: No such file or directory

# CORRECT — double quotes prevent word splitting
cat "$filename"
# Bash sees: cat "my report.txt"
# Works! ✅
```

This rule applies to ALL expansions:

```bash
output=$(command with spaces in output)
echo $output      # WRONG — output may be word-split
echo "$output"    # CORRECT — output preserved as-is
```

---

### 7. When Word Splitting is USEFUL

Sometimes word splitting is intentional — you want a variable to expand into multiple arguments:

```bash
# Intentional word splitting — no quotes
dirs="d1 d2 d3"
mkdir $dirs          # intentionally creates 3 separate directories

# Same with command output
files=$(ls *.sh)
for f in $files; do  # iterates over each filename
    echo "$f"
done
```

> 📌 **The rule of thumb:**
> - **Files/paths/values with possible spaces** → always quote
> - **Space-separated lists you want to split** → leave unquoted (intentional)
> - **When in doubt** → quote it

---

### 8. Viewing Word Splitting in Action with `set -x`

Use Bash's trace mode to see exactly what words a command receives after splitting:

```bash
set -x      # enable trace — shows commands after ALL expansions

dirs="d1 d2 d3"
mkdir $dirs
# Trace shows: + mkdir d1 d2 d3   (3 separate words after splitting)

mkdir "$dirs"
# Trace shows: + mkdir 'd1 d2 d3'  (1 word — splitting prevented)

set +x      # disable trace
```

---

## 🔑 Key Takeaways

- Word splitting is **step 7** in the expansion pipeline — after all other expansions.
- It only applies to the **results of unquoted expansions** (`$var`, `$(cmd)`, `$((expr))`).
- Literal words typed directly (not through expansions) are **never split**.
- The delimiter is `$IFS` — default value: **space, tab, newline**.
- Unquoted `$dirs` where `dirs="d1 d2 d3"` → **3 separate words**: `d1`, `d2`, `d3`.
- Quoted `"$dirs"` → **1 word**: `d1 d2 d3` (splitting prevented).
- **Changing `$IFS`** changes what character is used to split.
- Always **save and restore `$IFS`** when changing it.
- **Golden rule: quote your variable expansions** with `"$var"` to prevent accidental splitting.
- Use `set -x` to **trace and debug** how word splitting affects your commands.

---

## 💡 Tips & Tricks

**🔹 The quoting golden rule — memorise this:**
```bash
# Risky — word splitting may happen
command $variable

# Safe — double quotes prevent word splitting
command "$variable"
```

**🔹 View `$IFS` with invisible chars visible:**
```bash
echo "${IFS@Q}"     # → $' \t\n'  (space, tab, newline)
# or
cat -A <<< "$IFS"   # → shows ^ for invisible chars
```

**🔹 Always save/restore `$IFS` when changing it:**
```bash
old_ifs="$IFS"
IFS=:
# ... your work ...
IFS="$old_ifs"
```

**🔹 Use `read -r` with custom `$IFS` for parsing:**
```bash
# Parse a colon-separated line (like /etc/passwd)
IFS=: read -r user pass uid gid info home shell <<< "root:x:0:0:root:/root:/bin/bash"
echo "User: $user, Shell: $shell"
```

**🔹 Use `set -x` to debug word splitting live:**
```bash
set -x
your_command $your_variable
set +x
```

**🔹 Array is the clean alternative to splitting a variable:**
```bash
# Instead of relying on word splitting:
dirs="d1 d2 d3"
mkdir $dirs           # fragile — relies on word splitting

# Use an array — explicit, safe:
dirs=(d1 d2 d3)
mkdir "${dirs[@]}"    # each element safely quoted
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — See Word Splitting Happen

**Goal:** Create directories using word splitting and prove what happened.

```bash
# Step 1: Create variable with space-separated values
dirs="d1 d2 d3"
echo "Variable value: $dirs"

# Step 2: Use WITHOUT quotes — word splitting fires
cd /tmp
mkdir $dirs
echo "Directories created:"
ls -d d?

# Step 3: Verify 3 separate directories exist
echo "Count: $(ls -d d? | wc -l) directories"

# Step 4: Clean up
rm -rf d1 d2 d3
```

---

### Exercise 2 — Quotes Prevent Word Splitting

**Goal:** Compare quoted vs unquoted expansion with `mkdir`.

```bash
cd /tmp

dirs="d1 d2 d3"

# WITHOUT quotes → word splitting → 3 separate dirs
mkdir $dirs
echo "Unquoted result:"
ls -d d* 2>/dev/null
rm -rf d1 d2 d3

# WITH quotes → no splitting → 1 dir with spaces in name
mkdir "$dirs"
echo "Quoted result:"
ls -d "d1 d2 d3" 2>/dev/null
ls -d d* 2>/dev/null

# Clean up
rm -rf "d1 d2 d3"
```

---

### Exercise 3 — View `$IFS` and Change It

**Goal:** Inspect `$IFS` and change the delimiter.

```bash
# View the default IFS
echo "Default IFS (invisible):"
echo "$IFS"

echo "Default IFS (visible):"
echo "${IFS@Q}"
# or: printf '%q\n' "$IFS"

# Change IFS to colon
IFS=:
dirs="d1:d2:d3"

cd /tmp
mkdir $dirs
echo "Colon-split result:"
ls -d d?
rm -rf d1 d2 d3

# Now try with spaces — they won't split (not in IFS)
dirs="d1 d2 d3"
mkdir $dirs
echo "Space with colon IFS — only 1 dir:"
ls -d "d1 d2 d3" 2>/dev/null
rm -rf "d1 d2 d3"

# Restore IFS
unset IFS
echo "IFS restored to default:"
echo "${IFS@Q}"
```

---

### Exercise 4 — Trace Word Splitting with `set -x`

**Goal:** Use Bash trace mode to see exactly what words a command receives.

```bash
dirs="alpha beta gamma"
filename="my important file.txt"

set -x    # enable trace

# These show what bash sees AFTER word splitting:
echo $dirs          # trace: + echo alpha beta gamma
echo "$dirs"        # trace: + echo 'alpha beta gamma'

ls $filename        # trace: + ls my important file.txt  ← 3 args (broken!)
ls "$filename"      # trace: + ls 'my important file.txt' ← 1 arg (correct)

set +x    # disable trace
```

---

### Exercise 5 — Real-World: Safe vs Unsafe File Handling

**Goal:** See word splitting break a file operation, then fix it.

```bash
# Create a file with a space in its name
echo "hello world" > "/tmp/my report.txt"

# UNSAFE — word splitting breaks the filename
wc -l /tmp/my report.txt        # error: 'my' not found, 'report.txt' not found

# SAFE — quoted variable
filename="/tmp/my report.txt"
wc -l "$filename"               # works! ✅

# UNSAFE — command substitution result with spaces
file_list=$(ls /tmp/*.txt 2>/dev/null)
for f in $file_list; do         # word splitting may break filenames with spaces
    echo "File: $f"
done

# SAFE — use glob directly (filename expansion handles spaces correctly)
for f in /tmp/*.txt; do
    echo "File: $f"
done

# Clean up
rm "/tmp/my report.txt"
```

---

### Exercise 6 — Custom IFS for Parsing

**Goal:** Use custom `$IFS` to parse structured data.

```bash
# Parse /etc/passwd fields (colon-separated)
old_ifs="$IFS"
IFS=:

while read -r user pass uid gid info home shell; do
    echo "User: $user | Shell: $shell"
done < /etc/passwd | head -5

IFS="$old_ifs"   # restore

echo ""

# Parse a CSV line manually
csv_line="Alice,30,Chennai,Developer"
IFS=,
read -r name age city role <<< "$csv_line"
echo "Name: $name | Age: $age | City: $city | Role: $role"
IFS="$old_ifs"
```

---

### 🧠 Quiz — Test Yourself

1. What is word splitting and when does it occur?
2. Does word splitting apply to literal text you type, or only to expansion results?
3. What variable controls the word splitting delimiter?
4. What are the **three default** characters in `$IFS`?
5. If `dirs="d1 d2 d3"`, what does `mkdir $dirs` create?
6. If `dirs="d1 d2 d3"`, what does `mkdir "$dirs"` create?
7. How do you **prevent** word splitting on a variable expansion?
8. What does changing `IFS=:` do to word splitting behaviour?
9. How do you **restore** `$IFS` after changing it?
10. How do you use `set -x` to debug word splitting?
11. When is word splitting actually **useful** (intentional)?
12. What is the safer alternative to relying on word splitting for lists?

---

## 📎 Quick Reference Card

```
WORD SPLITTING — SUMMARY
──────────────────────────────────────────────────────────────
Applies to:   Results of UNQUOTED expansions ($var, $(cmd), $((expr)))
Does NOT apply: Literal text, anything inside double quotes
Trigger:      $IFS characters in the expansion result
Default $IFS: space  tab  newline  (whitespace)

THE GOLDEN RULE
──────────────────────────────────────────────────────────────
command $variable     ← RISKY — word splitting may occur
command "$variable"   ← SAFE  — double quotes prevent splitting

VIEW $IFS
──────────────────────────────────────────────────────────────
echo "${IFS@Q}"          → $' \t\n'  (space, tab, newline)
cat -A <<< "$IFS"        → shows invisible chars visually

CHANGE $IFS
──────────────────────────────────────────────────────────────
IFS=:                    Change delimiter to colon
IFS=$'\n'                Change delimiter to newline only
old_ifs="$IFS"           Save before changing
IFS="$old_ifs"           Restore after use
unset IFS                Restore to default (space, tab, newline)

EXAMPLES
──────────────────────────────────────────────────────────────
dirs="d1 d2 d3"

mkdir $dirs              → 3 dirs: d1/ d2/ d3/    (split on space)
mkdir "$dirs"            → 1 dir: "d1 d2 d3"/     (no split)

IFS=:
dirs_colon="d1:d2:d3"
mkdir $dirs_colon        → 3 dirs: d1/ d2/ d3/    (split on colon)

DEBUG WITH TRACE
──────────────────────────────────────────────────────────────
set -x          Enable trace — shows command after all expansions
set +x          Disable trace

PARSING WITH CUSTOM IFS
──────────────────────────────────────────────────────────────
IFS=: read -r user pass uid gid info home shell <<< "$passwd_line"
IFS=, read -r name age city <<< "$csv_line"
```

---

## 🗂️ Notion Tags (suggested)

`bash` `word-splitting` `IFS` `quoting` `shell-expansions` `debugging` `ZTM` `module-2` `section-03`

---

*← Previous: Module 2 | Section 03 — Process Substitution*
*→ Next Episode: Module 2 | Section 03 — Filename Expansion (Globbing)*
