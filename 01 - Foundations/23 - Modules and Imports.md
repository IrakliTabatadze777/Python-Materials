---
tags:
  - foundations
  - python
  - modules
stage: 1
difficulty: Beginner
---

# Modules and Imports in Python

**Prev:** [[22 - Built-in Functions]] | **Next:** [[24 - Virtual Environments and pip]]

> In this lesson you will learn how to organize your code into multiple files using **modules** and how to reuse code with **imports**. This is one of the most important concepts in Python for building real, maintainable applications.

---

## What is a Module?

A **module** is simply a file with a `.py` extension that contains Python code — variables, functions, classes, or even just data.

Think of it like this:
- A single Python file (`script.py`) is like a small notebook.
- A **module** is like a specialized notebook with related notes (e.g., all math functions in one file).
- A **package** is like a whole folder of related notebooks.

**Example:** The `math` module is a file on your computer that contains many mathematical functions.

---

## Why Do We Need Modules?

As your programs grow, putting everything in one file becomes messy and hard to maintain. Modules solve this by allowing you to:

- **Organize code** — Group related functionality together
- **Reuse code** — Write once, use in many projects
- **Collaborate** — Different team members can work on different modules
- **Improve readability** — Smaller files are easier to understand
- **Avoid name conflicts** — Keep variable/function names separate

---

## How to Import Modules

### 1. Import the entire module (Most Recommended)

```python
import math

print(math.sqrt(16))      # 4.0
print(math.pi)            # 3.141592653589793
```

**Deep explanation:**
- `import math` tells Python: "Load the math module into memory."
- You must use `module_name.item_name` to access anything inside.
- This style is clear and avoids name collisions.

---

### 2. Import specific items from a module

```python
from math import sqrt, pi, floor

print(sqrt(25))           # 5.0
print(pi)                 # 3.14159...
print(floor(4.9))         # 4
```

**Explanation:**
- Only the named items are brought into your current file's namespace.
- You can use them directly without prefixing `math.`
- Good when you only need a few things.

---

### 3. Import with an alias

```python
import math as m
import pandas as pd     # very common in data science
import numpy as np

print(m.sqrt(9))        # 3.0
```

**When to use aliases:**
- Module name is very long
- You want to use a shorter or more convenient name
- Standard conventions in the community (e.g., `pd` for pandas)

---

### 4. Import everything (Use with caution!)

```python
from math import *   # imports sqrt, pi, sin, cos, etc.

print(sqrt(36))      # works directly
```

**Warning:** This can cause name conflicts and makes it harder to know where a function came from. Avoid in real projects.

---

## Popular Built-in Modules (Standard Library)

Python comes with many useful modules — no installation needed.

### math — Mathematical operations

```python
import math

print(math.sqrt(16))           # 4.0
print(math.pow(2, 3))          # 8.0
print(math.factorial(5))       # 120
print(math.pi)                 # 3.14159...
print(math.radians(180))       # Convert degrees to radians
print(math.sin(math.radians(90)))  # 1.0
```

### random — Generate random numbers and choices

```python
import random

print(random.randint(1, 100))                    # Random integer
print(random.choice(["apple", "banana", "cherry"]))  # Random item from list

colors = ["red", "green", "blue"]
random.shuffle(colors)                           # Shuffle in place
print(colors)
```

### datetime — Work with dates and times

```python
from datetime import datetime, timedelta, date

now = datetime.now()
print("Current time:", now)

today = date.today()
print("Today:", today)

future = now + timedelta(days=30, hours=5)
print("30 days later:", future)
```

### os — Interact with operating system

```python
import os

print("Current directory:", os.getcwd())
print("List of files:", os.listdir("."))

# Create a folder (safe check)
if not os.path.exists("new_folder"):
    os.mkdir("new_folder")
```

### sys — System-specific parameters

```python
import sys

print("Python version:", sys.version)
print("Command line arguments:", sys.argv)
```

---

## Creating and Using Your Own Modules

**Step-by-step example:**

### 1. Create `utils.py`

```python
# utils.py

def greet(name: str) -> str:
    """Return a friendly greeting."""
    return f"Hello, {name}! Welcome to Python."

def add(a: int, b: int) -> int:
    """Add two numbers."""
    return a + b

def multiply(a: int, b: int) -> int:
    return a * b

# This is a constant
VERSION = "1.0.0"
```

### 2. Create `main.py` in the same folder

```python
# main.py
import utils

print(utils.greet("Alice"))
print("Sum:", utils.add(10, 25))
print("Product:", utils.multiply(4, 6))
print("Version:", utils.VERSION)
```

**How to run:**
```bash
python main.py
```

**Output:**
```
Hello, Alice! Welcome to Python.
Sum: 35
Product: 24
Version: 1.0.0
```

---

## The `__name__` Special Variable

This is one of the most important concepts for modules.

```python
# utils.py

def greet(name):
    return f"Hello, {name}!"

# This block runs ONLY when the file is executed directly
if __name__ == "__main__":
    print("=== Running utils.py directly ===")
    print(greet("World"))
    print("This code is for testing the module.")
else:
    print("utils module was imported by another file")
```

**Behavior:**
- Run directly (`python utils.py`) → `__name__` becomes `"__main__"`
- Imported (`import utils`) → `__name__` becomes `"utils"`

This pattern lets you write test code inside modules safely.

---

## Packages — Organizing Modules in Folders

A **package** is a directory containing an `__init__.py` file and multiple modules.

**Project structure:**
```
myproject/
├── __init__.py          # Makes this folder a package
├── utils.py
├── calculations.py
└── main.py
```

**calculations.py**
```python
def multiply(x, y):
    return x * y

def divide(x, y):
    if y == 0:
        raise ValueError("Cannot divide by zero!")
    return x / y
```

**main.py**
```python
from myproject.utils import greet
from myproject.calculations import multiply, divide

print(greet("Bob"))
print(multiply(7, 8))
```

---

## Best Practices and Common Pitfalls

- Prefer `import module` over `from module import *`
- Keep modules focused (one responsibility)
- Use meaningful names for modules and packages
- Avoid circular imports (module A imports B, B imports A)
- Put `if __name__ == "__main__":` in scripts meant for direct execution
- Use type hints and docstrings in your modules

**Common Error:**
```python
# ModuleNotFoundError
import my_missing_module
```

**Solution:** Check spelling, file location, or install missing packages.

---

## Summary Table

| Concept                    | Syntax Example                        | Purpose                              |
|---------------------------|---------------------------------------|--------------------------------------|
| Whole Module              | `import math`                         | Clear, safe                          |
| Specific Import           | `from math import sqrt`               | Direct access                        |
| Alias                     | `import pandas as pd`                 | Convenience                          |
| Package Import            | `from myproject.utils import greet`   | Organized code                       |
| Direct Execution Check    | `if __name__ == "__main__":`          | Test + reusable code                 |

---

## Key Takeaways

- Modules are `.py` files that help organize and reuse code
- Use `import` statements to bring modules into your program
- Always prefer clear and explicit imports
- Use `__name__ == "__main__"` to make modules testable
- Packages allow you to create professional folder structures

Mastering modules is the foundation for building larger Python applications.

---

## See also

- [[16 - Defining Functions]] — functions in modules are just functions in files
- [[18 - Scope and Namespaces]] — each module has its own global namespace
- [[24 - Virtual Environments and pip]] — installing third-party modules
- [[01 - Getting Started]] — standard library is built-in modules
- [Python docs — Standard Library](https://docs.python.org/3/library/)

---

## → What's Next

Now that you know how to organize code with modules, it's time to learn how to manage Python packages and dependencies using **virtual environments** and `pip`.

Continue with **[[24 - Virtual Environments and pip]]**
