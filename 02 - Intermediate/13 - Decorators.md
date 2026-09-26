---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# Decorators

**Prev:** [[12 - Dataclasses]] | **Next:** [[14 - Generators and Iterators]]

> In this lesson you will learn about **Decorators** — the `@` syntax you've been using throughout this series (`@dataclass`, `@property`, `@staticmethod`, `@abstractmethod`). You will learn what they actually are under the hood, and how to write your own.

---

## What is a Decorator?

A **decorator** is a function that takes another function (or class) as input and returns a **modified version** of it — adding behavior before, after, or around the original, without changing its actual code.

### Real-Life Analogy

Think of a **gift wrapping service**:

- You hand over a gift (a function)
- The wrapping service adds paper, a bow, and a card around it (extra behavior)
- You get back something that still *contains* the original gift, but now does more when it's opened

The gift itself never had to change — the wrapping added the extra behavior around it.

---

## Functions Are Objects

Decorators work because in Python, functions are **first-class objects** — they can be passed around, stored in variables, and returned from other functions, just like any value.

```python
def greet():
    return "Hello!"

say_hello = greet   # Assign the function itself, not its result
print(say_hello())  # Hello!
```

This is the foundation decorators are built on.

---

## Building a Decorator by Hand

A decorator is just a function that wraps another function:

```python
def shout(func):
    def wrapper():
        result = func()
        return result.upper()
    return wrapper


def greet():
    return "hello"

greet = shout(greet)   # Manually "decorating" greet
print(greet())          # HELLO
```

The `@` syntax is shorthand for exactly this pattern:

```python
def shout(func):
    def wrapper():
        result = func()
        return result.upper()
    return wrapper


@shout
def greet():
    return "hello"

print(greet())   # HELLO
```

`@shout` above `def greet():` is identical to writing `greet = shout(greet)` right after the function is defined.

---

## Handling Arguments with `*args` and `**kwargs`

Most functions take arguments, so a useful decorator needs to accept and forward any arguments the original function needs.

```python
def log_call(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__} with args={args}, kwargs={kwargs}")
        result = func(*args, **kwargs)
        print(f"{func.__name__} returned {result}")
        return result
    return wrapper


@log_call
def add(a, b):
    return a + b

add(3, 5)
```

**Output:**
```
Calling add with args=(3, 5), kwargs={}
add returned 8
```

`*args` and `**kwargs` let `wrapper` accept **any** combination of arguments and pass them straight through to `func`.

---

## Preserving Metadata with `functools.wraps`

There's a subtle problem with the decorator above: the wrapped function loses its original name and docstring.

```python
@log_call
def add(a, b):
    """Adds two numbers"""
    return a + b

print(add.__name__)   # wrapper - not "add"!
print(add.__doc__)    # None - lost the docstring!
```

Fix this with `functools.wraps`:

```python
from functools import wraps

def log_call(func):
    @wraps(func)   # Preserves func's name, docstring, etc.
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__} with args={args}, kwargs={kwargs}")
        return func(*args, **kwargs)
    return wrapper


@log_call
def add(a, b):
    """Adds two numbers"""
    return a + b

print(add.__name__)   # add
print(add.__doc__)    # Adds two numbers
```

**Always use `@wraps(func)`** inside your own decorators — it costs one line and prevents confusing debugging surprises later.

---

## Decorators with Arguments

Sometimes you want to configure the decorator itself, like `@retry(times=3)`. This requires an extra layer of nesting — a function that returns a decorator.

```python
from functools import wraps

def retry(times):
    """A decorator factory - returns a decorator configured with `times`"""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(1, times + 1):
                try:
                    return func(*args, **kwargs)
                except ValueError as e:
                    print(f"Attempt {attempt} failed: {e}")
            raise Exception(f"All {times} attempts failed")
        return wrapper
    return decorator


@retry(times=3)
def parse_number(value):
    return int(value)

parse_number("42")       # Works on first try
# parse_number("abc")    # Fails 3 times, then raises
```

`@retry(times=3)` first calls `retry(3)`, which returns `decorator` — and *that* is what actually wraps `parse_number`.

---

## Decorators You Already Know

Every one of these, seen earlier in this series, follows the exact same underlying pattern:

| Decorator          | Where you saw it                      | What it does                                  |
| -------------------- | --------------------------------------- | ------------------------------------------------ |
| `@property`          | [[04 - OOP — Encapsulation]]           | Turns a method into a controlled attribute        |
| `@staticmethod`       | [[08 - OOP — Class and Static Methods]] | Removes the automatic `self`/`cls` argument       |
| `@classmethod`        | [[08 - OOP — Class and Static Methods]] | Passes `cls` instead of `self`                    |
| `@abstractmethod`      | [[06 - OOP — Abstract Classes]]        | Marks a method as required in subclasses          |
| `@dataclass`           | [[12 - Dataclasses]]                   | Generates `__init__`, `__repr__`, `__eq__`, etc.  |

`@dataclass` is a great example of a **class decorator** — instead of wrapping a function, it takes a whole class and returns a modified version of it, adding the generated methods.

---

## Stacking Multiple Decorators

Decorators can be combined — they apply bottom to top, wrapping each other in layers.

```python
from functools import wraps

def bold(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return f"<b>{func(*args, **kwargs)}</b>"
    return wrapper

def italic(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return f"<i>{func(*args, **kwargs)}</i>"
    return wrapper


@bold
@italic
def message():
    return "Hello"

print(message())   # <b><i>Hello</i></b>
```

`italic` runs first (closest to the function), then `bold` wraps its result — reading bottom-up mirrors the order they're applied.

---

## Real-World Example: Timing Function Execution

```python
import time
from functools import wraps

def timer(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        elapsed = time.perf_counter() - start
        print(f"{func.__name__} took {elapsed:.4f} seconds")
        return result
    return wrapper


@timer
def slow_sum(n):
    return sum(range(n))

slow_sum(10_000_000)
# slow_sum took 0.1234 seconds
```

This is one of the most common real-world uses of decorators — measuring, logging, caching, or validating around a function without touching its internal logic.

---

## Benefits of Decorators

1. **Separation of Concerns** — Cross-cutting logic (logging, timing, validation) stays separate from core logic
2. **Reusability** — Write the wrapping behavior once, apply it to many functions with `@`
3. **Readability** — `@retry(times=3)` communicates intent clearly at the point of definition
4. **Composability** — Multiple decorators can be stacked to combine behaviors
5. **Non-Invasive** — The original function's code never has to change

---

## Common Mistakes

1. Forgetting `*args, **kwargs` in `wrapper`, breaking any decorated function that takes arguments
2. Forgetting `@wraps(func)`, losing the original function's name and docstring
3. Confusing a plain decorator (`def my_decorator(func):`) with a decorator **factory** (`def my_decorator(arg):` that returns a decorator) — mixing up when to add the extra layer
4. Overusing decorators for logic that would be clearer as a simple, explicit function call
5. Forgetting that stacked decorators apply bottom-to-top, leading to unexpected ordering

---

## Best Practices

1. **Always use `functools.wraps`** inside custom decorators
2. **Keep decorators focused** — one decorator, one responsibility (logging, timing, validation, etc.)
3. **Name decorators clearly** based on what they add (`@timer`, `@retry`, `@log_call`)
4. **Use decorator factories** (`@retry(times=3)`) when the behavior needs configuration
5. **Prefer built-in decorators** (`@property`, `@staticmethod`, `@classmethod`, `@dataclass`) over hand-rolled equivalents whenever they fit

---

## See also

- [[04 - OOP — Encapsulation]] — `@property` as a decorator that controls attribute access
- [[06 - OOP — Abstract Classes]] — `@abstractmethod` enforcing required methods
- [[08 - OOP — Class and Static Methods]] — `@classmethod` and `@staticmethod` in depth
- [[11 - Closures]] — the scope-capturing mechanism every decorator relies on
- [[12 - Dataclasses]] — `@dataclass` as a class-level decorator
- [[16 - Defining Functions]] — the function fundamentals decorators build on
- [[19 - Lambda Functions]] — anonymous functions, sometimes used inline with decorators
- [[14 - Generators and Iterators]] — the `__iter__`/`__next__` protocol decorators sometimes wrap

---

## → What's next

You now understand **Decorators**:

- Why decorators work — functions are first-class objects in Python
- How to write your own decorator using `*args`, `**kwargs`, and `functools.wraps`
- How to build a decorator factory for configurable decorators like `@retry(times=3)`
- How familiar decorators (`@property`, `@classmethod`, `@dataclass`) all follow this same pattern

In the next lesson, we will explore **Generators and Iterators** — how Python's `for` loops actually work under the hood, and how to write memory-efficient functions using `yield`.

Continue with **[[14 - Generators and Iterators]]**
