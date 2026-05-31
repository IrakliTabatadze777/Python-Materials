---
tags:
  - foundations
  - python
  - loops
  - for
  - range
  - control-flow
stage: 1
difficulty: Beginner
---

# The `for` Loop

**Prev:** [[09 - While loop]] | **Next:** [[11 - break, continue, pass]]

> A `for` loop walks through an **iterable** — a list, string, `range`, file, or any object that yields items one at a time. Use it when you know *what* to iterate over, even if you do not know the exact count in advance.

---

## Why the `for` loop exists

Most real programs process **collections**: names in a list, characters in a string, lines in a file, rows in a table. The `for` loop expresses that directly:

```python
for fruit in fruits:
    print(fruit)
```

Read it as: **"for each fruit in fruits, do this."** Python handles advancing to the next item — you do not manually increment an index (unless you choose to).

Compared to `while`:

| | `while` | `for` |
|---|---------|-------|
| **Driven by** | Condition true/false | Items in a sequence |
| **Typical use** | "Until done" | "For each one" |
| **Risk** | Infinite loop if condition never falsy | Off-by-one with `range()` |

---

## Syntax

```python
for variable in iterable:
    # body — runs once per item
```

| Part | Role |
|------|------|
| `for` | Keyword starting the loop |
| `variable` | Name bound to each item in turn (loop variable) |
| `in` | Required keyword |
| `iterable` | Object Python can iterate over |
| `:` + indented body | Code run for every item |

The loop variable is **reassigned** each iteration — it is just a name pointing at the current item:

```python
fruits = ["apple", "banana"]
for fruit in fruits:
    print(fruit)
# apple
# banana
# fruit is now "banana" — last value after loop ends
```

---

## What is an iterable?

An **iterable** is any object you can loop over with `for`. Common built-in iterables:

| Iterable | Example |
|----------|---------|
| `list` | `[1, 2, 3]` |
| `str` | `"Python"` |
| `tuple` | `(1, 2, 3)` |
| `range` | `range(5)` |
| `dict` | iterates **keys** by default |
| `set` | unordered unique items |
| `file` | each line |

Python calls `iter(iterable)` internally and pulls items with `next()` until exhausted. You rarely write that yourself — `for` does it for you.

---

## Example 1: Looping over a list

```python
fruits = ["apple", "banana", "cherry", "mango"]

for fruit in fruits:
    print(f"I like {fruit}")
# I like apple
# I like banana
# I like cherry
# I like mango
```

The variable `fruit` is a **temporary name** for each element. It can be any valid identifier:

```python
for f in fruits:       # fine
for item in fruits:    # clearer in some contexts
for x in fruits:       # works but vague — prefer descriptive names
```

---

## Example 2: `range()` — numeric sequences

`range()` generates a sequence of integers — the most common partner for `for` when you need counting.

```python
for i in range(5):           # 0, 1, 2, 3, 4
    print(i)

for i in range(1, 6):        # 1, 2, 3, 4, 5
    print(i)

for i in range(0, 11, 2):    # 0, 2, 4, 6, 8, 10
    print(i)
```

### `range()` parameters

```python
range(stop)              # 0 up to stop-1
range(start, stop)       # start up to stop-1
range(start, stop, step) # start, start+step, ... until < stop (or > stop if step negative)
```

| Call | Values produced | Count |
|------|-----------------|-------|
| `range(5)` | 0, 1, 2, 3, 4 | 5 |
| `range(1, 6)` | 1, 2, 3, 4, 5 | 5 |
| `range(0, 10, 2)` | 0, 2, 4, 6, 8 | 5 |
| `range(10, 0, -1)` | 10, 9, …, 1 | 10 |

**Stop is exclusive** — `range(1, 6)` never includes `6`. Think: "stop before this number."

```python
# Print 1 to 10 inclusive
for i in range(1, 11):    # not range(1, 10)
    print(i)
```

**Off-by-one** is the most common `for` loop bug. When unsure, list what `range` produces:

```python
print(list(range(1, 6)))   # [1, 2, 3, 4, 5]
```

### `range()` vs `while` counter

These are equivalent:

```python
# for
for i in range(1, 6):
    print(i)

# while
i = 1
while i <= 5:
    print(i)
    i += 1
```

Prefer `for i in range(...)` when counting — less boilerplate, no forgotten increment.

---

## Example 3: Looping over a string

A string is an iterable of characters:

```python
for letter in "Python":
    print(letter)
# P
# y
# t
# h
# o
# n
```

Each iteration binds `letter` to a one-character string.

**With index** — when you need position and value:

```python
for i in range(len("Python")):
    print(i, "Python"[i])
# 0 P
# 1 y
# ...

# Better — use enumerate (introduced later):
for i, letter in enumerate("Python"):
    print(i, letter)
```

---

## Looping over a dictionary

`for` on a dict iterates **keys**:

```python
user = {"name": "Ada", "age": 36}

for key in user:
    print(key, user[key])
# name Ada
# age 36
```

Explicit alternatives:

```python
for key in user.keys():      # same as default
    ...

for value in user.values():
    ...

for key, value in user.items():
    print(key, value)
```

---

## How many times does the body run?

Exactly **once per item** in the iterable (unless `break`/`continue` — see [[11 - break, continue, pass]]):

```python
for _ in range(3):
    print("hello")
# hello
# hello
# hello
```

`_` is convention for "I need a loop variable but won't use it."

```python
for item in []:
    print(item)    # body never runs — empty iterable
```

---

## Nested loops

A **nested loop** is a loop inside another loop. For **each step** of the outer loop, the inner loop runs **completely from start to finish**.

```python
for i in range(1, 4):          # outer — i = 1, 2, 3
    for j in range(1, 4):      # inner  — j = 1, 2, 3 each time
        print(f"({i}, {j})", end=" ")
    print()
# (1, 1) (1, 2) (1, 3)
# (2, 1) (2, 2) (2, 3)
# (3, 1) (3, 2) (3, 3)
```

### Mental model: outer = rows, inner = columns

```
        j →  1      2      3
    i
    ↓
    1       (1,1)  (1,2)  (1,3)
    2       (2,1)  (2,2)  (2,3)
    3       (3,1)  (3,2)  (3,3)
```

- **Outer** (`i`) moves **slowly** — one row per step
- **Inner** (`j`) moves **quickly** — sweeps all columns before `i` changes

When `i` becomes `2`, `j` **resets to 1** — it does not continue from where it left off.

### Step-by-step (first row)

| Step | Outer `i` | Inner `j` | Output |
|------|-----------|-----------|--------|
| 1 | 1 | 1 | `(1, 1)` |
| 2 | 1 | 2 | `(1, 2)` |
| 3 | 1 | 3 | `(1, 3)` |
| 4 | — | inner done | newline |
| 5 | 2 | 1 | `(2, 1)` |
| … | … | … | … |

### Total iterations

**Outer count × inner count:**

| Outer | Inner per outer | Total inner-body runs |
|-------|-----------------|------------------------|
| 3 | 3 | **9** |

Three levels multiply again: 3 × 3 × 3 = 27.

### Mixed nesting (`while` + `for`)

```python
row = 1
while row <= 2:
    for col in range(1, 4):
        print(f"({row}, {col})", end=" ")
    print()
    row += 1
# (1, 1) (1, 2) (1, 3)
# (2, 1) (2, 2) (2, 3)
```

Same rule: inner completes fully on each outer step.

### When nested loops help

- 2D grids (rows × columns)
- Comparing every pair in a list
- Multiplication tables
- Nested data (users → orders → items)

**Tip:** Trace a 2×2 example on paper before scaling up.

---

## Common mistakes / Gotchas

**Off-by-one with `range()`.**

```python
for i in range(1, 10):   # 1..9, not 1..10
for i in range(1, 11):   # 1..10 ✓
```

**Modifying a list while iterating.**

```python
nums = [1, 2, 3, 4]
for n in nums:
    if n % 2 == 0:
        nums.remove(n)   # skips elements — unpredictable
```

Build a new list or iterate over a copy: `for n in nums[:]`.

**Reusing the loop variable after the loop** — it holds the last item:

```python
for x in [1, 2, 3]:
    pass
print(x)   # 3 — may surprise you
```

**Assuming dict iteration order for logic** — keys are ordered in Python 3.7+ but do not rely on insertion order for correctness unless intentional.

**Deep nesting** — beyond 2–3 levels, extract functions or use itertools.

---

## Best practices

- Use descriptive loop variables: `for user in users:` not `for u in users:`
- Prefer `for item in collection` over manual index when you do not need the index
- Use `range(len(x))` only when index is required; prefer `enumerate(x)`
- Keep loop bodies short — extract to a function if complex
- Use `_` when the loop variable is unused

---

## See also

- [[09 - While loop]] — condition-driven repetition when the count is unknown
- [[11 - break, continue, pass]] — stopping early, skipping items, and loop `else`
- [[03 - Strings]] — strings as iterables you walk character by character
- [[02 - Variables and Data Types]] — lists, tuples, and dicts as loop targets
- [[07 - Comparisons and Logical Operators]] — membership with `in` when filtering in a loop

---

## → What's next

You can walk through sequences and nested grids. Loops also need fine-grained control — stopping early, skipping items, and leaving placeholders — with [[11 - break, continue, pass]].
