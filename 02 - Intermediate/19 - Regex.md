---
tags:
  - intermediate
  - python
  - strings
  - regex
stage: 2
difficulty: Intermediate
---

# Regex

**Prev:** [[18 - Typing and Type Hints]] | **Next:** [[20 - Encoding]]

> In this lesson you will learn about **Regular Expressions (Regex)** — a mini-language for describing text patterns, built into Python's `re` module. You will learn how to search, validate, extract, and replace text based on patterns instead of exact matches.

---

## What is Regex?

A **regular expression** is a sequence of characters that describes a **pattern** to match against text, rather than an exact string.

Instead of writing code to check "does this string contain a digit, followed by another digit, followed by a dash," you write a pattern once — `\d\d-` — and let the `re` module do the matching.

### Real-Life Analogy

Think of a **search template on a form**:

- A normal search looks for one exact phrase, like finding a specific word in a document
- A regex is like a fill-in-the-blank template: "three digits, then a dash, then four digits" matches `555-1234`, `800-9999`, and any other value with that same shape

Regex describes the *shape* of text, not one specific piece of text.

---

## Getting Started

```python
import re

text = "The price is $45.99"
pattern = r"\d+\.\d{2}"

match = re.search(pattern, text)
if match:
    print(match.group())   # 45.99
```

**Note the `r` before the pattern string** — this makes it a **raw string**, so backslashes (`\d`, `\w`, `\s`) are passed to `re` literally instead of being interpreted as Python escape sequences. Always use raw strings for regex patterns.

---

## Basic Metacharacters

| Symbol   | Meaning                            | Example              |
| -------- | ------------------------------------ | ----------------------- |
| `.`      | Any character except newline         | `a.b` matches `"a1b"`    |
| `^`      | Start of string                       | `^Hello`                 |
| `$`      | End of string                         | `world$`                 |
| `*`      | Zero or more occurrences              | `a*`                      |
| `+`      | One or more occurrences               | `a+`                      |
| `?`      | Zero or one occurrence                | `a?`                      |
| `{n}`    | Exactly n occurrences                 | `\d{3}`                   |
| `{n,m}`  | Between n and m occurrences           | `\d{2,4}`                 |
| `[]`     | Character set                         | `[a-z]`                   |
| `\|`     | OR operator                           | `cat\|dog`                |
| `()`     | Capturing group                       | `(\d+)`                   |
| `\d`     | Digit (0-9)                           | `\d+`                      |
| `\w`     | Word character (letter, digit, `_`)   | `\w+`                      |
| `\s`     | Whitespace                            | `\s+`                      |
| `\D \W \S` | Negation of the above               | `\D` = non-digit           |

---

## The Core Functions of `re`

### `re.search()` — Find the first match anywhere

```python
result = re.search(r"\d+", "I have 42 apples")
print(result.group())   # 42
```

### `re.match()` — Match only at the very beginning

```python
print(re.match(r"\d+", "42 apples"))    # <re.Match object; span=(0, 2), match='42'>
print(re.match(r"\d+", "apples 42"))    # None - doesn't start with a digit
```

### `re.findall()` — Find every match

```python
text = "I have 5 apples, 10 bananas, and 3 oranges"
numbers = re.findall(r"\d+", text)
print(numbers)   # ['5', '10', '3']
```

### `re.sub()` — Replace matches with new text

```python
text = "My email is test@example.com"
new_text = re.sub(r"\S+@\S+", "[EMAIL]", text)
print(new_text)   # My email is [EMAIL]
```

### `re.split()` — Split a string on a pattern

```python
text = "apple,banana;cherry orange"
items = re.split(r"[;, ]+", text)
print(items)   # ['apple', 'banana', 'cherry', 'orange']
```

---

## Groups and Capturing

Parentheses `()` create a **group** — a piece of the match you can extract separately.

```python
text = "Date: 2026-05-31"

match = re.search(r"(\d{4})-(\d{2})-(\d{2})", text)

print(match.group(0))   # 2026-05-31  - the full match
print(match.group(1))   # 2026
print(match.group(2))   # 05
print(match.group(3))   # 31
```

**Named groups** make extracted data much more readable:

```python
match = re.search(r"(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})", text)
print(match.group("year"))    # 2026
print(match.group("month"))   # 05
```

---

## Flags: Changing How Matching Works

```python
text = "Python is FUN"

# Case-insensitive matching
print(re.findall(r"python", text, re.IGNORECASE))   # ['Python']

# Multiline: ^ and $ match the start/end of each line, not just the whole string
print(re.findall(r"^.", "line1\nline2", re.MULTILINE))   # ['l', 'l']

# Verbose: allows whitespace and comments inside the pattern for readability
pattern = re.compile(r"""
    \d{3}     # Area code
    -         # Separator
    \d{4}     # Number
""", re.VERBOSE)
```

---

## Compiling Patterns for Reuse

If you use the same pattern many times, compile it once with `re.compile()` instead of passing the raw string every time.

```python
email_pattern = re.compile(r"[\w.-]+@[\w.-]+\.\w+")

print(email_pattern.search("contact@example.com"))
print(email_pattern.findall("a@b.com and c@d.com"))
```

This is both faster (the pattern is only parsed once) and cleaner when the same pattern appears in multiple places.

---

## Greedy vs. Non-Greedy Matching

By default, `*` and `+` are **greedy** — they match as much text as possible.

```python
text = "<b>bold</b> and <i>italic</i>"

greedy = re.findall(r"<.+>", text)
print(greedy)   # ['<b>bold</b> and <i>italic</i>'] - matches too much!

non_greedy = re.findall(r"<.+?>", text)
print(non_greedy)   # ['<b>', '</b>', '<i>', '</i>'] - stops at the first '>'
```

Adding `?` after `*` or `+` makes them **non-greedy**, matching as little as possible instead. This distinction matters a lot when working with delimited or nested-looking text.

---

## Real-World Example: Validating and Extracting Data

```python
import re

def is_valid_email(email):
    pattern = r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
    return re.match(pattern, email) is not None

print(is_valid_email("alice@example.com"))   # True
print(is_valid_email("not-an-email"))         # False


def extract_hashtags(text):
    return re.findall(r"#(\w+)", text)

print(extract_hashtags("Loving #python and #regex today!"))
# ['python', 'regex']


def mask_phone_number(text):
    return re.sub(r"\d{3}-\d{3}-\d{4}", "[REDACTED]", text)

print(mask_phone_number("Call me at 555-123-4567"))
# Call me at [REDACTED]
```

---

## Common Mistakes

1. **Forgetting raw strings** (`r"..."`) — without `r`, `\d` may be misinterpreted as a Python escape sequence
2. **Not escaping special characters** — `.` matches *any* character unless escaped as `\.`, which can silently match more than intended
3. **Assuming `*`/`+` are non-greedy by default** — they're greedy; add `?` when you need the shorter match
4. **Reaching for regex when a simple string method would do** — `"@" in email` is clearer than a regex for a trivial check
5. **Writing one giant, unreadable pattern** instead of breaking complex logic into smaller, named, or commented pieces (`re.VERBOSE`)

---

## Best Practices

1. **Always use raw strings** (`r"pattern"`) for regex patterns
2. **Test patterns on a tool like [Regex101.com](https://regex101.com)** before dropping them into code — it explains each part interactively
3. **Compile patterns you reuse** with `re.compile()` for clarity and performance
4. **Use named groups** (`(?P<name>...)`) instead of positional `group(1)`, `group(2)` for readability
5. **Prefer built-in string methods** (`str.split()`, `in`, `str.replace()`) for simple cases — save regex for real pattern matching
6. **Keep patterns readable** — use `re.VERBOSE` and comments for anything non-trivial

---

## See also

- [[03 - Strings]] — the string fundamentals regex operates on
- [[15 - Context Managers]] — often used together when regex processes data read from files
- [[18 - Typing and Type Hints]] — annotating functions that validate or extract patterns
- [[20 - Encoding]] — how the text regex matches is represented as bytes under the hood

---

## → What's next

You now understand **Regex**:

- What a regular expression is and why it matches patterns, not exact text
- The core `re` functions: `search`, `match`, `findall`, `sub`, and `split`
- How to capture and extract parts of a match with groups, including named groups
- The difference between greedy and non-greedy matching

In the next lesson, we will explore **Encoding** — how Python strings relate to bytes, ASCII, Unicode, and UTF-8, and why encoding issues can silently break text processing (including regex) if left unhandled.

Continue with **[[20 - Encoding]]**
