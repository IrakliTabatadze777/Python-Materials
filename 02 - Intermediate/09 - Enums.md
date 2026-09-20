---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# Enums

**Prev:** [[08 - OOP — Class and Static Methods]] | **Next:** [[10 - Collections Module]]

> In this lesson you will learn about **Enums** — a way to define a fixed, named set of related constants. You will learn why raw strings and numbers scattered through your code are a common source of bugs, and how `enum.Enum` fixes that with a clear, type-safe alternative.

---

## The Problem: Magic Strings and Numbers

It's common to represent a fixed set of options as plain strings or numbers:

```python
def process_order(status):
    if status == "pending":
        print("Waiting for payment")
    elif status == "shipped":
        print("On its way")
    elif status == "delivered":
        print("Order complete")

process_order("shiped")   # Typo! No error, just silently does nothing
```

Nothing stops you from misspelling `"shipped"`, and there's no single place that documents every valid status. This is often called a **magic string** — a value whose meaning is implicit and unchecked.

### Real-Life Analogy

Think of a **traffic light**:

- There are only ever three valid states: red, yellow, green
- You'd never accept "gren" or "5" as a traffic light state — the set of valid values is fixed and well-known
- A traffic light doesn't reinvent its state as a raw number each time; it has a small, named, closed set of possibilities

An `Enum` is Python's way of expressing exactly that kind of fixed, named set.

---

## Defining an Enum

```python
from enum import Enum

class OrderStatus(Enum):
    PENDING = "pending"
    SHIPPED = "shipped"
    DELIVERED = "delivered"


def process_order(status):
    if status == OrderStatus.PENDING:
        print("Waiting for payment")
    elif status == OrderStatus.SHIPPED:
        print("On its way")
    elif status == OrderStatus.DELIVERED:
        print("Order complete")


process_order(OrderStatus.SHIPPED)   # On its way
```

Now `OrderStatus.SHIPED` (misspelled) would raise an `AttributeError` immediately — Python knows exactly what the valid members are, and a typo is caught right away instead of silently doing nothing.

---

## Accessing Enum Members

```python
from enum import Enum

class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3


print(Color.RED)          # Color.RED
print(Color.RED.name)      # RED
print(Color.RED.value)     # 1

# Look up a member by its value
print(Color(2))            # Color.GREEN

# Look up a member by its name
print(Color["BLUE"])       # Color.BLUE
```

- `.name` gives the member's identifier as a string
- `.value` gives the underlying value assigned to it
- `EnumClass(value)` looks up a member **by value**
- `EnumClass["NAME"]` looks up a member **by name**

---

## Enum Members Are Singletons and Compare by Identity

```python
from enum import Enum

class Status(Enum):
    ACTIVE = "active"
    INACTIVE = "inactive"


a = Status.ACTIVE
b = Status.ACTIVE

print(a == b)   # True
print(a is b)   # True - there's only ever ONE Status.ACTIVE object
```

Every reference to `Status.ACTIVE` points to the exact same object — this is what makes `is` comparisons safe and fast with enums, unlike with regular strings.

---

## Iterating Over All Members

```python
from enum import Enum

class Direction(Enum):
    NORTH = "N"
    SOUTH = "S"
    EAST = "E"
    WEST = "W"


for direction in Direction:
    print(direction.name, direction.value)
```

**Output:**
```
NORTH N
SOUTH S
EAST E
WEST W
```

This is useful for validation, building menus, or generating documentation — you never have to maintain a separate list of "all the valid values" by hand.

---

## Enums with Methods

Just like a regular class from [[01 - OOP — Classes and Objects]], an `Enum` can have its own methods.

```python
from enum import Enum

class Weekday(Enum):
    MONDAY = 1
    TUESDAY = 2
    WEDNESDAY = 3
    THURSDAY = 4
    FRIDAY = 5
    SATURDAY = 6
    SUNDAY = 7

    def is_weekend(self):
        return self in (Weekday.SATURDAY, Weekday.SUNDAY)


print(Weekday.SATURDAY.is_weekend())   # True
print(Weekday.MONDAY.is_weekend())      # False
```

This connects to [[07 - OOP — Dunder Methods]] and [[08 - OOP — Class and Static Methods]] — an `Enum` is a real class under the hood, so it supports regular methods, `@classmethod`, `@staticmethod`, and even `@property`.

---

## `auto()`: Letting Python Assign Values

When the actual value doesn't matter — you only care that each member is distinct — use `auto()` instead of hand-picking numbers.

```python
from enum import Enum, auto

class Status(Enum):
    PENDING = auto()
    APPROVED = auto()
    REJECTED = auto()


print(Status.PENDING.value)    # 1
print(Status.APPROVED.value)   # 2
print(Status.REJECTED.value)   # 3
```

`auto()` assigns increasing integers automatically, so you never have to manually renumber every member after inserting a new one in the middle.

---

## `IntEnum`: When You Need Real Integer Behavior

A regular `Enum` member doesn't behave like a number in comparisons, even if its value is one:

```python
from enum import Enum

class Priority(Enum):
    LOW = 1
    MEDIUM = 2
    HIGH = 3


print(Priority.LOW < Priority.HIGH)   # TypeError!
```

`IntEnum` fixes this by making members behave as actual integers wherever needed:

```python
from enum import IntEnum

class Priority(IntEnum):
    LOW = 1
    MEDIUM = 2
    HIGH = 3


print(Priority.LOW < Priority.HIGH)    # True
print(Priority.HIGH == 3)               # True
```

Use `IntEnum` specifically when you need ordering or interoperability with code expecting plain integers; use a plain `Enum` otherwise, to avoid accidentally treating unrelated numbers as equivalent.

---

## `Flag`: Combinable Options

For sets of options that can be combined together (like file permissions), `Flag` supports bitwise operations.

```python
from enum import Flag, auto

class Permission(Flag):
    READ = auto()
    WRITE = auto()
    EXECUTE = auto()


user_permissions = Permission.READ | Permission.WRITE

print(user_permissions)                        # Permission.READ|WRITE
print(Permission.READ in user_permissions)       # True
print(Permission.EXECUTE in user_permissions)    # False
```

This is a specialized tool — most enums represent mutually exclusive choices, but `Flag` is the right fit when several options can be active at once.

---

## Real-World Example: Handling HTTP-Like Status Codes

```python
from enum import Enum

class HttpStatus(Enum):
    OK = 200
    CREATED = 201
    BAD_REQUEST = 400
    NOT_FOUND = 404
    SERVER_ERROR = 500

    @property
    def is_success(self):
        return 200 <= self.value < 300

    @property
    def is_error(self):
        return self.value >= 400


def handle_response(status):
    if status.is_success:
        print(f"Success: {status.name} ({status.value})")
    elif status.is_error:
        print(f"Error: {status.name} ({status.value})")


handle_response(HttpStatus.OK)            # Success: OK (200)
handle_response(HttpStatus.NOT_FOUND)      # Error: NOT_FOUND (404)
```

This is far clearer and safer than comparing raw integers scattered throughout a codebase — `HttpStatus.NOT_FOUND` documents itself, while `404` alone does not.

---

## Enums in Dataclasses

Enums combine naturally with the dataclasses from [[12 - Dataclasses]]:

```python
from dataclasses import dataclass
from enum import Enum

class TaskStatus(Enum):
    TODO = "todo"
    IN_PROGRESS = "in_progress"
    DONE = "done"

@dataclass
class Task:
    title: str
    status: TaskStatus = TaskStatus.TODO


task = Task("Write lesson")
print(task.status)   # TaskStatus.TODO

task.status = TaskStatus.DONE
print(task.status)   # TaskStatus.DONE
```

Using an `Enum` field instead of a raw string guarantees `task.status` can only ever be one of the defined, valid values.

---

## Benefits of Enums

1. **Prevents Invalid Values** — Only the defined members exist; there's no way to accidentally create `Status.TYPOED`
2. **Self-Documenting** — All valid options are declared in one place, visible at a glance
3. **Safe Comparisons** — Members compare by identity, avoiding subtle string-matching bugs
4. **Iterable** — Loop over every valid option without maintaining a separate list
5. **IDE-Friendly** — Autocomplete shows every valid member, unlike a raw string or number

---

## Common Mistakes

1. Using plain strings or numbers for a fixed set of options instead of reaching for `Enum`
2. Comparing a plain `Enum` member with `<`/`>` — use `IntEnum` if ordering actually matters
3. Using `Flag` for options that are actually mutually exclusive — a plain `Enum` is the right choice there
4. Forgetting that `EnumClass(value)` looks up by **value**, while `EnumClass["NAME"]` looks up by **name** — mixing these up raises a `ValueError` or `KeyError`
5. Recreating the same conceptual "status" as different raw strings across different parts of a codebase, defeating the purpose of a single source of truth

---

## Best Practices

1. **Reach for `Enum` whenever a variable can only take one of a small, fixed set of values**
2. **Use `auto()`** when the actual underlying value doesn't matter, only that each member is distinct
3. **Use `IntEnum`** only when you specifically need ordering or integer interoperability
4. **Use `Flag`** only for genuinely combinable options, not mutually exclusive ones
5. **Add methods or properties to enums** (like `is_weekend`, `is_success`) instead of writing separate lookup functions elsewhere

---

## See also

- [[01 - OOP — Classes and Objects]] — enums are real classes under the hood
- [[07 - OOP — Dunder Methods]] and [[08 - OOP — Class and Static Methods]] — enums support the same method types
- [[10 - Collections Module]] — specialized data structures, another standard-library toolbox
- [[11 - Closures]] — another way to represent state, complementary to enums for fixed values
- [[12 - Dataclasses]] — using enum fields for validated, self-documenting attributes

---

## → What's next

You now understand **Enums**:

- Why magic strings and numbers are a common source of silent bugs
- How to define, access, and iterate over `Enum` members
- The difference between `Enum`, `IntEnum`, and `Flag`, and when each applies
- How enums combine naturally with dataclasses for safer, self-documenting data

In the next lesson, we will explore the **Collections Module** — a standard-library toolbox of specialized, ready-made data structures that go beyond the built-in list, dict, and set.

Continue with **[[10 - Collections Module]]**
