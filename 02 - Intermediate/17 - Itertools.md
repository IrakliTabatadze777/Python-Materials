---
tags:
  - intermediate
  - python
  - functions
stage: 2
difficulty: Intermediate
---

# Itertools

**Prev:** [[16 - Functools]] | **Next:** [[18 - Typing and Type Hints]]

> In this lesson you will learn about the **`itertools`** module — Python's standard-library toolbox for working with iterators, building directly on [[14 - Generators and Iterators]]. You'll learn how to combine, filter, and generate sequences lazily, without ever building a full list in memory.

---

## What is `itertools`?

`itertools` is a built-in module of fast, memory-efficient functions for working with **iterables**. Every function in it returns a lazy iterator — values are only computed as they're requested, exactly like the generators from [[14 - Generators and Iterators]].

```python
import itertools
```

---

## Infinite Iterators

### `count()`: An Infinite Counter

```python
from itertools import count

for i in count(start=10, step=2):
    print(i)
    if i > 16:
        break
```

**Output:**
```
10
12
14
16
18
```

`count()` never stops on its own — like the `infinite_counter` generator from [[14 - Generators and Iterators]], you need a `break` or a limiting function like `islice` to bound it.

### `cycle()`: Repeating a Sequence Forever

```python
from itertools import cycle

colors = cycle(["red", "green", "blue"])

for _ in range(7):
    print(next(colors))
```

**Output:**
```
red
green
blue
red
green
blue
red
```

Useful for round-robin assignment — cycling through a fixed set of options indefinitely.

### `repeat()`: Repeating One Value

```python
from itertools import repeat

for value in repeat("hello", 3):
    print(value)
```

**Output:**
```
hello
hello
hello
```

---

## Combining Iterables

### `chain()`: Treating Several Iterables as One

```python
from itertools import chain

list1 = [1, 2, 3]
list2 = [4, 5, 6]

for number in chain(list1, list2):
    print(number)
```

**Output:**
```
1
2
3
4
5
6
```

`chain()` avoids building an intermediate combined list (`list1 + list2` would) — it walks through each iterable in turn, lazily.

### `zip_longest()`: Zipping Without Truncating

The built-in `zip()` stops at the shortest iterable. `zip_longest()` continues until the longest one is exhausted, filling in gaps.

```python
from itertools import zip_longest

names = ["Alice", "Bob", "Carol"]
scores = [90, 85]

for name, score in zip_longest(names, scores, fillvalue="N/A"):
    print(name, score)
```

**Output:**
```
Alice 90
Bob 85
Carol N/A
```

---

## Filtering and Slicing

### `islice()`: Slicing Any Iterable, Including Infinite Ones

Regular slicing (`some_list[2:5]`) only works on sequences. `islice()` works on **any** iterable, including infinite ones from `count()` or generators.

```python
from itertools import islice, count

first_five_even = islice(count(0, 2), 5)
print(list(first_five_even))   # [0, 2, 4, 6, 8]
```

This is the standard way to safely take a finite number of values from an infinite iterator.

### `filterfalse()`: The Opposite of `filter()`

```python
from itertools import filterfalse

numbers = range(10)

odds = filterfalse(lambda x: x % 2 == 0, numbers)
print(list(odds))   # [1, 3, 5, 7, 9]
```

### `takewhile()` and `dropwhile()`

```python
from itertools import takewhile, dropwhile

numbers = [1, 3, 5, 8, 9, 11]

print(list(takewhile(lambda x: x % 2 != 0, numbers)))   # [1, 3, 5] - stops at first even number
print(list(dropwhile(lambda x: x % 2 != 0, numbers)))   # [8, 9, 11] - drops until first even number
```

`takewhile` keeps values only **while** the condition holds, then stops entirely (even if later values would also pass). `dropwhile` does the opposite — it skips values until the condition first fails, then keeps everything after.

---

## Generating Combinations

### `product()`: Cartesian Product

```python
from itertools import product

sizes = ["S", "M", "L"]
colors = ["red", "blue"]

for size, color in product(sizes, colors):
    print(size, color)
```

**Output:**
```
S red
S blue
M red
M blue
L red
L blue
```

This replaces a nested `for` loop — `product(sizes, colors)` is equivalent to `for size in sizes: for color in colors:` but reads as a single, flat expression.

### `permutations()`: All Orderings

```python
from itertools import permutations

for order in permutations(["A", "B", "C"]):
    print(order)
```

**Output:**
```
('A', 'B', 'C')
('A', 'C', 'B')
('B', 'A', 'C')
('B', 'C', 'A')
('C', 'A', 'B')
('C', 'B', 'A')
```

Pass a second argument to limit the length: `permutations(["A", "B", "C"], 2)` gives all ordered pairs.

### `combinations()`: All Selections, Order Doesn't Matter

```python
from itertools import combinations

for pair in combinations(["A", "B", "C"], 2):
    print(pair)
```

**Output:**
```
('A', 'B')
('A', 'C')
('B', 'C')
```

Unlike `permutations`, `combinations` treats `('A', 'B')` and `('B', 'A')` as the same selection — only one appears.

---

## Grouping Data

### `groupby()`: Grouping Consecutive Items

```python
from itertools import groupby

words = ["apple", "ant", "banana", "bear", "cat"]

for letter, group in groupby(words, key=lambda w: w[0]):
    print(letter, list(group))
```

**Output:**
```
a ['apple', 'ant']
b ['banana', 'bear']
c ['cat']
```

**Important:** `groupby()` only groups *consecutive* matching items — it doesn't sort first. Sort by the same key beforehand if your data isn't already ordered:

```python
words = ["apple", "banana", "ant", "cat", "bear"]

sorted_words = sorted(words, key=lambda w: w[0])
for letter, group in groupby(sorted_words, key=lambda w: w[0]):
    print(letter, list(group))
```

---

## Real-World Example: Batch Processing

```python
from itertools import islice

def batched(iterable, batch_size):
    """Yield successive batches from an iterable"""
    iterator = iter(iterable)
    while batch := list(islice(iterator, batch_size)):
        yield batch


records = range(1, 11)

for batch in batched(records, 3):
    print(f"Processing batch: {batch}")
```

**Output:**
```
Processing batch: [1, 2, 3]
Processing batch: [4, 5, 6]
Processing batch: [7, 8, 9]
Processing batch: [10]
```

This is a common real-world pattern — sending API requests, database writes, or file uploads in manageable chunks instead of all at once. (Python 3.12+ actually includes this exact function as `itertools.batched`.)

---

## Benefits of `itertools`

1. **Memory Efficient** — Every function returns a lazy iterator, never building a full list unnecessarily
2. **Composable** — These tools chain together naturally to build small data pipelines
3. **Battle-Tested** — Implemented in C internally, making them faster than equivalent hand-written loops
4. **Expressive** — `product()`, `combinations()`, and `groupby()` replace nested loops with clear, named intent
5. **Works with Infinite Sequences** — `count()` and `cycle()` integrate naturally with `islice()` to bound them safely

---

## Common Mistakes

1. Forgetting that `groupby()` only groups **consecutive** items — always sort first if the data isn't already grouped
2. Using `count()` or `cycle()` without a `break` or `islice()`, causing an infinite loop
3. Confusing `permutations()` (order matters) with `combinations()` (order doesn't matter)
4. Forgetting these return **iterators**, not lists — wrap in `list(...)` if you need to inspect or reuse the result
5. Exhausting an itertools iterator and then trying to iterate over it again, getting nothing back

---

## Best Practices

1. **Reach for `itertools` before writing a manual nested loop** for combinations, products, or grouping
2. **Always sort before `groupby()`** unless you know the data is already grouped
3. **Use `islice()` to safely bound infinite iterators** like `count()` and `cycle()`
4. **Convert to `list()` only when you actually need to store or re-iterate** the results — otherwise keep it lazy
5. **Combine `itertools` with generator expressions** from [[14 - Generators and Iterators]] for efficient, readable data pipelines

---

## See also

- [[14 - Generators and Iterators]] — the iterator protocol every `itertools` function builds on
- [[16 - Functools]] — the companion standard-library module for working with functions
- [[19 - Lambda Functions]] — anonymous functions frequently used as `key` arguments here

---

## → What's next

You now understand **Itertools**:

- How infinite iterators like `count()` and `cycle()` work, and how to bound them safely
- How to combine iterables with `chain()` and `zip_longest()`
- How to generate combinations and permutations without nested loops
- How `groupby()` groups consecutive matching items, and why sorting matters first

In the next lesson, we will explore **Typing and Type Hints** — how to annotate your functions and variables with expected types, catching bugs before your code even runs.

Continue with **[[18 - Typing and Type Hints]]**
