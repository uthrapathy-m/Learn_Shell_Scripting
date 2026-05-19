# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 01 — Intro to Bash Shell Scripting
> **Episode:** 6. The Bash Shebang
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

This episode takes a **deep dive into the shebang** (`#!`) — the very first line of every script. You already saw it briefly in the previous episode. Here you'll fully understand what it is, why it exists, what happens when it's missing, and how it applies beyond Bash to **any scripting language** like Python, PHP, and more.

---

## 🧠 Theory

### 1. What is the Shebang?

The **shebang** is a special two-character sequence at the very start of a script file:

```bash
#!
```

| Character | Name | Combined |
|---|---|---|
| `#` | Hash | `#!` = **"Shebang"** |
| `!` | Bang | (short for "hash-bang") |

> 🗣️ **Etymology:** "Hash" + "Bang" → "Hashbang" → shortened to **"Shebang"**

It must always be on the **first line, first characters** of the file — no blank lines, no spaces before it.

---

### 2. What Does the Shebang Do?

The shebang tells the **operating system kernel** which program (interpreter) should be used to execute the script.

```
#!/bin/bash
  ↑
  └── Absolute path to the interpreter
```

**Full picture of how it works:**

```
You run:  ./my_script.sh
              ↓
    Kernel reads first line → #!/bin/bash
              ↓
    Kernel calls:  /bin/bash my_script.sh
              ↓
    Bash interprets and runs each line of the script
```

Without the shebang, the kernel has no instruction — it falls back to the **default shell** of the current session (usually bash). This works most of the time, but it's **implicit and unreliable** — especially when the script is written in a different language.

---

### 3. Shebang Syntax

```bash
#!/path/to/interpreter
```

**Rules:**
| Rule | Detail |
|---|---|
| Must be **line 1** | The very first line of the file — nothing before it |
| Must start with `#!` | No space between `#` and `!` |
| Space after `#!` | Optional — `#!/bin/bash` and `#! /bin/bash` both work |
| Must be an **absolute path** | `/bin/bash` ✅ — `bash` alone ❌ |

---

### 4. Common Shebang Lines

```bash
#!/bin/bash             # Bash — standard choice for bash scripts
#!/bin/sh               # sh — portable, minimal POSIX shell
#!/usr/bin/env bash     # Bash — portable (finds bash in PATH)
#!/usr/bin/env python3  # Python 3
#!/usr/bin/python3      # Python 3 (direct path)
#!/usr/bin/env php      # PHP
#!/usr/bin/env node     # Node.js
#!/usr/bin/perl         # Perl
```

> 💡 The shebang is **not just for Bash** — it works for any interpreted language. The interpreter just needs to be installed on the system.

---

### 5. `#!/bin/bash` vs `#!/usr/bin/env bash` — Which to Use?

This is an important distinction worth understanding:

| Shebang | How it works | Best used when... |
|---|---|---|
| `#!/bin/bash` | Directly calls bash at `/bin/bash` | You know bash is always at `/bin/bash` (most Linux systems) |
| `#!/usr/bin/env bash` | Asks `env` to find `bash` in `$PATH` | Writing portable scripts (macOS, BSD, NixOS, Docker containers) |

**Why does it matter?**
On some systems (macOS with Homebrew, NixOS, some containers), bash may be installed at a non-standard path like `/usr/local/bin/bash` or `/opt/homebrew/bin/bash`. Using `#!/usr/bin/env bash` handles this gracefully.

```bash
# Find where bash actually is on your system:
which bash
```

> ✅ **Recommendation:** Use `#!/bin/bash` for scripts that will always run on your own Linux machine. Use `#!/usr/bin/env bash` for scripts you share or deploy across different systems.

---

### 6. What Happens Without a Shebang?

```bash
# Script with NO shebang:
mkdir test_dir
echo "created"
```

When run as `./script.sh`, the kernel has no instruction → it defaults to the **current user's default shell** (usually bash on Linux). The script may work **by accident** — but this is fragile:

- If another user runs it with a different default shell → may break
- If the script contains bash-specific syntax (`[[`, arrays, etc.) → will break in `sh`
- If the script is Python or PHP code → will fail completely

**Proof — Python script without shebang:**
```python
# my_script1 (NO shebang)
import sys
print(sys.version)
```
Running `./my_script1` → bash tries to interpret Python code → **error**

**Same script WITH shebang:**
```python
#!/usr/bin/python3
import sys
print(sys.version)
```
Running `./my_script1` → kernel calls Python → **works perfectly** ✅

---

### 7. The Shebang is Invisible to the Interpreter

Once the kernel reads the shebang and launches the interpreter, **the shebang line itself is NOT passed to the interpreter** as code to execute. It's purely an OS-level instruction.

- In Bash, `#` starts a comment anyway — so even if bash saw it, it would be ignored.
- In Python, `#` also starts a comment — same result.
- This is by design and is why the `#!` convention works across languages.

---

### 8. Checking Interpreter Paths on Your System

Before writing a shebang, verify the interpreter path on your machine:

```bash
which bash        # → /bin/bash
which python3     # → /usr/bin/python3
which php         # → /usr/bin/php
which node        # → /usr/bin/node  (if installed)
which perl        # → /usr/bin/perl
```

---

## 🔑 Key Takeaways

> 📝 Commit these to memory — the shebang is used in every single script.

- **Shebang = `#!`** — "hash-bang", shortened to "shebang."
- It must be **line 1, character 1** of the script — nothing before it.
- It tells the **kernel** which interpreter to use to run the file.
- Without a shebang, the kernel defaults to the **current shell** — unreliable.
- The shebang works for **any scripting language** — Bash, Python, PHP, Node.js, Perl, etc.
- **Space after `#!` is optional** — `#!/bin/bash` and `#! /bin/bash` are both valid.
- `#!/bin/bash` = direct path; `#!/usr/bin/env bash` = portable (finds bash in `$PATH`).
- Always use `which interpreter` to verify the correct path before writing the shebang.
- The shebang line is **invisible to the interpreter** — the kernel consumes it, not bash/python.

---

## 💡 Tips & Tricks

**🔹 Always verify your interpreter path first:**
```bash
which bash
which python3
```
Then copy the path directly into your shebang. Never guess.

**🔹 `env` shebang for maximum portability:**
```bash
#!/usr/bin/env bash
```
`env` searches `$PATH` for bash — works across Linux, macOS, BSD, containers.

**🔹 Check what interpreter will run your script:**
```bash
head -1 script.sh      # Print just the first line (the shebang)
```

**🔹 Script in wrong language? The shebang will save you:**
If you accidentally run a Python file without the right shebang, you'll see bash errors like `syntax error near unexpected token`. Adding the correct shebang fixes it immediately.

**🔹 The `file` command identifies script types:**
```bash
file my_script.sh      # → Bourne-Again shell script, ASCII text executable
file my_script1        # → Python script, ASCII text executable
```

**🔹 Shebang with arguments:**
You can pass arguments to the interpreter in the shebang:
```bash
#!/bin/bash -x         # Run bash in debug/trace mode
#!/usr/bin/env python3 -u  # Run python3 with unbuffered output
```

---

## 🏋️ Practice Exercises

> Build on the `~/scripts` directory from the previous episode.

---

### Exercise 1 — Verify Interpreter Paths

**Goal:** Find the correct paths to common interpreters on your Linux system.

```bash
which bash
which sh
which python3
which perl

# Print them all at once:
for interpreter in bash sh python3 perl; do
  echo "$interpreter → $(which $interpreter 2>/dev/null || echo 'not installed')"
done
```

Write down the paths — you'll use them in your shebangs.

---

### Exercise 2 — Add a Shebang to Your First Script

**Goal:** Update the script from Episode 4 to include the shebang.

```bash
code ~/scripts/first_script.sh
```

Make sure the **very first line** is:
```bash
#!/bin/bash
```

Your full script should now look like:
```bash
#!/bin/bash

mkdir -p ~/drun
echo "hello-bash-world" > ~/drun/drun-file.txt
ls -a .
cat ~/drun/drun-file.txt
```

Run it and confirm it still works:
```bash
./first_script.sh
```

---

### Exercise 3 — Prove the Shebang Matters (Python Demo)

**Goal:** See exactly what happens with and without a shebang for a non-bash script.

**Step 1:** Create a Python script WITHOUT a shebang
```bash
code ~/scripts/python_test
```

Write inside:
```python
import sys
print("Python version:", sys.version)
print("Hello from Python!")
```

**Step 2:** Make it executable and run it
```bash
chmod +x ~/scripts/python_test
./python_test
```

**Observe:** Bash tries to run Python code → errors ❌

**Step 3:** Add the correct shebang as the first line
```python
#!/usr/bin/python3
import sys
print("Python version:", sys.version)
print("Hello from Python!")
```

**Step 4:** Run it again
```bash
./python_test
```

**Observe:** Python runs it correctly → prints version and message ✅

---

### Exercise 4 — Compare `#!/bin/bash` vs `#!/usr/bin/env bash`

**Goal:** Understand the portability difference between the two.

```bash
# Create two identical scripts with different shebangs

# Script 1 — direct path
cat > ~/scripts/shebang_direct.sh << 'EOF'
#!/bin/bash
echo "Interpreter: $BASH_VERSION"
echo "Running from: $(which bash)"
EOF

# Script 2 — env lookup
cat > ~/scripts/shebang_env.sh << 'EOF'
#!/usr/bin/env bash
echo "Interpreter: $BASH_VERSION"
echo "Running from: $(which bash)"
EOF

chmod +x ~/scripts/shebang_direct.sh ~/scripts/shebang_env.sh

# Run both and compare
./shebang_direct.sh
./shebang_env.sh
```

Both should produce identical output on your system. The difference matters on systems where bash is not at `/bin/bash`.

---

### Exercise 5 — Inspect Scripts with `head` and `file`

**Goal:** Use system tools to inspect shebangs on existing scripts.

```bash
# Check the shebang of your scripts
head -1 ~/scripts/first_script.sh
head -1 ~/scripts/python_test

# Use 'file' to identify script types
file ~/scripts/first_script.sh
file ~/scripts/python_test

# Check shebangs on real system scripts
head -1 /etc/init.d/cron 2>/dev/null || echo "file not found"
ls /usr/bin/*.sh 2>/dev/null | head -5 | xargs -I{} head -1 {}
```

**Observe:** Real system scripts use shebangs too — it's a universal convention.

---

### Exercise 6 — Multi-Language Script Collection

**Goal:** Create two scripts in different languages, each with the correct shebang, stored in `~/scripts`.

**Script 1 — Bash:**
```bash
cat > ~/scripts/hello_bash.sh << 'EOF'
#!/bin/bash
echo "Hello from Bash!"
echo "Shell: $BASH_VERSION"
EOF
chmod +x ~/scripts/hello_bash.sh
```

**Script 2 — Python:**
```bash
cat > ~/scripts/hello_python << 'EOF'
#!/usr/bin/python3
import sys
import platform
print("Hello from Python!")
print(f"Python version: {sys.version}")
print(f"Platform: {platform.system()}")
EOF
chmod +x ~/scripts/hello_python
```

**Run both:**
```bash
hello_bash.sh
hello_python
```

Both run perfectly from anywhere — because they each declare their own interpreter via the shebang. ✅

---

### 🧠 Quiz — Test Yourself

1. What do the characters `#` and `!` stand for in the word "shebang"?
2. On which line of a script must the shebang appear?
3. What does the shebang tell the operating system?
4. What happens if you run a script with **no shebang**?
5. What is the shebang for running a Python 3 script?
6. What is the difference between `#!/bin/bash` and `#!/usr/bin/env bash`?
7. Is the space after `#!` required? (e.g., `#! /bin/bash`)
8. Does the bash interpreter see and execute the shebang line? Why or why not?
9. What command would you use to find the correct path to python3 for a shebang?
10. What Linux command shows you the type of a script file?

---

## 📎 Quick Reference Card

```
SHEBANG SYNTAX
──────────────────────────────────────────────────────────────
#!/bin/bash              Standard bash shebang
#!/usr/bin/env bash      Portable bash shebang (recommended for sharing)
#!/bin/sh                Minimal POSIX shell
#!/usr/bin/python3       Python 3
#!/usr/bin/env python3   Python 3 (portable)
#!/usr/bin/env node      Node.js
#!/usr/bin/perl          Perl
#!/usr/bin/env php        PHP

RULES
──────────────────────────────────────────────────────────────
✅  Must be the FIRST line of the file
✅  Must start with #!  (no spaces between # and !)
✅  Space after #! is optional
✅  Must use absolute path to interpreter
❌  Cannot have blank lines above it

USEFUL COMMANDS
──────────────────────────────────────────────────────────────
which bash               Find path to bash
which python3            Find path to python3
head -1 script.sh        View the shebang of a script
file script.sh           Identify the type/interpreter of a script

COMPARISON
──────────────────────────────────────────────────────────────
#!/bin/bash              → Direct: bash must be at /bin/bash
#!/usr/bin/env bash      → Flexible: finds bash anywhere in $PATH
```

---

## 🗂️ Notion Tags (suggested)

`bash` `shebang` `interpreter` `shell-scripting` `python` `linux` `scripts` `ZTM` `module-2` `section-01`

---

*← Previous: Module 2 | Section 01 — Creating Your First Script and Changing the PATH*
*→ Next Episode: Module 2 | Section 01 — Commenting Out Code*
