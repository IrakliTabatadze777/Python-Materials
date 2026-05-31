---
tags:
  - foundations
  - python
  - loops
stage: 1
difficulty: Beginner
---

# Loops (for and while)

**Prev:** [[04 - Control Flow (if-elif-else)]] | **Next:** [[06 - Lists and Tuples]]

> Loops are a fundamental concept in programming that allow you to repeat a block of code multiple times. Python provides two main types of loops: `while` and `for`.

---

## Why Loops Matter

Loops help you avoid writing repetitive code. They are essential for:

- Processing large amounts of data
- Repeating tasks until a condition is met
- Iterating through lists, files, or user input
- Running simulations or games
- Automating repetitive operations

Without loops, even simple tasks like printing numbers from 1 to 100 would require 100 lines of code.

---

## The `while` Loop

### Syntax Explanation

```python
while condition:
    # code to repeat
    # must eventually make condition False
```

**Breakdown of the syntax:**

- `while` — the keyword that starts the loop
- `condition` — any expression that evaluates to `True` or `False`
- `:` — colon is required at the end of the line
- Indented block (4 spaces) — all code inside this block will be repeated

The loop continues **as long as** the condition is `True`. When the condition becomes `False`, the loop stops.

### Example 1: Basic while Loop

```python
count = 1

while count <= 5:
    print(f"Count is {count}")
    count = count + 1        # This line is crucial!
```

**How it works step by step:**
1. `count` is set to 1
2. Check if `1 <= 5` → True → execute the block
3. Print the message
4. Increase `count` by 1
5. Go back and check the condition again
6. Repeat until `count` becomes 6 (condition becomes False)

### Example 2: User Input Loop

```python
user_input = ""

while user_input != "quit":
    user_input = input("Type something (or 'quit' to stop): ")
    if user_input != "quit":
        print(f"You typed: {user_input}")
```

### Infinite Loops

An **infinite loop** is a loop whose condition **never becomes `False`**, so the block keeps running forever (until you stop the program manually).

**Accidental infinite loop** — forget to update the variable:

```python
count = 1

while count <= 5:
    print(f"Count is {count}")
    # count is never increased → condition stays True → loop never stops
```

**Intentional infinite loop** — `while True` with a way to exit inside the block:

```python
while True:
    command = input("Enter a command (or 'quit'): ")
    if command == "quit":
        break                    # exits the loop immediately
    print(f"Running: {command}")
```

| Type | Condition | How it stops |
|------|-----------|--------------|
| Normal `while` | eventually `False` | Loop ends on its own (e.g. `count` reaches 6) |
| Accidental infinite | always `True` | `Ctrl+C` in the terminal, or kill the process |
| `while True` + `break` | always `True` | `break`, `return`, or `raise` inside the block |

**Rule:** Every `while` loop needs something inside the block that can make the condition `False` — or an explicit `break`. Without one of those, you get an infinite loop.

---

## The `for` Loop

### Syntax Explanation

```python
for variable in sequence:
    # code to repeat for each item
```

**Breakdown of the syntax:**

- `for` — the keyword that starts the loop
- `variable` — a temporary name that takes the value of each item in the sequence
- `in` — required keyword
- `sequence` — any iterable object (list, string, range, tuple, etc.)
- `:` — colon at the end
- Indented block — code that runs for every item

The `for` loop automatically goes through each item in the sequence one by one.

### Example 1: Basic for Loop with List

```python
fruits = ["apple", "banana", "cherry", "mango"]

for fruit in fruits:
    print(f"I like {fruit}")
```

### Example 2: Using `range()`

`range()` is commonly used with `for` loops to generate sequences of numbers.

```python
# Different ways to use range()

for i in range(5):           # 0, 1, 2, 3, 4
    print(i)

for i in range(1, 6):        # 1, 2, 3, 4, 5
    print(i)

for i in range(0, 11, 2):    # 0, 2, 4, 6, 8, 10
    print(i)
```

### Example 3: Looping Over a String

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

---

## Loop Control Statements

### `break` - Exit the loop immediately

```python
for number in range(1, 11):
    if number == 6:
        break
    print(number)   # Will print 1 to 5
```

### `continue` - Skip to the next iteration

```python
for number in range(1, 11):
    if number % 2 == 0:   # if even number
        continue
    print(number)         # Will print only odd numbers
```

### `else` with Loops

Loop `else` is **not** the same as `if-else`. Here, `else` means: **"run this block only if the loop finished on its own — without a `break`."**

Use it when you want a cleanup or success message after a search loop, or to run code when no early exit happened.

**Works the same for `for` and `while`** — the rule is identical: `else` runs if the loop ended because the condition became `False` (`while`) or the sequence was exhausted (`for`), not because `break` was called.

**`for` example — loop completes normally:**

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

**`while` example — same behavior:**

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

**When `else` does *not* run — `break` exits early:**

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
| Condition became `False` / sequence finished | Yes |
| `break` was hit | No |
| `return` or exception inside the loop | No |

---

## Nested Loops

A **nested loop** is a loop placed **inside** another loop. For every one step of the **outer** loop, the **inner** loop runs **from start to finish**.

```python
for i in range(1, 4):          # outer loop — i = 1, 2, 3
    for j in range(1, 4):      # inner loop  — j = 1, 2, 3 (restarts each time i changes)
        print(f"({i}, {j})", end=" ")
    print()                    # new line after inner loop finishes
# (1, 1) (1, 2) (1, 3)
# (2, 1) (2, 2) (2, 3)
# (3, 1) (3, 2) (3, 3)
```

### The mental model: outer = rows, inner = columns

Think of a **multiplication table** or a **grid**:

```
        j →  1      2      3
    i
    ↓
    1       (1,1)  (1,2)  (1,3)   ← inner loop runs 3 times while i stays 1
    2       (2,1)  (2,2)  (2,3)   ← inner loop runs 3 times again while i stays 2
    3       (3,1)  (3,2)  (3,3)   ← inner loop runs 3 times again while i stays 3
```

- The **outer loop** (`i`) moves **slowly** — one row at a time.
- The **inner loop** (`j`) moves **quickly** — it sweeps across every column before `i` changes.

**Rule:** The inner loop must **finish completely** before the outer loop advances to its next value.

### How the layers work

```
Outer for: i = 1
    Inner for: j = 1 → print (1, 1)
    Inner for: j = 2 → print (1, 2)
    Inner for: j = 3 → print (1, 3)
    Inner loop done → print() (new line)
Outer for: i = 2
    Inner for: j = 1 → print (2, 1)
    Inner for: j = 2 → print (2, 2)
    ...
```

When the outer loop picks a new `i`, the inner loop **starts over** — `j` goes back to `1`, not where it left off.

### Step-by-step (first two rows)

| Step | Outer `i` | Inner `j` | Prints |
|------|-----------|-----------|--------|
| 1    | 1         | 1         | `(1, 1)` |
| 2    | 1         | 2         | `(1, 2)` |
| 3    | 1         | 3         | `(1, 3)` |
| 4    | —         | inner done | newline |
| 5    | 2         | 1         | `(2, 1)` |
| 6    | 2         | 2         | `(2, 2)` |
| …    | …         | …         | … |

After step 3, `j` has no more values, so the inner loop ends. Only then does `i` become `2` and the inner loop run again from `j = 1`.

### How many times does the body run?

Multiply the sizes:

| Outer iterations | Inner iterations per outer | Total inner-body runs |
|------------------|----------------------------|------------------------|
| 3 (`i` = 1, 2, 3) | 3 (`j` = 1, 2, 3) each time | **3 × 3 = 9** prints |

In general: **outer count × inner count**. A loop inside another loop inside another multiplies again (e.g. 3 × 3 × 3 = 27).

### `for` + `while` can mix

Nesting is not limited to two `for` loops:

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

Same idea: the inner loop completes fully on each step of the outer loop.

### `break` and `continue` in nested loops

`break` and `continue` affect **only the loop they are written inside** — the innermost enclosing loop.

```python
for i in range(1, 4):
    for j in range(1, 4):
        if j == 2:
            break              # exits inner loop only; outer loop keeps going
        print(f"({i}, {j})", end=" ")
    print()
# (1, 1)
# (2, 1)
# (3, 1)
```

Here `break` stops the inner `j` loop when `j == 2`, but `i` still moves to 2, then 3. To exit **both** loops at once, use a flag variable or refactor into a function with `return`.

### Common points of confusion

| Mistake | What actually happens |
|---------|------------------------|
| Expecting `j` to "remember" its value when `i` changes | Inner loop **resets** each time — `j` starts from the beginning again |
| Thinking outer loop waits mid-inner-loop | Outer loop **pauses** until inner loop is **fully done** |
| Confusing which variable changes faster | **Inner** variable changes faster; **outer** changes slower |
| Assuming `break` stops everything | `break` exits **one** loop — usually the inner one |

### When nested loops are useful

- **2D grids** — rows and columns, chess boards, pixels
- **Comparing every pair** — each item with every other item
- **Tables** — multiplication table, timetables
- **Nested data** — list of lists, users with multiple orders

If you can describe the task as "for each X, do something for every Y", a nested loop is often the right shape.

**Tip:** Trace nested loops on paper with a small example (2×2, not 10×10). Once you see the outer-slow / inner-fast pattern, larger grids follow the same rules.

---

## Common Mistakes & Gotchas

1. **Infinite loop** — forgetting to update the condition in `while` loop
   ```python
   count = 1
   while count <= 5:
       print(count)   # Missing count += 1 → Infinite loop!
   ```

2. **Off-by-one error** — especially with `range()`

3. **Modifying a list while iterating** — can cause unexpected behavior

4. **Using `==` instead of `=`** in loop counters

5. **Deep nesting** — makes code hard to read

---

## Best Practices

- Use `for` loop when you know how many times to iterate
- Use `while` loop when you don't know in advance when to stop
- Keep loop bodies short and clear
- Use meaningful variable names (`for user in users:` instead of `for x in y:`)
- Avoid modifying the sequence while iterating over it

---

## See Also

- [[04 - Control Flow (if-elif-else)]] — Decision making with conditions
- [[06 - Lists and Tuples]] — Working with collections of data

---

Continue with **[[06 - Lists and Tuples]]**

---