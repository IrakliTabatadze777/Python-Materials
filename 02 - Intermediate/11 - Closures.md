---
tags:
  - intermediate
  - python
  - functions
stage: 2
difficulty: Intermediate
---

# Closures

**Prev:** [[10 - Collections Module]] | **Next:** [[12 - Dataclasses]]

> In this lesson you will learn about **Closures** — what happens when a nested function "remembers" variables from the scope it was created in, even after that outer function has already finished running. This is the mechanism that makes decorators, and many other patterns, possible.

---

## Functions Inside Functions

Python allows you to define a function inside another function:

```python
def outer():
    message = "Hello from outer!"

    def inner():
        print(message)   # inner() can "see" outer's local variable

    inner()

outer()   # Hello from outer!
```

`inner()` can read `message` even though `message` belongs to `outer()`, not to `inner()` itself. This works because of Python's **scope** rules — an inner function can access variables from any enclosing function.

---

## What is a Closure?

A **closure** happens when an inner function is *returned* (or otherwise escapes) from the outer function, and it keeps access to the outer function's variables — even after the outer function has already finished executing.

```python
def make_greeter(name):
    def greet():
        return f"Hello, {name}!"
    return greet   # Return the function itself, not its result


greet_alice = make_greeter("Alice")
greet_bob = make_greeter("Bob")

print(greet_alice())   # Hello, Alice!
print(greet_bob())     # Hello, Bob!
```

By the time `greet_alice()` is called, `make_greeter("Alice")` has already returned — its local scope should be gone. But `greet` still remembers `name = "Alice"`. That's a closure: `greet` has "closed over" the variable `name`.

### Real-Life Analogy

Think of a **sealed envelope handed to a courier**:

- Before the courier leaves, someone writes a name on a slip of paper and seals it inside the envelope
- The courier carries that envelope around, long after the person who sealed it has left
- Whenever the envelope is opened, the name is still there — it was captured at the moment of sealing, not looked up later

The inner function is the envelope; the captured variable is the slip of paper inside it.

---

## Each Closure Gets Its Own Copy

Every call to the outer function creates a **new, independent** closure — they don't share state with each other.

```python
def make_counter():
    count = 0

    def increment():
        nonlocal count
        count += 1
        return count

    return increment


counter_a = make_counter()
counter_b = make_counter()

print(counter_a())   # 1
print(counter_a())   # 2
print(counter_b())   # 1 - independent from counter_a!
print(counter_a())   # 3
```

`counter_a` and `counter_b` each have their own private `count` variable, even though both were created by the exact same function.

---

## `nonlocal`: Modifying an Enclosing Variable

Notice the `nonlocal count` line above. By default, Python assumes any variable you *assign to* inside a function is a new local variable. `nonlocal` tells Python: "this name refers to a variable in the enclosing function, not a new local one."

```python
def make_counter():
    count = 0

    def increment():
        count += 1   # UnboundLocalError without `nonlocal`!
        return count

    return increment
```

Without `nonlocal`, `count += 1` would try to create a brand-new local `count` inside `increment`, then immediately fail because it's being read before it's assigned. `nonlocal` fixes this by pointing back to the outer `count`.

**Reading** an enclosing variable (without reassigning it) doesn't need `nonlocal` — only reassigning it does:

```python
def outer():
    message = "Hi"

    def inner():
        print(message)   # Reading - no nonlocal needed
    inner()
```

---

## Why Closures Matter: They Power Decorators

You've already used closures extensively in [[13 - Decorators]] without necessarily naming them as such:

```python
def shout(func):
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        return result.upper()
    return wrapper


@shout
def greet():
    return "hello"

print(greet())   # HELLO
```

`wrapper` is a closure — it remembers `func` (the original `greet` function) even after `shout(greet)` has already returned. Every decorator relies on exactly this mechanism to "remember" the function it's wrapping.

---

## Closures for Configurable Behavior

Closures let you generate customized functions on the fly, based on arguments captured at creation time.

```python
def make_multiplier(factor):
    def multiply(value):
        return value * factor
    return multiply


double = make_multiplier(2)
triple = make_multiplier(3)

print(double(5))   # 10
print(triple(5))   # 15
```

`double` and `triple` are both built from the same `multiply` function template, but each one remembers a different `factor`.

---

## A Common Pitfall: Closures in Loops

A classic bug happens when creating closures inside a loop — all the closures end up sharing the *same* variable, not a snapshot of its value at each iteration.

```python
def make_multipliers():
    multipliers = []
    for i in range(1, 4):
        def multiply(value):
            return value * i   # Captures the variable `i`, not its current value!
        multipliers.append(multiply)
    return multipliers


funcs = make_multipliers()
print(funcs[0](10))   # 30 - not 10!
print(funcs[1](10))   # 30 - not 20!
print(funcs[2](10))   # 30
```

By the time any of these functions are called, the loop has finished and `i` is `3` — every closure shares that same final value.

**The fix:** capture the current value explicitly, using a default argument:

```python
def make_multipliers():
    multipliers = []
    for i in range(1, 4):
        def multiply(value, i=i):   # Default argument captures i's value NOW
            return value * i
        multipliers.append(multiply)
    return multipliers


funcs = make_multipliers()
print(funcs[0](10))   # 10
print(funcs[1](10))   # 20
print(funcs[2](10))   # 30
```

Default argument values are evaluated once, at function definition time — so `i=i` freezes that iteration's value into the function itself.

---

## Real-World Example: A Simple Cache

```python
def make_cache():
    cache = {}

    def cached_call(func, arg):
        if arg not in cache:
            print(f"Computing {func.__name__}({arg})...")
            cache[arg] = func(arg)
        else:
            print(f"Using cached result for {arg}")
        return cache[arg]

    return cached_call


def slow_square(x):
    return x * x

cache_call = make_cache()

print(cache_call(slow_square, 5))   # Computing slow_square(5)... -> 25
print(cache_call(slow_square, 5))   # Using cached result for 5 -> 25
print(cache_call(slow_square, 6))   # Computing slow_square(6)... -> 36
```

`cache_call` remembers `cache` between calls — a private, persistent piece of state, without needing a class at all.

---

## Closures vs. Classes

Both closures and classes can bundle state with behavior. Closures are often a lighter-weight alternative when you only need one piece of behavior with some remembered state.

```python
# Class-based approach
class Counter:
    def __init__(self):
        self.count = 0

    def increment(self):
        self.count += 1
        return self.count


# Closure-based approach
def make_counter():
    count = 0
    def increment():
        nonlocal count
        count += 1
        return count
    return increment
```

Both work. Reach for a closure when you need one function with private state; reach for a class (see [[01 - OOP — Classes and Objects]]) when you need multiple related methods sharing that state.

---

## Benefits of Closures

1. **Encapsulated State** — Variables stay private to the closure, inaccessible from outside
2. **Lightweight Alternative to Classes** — No need for a full class when you just need one function with memory
3. **Foundation for Decorators** — Every decorator you write relies on closures under the hood
4. **Configurable Function Factories** — Generate specialized functions (`double`, `triple`) from a shared template
5. **No Global State Needed** — Avoids polluting global scope just to remember a value between calls

---

## Common Mistakes

1. Forgetting `nonlocal` when reassigning an enclosing variable, causing an `UnboundLocalError`
2. Creating closures inside a loop without capturing the loop variable's current value, causing every closure to share the same final value
3. Overusing closures for complex state that would be clearer as a class with named methods
4. Confusing a closure with a plain nested function — a nested function only becomes a closure once it's returned or escapes the enclosing scope and still references its variables
5. Mutating a captured mutable object (like a list) and being surprised that every closure referencing it sees the change — since they all reference the same object, not copies

---

## Best Practices

1. **Use closures for small, focused pieces of behavior** — configuration functions, callbacks, simple caches
2. **Use `nonlocal` explicitly and sparingly** — mutable closure state can get confusing quickly
3. **Capture loop variables with a default argument** (`def f(x=x):`) when creating closures inside a loop
4. **Reach for a class** once you need more than one method sharing the same state
5. **Recognize closures where they already exist** — nearly every decorator you write or use is one

---

## See also

- [[08 - OOP — Class and Static Methods]] — classes as an alternative way to bundle state and behavior
- [[09 - Enums]] — another way to represent fixed values, complementary to closures over variables
- [[13 - Decorators]] — the primary real-world use case built entirely on closures
- [[16 - Defining Functions]] — the scope and function fundamentals closures build on
- [[19 - Lambda Functions]] — anonymous functions, which can also form closures
- [[10 - Collections Module]] — specialized data structures, another standard-library toolbox
- [[12 - Dataclasses]] — bundling state into structured, named fields instead of closure variables

---

## → What's next

You now understand **Closures**:

- How a nested function can "remember" variables from its enclosing scope
- Why each call to the outer function creates an independent closure
- How `nonlocal` lets an inner function modify an enclosing variable
- Why closures inside loops need special care to avoid a classic shared-variable bug

In the next lesson, we will explore **Dataclasses** — a decorator that automatically generates `__init__`, `__repr__`, `__eq__`, and more for classes that are mostly about storing data.

Continue with **[[12 - Dataclasses]]**
