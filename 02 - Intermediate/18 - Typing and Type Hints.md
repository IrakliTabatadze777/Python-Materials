---
tags:
  - intermediate
  - python
  - typing
stage: 2
difficulty: Intermediate
---

# Typing and Type Hints

**Prev:** [[17 - Itertools]] | **Next:** [[19 - Regex]]

> In this lesson you will learn about **Type Hints** — optional annotations that document what types of values a function or variable expects. You will learn how they help catch bugs early, make code self-documenting, and how tools like `mypy` can check them without ever running your code.

---

## Python is Dynamically Typed — But You Can Still Hint

Python doesn't require you to declare types, and it never has:

```python
def add(a, b):
    return a + b

add(2, 3)         # 5
add("2", "3")     # "23" - technically works, probably not intended
add(2, "3")       # TypeError at runtime - too late, code already ran
```

**Type hints** let you document the *intended* types without changing how Python actually runs:

```python
def add(a: int, b: int) -> int:
    return a + b
```

Python still doesn't enforce this at runtime — `add("2", "3")` will still execute. Type hints are a **static** tool: they help humans and separate checking tools (like `mypy`) catch mistakes *before* the code runs, not while it runs.

### Real-Life Analogy

Think of type hints as **labels on storage bins** in a warehouse:

- The label says "Screws — 3mm" but doesn't physically stop someone from putting a bolt in there
- A careful worker (or an inspector doing a check) reads the label and catches the mistake before it causes a problem down the line
- The warehouse still *works* without labels — but mistakes are much easier to catch with them

---

## Basic Type Hints

```python
def greet(name: str) -> str:
    return f"Hello, {name}!"

def calculate_area(width: float, height: float) -> float:
    return width * height

is_active: bool = True
count: int = 0
price: float = 19.99
username: str = "alice"
```

- Parameter hints go after the parameter name: `name: str`
- The return type hint goes after `->`: `-> str`
- Variable hints work the same way, though they're optional and less commonly used for simple cases

---

## Hinting Collections

```python
from typing import List, Dict, Tuple, Set

names: List[str] = ["Alice", "Bob", "Carol"]
scores: Dict[str, int] = {"Alice": 90, "Bob": 85}
coordinates: Tuple[float, float] = (40.7128, -74.0060)
unique_ids: Set[int] = {1, 2, 3}
```

**Modern Python (3.9+)** lets you use the built-in types directly, without importing from `typing`:

```python
names: list[str] = ["Alice", "Bob", "Carol"]
scores: dict[str, int] = {"Alice": 90, "Bob": 85}
coordinates: tuple[float, float] = (40.7128, -74.0060)
unique_ids: set[int] = {1, 2, 3}
```

Prefer the modern lowercase syntax (`list[str]`) in new code — the `typing` module's capitalized versions (`List[str]`) are considered legacy for this purpose.

---

## `Optional` and `Union`: Values That Could Be Different Types

```python
from typing import Optional, Union

def find_user(user_id: int) -> Optional[str]:
    """Returns a username, or None if not found"""
    users = {1: "alice", 2: "bob"}
    return users.get(user_id)


def process_id(value: Union[int, str]) -> str:
    """Accepts either an int or a str"""
    return str(value)
```

`Optional[str]` means "a `str`, or `None`" — it's shorthand for `Union[str, None]`.

**Modern Python (3.10+)** allows the `|` syntax instead:

```python
def find_user(user_id: int) -> str | None:
    ...

def process_id(value: int | str) -> str:
    ...
```

---

## Type Hints for Functions with Many Cases

```python
from typing import Callable

def apply_twice(func: Callable[[int], int], value: int) -> int:
    """func is a function that takes an int and returns an int"""
    return func(func(value))


def double(x: int) -> int:
    return x * 2

print(apply_twice(double, 3))   # 12
```

`Callable[[int], int]` describes a function's *shape*: it takes one `int` argument and returns an `int` — useful when a function parameter is itself a function, as seen with decorators in [[13 - Decorators]].

---

## Type Hints on Classes

```python
class Point:
    def __init__(self, x: float, y: float) -> None:
        self.x = x
        self.y = y

    def distance_to(self, other: "Point") -> float:
        return ((self.x - other.x) ** 2 + (self.y - other.y) ** 2) ** 0.5
```

Note the quotes around `"Point"` in `distance_to` — this is a **forward reference**, needed because `Point` isn't fully defined yet at the point where its own method is being parsed. This connects directly to [[12 - Dataclasses]], where type annotations aren't just documentation — they're what `@dataclass` actually reads to generate `__init__`.

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: float   # These annotations are required, not optional, for a dataclass
    y: float
```

---

## Type Aliases: Naming Complex Types

When a type hint gets long or reused often, give it a name.

```python
from typing import Dict, List

UserId = int
UserDatabase = Dict[UserId, List[str]]

def get_user_roles(users: UserDatabase, user_id: UserId) -> List[str]:
    return users.get(user_id, [])
```

This makes signatures much easier to read, especially for nested collection types.

---

## Type Checking Tools: `mypy`

Type hints alone don't stop your code from running with the wrong types — you need a **static type checker** like `mypy` to actually catch mismatches.

```python
def add(a: int, b: int) -> int:
    return a + b

result = add("2", "3")   # Python runs this fine!
```

Running `mypy` on this file reports an error *before* you ever run the code:

```
error: Argument 1 to "add" has incompatible type "str"; expected "int"
```

This is the real payoff of type hints: catching an entire category of bugs during development, in your editor or CI pipeline, instead of at runtime.

---

## Real-World Example: A Typed API Function

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class User:
    id: int
    name: str
    email: str


def find_user_by_email(users: list[User], email: str) -> Optional[User]:
    for user in users:
        if user.email == email:
            return user
    return None


def create_user(name: str, email: str, existing_users: list[User]) -> User:
    new_id = max((u.id for u in existing_users), default=0) + 1
    return User(id=new_id, name=name, email=email)


users: list[User] = []
alice = create_user("Alice", "alice@example.com", users)
users.append(alice)

found = find_user_by_email(users, "alice@example.com")
print(found)   # User(id=1, name='Alice', email='alice@example.com')
```

Every function signature here documents exactly what it expects and returns, without needing a single comment.

---

## Benefits of Type Hints

1. **Self-Documenting Code** — Function signatures explain themselves without extra comments
2. **Earlier Bug Detection** — Tools like `mypy` catch type mismatches before runtime
3. **Better Editor Support** — Autocomplete, inline errors, and refactoring tools all improve significantly
4. **Safer Refactoring** — Changing a type ripples through the type checker, flagging every place that needs updating
5. **Clearer APIs** — Anyone reading the function signature immediately understands what to pass in

---

## Common Mistakes

1. Assuming type hints **enforce** types at runtime — they don't, unless you run a separate checker or add manual validation
2. Forgetting `Optional[...]` when a value can legitimately be `None`
3. Using overly broad hints like `Any` everywhere, which defeats the purpose of type checking
4. Not running a type checker (`mypy`) at all — hints without checking are just comments
5. Forgetting quotes around forward references (`"Point"`) when a class refers to itself before it's fully defined

---

## Best Practices

1. **Add type hints to function signatures first** — parameters and return types give the most value for the least effort
2. **Use modern syntax**: `list[str]`, `dict[str, int]`, `int | None` over their `typing` module equivalents
3. **Run `mypy` (or a similar checker) in CI** — hints only catch bugs if something actually checks them
4. **Use `Optional[T]` explicitly** whenever a value might be `None`
5. **Avoid `Any`** unless you genuinely need to accept anything — it silently disables type checking for that value

---

## See also

- [[12 - Dataclasses]] — type annotations as the mechanism `@dataclass` reads to generate methods
- [[13 - Decorators]] — `Callable` type hints for functions passed as arguments
- [[08 - OOP — Class and Static Methods]] — typing constructors and alternative constructors
- [[16 - Defining Functions]] — the function fundamentals type hints annotate
- [[19 - Regex]] — pattern matching, another tool for validating the shape of data

---

## → What's next

You now understand **Typing and Type Hints**:

- Why type hints are optional documentation, not runtime enforcement
- How to annotate functions, variables, and collections
- How `Optional`, `Union`, and the `|` syntax express "could be one of several types"
- Why a static type checker like `mypy` is what actually turns hints into caught bugs

In the next lesson, we will explore **Regex** — pattern matching for text with Python's `re` module, an essential tool for validating, extracting, and cleaning data.

Continue with **[[19 - Regex]]**
