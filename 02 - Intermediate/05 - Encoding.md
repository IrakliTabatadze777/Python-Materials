---
tags:
  - intermediate
  - python
  - strings
  - encoding
stage: 2
difficulty: Intermediate
---

# ASCII and Unicode in Python

**Prev:** [[04 - ReGex]] | **Next:** [[04 - Numbers and Math]]

> Understanding ASCII and Unicode is essential for working with text properly in Python. It explains how computers store and represent characters from different languages, symbols, and emojis.

---

## What is ASCII?

**ASCII** (American Standard Code for Information Interchange) is one of the earliest character encoding standards.

- Created in 1963
- Uses **7 bits** per character (128 possible characters)
- Includes English letters (A-Z, a-z), digits (0-9), punctuation, and control characters

### Example of ASCII

```python
print(ord('A'))    # 65
print(ord('a'))    # 97
print(ord('0'))    # 48
print(ord(' '))    # 32
```

```python
print(chr(65))     # A
print(chr(97))     # a
```

**Limitations of ASCII:**
- Only supports English characters
- Cannot represent accented letters (é, ñ), Cyrillic, Arabic, Georgian, Chinese, Japanese, or emojis
- Very limited for international applications

---

## What is Unicode?

**Unicode** is the modern standard that aims to represent **every character** from every writing system in the world.

- Over 140,000 characters defined
- Includes letters, symbols, emojis, mathematical symbols, ancient scripts, etc.
- Uses code points (e.g., U+0041 for 'A')

### Unicode in Python

In Python 3, **all strings are Unicode by default**. This is a major improvement over Python 2.

```python
# All of these are valid Unicode strings in Python 3
name = "ირაკლი"                    # Georgian
greeting = "こんにちは"             # Japanese
emoji = "Hello 👋 World 🌍"
mixed = "Python 3 supports é, ñ, ü, and 漢字"
```

---

## Key Concepts: Encoding and Decoding

### Encoding
Converting a string (Unicode) into bytes for storage or transmission.

```python
text = "Hello ირაკლი 👋"

# Common encodings
utf8_bytes = text.encode('utf-8')      # Most recommended
utf16_bytes = text.encode('utf-16')
ascii_bytes = text.encode('ascii', errors='replace')
```

### Decoding
Converting bytes back into a string.

```python
bytes_data = b'Hello \xe1\x83\x98\xe1\x83\xa0\xe1\x83\x90\xe1\x83\x99\xe1\x83\x9a\xe1\x83\x98'
decoded = bytes_data.decode('utf-8')
print(decoded)
```

---

## Important Functions

### `ord()` and `chr()`

```python
print(ord('A'))           # 65
print(ord('ა'))           # 4304 (Georgian letter)
print(ord('😊'))          # 128522

print(chr(65))            # A
print(chr(4304))          # ა
print(chr(128522))        # 😊
```

### Checking Unicode Properties

```python
import unicodedata

char = 'é'
print(unicodedata.name(char))           # LATIN SMALL LETTER E WITH ACUTE
print(unicodedata.category(char))       # Ll (Letter, lowercase)
```

---

## Common Encodings

| Encoding   | Usage                              | Pros                     | Cons                     |
|------------|------------------------------------|--------------------------|--------------------------|
| **UTF-8**  | Web, files, databases (default)    | Backward compatible with ASCII, efficient | Variable width |
| **UTF-16** | Windows internal, some APIs        | Good for Asian languages | Larger for English text |
| **ASCII**  | Legacy systems                     | Very small               | Extremely limited |
| **Latin-1**| Western European languages         | Simple                   | Limited characters |

**Best Practice:** Always use **UTF-8** unless you have a specific reason not to.

---

## Common Unicode Issues & Solutions

### 1. UnicodeEncodeError

```python
text = "Hello ირაკლი"
text.encode('ascii')                    # Raises UnicodeEncodeError

# Solutions:
text.encode('ascii', errors='ignore')
text.encode('ascii', errors='replace')  # Replaces with ?
text.encode('ascii', errors='xmlcharrefreplace')
```

### 2. Mojibake (Garbled Text)

Caused by decoding bytes with the wrong encoding.

### 3. Normalization

Different ways to represent the same character:

```python
import unicodedata

s1 = "café"                    # Precomposed
s2 = "cafe\u0301"              # Combined

print(s1 == s2)                # False

# Normalize
norm1 = unicodedata.normalize('NFC', s1)
norm2 = unicodedata.normalize('NFC', s2)
print(norm1 == norm2)          # True
```

---

## Working with Emojis and Special Characters

```python
print("Python supports emojis natively: 🚀 ❤️ 🔥")

# Length of emoji
print(len("👨‍👩‍👧‍👦"))        # 1 family emoji = 7 code points!
print(len("👨‍👩‍👧‍👦".encode('utf-8')))  # 25 bytes
```

---

## Best Practices

- Always specify encoding when reading/writing files (`encoding='utf-8'`)
- Use UTF-8 as your default encoding
- Handle encoding errors explicitly
- Normalize Unicode strings when comparing text
- Be aware that `len()` counts Unicode code points, not visual characters
- Test your code with non-English text and emojis

---

## Common Mistakes & Gotchas

1. **Forgetting to specify encoding** when working with files
2. **Assuming `len(string)` gives visual character count**
3. **Mixing different encodings**
4. **Hardcoding paths with special characters**
5. **Not handling Unicode in user input**

---

Continue with **[[04 - Numbers and Math]]**

---