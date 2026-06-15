---
tags:
  - foundations
  - python
  - data-formats
  - serialization
stage: 1
difficulty: Beginner
---

# Working with JSON in Python

**Prev:** [[27 - CSV]] | **Next:** *Coming Soon*

> In this lesson you will learn how to work with **JSON** — the most popular data format for web APIs, configuration files, and data exchange between systems. You will understand what serialization means, why JSON dominates modern programming, and master Python's built-in `json` module.

---

## What is Serialization?

**Serialization** is the process of converting data structures (like Python objects, lists, dictionaries) into a format that can be **stored** or **transmitted** and later **reconstructed**.

Think of it like this:
- Your program works with data in **memory** (Python objects, dictionaries, lists)
- To **save** it to a file or **send** it over the internet, you need to convert it to text or bytes
- Later, you or another program can **read** that text and reconstruct the original data

```python
# In-memory Python data
user = {"name": "Alice", "age": 28, "active": True}

# Serialized (converted to text string)
'{"name": "Alice", "age": 28, "active": true}'

# The serialized string can be saved to a file or sent over HTTP
```

**Deserialization** is the opposite process — converting the stored/transmitted format back into Python objects.

```python
# Text string from a file or API
'{"name": "Alice", "age": 28, "active": true}'

# Deserialized back to Python dictionary
{"name": "Alice", "age": 28, "active": True}
```

---

## Types of Serialized Data Formats

Different formats exist for different needs:

### 1. **JSON** (JavaScript Object Notation)

- **Human-readable** text format
- **Language-independent** (works with Python, JavaScript, Java, PHP, etc.)
- **Lightweight and fast** to parse
- **Best for**: Web APIs, configuration files, data exchange

```json
{
  "name": "Alice",
  "age": 28,
  "skills": ["Python", "SQL", "Docker"]
}
```

### 2. **XML** (eXtensible Markup Language)

- **Verbose** tag-based format
- **Self-describing** (tags explain what data means)
- **Complex** but powerful
- **Best for**: Enterprise systems, SOAP APIs, document storage

```xml
<user>
  <name>Alice</name>
  <age>28</age>
  <skills>
    <skill>Python</skill>
    <skill>SQL</skill>
  </skills>
</user>
```

### 3. **YAML** (YAML Ain't Markup Language)

- **Human-friendly** with minimal syntax
- **Indentation-based** (like Python)
- **Supports comments**
- **Best for**: Configuration files (Docker, Kubernetes, CI/CD)

```yaml
name: Alice
age: 28
skills:
  - Python
  - SQL
  - Docker
```

### 4. **CSV** (Comma-Separated Values)

- **Tabular data** only (rows and columns)
- **Simple but limited** (no nested structures)
- **Best for**: Spreadsheet data, simple exports

```csv
name,age,active
Alice,28,true
Bob,34,false
```

### 5. **Pickle** (Python-specific binary format)

- **Python-only** (not portable to other languages)
- **Can serialize almost any Python object** (functions, classes, etc.)
- **Binary format** (not human-readable)
- **Security risk** (can execute arbitrary code when loaded)
- **Best for**: Caching Python objects, model serialization in controlled environments

```python
import pickle

data = {"name": "Alice", "age": 28}
serialized = pickle.dumps(data)  # b'\x80\x04\x95...'
```

### 6. **Protocol Buffers / MessagePack / BSON**

- **Binary formats** optimized for speed and size
- **Best for**: High-performance systems, microservices, databases

---

## Why JSON is the Most Popular

JSON has become the **de facto standard** for data exchange. Here's why:

### 1. **Language-Independent**

JSON works with virtually every programming language — Python, JavaScript, Java, C#, PHP, Ruby, Go, Rust, etc. This makes it perfect for APIs where the client and server might use different languages.

```python
# Python
{"name": "Alice", "age": 28}

# JavaScript
{"name": "Alice", "age": 28}

# Same format!
```

### 2. **Human-Readable**

You can open a JSON file in any text editor and immediately understand the data structure. This makes debugging, configuration, and learning much easier.

### 3. **Lightweight and Fast**

JSON is simple to parse, small in size, and fast. No complex rules or verbose tags like XML.

```json
// JSON — 50 characters
{"name":"Alice","age":28}

<!-- XML — 63 characters -->
<user><name>Alice</name><age>28</age></user>
```

### 4. **Perfect for Web APIs**

JavaScript (the language of web browsers) can parse JSON **natively** with `JSON.parse()`. This makes JSON the natural choice for:
- REST APIs
- Web services
- Real-time communication (WebSockets)
- Single-page applications (React, Vue, Angular)

### 5. **Native Browser Support**

All modern browsers can handle JSON without any libraries or dependencies.

### 6. **Widely Supported**

Every major database, tool, and framework has built-in JSON support:
- PostgreSQL, MongoDB, MySQL have JSON data types
- Redis can store JSON
- AWS, Google Cloud, Azure APIs all use JSON
- Every HTTP library supports JSON

### 7. **Flexible Structure**

JSON supports:
- Objects (dictionaries)
- Arrays (lists)
- Nesting (objects inside objects)
- All common data types

This covers 95% of real-world data exchange needs.

---

## What is JSON?

**JSON** stands for **JavaScript Object Notation**.

It is a **text-based** data format designed for human and machine readability.

### JSON Data Types

JSON supports six data types:

| JSON Type   | Python Equivalent | Example                          |
|-------------|-------------------|----------------------------------|
| `string`    | `str`             | `"Hello"`, `"Alice"`             |
| `number`    | `int` or `float`  | `42`, `3.14`, `-10`              |
| `boolean`   | `bool`            | `true`, `false`                  |
| `null`      | `None`            | `null`                           |
| `array`     | `list`            | `[1, 2, 3]`, `["a", "b"]`        |
| `object`    | `dict`            | `{"name": "Alice", "age": 28}`   |

**Important differences from Python:**

- JSON uses `true` / `false` (lowercase), Python uses `True` / `False` (capitalized)
- JSON uses `null`, Python uses `None`
- JSON strings **must** use double quotes (`"name"`) — single quotes are invalid
- JSON keys **must** be strings

### Valid JSON Example

```json
{
  "name": "Alice",
  "age": 28,
  "active": true,
  "address": null,
  "skills": ["Python", "SQL", "Docker"],
  "profile": {
    "bio": "Software engineer",
    "github": "alice"
  }
}
```

---

## The `json` Module

Python provides a built-in `json` module to handle JSON serialization and deserialization.

You must **import** it:

```python
import json
```

### Core Functions

| Function          | Purpose                                      | Input       | Output       |
|-------------------|----------------------------------------------|-------------|--------------|
| `json.dumps()`    | Convert Python object → JSON string          | Python obj  | `str`        |
| `json.dump()`     | Convert Python object → JSON file            | Python obj  | file         |
| `json.loads()`    | Convert JSON string → Python object          | `str`       | Python obj   |
| `json.load()`     | Convert JSON file → Python object            | file        | Python obj   |

**Memory trick:**
- `dumps` = dump **string** (returns a string)
- `dump` = dump to **file**
- `loads` = load **string** (from a string)
- `load` = load from **file**

---

## Serialization: Python → JSON

### 1. `json.dumps()` — Serialize to String

Converts a Python object to a JSON-formatted **string**.

```python
import json

data = {
    "name": "Alice",
    "age": 28,
    "skills": ["Python", "SQL"],
    "active": True
}

json_string = json.dumps(data)
print(json_string)
# {"name": "Alice", "age": 28, "skills": ["Python", "SQL"], "active": true}

print(type(json_string))  # <class 'str'>
```

**Type Conversion During Serialization:**

| Python Type | JSON Type |
|-------------|-----------|
| `dict`      | `object`  |
| `list`, `tuple` | `array` |
| `str`       | `string`  |
| `int`, `float` | `number` |
| `True`      | `true`    |
| `False`     | `false`   |
| `None`      | `null`    |

### Pretty-Printing JSON

Use `indent` for human-readable output:

```python
data = {"name": "Alice", "age": 28, "skills": ["Python", "SQL"]}

# Compact (default)
print(json.dumps(data))
# {"name": "Alice", "age": 28, "skills": ["Python", "SQL"]}

# Pretty-printed
print(json.dumps(data, indent=2))
# {
#   "name": "Alice",
#   "age": 28,
#   "skills": [
#     "Python",
#     "SQL"
#   ]
# }

# Custom indentation
print(json.dumps(data, indent=4))
```

**Use `indent=2` or `indent=4` for:**
- Configuration files
- API responses you want to debug
- Files that humans will edit

**Use default (no indent) for:**
- Production APIs (smaller size)
- Data transmission (faster)

### Sorting Keys

Use `sort_keys=True` to order keys alphabetically:

```python
data = {"name": "Alice", "age": 28, "city": "Berlin"}

print(json.dumps(data, sort_keys=True, indent=2))
# {
#   "age": 28,
#   "city": "Berlin",
#   "name": "Alice"
# }
```

**Why use `sort_keys`?**
- Consistent output (easier to compare files, version control)
- Easier to read long JSON files

### Non-Serializable Types

Not all Python objects can be serialized to JSON:

```python
import json
import datetime

data = {
    "name": "Alice",
    "created": datetime.datetime.now()  # datetime is not JSON-serializable
}

# This will raise TypeError
json.dumps(data)
# TypeError: Object of type datetime is not JSON serializable
```

**Objects that cannot be serialized by default:**
- `datetime`, `date`, `time`
- `set`, `frozenset`
- Custom classes
- Functions, lambdas
- File objects

**Solution:** Convert them to serializable types first:

```python
import json
import datetime

data = {
    "name": "Alice",
    "created": datetime.datetime.now().isoformat()  # Convert to string
}

print(json.dumps(data))
# {"name": "Alice", "created": "2026-06-15T14:30:00.123456"}
```

### 2. `json.dump()` — Serialize to File

Writes JSON directly to a file.

```python
import json

data = {
    "name": "Alice",
    "age": 28,
    "skills": ["Python", "SQL"]
}

with open("user.json", "w", encoding="utf-8") as file:
    json.dump(data, file, indent=2)
```

**Result: `user.json`**

```json
{
  "name": "Alice",
  "age": 28,
  "skills": [
    "Python",
    "SQL"
  ]
}
```

**Always use `with` statement** — it automatically closes the file even if an error occurs.

**Always specify `encoding="utf-8"`** — ensures proper handling of non-ASCII characters (é, ñ, 中, etc.).

---

## Deserialization: JSON → Python

### 1. `json.loads()` — Deserialize from String

Converts a JSON-formatted **string** back to a Python object.

```python
import json

json_string = '{"name": "Alice", "age": 28, "active": true}'

data = json.loads(json_string)
print(data)
# {'name': 'Alice', 'age': 28, 'active': True}

print(type(data))  # <class 'dict'>

# Access like a normal dictionary
print(data["name"])  # Alice
print(data["age"])   # 28
```

**Type Conversion During Deserialization:**

| JSON Type   | Python Type |
|-------------|-------------|
| `object`    | `dict`      |
| `array`     | `list`      |
| `string`    | `str`       |
| `number` (int) | `int`    |
| `number` (float) | `float` |
| `true`      | `True`      |
| `false`     | `False`     |
| `null`      | `None`      |

### Handling Invalid JSON

If the JSON string is malformed, `json.loads()` raises `JSONDecodeError`:

```python
import json

invalid_json = '{"name": "Alice", "age": 28'  # Missing closing brace

try:
    data = json.loads(invalid_json)
except json.JSONDecodeError as e:
    print(f"Invalid JSON: {e}")
    # Invalid JSON: Expecting ',' delimiter: line 1 column 29 (char 28)
```

**Common JSON errors:**
- Missing quotes around keys
- Single quotes instead of double quotes
- Trailing commas
- Missing closing brackets/braces
- Comments (JSON doesn't support comments!)

### 2. `json.load()` — Deserialize from File

Reads JSON directly from a file.

**Sample file: `user.json`**

```json
{
  "name": "Alice",
  "age": 28,
  "skills": ["Python", "SQL"]
}
```

```python
import json

with open("user.json", "r", encoding="utf-8") as file:
    data = json.load(file)

print(data)
# {'name': 'Alice', 'age': 28, 'skills': ['Python', 'SQL']}

print(data["name"])   # Alice
print(data["skills"]) # ['Python', 'SQL']
```

---

## Working with Complex JSON

### Nested Structures

JSON can contain objects inside objects, arrays inside objects, etc.

```python
import json

data = {
    "user": {
        "id": 1,
        "name": "Alice",
        "contact": {
            "email": "alice@example.com",
            "phone": "+1234567890"
        }
    },
    "posts": [
        {"id": 101, "title": "First Post"},
        {"id": 102, "title": "Second Post"}
    ]
}

# Serialize
json_string = json.dumps(data, indent=2)
print(json_string)

# Deserialize
parsed = json.loads(json_string)

# Access nested data
print(parsed["user"]["name"])                  # Alice
print(parsed["user"]["contact"]["email"])      # alice@example.com
print(parsed["posts"][0]["title"])             # First Post
```

### Working with Lists of Objects

**Sample file: `users.json`**

```json
[
  {"id": 1, "name": "Alice", "age": 28},
  {"id": 2, "name": "Bob", "age": 34},
  {"id": 3, "name": "Charlie", "age": 22}
]
```

```python
import json

with open("users.json", "r", encoding="utf-8") as file:
    users = json.load(file)  # Returns a list

# Iterate over users
for user in users:
    print(f"{user['name']} is {user['age']} years old")

# Alice is 28 years old
# Bob is 34 years old
# Charlie is 22 years old

# Filter users
adults = [u for u in users if u["age"] >= 25]
print(adults)
# [{'id': 1, 'name': 'Alice', 'age': 28}, {'id': 2, 'name': 'Bob', 'age': 34}]
```

---

## Real-World Examples

### 1. Configuration Files

**`config.json`**

```json
{
  "database": {
    "host": "localhost",
    "port": 5432,
    "name": "myapp"
  },
  "debug": true,
  "max_connections": 100
}
```

```python
import json

with open("config.json", "r", encoding="utf-8") as file:
    config = json.load(file)

# Access configuration
db_host = config["database"]["host"]
debug_mode = config["debug"]

print(f"Connecting to {db_host} in {'debug' if debug_mode else 'production'} mode")
# Connecting to localhost in debug mode
```

### 2. Saving User Data

```python
import json

def save_user(user_data, filename="user.json"):
    """Save user data to JSON file."""
    with open(filename, "w", encoding="utf-8") as file:
        json.dump(user_data, file, indent=2)
    print(f"User data saved to {filename}")

def load_user(filename="user.json"):
    """Load user data from JSON file."""
    try:
        with open(filename, "r", encoding="utf-8") as file:
            return json.load(file)
    except FileNotFoundError:
        print(f"File {filename} not found")
        return None
    except json.JSONDecodeError:
        print(f"Invalid JSON in {filename}")
        return None

# Save
user = {"name": "Alice", "age": 28, "email": "alice@example.com"}
save_user(user)

# Load
loaded_user = load_user()
print(loaded_user)
# {'name': 'Alice', 'age': 28, 'email': 'alice@example.com'}
```

### 3. Working with Web APIs

Many web APIs return JSON responses:

```python
import json

# Simulated API response (in real code, use the requests library)
api_response = '''
{
  "status": "success",
  "data": {
    "user": {
      "id": 123,
      "username": "alice",
      "followers": 1520
    }
  }
}
'''

response = json.loads(api_response)

if response["status"] == "success":
    user = response["data"]["user"]
    print(f"User: {user['username']}, Followers: {user['followers']}")
    # User: alice, Followers: 1520
```

### 4. Data Export and Backup

```python
import json
from datetime import datetime

def export_data(data, filename=None):
    """Export data with timestamp."""
    if filename is None:
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        filename = f"backup_{timestamp}.json"
    
    with open(filename, "w", encoding="utf-8") as file:
        json.dump(data, file, indent=2)
    
    print(f"Data exported to {filename}")

# Export
users = [
    {"id": 1, "name": "Alice"},
    {"id": 2, "name": "Bob"}
]

export_data(users)
# Data exported to backup_20260615_143000.json
```

---

## JSON vs CSV: When to Use Each

| Factor              | JSON                                | CSV                             |
|---------------------|-------------------------------------|---------------------------------|
| **Structure**       | Nested, hierarchical                | Flat, tabular only              |
| **Data Types**      | Preserves types (string, number, boolean, null) | Everything is text |
| **Human Readable**  | Yes, with proper indentation        | Yes, very simple                |
| **Use Case**        | APIs, configs, complex data         | Spreadsheets, simple exports    |
| **Size**            | Slightly larger (includes keys)     | Smaller (no repeated keys)      |
| **Speed**           | Fast to parse                       | Very fast                       |
| **Best For**        | Nested objects, mixed data types    | Simple tables, data science     |

**Rule of thumb:**
- Use **JSON** when you have nested data, mixed types, or need to exchange data with APIs
- Use **CSV** when you have simple tabular data (rows and columns) and need maximum compatibility with Excel/spreadsheets

---

## Common Mistakes & Gotchas

### 1. Single Quotes in JSON

JSON requires **double quotes** — single quotes are invalid.

```python
# Invalid JSON
invalid = "{'name': 'Alice'}"  # Single quotes

# Valid JSON
valid = '{"name": "Alice"}'     # Double quotes

# Python uses single quotes by default, but json.dumps() always produces double quotes
data = {"name": "Alice"}
print(json.dumps(data))  # {"name": "Alice"} ✓
```

### 2. Trailing Commas

JSON does **not allow** trailing commas (Python does).

```python
# Invalid JSON
invalid = '{"name": "Alice", "age": 28,}'  # Trailing comma

# Valid JSON
valid = '{"name": "Alice", "age": 28}'
```

### 3. Boolean Capitalization

```python
# JSON uses lowercase
'{"active": true}'   # ✓ Valid JSON

# Python uses capitalized
{"active": True}     # ✓ Valid Python

# json.dumps() automatically converts
data = {"active": True}
json.dumps(data)  # '{"active": true}'

# json.loads() automatically converts back
json_string = '{"active": true}'
json.loads(json_string)  # {'active': True}
```

### 4. Tuples Become Lists

JSON has no tuple type — tuples are serialized as arrays and deserialize as lists.

```python
data = {"coords": (10, 20)}  # Tuple

json_string = json.dumps(data)
print(json_string)  # {"coords": [10, 20]}

parsed = json.loads(json_string)
print(parsed["coords"])  # [10, 20] — now a list!
print(type(parsed["coords"]))  # <class 'list'>
```

### 5. Sets Are Not Serializable

```python
data = {"tags": {"python", "json", "tutorial"}}  # Set

# This will raise TypeError
json.dumps(data)  # TypeError: Object of type set is not JSON serializable

# Solution: Convert to list
data = {"tags": list({"python", "json", "tutorial"})}
json.dumps(data)  # ✓ Works
```

### 6. Forgetting `encoding="utf-8"`

```python
# Missing encoding can cause issues with non-ASCII characters
data = {"name": "José", "city": "São Paulo"}

# Bad — may fail on some systems
with open("user.json", "w") as file:
    json.dump(data, file)

# Good — always specify UTF-8
with open("user.json", "w", encoding="utf-8") as file:
    json.dump(data, file)
```

### 7. Modifying JSON in Place

JSON is text — you must deserialize, modify, then serialize again.

```python
# Read JSON
with open("user.json", "r", encoding="utf-8") as file:
    data = json.load(file)

# Modify in Python
data["age"] = 29
data["updated"] = "2026-06-15"

# Write back to JSON
with open("user.json", "w", encoding="utf-8") as file:
    json.dump(data, file, indent=2)
```

---

## Best Practices

1. **Always use `with` statement** when working with files — ensures files are properly closed
2. **Specify `encoding="utf-8"`** for all file operations
3. **Use `indent=2` or `indent=4`** for human-readable JSON files
4. **Handle `JSONDecodeError`** when loading untrusted JSON
5. **Validate data types** after deserialization if critical
6. **Use `sort_keys=True`** for version control and consistency
7. **Convert non-serializable types** (datetime, sets) before serialization
8. **Don't store sensitive data** in plain JSON files (passwords, API keys) — use environment variables or encrypted storage
9. **Validate JSON structure** before processing (especially from external APIs)
10. **Use meaningful key names** — be consistent with naming conventions (snake_case or camelCase)

---

## See also

- [[27 - CSV]] — working with tabular data in CSV format; comparison with JSON
- [[26 - File I-O]] — fundamental file operations; how `open()` works with encodings and modes
- [[25 - Error Handling]] — catching `JSONDecodeError` and `FileNotFoundError` when loading JSON
- [[14 - Dictionaries]] — JSON objects become Python dictionaries; nested access patterns
- [[12 - Lists]] — JSON arrays become Python lists; iteration and comprehensions
- [[23 - Modules and Imports]] — how the `json` module is imported and used

---

## → What's next

You now understand serialization, why JSON is the dominant data format, and how to work with JSON in Python. JSON skills are essential for working with web APIs, configuration files, and data storage. 

In the **Intermediate** section, you will learn about **custom serialization** — how to serialize complex Python objects (classes, datetime, custom types) by writing your own serialization logic. This requires understanding Object-Oriented Programming (OOP), which will be covered in `02 - Intermediate/`.
