# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 03 — Shell Expansions
> **Episode:** 3. Brace Expansion — Sequence Lists
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

The second form of brace expansion generates **contiguous sequences** — ordered ranges of numbers or letters — in a single compact expression. This episode covers the full syntax: basic sequences, custom increments, zero-padding, reversed sequences, and a critically important limitation to understand: **variables cannot be used inside brace sequence expansions**.

---

## 🧠 Theory

### 1. What is a Sequence Expansion?

A sequence brace expansion generates a **contiguous, ordered range** of integers or characters:

```bash
echo {1..10}
# → 1 2 3 4 5 6 7 8 9 10

echo {A..Z}
# → A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
```

---

### 2. Full Syntax

```
{start..end}              # basic — increment of 1
{start..end..increment}   # with custom step
```

| Part | Required? | What it does |
|---|---|---|
| `start` | Yes | Starting integer or character |
| `..` | Yes | Double dot separator |
| `end` | Yes | Ending integer or character |
| `..increment` | Optional | Step size (default: 1) |

> 📌 Both `start` and `end` must be the **same type** — both integers OR both characters. Mixing types (e.g. `{1..Z}`) produces no expansion.

---

### 3. Integer Sequences

```bash
# Basic integer range
echo {1..10}
# → 1 2 3 4 5 6 7 8 9 10

echo {1..100}
# → 1 2 3 4 5 6 7 8 9 10 ... 100

# With a custom increment (step of 2)
echo {1..20..2}
# → 1 3 5 7 9 11 13 15 17 19   (odd numbers)

# Step of 5
echo {0..50..5}
# → 0 5 10 15 20 25 30 35 40 45 50

# Step of 10
echo {10..100..10}
# → 10 20 30 40 50 60 70 80 90 100

# Negative increment (reverse)
echo {20..1..2}
# → 20 18 16 14 12 10 8 6 4 2
```

---

### 4. Character (Letter) Sequences

```bash
# Letters A to L
echo {A..L}
# → A B C D E F G H I J K L

# Full uppercase alphabet
echo {A..Z}
# → A B C D E F G H I J K L M N O P Q R S T U V W X Y Z

# Lowercase alphabet
echo {a..z}
# → a b c d e f g h i j k l m n o p q r s t u v w x y z

# Subset of alphabet with step
echo {a..z..2}
# → a c e g i k m o q s u w y   (every other letter)
```

> 📌 Character sequences follow ASCII order. You can use any single character, but typically letters are most useful.

---

### 5. Reversed Sequences

Sequences can be **reversed** simply by putting the larger value first:

```bash
# Reversed integer sequence
echo {10..1}
# → 10 9 8 7 6 5 4 3 2 1

# Reversed with step
echo {100..0..10}
# → 100 90 80 70 60 50 40 30 20 10 0

# Reversed alphabet
echo {Z..A}
# → Z Y X W V U T S R Q P O N M L K J I H G F E D C B A

echo {z..a}
# → z y x w v u t s r q p o n m l k j i h g f e d c b a
```

---

### 6. Zero-Padding — Fixed Width Numbers

When either integer in the range is **prefixed with one or more zeros**, Bash zero-pads all generated numbers to the **same width**:

```bash
# Without zero-padding
echo {1..10}
# → 1 2 3 4 5 6 7 8 9 10   (different widths)

# WITH zero-padding (start with 01)
echo {01..10}
# → 01 02 03 04 05 06 07 08 09 10   (uniform 2-digit width)

echo {001..010}
# → 001 002 003 004 005 006 007 008 009 010   (uniform 3-digit width)
```

> 💡 **When to use zero-padding:** Any time you're creating numbered files or directories that will be sorted lexicographically (alphabetically). Without padding, `file_10` sorts before `file_2` in alphabetical order — zero-padding fixes this.

```bash
# Without padding — wrong sort order
touch file_{1..10}.log
ls file_*.log
# → file_1.log  file_10.log  file_2.log  ...  (file_10 before file_2!)

# With padding — correct sort order
touch file_{01..10}.log
ls file_*.log
# → file_01.log  file_02.log  ...  file_10.log  ✅
```

---

### 7. Combining Prefix/Suffix with Sequences

Just like string lists, sequences support optional **prefix and suffix**:

```bash
# Create backup filenames for days of the month
echo backup_{01..31}.tar.gz
# → backup_01.tar.gz backup_02.tar.gz ... backup_31.tar.gz

# Log file rotation
echo app.log.{1..7}
# → app.log.1 app.log.2 ... app.log.7

# Server naming convention
echo server{01..05}.prod.example.com
# → server01.prod.example.com ... server05.prod.example.com
```

---

### 8. ⚠️ Critical Limitation — Variables Don't Work Inside Brace Sequences

This is one of the most important rules and a common gotcha:

> **Brace expansion runs BEFORE variable expansion.** Therefore, variables cannot be used as sequence endpoints.

```bash
a=1
b=10

# ATTEMPT to use variables in brace sequence — FAILS
echo {$a..$b}
# → {1..10}   ← NOT expanded! Treated as literal text.
```

**Why?** The pipeline is:
```
{$a..$b}
    ↓  (Step 1: Brace expansion — $a and $b are still text, not values yet)
{$a..$b}   ← no valid brace expression found — left unchanged
    ↓  (Step 3: Variable expansion — $a → 1, $b → 10)
{1..10}    ← the braces are now just literal characters — NOT expanded again
```

**Workaround — use `eval` (with caution) or `seq`:**
```bash
a=1
b=10

# Using seq (the clean solution)
seq $a $b
# → 1 2 3 4 5 6 7 8 9 10

# Using seq in a loop
for i in $(seq $a $b); do
    echo "Processing: $i"
done

# Using eval (works but use with care — security risk with untrusted input)
eval echo {$a..$b}
# → 1 2 3 4 5 6 7 8 9 10
```

> ✅ When you need variable-based ranges, use **`seq`** instead of brace expansion.

---

### 9. Mixing String Lists and Sequences

Brace expansion types can be combined in a single command:

```bash
# Mix string list and sequence
echo {backup,archive}-{01..05}
# → backup-01 backup-02 backup-03 backup-04 backup-05
#   archive-01 archive-02 archive-03 archive-04 archive-05

# Create weekly log directories
mkdir -p logs/week_{01..52}

# Create monthly report files
touch report_{jan,feb,mar,apr,may,jun,jul,aug,sep,oct,nov,dec}_2025.csv
```

---

### 10. Practical Real-World Examples

```bash
# Create numbered test files
touch test_{001..020}.sh         # 20 zero-padded test files

# Create a week's worth of log dirs
mkdir -p /var/log/app/day_{01..07}

# Loop through numbers (preview of loops!)
for i in {1..5}; do
    echo "Step $i of 5"
done

# Countdown timer display
echo "Launching in: {10..1} ... GO!"
for i in {10..1}; do
    echo "T-minus $i"
    sleep 1
done
echo "GO!"

# Batch rename simulation
echo mv file_{1..5}.txt archive_{01..05}.txt
```

---

## 🔑 Key Takeaways

- Sequence brace expansion generates **contiguous ranges** of integers or characters.
- Syntax: `{start..end}` or `{start..end..increment}`.
- Both `start` and `end` must be the **same type** — both integers OR both characters.
- Default increment is `1` — specify `..N` for a custom step.
- Sequences can be **reversed** by putting the larger value first.
- **Zero-padding**: prefix start with `0` (e.g. `{01..10}`) to force fixed-width output.
- Zero-padding is essential for files/dirs that will be sorted alphabetically.
- Prefix and suffix work the same as with string lists.
- **Variables CANNOT be used inside brace sequences** — brace expansion runs before variable expansion.
- Use **`seq`** as the workaround when you need variable-based ranges.

---

## 💡 Tips & Tricks

**🔹 Always zero-pad numbered files — it saves headaches:**
```bash
# Wrong — sorts as: file_1, file_10, file_2, ...
touch file_{1..10}.log

# Right — sorts as: file_01, file_02, ..., file_10
touch file_{01..10}.log
```

**🔹 Use sequences in `for` loops (a preview of the loops section):**
```bash
for i in {1..10}; do
    echo "Iteration: $i"
done
```

**🔹 `seq` is the variable-friendly alternative:**
```bash
start=5
end=15
step=2
for i in $(seq $start $step $end); do
    echo $i
done
```

**🔹 Generate a quick alphabet:**
```bash
echo {a..z}     # all lowercase letters — useful for testing
echo {A..Z}     # all uppercase letters
```

**🔹 Combine reversed countdown with sleep for a real countdown:**
```bash
for i in {10..1}; do
    printf "\rLaunching in: %2d " $i
    sleep 1
done
echo -e "\rGO!              "
```

**🔹 Check what a brace expansion produces before running a destructive command:**
```bash
echo rm file_{01..10}.log     # preview
rm file_{01..10}.log           # run for real
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Basic Sequences

**Goal:** Get comfortable with integer and character sequences.

```bash
# Integer sequences
echo {1..10}
echo {1..100}
echo {5..50..5}       # multiples of 5
echo {0..20..2}       # even numbers 0-20
echo {1..20..2}       # odd numbers 1-19

# Character sequences
echo {A..Z}
echo {a..z}
echo {a..m}           # just a-m
echo {a..z..3}        # every 3rd letter
```

---

### Exercise 2 — Reversed Sequences

**Goal:** Practice generating sequences in reverse.

```bash
# Reverse integer
echo {10..1}
echo {100..0..10}     # reversed, step 10

# Reverse alphabet
echo {Z..A}
echo {z..a}

# Countdown simulation
for i in {5..1}; do
    echo "Countdown: $i"
done
echo "Done!"
```

---

### Exercise 3 — Zero-Padding in Action

**Goal:** See why zero-padding matters for sort order.

```bash
# Step 1: Create WITHOUT padding
mkdir /tmp/sort_test_$$
cd /tmp/sort_test_$$
touch file_{1..12}.log
echo "Without padding (notice sort order):"
ls file_*.log
echo ""

# Step 2: Clean up and redo WITH padding
rm file_*.log
touch file_{01..12}.log
echo "With padding (correct sort order):"
ls file_*.log

# Step 3: Clean up
cd - && rm -rf /tmp/sort_test_$$
```

**Observe:** Without padding, `file_10` appears before `file_2` in the listing. With padding, the order is correct.

---

### Exercise 4 — The Variable Limitation

**Goal:** See the variable-in-brace-sequence bug and learn the fix.

```bash
# Set variables
start=1
end=10

# ATTEMPT (BROKEN)
echo {$start..$end}
# What do you see? Is it expanded?

# FIX 1: Use seq
seq $start $end

# FIX 2: Use seq in a loop
for i in $(seq $start $end); do
    echo "Item: $i"
done

# FIX 3: eval (advanced — use with caution)
eval echo {$start..$end}
```

---

### Exercise 5 — Practical Sequence Applications

**Goal:** Apply sequences to real file management tasks.

```bash
# Create 7 daily backup directories
mkdir -p ~/bash-practice/backups/day_{01..07}
ls ~/bash-practice/backups/

# Create monthly log files for 2025
touch ~/bash-practice/{jan,feb,mar,apr,may,jun,jul,aug,sep,oct,nov,dec}_2025.log
ls ~/bash-practice/*_2025.log

# Create a numbered test suite
touch ~/bash-practice/test_{001..010}.sh
ls ~/bash-practice/test_*.sh

# Simulate server names
echo "Servers to provision:"
echo server{01..05}.{prod,dev}.internal

# Clean up
rm -rf ~/bash-practice/backups
rm ~/bash-practice/*_2025.log
rm ~/bash-practice/test_*.sh
```

---

### Exercise 6 — Combine String Lists and Sequences

**Goal:** Mix both types of brace expansion in one command.

```bash
# Create structured log archive
echo archive_{jan,feb,mar}_{2023..2025}.tar.gz
# How many filenames does this produce?

# Create environment-numbered configs
echo config_{dev,staging,prod}_{01..03}.conf

# Preview creating a full directory structure
echo mkdir -p project/server{01..03}/{logs,config,data}

# Actually create it
mkdir -p ~/bash-practice/project/server{01..03}/{logs,config,data}
find ~/bash-practice/project -type d | sort

# Clean up
rm -rf ~/bash-practice/project
```

---

### Exercise 7 — Sequence Loop Script

**Goal:** Write a script that uses sequences for practical numbered processing.

Create `~/scripts/process_numbered.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : process_numbered.sh
# Purpose : Demonstrate sequences in loops and file ops
# Usage   : ./process_numbered.sh
# ─────────────────────────────────────────────────────

declare -r WORKDIR="/tmp/seq_demo_$$"
mkdir -p "$WORKDIR"

echo "============================================"
echo "  Brace Expansion — Sequence Demo"
echo "============================================"

# Create zero-padded log files
echo ""
echo "1. Creating zero-padded log files..."
touch "$WORKDIR"/app_{01..10}.log
echo "   Created: $(ls "$WORKDIR"/app_*.log | wc -l) files"
ls "$WORKDIR"/app_*.log | xargs -I{} basename {}

# Loop using sequence
echo ""
echo "2. Processing files in sequence..."
for i in {01..10}; do
    echo "   Step $i: $(date +%T)" >> "$WORKDIR"/app_${i}.log
done
echo "   Done — timestamps written to each file"

# Countdown
echo ""
echo "3. Countdown:"
for i in {5..1}; do
    echo -n "   $i..."
    sleep 0.3
done
echo " GO!"

# Even numbers
echo ""
echo "4. Even numbers 2-20:"
echo "   $(echo {2..20..2})"

# Odd numbers
echo ""
echo "5. Odd numbers 1-19:"
echo "   $(echo {1..19..2})"

# Clean up
rm -rf "$WORKDIR"
echo ""
echo "============================================"
echo "  Demo complete."
```

```bash
chmod +x ~/scripts/process_numbered.sh
./process_numbered.sh
```

---

### 🧠 Quiz — Test Yourself

1. What is the syntax for a basic integer sequence from 1 to 20?
2. What is the syntax for a sequence with a custom step of 3?
3. What does `echo {01..10}` produce and how is it different from `echo {1..10}`?
4. How do you generate a **reversed** sequence from 10 to 1?
5. What character sequences does `echo {A..Z}` produce?
6. Can you mix integers and characters in a sequence like `{1..Z}`? What happens?
7. What happens when you use a variable inside a brace sequence: `echo {$a..10}`?
8. Why does using a variable inside a brace sequence fail?
9. What is the clean workaround when you need a variable-based range?
10. What does `echo {a..z..2}` produce?
11. What does `echo server{01..03}.internal` produce?
12. Why should you always use zero-padding for numbered files that will be sorted?

---

## 📎 Quick Reference Card

```
BRACE EXPANSION — SEQUENCES
──────────────────────────────────────────────────────────────
SYNTAX:  {start..end}  or  {start..end..increment}

INTEGER SEQUENCES
──────────────────────────────────────────────────────────────
{1..10}         → 1 2 3 4 5 6 7 8 9 10
{1..100}        → 1 through 100
{1..10..2}      → 1 3 5 7 9          (odd numbers, step 2)
{0..20..5}      → 0 5 10 15 20       (step 5)
{10..1}         → 10 9 8 7 6 5 4 3 2 1  (reversed)
{100..0..10}    → 100 90 80 ... 0    (reversed, step 10)

ZERO-PADDED (uniform width)
──────────────────────────────────────────────────────────────
{01..10}        → 01 02 03 04 05 06 07 08 09 10
{001..010}      → 001 002 003 004 005 006 007 008 009 010
{01..10..2}     → 01 03 05 07 09

CHARACTER SEQUENCES
──────────────────────────────────────────────────────────────
{A..Z}          → A B C ... Z
{a..z}          → a b c ... z
{a..m}          → a b c d e f g h i j k l m
{A..Z..2}       → A C E G I K M O Q S U W Y   (every other)
{Z..A}          → Z Y X ... A   (reversed)

WITH PREFIX / SUFFIX
──────────────────────────────────────────────────────────────
file_{01..10}.log      → file_01.log ... file_10.log
server{01..05}.prod    → server01.prod ... server05.prod
backup_{1..31}         → backup_1 ... backup_31

RULES & LIMITS
──────────────────────────────────────────────────────────────
✅  Both endpoints must be same type (int or char — not mixed)
✅  Reversed by putting larger value first
✅  Zero-pad by prefixing start with 0: {01..10}
❌  Variables DON'T work: {$a..$b} → literal (not expanded)

VARIABLE-BASED RANGES — USE SEQ INSTEAD
──────────────────────────────────────────────────────────────
seq $start $end              Simple range
seq $start $step $end        Range with step
for i in $(seq $a $b)        Loop with variable range
eval echo {$a..$b}           eval workaround (use with caution)
```

---

## 🗂️ Notion Tags (suggested)

`bash` `brace-expansion` `sequences` `ranges` `zero-padding` `seq` `shell-expansions` `ZTM` `module-2` `section-03`

---

*← Previous: Module 2 | Section 03 — Brace Expansion: String Lists*
*→ Next Episode: Module 2 | Section 03 — Brace Expansion Project*
