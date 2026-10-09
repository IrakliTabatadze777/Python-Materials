---
tags:
  - intermediate
  - python
  - files
stage: 2
difficulty: Intermediate
---

# Pathlib

**Prev:** [[20 - Encoding]] | **Next:** [[22 - Custom Serialization]]

> In this lesson you will learn about the **`pathlib`** module — Python's modern, object-oriented way to work with file system paths. You'll learn why it's preferred over the older `os.path` functions, and how it makes reading, writing, and navigating files cleaner and more portable.

---

## The Old Way: `os.path`

Before `pathlib`, working with file paths meant string manipulation and a collection of separate functions:

```python
import os

folder = "data"
filename = "report.csv"

full_path = os.path.join(folder, filename)
print(full_path)   # data/report.csv (or data\report.csv on Windows)

print(os.path.exists(full_path))
print(os.path.splitext(filename))     # ('report', '.csv')
print(os.path.basename(full_path))     # report.csv
print(os.path.dirname(full_path))      # data
```

This works, but it's verbose, spread across many separate function calls, and paths are just plain strings — nothing stops you from concatenating them incorrectly.

### Real-Life Analogy

Think of the difference between **describing a location in words** versus **pointing at it on a map**:

- `os.path` is like giving directions purely as text: "go to the folder named data, then the file named report.csv" — you have to parse and manipulate the sentence itself to change anything
- `pathlib` is like an actual pin on a map: a `Path` object you can query, combine, and navigate directly, without ever touching raw text

---

## Creating a `Path`

```python
from pathlib import Path

p = Path("data/report.csv")
print(p)          # data/report.csv
print(type(p))     # <class 'pathlib.PosixPath'> (or WindowsPath on Windows)
```

`pathlib` automatically uses the right path style for the operating system it's running on — you don't need separate logic for Windows vs. macOS/Linux.

---

## Building Paths with `/`

Instead of `os.path.join()`, `pathlib` overloads the `/` operator to combine path segments — a great real-world example of the operator overloading from [[07 - OOP — Dunder Methods]].

```python
from pathlib import Path

folder = Path("data")
full_path = folder / "reports" / "2026" / "summary.csv"

print(full_path)   # data/reports/2026/summary.csv
```

This reads naturally, almost like describing the path out loud, and it's impossible to get platform-specific separators wrong.

---

## Inspecting a Path

```python
from pathlib import Path

p = Path("data/reports/summary.csv")

print(p.name)          # summary.csv - full filename
print(p.stem)           # summary - filename without extension
print(p.suffix)         # .csv - just the extension
print(p.parent)         # data/reports - the containing directory
print(p.parts)          # ('data', 'reports', 'summary.csv')
print(p.is_absolute())  # False
```

```python
absolute = p.resolve()
print(absolute)          # /Users/you/project/data/reports/summary.csv
print(absolute.is_absolute())  # True
```

`.resolve()` converts a relative path into a full, absolute one, resolving any `..` or `.` segments along the way.

---

## Checking Existence and Type

```python
from pathlib import Path

p = Path("data/report.csv")

print(p.exists())      # True or False
print(p.is_file())      # True if it exists and is a file
print(p.is_dir())        # True if it exists and is a directory
```

---

## Reading and Writing Files

`pathlib` provides convenient shortcuts that skip the explicit `open()`/`close()` dance from [[15 - Context Managers]] for simple cases.

```python
from pathlib import Path

p = Path("notes.txt")

p.write_text("Hello, pathlib!", encoding="utf-8")

content = p.read_text(encoding="utf-8")
print(content)   # Hello, pathlib!
```

For binary data, use `.write_bytes()` and `.read_bytes()` instead.

**For more control** (appending, streaming large files, or anything requiring an explicit context manager), `Path` objects still work with `open()` directly:

```python
with open(p, "a", encoding="utf-8") as f:
    f.write("\nAnother line")
```

This connects directly to [[20 - Encoding]] — specifying `encoding="utf-8"` explicitly matters just as much here as it does with plain `open()`.

---

## Creating and Removing Directories and Files

```python
from pathlib import Path

folder = Path("output/reports")
folder.mkdir(parents=True, exist_ok=True)   # Creates nested folders as needed

file_path = folder / "summary.txt"
file_path.write_text("Report data", encoding="utf-8")

file_path.unlink()          # Delete the file
folder.rmdir()               # Delete the (now-empty) folder
```

- `parents=True` creates any missing parent directories along the way
- `exist_ok=True` avoids raising an error if the directory already exists

---

## Listing Directory Contents

```python
from pathlib import Path

folder = Path("data")

for item in folder.iterdir():
    print(item, "- directory" if item.is_dir() else "- file")
```

### Pattern Matching with `glob()`

```python
from pathlib import Path

folder = Path("data")

csv_files = list(folder.glob("*.csv"))
print(csv_files)   # [PosixPath('data/report.csv'), PosixPath('data/summary.csv')]

# Recursive search through all subdirectories
all_python_files = list(folder.glob("**/*.py"))
```

`glob()` supports the same `*`/`**`/`?` wildcard patterns you may already know from the shell — `**` in particular means "search recursively through all subdirectories."

---

## Cross-Platform Paths Without Any Extra Work

```python
from pathlib import Path

config_path = Path.home() / ".config" / "myapp" / "settings.json"
print(config_path)
```

`Path.home()` returns the current user's home directory correctly on any operating system — no hardcoded `/Users/...` or `C:\Users\...` required. Similarly, `Path.cwd()` gives the current working directory.

---

## Real-World Example: Processing All Files in a Directory

```python
from pathlib import Path
import json

def load_all_configs(config_dir):
    """Load every .json file in a directory into a single dict, keyed by filename"""
    configs = {}
    for path in Path(config_dir).glob("*.json"):
        configs[path.stem] = json.loads(path.read_text(encoding="utf-8"))
    return configs


all_configs = load_all_configs("configs")
print(all_configs)
# {'database': {...}, 'logging': {...}, 'app': {...}}
```

This combines [[19 - Regex]]-adjacent pattern matching (via `glob`), [[20 - Encoding]]'s explicit UTF-8 handling, and JSON parsing into a short, readable function — no manual string path-joining anywhere.

---

## Real-World Example: Safe File Renaming

```python
from pathlib import Path

def add_prefix_to_files(directory, prefix):
    folder = Path(directory)
    for file in folder.glob("*.txt"):
        new_name = file.with_name(f"{prefix}_{file.name}")
        file.rename(new_name)
        print(f"Renamed {file.name} -> {new_name.name}")


add_prefix_to_files("documents", "archived")
```

`.with_name()` returns a *new* `Path` with just the filename changed, leaving the rest of the path intact — another example of `pathlib` methods returning new `Path` objects rather than mutating strings in place.

---

## Benefits of `pathlib`

1. **Cross-Platform by Default** — No manual handling of `/` vs `\` separators
2. **Object-Oriented and Chainable** — `folder / "sub" / "file.txt"` reads naturally, and methods return new `Path` objects
3. **Fewer Imports** — File reading, writing, existence checks, and globbing all live on the `Path` object itself
4. **Readable** — `path.stem`, `path.suffix`, `path.parent` are far clearer than `os.path.splitext(...)[0]`
5. **Consistent with Modern Python** — Most standard library functions (like `open()`) accept `Path` objects directly

---

## Common Mistakes

1. Mixing string concatenation (`folder + "/" + filename`) with `pathlib`, reintroducing the exact cross-platform bugs `pathlib` avoids
2. Forgetting `parents=True` when creating a nested directory structure that doesn't fully exist yet
3. Using `.read_text()`/`.write_text()` without specifying `encoding="utf-8"` explicitly (see [[20 - Encoding]])
4. Forgetting that `glob()` returns a **generator** the first time — wrap in `list()` if you need to reuse or measure the results
5. Assuming a relative `Path` is automatically resolved — call `.resolve()` explicitly when you need the full, unambiguous path

---

## Best Practices

1. **Use `pathlib.Path` instead of `os.path`** in new code — it's the modern standard
2. **Build paths with `/`** instead of manual string concatenation
3. **Always specify `encoding="utf-8"`** with `.read_text()`/`.write_text()`
4. **Use `mkdir(parents=True, exist_ok=True)`** to safely create directory structures
5. **Use `glob()`/`rglob()`** instead of manually walking directories with `os.listdir()`

---

## See also

- [[07 - OOP — Dunder Methods]] — the `/` operator overloading that makes `Path` combination possible
- [[15 - Context Managers]] — `open()` still works with `Path` objects for more advanced file handling
- [[20 - Encoding]] — why explicit UTF-8 matters for `.read_text()`/`.write_text()` too
- [[22 - Custom Serialization]] — reading and writing the JSON files this lesson's examples build paths for

---

## → What's next

You now understand **Pathlib**:

- Why `pathlib.Path` is preferred over the older `os.path` functions
- How to build, inspect, and navigate paths using `/` and properties like `.stem`, `.suffix`, `.parent`
- How to read, write, create, and delete files and directories directly through `Path` methods
- How `glob()` finds files matching a pattern, including recursively with `**`

In the next lesson, we will explore **Custom Serialization** — how to control exactly how your own classes convert to and from formats like JSON, building on both encoding and dataclasses.

Continue with **[[22 - Custom Serialization]]**
