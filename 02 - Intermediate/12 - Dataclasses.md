---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# Dataclasses

**Prev:** [[11 - Closures]] | **Next:** [[13 - Decorators]]

> In this lesson you will learn about **Dataclasses** — a built-in decorator that eliminates the repetitive boilerplate of writing `__init__`, `__repr__`, and `__eq__` by hand for classes that are mostly about storing data.

---

## The Problem: Boilerplate

Think back to [[07 - OOP — Dunder Methods]]. A simple class that just stores a few values still needs several dunder methods written by hand to behave well:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Point(x={self.x}, y={self.y})"

    def __eq__(self, other):
        if not isinstance(other, Point):
            return NotImplemented
        return self.x == other.x and self.y == other.y


p1 = Point(1, 2)
p2 = Point(1, 2)

print(p1)          # Point(x=1, y=2)
print(p1 == p2)     # True
```

This works, but it's a lot of repetitive code for something so simple — and you'd have to write it again for every similar class.

---

## The Solution: `@dataclass`

The `dataclasses` module (built into Python) generates `__init__`, `__repr__`, and `__eq__` automatically from a list of **type-annotated attributes**.

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int


p1 = Point(1, 2)
p2 = Point(1, 2)

print(p1)          # Point(x=1, y=2)   <- __repr__ generated for you
print(p1 == p2)     # True              <- __eq__ generated for you
```

Same behavior, a fraction of the code. The type annotations (`x: int`, `y: int`) are what `@dataclass` reads to know what fields to generate methods for.

---

## Default Values

Just like regular function parameters, fields can have defaults:

```python
from dataclasses import dataclass

@dataclass
class Product:
    name: str
    price: float
    in_stock: bool = True   # Default value


p1 = Product("Laptop", 999.99)
p2 = Product("Mouse", 25.00, in_stock=False)

print(p1)  # Product(name='Laptop', price=999.99, in_stock=True)
print(p2)  # Product(name='Mouse', price=25.0, in_stock=False)
```

**Important:** fields without defaults must come before fields with defaults — same rule as regular function parameters.

```python
@dataclass
class Broken:
    name: str = "Unnamed"
    price: float   # SyntaxError: non-default argument follows default argument
```

---

## Mutable Defaults Need `field(default_factory=...)`

You can't use a mutable object (like a list or dict) directly as a default value — Python raises an error to prevent a classic bug where every instance would share the same list.

```python
from dataclasses import dataclass, field

@dataclass
class ShoppingCart:
    items: list = field(default_factory=list)   # Correct way
    owner: str = "Guest"


cart1 = ShoppingCart()
cart2 = ShoppingCart()

cart1.items.append("Apples")

print(cart1.items)  # ['Apples']
print(cart2.items)  # [] - each instance gets its own list!
```

Without `default_factory`, `items: list = []` would raise a `ValueError` — dataclasses catch this mistake for you before it becomes a hidden bug.

---

## Adding Your Own Methods

A dataclass is still a normal class — you can add regular methods alongside the generated ones.

```python
from dataclasses import dataclass

@dataclass
class Rectangle:
    width: float
    height: float

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)


rect = Rectangle(5, 3)
print(rect.area())        # 15
print(rect.perimeter())   # 16
print(rect)                # Rectangle(width=5, height=3) - still auto-generated
```

---

## Making Instances Immutable: `frozen=True`

Passing `frozen=True` to the decorator makes instances read-only after creation — any attempt to change an attribute raises an error.

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Coordinate:
    latitude: float
    longitude: float


location = Coordinate(40.7128, -74.0060)
print(location)  # Coordinate(latitude=40.7128, longitude=-74.006)

location.latitude = 0  # FrozenInstanceError!
```

This is useful for values that should never change after creation, similar in spirit to a read-only property from [[04 - OOP — Encapsulation]] — but enforced automatically for every field at once.

---

## Ordering Instances: `order=True`

By default, dataclass instances only support `==` and `!=`. Passing `order=True` also generates `<`, `<=`, `>`, and `>=`, comparing fields in the order they're declared.

```python
from dataclasses import dataclass

@dataclass(order=True)
class Version:
    major: int
    minor: int
    patch: int


v1 = Version(1, 2, 0)
v2 = Version(1, 3, 0)

print(v1 < v2)   # True - compares (major, minor, patch) tuples in order
print(sorted([v2, v1]))  # [Version(major=1, minor=2, patch=0), Version(major=1, minor=3, patch=0)]
```

---

## Real-World Example: Configuration Object

```python
from dataclasses import dataclass, field

@dataclass
class ServerConfig:
    host: str
    port: int = 8080
    debug: bool = False
    allowed_origins: list = field(default_factory=list)

    def url(self):
        return f"http://{self.host}:{self.port}"


config = ServerConfig("localhost", allowed_origins=["example.com"])

print(config)         # ServerConfig(host='localhost', port=8080, debug=False, allowed_origins=['example.com'])
print(config.url())   # http://localhost:8080
```

Compare this to writing `__init__`, `__repr__`, and `__eq__` by hand for four fields — `@dataclass` removes all of that repetition while still giving you a fully normal class underneath.

---

## Dataclasses vs. Regular Classes

| Feature                      | Regular Class          | `@dataclass`              |
| ------------------------------ | ------------------------ | ---------------------------- |
| `__init__`                    | Written by hand           | Auto-generated from fields    |
| `__repr__`                    | Written by hand           | Auto-generated                |
| `__eq__`                      | Written by hand           | Auto-generated                |
| Custom methods                | Supported                 | Fully supported               |
| Inheritance, `@property`, etc. | Supported                 | Fully supported               |
| Best for                       | Complex behavior-heavy classes | Classes primarily storing data |

Dataclasses don't replace regular classes — they're best suited for classes whose main job is holding a fixed set of related values (like the `Point`, `Product`, and `ServerConfig` examples above).

---

## Benefits of Dataclasses

1. **Less Boilerplate** — No need to hand-write `__init__`, `__repr__`, `__eq__`
2. **Safer Defaults** — Mutable default bugs are caught automatically
3. **Readable Declarations** — Fields and their types are visible at a glance
4. **Still a Real Class** — You can add methods, use inheritance, or mix in `@property` freely
5. **Optional Extras** — `frozen=True` and `order=True` add immutability and comparisons on demand

---

## Common Mistakes

1. Using `field(default_factory=list)` incorrectly, e.g., `field(default_factory=[])` (should be `list`, not `[]`)
2. Forgetting type annotations — a dataclass **requires** them; a plain `x = 0` without a type hint is not treated as a field
3. Assuming `@dataclass` gives you validation — it doesn't; use `__post_init__` if you need to validate values
4. Trying to mutate a `frozen=True` instance and being surprised by the error
5. Reaching for `@dataclass` on classes with complex behavior, where a regular class is clearer

---

## Validating Data with `__post_init__`

Dataclasses don't validate values by default, but you can add a `__post_init__` method that runs automatically right after `__init__`.

```python
from dataclasses import dataclass

@dataclass
class Age:
    years: int

    def __post_init__(self):
        if self.years < 0:
            raise ValueError("Age cannot be negative")


valid = Age(25)
# invalid = Age(-5)  # Raises ValueError
```

---

## Best Practices

1. **Use `@dataclass` for simple, data-focused classes** — configs, records, coordinates, DTOs
2. **Always use `field(default_factory=...)`** for mutable defaults like lists, dicts, or sets
3. **Add `frozen=True`** when values should never change after creation
4. **Use `__post_init__`** for validation logic that regular `@dataclass` fields can't express
5. **Don't force complex, behavior-heavy classes into a dataclass** just to save a few lines

---

## See also

- [[01 - OOP — Classes and Objects]] — foundation of classes and `__init__`
- [[04 - OOP — Encapsulation]] — validating and protecting data, complementary to `__post_init__`
- [[07 - OOP — Dunder Methods]] — the `__init__`, `__repr__`, and `__eq__` methods dataclasses generate for you
- [[08 - OOP — Class and Static Methods]] — alternative constructors, still usable on dataclasses
- [[10 - Collections Module]] — specialized data structures, another way to model structured data
- [[11 - Closures]] — nested functions and scope, a different way to bundle state and behavior
- [[13 - Decorators]] — how `@dataclass`, `@property`, `@staticmethod`, and `@classmethod` actually work

---

## → What's next

You now understand **Dataclasses**:

- Why `@dataclass` removes repetitive boilerplate from data-focused classes
- How to set default values, including safely handling mutable defaults
- How to make instances immutable (`frozen=True`) or sortable (`order=True`)
- How to validate fields with `__post_init__`

In the next lesson, we will explore **Decorators** — the `@` syntax you've been using throughout this series (`@dataclass`, `@property`, `@staticmethod`, `@classmethod`, `@abstractmethod`) and how to write your own.

Continue with **[[13 - Decorators]]**
