---
tags:
  - foundations
  - python
  - operators
  - expressions
  - arithmetic
stage: 1
difficulty: Beginner
---

# Operators and Expressions

**Prev:** [[05 - Boolean and None]] | **Next:** [[07 - Comparisons and Logical Operators]]

> Operators act on values; expressions combine operators and operands to produce new values. Arithmetic and assignment operators are the arithmetic engine of every Python program — and knowing whether you are writing an *expression* or a *statement* changes how you read and debug code.

---

## Why this matters

Before `if`, loops, or functions, programs still need to **compute** and **store** values. `total = price * quantity + tax` is three ideas at once: multiplication, addition, and assignment. If you misunderstand `/` vs `//`, precedence, or what `+=` actually does, bugs show up as wrong totals, off-by-one errors, or silent list mutations.

This note covers only **arithmetic operators**, **assignment operators**, **precedence**, and the **expression vs statement** distinction — the foundation layer everything else builds on.

---

## Expression vs statement

Python code is built from two kinds of building blocks. Confusing them is one of the most common beginner mistakes.

### Expression — produces a value

An **expression** is anything Python can **evaluate** to a single value. Expressions can be nested inside other expressions.

```python
3 + 4                    # 7
len("Python")            # 6
(10 + 5) * 2             # 30
"Hello " + "world"       # 'Hello world'
2 ** 3 ** 2              # 512  (right-associative — see Precedence)
```

You can often place an expression anywhere a value is expected:

```python
print(3 + 4)             # expression inside a function call
x = 10 * 2               # expression on the right of =
y = (x + 5) * 3          # expressions inside a larger expression
```

In the **REPL**, if you type an expression alone, Python prints its value:

```python
>>> 10 + 5
15
```

### Statement — performs an action

A **statement** is a complete line (or block) of instruction. It **does something** — assign, print, define, loop — but is **not** itself a value you can plug into another expression.

```python
x = 10                   # assignment statement
print(x)                 # print statement
import math              # import statement
if x > 5: pass           # if statement
```

These do **not** make sense as nested values:

```python
# x = (x = 10)           # SyntaxError — assignment is not an expression
# y = print(5)           # legal, but print returns None — different intent
```

### Side-by-side comparison

| | Expression | Statement |
|---|------------|-----------|
| **Produces a value?** | Yes — always evaluates to something | No — performs an action |
| **Can nest inside `()`?** | Yes — `2 * (3 + 4)` | No — `2 * (x = 5)` is invalid |
| **REPL shows result?** | Yes, when typed alone | Usually no visible value (`print` outputs text, but that's side effect) |
| **Examples** | `3 + 4`, `"a" * 3`, `2 ** 10` | `x = 3`, `print(x)`, `pass` |

### The gray area: assignment and augmented assignment

```python
x = 3 + 4        # whole line is a *statement*
                 # but `3 + 4` on the right is an *expression*
```

The **right-hand side** is always an expression. Python evaluates it first, then the assignment statement stores the result.

```python
x = 10
x += 5           # statement — shorthand for x = x + 5 (with one subtle difference for mutables; see Assignment)
```

**Rule of thumb:** If you can imagine it inside parentheses as a sub-calculation — `(3 + 4)`, `(price * 0.2)` — it is an expression. If it is a top-level instruction that changes state or controls flow — `x = ...`, `print(...)`, `while ...` — it is a statement.

---

## Arithmetic operators

Arithmetic operators take numeric operands (and in some cases sequences) and return a result.

| Operator | Name | Example | Result |
|----------|------|---------|--------|
| `+` | Addition | `10 + 3` | `13` |
| `-` | Subtraction | `10 - 3` | `7` |
| `*` | Multiplication | `10 * 3` | `30` |
| `/` | True division | `10 / 3` | `3.333...` |
| `//` | Floor division | `10 // 3` | `3` |
| `%` | Modulus (remainder) | `10 % 3` | `1` |
| `**` | Exponentiation | `2 ** 3` | `8` |

Operands are the values the operator works on (`10` and `3` above). The whole piece `10 + 3` is an **expression** whose value is `13`.

---

### Addition (`+`)

```python
print(10 + 3)    # 13
print(-5 + 12)   # 7
print(3.5 + 2.5) # 6.0  — int + float promotes to float
```

If either operand is a `float`, the result is a `float`.

`+` also **concatenates** sequences (not just numbers):

```python
print("Hello " + "world")   # Hello world
print([1, 2] + [3, 4])      # [1, 2, 3, 4]
```

You cannot add a number and a string — Python does not guess:

```python
# print("Score: " + 100)     # TypeError
print("Score: " + str(100))  # Score: 100  — convert first
```

---

### Subtraction (`-`)

```python
print(10 - 3)    # 7
print(3 - 10)    # -7
print(5.0 - 2)   # 3.0
```

Unary minus is also subtraction from zero:

```python
x = 5
print(-x)        # -5
```

---

### Multiplication (`*`)

```python
print(6 * 4)     # 24
print(2.5 * 4)   # 10.0
```

Sequence repetition:

```python
print("Ha" * 3)      # HaHaHa
print([0] * 4)       # [0, 0, 0, 0]
print("-" * 20)      # --------------------
```

The multiplier must be an integer when repeating sequences.

---

### Division (`/`)

In Python 3, `/` is **true division** — it always returns a **float**, even when the result is whole:

```python
print(10 / 2)    # 5.0   — not 5
print(10 / 3)    # 3.3333333333333335
print(type(10 / 2))  # <class 'float'>
```

This is deliberate. Python removed "integer division" from `/` to avoid silent truncation bugs. When you need whole-number division, use `//`.

---

### Floor division (`//`)

`//` divides and **rounds down** to the nearest integer (toward negative infinity):

```python
print(10 // 3)    # 3
print(17 // 5)    # 3
print(10 // 2)    # 5   — whole result, but type is int when both operands are int
print(10.0 // 3)  # 3.0 — if either operand is float, result is float
```

**Negative numbers** — floor means toward −∞, not toward zero:

```python
print(7 // 3)     # 2
print(-7 // 3)    # -3   — not -2  (floor of -2.333... is -3)
print(7 // -3)    # -3
```

Use `//` when you need "how many whole groups fit?" — pages of results, hours in a day chunk, grid cells.

---

### Modulus (`%`)

`%` returns the **remainder** after division:

```python
print(10 % 3)     # 1   — 10 = 3*3 + 1
print(17 % 5)     # 2
print(10 % 2)     # 0   — even number (no remainder)
print(11 % 2)     # 1   — odd number
```

**Mathematical relationship** (for integers `a`, `b` with `b != 0`):

```
a == (a // b) * b + (a % b)
```

```python
a, b = 17, 5
print((a // b) * b + (a % b))   # 17
```

Common uses:

- **Even/odd:** `n % 2 == 0`
- **Wrap-around:** clock arithmetic `hour % 12`
- **Cycling index:** `i % len(items)`

With negatives, `%` follows the same floor-division rule so the identity above always holds:

```python
print(-7 % 3)     # 2   — because -7 // 3 == -3, and -3*3 + 2 == -7
```

---

### Exponentiation (`**`)

Raises the left operand to the power of the right:

```python
print(2 ** 3)     # 8    — 2×2×2
print(5 ** 2)     # 25
print(10 ** 0)    # 1    — any number to power 0 is 1
print(2 ** -1)    # 0.5  — negative exponent = reciprocal
print(9 ** 0.5)   # 3.0  — square root
```

`**` is **right-associative** (evaluated right-to-left when chained):

```python
print(2 ** 3 ** 2)   # 512  — same as 2 ** (3 ** 2) = 2 ** 9
print((2 ** 3) ** 2) # 64   — parentheses change grouping
```

For large powers, integers can grow huge — Python integers have arbitrary precision:

```python
print(2 ** 100)   # 1267650600228229401496703205376
```

---

### `/` vs `//` vs `%` together

```python
a, b = 17, 5

print(a / b)    # 3.4   — exact quotient as float
print(a // b)   # 3     — whole groups
print(a % b)    # 2     — what's left over
```

| Question | Operator |
|----------|----------|
| "What is 17 divided by 5 as a decimal?" | `/` |
| "How many full 5s fit in 17?" | `//` |
| "What's left after taking full 5s?" | `%` |

---

## Assignment operators

Assignment **stores** a value in a name. Augmented assignment **updates** a name using an operation.

| Operator | Example | Equivalent to |
|----------|---------|---------------|
| `=` | `x = 10` | bind name `x` to value |
| `+=` | `x += 5` | `x = x + 5` |
| `-=` | `x -= 3` | `x = x - 3` |
| `*=` | `x *= 2` | `x = x * 2` |
| `/=` | `x /= 2` | `x = x / 2` |
| `//=` | `x //= 3` | `x = x // 3` |
| `**=` | `x **= 2` | `x = x ** 2` |

Every assignment line is a **statement**, not an expression.

---

### Basic assignment (`=`)

```python
x = 10
```

`=` does **not** copy the way you might expect for mutable objects — it **binds the name** to an object:

```python
a = [1, 2, 3]
b = a           # b points to the same list object as a
b.append(4)
print(a)        # [1, 2, 3, 4]  — both names, one list
```

For immutable values (`int`, `float`, `str`), rebinding one name does not affect another:

```python
x = 10
y = x
x = 20
print(y)        # 10  — y still points to 10
```

---

### Augmented assignment — what really happens

```python
count = 10
count += 5
print(count)    # 15
```

Python evaluates this as:

1. Look up the current value of `count` (`10`)
2. Evaluate the right-hand operation (`10 + 5` → `15`)
3. Bind `count` to the new value

For **immutable** types (`int`, `float`, `str`), `x += y` behaves identically to `x = x + y`:

```python
n = 10
n += 5
print(n)        # 15
```

---

### The mutable gotcha: `+=` on lists

For **mutable** types, augmented assignment can differ from `x = x + y`:

```python
# list +=  mutates in place
nums = [1, 2, 3]
other = nums
nums += [4, 5]          # same as nums.extend([4, 5])
print(nums)             # [1, 2, 3, 4, 5]
print(other)            # [1, 2, 3, 4, 5]  — same object

# list + list  creates a new list
nums = [1, 2, 3]
other = nums
nums = nums + [4, 5]    # new list object; nums rebinds
print(nums)             # [1, 2, 3, 4, 5]
print(other)            # [1, 2, 3]  — unchanged
```

For numbers, this distinction does not matter — `int` is immutable. For lists and similar types, know which form you are using.

---

### Each augmented operator

**`-=`** — subtract and rebind:

```python
x = 20
x -= 8
print(x)        # 12
```

**`*=`** — scale:

```python
x = 6
x *= 3
print(x)        # 18

s = "Hi "
s *= 3
print(s)        # Hi Hi Hi
```

**`/=`** — always produces float if true division applies:

```python
x = 10
x /= 4
print(x)        # 2.5
print(type(x))  # <class 'float'>
```

**`//=`** — floor-divide and assign:

```python
x = 17
x //= 5
print(x)        # 3
```

**`**=`** — raise to power and assign:

```python
x = 2
x **= 10
print(x)        # 1024
```

---

### Chained assignment

Python allows binding one value to multiple names in one statement:

```python
a = b = c = 0
print(a, b, c)  # 0 0 0
```

All three names point to the **same** object. With mutables, they share state:

```python
a = b = []
a.append(1)
print(b)        # [1]
```

Safer pattern for mutables:

```python
a = []
b = []
```

---

## Operator precedence

When an expression contains multiple operators, Python does not evaluate left-to-right blindly — it follows **precedence rules** (similar to PEMDAS in math, with Python-specific details).

### PEMDAS equivalent in Python

| Order | Operation | Operators | Notes |
|-------|-----------|-----------|-------|
| 1 | Parentheses | `(...)` | Always override — use liberally when unsure |
| 2 | Exponentiation | `**` | **Right-to-left**: `2**3**2` = `2**(3**2)` |
| 3 | Unary plus/minus | `+x`, `-x` | `-2**2` = `-(2**2)` = `-4`, not `4` |
| 4 | Multiply/divide/modulo/floor | `*`, `/`, `//`, `%` | **Left-to-right** at same level |
| 5 | Add/subtract | `+`, `-` | **Left-to-right** at same level |

PEMDAS says "Multiplication before Addition" — Python follows that. But Python has **no** "MD before AS" priority between `*`, `/`, `//`, `%` — they are equal and evaluated **left to right**.

---

### Step-by-step evaluations

```python
print(2 + 3 * 4)       # 14  — not 20
# 3 * 4 → 12, then 2 + 12 → 14

print((2 + 3) * 4)     # 20  — parentheses first

print(10 - 4 + 2)      # 8   — left to right: (10-4)+2, not 10-(4+2)
# 10 - 4 → 6, then 6 + 2 → 8

print(20 / 4 * 2)      # 10.0 — left to right: (20/4)*2, not 20/(4*2)
# 20 / 4 → 5.0, then 5.0 * 2 → 10.0
```

---

### Exponentiation traps

```python
print(-2 ** 2)         # -4  — unary minus after **: -(2**2)
print((-2) ** 2)       # 4   — base is -2

print(2 ** 3 ** 2)     # 512 — 2 ** (3 ** 2) = 2 ** 9
print(2 ** 2 ** 3)     # 256 — 2 ** (2 ** 3) = 2 ** 8
```

When in doubt, **add parentheses**. They make intent explicit and cost nothing.

---

### Mixed assignment and expressions

Assignment has **lower** precedence than arithmetic — the expression on the right is fully evaluated before storing:

```python
x = 2 + 3 * 4
print(x)        # 14  — computes 2 + (3 * 4) first, then assigns
```

```python
total = price * quantity + tax_rate * price * quantity
# evaluates as: (price * quantity) + (tax_rate * price * quantity)
# if you need different grouping, use parentheses
```

---

### Quick reference card

```python
# Parentheses win
result = (2 + 3) * 4          # 20

# ** before * /
result = 2 * 3 ** 2             # 18  — 2 * (3**2)

# * / // % before + -
result = 10 + 20 / 5            # 14.0 — 10 + (20/5)

# Left-to-right among equals
result = 100 / 10 / 2           # 5.0 — (100/10)/2
```

---

## Common mistakes / Gotchas

**Using `/` when you meant `//`.**

```python
pages = 95 / 10    # 9.5 — float, wrong for "how many full pages"
pages = 95 // 10   # 9   — correct for whole pages
```

**Assuming `%` is always "positive remainder."** With negative operands, sign follows the divisor rule — use the identity `a == (a // b) * b + (a % b)` to verify.

**Writing `2 + 3 * 4` and expecting `20.** Precedence applies; use `(2 + 3) * 4`.

**Treating `x += y` and `x = x + y` as always identical.** For lists, `+=` mutates in place; `+` creates a new list.

**Forgetting `/` returns float.**

```python
avg = (10 + 20) / 2   # 15.0, not 15
```

**Chained comparisons are not math shorthand** (covered in comparisons note) — `3 < x < 10` is valid Python but different from `(3 < x) < 10`.

---

## See also

- [[04 - Numbers and Math]] — `int`, `float`, and numeric types in depth
- [[02 - Variables and Data Types]] — how assignment binds names to objects
- [[05 - Boolean and None]] — truth values and `None`
- [[07 - Comparisons and Logical Operators]] — `==`, `<`, `and`, `or`, `not`
- [Python docs — Operator precedence](https://docs.python.org/3/reference/expressions.html#operator-precedence)

---

## → What's next

You can calculate values and assign them to names. The next layer is *asking questions* about those values — comparing them and combining answers with `and`, `or`, `is`, and `in` — in [[07 - Comparisons and Logical Operators]].
