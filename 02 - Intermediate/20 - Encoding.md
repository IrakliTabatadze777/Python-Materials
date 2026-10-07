---
tags:
  - intermediate
  - python
  - strings
  - encoding
stage: 2
difficulty: Intermediate
---

# Encoding

**Prev:** [[19 - Regex]] | **Next:** [[21 - Pathlib]]

> In this lesson you will learn how Python represents text internally, and how it converts between human-readable strings and the raw bytes computers actually store and transmit. You will understand ASCII, Unicode, UTF-8, and the encoding bugs that silently break real programs.

---

## Why This Matters

Every piece of text your program handles — a name, a file, a network request — eventually has to become **bytes** to be stored on disk or sent over a network. How that conversion happens, and what can go wrong along the way, is the subject of this lesson.

### Real-Life Analogy

Think of **Morse code**:

- A message like "SOS" is meaningful to a person
- To send it over a telegraph wire, it must be converted into dots and dashes — a fixed encoding
- The receiver must know the *same* code to convert the dots and dashes back into "SOS"

If the sender and receiver disagree on the code, the message comes out as garbage — even though nothing was "lost," it was just interpreted with the wrong rules. This is exactly what happens with text encoding bugs.

---

## What is ASCII?

**ASCII** (American Standard Code for Information Interchange) is one of the earliest character encoding standards, from 1963. It uses **7 bits** per character, giving it 128 possible characters — enough for English letters, digits, punctuation, and basic control characters.

```python
print(ord('A'))    # 65 - ord() gives the numeric code point
print(ord('a'))    # 97
print(chr(65))      # 'A' - chr() converts a number back to a character
```

**ASCII's limitation:** it has no way to represent accented letters (é, ñ), Cyrillic, Georgian, Chinese, Japanese, or emojis — anything outside the original 128 characters simply doesn't exist in ASCII.

---

## What is Unicode?

**Unicode** is the modern standard that assigns a unique number (a **code point**) to every character in every major writing system — over 140,000 characters and counting, including emojis.

```python
name = "ირაკლი"                     # Georgian
greeting = "こんにちは"              # Japanese
emoji = "Hello 👋 World 🌍"

print(ord('ა'))     # 4304 - Georgian letter's code point
print(ord('😊'))    # 128522 - emoji's code point
```

In Python 3, **every string is Unicode by default** — you don't need to do anything special to store non-English text; `str` handles it natively.

---

## Encoding and Decoding: Strings vs. Bytes

- **Encoding**: converting a Unicode `str` into raw `bytes`, using a specific scheme
- **Decoding**: converting `bytes` back into a Unicode `str`, using that same scheme

```python
text = "Hello ირაკლი 👋"

utf8_bytes = text.encode("utf-8")     # b'Hello \xe1\x83...'
print(type(utf8_bytes))                # <class 'bytes'>

decoded = utf8_bytes.decode("utf-8")
print(decoded)                          # Hello ირაკლი 👋 - back to normal
print(decoded == text)                  # True
```

If you decode with the **wrong** encoding, you don't get an error — you often get garbled text (known as **mojibake**), because every encoding scheme happily produces *some* interpretation, just not the right one:

```python
text = "café"
correct = text.encode("utf-8").decode("utf-8")
print(correct)   # café

wrong = text.encode("utf-8").decode("latin-1")
print(wrong)      # cafÃ© - mojibake, decoded with the wrong rules
```

---

## Common Encodings

| Encoding    | Typical use                     | Notes                                      |
| ------------- | ---------------------------------- | --------------------------------------------- |
| **UTF-8**      | Web, files, databases (the default) | Backward-compatible with ASCII, variable-width, space-efficient |
| **UTF-16**     | Windows internals, some APIs        | Better for some Asian scripts, larger for plain English text |
| **ASCII**      | Legacy systems                      | Extremely limited — English only |
| **Latin-1**     | Older Western European systems      | Covers more accented letters than ASCII, still very limited |

**Default to UTF-8** unless you have a specific, documented reason to use something else — it's the standard across the modern web and most tooling.

---

## Handling Encoding Errors

Encoding text that contains characters outside the target scheme raises a `UnicodeEncodeError` by default — but you can control what happens instead.

```python
text = "Hello ირაკლი"

# text.encode("ascii")  # Raises UnicodeEncodeError - Georgian isn't in ASCII

print(text.encode("ascii", errors="ignore"))            # b'Hello ' - drops unsupported chars
print(text.encode("ascii", errors="replace"))            # b'Hello ??????' - replaces with '?'
print(text.encode("ascii", errors="xmlcharrefreplace"))  # b'Hello &#4304;...' - XML entities
```

Choose the error strategy deliberately — silently dropping characters (`errors="ignore"`) can quietly corrupt data if you're not careful.

---

## Always Specify Encoding When Working with Files

This is one of the most common real-world sources of encoding bugs:

```python
# Risky - relies on the operating system's default encoding, which varies!
with open("notes.txt", "r") as f:
    content = f.read()

# Correct - explicit and consistent across every machine
with open("notes.txt", "r", encoding="utf-8") as f:
    content = f.read()
```

Without an explicit `encoding="utf-8"`, the same code can behave differently on different machines — this connects directly to [[15 - Context Managers]], since `open()` is itself a context manager, and getting its encoding right is part of using it correctly.

---

## Unicode Normalization

The same visible character can sometimes be represented by **different underlying code points** — which breaks naive string comparison.

```python
import unicodedata

s1 = "café"                # 'é' as a single precomposed character
s2 = "café"           # 'e' followed by a combining accent mark

print(s1 == s2)   # False - even though they look identical!

norm1 = unicodedata.normalize("NFC", s1)
norm2 = unicodedata.normalize("NFC", s2)
print(norm1 == norm2)   # True - now they compare equal
```

Normalize text with `unicodedata.normalize()` before comparing or deduplicating user input from different sources (different OSes, keyboards, or apps can produce different representations of the "same" text).

---

## `len()` Counts Code Points, Not Visual Characters

```python
family_emoji = "👨‍👩‍👧‍👦"

print(len(family_emoji))              # 7 - it's actually 7 Unicode code points joined together!
print(len(family_emoji.encode("utf-8")))  # 25 - even more bytes in UTF-8
```

What looks like "one emoji" on screen can be several code points combined with invisible joiner characters — don't assume `len(string)` matches what a user visually perceives as the character count.

---

## Real-World Example: Safely Reading Unknown Text

```python
def read_text_safely(file_path):
    try:
        with open(file_path, "r", encoding="utf-8") as f:
            return f.read()
    except UnicodeDecodeError:
        print(f"Warning: {file_path} is not valid UTF-8, retrying with errors='replace'")
        with open(file_path, "r", encoding="utf-8", errors="replace") as f:
            return f.read()


content = read_text_safely("mystery_file.txt")
```

This pattern acknowledges that not all files are guaranteed to be UTF-8, and handles the mismatch explicitly instead of letting the program crash unexpectedly.

---

## Benefits of Understanding Encoding

1. **Fewer Silent Bugs** — Encoding mismatches often don't crash loudly; understanding them helps you catch corruption early
2. **International Support** — Correctly handling Unicode means your code works for users in any language
3. **Reliable File and Network I/O** — Explicit encodings prevent "works on my machine" failures
4. **Accurate Comparisons** — Normalization avoids subtle bugs when comparing user-entered text
5. **Better Debugging** — Recognizing mojibake immediately tells you the encoding, not the data, is wrong

---

## Common Mistakes

1. **Not specifying an encoding** when opening files, relying on the OS default instead
2. **Assuming `len(string)` equals the visual character count** — emojis and combined characters break this assumption
3. **Mixing encodings** — encoding with one scheme and decoding with another
4. **Comparing Unicode strings without normalizing them first**, when they came from different sources
5. **Silently ignoring encoding errors** (`errors="ignore"`) without considering what data that quietly discards

---

## Best Practices

1. **Always specify `encoding="utf-8"`** explicitly when reading or writing files
2. **Default to UTF-8** everywhere unless you have a documented reason not to
3. **Handle encoding errors explicitly** — choose `strict`, `ignore`, or `replace` deliberately, not by accident
4. **Normalize Unicode strings** before comparing text gathered from different input sources
5. **Test with non-English text and emojis** during development, not just as an afterthought

---

## See also

- [[19 - Regex]] — pattern matching on the text this lesson explains how to represent as bytes
- [[03 - Strings]] — the string fundamentals encoding builds on
- [[15 - Context Managers]] — `open()` as a context manager, and why its `encoding` argument matters
- [[21 - Pathlib]] — working with file paths, closely tied to reading and writing encoded text
- [[22 - Custom Serialization]] — encoding data structures, not just text, into bytes and back

---

## → What's next

You now understand **Encoding**:

- How ASCII and Unicode differ, and why Unicode is the modern standard
- How `.encode()` and `.decode()` convert between `str` and `bytes`
- Why specifying `encoding="utf-8"` explicitly avoids a whole class of subtle bugs
- How normalization keeps visually identical strings comparing as equal

In the next lesson, we will explore **Pathlib** — Python's modern, object-oriented way to work with file paths, replacing much of the older `os.path` module.

Continue with **[[21 - Pathlib]]**
