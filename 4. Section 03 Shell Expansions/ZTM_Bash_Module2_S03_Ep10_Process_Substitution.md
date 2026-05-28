# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 03 — Shell Expansions
> **Episode:** 10. Process Substitution
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

Process substitution is the **sixth expansion** in the pipeline — sitting between arithmetic expansion and word splitting. This episode introduces it at a conceptual level. It's a more advanced feature whose full power becomes clear when working with arrays, loops, and complex pipelines. The instructor explicitly notes this is an **introduction** — more examples appear throughout the rest of the course.

---

## 🧠 Theory

### 1. What is Process Substitution?

Process substitution means **treating the output of a command as if it were a file**.

In Linux, everything is a file — regular files, devices, sockets. Process substitution extends this philosophy: the output of any running process can be **temporarily represented as a named file** that another command can read from.

```
<(command)
  ↓
The command runs in a subshell.
Its output is made available through a temporary file-like path
(typically /dev/fd/63 or /proc/self/fd/63 on Linux).
That path is then substituted into your command.
```

---

### 2. Syntax

There are two forms:

| Form | Direction | Use |
|---|---|---|
| `<(command)` | **Input** — read from command output | Feed a command's output as a file to another command |
| `>(command)` | **Output** — write into command input | Send a command's input to another command for processing |

> ⚠️ **Critical rule: NO space between `<` (or `>`) and `(`**
```bash
<(ls)     # CORRECT
< (ls)    # WRONG — space causes an error
```

---

### 3. How It Works — The File Path

When you write `<(command)`, Bash:
1. Runs `command` in a subshell
2. Creates a **temporary file descriptor** (like `/dev/fd/63`)
3. **Substitutes that path** into your command line

```bash
# echo shows the temporary file path:
echo <(ls)
# → /dev/fd/63   (the path — not the contents!)

# cat reads the contents through that path:
cat <(ls)
# → (lists the current directory — same as just running ls)
```

> 💡 `echo <(ls)` prints the **filename** (the path). `cat <(ls)` prints the **contents** through that path. This mirrors regular file behaviour — `echo file.txt` prints the name, `cat file.txt` prints the contents.

---

### 4. The Key Use Case — `diff` on Command Outputs

The most common and practical use of process substitution is comparing the **output of two commands** with `diff` — without creating temporary files.

**The classic problem:** `diff` normally takes two file arguments. But what if you want to diff the output of two commands? Without process substitution you'd have to save both outputs to temp files, diff them, then delete the temp files.

**With process substitution — one line:**

```bash
# Compare the contents of two directories
diff <(ls dir1) <(ls dir2)
```

**Step-by-step example:**
```bash
# Setup
mkdir dir1 dir2
touch dir1/a dir1/b
touch dir2/a dir2/c

ls dir1    # → a  b
ls dir2    # → a  c

# Compare with process substitution
diff <(ls dir1) <(ls dir2)
# Output:
# 2c2
# < b       ← only in dir1
# ---
# > c       ← only in dir2
```

**Translation:** File `b` is in `dir1` but not `dir2`. File `c` is in `dir2` but not `dir1`. File `a` exists in both.

> 💡 Without process substitution, you'd need:
> ```bash
> ls dir1 > /tmp/dir1_list.txt
> ls dir2 > /tmp/dir2_list.txt
> diff /tmp/dir1_list.txt /tmp/dir2_list.txt
> rm /tmp/dir1_list.txt /tmp/dir2_list.txt
> ```
> Process substitution collapses all of that into one clean command.

---

### 5. More Examples

```bash
# Diff two sorted outputs
diff <(sort file1.txt) <(sort file2.txt)

# Compare active processes vs a saved list
diff <(ps aux) <(cat saved_processes.txt)

# Find files in dir1 not in dir2
comm -23 <(ls dir1 | sort) <(ls dir2 | sort)

# Compare two commands' outputs side by side
diff <(ls -la /etc) <(ls -la /usr)
```

---

### 6. Position in the Expansion Pipeline

```
1. Brace Expansion          {a,b,c}
2. Tilde Expansion          ~
3. Parameter Expansion      $var
4. Command Substitution     $(...)
5. Arithmetic Expansion     $((...))
6. Process Substitution     <(...) or >(...)   ← HERE
7. Word Splitting
8. Filename Expansion
```

> 📌 Process substitution happens **before word splitting and globbing** — meaning the temporary file path it creates is treated as a single word (no splitting issues).

---

### 7. Process Substitution vs Command Substitution

| Feature | Command Substitution `$(...)` | Process Substitution `<(...)` |
|---|---|---|
| What it returns | The **text output** of the command | A **file path** to the command's output |
| Used as | A string/value | A file argument |
| Primary use | Store output in variable, embed in string | Pass output as file to commands like `diff`, `comm`, `wc` |
| Example | `now=$(date)` | `diff <(cmd1) <(cmd2)` |

---

## 🔑 Key Takeaways

- Process substitution **treats command output as a file** — no temp files needed.
- `<(command)` creates a readable file path from command output.
- `>(command)` creates a writable file path that feeds into command input.
- **No space** between `<` or `>` and `(` — a space causes an error.
- `echo <(ls)` shows the **file path**; `cat <(ls)` shows the **contents**.
- Most common use: `diff <(cmd1) <(cmd2)` — compare two command outputs directly.
- Process substitution is **step 6** in the expansion pipeline.
- This is an introduction — full power shows in later episodes with loops and arrays.
- The temporary file descriptor lives at `/dev/fd/N` on Linux.

---

## 💡 Tips & Tricks

**🔹 The no-temp-file `diff` pattern — the most common use:**
```bash
diff <(command1) <(command2)
# No temp files, no cleanup — one line does it all
```

**🔹 Compare sorted versions of files:**
```bash
diff <(sort file1.txt) <(sort file2.txt)
# Useful when order doesn't matter — just the content
```

**🔹 `comm` for set operations on two sorted lists:**
```bash
# Files only in dir1 (not dir2):
comm -23 <(ls dir1 | sort) <(ls dir2 | sort)

# Files only in dir2 (not dir1):
comm -13 <(ls dir1 | sort) <(ls dir2 | sort)

# Files in BOTH directories:
comm -12 <(ls dir1 | sort) <(ls dir2 | sort)
```

**🔹 `wc` on command output:**
```bash
wc -l <(grep "ERROR" /var/log/syslog)
# Count ERROR lines without creating a temp file
```

**🔹 Always verify the syntax — no space:**
```bash
cat <(echo "hello")    # ✅ correct
cat < (echo "hello")   # ❌ error: space not allowed
```

**🔹 Debug by printing the path first:**
```bash
echo <(ls)     # → /dev/fd/63  (see the temp path)
cat <(ls)      # → actual directory listing
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — See the File Path

**Goal:** Understand that `<(command)` returns a file path, not the output.

```bash
# Print the FILE PATH (not the contents)
echo <(ls)
echo <(date)
echo <(whoami)

# Print the CONTENTS through that path
cat <(ls)
cat <(date)
cat <(whoami)

# Side by side:
echo "Path  : $(echo <(ls))"
echo "Content preview: $(cat <(ls) | head -3)"
```

**Observe:** `echo <(ls)` gives `/dev/fd/63` — a path. `cat <(ls)` gives directory contents.

---

### Exercise 2 — `diff` Two Directories

**Goal:** Re-create the instructor's directory comparison example.

```bash
# Create test directories
mkdir -p /tmp/psub_test/dir1 /tmp/psub_test/dir2
touch /tmp/psub_test/dir1/{a,b,common}
touch /tmp/psub_test/dir2/{c,d,common}

echo "=== dir1 contents ==="
ls /tmp/psub_test/dir1/

echo "=== dir2 contents ==="
ls /tmp/psub_test/dir2/

echo "=== diff (files unique to each dir) ==="
diff <(ls /tmp/psub_test/dir1/) <(ls /tmp/psub_test/dir2/)

echo ""
echo "=== comm (set operations) ==="
echo "Only in dir1:"
comm -23 <(ls /tmp/psub_test/dir1/ | sort) <(ls /tmp/psub_test/dir2/ | sort)

echo "Only in dir2:"
comm -13 <(ls /tmp/psub_test/dir1/ | sort) <(ls /tmp/psub_test/dir2/ | sort)

echo "In both:"
comm -12 <(ls /tmp/psub_test/dir1/ | sort) <(ls /tmp/psub_test/dir2/ | sort)

# Clean up
rm -rf /tmp/psub_test
```

---

### Exercise 3 — Process Substitution vs Command Substitution

**Goal:** See the practical difference between `<(...)` and `$(...)`.

```bash
# Command substitution — captures TEXT output
result=$(ls ~/scripts)
echo "Type: $(echo "$result" | head -1)"    # text string

# Process substitution — returns a FILE PATH
path=<(ls ~/scripts)
echo "Path: $path"        # → /dev/fd/63

# Use cat to read through the file path
echo "Contents via cat:"
cat <(ls ~/scripts) | head -3

# Use wc through process substitution
echo "Script count: $(wc -l < <(ls ~/scripts))"
```

---

### Exercise 4 — Real-World: Compare Sorted Config Files

**Goal:** Use process substitution to compare two files ignoring sort order.

```bash
# Create two test files with same content in different order
cat > /tmp/list1.txt << 'EOF'
banana
apple
cherry
date
EOF

cat > /tmp/list2.txt << 'EOF'
cherry
date
apple
fig
EOF

echo "=== Normal diff (order-sensitive) ==="
diff /tmp/list1.txt /tmp/list2.txt

echo ""
echo "=== Diff after sorting (content-only comparison) ==="
diff <(sort /tmp/list1.txt) <(sort /tmp/list2.txt)
# Only shows content differences, not ordering differences

# Clean up
rm /tmp/list1.txt /tmp/list2.txt
```

---

### Exercise 5 — Count Lines Without Temp Files

**Goal:** Use `<(...)` to pass pipeline output to `wc`.

```bash
# Count lines in filtered output — no temp file needed
echo "=== Line counts ==="
echo "Total processes     : $(wc -l < <(ps aux))"
echo "Running processes   : $(wc -l < <(ps aux | grep -v grep))"
echo "Users in /etc/passwd: $(wc -l < <(cat /etc/passwd))"
echo "Files in /etc       : $(wc -l < <(ls /etc))"
echo "Bash scripts in ~/scripts: $(wc -l < <(ls ~/scripts/*.sh 2>/dev/null))"
```

---

### 🧠 Quiz — Test Yourself

1. What does process substitution allow you to do?
2. What are the two forms of process substitution and what do they do?
3. What is the critical syntax rule about spacing?
4. What does `echo <(ls)` print vs `cat <(ls)`?
5. Where does the temporary file appear on Linux?
6. What is the most common practical use of process substitution?
7. Write the command to compare the output of `ls dir1` and `ls dir2` using `diff`.
8. How is process substitution different from command substitution `$(...)`?
9. What position does process substitution occupy in the 8-step expansion pipeline?
10. Why is process substitution useful compared to using temp files?

---

## 📎 Quick Reference Card

```
PROCESS SUBSTITUTION
──────────────────────────────────────────────────────────────
<(command)    Treat command OUTPUT as a readable file
>(command)    Treat command INPUT as a writable file

CRITICAL RULE
──────────────────────────────────────────────────────────────
<(command)    ✅ CORRECT — no space
< (command)   ❌ WRONG — space causes error

UNDERSTANDING THE PATH
──────────────────────────────────────────────────────────────
echo <(ls)    → /dev/fd/63   (shows the FILE PATH)
cat <(ls)     → (directory listing contents)

COMMON USE CASES
──────────────────────────────────────────────────────────────
diff <(cmd1) <(cmd2)                  Compare two command outputs
diff <(sort f1) <(sort f2)            Compare ignoring sort order
comm -23 <(ls d1|sort) <(ls d2|sort)  Files only in d1
comm -13 <(ls d1|sort) <(ls d2|sort)  Files only in d2
comm -12 <(ls d1|sort) <(ls d2|sort)  Files in both
wc -l < <(command)                    Count lines of output

vs COMMAND SUBSTITUTION
──────────────────────────────────────────────────────────────
$(command)    Returns TEXT — store in variable, embed in string
<(command)    Returns FILE PATH — pass as file argument to commands

POSITION IN EXPANSION PIPELINE
──────────────────────────────────────────────────────────────
1. Brace       {a,b,c}
2. Tilde       ~
3. Parameter   $var
4. Command     $(...)
5. Arithmetic  $((...))
6. Process     <(...)  ← HERE
7. Word Split
8. Filename    *.txt
```

---

## 🗂️ Notion Tags (suggested)

`bash` `process-substitution` `shell-expansions` `diff` `comm` `advanced` `ZTM` `module-2` `section-03`

---

*← Previous: Module 2 | Section 03 — Arithmetic Expansion*
*→ Next Episode: Module 2 | Section 03 — Word Splitting*
