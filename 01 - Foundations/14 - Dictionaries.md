---
tags:
  - foundations
  - python
  - data-structures
stage: 1
difficulty: Beginner
---

# Dictionaries in Python

**Prev:** [[13 - Tuples]] | **Next:** [[15 - Sets]]

> Dictionaries are one of Python’s most powerful and frequently used data structures. They store data in **key-value** pairs, allowing fast lookup, insertion, and deletion.

---

## What is a Dictionary?

A **dictionary** (`dict`) is a mutable collection of **key-value** pairs. Each key must be **unique** and **hashable**. Since Python 3.7, dictionaries also **preserve insertion order** — the order in which keys were first added.

```python
# Examples
person = {
    "name": "Alice",
    "age": 25,
    "city": "Tbilisi",
    "is_student": False
}

student_scores = {
    "math": 95,
    "physics": 88,
    "history": 76
}

empty_dict = {}
empty_dict2 = dict()
```

**Key Characteristics:**
- Keys must be **hashable** (explained later in this note)
- Values can be any data type (including lists, other dicts, etc.)
- Fast average O(1) lookup time
- Maintains **insertion order** (Python 3.7+, explained later)
- Keys are unique

---

## Creating Dictionaries

```python
# Different ways to create dictionaries

# 1. Literal syntax
person = {"name": "Alice", "age": 25}

# 2. Using dict() constructor
person2 = dict(name="Alice", age=25)

# 3. From list of tuples
items = [("name", "Alice"), ("age", 25)]
person3 = dict(items)

# 4. Dictionary comprehension
squares = {x: x**2 for x in range(6)}
```

---

## Accessing and Modifying

### Accessing Values

```python
person = {"name": "Alice", "age": 25, "city": "Tbilisi"}

print(person["name"])                    # Alice
print(person["phone"])                   # Raises KeyError

# Safer way - avoids KeyError
print(person.get("name"))                # Alice
print(person.get("phone", "Not found"))  # Not found (default value)
```

### Adding / Updating Items

```python
person["email"] = "alice@example.com"   # Add new key
person["age"] = 26                       # Update existing key

# Update multiple values
person.update({"city": "Batumi", "job": "Developer"})
```

### Removing Items

```python
del person["age"]                        # Delete by key
removed = person.pop("city")             # Remove and return value
person.popitem()                         # Remove last inserted item
person.clear()                           # Remove all items
```

---

## Important Dictionary Methods

| Method              | Description                                   | Example |
|---------------------|-----------------------------------------------|--------|
| `get(key, default)` | Get value with default fallback               | `d.get('key', 0)` |
| `keys()`            | View of all keys                              | `d.keys()` |
| `values()`          | View of all values                            | `d.values()` |
| `items()`           | View of (key, value) pairs                    | `d.items()` |
| `update()`          | Update with another dict                      | `d.update(other)` |
| `pop(key)`          | Remove key and return value                   | `d.pop('key')` |
| `popitem()`         | Remove and return last item                   | `d.popitem()` |
| `setdefault()`      | Set default if key doesn't exist              | `d.setdefault('count', 0)` |
| `copy()`            | Shallow copy                                  | `d.copy()` |

---

## Hashable — what it means and why dict keys need it

A value is **hashable** if Python can compute a fixed **hash number** from it and that number never changes for the lifetime of the object. Dictionaries use that hash to find the right key slot quickly — like an index number that points to where `"name"` lives in memory.

```python
print(hash("name"))     # e.g. -1234567890123456789 (number varies per run)
print(hash(42))         # 42
print(hash((1, 2)))     # works — tuple of hashable items
```

### Hashable vs not hashable

| Hashable (valid keys) | Not hashable (invalid keys) |
|-----------------------|----------------------------|
| `str`, `int`, `float`, `bool` | `list` |
| `tuple` (if every item inside is hashable) | `dict` |
| `frozenset` | `set` |
| `None` | Any custom mutable object |

```python
valid = {
    "name": "Alice",        # str key — OK
    42: "answer",           # int key — OK
    (1, 2): "point",        # tuple key — OK
}

# invalid = {[1, 2]: "oops"}   # TypeError: unhashable type: 'list'
# invalid = {{}: "oops"}       # TypeError: unhashable type: 'dict'
```

**Why mutable objects cannot be keys:** if a list's contents change, its hash would need to change too — but the dictionary already stored it under the *old* hash. You would lose the key or get collisions. Python forbids this at the source by making lists, dicts, and sets unhashable.

```python
key = [1, 2]
# hash(key)   # TypeError: unhashable type: 'list'
```

**Tuples are hashable only when every element is hashable:**

```python
hash(("Alice", 25))       # OK

# hash(("Alice", [85, 90]))   # TypeError — inner list is mutable
```

This connects directly to [[13 - Tuples]] — tuples with only immutable items work as dict keys; tuples containing lists do not.

**Values are not restricted** — only keys must be hashable. A dict can store lists, dicts, or anything else as values:

```python
person = {
    "name": "Alice",
    "grades": [85, 90, 88],      # list as value — fine
    "address": {"city": "Tbilisi"}  # nested dict as value — fine
}
```

---

## Insertion order — what it means

**Insertion order** means: when you loop over a dictionary (or call `.keys()`, `.values()`, `.items()`), items appear in the order the keys were **first inserted**, not sorted alphabetically or by hash.

```python
person = {}
person["name"] = "Alice"
person["age"] = 25
person["city"] = "Tbilisi"

for key in person:
    print(key)
# name
# age
# city
```

### What changes order and what does not

**Updating an existing key does not move it** — it stays in its original position:

```python
person = {"name": "Alice", "age": 25, "city": "Tbilisi"}
person["age"] = 26          # update value only

list(person.keys())         # ['name', 'age', 'city'] — same order
```

**Deleting and re-adding puts the key at the end** (new insertion):

```python
person = {"name": "Alice", "age": 25, "city": "Tbilisi"}
del person["age"]
person["age"] = 26

list(person.keys())         # ['name', 'city', 'age'] — 'age' moved to end
```

**`popitem()` removes the last inserted pair** (LIFO — last in, first out):

```python
person = {"a": 1, "b": 2, "c": 3}
person.popitem()            # ('c', 3) — last inserted
```

### Before Python 3.7

In older Python versions, dictionary order was **not guaranteed** — keys could appear in any order between runs. Code that relied on order was fragile. Since **Python 3.7**, insertion order is part of the language specification, so you can rely on it in modern code.

Do not confuse insertion order with **sorted order**. If you need alphabetical keys, sort explicitly:

```python
for key in sorted(person):
    print(key, person[key])
```

---

## Iterating Over Dictionaries

```python
person = {"name": "Alice", "age": 25, "city": "Tbilisi"}

# Iterate over keys
for key in person:
    print(key, person[key])

# Better: iterate over items
for key, value in person.items():
    print(f"{key}: {value}")

# Iterate over keys only
for key in person.keys():
    ...

# Iterate over values only
for value in person.values():
    ...
```

---

## Dictionary Comprehensions

```python
# Create dict from range
squares = {x: x*x for x in range(1, 6)}

# Filter data
high_scores = {subject: score for subject, score in student_scores.items() if score >= 80}

# Transform keys/values
upper_keys = {k.upper(): v for k, v in person.items()}
```

---

## Real-World Examples

### 1. Simple Phone Book

```python
phone_book = {}

phone_book["Alice"] = "+995 555 123 456"
phone_book["Anna"] = "+995 577 987 654"

name = input("Enter name: ")
print(phone_book.get(name, "Contact not found"))
```

### 2. Word Frequency Counter

```python
text = "python is great python is fun"
words = text.split()

frequency = {}
for word in words:
    frequency[word] = frequency.get(word, 0) + 1

print(frequency)
```

---

## Advanced Features

### Nested Dictionaries

```python
students = {
    "Alice": {
        "age": 25,
        "grades": {"math": 95, "physics": 88}
    },
    "anna": {
        "age": 23,
        "grades": {"math": 92, "physics": 90}
    }
}

print(students["Alice"]["grades"]["math"])
```


---

## Common Mistakes & Gotchas

1. **Accessing non-existent key** with `dict[key]` → `KeyError`
2. **Using mutable objects as keys** (lists, dicts) → TypeError
3. **Modifying dictionary while iterating** (can cause RuntimeError)
4. **Confusing `get()` with direct access**
5. **Assuming order before Python 3.7**

---

## Best Practices

- Use meaningful keys
- Prefer `.get()` over direct access when key may not exist
- Use `defaultdict` from `collections` when counting or grouping
- Keep dictionaries reasonably flat when possible

---

## → What's Next

Dictionaries are incredibly versatile for organizing and accessing data by meaningful keys. Next, you'll learn about Sets — another important data structure.

Continue with **[[15 - Sets]]**
