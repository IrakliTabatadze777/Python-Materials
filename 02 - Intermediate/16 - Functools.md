---
tags:
  - intermediate
  - python
  - functions
stage: 2
difficulty: Intermediate
---

# Functools

**Prev:** [[15 - Context Managers]] | **Next:** [[17 - Itertools]]

> In this lesson you will learn about the **`functools`** module — Python's standard-library toolbox for working with functions themselves. You'll learn how to cache expensive calls, pre-fill arguments, and build well-behaved decorators.

---

## What is `functools`?

`functools` is a built-in module full of tools for manipulating and enhancing **functions** — not the data they operate on. You've already used one of them, `wraps`, back in [[13 - Decorators]]. This lesson covers the rest of the toolbox.

```python
import functools
```

---

## `functools.wraps`: A Quick Recap

As seen in [[13 - Decorators]], `@wraps` preserves a wrapped function's name and docstring:

```python
from functools import wraps

def log_call(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper
```

Every custom decorator you write should use it — it's the single most common `functools` tool.

---

## `functools.lru_cache`: Automatic Memoization

`@lru_cache` automatically caches a function's return values, so repeated calls with the same arguments skip recomputation entirely. ("LRU" means Least Recently Used — the oldest unused entries are dropped once the cache fills up.)

```python
from functools import lru_cache
import time

@lru_cache(maxsize=None)
def slow_square(n):
    time.sleep(1)   # Simulate an expensive computation
    return n * n


print(slow_square(4))   # Takes ~1 second
print(slow_square(4))   # Instant - returned from cache!
print(slow_square(5))   # Takes ~1 second (new argument)
```

This is especially powerful for recursive functions:

```python
@lru_cache(maxsize=None)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)


print(fibonacci(35))   # Fast! Without caching, this would take a very long time
```

Without `@lru_cache`, naive recursive Fibonacci recomputes the same values millions of times. With it, each unique input is computed only once.

**Checking cache statistics:**

```python
print(fibonacci.cache_info())   # CacheInfo(hits=33, misses=36, maxsize=None, currsize=36)
fibonacci.cache_clear()          # Clears the cache manually
```

`maxsize=None` means an unlimited cache; pass a number (e.g., `maxsize=128`) to cap how many results are kept.

---

## `functools.cache`: A Simpler Alias

Python 3.9+ offers `@cache` as a simpler shorthand for `@lru_cache(maxsize=None)`:

```python
from functools import cache

@cache
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)
```

Use `@cache` when you want an unbounded cache and don't need to configure `maxsize`.

---

## `functools.partial`: Pre-Filling Arguments

`partial` creates a new function with some arguments already "locked in," similar in spirit to the closures from [[11 - Closures]], but built from an existing function instead of a hand-written one.

```python
from functools import partial

def power(base, exponent):
    return base ** exponent

square = partial(power, exponent=2)
cube = partial(power, exponent=3)

print(square(5))   # 25
print(cube(5))      # 125
```

`partial(power, exponent=2)` returns a new callable that always passes `exponent=2`, leaving `base` to be supplied later.

### Real-World Use: Configuring Callbacks

```python
from functools import partial

def log_message(level, message):
    print(f"[{level}] {message}")

log_error = partial(log_message, "ERROR")
log_info = partial(log_message, "INFO")

log_error("Database connection failed")   # [ERROR] Database connection failed
log_info("Server started")                 # [INFO] Server started
```

This pattern is common when passing callback functions to APIs that expect a specific number of arguments, but you need to supply extra configuration up front.

---

## `functools.reduce`: Combining a Sequence into One Value

`reduce` repeatedly applies a function to pairs of values, collapsing a sequence down to a single result.

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]

total = reduce(lambda acc, x: acc + x, numbers)
print(total)   # 15

product = reduce(lambda acc, x: acc * x, numbers)
print(product)  # 120
```

`reduce` applies the function cumulatively: `((((1+2)+3)+4)+5)`. It's the same idea behind `sum()`, but generalized to any combining operation.

**With a starting value:**

```python
total = reduce(lambda acc, x: acc + x, numbers, 100)
print(total)   # 115 - starts from 100 instead of the first element
```

**When to prefer alternatives:** for simple sums or products, built-ins like `sum()` and `math.prod()` are clearer. Reach for `reduce` when the combining logic is genuinely custom.

---

## `functools.total_ordering`: Filling In Comparison Methods

Recall from [[07 - OOP — Dunder Methods]] that comparison operators (`<`, `<=`, `>`, `>=`) each need their own dunder method. `@total_ordering` fills in the rest automatically if you define just `__eq__` and *one* of the others.

```python
from functools import total_ordering

@total_ordering
class Version:
    def __init__(self, major, minor):
        self.major = major
        self.minor = minor

    def __eq__(self, other):
        return (self.major, self.minor) == (other.major, other.minor)

    def __lt__(self, other):
        return (self.major, self.minor) < (other.major, other.minor)


v1 = Version(1, 2)
v2 = Version(1, 5)

print(v1 < v2)    # True  - defined directly
print(v1 > v2)    # False - generated by total_ordering
print(v1 <= v2)   # True  - generated by total_ordering
print(v1 >= v2)   # False - generated by total_ordering
```

This saves you from writing four nearly-identical comparison methods by hand.

---

## Real-World Example: Caching an Expensive API Call

```python
from functools import lru_cache

@lru_cache(maxsize=100)
def fetch_user_profile(user_id):
    print(f"Fetching profile for user {user_id} from the database...")
    # Simulates a slow database or network call
    return {"id": user_id, "name": f"User{user_id}"}


print(fetch_user_profile(42))   # Fetching profile for user 42...
print(fetch_user_profile(42))   # Instant - cached!
print(fetch_user_profile(7))    # Fetching profile for user 7...
```

This pattern is extremely common for anything backed by a slow resource — a database, a network call, or a heavy computation — where repeated calls with the same input are wasteful.

---

## Benefits of `functools`

1. **Automatic Caching** — `@lru_cache`/`@cache` eliminate repeated work with almost no code
2. **Cleaner Callback Configuration** — `partial` pre-fills arguments instead of writing wrapper lambdas
3. **Less Repetition** — `@total_ordering` and `@wraps` remove boilerplate you'd otherwise write by hand
4. **Battle-Tested** — These are standard library tools, well-optimized and widely understood
5. **Composable** — All of these tools combine naturally with decorators and closures from earlier lessons

---

## Common Mistakes

1. Using `@lru_cache` on a function with **mutable** arguments (like a `list`) — they aren't hashable, so this raises a `TypeError`
2. Caching a function whose result depends on external state (like the current time or a database that changes) — the cache can silently return stale results
3. Reaching for `reduce` when a simple `sum()`, `max()`, or list comprehension would be clearer
4. Forgetting that `@total_ordering` still requires `__eq__` and one ordering method to be written by hand — it doesn't generate everything from nothing
5. Never clearing or bounding a cache (`maxsize`) in a long-running process, letting it grow unbounded

---

## Best Practices

1. **Use `@lru_cache`/`@cache` for pure functions** — same input always produces the same output, with no side effects
2. **Set a `maxsize`** in long-running applications to avoid unbounded memory growth
3. **Prefer `partial` over writing a small wrapper function** just to pre-fill arguments
4. **Use `@total_ordering`** instead of writing all four comparison methods by hand
5. **Reach for `reduce` only when the combining logic is custom** — prefer built-ins for simple sums and products

---

## See also

- [[07 - OOP — Dunder Methods]] — the comparison methods `@total_ordering` helps generate
- [[11 - Closures]] — the scope-capturing idea `partial` achieves without writing a closure by hand
- [[13 - Decorators]] — `functools.wraps`, and the pattern `lru_cache`/`cache` follow
- [[17 - Itertools]] — another standard-library toolbox, this one focused on iterables

---

## → What's next

You now understand **Functools**:

- How `@lru_cache`/`@cache` eliminate repeated work by memoizing return values
- How `partial` pre-fills a function's arguments to create a specialized version of it
- How `reduce` collapses a sequence into a single combined value
- How `@total_ordering` fills in comparison methods from just `__eq__` and one other

In the next lesson, we will explore **Itertools** — a standard-library module full of memory-efficient tools for working with iterators and generators.

Continue with **[[17 - Itertools]]**
