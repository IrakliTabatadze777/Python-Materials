---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# Context Managers

**Prev:** [[14 - Generators and Iterators]] | **Next:** [[16 - Functools]]

> In this lesson you will learn about **Context Managers** — the machinery behind the `with` statement. You briefly saw `__enter__` and `__exit__` in [[07 - OOP — Dunder Methods]]; here you'll go deeper, learn why they matter for reliable cleanup, and discover a much shorter way to write one using generators.

---

## The Problem: Reliable Cleanup

Resources like files, network connections, and locks need to be **released** when you're done with them — even if something goes wrong in between.

```python
file = open("notes.txt", "w")
file.write("Hello")
file.close()  # What if an error happens before this line runs?
```

If an exception occurs between `open()` and `close()`, the file is never closed. You could fix this with `try`/`finally`:

```python
file = open("notes.txt", "w")
try:
    file.write("Hello")
finally:
    file.close()  # Always runs, even if an error occurs
```

This works, but it's verbose to repeat every time you open a resource. The `with` statement solves this same problem more cleanly.

```python
with open("notes.txt", "w") as file:
    file.write("Hello")
# File is automatically closed here, even if an error occurred
```

---

## What is a Context Manager?

A **context manager** is any object that defines two dunder methods:

- `__enter__` — runs when the `with` block starts, returns the value bound to `as`
- `__exit__` — runs when the `with` block ends, **always**, even if an exception was raised inside it

```python
class FileManager:
    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode
        self.file = None

    def __enter__(self):
        print("Opening file...")
        self.file = open(self.filename, self.mode)
        return self.file

    def __exit__(self, exc_type, exc_value, traceback):
        print("Closing file...")
        self.file.close()


with FileManager("notes.txt", "w") as f:
    f.write("Hello, context managers!")
```

**Output:**
```
Opening file...
Closing file...
```

`__exit__` runs no matter how the `with` block ends — normally, or because of an exception.

---

## The `__exit__` Parameters

`__exit__` always receives three arguments describing any exception that occurred inside the `with` block:

```python
class SafeDivision:
    def __enter__(self):
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        if exc_type is ZeroDivisionError:
            print("Caught a division by zero!")
            return True   # Suppresses the exception
        return False       # Let any other exception propagate


with SafeDivision():
    result = 10 / 0
    print(result)   # Never reached

print("Program continues normally")
```

**Output:**
```
Caught a division by zero!
Program continues normally
```

- If no exception occurred, all three arguments are `None`
- Returning `True` from `__exit__` **suppresses** the exception
- Returning `False` (or nothing) lets the exception propagate normally after cleanup runs

---

## A Much Shorter Way: `@contextmanager`

Writing a full class with `__enter__` and `__exit__` is a lot of code for something conceptually simple. The `contextlib` module lets you build a context manager from a single generator function instead.

```python
from contextlib import contextmanager

@contextmanager
def file_manager(filename, mode):
    print("Opening file...")
    file = open(filename, mode)
    try:
        yield file          # Everything before yield is __enter__
    finally:
        print("Closing file...")
        file.close()        # Everything after yield is __exit__


with file_manager("notes.txt", "w") as f:
    f.write("Hello, generator-based context managers!")
```

Same output as the class-based version, in far fewer lines:

- Code **before** `yield` runs when the `with` block starts (like `__enter__`)
- The value passed to `yield` becomes the `as` variable
- Code **after** `yield` runs when the `with` block ends (like `__exit__`) — the `try`/`finally` ensures it always runs, even on an exception

This connects directly to [[14 - Generators and Iterators]]: `yield` pauses the function exactly at the point where the `with` block's code runs, then resumes for cleanup once the block finishes.

---

## Handling Exceptions in a Generator-Based Context Manager

```python
from contextlib import contextmanager

@contextmanager
def safe_division():
    try:
        yield
    except ZeroDivisionError:
        print("Caught a division by zero!")


with safe_division():
    result = 10 / 0

print("Program continues normally")
```

If an exception happens inside the `with` block, it's raised **at the `yield` line** inside the generator — so a regular `try`/`except` around the `yield` catches it, exactly like it would around any other code.

---

## Real-World Example: Timing a Block of Code

```python
import time
from contextlib import contextmanager

@contextmanager
def timer(label):
    start = time.perf_counter()
    yield
    elapsed = time.perf_counter() - start
    print(f"{label} took {elapsed:.4f} seconds")


with timer("Data processing"):
    total = sum(x * x for x in range(1_000_000))

print(total)
```

**Output:**
```
Data processing took 0.0842 seconds
500000...
```

This is one of the most common real-world uses of context managers — measuring, logging, or setting up/tearing down state around a specific block of code, without cluttering the code itself.

---

## Real-World Example: Temporarily Changing State

```python
from contextlib import contextmanager

class Config:
    debug = False

@contextmanager
def debug_mode():
    """Temporarily enables debug mode, then restores the original value"""
    original = Config.debug
    Config.debug = True
    try:
        yield
    finally:
        Config.debug = original


print(Config.debug)   # False

with debug_mode():
    print(Config.debug)   # True

print(Config.debug)   # False - restored automatically
```

This pattern — save state, change it, guarantee it's restored — is one of the clearest reasons context managers exist.

---

## Multiple Context Managers at Once

You can open several context managers in a single `with` statement:

```python
with open("input.txt") as infile, open("output.txt", "w") as outfile:
    for line in infile:
        outfile.write(line.upper())
```

Both files are managed correctly — if writing to `outfile` fails partway through, `infile` still gets closed properly.

---

## Class-Based vs. Generator-Based: When to Use Each

| Approach                | Best for                                             |
| -------------------------- | ------------------------------------------------------- |
| Class (`__enter__`/`__exit__`) | Reusable context managers needing extra methods or state |
| `@contextmanager` generator     | Simple, one-off setup/teardown logic — usually shorter and clearer |

Both produce objects that work identically with `with` — the generator version is just a more concise way to express the same `__enter__`/`__exit__` protocol.

---

## Benefits of Context Managers

1. **Guaranteed Cleanup** — Resources are released even when exceptions occur
2. **Less Boilerplate** — No repeated `try`/`finally` blocks scattered throughout your code
3. **Clear Scope** — The `with` block visually marks exactly where a resource is "in use"
4. **Composable** — Multiple context managers can be combined in one `with` statement
5. **Two Ways to Build Them** — Choose a class or a generator, whichever fits the situation better

---

## Common Mistakes

1. Forgetting the `try`/`finally` inside a `@contextmanager` generator — without it, an exception skips the cleanup code after `yield`
2. Returning `True` from `__exit__` by accident, silently swallowing exceptions that should have propagated
3. Doing setup work *outside* `__enter__` (or before `yield`), so it runs even when the context manager is only created, not entered
4. Using a full class-based context manager when a short `@contextmanager` generator would be clearer
5. Forgetting that `__exit__` needs exactly three extra parameters (`exc_type`, `exc_value`, `traceback`) besides `self`

---

## Best Practices

1. **Prefer `@contextmanager`** for simple setup/teardown logic — it's usually shorter and easier to read
2. **Always wrap `yield` in `try`/`finally`** inside a generator-based context manager, so cleanup runs even on error
3. **Only return `True` from `__exit__`** when you deliberately want to suppress that specific exception type
4. **Use context managers for anything with a clear "open/close" or "start/stop" lifecycle** — files, connections, locks, timers, temporary state changes
5. **Combine multiple context managers** in one `with` statement instead of nesting them unnecessarily

---

## See also

- [[07 - OOP — Dunder Methods]] — where `__enter__` and `__exit__` were first introduced
- [[14 - Generators and Iterators]] — the `yield` mechanics that power `@contextmanager`
- [[13 - Decorators]] — `@contextmanager` itself is a decorator from `contextlib`
- [[04 - OOP — Encapsulation]] — controlling access and lifecycle of internal resources
- [[16 - Functools]] — more tools for wrapping and composing functions, alongside `contextlib`

---

## → What's next

You now understand **Context Managers**:

- Why `with` guarantees cleanup even when exceptions occur
- How to build a context manager with `__enter__` and `__exit__`
- How to build a much shorter one using `@contextmanager` and `yield`
- How to suppress or handle exceptions inside a context manager

In the next lesson, we will explore **Functools** — a standard library module full of tools for working with functions, including caching, partial application, and `functools.wraps` from [[13 - Decorators]].

Continue with **[[16 - Functools]]**
