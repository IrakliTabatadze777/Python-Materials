---
tags:
  - foundations
  - python
  - loops
  - break
  - continue
  - pass
  - control-flow
stage: 1
difficulty: Beginner
---

# break, continue, and pass

**Prev:** [[10 - For loop]] | **Next:** [[12 - Lists]]

> `break` exits a loop early. `continue` skips to the next iteration. `pass` does nothing — but deliberately. Together they let you control loop flow without rewriting the whole structure.

---

## Why these keywords exist

Loops run repetitively, but real programs need exceptions:

- Stop searching when the target is found → **`break`**
- Skip invalid items but keep going → **`continue`**
- Leave a syntactically empty block as a placeholder → **`pass`**

All three work in **`for` and `while` loops** (and `pass` also appears in `if`, functions, and classes).

---

## `break` — exit the loop immediately

`break` terminates the **innermost enclosing loop** and jumps to the first statement after that loop.

```python
for number in range(1, 11):
    if number == 6:
        break
    print(number)
# 1
# 2
# 3
# 4
# 5
```

Without `break`, the loop would print 1 through 10. With it, execution stops permanently when `number == 6`.

### Flow diagram

```
for each item:
    if should_stop:
        break  ──────────► exit loop entirely
    do_work()
# continues here
```

### `while` + `break` — menu / input pattern

```python
while True:
    command = input("Command (quit to exit): ")
    if command == "quit":
        break
    print(f"Running: {command}")
# loop ends here after break
```

`while True` + `break` is idiomatic when exit logic lives **inside** the body.

### Search-and-stop

```python
names = ["Alice", "Bob", "Carol", "Dave"]
target = "Carol"

for name in names:
    if name == target:
        print(f"Found {target}")
        break
else:
    print(f"{target} not found")   # runs only if no break — see Loop else below
# Found Carol
```

---

## `continue` — skip to the next iteration

`continue` stops the **current** iteration immediately and jumps to the **next** check / next item. Code below `continue` in the same iteration does not run.

```python
for number in range(1, 11):
    if number % 2 == 0:
        continue
    print(number)
# 1
# 3
# 5
# 7
# 9
```

When `number` is even, `continue` skips `print`. Odd numbers print normally.

### Flow diagram

```
for each item:
    if should_skip:
        continue  ──► next item (skip rest of body)
    do_work()
```

### Filter invalid input

```python
numbers = [1, -2, 3, 0, 5, -1]

for n in numbers:
    if n <= 0:
        continue
    print(f"Processing {n}")
# Processing 1
# Processing 3
# Processing 5
```

### `break` vs `continue`

| | `break` | `continue` |
|---|---------|------------|
| **Effect** | Exit loop completely | Skip rest of **this** iteration |
| **Loop keeps running?** | No | Yes — next iteration |
| **Use when** | Goal achieved or fatal error | Current item should be ignored |

```python
for i in range(5):
    if i == 2:
        break       # prints 0, 1 — then stops
    print(i)

for i in range(5):
    if i == 2:
        continue    # prints 0, 1, 3, 4 — skips 2 only
    print(i)
```

---

## `pass` — intentional no-op

`pass` is a **null statement** — it does nothing. It exists because Python **requires a body** after `if`, `for`, `while`, `def`, and `class`. You cannot leave those blocks empty.

```python
if age >= 18:
    pass    # TODO: add adult logic later
else:
    print("Too young")
```

Without `pass`, this is a **SyntaxError**:

```python
# if age >= 18:
#     # empty — SyntaxError: expected an indented block
```

### Where `pass` appears

**Placeholder in a loop:**

```python
for item in items:
    pass    # not implemented yet
```

**Stub function:**

```python
def process_data(data):
    pass    # will implement later
```

**Stub class:**

```python
class UserAccount:
    pass
```

**Silent `if` branch:**

```python
if error:
    pass              # deliberately ignore
else:
    handle_success()
```

### `pass` vs `continue` vs `break`

| Keyword | Scope | Effect |
|---------|-------|--------|
| `pass` | Any block | Nothing — placeholder only |
| `continue` | Loop only | Skip to next iteration |
| `break` | Loop only | Exit loop entirely |

```python
for i in range(3):
    pass       # does nothing — still prints all 3

for i in range(3):
    continue   # skips print — no output

for i in range(3):
    break      # exits immediately — no output
    print(i)
```

**Never use `pass` to skip loop iterations** — that is `continue`. **Never use `pass` to exit** — that is `break`.

---

## Loop `else` — runs when no `break`

Loop `else` is **not** like `if-else`. The `else` block runs only if the loop **completed normally** — without hitting `break`.

```python
for number in range(1, 5):
    print(number)
else:
    print("Loop completed normally!")
# 1
# 2
# 3
# 4
# Loop completed normally!
```

**Works identically for `while`:**

```python
count = 1

while count <= 3:
    print(count)
    count += 1
else:
    print("Loop completed normally!")
# 1
# 2
# 3
# Loop completed normally!
```

### When `else` does *not* run

```python
for number in range(1, 5):
    if number == 3:
        break
    print(number)
else:
    print("Loop completed normally!")   # never printed
# 1
# 2
```

| Loop ended because… | `else` runs? |
|---------------------|--------------|
| Condition falsy / sequence exhausted | Yes |
| `break` | No |
| `return` or uncaught exception | No |

### Practical use — search not found

```python
target = 99
for n in [1, 2, 3, 4, 5]:
    if n == target:
        print("Found")
        break
else:
    print("Not found")    # runs because break never hit
# Not found
```

Some developers prefer a flag variable instead — both styles are valid.

---

## `break` and `continue` in nested loops

Both affect **only the innermost loop** they are written inside.

```python
for i in range(1, 4):
    for j in range(1, 4):
        if j == 2:
            break              # exits inner j loop only
        print(f"({i}, {j})", end=" ")
    print()
# (1, 1)
# (2, 1)
# (3, 1)
```

Outer `i` still runs 1, 2, 3. Inner `j` breaks at 2 each time.

```python
for i in range(1, 4):
    for j in range(1, 4):
        if j == 2:
            continue           # skips j==2, inner loop continues
        print(f"({i}, {j})", end=" ")
    print()
# (1, 1) (1, 3)
# (2, 1) (2, 3)
# (3, 1) (3, 3)
```

### Breaking out of **both** loops

Python has no `break 2`. Common patterns:

**Flag variable:**

```python
found = False
for i in range(1, 100):
    for j in range(1, 100):
        if i * j == 42:
            found = True
            break
    if found:
        break
```

**Extract to a function — use `return`:**

```python
def find_product(target):
    for i in range(1, 100):
        for j in range(1, 100):
            if i * j == target:
                return i, j
    return None

print(find_product(42))   # (6, 7) or (7, 6) etc.
```

`return` exits the entire function — cleaner than double `break` for deep nesting.

---

## Common mistakes / Gotchas

**Using `pass` expecting it to skip an iteration.**

```python
for n in [1, 2, 3]:
    if n == 2:
        pass       # still prints 2
    print(n)
```

Use `continue` to skip.

**Assuming `break` exits all nested loops.** Only the innermost.

**Loop `else` confused with `if-else`.** Loop `else` means "no break occurred."

**`continue` in `while` without updating state** — can cause infinite loop:

```python
n = 0
while n < 5:
    if n == 2:
        continue    # skips n += 1 when n==2 → infinite loop
    print(n)
    n += 1
```

Ensure progress happens before `continue`, or restructure.

**Overusing `break`/`continue`** — sometimes a clearer `if` without early jumps reads better:

```python
# readable
for n in numbers:
    if n > 0:
        print(n)
```

---

## Best practices

- Use `break` for early exit when found or on fatal error
- Use `continue` to filter — keep the main body at low indentation
- Use `pass` only as a syntactic placeholder — replace with real code or remove
- For nested break-out, prefer a function with `return` over flag spaghetti
- Loop `else` is optional and unfamiliar to many — use only when it aids readability

---

## See also

- [[09 - While loop]] — `while True` + `break` for open-ended input loops
- [[10 - For loop]] — nested loops where `break` and `continue` apply per level
- [[08 - Control Flow (if-elif-else)]] — `if` conditions that trigger `break` or `continue`
- [[07 - Comparisons and Logical Operators]] — search patterns that pair with loop `else`

---

## → What's next

You can control repetition with `break`, `continue`, and `pass`. Foundations finish by organizing data into collections — storing, accessing, and iterating groups of values — starting with [[12 - Lists]].
