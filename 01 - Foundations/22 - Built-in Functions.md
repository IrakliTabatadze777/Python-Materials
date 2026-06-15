---
tags:
  - foundations
  - python
  - built-in-functions
stage: 1
difficulty: Beginner
---

# Built-in Functions in Python (Core Set)

**Prev:** [[21 - Higher Order Functions]] | **Next:** [[23 - Modules and Imports]]

> In this lesson we focus on the most important built-in functions you will constantly use in Python: `len`, `range`, `enumerate`, `zip`, `sorted`, `map`, `filter`, and `reduce`.

These functions help you write cleaner, more Pythonic code by reducing manual loops and boilerplate.

---

## len()

### What it does
`len()` returns the number of elements in a collection (or characters in a string).

```python
text = "Python"
print(len(text))  # Counts each character
```

**Output:**
```
6
```

### Deep explanation
`len()` works on strings, lists, tuples, sets, and dictionaries. It simply tells you "how many items are there?"

**More examples:**
```python
print(len([10, 20, 30, 40]))      # 4 - list
print(len((1, 2, 3)))             # 3 - tuple
print(len({"a": 1, "b": 2}))      # 2 - dictionary (counts keys)
print(len(set([1, 1, 2, 3])))     # 3 - set (unique elements)
```

---

## range()

### What it does
`range()` generates a sequence of numbers efficiently without storing them all in memory.

```python
# Basic usage: numbers from 0 up to (but not including) 5
for i in range(5):
    print(i)
```

**Output:**
```
0
1
2
3
4
```

### Deep explanation & Common patterns

```python
# 1. Only stop value (starts from 0)
for i in range(5):
    print(i)

# 2. Start and stop
for i in range(2, 8):           # from 2 to 7
    print(i)

# 3. With step (how much to jump)
for i in range(0, 11, 2):       # 0, 2, 4, 6, 8, 10
    print(i)

# 4. Counting backwards
for i in range(10, 0, -1):
    print(i)
```

**Very common pattern:**
```python
fruits = ["apple", "banana", "cherry"]
for i in range(len(fruits)):      # loop by index
    print(i, fruits[i])
```

`range()` is **lazy** — it generates numbers only when needed, saving memory.

---

## enumerate()

### What it does
`enumerate()` gives you both the **index** and the **value** while looping.

```python
fruits = ["apple", "banana", "cherry"]

for index, fruit in enumerate(fruits):
    print(index, fruit)
```

**Output:**
```
0 apple
1 banana
2 cherry
```

### Why it's useful

```python
# Without enumerate (manual and error-prone)
fruits = ["apple", "banana", "cherry"]
i = 0
for fruit in fruits:
    print(i, fruit)
    i += 1

# With enumerate (clean)
for index, fruit in enumerate(fruits, start=1):  # start from 1 instead of 0
    print(f"{index}. {fruit}")
```

---

## zip()

### What it does
`zip()` pairs items from multiple lists (or other iterables) together.

```python
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]

for name, age in zip(names, ages):
    print(f"{name} is {age} years old")
```

**Output:**
```
Alice is 25 years old
Bob is 30 years old
Charlie is 35 years old
```

**Key point:** It stops at the shortest list.

---

## sorted()

### What it does
`sorted()` returns a **new** sorted list. The original list stays unchanged.

```python
numbers = [5, 2, 9, 1, 8]
sorted_numbers = sorted(numbers)

print("Original:", numbers)
print("Sorted:  ", sorted_numbers)
```

**Output:**
```
Original: [5, 2, 9, 1, 8]
Sorted:   [1, 2, 5, 8, 9]
```

**Advanced usage:**
```python
# Descending order
print(sorted(numbers, reverse=True))

# Sort by custom rule (e.g., length of strings)
words = ["python", "is", "awesome", "code"]
print(sorted(words, key=len))
```

---

## map()

### What it does
`map()` is a **higher-order function** — it takes another function as an argument and applies it to every item in a list (or iterable).

It is **lazy**: it doesn't do the work immediately. You need `list()` to see the results.

```python
numbers = [1, 2, 3, 4, 5]

# Using lambda - a small anonymous function
doubled = map(lambda x: x * 2, numbers)
print(list(doubled))
```

**Output:**
```
[2, 4, 6, 8, 10]
```

### What is `lambda`?

`lambda x: x * 2` is a short way to write a small function without giving it a name.

It means:
- Take input `x`
- Return `x * 2`

**More readable version with a named function:**

```python
def double(x):
    return x * 2

numbers = [1, 2, 3, 4, 5]
doubled = map(double, numbers)   # pass the function as argument
print(list(doubled))
```

**Step-by-step what `map()` does:**
1. Takes the function `double`
2. Takes the list `[1, 2, 3, 4, 5]`
3. Applies `double(1)`, `double(2)`, ... one by one
4. Returns the results lazily

**With multiple lists:**
```python
a = [1, 2, 3]
b = [10, 20, 30]

result = list(map(lambda x, y: x + y, a, b))
print(result)  # [11, 22, 33]
```

---

## filter()

### What it does
`filter()` is also a **higher-order function**. It takes a function (that returns `True` or `False`) and keeps only the items where the function returns `True`.

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Keep only even numbers
evens = filter(lambda x: x % 2 == 0, numbers)
print(list(evens))
```

**Output:**
```
[2, 4, 6, 8, 10]
```

### Understanding the condition

`lambda x: x % 2 == 0` means:
- Take `x`
- Check if `x` divided by 2 has no remainder (`== 0`)
- Return `True` or `False`

**With a named function (easier to read):**

```python
def is_even(n):
    return n % 2 == 0

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
evens = list(filter(is_even, numbers))
print(evens)
```

---

## reduce()

### What it does
`reduce()` is a **higher-order function** from the `functools` module. It takes a function and "reduces" the entire list into a single value by repeatedly applying the function.

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]

# Sum all numbers
total = reduce(lambda a, b: a + b, numbers)
print(total)   # 15
```

**Step-by-step how it works:**
```
Start:          1 + 2  →  3
Next:           3 + 3  →  6
Next:           6 + 4  → 10
Next:          10 + 5  → 15
```

### With named function and initial value

```python
from functools import reduce

def add(x, y):
    return x + y

numbers = [1, 2, 3, 4, 5]

# Start with 100
total = reduce(add, numbers, 100)
print(total)   # 115
```

**Other uses:**
```python
# Find the maximum number
maximum = reduce(lambda a, b: a if a > b else b, numbers)
print(maximum)  # 5
```

---

## Summary Table

| Function     | Purpose                        | Returns             | Lazy? | Higher-order? |
|--------------|--------------------------------|---------------------|-------|---------------|
| `len()`      | Count items                    | `int`               | No    | No            |
| `range()`    | Generate numbers               | `range` object      | Yes   | No            |
| `enumerate()`| Add index to items             | `enumerate` object  | Yes   | No            |
| `zip()`      | Combine multiple iterables     | `zip` object        | Yes   | No            |
| `sorted()`   | Return sorted list             | `list`              | No    | No            |
| `map()`      | Apply function to each item    | `map` object        | Yes   | Yes           |
| `filter()`   | Keep items by condition        | `filter` object     | Yes   | Yes           |
| `reduce()`   | Combine into single value      | single value        | No    | Yes           |

---

## See also

- [[10 - For loop]] — `range`, `enumerate`, and `zip` are loop companions
- [[12 - Lists]] — list comprehensions vs `map` and `filter`
- [[21 - Higher Order Functions]] — `map`, `filter`, `reduce` accept functions
- [[19 - Lambda Functions]] — lambdas with `sorted`, `map`, and `filter`
- [[03 - Strings]] — `len` on strings, `sorted` on characters

---

## → What's Next

Now that you understand Python’s core built-in functions, the next step is learning how to organize code into multiple files using **modules and imports**.

Continue with **[[23 - Modules and Imports]]**
