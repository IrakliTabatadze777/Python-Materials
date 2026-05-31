---
tags:
  - foundations
  - python
  - strings
  - regex
stage: 1
difficulty: Intermediate
---

# Regular Expressions (Regex) in Python

**Prev:** [[03 - Strings]] | **Next:** [[05 - Encoding]]

> Regular Expressions are a powerful tool for pattern matching and text manipulation. They allow you to search, validate, extract, and replace text based on complex patterns.

---

## What is Regex?

**Regular Expression (Regex)** is a sequence of characters that defines a search pattern. It is used for:

- Validating input (email, phone, password)
- Extracting information from text
- Finding and replacing patterns
- Data cleaning and parsing
- Log analysis

Python provides regex support through the built-in `re` module.

**Important Tip:** Use [Regex101.com](https://regex101.com) — an excellent interactive tool to test and debug regex patterns in real-time with explanations.

---

## Getting Started with Regex

```python
import re

text = "The price is $45.99"
pattern = r"\d+\.\d{2}"

match = re.search(pattern, text)
if match:
    print(match.group())    # Output: 45.99
```

---

## Basic Metacharacters

| Symbol       | Meaning                           | Example             |      |      |
| ------------ | --------------------------------- | ------------------- | ---- | ---- |
| `.`          | Any character except newline      | `a.b` matches "a1b" |      |      |
| `^`          | Start of string                   | `^Hello`            |      |      |
| `$`          | End of string                     | `world$`            |      |      |
| `*`          | Zero or more occurrences          | `a*`                |      |      |
| `+`          | One or more occurrences           | `a+`                |      |      |
| `?`          | Zero or one occurrence            | `a?`                |      |      |
| `{n}`        | Exactly n occurrences             | `\d{3}`             |      |      |
| `{n,m}`      | Between n and m occurrences       | `\d{2,4}`           |      |      |
| `[]`         | Character set                     | `[a-z]`             |      |      |
| `            | `                                 | OR operator         | `cat | dog` |
| `()`         | Capturing group                   | `(\d+)`             |      |      |
| `\d`         | Digit (0-9)                       | `\d+`               |      |      |
| `\w`         | Word character (letter, digit, _) | `\w+`               |      |      |
| `\s`         | Whitespace                        | `\s+`               |      |      |
| `\D, \W, \S` | Negation of above                 | `\D` = non-digit    |      |      |

---

## Common Regex Functions

### `re.search()` — Find first match anywhere

```python
result = re.search(r"\d+", "I have 42 apples")
print(result.group())        # 42
```

### `re.match()` — Match only at the beginning

```python
print(re.match(r"\d+", "42 apples"))     # Matches
print(re.match(r"\d+", "apples 42"))     # None
```

### `re.findall()` — Find all matches

```python
text = "I have 5 apples, 10 bananas, and 3 oranges"
numbers = re.findall(r"\d+", text)
print(numbers)               # ['5', '10', '3']
```

### `re.sub()` — Replace patterns

```python
text = "My email is test@example.com"
new_text = re.sub(r"\S+@\S+", "[EMAIL]", text)
print(new_text)              # My email is [EMAIL]
```

### `re.split()` — Split string by pattern

```python
text = "apple,banana;cherry orange"
items = re.split(r"[;, ]+", text)
print(items)
```

---

## Character Classes and Sets

```python
# Custom sets
re.findall(r"[aeiou]", "Python")           # vowels
re.findall(r"[^aeiou]", "Python")          # non-vowels
re.findall(r"[0-5]", "Score: 87")          # digits 0-5
```

### Predefined Character Classes

- `\d` → `[0-9]`
- `\D` → `[^0-9]`
- `\w` → `[a-zA-Z0-9_]`
- `\W` → `[^a-zA-Z0-9_]`
- `\s` → `[ \t\n\r\f\v]`
- `\S` → `[^ \t\n\r\f\v]`

---

## Groups and Capturing

```python
text = "Date: 2026-05-31"

match = re.search(r"(\d{4})-(\d{2})-(\d{2})", text)

print(match.group(0))   # Full match: 2026-05-31
print(match.group(1))   # 2026
print(match.group(2))   # 05
print(match.group(3))   # 31
```

**Named Groups:**

```python
match = re.search(r"(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})", text)
print(match.group("year"))    # 2026
```

---

## Flags (Modifiers)

```python
text = "Python is FUN"

# Case insensitive
print(re.findall(r"python", text, re.IGNORECASE))

# Multiline
print(re.findall(r"^.", text, re.MULTILINE))

# Verbose (allows comments in pattern)
pattern = re.compile(r"""
    \d{3}           # Area code
    -               # Separator
    \d{4}           # Number
""", re.VERBOSE)
```

Common flags: `re.IGNORECASE`, `re.MULTILINE`, `re.DOTALL`, `re.VERBOSE`

---

## Compiling Patterns

For better performance when using the same pattern multiple times:

```python
email_pattern = re.compile(r"[\w\.-]+@[\w\.-]+\.\w+")

result = email_pattern.search("contact@irakli.dev")
```

---

## Real-World Examples

### 1. Email Validation

```python
def is_valid_email(email):
    pattern = r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
    return re.match(pattern, email) is not None
```

### 2. Phone Number Extraction

```python
text = "Call me at +995 555 123 456 or 032 2 123 456"
phones = re.findall(r"\+?\d{1,3}[-.\s]?\d{1,4}[-.\s]?\d{1,4}", text)
```

### 3. URL Detection

```python
urls = re.findall(r"https?://(?:[-\w.]|(?:%[\da-fA-F]{2}))+", text)
```

---

## Common Mistakes & Gotchas

1. **Forgetting raw strings** (`r"..."`) — backslashes are interpreted as escape characters.
2. **Greedy vs Non-greedy matching** — `*` is greedy, `*?` is non-greedy.
3. **Overly complex regex** — sometimes `str` methods are better.
4. **Not escaping special characters** — `.` matches any character unless escaped `\.`
5. **Assuming regex is always the best solution** — for simple cases, use string methods.

---

## Best Practices

- Use raw strings (`r"pattern"`)
- Test patterns on [Regex101.com](https://regex101.com)
- Start simple and build complexity gradually
- Use named groups for readability
- Comment complex patterns using `re.VERBOSE`
- Prefer built-in string methods when possible
- Keep regex readable — don't try to do everything in one pattern


---

Continue with **[[05 - Encoding]]**

---