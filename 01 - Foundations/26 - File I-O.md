---
tags:
  - foundations
  - python
  - file-handling
stage: 1
difficulty: Beginner
---

# File I/O in Python

**Prev:** [[25 - Error Handling]] | **Next:** [[27 - CSV]]

> In this lesson you will learn how to read from and write to files on your computer using Python. File I/O (Input/Output) is a fundamental skill for working with data, configuration files, logs, user data, and more.

---

## Sample File for Examples

We will use a file named **`example.txt`** with the following content for all reading demonstrations:

```txt
Hello, Python World!
This is line 2.
Python is awesome.
Learning File I/O is important.
End of file.
```

---

## Why File Handling Matters

Programs often need to:
- Save user data persistently (so it survives when the program closes)
- Read configuration or settings
- Process large datasets
- Generate logs and reports

Python makes file operations simple and powerful.

---

## Opening Files with `open()`

The built-in `open()` function is the core of file handling.

### Basic Syntax

```python
file_object = open(filename, mode, encoding=None)
```

### Important File Modes

| Mode   | Description                                      | Use Case                          | Creates File? |
|--------|--------------------------------------------------|-----------------------------------|---------------|
| `'r'`  | Read (default)                                   | Reading existing files            | No            |
| `'w'`  | Write (overwrites if file exists)                | Creating or replacing content     | Yes           |
| `'a'`  | Append (adds to the end)                         | Adding data without overwriting   | Yes           |
| `'r+'` | Read and Write                                   | Modifying existing content        | No            |
| `'rb'` | Read Binary                                      | Images, videos, raw data          | No            |
| `'wb'` | Write Binary                                     | Saving binary data                | Yes           |

**Always specify `encoding="utf-8"`** when working with text files to avoid character issues.

---

## Reading Files

### 1. Using `open()` + `read()`

```python
# Open file in read mode
file = open("example.txt", "r", encoding="utf-8")

# Read entire content as one string
content = file.read()

print(content)

file.close()   # Always close the file!
```

**Output:**
```
Hello, Python World!
This is line 2.
Python is awesome.
Learning File I/O is important.
End of file.
```

---

### 2. Reading Line by Line

```python
file = open("example.txt", "r", encoding="utf-8")

# Read one line at a time
print("First line:", file.readline().strip())
print("Second line:", file.readline().strip())

file.close()
```

**Output:**
```
First line: Hello, Python World!
Second line: This is line 2.
```

---

### 3. Reading All Lines into a List

```python
file = open("example.txt", "r", encoding="utf-8")

lines = file.readlines()          # Returns list of strings (with \n)

for line in lines:
    print(line.strip())           # strip() removes newline

file.close()
```

**Output:**
```
Hello, Python World!
This is line 2.
Python is awesome.
Learning File I/O is important.
End of file.
```

---

### 4. Most Pythonic Way — Iterating Directly

```python
file = open("example.txt", "r", encoding="utf-8")

for line in file:                 # Memory efficient for large files
    print(line.strip())

file.close()
```

**Output:** (same as sample file content above)

---

## Writing to Files

### Writing New Content (Mode `'w'` — overwrites)

```python
file = open("output.txt", "w", encoding="utf-8")

file.write("This is the first line I wrote from Python.\n")
file.write("Second line.\n")
file.writelines(["Third line\n", "Fourth line\n"])

file.close()

print("Writing completed!")
```

**After running, `output.txt` will contain:**
```
This is the first line I wrote from Python.
Second line.
Third line
Fourth line
```

---

### Appending Content (Mode `'a'`)

```python
file = open("log.txt", "a", encoding="utf-8")
file.write("User logged in at 2026-06-15\n")
file.write("Another entry.\n")
file.close()
```

This adds new lines without deleting existing content.

---

## File Position Control: `seek()` and `tell()`

```python
file = open("example.txt", "r", encoding="utf-8")

print("Current position:", file.tell())   # 0

first_10 = file.read(10)
print("First 10 chars:", first_10)
print("New position:", file.tell())

file.seek(0)                              # Go back to start
print("Back to beginning:", file.read(20))

file.close()
```

**Output:**
```
Current position: 0
First 10 chars: Hello, Pyt
New position: 10
Back to beginning: Hello, Python World!
```

---

## Working with Binary Files

```python
# Example: Copying a binary file (image, pdf, etc.)
with open("photo.jpg", "rb") as source:
    data = source.read()

with open("copy_photo.jpg", "wb") as destination:
    destination.write(data)

print("Binary file copied successfully!")
```

---

## The Best Practice: Context Manager (`with` statement)

The `with` statement automatically closes the file — even if an error occurs.

```python
# Reading with context manager
with open("example.txt", "r", encoding="utf-8") as file:
    content = file.read()
    print("=== File Content ===")
    print(content)

# File is automatically closed here
print("File is now closed.")
```

**Output:**
```
=== File Content ===
Hello, Python World!
This is line 2.
Python is awesome.
Learning File I/O is important.
End of file.
File is now closed.
```

### Writing with Context Manager

```python
with open("new_file.txt", "w", encoding="utf-8") as file:
    file.write("This is safe writing.\n")
    file.write("No need to call close()!\n")

print("File written successfully using 'with'.")
```

---

## Complete Real-World Example with Error Handling

```python
def read_file_safely(filename):
    try:
        with open(filename, "r", encoding="utf-8") as file:
            return file.read()
    except FileNotFoundError:
        print(f"❌ File '{filename}' not found.")
        return None
    except PermissionError:
        print(f"❌ Permission denied for '{filename}'.")
        return None
    except Exception as e:
        print(f"❌ Unexpected error: {e}")
        return None


content = read_file_safely("example.txt")
if content:
    print("File read successfully!")
```

---

## Working with File Paths (Modern Way)

```python
from pathlib import Path

path = Path("example.txt")

print("Exists:", path.exists())
print("File name:", path.name)
print("Extension:", path.suffix)
print("Parent folder:", path.parent)

# Create directories safely
Path("data/logs").mkdir(parents=True, exist_ok=True)
```

---

## Best Practices & Common Pitfalls

- **Always use `with open(...)`** — never forget to close files
- **Specify `encoding="utf-8"`** for text files
- **Handle exceptions** (especially `FileNotFoundError`)
- Read large files **line by line** to avoid memory issues
- Use `'a'` mode for logs
- Use `pathlib.Path` for modern path manipulation
- Never assume a file exists

---

## Summary Table

| Operation                | Recommended Code                                   | Key Notes                          |
|--------------------------|----------------------------------------------------|------------------------------------|
| Read entire file         | `with open(..., "r") as f: f.read()`              | Small files                        |
| Read line by line        | `for line in file:`                                | Large files (best)                 |
| Write (overwrite)        | `with open(..., "w") as f: f.write()`             | Replaces content                   |
| Append                   | `with open(..., "a") as f: f.write()`             | Adds to end                        |
| Binary files             | `"rb"` / `"wb"`                                    | Images, executables                |
| Safe file handling       | `try` + `with` + specific `except`                | Production code                    |

---

## Key Takeaways

- `open()` with correct mode is the foundation
- Always prefer context managers (`with`)
- Handle errors and specify encoding
- Use `read()`, `readline()`, `readlines()`, or iteration depending on needs
- `pathlib` makes path handling cleaner
- Good file handling is essential for real-world applications

---

## → What's Next

Now that you know how to work with files in Python, the next step is learning how to handle **CSV files** — one of the most common formats for storing tabular data.

Continue with **[[27 - CSV]]**
