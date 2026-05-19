# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 01 — Intro to Bash Shell Scripting
> **Episode:** 4. Creating Your First Script and Changing the PATH
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

This is the first **hands-on coding episode**. You'll create your very first shell script from scratch, learn how to execute it, and then understand one of the most important concepts in Linux: the **`$PATH` variable** — why it exists, how it works, and how to modify it so your scripts can be run from **anywhere** on the system.

---

## 🧠 Theory

### 1. Setting Up a Scripts Directory

Before writing scripts, it's good practice to **organise them in a dedicated folder** in your home directory.

Two common naming conventions:

| Directory | Convention |
|---|---|
| `~/scripts` | Descriptive, easy to understand |
| `~/bin` | Traditional Unix convention — short for "binaries" |

```bash
# Create a scripts directory in your home folder
mkdir ~/scripts
cd ~/scripts
```

> 💡 Both are equally valid. The instructor uses `~/scripts`. Pick one and stick with it throughout the course.

---

### 2. Creating a Script File

A shell script is just a **plain text file**. Create it with any text editor:

```bash
# Using vim (instructor's choice)
vim first_script.sh

# Using nano (beginner-friendly)
nano first_script.sh

# Using VSCode
code first_script.sh
```

#### ⚠️ Important Filename Rules

| Rule | Detail |
|---|---|
| **No spaces in filenames** | Spaces are special characters in bash — use `_` instead |
| **`.sh` extension** | Optional but conventional — it's a human hint only |
| **Linux ignores extensions** | The OS doesn't care about `.sh` — it's just for us to identify the file |

> 📌 **Note:** Some experienced Linux users prefer scripts **without** the `.sh` extension — to make them look like regular system commands (e.g. `backup` instead of `backup.sh`). Both are fine.

---

### 3. Anatomy of Your First Script

Here's the script created in the episode:

```bash
#!/bin/bash

mkdir -p ~/drun
echo "hello-bash-world" > ~/drun/drun-file.txt
ls -a .
cat ~/drun/drun-file.txt
```

Let's break it down line by line:

| Line | What it does |
|---|---|
| `#!/bin/bash` | **Shebang** — tells the OS to use Bash to interpret this file |
| `mkdir -p ~/drun` | Creates a directory called `drun` (`-p` = no error if it already exists) |
| `echo "..." > file` | Writes a string into a file (creates the file if it doesn't exist) |
| `ls -a .` | Lists contents of the current directory (including hidden files) |
| `cat ~/drun/drun-file.txt` | Prints the contents of the file |

---

### 4. The Shebang Line `#!/bin/bash`

The very **first line** of every Bash script should be:

```bash
#!/bin/bash
```

| Part | Meaning |
|---|---|
| `#!` | "Shebang" — special marker that tells the kernel this file is a script |
| `/bin/bash` | The **absolute path** to the interpreter that should run this script |

> 🔑 **Without the shebang**, the system may use a different shell to run your script — leading to unexpected behaviour or errors. Always include it.

**Other shebang examples:**
```bash
#!/bin/sh          # Run with sh (more portable, but fewer features)
#!/usr/bin/env bash  # Find bash wherever it's installed (more portable)
```

> 💡 `#!/usr/bin/env bash` is preferred in professional scripts because it works even if bash is not at `/bin/bash` (e.g., on some macOS or BSD systems).

---

### 5. Making a Script Executable

A script file must have **execute permission** before it can be run as a command.

```bash
# Give execute permission to the owner only (most secure)
chmod 700 first_script.sh

# Give execute permission to ALL users
chmod +x first_script.sh
```

#### Permission comparison:

| Command | Owner | Group | Others | Use when... |
|---|---|---|---|---|
| `chmod 700` | rwx | --- | --- | Only you should run the script |
| `chmod 755` | rwx | r-x | r-x | All users can read & run, only you can edit |
| `chmod +x` | adds x | adds x | adds x | Quick shorthand to make executable for all |

> ✅ For personal scripts, `chmod 700` or `chmod +x` are the most common choices.

---

### 6. Running the Script — Three Ways

Once executable, you can run the script in three ways:

#### Method 1 — Relative path (from the script's directory)
```bash
./first_script.sh
```
The `./` means "in the **current directory**". Required because the shell won't look in the current directory by default.

#### Method 2 — Absolute path (from anywhere)
```bash
/home/student/scripts/first_script.sh
```
Works from **any directory** on the system.

#### Method 3 — By name only (requires PATH setup — see Section 8)
```bash
first_script.sh
```
Only works if the script's directory is added to `$PATH`.

---

### 7. What is the `$PATH` Variable?

When you type a command (like `ls`, `date`, `grep`), the shell doesn't search **everywhere** on your system for it. It only looks in specific directories defined in the **`$PATH`** variable.

```bash
# View your current PATH
echo $PATH
```

**Example output:**
```
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

Each directory is separated by a **colon `:`**. The shell searches them **left to right** — the first match wins.

```
Shell searches:  /usr/local/sbin → /usr/local/bin → /usr/sbin → /usr/bin → /sbin → /bin
                        ↑ checks each directory in order until command is found
```

> 🔍 This is why `ls` works without `./` — it lives in `/bin` which is already in `$PATH`. Your custom script in `~/scripts` is NOT in PATH by default — that's why you need `./`.

---

### 8. Adding Your Scripts Directory to `$PATH`

To run your scripts **by name from any directory**, add `~/scripts` to your `$PATH`.

#### Step 1: Open `~/.bashrc`
```bash
nano ~/.bashrc
# or
code ~/.bashrc
```

#### Step 2: Add this line at the end of the file
```bash
export PATH="$PATH:~/scripts"
```

**Breaking this down:**
| Part | Meaning |
|---|---|
| `export` | Makes the variable available to child processes |
| `PATH=` | We're reassigning the PATH variable |
| `"$PATH:` | Start with everything already in PATH (preserve existing entries) |
| `~/scripts"` | Then add our scripts directory at the **end** |

> ⚠️ Always append (`$PATH:~/newdir`) — never replace. Replacing PATH breaks all system commands!

#### Step 3: Reload `.bashrc`
```bash
source ~/.bashrc
```

#### Step 4: Verify it was added
```bash
echo $PATH
# You should now see ~/scripts at the end
```

#### Step 5: Run your script by name (no `./` needed!)
```bash
first_script.sh
```

---

### 9. 🔐 Security Warning — PATH Order Matters

The shell searches PATH **left to right**. If you accidentally put your scripts directory at the **beginning** of PATH:

```bash
export PATH="~/scripts:$PATH"   # ⚠️ DANGEROUS — scripts dir searched FIRST
```

If you create a script named `ls` or `sudo` in `~/scripts`, it would run **instead of the real system command** — a serious security risk.

> ✅ **Always append your custom directory at the END of PATH:**
> ```bash
> export PATH="$PATH:~/scripts"   # ✅ SAFE — system commands take priority
> ```

---

### 10. Key Behaviour — `.bashrc` Loads Once

`.bashrc` is only read **when a new shell session starts**. If you edit it and don't reload, the changes won't take effect in the current terminal.

```
Edit .bashrc → changes sit on disk → NOT active yet in current terminal
     ↓
source ~/.bashrc  OR  open new terminal
     ↓
Changes now active ✅
```

---

## 🔑 Key Takeaways

> 📝 The most important things from this episode.

- Organise your scripts in `~/scripts` or `~/bin` — keep them tidy.
- **No spaces in filenames** — use underscores (`_`) instead.
- The `.sh` extension is a **human convention only** — Linux ignores it.
- Always start a Bash script with `#!/bin/bash` (the **shebang line**).
- Scripts need **execute permission**: `chmod +x script.sh` or `chmod 700 script.sh`.
- Use `./script.sh` to run from the current directory.
- Use the **absolute path** to run from any directory.
- `$PATH` is a list of directories the shell searches for commands — **colon-separated, left to right**.
- To run scripts by name, add their directory to `$PATH` in `~/.bashrc`.
- **Always append** your directory to the END of `$PATH` — never prepend (security risk).
- After editing `~/.bashrc`, run `source ~/.bashrc` to activate changes.

---

## 💡 Tips & Tricks

**🔹 Use `#!/usr/bin/env bash` for portability:**
```bash
#!/usr/bin/env bash
```
This finds bash wherever it's installed — safer for scripts shared across different systems.

**🔹 Check if a command is an alias, built-in, or binary:**
```bash
type ls        # tells you: ls is aliased to...
type cd        # cd is a shell builtin
type bash      # bash is /bin/bash
```

**🔹 `which` finds the location of a command:**
```bash
which bash      # → /bin/bash
which python3   # → /usr/bin/python3
```

**🔹 See the full PATH clearly (one directory per line):**
```bash
echo $PATH | tr ':' '\n'
```

**🔹 Quickly scaffold a new script with the shebang already added:**
```bash
echo '#!/bin/bash' > my_script.sh && chmod +x my_script.sh
```

**🔹 VSCode Tip — Run your script with a keyboard shortcut:**
- Install the **"Code Runner"** extension
- Open your `.sh` file
- Press `Ctrl+Alt+N` to run it directly from the editor

**🔹 Common mistake — forgetting `./`:**
```bash
first_script.sh    # ❌ command not found (unless PATH is set)
./first_script.sh  # ✅ works from the script's directory
```

---

## 🏋️ Practice Exercises

> All exercises should be done in your terminal inside VSCode. Build on each exercise — they connect to each other.

---

### Exercise 1 — Set Up Your Scripts Directory

**Goal:** Create and organise your workspace for the rest of the course.

```bash
# Create your scripts directory
mkdir ~/scripts

# Navigate into it
cd ~/scripts

# Confirm you're in the right place
pwd
```

---

### Exercise 2 — Write Your First Script

**Goal:** Create a script from scratch and understand its structure.

**Step 1:** Create the file in VSCode
```bash
code ~/scripts/first_script.sh
```

**Step 2:** Write the following inside:
```bash
#!/bin/bash

# First script — creates a directory, writes a file, lists both
mkdir -p ~/drun
echo "hello-bash-world" > ~/drun/drun-file.txt
ls -a .
cat ~/drun/drun-file.txt
```

**Step 3:** Save, make executable, and run
```bash
chmod +x ~/scripts/first_script.sh
cd ~/scripts
./first_script.sh
```

**Expected output:**
```
.  ..  drun  (contents of current directory)
hello-bash-world
```

---

### Exercise 3 — Run Using All Three Methods

**Goal:** Practice all three ways to execute a script.

```bash
# Method 1: Relative path (from scripts directory)
cd ~/scripts
./first_script.sh

# Method 2: Absolute path (from a different directory)
cd /tmp
/home/YOUR_USERNAME/scripts/first_script.sh

# Method 3: By name (will fail for now — set up in Exercise 4)
first_script.sh    # ← expected: command not found
```

---

### Exercise 4 — Add `~/scripts` to `$PATH`

**Goal:** Make your scripts runnable from anywhere.

**Step 1:** Open `.bashrc`
```bash
code ~/.bashrc
```

**Step 2:** Add this at the **very end** of the file:
```bash
# Add personal scripts directory to PATH
export PATH="$PATH:~/scripts"
```

**Step 3:** Save and reload
```bash
source ~/.bashrc
```

**Step 4:** Verify PATH was updated
```bash
echo $PATH | tr ':' '\n'
# ~/scripts should appear at the bottom of the list
```

**Step 5:** Run your script by name from any directory
```bash
cd /tmp
first_script.sh    # ← should now work! ✅
```

---

### Exercise 5 — Explore PATH Behaviour

**Goal:** Understand how PATH order affects command resolution.

```bash
# See which 'ls' command bash finds first
which ls
type ls

# See all your PATH directories, one per line
echo $PATH | tr ':' '\n'

# Confirm your scripts dir is at the END (safe position)
echo $PATH | tr ':' '\n' | tail -5
```

---

### Exercise 6 — Build a Personal Info Script

**Goal:** Write a more complete script that uses everything from this episode and the previous one.

Create `~/scripts/my_info.sh`:

```bash
#!/bin/bash

# my_info.sh — Personal system info script

echo "========================================="
echo "         SYSTEM & USER INFORMATION"
echo "========================================="
echo ""
echo "  User       : $(whoami)"
echo "  Hostname   : $(hostname)"
echo "  Shell      : $SHELL"
echo "  Home Dir   : $HOME"
echo "  Current Dir: $(pwd)"
echo "  Date/Time  : $(date '+%A, %d %B %Y — %H:%M:%S')"
echo ""
echo "========================================="
echo "               DISK USAGE"
echo "========================================="
df -h /
echo ""
echo "========================================="
echo "             MEMORY USAGE"
echo "========================================="
free -h
echo ""
echo "  Script PATH: $0"
echo "========================================="
```

Make it executable and run it:
```bash
chmod +x ~/scripts/my_info.sh
my_info.sh    # runs from anywhere (PATH must be set from Exercise 4)
```

---

### 🧠 Quiz — Test Yourself

1. What are two common directory names used to store personal scripts?
2. Why should you avoid spaces in script filenames?
3. What does the `.sh` extension actually do on a Linux system?
4. What is the **shebang line** and where does it go?
5. What is the difference between `chmod 700` and `chmod +x`?
6. Why do you need `./` before a script name when running it?
7. What does `$PATH` contain and what is it used for?
8. What is the syntax to add `~/scripts` to your PATH in `.bashrc`?
9. Why is it dangerous to prepend (`~/scripts:$PATH`) instead of append (`$PATH:~/scripts`)?
10. What command reloads `.bashrc` without restarting the terminal?

---

## 📎 Quick Reference Card

```
SETUP
──────────────────────────────────────────────────────────────
mkdir ~/scripts              Create a scripts directory
cd ~/scripts                 Navigate into it

CREATING & RUNNING SCRIPTS
──────────────────────────────────────────────────────────────
code script.sh               Create/edit script in VSCode
chmod +x script.sh           Add execute permission (all users)
chmod 700 script.sh          Add execute permission (owner only)
./script.sh                  Run from current directory
bash script.sh               Run without execute permission
/full/path/script.sh         Run from any directory (absolute path)
script.sh                    Run by name (only if dir is in PATH)

PATH MANAGEMENT
──────────────────────────────────────────────────────────────
echo $PATH                   View current PATH
echo $PATH | tr ':' '\n'     View PATH one directory per line
which command                Find location of a command
type command                 Show what type a command is

Add to ~/.bashrc:
export PATH="$PATH:~/scripts"    ✅ SAFE — append at end
export PATH="~/scripts:$PATH"    ⚠️  UNSAFE — prepend at start

source ~/.bashrc             Reload .bashrc without restarting terminal

SHEBANG LINE (first line of every script)
──────────────────────────────────────────────────────────────
#!/bin/bash                  Standard — use bash at /bin/bash
#!/usr/bin/env bash          Portable — find bash wherever it is
```

---

## 🗂️ Notion Tags (suggested)

`bash` `shell-scripting` `path-variable` `chmod` `shebang` `first-script` `linux` `automation` `ZTM` `module-2` `section-01`

---

*← Previous: Module 2 | Section 01 — Shell and Scripts*
*→ Next Episode: Module 2 | Section 01 — (Next transcript)*
