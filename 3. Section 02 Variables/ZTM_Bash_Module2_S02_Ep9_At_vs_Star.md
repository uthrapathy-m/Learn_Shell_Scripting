# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 02 — Variables
> **Episode:** 9. `$@` vs `$*`
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

This episode is dedicated entirely to clearing up **one of the classic gotchas of Bash** — the difference between `$@` and `$*`. They look identical at first, and unquoted they behave identically. The critical difference only emerges when they are wrapped in **double quotes**. Understanding this distinction is essential for writing reliable scripts that handle arguments with spaces correctly.

---

## 🧠 Theory

### 1. The Surface-Level Similarity

Both `$@` and `$*` expand to represent **all positional parameters** (`$1`, `$2`, `$3`, ...):

```bash
./script.sh one two three

# Both seem to do the same thing:
echo $@     # → one two three
echo $*     # → one two three
```

When **unquoted**, they are indeed identical — both subject to word splitting.

> ⚠️ **The difference only appears when quoted** — `"$@"` vs `"$*"`.

---

### 2. Word Splitting — A Quick Recap

**Word splitting** is the process where Bash breaks a string into separate words based on the characters in `$IFS` (Internal Field Separator — default: space, tab, newline).

```bash
arg="hello world"

# WITHOUT quotes → word splitting → 2 separate words:
touch $arg          # creates files: 'hello' and 'world' (2 files!)

# WITH quotes → no word splitting → treated as 1 word:
touch "$arg"        # creates 1 file: 'hello world'
```

This same principle is the heart of the `$@` vs `$*` difference.

---

### 3. The Four Behaviours — Side by Side

```bash
./script.sh "my file.txt" "your report.txt" "his doc.txt"
# Three arguments — each contains a space
```

| Form | Equivalent to | Word splitting? | Files created |
|---|---|---|---|
| `$@` (unquoted) | `$1 $2 $3` (unquoted) | ✅ YES — splits on spaces | 6 (wrong!) |
| `$*` (unquoted) | `$1 $2 $3` (unquoted) | ✅ YES — splits on spaces | 6 (wrong!) |
| `"$@"` (quoted) | `"$1" "$2" "$3"` (each separately quoted) | ❌ NO — preserved | 3 ✅ (correct!) |
| `"$*"` (quoted) | `"$1 $2 $3"` (all joined as one string) | ❌ NO — but joined | 1 (usually wrong!) |

---

### 4. `"$@"` — Each Argument as Its Own Quoted Word

When you write `"$@"`, Bash expands it as if you had written each argument individually in double quotes:

```bash
"$@"  →  "$1" "$2" "$3" ...
```

This means:
- Each argument is **protected from word splitting**
- An argument like `"hello world"` remains ONE word, not two
- The argument count is **perfectly preserved**

```bash
# With 3 arguments, each containing a space:
./script.sh "my file.txt" "your report.txt" "his doc.txt"

touch "$@"
# Equivalent to: touch "my file.txt" "your report.txt" "his doc.txt"
# → Creates exactly 3 files ✅
```

---

### 5. `"$*"` — All Arguments Joined as One String

When you write `"$*"`, Bash joins ALL arguments into a **single string**, with the first character of `$IFS` (a space by default) as the separator:

```bash
"$*"  →  "$1 $2 $3 ..."   (one single quoted string)
```

```bash
# With 3 arguments, each containing a space:
./script.sh "my file.txt" "your report.txt" "his doc.txt"

touch "$*"
# Equivalent to: touch "my file.txt your report.txt his doc.txt"
# → Creates exactly 1 file with a very long name ❌
```

> 💡 **Custom IFS example:** If you set `IFS=:`, then `"$*"` expands with `:` between each argument:
> ```bash
> IFS=:
> echo "$*"    # → my file.txt:your report.txt:his doc.txt
> ```

---

### 6. The Definitive Comparison

```bash
#!/bin/bash
# Run as: ./demo.sh "hello world" foo bar

echo "--- Unquoted \$@ (word splitting ON) ---"
for arg in $@; do echo "  [$arg]"; done
# → [hello], [world], [foo], [bar]  (4 items — "hello world" split!)

echo "--- Quoted \"\$@\" (word splitting OFF) ---"
for arg in "$@"; do echo "  [$arg]"; done
# → [hello world], [foo], [bar]  (3 items — CORRECT ✅)

echo "--- Unquoted \$* (word splitting ON) ---"
for arg in $*; do echo "  [$arg]"; done
# → [hello], [world], [foo], [bar]  (4 items — same as unquoted $@)

echo "--- Quoted \"\$*\" (word splitting OFF, but joined) ---"
for arg in "$*"; do echo "  [$arg]"; done
# → [hello world foo bar]  (1 item — all joined ❌)
```

---

### 7. The Practical Demo — `touch` With Files Containing Spaces

The instructor demonstrates this concretely with `touch`:

**Script using unquoted `$@` (BROKEN):**
```bash
#!/bin/bash
touch $@    # ← unquoted — word splitting destroys arguments with spaces
```

```bash
./script.sh "myfile.txt" "yourreport.txt" "hisdocument.txt"
# Creates 6 files instead of 3:
# → myfile.txt  yourreport.txt  hisdocument.txt  (plus extra split words)
```

**Script using quoted `"$@"` (CORRECT):**
```bash
#!/bin/bash
touch "$@"    # ← quoted — each argument preserved perfectly
```

```bash
./script.sh "myfile.txt" "yourreport.txt" "hisdocument.txt"
# Creates exactly 3 files ✅
```

---

### 8. The Golden Rule

> ✅ **In 99% of cases, use `"$@"` (quoted).** It is the safe, correct, and idiomatic way to handle all script arguments.

| Situation | What to use |
|---|---|
| Loop over all arguments | `for arg in "$@"` |
| Pass all args to another command | `command "$@"` |
| Count arguments | `$#` |
| Join all arguments as a single string with a custom delimiter | `"$*"` with custom `$IFS` |
| Display all args (just for info, no further use) | `echo $@` or `echo $*` (both fine) |

---

## 🔑 Key Takeaways

- **Unquoted**, `$@` and `$*` behave **identically** — both perform word splitting.
- **Quoted**, they behave **completely differently**:
  - `"$@"` → each argument as its **own separate quoted word** — preserves spaces ✅
  - `"$*"` → all arguments joined into **one single string** — loses argument boundaries ❌
- Word splitting causes arguments with spaces to be **broken into multiple words** when unquoted.
- `"$@"` is the **safe, idiomatic choice** in virtually all real-world scripts.
- Use `"$*"` only when you deliberately want all args as **one string** (e.g. CSV generation with custom `$IFS`).
- The first character of `$IFS` is what `"$*"` uses to join arguments (default: space).
- **Always test scripts with arguments that contain spaces** — this is how quoting bugs reveal themselves.

---

## 💡 Tips & Tricks

**🔹 The definitive mental model:**
```
"$@"  →  "$1" "$2" "$3"   ← each arg in its own quotes (SAFE)
"$*"  →  "$1 $2 $3"       ← all args in one set of quotes (JOINS)
```

**🔹 Always use `"$@"` in loops and when passing args forward:**
```bash
# Passing all script arguments to another command:
rsync "$@" /backup/

# Looping over all arguments:
for file in "$@"; do
    echo "Processing: $file"
done
```

**🔹 The space test — always test with space-containing args:**
```bash
# If your script handles filenames, always test with:
./script.sh "file with spaces.txt" "another file.txt"
```

**🔹 Rare legitimate use of `"$*"` — building a CSV line:**
```bash
#!/bin/bash
IFS=","
csv_line="$*"
echo "CSV: $csv_line"
# ./script.sh apple banana cherry
# → CSV: apple,banana,cherry
```

**🔹 Debugging: print what args your script received:**
```bash
echo "Received $# argument(s):"
for i in "$@"; do
    echo "  [$i]"
done
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — See the Difference Interactively

**Goal:** Run a script with space-containing arguments and observe `$@` vs `$*` behaviour.

Create `~/scripts/at_vs_star_full.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : at_vs_star_full.sh
# Purpose : Show the difference between $@ and $* in all 4 forms
# Usage   : ./at_vs_star_full.sh "arg one" "arg two" "arg three"
# ─────────────────────────────────────────────────────

echo "Arguments received: $#"
echo ""

echo "=== 1. Unquoted \$@ (word splitting ON) ==="
count=0
for arg in $@; do
    ((count++))
    echo "  item $count: [$arg]"
done
echo "  Total items: $count"
echo ""

echo "=== 2. Quoted \"\$@\" — CORRECT (each arg preserved) ==="
count=0
for arg in "$@"; do
    ((count++))
    echo "  item $count: [$arg]"
done
echo "  Total items: $count"
echo ""

echo "=== 3. Unquoted \$* (word splitting ON) ==="
count=0
for arg in $*; do
    ((count++))
    echo "  item $count: [$arg]"
done
echo "  Total items: $count"
echo ""

echo "=== 4. Quoted \"\$*\" — JOINS all into one ==="
count=0
for arg in "$*"; do
    ((count++))
    echo "  item $count: [$arg]"
done
echo "  Total items: $count"
```

```bash
chmod +x ~/scripts/at_vs_star_full.sh

# Run with space-containing arguments
./at_vs_star_full.sh "hello world" "foo bar" "baz"
```

**Expected output analysis:**
- Section 1 & 3 (unquoted): should show MORE items than you passed (word split)
- Section 2 (quoted `"$@"`): should show EXACTLY 3 items — each preserved ✅
- Section 4 (quoted `"$*"`): should show 1 item — all joined together

---

### Exercise 2 — The `touch` File Demo

**Goal:** Reproduce the instructor's `touch` demo to see word splitting destroy filenames.

Create `~/scripts/touch_demo.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : touch_demo.sh
# Purpose : Demonstrate word splitting with touch and $@
# Usage   : ./touch_demo.sh <filenames...>
# ─────────────────────────────────────────────────────

TEST_DIR="/tmp/touch_test_$$"
mkdir -p "$TEST_DIR"

echo "Creating files in: $TEST_DIR"
echo ""

# Version 1: UNQUOTED $@ — BROKEN
echo "--- Version 1: touch \$@ (unquoted — BROKEN) ---"
touch $@    # word splitting will break filenames with spaces
ls "$TEST_DIR" 2>/dev/null
echo "Files created by unquoted \$@:"
ls /tmp/touch_test_$$ 2>/dev/null || true
echo ""

# Clean and redo with Version 2
rm -rf "$TEST_DIR"
mkdir -p "$TEST_DIR"
cd "$TEST_DIR" || exit

# Version 2: QUOTED "$@" — CORRECT
echo "--- Version 2: touch \"\$@\" (quoted — CORRECT) ---"
touch "$@"
echo "Files created by quoted \"\$@\":"
ls -1
echo "Count: $(ls | wc -l) files"

cd - > /dev/null
rm -rf "$TEST_DIR"
echo ""
echo "Cleaned up temp dir."
```

```bash
chmod +x ~/scripts/touch_demo.sh

# Run with space-containing filenames
./touch_demo.sh "myfile.txt" "your report.txt" "his document.txt"
```

**Observe:**
- Unquoted `$@` creates more files than expected
- Quoted `"$@"` creates exactly the right number

---

### Exercise 3 — The Rare Legitimate Use of `"$*"`

**Goal:** See when `"$*"` is actually useful — joining arguments with a custom separator.

```bash
#!/bin/bash
# Save as: ~/scripts/join_args.sh

if [[ "$#" -lt 2 ]]; then
    echo "Usage: $0 <separator> <arg1> [arg2] ..."
    echo "Example: $0 ',' apple banana cherry"
    exit 1
fi

separator="$1"
shift       # remove $1, shift remaining args down ($2 becomes $1, etc.)

# Set IFS to the separator, then use "$*" to join
IFS="$separator"
echo "$*"
```

```bash
chmod +x ~/scripts/join_args.sh

# Join with comma
./join_args.sh "," apple banana cherry
# → apple,banana,cherry

# Join with pipe
./join_args.sh "|" one two three four
# → one|two|three|four

# Join with colon (like a PATH)
./join_args.sh ":" /usr/bin /usr/local/bin /home/user/scripts
# → /usr/bin:/usr/local/bin:/home/user/scripts
```

---

### Exercise 4 — Safely Forward All Arguments

**Goal:** Write a wrapper script that forwards all its arguments to another command using `"$@"`.

Create `~/scripts/safe_ls.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : safe_ls.sh
# Purpose : A wrapper around ls that safely forwards all args
# Usage   : ./safe_ls.sh [ls options] [paths...]
# ─────────────────────────────────────────────────────

echo "Running: ls $*"
echo "---"
ls "$@"     # "$@" safely passes all arguments to ls — spaces preserved
```

```bash
chmod +x ~/scripts/safe_ls.sh

./safe_ls.sh -la /home
./safe_ls.sh -lh /tmp
./safe_ls.sh -la "/home" "/tmp"    # multiple paths
```

---

### Exercise 5 — Build the Definitive Cheat Sheet Script

**Goal:** Create a script you can run anytime to remind yourself of the `$@` vs `$*` rules.

Create `~/scripts/at_star_cheatsheet.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : at_star_cheatsheet.sh
# Purpose : Personal cheat sheet for $@ vs $*
# ─────────────────────────────────────────────────────

cat << 'EOF'
╔══════════════════════════════════════════════════════════╗
║              $@ vs $* — The Definitive Guide             ║
╠══════════════════════════════════════════════════════════╣
║                                                          ║
║  UNQUOTED (both identical — word splitting ON)           ║
║  ─────────────────────────────────────────────           ║
║  $@  →  $1 $2 $3 ...   (word-split — AVOID)             ║
║  $*  →  $1 $2 $3 ...   (word-split — AVOID)             ║
║                                                          ║
║  QUOTED (behaviour diverges!)                            ║
║  ─────────────────────────────────────────────           ║
║  "$@"  →  "$1" "$2" "$3"  (separate args)  ✅ USE THIS  ║
║  "$*"  →  "$1 $2 $3"      (one string)     ⚠️  AVOID    ║
║                                                          ║
║  RULES                                                   ║
║  ─────────────────────────────────────────────           ║
║  ✅ Always use "$@" for loops and forwarding args        ║
║  ✅ Always use "$@" when passing to another command      ║
║  ⚠️  "$*" only for deliberately joining into 1 string   ║
║                                                          ║
║  EXAMPLES                                                ║
║  ─────────────────────────────────────────────           ║
║  for arg in "$@"; do echo "$arg"; done    ← CORRECT     ║
║  command "$@"                             ← CORRECT     ║
║  IFS=","; echo "$*"  ← join with comma   ← OK (rare)   ║
╚══════════════════════════════════════════════════════════╝
EOF

echo ""
echo "Quick live demo with args: \"hello world\" foo bar"
echo "─────────────────────────────────────────────────"

set -- "hello world" foo bar    # set positional params for demo

echo ""
echo 'for arg in "$@"  →'
for arg in "$@"; do printf '  [%s]\n' "$arg"; done

echo ""
echo 'for arg in "$*"  →'
for arg in "$*"; do printf '  [%s]\n' "$arg"; done
```

```bash
chmod +x ~/scripts/at_star_cheatsheet.sh
./at_star_cheatsheet.sh
```

---

### 🧠 Quiz — Test Yourself

1. When are `$@` and `$*` identical in behaviour?
2. When do they start to differ?
3. What does `"$@"` expand to when the script has 3 arguments?
4. What does `"$*"` expand to when the script has 3 arguments?
5. A script is called with `./script.sh "hello world" foo`. With **unquoted** `$@`, how many words does `for arg in $@` iterate over?
6. With **quoted** `"$@"`, how many arguments does `for arg in "$@"` iterate over in the same case?
7. What character does `"$*"` use to join arguments by default?
8. Which variable controls the joining character used by `"$*"`?
9. In what rare scenario is `"$*"` actually the right choice?
10. Write the correct form to safely pass all script arguments to the `tar` command.

---

## 📎 Quick Reference Card

```
$@ vs $* — DEFINITIVE REFERENCE
──────────────────────────────────────────────────────────────
Form        Equivalent to          Word split?  Result
──────────────────────────────────────────────────────────────
$@          $1 $2 $3 ...           YES          Each word split individually
$*          $1 $2 $3 ...           YES          Each word split individually
"$@"        "$1" "$2" "$3" ...     NO           ✅ Each arg preserved separately
"$*"        "$1 $2 $3 ..."         NO           ⚠️  All args joined as ONE string

WHAT TO USE
──────────────────────────────────────────────────────────────
Loop over all arguments:           for arg in "$@"
Pass all args to a command:        command "$@"
Count arguments:                   $#
Join all args as one string:       IFS=","; echo "$*"  (rare)

MENTAL MODEL
──────────────────────────────────────────────────────────────
"$@"  =  "$1" "$2" "$3"   → each in own quotes  (SEPARATE) ✅
"$*"  =  "$1 $2 $3"       → all in one quote    (JOINED)   ⚠️

EXAMPLES
──────────────────────────────────────────────────────────────
# Correct loop:
for file in "$@"; do
    echo "Processing: $file"
done

# Correct pass-through:
tar -czvf archive.tar.gz "$@"
rsync -av "$@" /backup/
grep -r "pattern" "$@"

# Correct debug print:
echo "Got $# args:"
printf '  [%s]\n' "$@"

# Rare $* use — join with custom separator:
IFS=":"
path_string="$*"    # joins all args with :
unset IFS           # restore default IFS after use
```

---

## 🗂️ Notion Tags (suggested)

`bash` `special-parameters` `dollar-at` `dollar-star` `word-splitting` `quoting` `IFS` `shell-scripting` `ZTM` `module-2` `section-02`

---

## 🏁 Section 02 — Variables: Fully Complete!

> **Section 02 is done!** Here is everything you mastered:
>
> ✅ **Ep1 — Variables** — creating, reading, listing, unset, declare -r
> ✅ **Ep2 — Expansion & Quoting** — `$var`, `${var}`, single/double quotes, backslash
> ✅ **Ep3 — Environment Variables** — `export`, `env`, `printenv`, `set`, persistence
> ✅ **Ep4 — User Input** — `read`, `-p`, `-s`, `-r`, `-n`, `-t`, `$REPLY`
> ✅ **Ep6 — Positional Parameters** — `$1`–`$9`, `${10}+`, `$0`, defaults
> ✅ **Ep7 — Project** — IP blocker with `iptables` using `read` and `$1`
> ✅ **Ep8 — Special Parameters** — `$#`, `$@`, `$*`, `$?`, `$$`, `$!`
> ✅ **Ep9 — `$@` vs `$*`** — the quoted difference explained in full

*→ Next: Module 2 | Section 03 — Shell Expansions*
