---
tags:
  - foundations
  - python
  - data-structures
  - collections
stage: 1
difficulty: Beginner
---

# Lists in Python

**Prev:** [[11 - break, continue, pass]] | **Next:** [[13 - Tuples]]

> Lists are one of the most versatile and commonly used data structures in Python. They allow you to store, organize, and manipulate collections of items efficiently.

---

## What is a List?

A **list** is an ordered, mutable (changeable) collection of items. Lists can contain elements of different data types, including other lists.

```python
# Examples of lists
numbers = [1, 2, 3, 4, 5]
fruits = ["apple", "banana", "cherry"]
mixed = [1, "hello", 3.14, True, None]
nested = [1, [2, 3], [4, 5, 6]]
empty_list = []
```

**Key Characteristics:**
- Ordered (elements have a defined order)
- Mutable (you can add, remove, or change items)
- Allows duplicates
- Dynamic size (can grow or shrink)

---

## Creating Lists

### Different ways to create lists:

```python
# Direct creation
colors = ["red", "green", "blue"]

# Using list() constructor
letters = list("Python")          # ['P', 'y', 't', 'h', 'o', 'n']

# List comprehension (very Pythonic)
squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
```

---

## Accessing Elements

### Indexing

```python
fruits = ["apple", "banana", "cherry", "mango", "kiwi"]

print(fruits[0])      # apple
print(fruits[2])      # cherry
print(fruits[-1])     # kiwi (last item)
print(fruits[-2])     # mango
```

### Slicing

```python
print(fruits[1:4])        # ['banana', 'cherry', 'mango']
print(fruits[:3])         # First 3 items
print(fruits[2:])         # From index 2 to end
print(fruits[::2])        # Every second item
print(fruits[::-1])       # Reversed list
```

---

## Modifying Lists

### Changing Elements

```python
fruits[1] = "blueberry"
fruits[0:2] = ["orange", "grape"]
```

### Adding Elements

```python
fruits.append("pineapple")           # Add to the end
fruits.insert(1, "lemon")            # Insert at specific position
fruits.extend(["watermelon", "peach"])  # Add multiple items
```

### Removing Elements

```python
fruits.remove("banana")              # Remove by value
popped = fruits.pop()                # Remove and return last item
popped = fruits.pop(2)               # Remove by index
fruits.clear()                       # Remove all items
```

---

## Important List Methods

| Method              | Description                              | Example |
|---------------------|------------------------------------------|--------|
| `append()`          | Add item to end                          | `lst.append(x)` |
| `insert()`          | Insert at index                          | `lst.insert(0, x)` |
| `extend()`          | Add multiple items                       | `lst.extend([1,2])` |
| `remove()`          | Remove first occurrence of value         | `lst.remove(x)` |
| `pop()`             | Remove and return item                   | `lst.pop()` |
| `clear()`           | Remove all items                         | `lst.clear()` |
| `index()`           | Find index of first occurrence           | `lst.index(x)` |
| `count()`           | Count occurrences of value               | `lst.count(x)` |
| `sort()`            | Sort list in place                       | `lst.sort()` |
| `reverse()`         | Reverse list in place                    | `lst.reverse()` |
| `copy()`            | Create shallow copy                      | `lst.copy()` |

---

## List Operations

### Concatenation and Repetition

```python
list1 = [1, 2, 3]
list2 = [4, 5, 6]

combined = list1 + list2          # [1, 2, 3, 4, 5, 6]
repeated = list1 * 3              # [1, 2, 3, 1, 2, 3]
```

### Membership Test

```python
if "apple" in fruits:
    print("Apple is in the list")
```

### Length, Min, Max, Sum

```python
print(len(fruits))
print(min(numbers))
print(max(numbers))
print(sum(numbers))
```

---

## List Comprehensions

A **list comprehension** is a compact expression that builds a new list by looping over an iterable, optionally filtering items, and transforming each one. It is syntactic sugar — Python still runs a loop under the hood, but the intent ("build a list from these items") is visible in one line.

### General form

```python
[expression for item in iterable]
[expression for item in iterable if condition]
```

| Part | Role |
|------|------|
| `expression` | Value placed into the **new** list (can use `item`) |
| `for item in iterable` | Loop — same idea as a `for` loop |
| `if condition` | Optional filter — only items where condition is `True` are included |

Read it left to right: **"Give me `expression` for each `item` in `iterable` (if `condition`)."**

---

### How execution works (step by step)

Take:

```python
squares = [x**2 for x in range(1, 4)]
```

Python does roughly this:

1. Create an empty list internally.
2. Loop: `x = 1` → compute `x**2` → append `1`.
3. Loop: `x = 2` → compute `x**2` → append `4`.
4. Loop: `x = 3` → compute `x**2` → append `9`.
5. Return the list `[1, 4, 9]`.

With a filter:

```python
evens = [x for x in range(6) if x % 2 == 0]
```

| Step | `x` | `x % 2 == 0` | Added to result? |
|------|-----|--------------|------------------|
| 1 | `0` | `True` | yes → `[0]` |
| 2 | `1` | `False` | skip |
| 3 | `2` | `True` | yes → `[0, 2]` |
| 4 | `3` | `False` | skip |
| 5 | `4` | `True` | yes → `[0, 2, 4]` |
| 6 | `5` | `False` | skip |

Result: `[0, 2, 4]`.

**Order of parts:** With both `if` and an expression, Python evaluates **`for` → `if` → expression** for each iteration. The filter runs before the expression is computed for that item.

---

### List comprehension vs `for` loop

They do the same job. A comprehension is usually preferred when you are **only** building a list and the logic fits on one readable line.

#### Basic — same result, two styles

**`for` loop:**

```python
squares = []
for x in range(1, 11):
    squares.append(x**2)
# squares → [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

**List comprehension:**

```python
squares = [x**2 for x in range(1, 11)]
# squares → [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

The loop version separates **create empty list → loop → append**. The comprehension states the whole pipeline in the brackets.

#### With a condition

**`for` loop:**

```python
evens = []
for x in range(20):
    if x % 2 == 0:
        evens.append(x)
# evens → [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

**List comprehension:**

```python
evens = [x for x in range(20) if x % 2 == 0]
# evens → [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

The `if` in a comprehension is a **filter** (which items to include), not an `if`/`else` branch inside the loop body.

#### With transformation

```python
fruits = ["apple", "banana", "cherry"]

# for loop
upper_fruits = []
for fruit in fruits:
    upper_fruits.append(fruit.upper())
# ['APPLE', 'BANANA', 'CHERRY']

# comprehension
upper_fruits = [fruit.upper() for fruit in fruits]
# ['APPLE', 'BANANA', 'CHERRY']
```

---

### When to use which

| Use a **list comprehension** when… | Use a **`for` loop** when… |
|-----------------------------------|----------------------------|
| You only need a new list as the outcome | You need side effects (`print`, file I/O, `append` to multiple lists) |
| The logic is one expression + optional filter | The body has many statements or complex branching |
| The line stays easy to read | A comprehension would be long or nested awkwardly |
| You are mapping/filtering data | You are waiting on user input or running until a condition changes |

**Rule of thumb:** If you find yourself writing `result.append(...)` inside a simple loop with no other statements, a comprehension is often clearer.

**Prefer a `for` loop** when the body does more than produce one value:

```python
# Side effects — use a normal loop
for fruit in fruits:
    print(fruit.upper())

# Multiple actions — use a normal loop
for x in data:
    cleaned = x.strip()
    total += len(cleaned)
```

---

### Examples (basic → nested)

```python
# Basic — transform each item
squares = [x**2 for x in range(1, 11)]
# [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

# With condition — filter while building
evens = [x for x in range(20) if x % 2 == 0]
# [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

# With transformation
fruits = ["apple", "banana", "cherry"]
upper_fruits = [fruit.upper() for fruit in fruits]
# ['APPLE', 'BANANA', 'CHERRY']

# if-else in the expression (not the filter position)
labels = ["even" if x % 2 == 0 else "odd" for x in range(5)]
# ['even', 'odd', 'even', 'odd', 'even']
```

Note the difference:

- `[x for x in items if x > 0]` — **`if` filters** which items are included.
- `["pos" if x > 0 else "neg" for x in items]` — **`if`/`else` inside the expression** chooses what value to store.

#### Nested comprehensions

```python
matrix = [[i * j for j in range(3)] for i in range(3)]
# [[0, 0, 0], [0, 1, 2], [0, 2, 4]]
```

Same idea as nested loops — outer `i`, inner `j`:

```python
matrix = []
for i in range(3):
    row = []
    for j in range(3):
        row.append(i * j)
    matrix.append(row)
# [[0, 0, 0], [0, 1, 2], [0, 2, 4]]
```

The outer comprehension builds the **list of rows**; the inner one builds **each row**. Read nested comprehensions from **inside out**: inner `for j` runs completely for each `i`.

Nested comprehensions are powerful but easy to overuse. If a nested comprehension is hard to read, a nested `for` loop (or a helper function) is the better choice.

---

### Performance note

Comprehensions are often **slightly faster** than an equivalent `for` loop with `.append()` because Python optimizes the built-in construct. The main reason to use them is **clarity**, not speed — unless you are building very large lists in hot code paths.

Avoid building huge lists in a loop with `+`:

```python
# Slow — creates a new list every iteration
result = []
for x in range(10000):
    result = result + [x**2]

# Better — append in a loop, or use a comprehension
result = [x**2 for x in range(10000)]
```


---

## Common Patterns

### Iterating over Lists

```python
for fruit in fruits:
    print(fruit)

# With index
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")
```

### Finding Items

```python
# First item matching condition
first = next((x for x in fruits if "a" in x), None)
```

---

## Shallow vs Deep Copy

When a list contains **other mutable objects** (nested lists, dicts, custom objects), copying gets subtle. Python gives you three different ideas that beginners often mix up:

| Operation | What it does |
|-----------|--------------|
| `b = a` | **Assignment** — both names point to the **same** list |
| `b = a.copy()` | **Shallow copy** — new outer list, but **shared** inner objects |
| `b = copy.deepcopy(a)` | **Deep copy** — fully independent copy at every level |

---

### Assignment is not a copy

```python
original = [1, 2, 3]
alias = original          # same list object, two names

alias.append(4)

print(original)         # [1, 2, 3, 4]
print(alias is original)  # True
```

Changing `alias` changes `original` because there is only **one** list in memory. This is the "lists are mutable and references are shared" behavior from [[02 - Variables and Data Types]].

To duplicate a list you need an explicit **copy** operation — not `=`.

---

### Shallow copy — new container, shared contents

A **shallow copy** creates a **new list object**, but each **element** inside still points to the same objects as the original.

```python
import copy

lst = [1, [2, 3]]
shallow = lst.copy()        # same as lst[:], list(lst), or copy.copy(lst)
```

Mental model:

```text
lst      →  [ 1 ,  [2, 3] ]
                 ↑      ↑
shallow  →  [ 1 ,  [2, 3] ]   ← new outer list
                      ↑
                 same inner list object
```

**Top-level change** — only affects the list you modify:

```python
lst = [1, [2, 3]]
shallow = lst.copy()

lst[0] = 99                 # replace first slot in lst only

print(lst)                  # [99, [2, 3]]
print(shallow)              # [1, [2, 3]]  — unchanged
```

**Nested change** — affects both, because the inner list is **shared**:

```python
lst = [1, [2, 3]]
shallow = lst.copy()

lst[1][0] = 99              # mutate the inner list object

print(lst)                  # [1, [99, 3]]
print(shallow)              # [1, [99, 3]]  — also changed!
print(lst[1] is shallow[1]) # True — same inner list
```

Shallow copy is enough when the list is **flat** (only numbers, strings, tuples — immutable items):

```python
nums = [1, 2, 3]
copy_nums = nums.copy()
copy_nums[0] = 99

print(nums)       # [1, 2, 3]  — safe
print(copy_nums)  # [99, 2, 3]
```

---

### Deep copy — fully independent tree

A **deep copy** walks the whole structure and duplicates nested mutable objects too, so no level is shared.

```python
import copy

lst = [1, [2, 3]]
deep = copy.deepcopy(lst)

lst[1][0] = 99

print(lst)                  # [1, [99, 3]]
print(deep)                 # [1, [2, 3]]   — unchanged
print(lst[1] is deep[1])    # False — different inner lists
```

Use `deepcopy` when:

- The list contains nested lists, dicts, or other mutable containers
- You need to edit a copy without affecting the original anywhere inside
- You are cloning complex state (configs, game boards, parsed trees)

Deep copy is slower and uses more memory — only use it when shallow copy is not enough.

---

### Ways to make a shallow copy

All of these produce a **shallow** copy:

```python
shallow = lst.copy()
shallow = lst[:]
shallow = list(lst)
shallow = copy.copy(lst)
```

They behave the same for lists. Pick one style and stay consistent — `.copy()` reads most clearly.

---

### Comparison at a glance

```python
import copy

lst = [1, [2, 3]]

alias     = lst
shallow   = lst.copy()
deep_copy = copy.deepcopy(lst)

lst[1][0] = 99

print(alias)       # [1, [99, 3]]  — same object as lst
print(shallow)     # [1, [99, 3]]  — shared inner list
print(deep_copy)   # [1, [2, 3]]   — fully independent
```

| After `lst[1][0] = 99` | Outer list shared? | Inner list shared? |
|------------------------|--------------------|--------------------|
| `alias = lst` | Yes (same object) | Yes |
| `shallow = lst.copy()` | No (new outer list) | Yes |
| `deep = copy.deepcopy(lst)` | No | No |

---

### Connection to list multiplication gotcha

This is why `[[0] * 3] * 3` breaks — you get one inner list referenced three times (shallow repetition, not deep):

```python
# Wrong — three rows, one shared inner list
matrix = [[0] * 3] * 3
matrix[0][0] = 1
print(matrix)   # [[1, 0, 0], [1, 0, 0], [1, 0, 0]]

# Correct — each row is its own list (comprehension builds new inner lists)
matrix = [[0] * 3 for _ in range(3)]
matrix[0][0] = 1
print(matrix)   # [[1, 0, 0], [0, 0, 0], [0, 0, 0]]
```

The comprehension creates a **new** inner list on each iteration. Multiplication reuses the **same** reference.


---

## Common Mistakes & Gotchas

1. **Forgetting that lists are mutable** — changes affect all references
2. **Modifying list while iterating** — can cause skipping or infinite loops
3. **Using `+` in a loop** to build large lists (very slow)
4. **Assuming sort() returns a new list** (it sorts in place)
5. **List multiplication with mutable items** can cause unexpected behavior

**Wrong:**
```python
matrix = [[0] * 3] * 3   # All rows reference same list!
```

**Correct:**
```python
matrix = [[0] * 3 for _ in range(3)]
```

---

## Best Practices

- Use meaningful variable names
- Prefer list comprehensions for simple transformations
- Use `append()` instead of `+` when building lists
- Consider using tuples for immutable collections
- Use `enumerate()` when you need both index and value
- Keep lists homogeneous when possible for clarity

---

## See also

- [[11 - break, continue, pass]] — prev note; pitfalls of modifying a list while iterating
- [[10 - For loop]] — iterating lists and the loop logic behind comprehensions
- [[02 - Variables and Data Types]] — mutability, references, and why `=` is not a copy
- [[13 - Tuples]] — ordered sequences that cannot be changed after creation
- [[07 - Comparisons and Logical Operators]] — membership with `in` and filtering in comprehensions

---

## → What's Next

Lists are foundational for Python programming. With a strong understanding of lists, you're ready to explore key-value data structures.

Continue with **[[13 - Tuples]]**
