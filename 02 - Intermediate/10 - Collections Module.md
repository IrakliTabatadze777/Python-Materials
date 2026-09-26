---
tags:
  - intermediate
  - python
  - collections
stage: 2
difficulty: Intermediate
---

# Collections Module

**Prev:** [[09 - Enums]] | **Next:** [[11 - Closures]]

> In this lesson you will learn about the **`collections`** module — a standard-library toolbox of specialized data structures that extend Python's built-in `list`, `dict`, and `set`. You'll learn how `Counter`, `defaultdict`, `namedtuple`, `deque`, and `OrderedDict` each solve a specific, common problem more cleanly than plain built-ins.

---

## Why Not Just Use the Built-Ins?

Lists, dicts, and sets handle most cases well, but some very common patterns require extra, repetitive code around them. `collections` provides purpose-built alternatives for exactly those patterns.

```python
import collections
```

---

## `Counter`: Counting Things

Counting occurrences with a plain `dict` requires manual bookkeeping:

```python
word = "mississippi"

counts = {}
for letter in word:
    counts[letter] = counts.get(letter, 0) + 1

print(counts)   # {'m': 1, 'i': 4, 's': 4, 'p': 2}
```

`Counter` does this in one line:

```python
from collections import Counter

word = "mississippi"
counts = Counter(word)

print(counts)   # Counter({'i': 4, 's': 4, 'p': 2, 'm': 1})
```

### Useful `Counter` Methods

```python
from collections import Counter

votes = Counter(["red", "blue", "red", "green", "blue", "red"])

print(votes.most_common())      # [('red', 3), ('blue', 2), ('green', 1)]
print(votes.most_common(1))     # [('red', 3)] - just the top result
print(votes["red"])              # 3
print(votes["yellow"])           # 0 - missing keys don't raise KeyError!
```

### Combining Counters

```python
from collections import Counter

inventory_a = Counter({"apples": 10, "bananas": 5})
inventory_b = Counter({"apples": 3, "oranges": 7})

total = inventory_a + inventory_b
print(total)   # Counter({'apples': 13, 'oranges': 7, 'bananas': 5})
```

`Counter` instances can be added, subtracted, and compared, treating counts like a mathematical multiset.

---

## `defaultdict`: Dictionaries with Automatic Defaults

A common annoyance with regular dicts is handling missing keys when building up grouped data:

```python
words = ["apple", "banana", "avocado", "blueberry", "cherry"]

groups = {}
for word in words:
    first_letter = word[0]
    if first_letter not in groups:
        groups[first_letter] = []
    groups[first_letter].append(word)

print(groups)   # {'a': ['apple', 'avocado'], 'b': ['banana', 'blueberry'], 'c': ['cherry']}
```

`defaultdict` removes the `if first_letter not in groups` check entirely:

```python
from collections import defaultdict

words = ["apple", "banana", "avocado", "blueberry", "cherry"]

groups = defaultdict(list)
for word in words:
    groups[word[0]].append(word)

print(dict(groups))   # {'a': ['apple', 'avocado'], 'b': ['banana', 'blueberry'], 'c': ['cherry']}
```

`defaultdict(list)` takes a **factory function** — when a missing key is accessed, it automatically calls that factory (here, `list()`) to create the default value, instead of raising a `KeyError`.

```python
from collections import defaultdict

# Common factories
counts = defaultdict(int)     # Missing keys default to 0
groups = defaultdict(list)     # Missing keys default to []
nested = defaultdict(dict)     # Missing keys default to {}

counts["apples"] += 1   # Works even though "apples" was never set before
print(counts["apples"])   # 1
```

This connects to [[09 - Enums]] and closures from [[11 - Closures]]: the factory passed to `defaultdict` is just a callable, so you can pass any zero-argument function, including a `lambda` or a small closure that returns a custom default.

---

## `namedtuple`: Tuples with Named Fields

A plain tuple works, but accessing fields by position (`point[0]`, `point[1]`) isn't very readable.

```python
point = (3, 4)
print(point[0], point[1])   # 3 4 - what do these even mean?
```

`namedtuple` gives tuple fields actual names, while keeping tuples' immutability and efficiency:

```python
from collections import namedtuple

Point = namedtuple("Point", ["x", "y"])

p = Point(3, 4)
print(p.x, p.y)    # 3 4 - much clearer
print(p[0], p[1])   # 3 4 - still works like a regular tuple
print(p)             # Point(x=3, y=4)
```

`namedtuple` predates [[12 - Dataclasses]] as a way to create small, structured, immutable records — dataclasses are generally preferred for new code, but `namedtuple` remains common in existing codebases and libraries, and is lighter-weight when you truly just need an immutable tuple with names.

```python
# Unpacking still works exactly like a regular tuple
x, y = p
print(x, y)   # 3 4
```

---

## `deque`: Fast Appends and Pops from Both Ends

A regular `list` is fast at adding/removing from the **end**, but slow at the **beginning** — removing the first element requires shifting every other element over.

```python
from collections import deque

queue = deque(["task1", "task2", "task3"])

queue.append("task4")        # Fast: add to the right
queue.appendleft("task0")     # Fast: add to the left
print(queue)                   # deque(['task0', 'task1', 'task2', 'task3', 'task4'])

queue.pop()                   # Fast: remove from the right -> 'task4'
queue.popleft()                # Fast: remove from the left -> 'task0'
print(queue)                    # deque(['task1', 'task2', 'task3'])
```

`deque` (pronounced "deck," short for "double-ended queue") is the right choice whenever you need a queue, a stack with both ends active, or a fixed-size rolling history.

### A Bounded `deque`: Automatic Rolling History

```python
from collections import deque

recent_logs = deque(maxlen=3)

for i in range(1, 6):
    recent_logs.append(f"log entry {i}")
    print(list(recent_logs))
```

**Output:**
```
['log entry 1']
['log entry 1', 'log entry 2']
['log entry 1', 'log entry 2', 'log entry 3']
['log entry 2', 'log entry 3', 'log entry 4']
['log entry 3', 'log entry 4', 'log entry 5']
```

Once `maxlen` is reached, adding a new item automatically drops the oldest one — perfect for "keep only the last N" patterns without manual trimming.

---

## `OrderedDict`: Explicit Ordering (Mostly Historical Now)

Since Python 3.7, regular `dict` already preserves insertion order — but `OrderedDict` has one extra trick: `move_to_end()`.

```python
from collections import OrderedDict

cache = OrderedDict()
cache["a"] = 1
cache["b"] = 2
cache["c"] = 3

cache.move_to_end("a")   # Move "a" to the end
print(list(cache.keys()))   # ['b', 'c', 'a']

cache.move_to_end("c", last=False)   # Move "c" to the beginning
print(list(cache.keys()))              # ['c', 'b', 'a']
```

This makes `OrderedDict` genuinely useful for implementing an **LRU (Least Recently Used) cache** by hand — moving recently accessed items to one end, and evicting from the other.

---

## Real-World Example: Word Frequency Report

```python
from collections import Counter

def word_frequency_report(text, top_n=3):
    words = text.lower().split()
    counts = Counter(words)
    return counts.most_common(top_n)


text = "the quick brown fox jumps over the lazy dog the fox runs"
report = word_frequency_report(text)

for word, count in report:
    print(f"{word}: {count}")
```

**Output:**
```
the: 3
fox: 2
quick: 1
```

---

## Real-World Example: Grouping Records with `defaultdict`

```python
from collections import defaultdict

orders = [
    {"customer": "Alice", "item": "Book"},
    {"customer": "Bob", "item": "Pen"},
    {"customer": "Alice", "item": "Laptop"},
    {"customer": "Bob", "item": "Notebook"},
]

orders_by_customer = defaultdict(list)
for order in orders:
    orders_by_customer[order["customer"]].append(order["item"])

for customer, items in orders_by_customer.items():
    print(f"{customer}: {items}")
```

**Output:**
```
Alice: ['Book', 'Laptop']
Bob: ['Pen', 'Notebook']
```

---

## Choosing the Right Tool

| Need                                              | Use              |
| ---------------------------------------------------- | ------------------ |
| Count occurrences of items                            | `Counter`           |
| Group items without checking if a key exists first     | `defaultdict`        |
| A lightweight, named, immutable record                 | `namedtuple`          |
| Fast additions/removals from both ends of a sequence     | `deque`               |
| Rely on move-to-end reordering (e.g., an LRU cache)       | `OrderedDict`          |

---

## Benefits of the `collections` Module

1. **Less Boilerplate** — `Counter` and `defaultdict` eliminate manual existence checks
2. **Clearer Code** — `namedtuple` fields are self-documenting compared to positional tuple access
3. **Better Performance** — `deque` is genuinely faster than a list for operations at both ends
4. **Battle-Tested** — Implemented in C internally, and used throughout the standard library itself
5. **Purpose-Built** — Each tool solves one specific, common problem well, instead of forcing a generic structure to do it awkwardly

---

## Common Mistakes

1. Using a plain `list` as a queue (`pop(0)`) instead of `deque`, which is much slower for that operation on large lists
2. Forgetting that `defaultdict` **creates** a default entry on any missing-key access, even a failed lookup like `some_defaultdict["missing"]` in an `if` check — this can silently grow the dictionary
3. Reaching for `namedtuple` when a mutable [[12 - Dataclasses]]-based class would fit better (namedtuples are immutable)
4. Assuming `OrderedDict` is still necessary just for insertion order — regular `dict` already guarantees that since Python 3.7
5. Forgetting `Counter` handles missing keys gracefully (returning `0`), unlike a plain `dict`, which raises `KeyError`

---

## Best Practices

1. **Reach for `Counter`** any time you're counting occurrences of anything
2. **Reach for `defaultdict`** any time you're grouping or accumulating into a dict by key
3. **Use `deque`** for queues, stacks, or any fixed-size rolling window of recent items
4. **Prefer `namedtuple` for simple, immutable records** in older codebases; prefer `@dataclass` for new code needing mutability or extra features
5. **Check `Counter.most_common()`** before writing manual sorting logic for frequency data

---

## See also

- [[11 - Closures]] — factory functions, useful as `defaultdict` default factories
- [[12 - Dataclasses]] — the modern alternative to `namedtuple` for structured records
- [[09 - Enums]] — another specialized standard-library tool for representing fixed, well-defined values
- [[17 - Itertools]] — the companion standard-library module for working with iterables

---

## → What's next

You now understand the **Collections Module**:

- How `Counter` simplifies counting occurrences and finding the most common items
- How `defaultdict` eliminates manual existence checks when grouping data
- How `namedtuple` makes tuple fields self-documenting
- How `deque` provides fast operations at both ends of a sequence, and how `OrderedDict` supports explicit reordering

In the next lesson, we will explore **Closures** — how a nested function can "remember" variables from the scope it was created in, even after that outer function has finished running.

Continue with **[[11 - Closures]]**
