---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# Generators and Iterators

**Prev:** [[13 - Decorators]] | **Next:** [[15 - Context Managers]]

> In this lesson you will learn about **Iterators** and **Generators** — the machinery behind every `for` loop you've ever written. You will learn how to build your own iterable objects, and how `yield` lets you write memory-efficient, lazy sequences.

---

## What Actually Happens in a `for` Loop?

You've written loops like this many times:

```python
for item in [1, 2, 3]:
    print(item)
```

Under the hood, Python is doing something more specific: it calls `iter()` on the list to get an **iterator**, then repeatedly calls `next()` on that iterator until it's exhausted.

```python
numbers = [1, 2, 3]

iterator = iter(numbers)   # Get an iterator from the list

print(next(iterator))   # 1
print(next(iterator))   # 2
print(next(iterator))   # 3
print(next(iterator))   # StopIteration!
```

A `for` loop is just a convenient wrapper around this exact pattern — it calls `next()` repeatedly and stops automatically when it catches `StopIteration`.

---

## Iterables vs. Iterators

These two terms are related but different:

| Term          | Meaning                                             | Requires             |
| --------------- | ------------------------------------------------------ | ----------------------- |
| **Iterable**    | Something you *can* loop over (`for x in it:`)          | `__iter__`               |
| **Iterator**    | The object that actually produces values one at a time | `__iter__` and `__next__` |

A list is **iterable** — it has `__iter__`, which returns an **iterator**. The iterator has `__next__`, which produces one value at a time.

```python
numbers = [1, 2, 3]

print(hasattr(numbers, "__iter__"))    # True  - list is iterable
print(hasattr(numbers, "__next__"))    # False - list itself is not the iterator

iterator = iter(numbers)
print(hasattr(iterator, "__next__"))   # True  - the iterator can produce values
```

---

## Building a Custom Iterator

You can make your own class iterable by implementing the two dunder methods from [[07 - OOP — Dunder Methods]]: `__iter__` and `__next__`.

```python
class CountUp:
    def __init__(self, start, end):
        self.current = start
        self.end = end

    def __iter__(self):
        """Returns the iterator object itself"""
        return self

    def __next__(self):
        """Returns the next value, or raises StopIteration when done"""
        if self.current > self.end:
            raise StopIteration
        value = self.current
        self.current += 1
        return value


for number in CountUp(1, 5):
    print(number)
```

**Output:**
```
1
2
3
4
5
```

This works with `for`, `list()`, `sum()`, and anything else that expects an iterable — because it follows the same protocol as every built-in sequence.

---

## The Problem with Manual Iterators

Writing `__iter__` and `__next__` by hand works, but it's verbose — you have to manually track state (`self.current`) between calls. Generators solve this problem.

---

## Generator Functions: `yield`

A **generator function** looks like a normal function, but uses `yield` instead of `return`. Each call to `yield` pauses the function, remembering exactly where it left off.

```python
def count_up(start, end):
    current = start
    while current <= end:
        yield current
        current += 1


for number in count_up(1, 5):
    print(number)
```

**Output:**
```
1
2
3
4
5
```

Same result as `CountUp` above, with far less code — no `__iter__`, `__next__`, or manual state tracking required. Python builds the iterator for you automatically.

### How `yield` Works Step by Step

Calling a generator function doesn't run its body immediately — it returns a **generator object**:

```python
def count_up(start, end):
    print("Starting!")
    current = start
    while current <= end:
        yield current
        current += 1

gen = count_up(1, 3)
print(gen)          # <generator object count_up at 0x...>

print(next(gen))     # Starting!   -> 1
print(next(gen))     # 2   (resumes right after the yield)
print(next(gen))     # 3
print(next(gen))     # StopIteration!
```

Each `next()` call resumes the function exactly where it paused, runs until the next `yield`, and pauses again.

---

## Why Use Generators? Memory Efficiency

The biggest advantage of generators is that they produce values **lazily** — one at a time, on demand — instead of building the entire sequence in memory at once.

```python
def all_squares_list(n):
    """Builds the ENTIRE list in memory before returning"""
    return [x * x for x in range(n)]

def all_squares_gen(n):
    """Produces one value at a time, using almost no memory"""
    for x in range(n):
        yield x * x


# Both produce the same values...
squares_list = all_squares_list(1_000_000)   # Allocates a full list of 1,000,000 ints
squares_gen = all_squares_gen(1_000_000)      # Allocates almost nothing until consumed

print(sum(squares_gen))   # Values are generated and consumed one at a time
```

For huge or even infinite sequences, generators make the difference between a program that works and one that crashes from running out of memory.

---

## Generator Expressions

Just like list comprehensions, there's a compact syntax for simple generators — swap `[]` for `()`.

```python
squares_list = [x * x for x in range(5)]   # List comprehension - built immediately
squares_gen = (x * x for x in range(5))     # Generator expression - lazy

print(squares_list)   # [0, 1, 4, 9, 16]
print(squares_gen)    # <generator object <genexpr> at 0x...>
print(list(squares_gen))  # [0, 1, 4, 9, 16] - values only computed here
```

Use a generator expression whenever you're going to consume the values once (e.g., pass them to `sum()`, `max()`, or a `for` loop) and don't need to keep the whole sequence around.

---

## Infinite Generators

Because values are produced lazily, generators can represent sequences that never end — something a list could never do.

```python
def infinite_counter(start=0):
    current = start
    while True:
        yield current
        current += 1


counter = infinite_counter()
print(next(counter))   # 0
print(next(counter))   # 1
print(next(counter))   # 2
# ... would continue forever if you kept calling next()
```

This is only safe because nothing is computed until you actually ask for the next value.

---

## Real-World Example: Reading Large Files

```python
def read_large_file(file_path):
    """Yields one line at a time instead of loading the whole file into memory"""
    with open(file_path) as f:
        for line in f:
            yield line.strip()


for line in read_large_file("huge_log.txt"):
    if "ERROR" in line:
        print(line)
```

This pattern lets you process files far larger than available memory, since only one line exists in memory at any given moment.

---

## Benefits of Generators

1. **Memory Efficient** — Values are produced one at a time, not stored all at once
2. **Lazy Evaluation** — Nothing is computed until it's actually needed
3. **Can Represent Infinite Sequences** — Impossible with a list
4. **Simpler Code** — No need to manually implement `__iter__`/`__next__`
5. **Composable** — Generators can be chained together to build efficient data pipelines

---

## Common Mistakes

1. Trying to iterate over a generator **twice** — once exhausted, it's empty
2. Using `[x for x in huge_range]` when a generator expression `(x for x in huge_range)` would avoid building the whole list
3. Forgetting that a generator function's body **doesn't run at all** until you call `next()` or iterate over it
4. Mixing up `yield` and `return` — a `return` inside a generator ends iteration (raises `StopIteration`), it doesn't yield a value
5. Trying to get the length of a generator with `len()` — generators don't know their length in advance

```python
def gen():
    yield 1
    yield 2

g = gen()
print(list(g))   # [1, 2]
print(list(g))   # [] - already exhausted!
```

---

## Best Practices

1. **Prefer generators over lists** when you only need to iterate once, especially for large data
2. **Use generator expressions** `(x for x in ...)` for simple cases instead of full generator functions
3. **Use generator functions with `yield`** when the logic is too complex for a single expression
4. **Chain generators** to build efficient step-by-step data pipelines instead of materializing intermediate lists
5. **Reach for a list** only when you actually need to store, re-iterate, index, or measure the length of the values

---

## See also

- [[07 - OOP — Dunder Methods]] — `__iter__` and `__next__` as part of the dunder method protocol
- [[13 - Decorators]] — decorators are often used to wrap generator-based functions
- [[16 - Defining Functions]] — the function fundamentals `yield`-based generators build on
- [[19 - Lambda Functions]] — anonymous functions, sometimes combined with generator expressions
- [[15 - Context Managers]] — the `with` statement, and how `yield` builds context managers too

---

## → What's next

You now understand **Generators and Iterators**:

- How `for` loops actually work via `__iter__` and `__next__`
- How to build a custom iterator class from scratch
- How `yield` lets you write generator functions with far less code
- Why generators are memory-efficient, lazy, and can represent infinite sequences

In the next lesson, we will explore **Context Managers** — the `with` statement you saw briefly in [[07 - OOP — Dunder Methods]], covered in depth, including how to build one with a generator instead of a class.

Continue with **[[15 - Context Managers]]**
