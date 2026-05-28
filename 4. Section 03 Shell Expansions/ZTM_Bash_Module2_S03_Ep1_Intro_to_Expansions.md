# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 03 — Shell Expansions
> **Episode:** 1. Intro to Expansions
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

This short but important episode sets the stage for the entire Shell Expansions section. Before you can fully understand what Bash does with the commands you type, you need to understand the pipeline that happens **between pressing Enter and the command actually executing**. Expansions are a core part of that pipeline — and this episode maps out all eight of them in order.

---

## 🧠 Theory

### 1. What Happens When You Press Enter?

Every time you type a command and hit Enter, Bash goes through a multi-stage processing pipeline **before** actually executing anything:

```
You type a command and press Enter
            ↓
    1. TOKENIZATION
       Bash splits the input into tokens
            ↓
    2. PARSING
       Tokens are identified as simple or compound commands
            ↓
    3. EXPANSIONS (8 types, in strict order)
       Referenced entities are replaced with their values
            ↓
    4. QUOTE REMOVAL
       Quoting characters that were used for grouping are stripped
            ↓
    5. EXECUTION
       The final, fully-expanded command is executed
```

> 💡 **Shell expansion** is the procedure of **replacing a referenced entity with its actual value** — for example, replacing `$PATH` with the list of directories it contains.

---

### 2. The 8 Types of Shell Expansion — In Order

Bash performs expansions in a **strict, fixed order**. Understanding this order explains many behaviours that otherwise seem mysterious.

| # | Expansion Type | Trigger | Example |
|---|---|---|---|
| 1 | **Brace Expansion** | `{...}` | `{a,b,c}`, `{1..5}` |
| 2 | **Tilde Expansion** | `~` | `~` → `/home/user` |
| 3 | **Parameter & Variable Expansion** | `$` | `$PATH`, `${var}` |
| 4 | **Command Substitution** | `$(...)` or `` `...` `` | `$(date)`, `` `whoami` `` |
| 5 | **Arithmetic Expansion** | `$((...))` | `$((3 + 5))` → `8` |
| 6 | **Process Substitution** | `<(...)` or `>(...)` | `diff <(cmd1) <(cmd2)` |
| 7 | **Word Splitting** | (automatic) | Splits on `$IFS` characters |
| 8 | **Filename Expansion (Globbing)** | `*`, `?`, `[...]` | `*.txt`, `file?.sh` |

> 📌 After all 8 expansions, **quote removal** is performed — the quoting characters (`"`, `'`, `\`) used for grouping are stripped from the final command.

---

### 3. Why Order Matters

Because expansions happen in a fixed order, the output of one expansion can become the input for a later one.

```bash
dir=~/documents
ls $dir/*.txt
```

Processing steps:
1. `$dir` → parameter expansion → `/home/user/documents`
2. `/home/user/documents/*.txt` → glob expansion → all matching `.txt` files

If the order were reversed, glob would run on `$dir/*.txt` literally — finding nothing.

---

### 4. What You've Already Seen

The course has already used several expansions without formally naming them:

| What you wrote | Expansion type |
|---|---|
| `echo $PATH` | Parameter & Variable expansion |
| `echo $USER` | Parameter & Variable expansion |
| `echo ${os}11` | Parameter & Variable expansion |
| `$(whoami)` | Command substitution |
| `$((3 + 2))` | Arithmetic expansion |

> ✅ You've been using expansions since Day 1 — this section names them properly and goes deep into each one.

---

### 5. Upcoming Episodes — What Each Expansion Covers

Each expansion gets its own dedicated episode in this section:

| Episode | Expansion | What you'll learn |
|---|---|---|
| Ep 2 | Brace Expansion | Generate sequences and lists: `{1..10}`, `{a,b,c}` |
| Ep 3 | Tilde Expansion | `~`, `~user`, `~+`, `~-` |
| Ep 4 | Parameter Expansion | Advanced `${var}` forms, defaults, substrings |
| Ep 5 | Command Substitution | `$(command)` — embed command output in scripts |
| Ep 6 | Arithmetic Expansion | `$((expr))` — math in Bash |
| Ep 7 | Process Substitution | `<(cmd)` — treat command output as a file |
| Ep 8 | Word Splitting | How `$IFS` controls how strings are broken up |
| Ep 9 | Filename Expansion | Globs: `*`, `?`, `[...]`, `**` |

---

## 🔑 Key Takeaways

- When you press Enter, Bash processes your command through a pipeline: **tokenization → parsing → expansions → quote removal → execution**.
- **Shell expansion** = replacing a referenced entity (variable, command, expression) with its actual value.
- There are **8 types** of expansion, performed in a **strict, fixed order**.
- The order is: Brace → Tilde → Parameter/Variable → Command Substitution → Arithmetic → Process Substitution → Word Splitting → Filename (Glob).
- After all expansions, **quote removal** is the final step before execution.
- You've already been using Parameter expansion and Command substitution — this section formally covers all 8.
- Understanding expansion order explains many "mysterious" Bash behaviours.

---

## 💡 Tips & Tricks

**🔹 Use `set -x` to see expansions as they happen:**
```bash
set -x      # enable debug/trace mode — shows each command AFTER expansion
ls $HOME/*.txt
set +x      # disable trace mode
```
This is one of the most powerful debugging tools in Bash — you can see exactly what Bash expanded your command into before running it.

**🔹 The expansion order is a mental checklist:**
When a command doesn't behave as expected, mentally walk through the 8 expansion steps in order to find where it diverges from your intention.

**🔹 Quote removal is NOT an expansion:**
It happens after all expansions are done. Quotes are used to **control** which expansions happen — then they're stripped away before execution.

**🔹 A handy mnemonic for the order:**
> **B**old **T**igers **P**rowl **C**arelessly **A**nd **P**ounce **W**ithout **F**ear
> **B**race · **T**ilde · **P**arameter · **C**ommand · **A**rithmetic · **P**rocess · **W**ord · **F**ilename

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Identify the Expansion Types

**Goal:** Look at each command and identify which expansion type(s) are involved.

```bash
# Identify the expansion type in each:

echo $USER
# Type: _______________

echo ~/documents
# Type: _______________

echo $(date)
# Type: _______________

echo $((10 * 5))
# Type: _______________

echo {1..5}
# Type: _______________

ls *.sh
# Type: _______________

echo "Hello $USER, today is $(date +%A)"
# Types: _______________ and _______________
```

**Answers:**
1. Parameter/Variable expansion
2. Tilde expansion
3. Command substitution
4. Arithmetic expansion
5. Brace expansion
6. Filename expansion (Globbing)
7. Parameter/Variable expansion + Command substitution

---

### Exercise 2 — Watch Expansions with `set -x`

**Goal:** Use Bash's trace mode to see what commands look like after expansion.

```bash
# Enable trace mode
set -x

# Try commands with various expansions
echo $HOME
echo $(whoami)
echo $((2 + 3))
ls ~/scripts/*.sh 2>/dev/null

# Disable trace mode
set +x
```

**What to observe:** The `+ command` lines show the FULLY EXPANDED version of each command — exactly what Bash passes to the kernel for execution.

---

### Exercise 3 — Map the Expansion Pipeline

**Goal:** Manually trace through Bash's expansion pipeline for a complex command.

Take this command:
```bash
echo "Files in $HOME: $(ls $HOME | wc -l)"
```

Trace the steps:
1. **Parameter expansion** — `$HOME` → `/home/yourusername`
2. **Command substitution** — `$(ls /home/yourusername | wc -l)` → count of files
3. **Quote removal** — outer double quotes are stripped
4. **Execution** — `echo "Files in /home/yourusername: 42"` runs

Now try it:
```bash
echo "Files in $HOME: $(ls $HOME | wc -l)"
```

Then trace this one yourself:
```bash
cp $HOME/scripts/{backup,restore}.sh /tmp/
```

---

### Exercise 4 — Build Your Expansion Reference Map

**Goal:** Create a personal cheat sheet script that demonstrates all 8 expansion types.

Create `~/scripts/expansion_preview.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : expansion_preview.sh
# Purpose : Preview all 8 Bash expansion types
# ─────────────────────────────────────────────────────

echo "============================================="
echo "    Bash Shell Expansions — All 8 Types"
echo "============================================="
echo ""

echo "1. Brace Expansion"
echo "   {a,b,c}   → $(echo {a,b,c})"
echo "   {1..5}    → $(echo {1..5})"
echo ""

echo "2. Tilde Expansion"
echo "   ~         → $HOME"
echo "   ~root     → ~root (root's home)"
echo ""

echo "3. Parameter/Variable Expansion"
myvar="hello"
echo "   \$myvar   → $myvar"
echo "   \$USER    → $USER"
echo ""

echo "4. Command Substitution"
echo "   \$(date +%Y)  → $(date +%Y)"
echo "   \$(whoami)    → $(whoami)"
echo ""

echo "5. Arithmetic Expansion"
echo "   \$((10 * 5))  → $((10 * 5))"
echo "   \$((2 ** 8))  → $((2 ** 8))"
echo ""

echo "6. Process Substitution"
echo "   diff <(echo 'a') <(echo 'b')  → (see diff)"
diff <(echo "line1") <(echo "line2") || true
echo ""

echo "7. Word Splitting"
IFS=:
str="one:two:three"
for word in $str; do printf "   word: [%s]\n" "$word"; done
unset IFS
echo ""

echo "8. Filename Expansion (Globbing)"
echo "   ~/scripts/*.sh  →"
ls ~/scripts/*.sh 2>/dev/null | head -5
echo ""

echo "============================================="
```

```bash
chmod +x ~/scripts/expansion_preview.sh
./expansion_preview.sh
```

---

### 🧠 Quiz — Test Yourself

1. What are the 5 stages Bash goes through from the moment you press Enter to command execution?
2. What is **shell expansion** in one sentence?
3. List all **8 types** of shell expansion in the correct order.
4. What happens after all 8 expansions are complete?
5. Which expansion type does `$PATH` trigger?
6. Which expansion type does `$(date)` trigger?
7. Which expansion type does `{1..10}` trigger?
8. Which expansion type does `*.txt` trigger?
9. Which expansion type does `~` trigger?
10. Why does the **order** of expansions matter?
11. What Bash command lets you see your commands after expansion (debug mode)?
12. Is **quote removal** an expansion? When does it happen?

---

## 📎 Quick Reference Card

```
THE 8 BASH EXPANSIONS — IN ORDER
──────────────────────────────────────────────────────────────
1. Brace Expansion         {a,b,c}  {1..5}  {A..Z}
2. Tilde Expansion         ~  ~user  ~+  ~-
3. Parameter Expansion     $var  ${var}  ${var:-default}
4. Command Substitution    $(command)  `command`
5. Arithmetic Expansion    $((expression))
6. Process Substitution    <(command)  >(command)
7. Word Splitting          automatic — splits on $IFS chars
8. Filename Expansion      *  ?  [abc]  **  (globbing)

Then: QUOTE REMOVAL        " ' \ used for grouping are stripped

COMMAND PROCESSING PIPELINE
──────────────────────────────────────────────────────────────
Input → Tokenization → Parsing → Expansions (1-8) → Quote Removal → Execution

DEBUGGING EXPANSIONS
──────────────────────────────────────────────────────────────
set -x      Enable trace mode — shows commands AFTER expansion
set +x      Disable trace mode
bash -x script.sh   Run entire script in trace mode

MNEMONIC (expansion order)
──────────────────────────────────────────────────────────────
Bold Tigers Prowl Carefully And Pounce Without Fear
Brace · Tilde · Parameter · Command · Arithmetic · Process · Word · Filename
```

---

## 🗂️ Notion Tags (suggested)

`bash` `expansions` `shell-expansions` `tokenization` `brace` `tilde` `parameter` `command-substitution` `arithmetic` `globbing` `word-splitting` `ZTM` `module-2` `section-03`

---

*← Previous: Module 2 | Section 02 — `$@` vs `$*` (Section 02 Complete)*
*→ Next Episode: Module 2 | Section 03 — Brace Expansion*
