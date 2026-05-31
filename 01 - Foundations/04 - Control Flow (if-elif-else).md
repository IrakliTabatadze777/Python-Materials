---
tags: [foundations, python, ontrol-flow]
stage: 1
difficulty: Beginner
---

# Control Flow: if, elif, else

**Prev:** [[03 - Operators and Expressions]] | **Next:** [[05 - Loops]]

> Control flow determines the path your program takes. The `if-elif-else` structure is Python’s primary way of making decisions, allowing your code to respond intelligently to different situations.

---

## Why Control Flow Matters

Without control flow, your programs would execute every line in order, every time. With `if` statements, your code can:

- React to user input
- Validate data
- Handle different scenarios
- Make logical decisions
- Implement business rules

Mastering control flow is essential for writing programs that behave intelligently.

## Boolean Logic – The Foundation

Before diving into `if`, you need to understand **Boolean** values and expressions.

### Boolean Values
Python has two boolean values: `True` and `False`.

```python
is_adult = True
is_logged_in = False
```

### Comparison Operators

| Operator | Meaning                  | Example             |
|----------|--------------------------|---------------------|
| `==`     | Equal to                 | `x == 5`            |
| `!=`     | Not equal to             | `x != 5`            |
| `>`      | Greater than             | `x > 10`            |
| `<`      | Less than                | `x < 10`            |
| `>=`     | Greater than or equal    | `x >= 18`           |
| `<=`     | Less than or equal       | `x <= 65`           |

### Logical Operators

| Operator | Meaning          | Example                        |
|----------|------------------|--------------------------------|
| `and`    | Both true        | `age >= 18 and has_ticket`     |
| `or`     | At least one     | `is_admin or is_moderator`     |
| `not`    | Opposite         | `not is_blocked`               |

---

## The `if` Statement

### Basic Syntax

```python
age = 20

if age >= 18:
    print("You are an adult.")   # You are an adult.
    print("You can vote.")       # You can vote.
```

**Important rules:**
- The condition must evaluate to `True` or `False`
- A colon `:` is required at the end of the `if` line
- Indentation (4 spaces) defines the block that runs if condition is true

### `if` + `else`

```python
age = 16

if age >= 18:
    print("You are an adult.")
else:
    print("You are a minor.")    # You are a minor.
```

**How `else` works:**

- Python evaluates the `if` condition first.
- If it is `True`, the indented block under `if` runs and Python **skips** the `else` block entirely.
- If it is `False`, Python **skips** the `if` block and runs the indented block under `else`.
- Exactly **one** of the two blocks runs — never both, never neither.

With `age = 16`: `16 >= 18` is `False`, so `"You are a minor."` is printed.

### `if` + `elif` + `else`

```python
score = 85

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
elif score >= 60:
    grade = "D"
else:
    grade = "F"

print(f"Your grade is: {grade}")   # Your grade is: B
```

**How `elif` and `else` work together:**

`elif` is short for **"else if"**. Python treats the whole chain as a single decision and checks conditions **from top to bottom**, stopping at the **first** one that is `True`.

| Step | Condition checked | Result for `score = 85` | Action |
|------|-------------------|-------------------------|--------|
| 1    | `score >= 90`     | `False` (85 is not ≥ 90) | Continue to next `elif` |
| 2    | `score >= 80`     | `True` (85 ≥ 80)         | Run this block → `grade = "B"`, **stop** |
| 3–5  | remaining checks  | **not evaluated**        | Skipped because step 2 already matched |
| `else` | (fallback)      | **not reached**          | Only runs when every condition above was `False` |

Key rules:

1. **First match wins** — once a condition is `True`, Python runs that block and ignores every `elif` and `else` below it.
2. **`elif` is only checked when all conditions above it were `False`** — it is not a separate, independent `if`.
3. **`else` is optional** — it runs only when **no** `if` or `elif` condition was `True`. Think of it as the default / catch-all.
4. **Order matters** — put more specific or stricter conditions first. If you reversed the chain (`>= 60` before `>= 90`), almost every score would match the first `elif` and never reach the higher grades.

**Walkthrough with different scores:**

```python
# score = 85  →  checks fail at >= 90, pass at >= 80  →  grade = "B"
# score = 92  →  passes at >= 90 immediately           →  grade = "A" (>= 80 is never checked)
# score = 55  →  all if/elif fail                      →  else runs → grade = "F"
```

**Note:** Only the body of the matching branch runs. In the example above, when `score = 85`, Python assigns `"B"` once and does not re-enter any other branch.

---

## Deep Dive: Truthy and Falsy Values

Python evaluates non-boolean values as `True` or `False` in conditions:

### Falsy values (evaluated as False):
- `False`
- `0` (integer)
- `0.0` (float)
- `""` (empty string)
- `[]` (empty list)
- `{}` (empty dict)
- `None`

### Everything else is Truthy.

```python
name = input("Enter your name: ")

if name:                    # Truthy if name is not empty
    print(f"Hello, {name}!")
else:
    print("You didn't enter a name.")
```

---

## Nested if Statements

A **nested `if`** is an `if` statement placed **inside** another `if` (or `elif` / `else`) block. The inner `if` only runs when the outer condition is already `True` — it cannot be reached otherwise.

```python
age = 25
has_license = True

if age >= 18:
    print("You are old enough to drive.")       # You are old enough to drive.
    if has_license:
        print("You can drive on the road.")       # You can drive on the road.
    else:
        print("You need to get a license first.")
else:
    print("You are too young to drive.")
```

### How the layers work

Think of nesting as **decisions inside decisions**. Each indentation level is its own mini program:

```
Outer if:  age >= 18?
    ├─ False → run outer else → "too young" (inner if never runs)
    └─ True  → enter outer block
           Inner if: has_license?
               ├─ True  → "can drive on the road"
               └─ False → "need to get a license first"
```

**Critical rule:** The inner `else` belongs to the **inner** `if` only. The outer `else` belongs to the **outer** `if` only. Python matches each `else` to the nearest unmatched `if` at the same indentation level.

### Step-by-step with `age = 25`, `has_license = True`

| Step | What Python checks | Result | What runs |
|------|--------------------|--------|-----------|
| 1    | `age >= 18`        | `True` | Enter outer block |
| 2    | print first message | —   | `"You are old enough to drive."` |
| 3    | `has_license`      | `True` | Enter inner block |
| 4    | print second message | —  | `"You can drive on the road."` |
| —    | inner `else`       | skipped | Inner condition was `True` |
| —    | outer `else`       | skipped | Outer condition was `True` |

### All four combinations

| `age >= 18` | `has_license` | Output |
|-------------|---------------|--------|
| `False` (e.g. 15) | `True` or `False` | `"You are too young to drive."` — inner `if` is never evaluated |
| `True` (e.g. 25) | `True` | `"You are old enough to drive."` then `"You can drive on the road."` |
| `True` (e.g. 25) | `False` | `"You are old enough to drive."` then `"You need to get a license first."` |

Notice: when age is too young, **`has_license` does not matter**. Python never asks about the license because the outer condition already failed.

### Nesting vs `and`

The same logic can often be written flat with `and`:

```python
age = 25
has_license = True

if age >= 18 and has_license:
    print("You are old enough to drive.")
    print("You can drive on the road.")
elif age >= 18:
    print("You are old enough to drive.")
    print("You need to get a license first.")
else:
    print("You are too young to drive.")
```

| Approach | Best when |
|----------|-----------|
| **Nested `if`** | Inner check only makes sense after outer check passes (e.g. license only relevant if old enough) |
| **`and` / `elif` chain** | Conditions are independent and you want fewer indentation levels |

Both are correct. Nesting reads like a conversation ("if old enough, *then* check license"); flat `and` reads like a checklist.

### Why deep nesting becomes a problem

Each extra level adds mental load — you must track which `else` pairs with which `if`. Beyond 2–3 levels, code gets hard to read and test.

**Prefer:**

- **`and` / `or`** to combine related conditions on one line
- **`elif`** instead of `else: if ...`
- **Early exit** (in functions) — return or raise as soon as a guard fails:

```python
def can_drive(age: int, has_license: bool) -> str:
    if age < 18:
        return "You are too young to drive."
    if not has_license:
        return "You need to get a license first."
    return "You can drive on the road."
```

Each condition stands alone. No pyramid of indentation.

**Tip:** One level of nesting (like the driving example) is fine. If you find yourself scrolling sideways to follow `if` blocks, flatten the logic.

---

## Conditional Expressions (Ternary Operator)

A compact way to write a simple **two-way choice that produces a value**. It is called "ternary" because it takes three parts:

```python
value_if_true if condition else value_if_false
```

```python
age = 20
status = "Adult" if age >= 18 else "Minor"
print(status)   # Adult
```

**Same logic as a regular `if-else`:**

```python
if age >= 18:
    status = "Adult"
else:
    status = "Minor"
```

The ternary form is an **expression** — it evaluates to a single value you can assign, pass to a function, or return. A normal `if-else` block is a **statement** and cannot sit inline on one line like this.

**Another example:**

```python
score = 55
result = "Pass" if score >= 60 else "Fail"
print(result)   # Fail
```

| Use it when | Avoid it when |
|-------------|---------------|
| Both branches are short, simple values | Either branch has multiple lines or side effects (`print`, loops) |
| You need a value in one expression | You need `elif` — ternary has no `elif`; chain another ternary only if it stays readable |
| Readability is still clear at a glance | Nesting ternaries: `"A" if x >= 90 else "B" if x >= 80 else "F"` — use `if-elif-else` instead |

**Warning:** Use sparingly. Good for simple cases, bad for complex logic.

---

## Real-World Examples

### Example 1: Login System
```python
username = "admin"
password = "secret123"

input_user = input("Username: ")
input_pass = input("Password: ")

if input_user == username and input_pass == password:
    print("Login successful! Welcome back.")
elif input_user == username:
    print("Wrong password.")
elif input_pass == password:
    print("Wrong username.")
else:
    print("Invalid credentials.")
```

### Example 2: Temperature Advisor
```python
temp = float(input("Enter temperature in °C: "))

if temp < 0:
    print("It's freezing! Wear warm clothes.")
elif temp < 15:
    print("It's cold. Bring a jacket.")
elif temp < 25:
    print("Nice weather. Enjoy your day!")
elif temp < 35:
    print("It's warm.")
else:
    print("It's very hot. Stay hydrated!")
```

---

## Common Mistakes & Gotchas

1. **Using `=` instead of `==`**  
   ```python
   if age = 18:    # Wrong! This is assignment
       ...
   if age == 18:   # Correct
   ```

2. **Forgetting the colon `:`**

3. **Incorrect indentation** — causes `IndentationError`

4. **Checking `None` with `==` instead of `is`**
   ```python
   if result is None:      # Recommended
   if result == None:      # Works but less Pythonic
   ```

5. **Too many `elif` chains** — consider using dictionaries or polymorphism for complex cases.

6. **Assuming order doesn't matter** — Python checks `if-elif` from top to bottom.

---

## Best Practices

- Keep conditions simple and readable
- Use descriptive variable names in conditions
- Avoid deep nesting (aim for max 2-3 levels)
- Use `elif` instead of multiple separate `if` statements when conditions are mutually exclusive
- Add comments for complex conditions

---
## See Also

- [[02 - Variables and Data Types]] — Understanding objects and mutability
- [[05 - Loops]] — Repeating actions with `for` and `while`

---

Continue with **[[05 - Loops]]**

---