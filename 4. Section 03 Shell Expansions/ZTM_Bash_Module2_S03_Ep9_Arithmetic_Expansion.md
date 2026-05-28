# 🐚 ZeroToMastery — Bash Scripting: Learn Shell Scripting (2025)

> **Course:** ZeroToMastery - Bash Scripting Learn Shell Scripting (2025-4)
> **Module:** 2. Section 03 — Shell Expansions
> **Episode:** 9. Arithmetic Expansion
> **Instructor:** Andrei Dumitrescu

---

## 📌 Episode Overview

Arithmetic expansion is the fifth expansion in Bash's pipeline — it allows you to **evaluate mathematical expressions** directly inside the shell and substitute the result. This episode covers the `$((...))` syntax, all arithmetic operators, two important limitations (integers only, no overflow protection), the `let` built-in, and how to handle **decimal/floating-point arithmetic** using the `bc` command.

---

## 🧠 Theory

### 1. What is Arithmetic Expansion?

Arithmetic expansion **evaluates a mathematical expression** and replaces it with the result — all inside the shell, no external tools needed for integers.

```bash
x=$((7 * 9))
echo $x       # → 63
```

**Syntax:**
```
$((expression))
   ↑↑          ↑↑
   double parentheses — required
```

> 📌 Note the double parentheses `((...))` — not to be confused with command substitution `$(...)` (single parentheses).

---

### 2. All Arithmetic Operators

| Operator | Operation | Example | Result |
|---|---|---|---|
| `+` | Addition | `$((5 + 3))` | `8` |
| `-` | Subtraction | `$((10 - 4))` | `6` |
| `*` | Multiplication | `$((6 * 7))` | `42` |
| `/` | Division (integer) | `$((17 / 5))` | `3` (truncated) |
| `%` | Modulo (remainder) | `$((17 % 5))` | `2` |
| `**` | Exponentiation | `$((2 ** 8))` | `256` |
| `++var` | Pre-increment | `$((++x))` | x+1, then use |
| `var++` | Post-increment | `$((x++))` | use x, then x+1 |
| `--var` | Pre-decrement | `$((--x))` | x-1, then use |
| `var--` | Post-decrement | `$((x--))` | use x, then x-1 |

> 📌 **Operator precedence** follows standard mathematical rules — same as in C, Python, or any other language. Use parentheses to enforce order when needed.

```bash
echo $((2 + 3 * 4))      # → 14  (* before +)
echo $(((2 + 3) * 4))    # → 20  (parentheses first)
```

---

### 3. Variables Inside `$((...))`

Inside arithmetic expansion, variable names are used **without the `$`** prefix — Bash knows they're variables from context:

```bash
x=5
y=3

echo $((x + y))       # → 8  (no $ needed inside ((...)))
echo $((x * y))       # → 15
echo $((x ** y))      # → 125  (5 to the power of 3)

# But $ also works inside (you'll see both styles):
echo $(($x + $y))     # → 8  (also valid)
```

> ✅ The style without `$` (`$((x + y))`) is cleaner and preferred inside arithmetic context.

---

### 4. Storing Arithmetic Results in Variables

```bash
# Basic assignment
result=$((7 * 9))
echo $result      # → 63

# More complex
x=10
y=3
sum=$((x + y))
diff=$((x - y))
product=$((x * y))
quotient=$((x / y))
remainder=$((x % y))
power=$((x ** y))

echo "Sum      : $sum"
echo "Diff     : $diff"
echo "Product  : $product"
echo "Quotient : $quotient"
echo "Remainder: $remainder"
echo "Power    : $power"
```

---

### 5. The `let` Built-in — Alternative Syntax

The `let` command is another way to evaluate arithmetic expressions — it's a Bash built-in:

```bash
let y=2**8
echo $y       # → 256

let "y = 2 ** 8"    # quotes allow spaces
echo $y             # → 256

# Increment a counter
counter=0
let counter++
let counter++
echo $counter       # → 2

# let returns exit status 0 (true) for non-zero result, 1 (false) for zero
let "5 > 3"    # exit status 0 (true — 5 is greater than 3)
let "3 > 5"    # exit status 1 (false)
```

> 💡 The `((...))` form (without the leading `$`) can also be used for arithmetic without capturing the result — it's used for conditionals and loops:
> ```bash
> x=5
> ((x > 3)) && echo "x is greater than 3"
> ((x++))   # increment x in place
> ```

---

### 6. ⚠️ Two Important Limitations

#### Limitation 1 — Integer Arithmetic Only

Bash arithmetic **only works with integers**. Decimal/floating-point numbers are truncated:

```bash
echo $((10 / 3))     # → 3   (NOT 3.333... — decimal part lost!)
echo $((7 / 2))      # → 3   (NOT 3.5)
echo $((1 / 4))      # → 0   (NOT 0.25)
```

> ⚠️ Division always truncates towards zero — there is no rounding.

#### Limitation 2 — No Overflow Protection

Bash arithmetic uses 64-bit signed integers. The maximum value is `2^63 - 1 = 9,223,372,036,854,775,807`. Exceeding it causes **silent overflow** — no error, just a wrong result:

```bash
let y=2**63        # maximum safe value territory
echo $y            # → works

let y=2**128       # overflow!
echo $y            # → 0  (wrong! no error shown)
```

> ⚠️ **Overflow is silent** — Bash does not warn you. The result is simply wrong. For large number arithmetic, use `bc` or Python.

#### Division by Zero — IS flagged as an error:
```bash
echo $((10 / 0))   # → bash: division by zero  ← error shown
```

---

### 7. Floating-Point Arithmetic — Using `bc`

Since Bash can't handle decimals natively, use **`bc`** (Basic Calculator) — a command-line arbitrary precision calculator.

#### Method 1 — Pipe a string to `bc`:
```bash
echo "3 * 7" | bc      # → 21
echo "11 / 4" | bc     # → 2  (no decimal yet)
```

> ⚠️ Without specifying `scale`, `bc` returns integers by default even for division.

#### Method 2 — Set `scale` for decimal places:
```bash
# scale = number of decimal digits after the decimal point
echo "scale=2; 11 / 4" | bc        # → 2.75
echo "scale=4; 23 / 7" | bc        # → 3.2857
echo "scale=10; 22 / 7" | bc       # → 3.1428571428  (pi approximation)
```

#### Method 3 — Here string `<<<` (clean, no pipe needed):
```bash
bc <<< "scale=3; 23 / 7"           # → 3.285
bc <<< "scale=2; 1.5 * 2.5"        # → 3.75
bc <<< "sqrt(2)"                   # → 1  (need scale for decimals)
bc <<< "scale=6; sqrt(2)"          # → 1.414213
```

> 💡 The `<<<` operator is called a **here string** — it feeds a string directly to a command's standard input, avoiding a pipe.

#### Method 4 — Interactive mode:
```bash
bc          # enter interactive mode — type expressions directly
# 5 + 3
# → 8
# quit     ← to exit
```

---

### 8. `bc` Useful Functions

```bash
# Square root
bc <<< "scale=4; sqrt(16)"        # → 4.0000
bc <<< "scale=4; sqrt(2)"         # → 1.4142

# Power
bc <<< "2^10"                      # → 1024
bc <<< "scale=4; 2.5^3"           # → 15.6250

# Trigonometry (requires -l math library)
bc -l <<< "s(1)"                   # sine of 1 radian
bc -l <<< "c(0)"                   # cosine of 0 = 1
```

---

### 9. Combining Arithmetic Expansion with Command Substitution

Store `bc` results in variables using command substitution:

```bash
result=$(echo "scale=2; 10 / 3" | bc)
echo "10 / 3 = $result"     # → 10 / 3 = 3.33

# Circle area: π * r²
radius=5
area=$(bc <<< "scale=4; 3.14159 * $radius * $radius")
echo "Area of circle (r=$radius): $area"    # → 78.5397

# Percentage calculation
used=75
total=120
pct=$(bc <<< "scale=1; $used * 100 / $total")
echo "Usage: $pct%"          # → 62.5%
```

---

## 🔑 Key Takeaways

- Arithmetic expansion syntax: **`$((expression))`** — double parentheses.
- Supports: `+`, `-`, `*`, `/`, `%` (modulo), `**` (power), `++`, `--`.
- Variables inside `((...))` don't need `$` — `$((x + y))` not `$(($x + $y))`.
- **Integer arithmetic only** — division truncates, no rounding.
- **No overflow protection** — large results silently wrap to wrong values.
- Division by zero is the one error that IS caught.
- `let` is an alternative built-in: `let y=2**8`.
- `((...))` without `$` is used for arithmetic in conditions and loops.
- For **floating-point**, use `bc` (Basic Calculator).
- `echo "scale=2; 11/4" | bc` → `2.75` — `scale` sets decimal places.
- **Here string** `<<<` feeds a string directly to `bc`: `bc <<< "scale=2; 22/7"`.
- Combine `bc` with command substitution to store decimal results: `result=$(bc <<< "scale=2; ...")`.

---

## 💡 Tips & Tricks

**🔹 Use `$((...))` for counters and loop calculations:**
```bash
total=0
for i in {1..10}; do
    total=$((total + i))
done
echo "Sum 1-10: $total"    # → 55
```

**🔹 Quick modulo check (is a number even?):**
```bash
n=42
if (( n % 2 == 0 )); then
    echo "$n is even"
else
    echo "$n is odd"
fi
```

**🔹 Increment a counter in a script:**
```bash
count=0
((count++))      # increment by 1
((count += 5))   # increment by 5
((count *= 2))   # double it
```

**🔹 Percentage calculation with `bc`:**
```bash
pct=$(bc <<< "scale=2; 73 * 100 / 200")
echo "Percentage: $pct%"    # → 36.50%
```

**🔹 `bc` with `-l` for math functions:**
```bash
bc -l <<< "scale=6; 4*a(1)"    # → 3.141592  (π — 4 * arctan(1))
```

**🔹 Avoid integer division truncation — multiply first:**
```bash
# WRONG — truncates before scaling
echo $((1 / 3 * 100))    # → 0  (1/3 = 0 in integer math!)

# RIGHT — multiply first to preserve precision
echo $((100 / 3))        # → 33  (still integer but better)

# BEST — use bc for true percentage
echo "scale=2; 1/3 * 100" | bc   # → 33.33
```

**🔹 Check if `bc` is installed:**
```bash
which bc || echo "Install bc: sudo apt install bc"
```

---

## 🏋️ Practice Exercises

---

### Exercise 1 — Basic Arithmetic Expansion

**Goal:** Get comfortable with all arithmetic operators.

```bash
# Addition, subtraction, multiplication
echo $((10 + 5))
echo $((20 - 7))
echo $((6 * 8))

# Division (observe truncation!)
echo $((17 / 5))      # what do you get?
echo $((17 % 5))      # what is the remainder?

# Exponentiation
echo $((2 ** 8))      # 2 to the power of 8
echo $((7 ** 9))      # 7 to the power of 9

# Order of operations
echo $((2 + 3 * 4))      # → 14  (not 20!)
echo $(((2 + 3) * 4))    # → 20  (parentheses first)
```

---

### Exercise 2 — Variables in Arithmetic

**Goal:** Use variables inside `$((...))`.

```bash
x=15
y=4

echo "x = $x, y = $y"
echo "x + y = $((x + y))"
echo "x - y = $((x - y))"
echo "x * y = $((x * y))"
echo "x / y = $((x / y))"    # integer division!
echo "x % y = $((x % y))"    # remainder
echo "x ** y = $((x ** y))"

# Modify a variable using arithmetic
x=$((x + 10))
echo "x after +10: $x"

# Shorthand with ((...))
((x += 5))
echo "x after +=5: $x"
```

---

### Exercise 3 — Witness the Limitations

**Goal:** See integer truncation and overflow in action.

```bash
echo "=== Integer Division Truncation ==="
echo "10 / 3 = $((10 / 3))"       # → 3 (not 3.333)
echo "7 / 2  = $((7 / 2))"        # → 3 (not 3.5)
echo "1 / 4  = $((1 / 4))"        # → 0 (not 0.25)

echo ""
echo "=== Overflow (silent!) ==="
let y=2**62
echo "2^62 = $y"                   # works fine
let y=2**63
echo "2^63 = $y"                   # may overflow
let y=2**128
echo "2^128 = $y"                  # → 0 (silent overflow!)

echo ""
echo "=== Division by Zero (error) ==="
echo $((10 / 0)) 2>&1 || echo "Caught: division by zero error"
```

---

### Exercise 4 — The `let` Built-in

**Goal:** Practice using `let` as an alternative to `$((...))`.

```bash
# Basic let usage
let a=5+3
echo "a = $a"

let b=2**10
echo "b = $b"

# let with spaces (needs quotes)
let "c = 10 * 5"
echo "c = $c"

# Increment with let
counter=0
let counter++
let counter++
let counter++
echo "counter = $counter"

# let for logical test (exit status)
let "5 > 3"
echo "5 > 3 exit status: $?"    # → 0 (true)

let "3 > 5"
echo "3 > 5 exit status: $?"    # → 1 (false)
```

---

### Exercise 5 — Floating-Point with `bc`

**Goal:** Use `bc` for decimal arithmetic.

```bash
echo "=== Basic bc ==="
echo "3 * 7" | bc
echo "11 / 4" | bc             # → 2 (no scale yet!)

echo ""
echo "=== With scale ==="
echo "scale=2; 11 / 4" | bc    # → 2.75
echo "scale=4; 22 / 7" | bc    # → 3.1428

echo ""
echo "=== Here string <<< ==="
bc <<< "scale=3; 23 / 7"
bc <<< "scale=6; sqrt(2)"

echo ""
echo "=== Store in variable ==="
pi=$(bc <<< "scale=10; 4*a(1)" 2>/dev/null || bc <<< "scale=10; 22/7")
echo "Pi ≈ $pi"

result=$(echo "scale=2; 100 / 3" | bc)
echo "100 / 3 = $result%"
```

---

### Exercise 6 — Build a Calculator Script

**Goal:** Write a script that does both integer and floating-point calculations.

Create `~/scripts/calculator.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : calculator.sh
# Purpose : Demonstrate integer and floating-point math
# Usage   : ./calculator.sh
# ─────────────────────────────────────────────────────

echo "=============================================="
echo "       Bash Arithmetic Calculator"
echo "=============================================="

# Get input
read -rp "Enter first number  : " a
read -rp "Enter second number : " b

echo ""
echo "── Integer Arithmetic (Bash \$((...))):──────"
echo "  $a + $b = $(( a + b ))"
echo "  $a - $b = $(( a - b ))"
echo "  $a * $b = $(( a * b ))"
echo "  $a / $b = $(( a / b )) (integer division)"
echo "  $a % $b = $(( a % b )) (remainder)"
echo "  $a ^ $b = $(( a ** b ))"

echo ""
echo "── Floating-Point Arithmetic (bc): ─────────"
echo "  $a + $b = $(bc <<< "scale=4; $a + $b")"
echo "  $a - $b = $(bc <<< "scale=4; $a - $b")"
echo "  $a * $b = $(bc <<< "scale=4; $a * $b")"
echo "  $a / $b = $(bc <<< "scale=4; $a / $b")"

echo ""
echo "── Special: ─────────────────────────────────"
echo "  sqrt($a) = $(bc <<< "scale=4; sqrt($a)")"
echo "  $a%  of $b = $(bc <<< "scale=2; $a * $b / 100")"
echo "=============================================="
```

```bash
chmod +x ~/scripts/calculator.sh
./calculator.sh
```

---

### Exercise 7 — Disk Usage Percentage Script

**Goal:** Apply arithmetic to a real system monitoring use case.

Create `~/scripts/disk_alert.sh`:

```bash
#!/bin/bash
# ─────────────────────────────────────────────────────
# Script  : disk_alert.sh
# Purpose : Check disk usage and alert if above threshold
# Usage   : ./disk_alert.sh [threshold_percent]
# ─────────────────────────────────────────────────────

threshold="${1:-80}"

echo "Disk Usage Report — $(date +'%F %T')"
echo "Alert threshold: ${threshold}%"
echo ""

# Get disk usage for each filesystem
df -h | tail -n +2 | while IFS= read -r line; do
    # Extract the use percentage (strip the % sign)
    pct=$(echo "$line" | awk '{print $5}' | tr -d '%')
    mount=$(echo "$line" | awk '{print $6}')
    size=$(echo "$line" | awk '{print $2}')
    used=$(echo "$line" | awk '{print $3}')

    # Skip if pct is not a number
    [[ "$pct" =~ ^[0-9]+$ ]] || continue

    # Calculate remaining using arithmetic expansion
    remaining=$((100 - pct))

    status="OK"
    if (( pct >= threshold )); then
        status="⚠️  ALERT"
    fi

    printf "  %-20s %3d%% used (%s remaining) %s\n" \
        "$mount" "$pct" "${remaining}%" "$status"
done
```

```bash
chmod +x ~/scripts/disk_alert.sh

# Default 80% threshold
./disk_alert.sh

# Custom threshold
./disk_alert.sh 50
```

---

### 🧠 Quiz — Test Yourself

1. What is the syntax for arithmetic expansion in Bash?
2. What is the difference between `$(...)` and `$((...))` ?
3. What does `$((17 / 5))` return? Why?
4. What does `$((17 % 5))` return?
5. What operator is used for exponentiation?
6. Do variables inside `$((...))` need a `$` prefix?
7. What is the `let` built-in and how do you use it?
8. What are the two main limitations of Bash arithmetic?
9. What happens when you compute `$((2 ** 128))`?
10. Is division by zero caught as an error?
11. What tool do you use for floating-point arithmetic in Bash?
12. What does `scale` control in `bc`?
13. Write the command to calculate `22/7` to 4 decimal places using `bc`.
14. What is a **here string** (`<<<`) and how is it used with `bc`?

---

## 📎 Quick Reference Card

```
ARITHMETIC EXPANSION
──────────────────────────────────────────────────────────────
$((expression))     Evaluate and substitute result
((expression))      Evaluate only (no substitution — for tests/loops)
let var=expression  Alternative built-in syntax

OPERATORS
──────────────────────────────────────────────────────────────
+    Addition           $((5 + 3))   → 8
-    Subtraction        $((10 - 4))  → 6
*    Multiplication     $((6 * 7))   → 42
/    Division (int)     $((17 / 5))  → 3  (truncates!)
%    Modulo/Remainder   $((17 % 5))  → 2
**   Exponentiation     $((2 ** 8))  → 256
++   Increment          ((count++))
--   Decrement          ((count--))
+=   Add and assign     ((x += 5))
*=   Multiply and assign((x *= 2))

LIMITATIONS
──────────────────────────────────────────────────────────────
✅  Integer arithmetic only — division truncates (no .5)
⚠️  No overflow protection — silent wrong results on large numbers
✅  Division by zero IS caught as an error

FLOATING-POINT WITH bc
──────────────────────────────────────────────────────────────
echo "expression" | bc            Basic (no decimals by default)
echo "scale=N; expr" | bc         N decimal places
bc <<< "scale=N; expression"      Here string (cleaner)
bc -l <<< "expression"            Math library (sqrt, trig, etc.)
result=$(bc <<< "scale=2; ...")   Store result in variable

COMMON bc EXAMPLES
──────────────────────────────────────────────────────────────
bc <<< "scale=2; 11/4"           → 2.75
bc <<< "scale=4; 22/7"           → 3.1428
bc <<< "scale=6; sqrt(2)"        → 1.414213
bc -l <<< "scale=10; 4*a(1)"     → 3.1415926535  (π)

HERE STRING
──────────────────────────────────────────────────────────────
command <<< "string"   Feed string directly to command's stdin
bc <<< "5 + 3"         → 8  (equivalent to: echo "5+3" | bc)
```

---

## 🗂️ Notion Tags (suggested)

`bash` `arithmetic-expansion` `math` `bc` `integers` `floating-point` `shell-expansions` `let` `ZTM` `module-2` `section-03`

---

*← Previous: Module 2 | Section 03 — Command Substitution*
*→ Next Episode: Module 2 | Section 03 — Word Splitting*
