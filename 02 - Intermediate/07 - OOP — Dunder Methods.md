---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# OOP — Dunder Methods

**Prev:** [[06 - OOP — Abstract Classes]] | **Next:** [[08 - OOP — Class and Static Methods]]

> In this lesson you will learn about **Dunder Methods** (short for "double underscore") — the special methods that let your custom objects work naturally with Python's built-in syntax, like `+`, `len()`, `print()`, and `for` loops.

---

## What are Dunder Methods?

**Dunder methods** (also called **magic methods** or **special methods**) are methods surrounded by double underscores, like `__init__`, `__str__`, and `__len__`.

You never call them directly by name — instead, Python calls them for you behind the scenes when you use built-in syntax or functions on your objects.

### Real-Life Analogy

Think of a **universal remote control**:

- Every button (power, volume, channel) does the same *kind* of thing across every brand of TV
- Each TV manufacturer wires up its own internals to respond to those same buttons
- You never open the TV and flip wires yourself — you press the button, and the TV "just works"

Dunder methods are those buttons. Python presses them for you whenever you write `obj + other`, `len(obj)`, `print(obj)`, and so on — your class defines what happens underneath.

---

## `__init__`: You Already Know This One

You've been using a dunder method since your very first class:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

p = Point(3, 4)  # Python calls __init__ automatically
```

`__init__` is called automatically when you create a new object. Every dunder method follows this same idea: **Python calls it for you** in response to some action.

---

## `__str__` and `__repr__`: Controlling How Objects Print

By default, printing an object gives an unhelpful result:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

p = Point(3, 4)
print(p)  # <__main__.Point object at 0x104a3b2e0>
```

`__str__` and `__repr__` let you control this:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        """Readable output, used by print() and str()"""
        return f"({self.x}, {self.y})"

    def __repr__(self):
        """Unambiguous output, used in the console and inside collections"""
        return f"Point(x={self.x}, y={self.y})"

p = Point(3, 4)
print(p)        # (3, 4)              -> uses __str__
print([p])      # [Point(x=3, y=4)]   -> uses __repr__
p               # Point(x=3, y=4)     -> uses __repr__ in the interactive shell
```

**Rule of thumb:**
- `__str__` — readable, for end users (`print(obj)`)
- `__repr__` — precise, for developers (should ideally look like valid Python code to recreate the object)
- If you only define one, define `__repr__` — Python falls back to it when `__str__` is missing

---

## Operator Overloading: `__add__`, `__eq__`, and More

Dunder methods let you define how operators behave on your own objects.

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Point({self.x}, {self.y})"

    def __add__(self, other):
        """Called for point1 + point2"""
        return Point(self.x + other.x, self.y + other.y)

    def __eq__(self, other):
        """Called for point1 == point2"""
        return self.x == other.x and self.y == other.y

    def __lt__(self, other):
        """Called for point1 < point2"""
        return (self.x, self.y) < (other.x, other.y)


p1 = Point(1, 2)
p2 = Point(3, 4)

print(p1 + p2)        # Point(4, 6)
print(p1 == Point(1, 2))  # True
print(p1 < p2)         # True
```

**Common operator dunder methods:**

| Method     | Operator / Behavior |
| ---------- | -------------------- |
| `__add__`  | `+`                   |
| `__sub__`  | `-`                   |
| `__mul__`  | `*`                   |
| `__eq__`   | `==`                  |
| `__lt__`   | `<`                   |
| `__gt__`   | `>`                   |
| `__len__`  | `len(obj)`            |
| `__bool__` | `bool(obj)`, truthiness in `if obj:` |

---

## Making Objects Act Like Collections

Dunder methods can make a custom class behave like a list, dictionary, or other container.

```python
class Playlist:
    def __init__(self, songs):
        self.songs = songs

    def __len__(self):
        """Called by len(playlist)"""
        return len(self.songs)

    def __getitem__(self, index):
        """Called by playlist[index], also enables iteration and slicing"""
        return self.songs[index]

    def __contains__(self, song):
        """Called by 'song in playlist'"""
        return song in self.songs


playlist = Playlist(["Song A", "Song B", "Song C"])

print(len(playlist))            # 3
print(playlist[1])              # Song B
print("Song A" in playlist)     # True

for song in playlist:           # __getitem__ makes this work automatically
    print(song)
```

---

## Making Objects Callable: `__call__`

`__call__` lets you use an object as if it were a function.

```python
class Multiplier:
    def __init__(self, factor):
        self.factor = factor

    def __call__(self, value):
        return value * self.factor

double = Multiplier(2)
print(double(5))   # 10 - calling the object directly!
```

This pattern is common for building configurable, reusable function-like objects (e.g., decorators, callbacks).

---

## Using Objects with `with`: `__enter__` and `__exit__`

Dunder methods can also make your class work as a **context manager** (the `with` statement).

```python
class FileManager:
    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode
        self.file = None

    def __enter__(self):
        self.file = open(self.filename, self.mode)
        return self.file

    def __exit__(self, exc_type, exc_value, traceback):
        self.file.close()
        print("File closed automatically")


with FileManager("notes.txt", "w") as f:
    f.write("Hello, dunder methods!")
# File closed automatically
```

`__enter__` runs at the start of the `with` block, and `__exit__` always runs at the end — even if an error occurs inside the block.

---

## Real-World Example: A `Money` Class

```python
class Money:
    def __init__(self, amount, currency="USD"):
        self.amount = amount
        self.currency = currency

    def __repr__(self):
        return f"Money({self.amount}, '{self.currency}')"

    def __str__(self):
        return f"{self.amount:.2f} {self.currency}"

    def __add__(self, other):
        if self.currency != other.currency:
            raise ValueError("Cannot add different currencies")
        return Money(self.amount + other.amount, self.currency)

    def __eq__(self, other):
        return self.amount == other.amount and self.currency == other.currency

    def __lt__(self, other):
        if self.currency != other.currency:
            raise ValueError("Cannot compare different currencies")
        return self.amount < other.amount


wallet = Money(50) + Money(25)
print(wallet)                    # 75.00 USD
print(Money(10) < Money(20))     # True
print(Money(10) == Money(10))    # True
```

---

## Benefits of Dunder Methods

1. **Natural Syntax** — Your objects work with `+`, `len()`, `for`, `with`, and other built-ins
2. **Consistency** — Custom classes behave like Python's own built-in types
3. **Integration** — Your objects play nicely with existing functions and libraries that expect these protocols
4. **Readable Code** — `total = a + b` is clearer than `total = a.add(b)`

---

## Common Mistakes

1. Overriding `__eq__` without also handling hashing — if you need the object in a `set` or as a `dict` key, you may also need `__hash__`
2. Defining `__str__` but forgetting `__repr__` (or vice versa) — leads to confusing debug output
3. Implementing operators that don't make logical sense (e.g., `__add__` on unrelated types)
4. Forgetting that `__eq__` should compare *values*, not just check `is` identity
5. Using dunder methods for something a regular, clearly-named method would communicate better

---

## Best Practices

1. **Always compare `type` or relevant attributes carefully** inside `__eq__` — don't assume `other` is the same class
2. **Keep dunder methods side-effect free** where possible (e.g., `__add__` shouldn't print or mutate `self`)
3. **Implement `__repr__` for every class** you expect to debug or log — it costs little and helps enormously
4. **Only implement operators that make real-world sense** for your class
5. **Use `functools.total_ordering`** if you want full comparison support (`<`, `<=`, `>`, `>=`) without writing every method by hand

---

## See also

- [[01 - OOP — Classes and Objects]] — foundation of classes, objects, and `__init__`
- [[04 - OOP — Encapsulation]] — protecting data accessed by dunder methods
- [[05 - OOP — Polymorphism]] — operator overloading as a form of polymorphism
- [[06 - OOP — Abstract Classes]] — enforcing method implementation in subclasses
- [[08 - OOP — Class and Static Methods]] — alternative constructors and utility methods

---

## → What's next

You now understand **Dunder Methods**:

- How Python calls special methods automatically in response to built-in syntax
- How to control string output with `__str__` and `__repr__`
- How to overload operators like `+`, `==`, and `<`
- How to make objects behave like collections, callables, and context managers

In the next lesson, we will explore **Class and Static Methods** — how `@classmethod` and `@staticmethod` let you define methods that belong to the class itself rather than to a single instance.

Continue with **[[08 - OOP — Class and Static Methods]]**
