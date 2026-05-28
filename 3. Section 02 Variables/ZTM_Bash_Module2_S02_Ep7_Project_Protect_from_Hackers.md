# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 02 — Variables
> **Episode:** 7. Project — How to Protect Yourself from Hackers
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

This is the **first real project** of the course — a practical, real-world script that a sysadmin would actually use. It brings together everything covered in Section 02: `read`, variables, and positional parameters. The project comes in **two versions**: an interactive one (using `read`) and an automated one (using positional parameters via `$1`). Both block malicious IPs using the Linux firewall tool `iptables`.

---

## 🧠 Theory

### 1. The Real-World Problem — SSH Brute Force Attacks

When you run a Linux server on the internet, it is constantly probed by automated bots and attackers. The most common attack is an **SSH brute force** — trying thousands of username/password combinations against your SSH port.

You'll see entries like this in your system logs:

```bash
# Real-time SSH auth failures — watch the logs live:
tail -f /var/log/auth.log | grep "Failed"
```

**Example log entries:**
```
Failed password for root from 192.168.1.55 port 42312 ssh2
Invalid user admin from 203.0.113.42 port 51234
Failed password for invalid user test from 198.51.100.7
```

Each line contains:
- The **attack type** (failed password, invalid user)
- The **source IP address** of the attacker
- The **port** they connected from

> 🎯 **Goal:** Write a script that takes an attacker's IP address, and uses `iptables` to silently **DROP all incoming packets** from that IP.

---

### 2. How `iptables` Blocking Works

`iptables` is the standard Linux firewall tool. The rule used in this project:

```bash
iptables -I INPUT -s <IP_ADDRESS> -j DROP
```

| Part | What it does |
|---|---|
| `iptables` | The Linux firewall command |
| `-I INPUT` | **Insert** the rule at the TOP of the INPUT chain (highest priority) |
| `-s <IP>` | Match packets by **source** IP/network/domain |
| `-j DROP` | **Action:** silently discard the packet — no response sent to attacker |

**DROP vs REJECT:**
| Action | Behaviour | Attacker sees |
|---|---|---|
| `DROP` | Packet silently discarded | Connection times out (no response) |
| `REJECT` | Packet discarded + error sent back | Connection refused immediately |

> ✅ `DROP` is preferred over `REJECT` for attackers — they get no confirmation that the port exists or that their packet was received. It wastes their time.

> ⚠️ `iptables` requires **root privileges**. Always run this script with `sudo`.

---

### 3. Version 1 — Interactive Script (using `read`)

This version **prompts the user** to enter the IP during script execution.

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : dropPackets.sh
# Purpose : Block all incoming packets from a given IP/network/domain
# Usage   : sudo ./dropPackets.sh
# Requires: Root privileges
# ─────────────────────────────────────────────────────

# Prompt user for the IP to block
read -rp "Enter IP, network or domain to drop: " ip

# Confirm what we're doing
echo "Blocking connections from: $ip"
sleep 1

# Apply the firewall rule
iptables -I INPUT -s "$ip" -j DROP

echo "Done. Packets from $ip are now being dropped."
```

**How it runs:**
```bash
sudo ./dropPackets.sh
# → Enter IP, network or domain to drop: 192.168.1.55
# → Blocking connections from: 192.168.1.55
# → Done. Packets from 192.168.1.55 are now being dropped.
```

---

### 4. Version 2 — Argument-Based Script (using `$1`)

This version accepts the IP as a **command-line argument** — better for automation.

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : dropPackets_arg.sh
# Purpose : Block all incoming packets from IP passed as argument
# Usage   : sudo ./dropPackets_arg.sh <IP/network/domain>
# Example : sudo ./dropPackets_arg.sh 192.168.1.55
# Requires: Root privileges
# ─────────────────────────────────────────────────────

# $1 holds the IP/network/domain passed on the command line
echo "Blocking connections from: $1"
sleep 1

# Apply the firewall rule
iptables -I INPUT -s "$1" -j DROP

echo "Done. Packets from $1 are now being dropped."
```

**How it runs:**
```bash
sudo ./dropPackets_arg.sh 192.168.1.55
# → Blocking connections from: 192.168.1.55
# → Done. Packets from 192.168.1.55 are now being dropped.
```

---

### 5. Version 1 vs Version 2 — When to Use Each

| | Version 1 (`read`) | Version 2 (`$1`) |
|---|---|---|
| **Input method** | Interactive prompt during run | Command-line argument before run |
| **Who uses it** | A human manually responding to an attack | Automated system or another script |
| **Best for** | Manual incident response | Automated log parsing and blocking |
| **Can be scripted?** | ❌ Requires human input | ✅ Fully automatable |
| **Example use** | Admin sits at terminal, reads log, runs script | Cron job reads `/var/log/auth.log`, auto-blocks IPs |

> 💡 **Real-world extension:** The argument version (`$1`) could be fed by a log-parsing script that automatically extracts attacker IPs and blocks them without any human intervention.

---

### 6. Undoing the Firewall Rule

After testing, you'll want to **remove** the rule to restore normal connectivity:

```bash
# Remove a specific DROP rule (replace IP with the one you blocked):
sudo iptables -D INPUT -s 192.168.1.55 -j DROP

# List all current iptables rules (to verify):
sudo iptables -L INPUT -n -v --line-numbers

# Flush ALL INPUT rules (use with caution!):
sudo iptables -F INPUT
```

> ⚠️ `iptables -F INPUT` removes ALL INPUT rules — only use this in a safe test environment.

---

### 7. Concepts Applied in This Project

This project consolidates everything from Section 02:

| Concept | Where used |
|---|---|
| **Variables** | `ip="$input"` — storing the target IP |
| **`read -rp`** | Prompting the user interactively (Version 1) |
| **Positional parameters `$1`** | Taking IP as command-line argument (Version 2) |
| **Double quoting** | `"$ip"` and `"$1"` — safe use in commands |
| **`echo` + `sleep`** | User feedback with a pause |
| **Shebang `#!/bin/bash`** | Declaring the interpreter |
| **Comments** | Documenting the script header and inline |
| **`chmod +x`** | Giving execute permission |
| **`sudo` / root** | Privilege requirement for `iptables` |

---

## 🔑 Key Takeaways

- Real servers are constantly under attack — brute force SSH attempts are logged in `/var/log/auth.log`.
- `iptables -I INPUT -s <IP> -j DROP` silently blocks all traffic from a source IP.
- `-I` inserts the rule at the **top** of the chain (highest priority) — use this over `-A` (append) when blocking attackers.
- `DROP` is preferred over `REJECT` — gives attackers no information, wastes their time.
- **Version 1 (interactive):** Uses `read -rp` — good for manual incident response.
- **Version 2 (argument):** Uses `$1` — good for automation and scripting pipelines.
- Always **double-quote** the variable/parameter when passing to `iptables`: `"$ip"` not `$ip`.
- Always run `iptables` commands with **root privileges** (`sudo`).
- Test the rule then **undo it** with `iptables -D` after testing.
- The argument-based version is more powerful for real-world use — it can be called by other scripts.

---

## 💡 Tips & Tricks

**🔹 Watch SSH attacks in real time on your own server:**
```bash
sudo tail -f /var/log/auth.log | grep -i "failed\|invalid"
```

**🔹 Extract attacker IPs automatically from logs:**
```bash
# Get all unique IPs that failed SSH login
grep "Failed password" /var/log/auth.log | \
  awk '{print $11}' | sort | uniq -c | sort -rn | head -10
```

**🔹 View all active iptables rules:**
```bash
sudo iptables -L INPUT -n -v --line-numbers
```

**🔹 Check if a specific IP is already blocked:**
```bash
sudo iptables -L INPUT -n | grep "192.168.1.55"
```

**🔹 Block an entire network (CIDR notation):**
```bash
# Block all IPs in the 10.0.0.0/8 range:
sudo iptables -I INPUT -s 10.0.0.0/8 -j DROP
```

**🔹 Make iptables rules persist across reboots:**
```bash
# Save current rules:
sudo iptables-save > /etc/iptables/rules.v4

# Restore on reboot (Debian/Ubuntu):
sudo apt install iptables-persistent
```

**🔹 Always add a root check to scripts that need it:**
```bash
if [[ "$EUID" -ne 0 ]]; then
    echo "Error: This script must be run as root."
    echo "Run: sudo $0"
    exit 1
fi
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Build Version 1: Interactive Script

**Goal:** Write the interactive `dropPackets.sh` from scratch without looking at the notes.

Create `~/scripts/dropPackets.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : dropPackets.sh
# Purpose : Block all incoming packets from a given IP
# Usage   : sudo ./dropPackets.sh
# ─────────────────────────────────────────────────────

# TODO: Prompt user for IP address
# TODO: Show a message about what is being blocked
# TODO: Sleep 1 second
# TODO: Apply iptables rule
# TODO: Show "Done" message
```

Fill in each `TODO` then test it.

**Full solution (compare after attempting):**
```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : dropPackets.sh
# Purpose : Block all incoming packets from a given IP
# Usage   : sudo ./dropPackets.sh
# ─────────────────────────────────────────────────────

read -rp "Enter IP, network or domain to drop: " ip
echo "Blocking connections from: $ip"
sleep 1
iptables -I INPUT -s "$ip" -j DROP
echo "Done. All packets from $ip are now dropped."
```

---

### Exercise 2 — Build Version 2: Argument-Based Script

**Goal:** Convert Version 1 to use a positional parameter instead of `read`.

```bash
cp ~/scripts/dropPackets.sh ~/scripts/dropPackets_arg.sh
code ~/scripts/dropPackets_arg.sh
```

Changes to make:
- Remove the `read` line
- Replace `$ip` with `$1` throughout
- Update the header comments (usage, example)

**Full solution:**
```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : dropPackets_arg.sh
# Purpose : Block all incoming packets from IP given as argument
# Usage   : sudo ./dropPackets_arg.sh <IP/network/domain>
# Example : sudo ./dropPackets_arg.sh 192.168.1.55
# ─────────────────────────────────────────────────────

echo "Blocking connections from: $1"
sleep 1
iptables -I INPUT -s "$1" -j DROP
echo "Done. All packets from $1 are now dropped."
```

---

### Exercise 3 — Enhance Both Scripts with Validation

**Goal:** Add professional-grade error checking to both scripts.

Add these improvements to `dropPackets.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : dropPackets.sh (enhanced)
# Purpose : Block all incoming packets from a given IP
# Usage   : sudo ./dropPackets.sh
# ─────────────────────────────────────────────────────

# 1. Check for root privileges
if [[ "$EUID" -ne 0 ]]; then
    echo "Error: This script must be run as root."
    echo "Usage: sudo $0"
    exit 1
fi

# 2. Prompt user with raw + silent input
read -rp "Enter IP, network or domain to drop: " ip

# 3. Validate input is not empty
if [[ -z "$ip" ]]; then
    echo "Error: No IP address entered. Exiting."
    exit 1
fi

# 4. Confirm before applying
read -rp "Block ALL traffic from '$ip'? (y/n): " confirm
if [[ "$confirm" != "y" && "$confirm" != "Y" ]]; then
    echo "Cancelled. No rules applied."
    exit 0
fi

# 5. Apply the rule
echo ""
echo "Blocking connections from: $ip"
sleep 1
iptables -I INPUT -s "$ip" -j DROP

echo "Done. Packets from '$ip' are now being dropped."
echo "To undo: sudo iptables -D INPUT -s $ip -j DROP"
```

Add the same improvements to `dropPackets_arg.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : dropPackets_arg.sh (enhanced)
# Usage   : sudo ./dropPackets_arg.sh <IP/network/domain>
# ─────────────────────────────────────────────────────

# 1. Check for root privileges
if [[ "$EUID" -ne 0 ]]; then
    echo "Error: Must be run as root. Use: sudo $0 <IP>"
    exit 1
fi

# 2. Check argument was provided
if [[ -z "$1" ]]; then
    echo "Error: No IP address provided."
    echo "Usage: sudo $0 <IP/network/domain>"
    echo "Example: sudo $0 192.168.1.55"
    exit 1
fi

ip="$1"

echo "Blocking connections from: $ip"
sleep 1
iptables -I INPUT -s "$ip" -j DROP
echo "Done. Packets from '$ip' are now being dropped."
echo "To undo: sudo iptables -D INPUT -s $ip -j DROP"
```

---

### Exercise 4 — Test the Scripts (Safe Lab Test)

**Goal:** Test your scripts safely without breaking real connectivity.

> ⚠️ Only test on a virtual machine or in a lab environment. Never test blocking rules on a production server.

```bash
# Step 1: Check what rules exist before
sudo iptables -L INPUT -n --line-numbers

# Step 2: Test pinging a public IP (verify it works)
ping -c 3 8.8.8.8

# Step 3: Run the script to block it
sudo ./dropPackets_arg.sh 8.8.8.8

# Step 4: Try pinging again — should fail
ping -c 3 8.8.8.8

# Step 5: Check rules to see your new rule
sudo iptables -L INPUT -n --line-numbers

# Step 6: REMOVE the rule (restore connectivity)
sudo iptables -D INPUT -s 8.8.8.8 -j DROP

# Step 7: Ping again — should work now
ping -c 3 8.8.8.8
```

---

### Exercise 5 — View and Manage iptables Rules

**Goal:** Get comfortable with viewing and removing iptables rules.

```bash
# List all INPUT rules with line numbers
sudo iptables -L INPUT -n -v --line-numbers

# Add a few test rules
sudo iptables -I INPUT -s 10.0.0.1 -j DROP
sudo iptables -I INPUT -s 10.0.0.2 -j DROP
sudo iptables -I INPUT -s 10.0.0.3 -j DROP

# List them again
sudo iptables -L INPUT -n --line-numbers

# Delete by line number (safer than by rule)
sudo iptables -D INPUT 1    # deletes rule #1

# Delete by rule specification
sudo iptables -D INPUT -s 10.0.0.2 -j DROP

# Delete all remaining test rules
sudo iptables -D INPUT -s 10.0.0.3 -j DROP

# Verify they're gone
sudo iptables -L INPUT -n --line-numbers
```

---

### Exercise 6 — Extend: Block Multiple IPs from a List

**Goal:** Extend the concept to block multiple IPs from a file — a taste of what's coming with loops.

Create `~/scripts/block_list.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : block_list.sh
# Purpose : Block all IPs listed in a file (one IP per line)
# Usage   : sudo ./block_list.sh <ip_list_file>
# Example : sudo ./block_list.sh attackers.txt
# ─────────────────────────────────────────────────────

if [[ "$EUID" -ne 0 ]]; then
    echo "Error: Must be run as root."
    exit 1
fi

if [[ -z "$1" ]]; then
    echo "Usage: sudo $0 <ip_list_file>"
    exit 1
fi

ip_file="$1"

if [[ ! -f "$ip_file" ]]; then
    echo "Error: File '$ip_file' not found."
    exit 1
fi

echo "Blocking all IPs from: $ip_file"
echo ""

blocked=0
while IFS= read -r ip || [[ -n "$ip" ]]; do
    # Skip empty lines and comments
    [[ -z "$ip" || "$ip" == \#* ]] && continue
    
    iptables -I INPUT -s "$ip" -j DROP
    echo "  Blocked: $ip"
    ((blocked++))
done < "$ip_file"

echo ""
echo "Done. $blocked IP(s) blocked."
```

Create a test IP list file:
```bash
cat > ~/scripts/test_attackers.txt << 'EOF'
# Test attacker IPs
10.0.0.1
10.0.0.2
# 10.0.0.3   (this one is commented out)
10.0.0.4
EOF
```

```bash
chmod +x ~/scripts/block_list.sh
sudo ./block_list.sh ~/scripts/test_attackers.txt

# Verify rules
sudo iptables -L INPUT -n | grep "10.0.0"

# Clean up test rules
sudo iptables -D INPUT -s 10.0.0.1 -j DROP
sudo iptables -D INPUT -s 10.0.0.2 -j DROP
sudo iptables -D INPUT -s 10.0.0.4 -j DROP
```

---

### 🧠 Quiz — Test Yourself

1. Where on a Linux server can you see real-time SSH failed login attempts?
2. What `iptables` command blocks all packets from `192.168.1.55`?
3. What does `-I INPUT` do vs `-A INPUT`?
4. What is the difference between `DROP` and `REJECT` in iptables?
5. Why is `DROP` preferred over `REJECT` when blocking attackers?
6. What does `-s` mean in the iptables command?
7. In Version 1, how does the script receive the IP to block?
8. In Version 2, how does the script receive the IP to block?
9. Which version (1 or 2) is better suited for automation? Why?
10. How do you **undo** a DROP rule added with `-I`?
11. How do you list all current rules in the INPUT chain with line numbers?
12. What is the purpose of `sleep 1` in the script?
13. Why must `iptables` commands be run as root?
14. How would you use the argument-based script to block an entire `/24` subnet?

---

## 📎 Quick Reference Card

```
THE CORE IPTABLES BLOCK COMMAND
──────────────────────────────────────────────────────────────
sudo iptables -I INPUT -s <IP> -j DROP

  -I INPUT    Insert at TOP of INPUT chain (high priority)
  -A INPUT    Append to BOTTOM of INPUT chain (use -I instead for blocking)
  -s <IP>     Match by Source IP, network (CIDR), or domain
  -j DROP     Silently discard the packet
  -j REJECT   Discard + send error back to sender

MANAGING IPTABLES RULES
──────────────────────────────────────────────────────────────
sudo iptables -L INPUT -n -v --line-numbers   View rules with line numbers
sudo iptables -D INPUT -s <IP> -j DROP        Remove specific rule
sudo iptables -D INPUT <line_number>          Remove rule by line number
sudo iptables -F INPUT                        Flush ALL INPUT rules (caution!)
sudo iptables-save > /etc/iptables/rules.v4   Save rules to persist on reboot

VERSION 1 — INTERACTIVE (read)
──────────────────────────────────────────────────────────────
read -rp "Enter IP to block: " ip
iptables -I INPUT -s "$ip" -j DROP

When to use: Manual incident response, human operator present

VERSION 2 — ARGUMENT ($1)
──────────────────────────────────────────────────────────────
iptables -I INPUT -s "$1" -j DROP
./dropPackets_arg.sh 192.168.1.55

When to use: Automation, cron jobs, called from other scripts

ROOT CHECK PATTERN (use in any script needing root)
──────────────────────────────────────────────────────────────
if [[ "$EUID" -ne 0 ]]; then
    echo "Error: Run as root: sudo $0"
    exit 1
fi

VIEWING ATTACK LOGS IN REAL TIME
──────────────────────────────────────────────────────────────
sudo tail -f /var/log/auth.log | grep -i "failed\|invalid"
grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -rn
```

---

## 🗂️ Notion Tags (suggested)

`bash` `project` `iptables` `security` `firewall` `brute-force` `sysadmin` `read` `positional-parameters` `ZTM` `module-2` `section-02`

---

## 🏆 Project Complete!

> You've built your **first real-world security tool** in Bash — a script that protects a Linux server from attackers by blocking their IP with a single command. This is the kind of tool that sysadmins and DevOps engineers actually use.

*← Previous: Module 2 | Section 02 — Positional Parameters*
*→ Next Episode: Module 2 | Section 02 — Special Parameters*
