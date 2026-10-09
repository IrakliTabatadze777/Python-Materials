---
tags:
  - intermediate
  - python
  - oop
  - serialization
stage: 2
difficulty: Intermediate
---

# Custom Serialization

**Prev:** [[21 - Pathlib]] | **Next:** [[23 - Custom Exceptions]]

> In this lesson you will learn about **Serialization** — converting your own Python objects into a format like JSON that can be saved to a file, sent over a network, or stored in a database, and converting that format back into objects again.

---

## What is Serialization?

**Serialization** is the process of converting a Python object into a format that can be stored or transmitted — most commonly JSON, but also formats like CSV or pickle.

**Deserialization** is the reverse: turning that stored format back into a usable Python object.

### Real-Life Analogy

Think of **flat-pack furniture**:

- A finished bookshelf (your object) is too bulky to ship efficiently
- The manufacturer disassembles it into flat, labeled parts (serialization) that fit in a small box
- You (or another program) later reassemble it using the same instructions (deserialization) to get back an equivalent bookshelf

The bookshelf "in the box" isn't usable directly — it needs to be reconstructed first.

---

## JSON Basics: What Works Out of the Box

Python's built-in `json` module handles simple types automatically.

```python
import json

data = {
    "name": "Alice",
    "age": 30,
    "is_active": True,
    "tags": ["admin", "verified"]
}

json_string = json.dumps(data)
print(json_string)
# {"name": "Alice", "age": 30, "is_active": true, "tags": ["admin", "verified"]}

parsed = json.loads(json_string)
print(parsed["name"])   # Alice
```

`json.dumps()` converts a Python object to a JSON string. `json.loads()` parses a JSON string back into Python objects (dicts, lists, strings, numbers, booleans, `None`).

**The problem:** `json.dumps()` only knows how to handle a fixed set of built-in types. It has no idea how to serialize a *custom class*.

```python
class User:
    def __init__(self, name, age):
        self.name = name
        self.age = age

user = User("Alice", 30)
json.dumps(user)   # TypeError: Object of type User is not JSON serializable
```

---

## Serializing Custom Objects: The Manual Way

The simplest fix is to convert your object to a plain dictionary yourself before serializing.

```python
class User:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def to_dict(self):
        return {"name": self.name, "age": self.age}


user = User("Alice", 30)
json_string = json.dumps(user.to_dict())
print(json_string)   # {"name": "Alice", "age": 30}
```

And to reconstruct the object:

```python
class User:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def to_dict(self):
        return {"name": self.name, "age": self.age}

    @classmethod
    def from_dict(cls, data):
        return cls(data["name"], data["age"])


data = json.loads(json_string)
user = User.from_dict(data)
print(user.name, user.age)   # Alice 30
```

This uses the alternative-constructor pattern from [[08 - OOP — Class and Static Methods]] — `from_dict` is a `@classmethod` that builds a `User` from parsed JSON data.

---

## A Custom `default` Function for `json.dumps`

Instead of manually converting every object before serializing, you can teach `json.dumps` how to handle your class with a `default` function.

```python
class User:
    def __init__(self, name, age):
        self.name = name
        self.age = age


def user_encoder(obj):
    if isinstance(obj, User):
        return {"name": obj.name, "age": obj.age}
    raise TypeError(f"Object of type {type(obj).__name__} is not JSON serializable")


user = User("Alice", 30)
print(json.dumps(user, default=user_encoder))
# {"name": "Alice", "age": 30}
```

`default` is called automatically whenever `json.dumps` encounters something it doesn't know how to serialize — here, that's the `User` object.

---

## Dataclasses Make This Easier

Recall from [[12 - Dataclasses]] that `@dataclass` gives you structured fields for free. Combined with `dataclasses.asdict()`, serialization becomes almost trivial.

```python
from dataclasses import dataclass, asdict
import json

@dataclass
class User:
    name: str
    age: int


user = User("Alice", 30)

data = asdict(user)          # {'name': 'Alice', 'age': 30}
json_string = json.dumps(data)
print(json_string)            # {"name": "Alice", "age": 30}

# Reconstructing is just unpacking the dict back into the dataclass
restored = User(**json.loads(json_string))
print(restored)   # User(name='Alice', age=30)
```

`asdict()` recursively converts a dataclass instance (including nested dataclasses) into a plain dictionary — no manual `to_dict()` method required.

---

## Custom Decoding with `object_hook`

`json.loads` can also be taught to reconstruct custom objects automatically, using `object_hook`.

```python
def user_decoder(data):
    if "name" in data and "age" in data:
        return User(data["name"], data["age"])
    return data


json_string = '{"name": "Alice", "age": 30}'
user = json.loads(json_string, object_hook=user_decoder)
print(type(user))   # <class '__main__.User'>
print(user.name)     # Alice
```

`object_hook` is called for every JSON object encountered during parsing — this makes it possible to reconstruct custom classes directly from `json.loads`, not just plain dicts.

---

## Handling Nested Objects

Real-world data is often nested — objects containing other objects.

```python
from dataclasses import dataclass, asdict
import json

@dataclass
class Address:
    city: str
    zip_code: str

@dataclass
class User:
    name: str
    address: Address


user = User("Alice", Address("Tbilisi", "0100"))

data = asdict(user)
print(json.dumps(data))
# {"name": "Alice", "address": {"city": "Tbilisi", "zip_code": "0100"}}

# Reconstructing nested dataclasses needs to be done explicitly
parsed = json.loads(json.dumps(data))
restored = User(name=parsed["name"], address=Address(**parsed["address"]))
print(restored)   # User(name='Alice', address=Address(city='Tbilisi', zip_code='0100'))
```

`asdict()` flattens nested dataclasses automatically when *encoding*, but reconstructing them on the way back in has to rebuild the nested structure explicitly — plain dictionaries don't know they should become an `Address`.

---

## Handling Types JSON Doesn't Support Natively

JSON has no native concept of dates, sets, or many other Python types — you need to convert them explicitly.

```python
from datetime import datetime
import json

def custom_encoder(obj):
    if isinstance(obj, datetime):
        return obj.isoformat()   # Convert to a string JSON understands
    raise TypeError(f"Object of type {type(obj).__name__} is not JSON serializable")


data = {"created_at": datetime(2026, 9, 16, 12, 0)}
print(json.dumps(data, default=custom_encoder))
# {"created_at": "2026-09-16T12:00:00"}
```

On the way back in, you have to explicitly parse the string back into a `datetime` — JSON itself can't tell you it was ever one.

---

## Real-World Example: Saving and Loading Application State

```python
from dataclasses import dataclass, asdict
import json

@dataclass
class Task:
    title: str
    done: bool = False


def save_tasks(tasks, filepath):
    with open(filepath, "w", encoding="utf-8") as f:
        json.dump([asdict(task) for task in tasks], f, indent=2)


def load_tasks(filepath):
    with open(filepath, "r", encoding="utf-8") as f:
        data = json.load(f)
    return [Task(**item) for item in data]


tasks = [Task("Write lesson"), Task("Review PR", done=True)]
save_tasks(tasks, "tasks.json")

loaded = load_tasks("tasks.json")
print(loaded)   # [Task(title='Write lesson', done=False), Task(title='Review PR', done=True)]
```

This combines several earlier lessons: [[15 - Context Managers]] for safely opening the file, [[12 - Dataclasses]] for the `Task` structure, and [[20 - Encoding]] for the explicit `encoding="utf-8"`.

---

## Benefits of Custom Serialization

1. **Persistence** — Save application state to disk and restore it later
2. **Interoperability** — JSON is understood by virtually every language and system, not just Python
3. **API Communication** — Most web APIs send and receive JSON, so this is essential for networked applications
4. **Clear Boundaries** — Explicit `to_dict`/`from_dict` methods document exactly what data crosses the serialization boundary
5. **Works Well with Dataclasses** — `asdict()` removes most of the manual conversion work for simple, flat objects

---

## Common Mistakes

1. Assuming `json.dumps()` can serialize any Python object automatically — it can't, without help
2. Forgetting that `asdict()` flattens nested dataclasses on the way out, but reconstructing them on the way in must be done manually
3. Not handling types JSON doesn't support (dates, sets, custom classes) with a `default` function
4. Losing precision or type information — e.g., JSON has no `int` vs `float` distinction the way Python does for all cases, and tuples become lists
5. Forgetting `encoding="utf-8"` when reading/writing JSON files containing non-ASCII text

---

## Best Practices

1. **Use `dataclasses.asdict()`** for simple, flat objects instead of writing manual `to_dict()` methods
2. **Define `to_dict`/`from_dict` (or `default`/`object_hook`)** explicitly for classes with custom logic or nested nested nested structures
3. **Always specify `encoding="utf-8"`** when reading or writing JSON files
4. **Convert non-JSON-native types explicitly** (dates to ISO strings, sets to lists) rather than hoping they'll "just work"
5. **Keep serialization logic close to the class it serializes** — a `to_dict` method on the class itself is easy to find and maintain

---

## See also

- [[08 - OOP — Class and Static Methods]] — `from_dict` as an alternative constructor pattern
- [[12 - Dataclasses]] — `asdict()` and how dataclass fields map naturally to JSON
- [[15 - Context Managers]] — safely opening files for reading and writing serialized data
- [[20 - Encoding]] — why explicit UTF-8 matters when saving JSON containing non-English text
- [[21 - Pathlib]] — constructing the file paths used when saving and loading serialized data
- [[23 - Custom Exceptions]] — raising clear, specific errors when serialization or deserialization fails

---

## → What's next

You now understand **Custom Serialization**:

- Why `json.dumps()` can't handle custom objects without help
- How to write `to_dict`/`from_dict` methods, or use `default`/`object_hook` for automatic conversion
- How `dataclasses.asdict()` simplifies serializing simple, flat objects
- How to handle nested objects and types JSON doesn't support natively

In the next lesson, we will explore **Custom Exceptions** — how to define your own exception types so error handling communicates exactly what went wrong, instead of relying only on generic built-in exceptions.

Continue with **[[23 - Custom Exceptions]]**
