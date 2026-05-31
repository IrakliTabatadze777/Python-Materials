---
tags:
  - foundations
  - python
  - strings
stage: 1
difficulty: Beginner
---

# Strings in Python

**Prev:** [[02 - Variables and Data Types]] | **Next:** [[04 - Numbers and Math]]

> Strings are one of the most commonly used data types in Python. They allow you to work with text — from simple messages to complex data processing.

---

## What is a String?

A **string** is a sequence of characters enclosed in quotes. In Python, strings are **immutable** (they cannot be changed after creation) and are objects of the built-in `str` class.

```python
name = "Irakli"
message = 'Hello, World!'
multiline = """This is a
multi-line string"""
```

### Ways to Create Strings

| Method              | Example                              | Use Case                     |
|---------------------|--------------------------------------|------------------------------|
| Single quotes       | `'Hello'`                            | Simple strings               |
| Double quotes       | `"Hello"`                            | Most common                  |
| Triple quotes       | `"""Hello"""` or `'''Hello'''`       | Multi-line strings           |

You can use single or double quotes interchangeably. Triple quotes are useful for multi-line text and docstrings.

---

## Basic String Operations

### Concatenation

```python
first = "Hello"
last = "World"
greeting = first + " " + last
print(greeting)          # Output: Hello World
```

### Repetition

```python
print("-" * 50)          # Prints 50 dashes
print("Python " * 3)     # Python Python Python
```

### Membership Test

```python
text = "Python is amazing"
print("Python" in text)      # True
print("Java" not in text)    # True
```

---

## String Indexing and Slicing

Strings are sequences, so you can access individual characters by index.

```python
language = "Python"

print(language[0])      # P
print(language[1])      # y
print(language[-1])     # n (negative index from the end)
print(language[-2])     # o
```

### Slicing

```python
print(language[0:4])    # Pyth
print(language[2:])     # thon
print(language[:4])     # Pyth
print(language[::2])    # Pto (every second character)
print(language[::-1])   # nohtyP (reversed)
```

**Syntax:** `string[start:end:step]`

- `start` is inclusive
- `end` is exclusive

---

## String Formatting

### 1. f-strings (Recommended - Python 3.6+)

```python
name = "Irakli"
age = 25
score = 95.7

print(f"My name is {name}")
print(f"I am {age} years old")
print(f"Score: {score:.2f}")           # 95.70
print(f"{name.upper()} has {len(name)} letters")
```

### 2. .format() method

```python
print("My name is {}".format(name))
print("Hello, {0}! You are {1} years old.".format(name, age))
```

### 3. Old style (% formatting)

```python
print("Name: %s, Age: %d" % (name, age))
```

**Best Practice:** Always prefer f-strings for new code.

---

## Important String Methods

### Case Conversion
```python
text = "python programming"

print(text.upper())
print(text.lower())
print(text.title())      # Capitalize each word
print(text.capitalize()) # First letter only
```

### Whitespace Removal
```python
text = "   hello world   "
print(text.strip())      # "hello world"
print(text.lstrip())     # left strip
print(text.rstrip())     # right strip
```

### Splitting and Joining
```python
sentence = "Python is fun to learn"

words = sentence.split()           # split by whitespace
print(words)                       # ['Python', 'is', 'fun', 'to', 'learn']

csv = "apple,banana,cherry"
items = csv.split(",")             # split by comma

# Joining
joined = " | ".join(words)
print(joined)
```

### Search and Replace
```python
text = "I love Python programming"

print(text.find("Python"))         # returns index
print(text.replace("Python", "JavaScript"))
print("Python" in text)
```

### Checking String Properties
```python
print("123".isdigit())             # True
print("abc".isalpha())             # True
print("abc123".isalnum())          # True
print("   ".isspace())             # True
print("hello".startswith("he"))    # True
print("world".endswith("ld"))      # True
```

---

## Immutability of Strings

Strings cannot be changed in place:

```python
s = "hello"
# s[0] = "H"        # This will raise TypeError!

# Correct way - create a new string
s = "H" + s[1:]
print(s)            # Hello
```

This is why methods like `upper()` return **new** strings instead of modifying the original.

---

## Escape Characters

| Escape | Meaning                  |
|--------|--------------------------|
| `\n`   | New line                 |
| `\t`   | Tab                      |
| `\'`   | Single quote             |
| `\"`   | Double quote             |
| `\\`   | Backslash                |

```python
print("Hello\nWorld")     # New line
print("He said \"Hi\"")   # Escaping quotes
```

### Raw Strings

Useful for regular expressions and file paths:

```python
path = r"C:\Users\Irakli\Documents\file.txt"
print(path)
```

---

## Common String Patterns

### Checking Empty String
```python
text = ""

if not text:                    # Most Pythonic
    print("String is empty")

if text == "":
    print("String is empty")
```

### String Validation
```python
email = "user@example.com"

if "@" in email and "." in email:
    print("Looks like a valid email")
```

---

## Common Mistakes & Gotchas

1. **Trying to modify a string directly**
2. **Forgetting that slicing is exclusive at the end**
3. **Using `+` for many concatenations** (slow) — use f-strings or `.join()` instead
4. **Assuming case sensitivity** — `"Python" != "python"`
5. **Not handling whitespace properly**

---

## Best Practices

- Use f-strings for formatting
- Prefer `.join()` over repeated `+` for building strings
- Use meaningful variable names
- Strip user input with `.strip()`
- Be consistent with quote style (prefer double quotes)
- Use raw strings for paths and regex

---

## See also

- [[02 - Variables and Data Types]] — immutability, object references, and type checking
- [[04 - Numbers and Math]] — converting between strings and numbers with `int()` / `str()`
- [[07 - Comparisons and Logical Operators]] — membership with `in`, comparing and combining strings
- [[10 - For loop]] — iterating over characters in a string

---

## → What's next

You can create, slice, and format text. Programs also need numbers for counting, measuring, and calculating — integers, floats, and the math that goes with them — in [[04 - Numbers and Math]].

