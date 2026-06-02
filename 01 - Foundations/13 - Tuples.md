---
tags:
  - foundations
  - python
  - data-structures
stage: 1
difficulty: Beginner
---

# Tuples in Python

**Prev:** [[12 - Lists]] | **Next:** [[14 - Dictionaries]]

> Tuples are ordered, immutable collections in Python. While similar to lists, their immutability makes them ideal for data that should not change, offering better performance and data protection.

---

## What is a Tuple?

A **tuple** is an ordered, **immutable** sequence of items. Once created, you cannot add, remove, or modify elements.

```python
# Examples of tuples
coordinates = (10.5, 20.8)
rgb_color = (255, 128, 0)
person = ("Irakli", 25, "Tbilisi")
empty_tuple = ()
single_item = (5,)           # Note the comma!
```

**Key Characteristics:**
- Ordered (elements have positions)
- **Immutable** (cannot be changed after creation)
- Allows duplicates
- Can contain mixed data types
- More memory efficient than lists

---

## Lists vs Tuples

| Feature              | List                     | Tuple                     |
|----------------------|--------------------------|---------------------------|
| Mutability           | Mutable                  | Immutable                 |
| Performance          | Slower                   | Faster                    |
| Memory Usage         | Higher                   | Lower                     |
| Use Case             | Changing data            | Fixed data                |
| Syntax               | `[]`                     | `()`                      |
| Can be Dictionary Key| No                       | Yes (if hashable)         |

---

## Creating Tuples

```python
# Different ways to create tuples
numbers = (1, 2, 3, 4, 5)
fruits = "apple", "banana", "cherry"   # Parentheses are optional
single = (42,)                         # Single item needs comma
from_list = tuple([1, 2, 3])
```

---

## Accessing Elements

### Indexing and Slicing

```python
colors = ("red", "green", "blue", "yellow", "purple")

print(colors[0])      # red
print(colors[-1])     # purple
print(colors[1:4])    # ('green', 'blue', 'yellow')
print(colors[::-1])   # Reversed tuple
```

---

## Tuple Operations

### Concatenation and Repetition

```python
tuple1 = (1, 2, 3)
tuple2 = (4, 5, 6)

combined = tuple1 + tuple2          # (1, 2, 3, 4, 5, 6)
repeated = tuple1 * 3               # (1, 2, 3, 1, 2, 3)
```

### Membership Test

```python
if "red" in colors:
    print("Red is present")
```

### Length and Built-in Functions

```python
print(len(colors))
print(max((10, 5, 8, 12)))    # 12
print(min((10, 5, 8, 12)))    # 5
```

---

## Tuple Unpacking

One of the most powerful features of tuples:

```python
# Basic unpacking
x, y, z = (10, 20, 30)
print(x) # 10
print(y) # 20
print(z) # 30

# Unpacking with * (extended unpacking)
first, *middle, last = (1, 2, 3, 4, 5)
print(first)   # 1
print(middle)  # [2, 3, 4]
print(last)    # 5

# Swapping variables
a, b = 10, 20
a, b = b, a        # Clean swap using tuples
```

---

## Tuple Methods

Tuples have only two built-in methods (because they are immutable):

```python
numbers = (1, 3, 2, 4, 3, 5, 3)

print(numbers.count(3))    # 3
print(numbers.index(4))    # 3 (first occurrence)
```

---

## Nested Tuples

```python
student = ("Irakli", 25, ("Mathematics", "Physics", "Computer Science"))

print(student[2][0])       # Mathematics
```

---

## When to Use Tuples

- **Fixed data** that should not change (coordinates, RGB values, configuration)
- **Dictionary keys** (tuples are hashable)
- **Function returning multiple values**
- **Performance-critical code** (tuples are faster)
- **Data integrity** — protecting data from accidental modification

---

## Common Mistakes & Gotchas

1. **Forgetting the comma** for single-item tuples:
   ```python
   x = (5)      # This is just integer 5, not a tuple!
   y = (5,)     # Correct single-item tuple
   ```

2. Trying to modify a tuple:
   ```python
   t = (1, 2, 3)
   # t[0] = 99    # TypeError!
   ```

3. **Using mutable objects inside tuples** — the tuple is immutable, but its contents may not be:

   ```python
   record = ("Alice", [85, 90, 88])   # name + mutable grades list

   # Cannot replace the list slot — tuple structure is fixed
   # record[1] = [100, 95]            # TypeError: 'tuple' object does not support item assignment

   # But you CAN mutate the list inside the tuple
   record[1].append(92)

   print(record)   # ('Alice', [85, 90, 88, 92])
   ```

   You thought the tuple was "frozen," but the **inner list still changed**. Any code holding a reference to that same list sees the update too:

   ```python
   grades = [85, 90, 88]
   record = ("Alice", grades)

   grades.append(92)        # mutate via the original list reference

   print(record)            # ('Alice', [85, 90, 88, 92])
   print(grades)            # [85, 90, 88, 92] — same list object
   print(record[1] is grades)  # True
   ```

   This also breaks **hashability** — tuples with mutable elements cannot be dictionary keys:

   ```python
   key = ("team", [1, 2, 3])
   # cache = {key: "data"}   # TypeError: unhashable type: 'list'
   ```

   **Fix:** keep tuples fully immutable — use only immutable items inside (numbers, strings, nested tuples), or copy mutable data before storing:

   ```python
   record = ("Alice", tuple([85, 90, 88]))   # inner tuple, not list
   ```

---

## Best Practices

- Use tuples for data that logically should not change
- Prefer tuples when returning multiple values from functions
- Use namedtuples for better readability when you have fixed fields
- Use tuples as dictionary keys when needed
- Keep tuples homogeneous when possible

---

## → What's Next

Tuples provide a safe and efficient way to work with fixed collections. Now you're ready to explore key-value mappings.

Continue with **[[14 - Dictionaries]]**
