---
tags:
  - foundations
  - python
  - loops
  - while
  - control-flow
stage: 1
difficulty: Beginner
---

# The `while` Loop

**Prev:** [[08 - Control Flow (if-elif-else)]] | **Next:** [[10 - For loop]]

> A `while` loop repeats a block as long as a condition stays true. Use it when you do not know in advance how many times you need to repeat — waiting for user input, polling a server, or running until a flag changes.

---

## Why the `while` loop exists

`if` runs a block **once** when a condition is true. Sometimes you need the **same check repeated** until something changes:

- Keep asking for a password until it is correct
- Keep reading lines from a file until EOF
- Keep a game running until the player quits
- Keep retrying a network request until it succeeds or times out

Without loops, you would copy-paste the same code hundreds of times. The `while` loop is Python's way of saying: **"keep doing this while the condition holds."**

---

## Syntax

```python
while condition:
    # indented block — runs each iteration
    # must eventually make condition False (or use break)
```

| Part | Role |
|------|------|
| `while` | Keyword that starts the loop |
| `condition` | Any expression — usually comparison or truthiness check |
| `:` | Required — starts the block |
| Indented body | Code executed on every iteration |

The condition is checked **before each iteration**. If it is falsy on the first check, the body **never runs**.

```python
count = 10
while count < 5:
    print(count)   # never runs — condition is False immediately
```

---

## How execution flows

```
        ┌─────────────────┐
        │ Check condition │
        └────────┬────────┘
                 │
         False   │   True
           ┌─────┴─────┐
           ▼           ▼
      Exit loop    Run body
           ▲           │
           │           │
           └───────────┘
              (repeat)
```

Every iteration:

1. Evaluate the condition (truthiness or comparison)
2. If **falsy** → exit the loop, continue with code after it
3. If **truthy** → run the entire indented body top to bottom
4. Go back to step 1

---

## Example 1: Basic counter

```python
count = 1

while count <= 5:
    print(f"Count is {count}")
    count = count + 1        # crucial — moves toward exit
# Count is 1
# Count is 2
# Count is 3
# Count is 4
# Count is 5
```

### Step-by-step

| Iteration | Condition `count <= 5` | Action | `count` after body |
|-----------|------------------------|--------|---------------------|
| 1 | `1 <= 5` → True | print 1 | 2 |
| 2 | `2 <= 5` → True | print 2 | 3 |
| 3 | `3 <= 5` → True | print 3 | 4 |
| 4 | `4 <= 5` → True | print 4 | 5 |
| 5 | `5 <= 5` → True | print 5 | 6 |
| 6 | `6 <= 5` → **False** | exit | — |

When `count` becomes `6`, the condition fails **before** a sixth print. The loop runs exactly **5 times**.

**Shorthand update:**

```python
count += 1    # same as count = count + 1
```

---

## Example 2: User input until quit

```python
user_input = ""

while user_input != "quit":
    user_input = input("Type something (or 'quit' to stop): ")
    if user_input != "quit":
        print(f"You typed: {user_input}")
```

**Why initialize `user_input = ""`?**  
The condition is checked **before** the first `input()`. An empty string is not `"quit"`, so the loop enters and prompts immediately. Without initialization, `user_input` would not exist → `NameError`.

**Pattern:** `while` + `input()` is the standard "keep asking until done" shape.

---

## Example 3: Truthiness as condition

Any truthy/falsy value works — not just comparisons:

```python
items = [1, 2, 3]

while items:
    print(items.pop())   # removes and prints from end
# 3
# 2
# 1
```

Loop runs while `items` is non-empty. When `[]`, condition is falsy → stop.

```python
while True:              # always True — see Infinite Loops below
    ...
```

---

## Infinite loops

An **infinite loop** never satisfies its exit condition — the body runs forever until you stop the program.

### Accidental — forgot to update the condition

```python
count = 1

while count <= 5:
    print(f"Count is {count}")
    # missing count += 1 → count stays 1 → condition always True
```

`count` stays `1`, so `1 <= 5` is always true. The terminal fills with output until you press **Ctrl+C**.

### Intentional — `while True` with exit inside

```python
while True:
    command = input("Enter a command (or 'quit'): ")
    if command == "quit":
        break                    # exits the loop — covered in [[11 - break, continue, pass]]
    print(f"Running: {command}")
```

| Type | Condition | How it stops |
|------|-----------|--------------|
| Normal `while` | eventually falsy | Loop ends on its own |
| Accidental infinite | always truthy | `Ctrl+C` or kill process |
| `while True` + `break` | always truthy | `break`, `return`, or `raise` inside body |

**Rule:** Every `while` needs either:

- Something in the body that makes the condition **eventually falsy**, or
- An explicit **`break`** / **`return`** path

---

## `while` vs `for` — when to use which

| Use `while` when… | Use `for` when… |
|-------------------|-----------------|
| You do not know how many iterations | You are iterating a known sequence |
| Condition-driven ("until done") | Collection-driven ("for each item") |
| Waiting for external event (input, file, network) | Using `range()`, list, string, etc. |

```python
# while — unknown iterations
while not connected:
    retry_connection()

# for — known sequence (see [[10 - For loop]])
for user in users:
    send_email(user)
```

If you find yourself writing `while i < len(items)`, you probably want a `for` loop instead.

---

## Common mistakes / Gotchas

**Forgetting to update the loop variable.**

```python
count = 1
while count <= 5:
    print(count)   # infinite loop
```

**Condition never true — body never runs.**

```python
x = 10
while x < 5:
    print(x)       # skipped entirely
```

**Using `=` instead of `==` in condition.**

```python
# while count = 5:   # SyntaxError
while count == 5:    # correct
```

**Not initializing variables before the loop.**

```python
# while user_input != "quit":   # NameError
user_input = ""
while user_input != "quit":
    ...
```

**Modifying a collection while iterating over it** — can skip items or loop forever. Iterate over a copy or use a `for` loop with care.

**Floating-point conditions** — `while x != 0.0` may never terminate due to precision. Prefer `while abs(x) > epsilon` or integer counters.

---

## Best practices

- Keep the body short — extract complex logic into functions
- Make the **progress toward exit** obvious (`count += 1`, `items.pop()`, flag flip)
- Prefer `for` when iterating a fixed collection
- Use `while True` + `break` for menu/input loops when exit logic is inside the body
- Name conditions clearly: `while is_running:` not `while x:` when `x` is opaque

---

## See also

- [[08 - Control Flow (if-elif-else)]] — `if` blocks inside `while` loop bodies
- [[07 - Comparisons and Logical Operators]] — truthiness and comparisons that drive loop conditions
- [[10 - For loop]] — counterpart when you iterate a known sequence instead
- [[11 - break, continue, pass]] — exiting early or skipping to the next iteration

---

## → What's next

You can repeat code while a condition stays true. When you already know *what* to iterate over — a list, a string, or a `range()` — the `for` loop is the cleaner tool; continue with [[10 - For loop]].
