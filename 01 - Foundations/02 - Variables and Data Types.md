---
tags:
  - foundations
  - python
  - variables
  - data-types
stage: 1
difficulty: Beginner
---

# Variables and Data Types in Python

**Prev:** [[01 - Getting Started]] | **Next:** [[03 - Operators and Expressions]]

> In Python, everything is an object. Variables are simply names that reference these objects, and data types define what kind of object is stored and how Python handles it.

---

## What is a Variable?

A **variable** in Python is a **reference (name binding)** to an object stored in memory.

### Simple idea:

A variable is not the value itself — it is a label attached to a value.

```python
x = 10
```

What happens internally:

* Python creates an integer object `10`
* The name `x` is bound to that object

You can imagine it like this:

```
x ───► 10 (object in memory)
```

---

## Variables Are References (Important Concept)

In Python, variables do not store values directly — they store **references**.

```python
a = 10
b = a
```

Now both variables point to the same object:

```
a ───► 10 ◄─── b
```

This is important for understanding how Python works with memory and performance.

---

## Python is Dynamically Typed

Python does not require you to declare variable types.

The type belongs to the **object**, not the variable.

```python
x = 10        # int
x = "hello"   # str
x = True      # bool
```

So:

* The variable `x` stays the same
* The object it points to changes

---

## Variable Naming Rules

Python variables are called **identifiers**.

### Rules:

* Must start with a letter or `_`
* Can contain letters, numbers, `_`
* Cannot start with a number
* Cannot use reserved keywords

### Valid:

```python
user_name = "Nutsa"
_age = 20
x1 = 100
```

### Invalid:

```python
2name = "Nutsa"   # ❌ starts with number
for = 10        # ❌ keyword
```

---

## Reserved Keywords in Python

**Keywords** are special reserved words used by Python syntax. They cannot be used as variable names.

Examples:

* `if`
* `for`
* `while`
* `class`
* `def`
* `return`

---

## How to Check Reserved Keywords

Python provides a built-in module called `keyword`.

---

### 1. View all keywords

```python
import keyword

print(keyword.kwlist)
```

This prints all reserved words in Python.

---

### 2. Check if a word is a keyword

```python
import keyword

print(keyword.iskeyword("for"))     # True
print(keyword.iskeyword("hello"))    # False
```

---

### 3. Practical usage (validation)

```python
import keyword

word = "class"

if keyword.iskeyword(word):
    print("Invalid variable name")
else:
    print("Valid variable name")
```

---

## Why Keywords Cannot Be Used as Variables

Keywords already have a fixed meaning in Python syntax.

Example:

```python
if = 10   # ❌ Syntax Error
```

Python uses `if` for conditions, so it cannot be reassigned.

---

# Python Data Types

Data types define:

* What kind of data is stored
* How memory is interpreted
* What operations are allowed

---

## Integer (`int`)

Whole numbers:

```python
x = 10
```

### Key idea:

Python integers have **unlimited precision**.

```python
x = 999999999999999999999
```

---

## Float (`float`)

Decimal numbers:

```python
x = 10.5
```

### Important detail (precision issue):

```python
print(0.1 + 0.2)
# 0.30000000000000004
```

This happens due to binary floating-point representation.

---

## String (`str`)

Text data:

```python
name = "Ana"
```

### Strings are immutable

```python
s = "hello"
# s[0] = "H"  ❌ not allowed
```

Instead, a new string is created.

---

## Boolean (`bool`)

Logical values:

```python
is_active = True
is_admin = False
```

### Internally:

* `True` → 1
* `False` → 0

```python
print(True + True)  # 2
```

---

# Checking Data Types

Use `type()`:

```python
x = 10
print(type(x))
```

Output:

```
<class 'int'>
```

---

# Type Casting (Conversion)

Convert between types:

---

## String → Integer

```python
int("10")
```

---

## Integer → String

```python
str(100)
```

---

## Float → Integer (cuts decimal part)

```python
int(5.9)  # 5
```

---

## Invalid conversion example

```python
int("hello")  # ValueError
```

---

# Memory Insight (Advanced Concept)

You can check object identity:

```python
a = 10
b = 10

print(id(a))
print(id(b))
```

Python may reuse objects for efficiency (especially small integers).

---

# Summary

* Variables are references to objects
* Python is dynamically typed
* Data type defines object behavior
* Keywords cannot be used as variable names
* Use `keyword` module to inspect reserved words

```python
import keyword

print(keyword.kwlist)
print(keyword.iskeyword("for"))
```

---

Continue with **[[03 - Operators and Expressions]]**

---
