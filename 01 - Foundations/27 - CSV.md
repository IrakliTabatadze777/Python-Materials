---
tags:
  - foundations
  - python
  - data-formats
stage: 1
difficulty: Beginner
---

# Working with CSV Files in Python

**Prev:** [[26 - File I-O]] | **Next:** [[28 - JSON]]

> In this lesson you will learn how to read and write **CSV** files — one of the most common and important data formats in programming. You will master Python’s built-in `csv` module with deep understanding of every component.

---

## What is CSV?

**CSV** stands for **Comma-Separated Values**.

It is a simple, plain-text file format used to store **tabular data** (data organized in rows and columns). 

- Each line in the file represents one **row**.
- Values (columns) in each row are separated by a **delimiter** — most commonly a comma (`,`).
- It is a text file, so you can open it with Notepad, Excel, Google Sheets, or any text editor.

### Sample File: `users.csv`

We will use this file for all reading examples:

```csv
id,name,age,city,active
1,Alice,28,New York,True
2,Bob,34,London,False
3,Charlie,22,Paris,True
4,Diana,45,Berlin,True
```

**Why does CSV exist and why is it so popular?**

- **Simplicity**: Easy for humans and machines to read/write
- **Universality**: Supported by virtually every spreadsheet program, database, and programming language
- **Lightweight**: Much smaller than Excel (.xlsx) files
- **Interoperability**: Perfect for moving data between systems (e.g., export from a website, import into Python)
- **Human readable**: You can understand the data without special software
- **Standard for data exchange**: Used heavily in data science, web development, finance, and business analytics

**Limitations**: No data types (everything is text), no built-in formulas, limited support for complex structures.

---

## The `csv` Module

Python provides a built-in `csv` module to handle CSV files safely and correctly (handles quoting, escaping commas inside fields, different line endings, etc.).

You must **import** it:

```python
import csv
```

Never try to read CSV files manually with `split(',')` — it breaks when fields contain commas (e.g., "New York, USA").

---

## Reading CSV Files

### 1. `csv.reader` — Basic Reader

`csv.reader` creates an **iterator** that returns each row as a **list** of strings.

```python
import csv

with open("users.csv", "r", encoding="utf-8", newline="") as file:
    reader = csv.reader(file)        # Create the reader object
    
    for row in reader:               # Iterate over rows
        print(row)
```

**Output:**
```
['id', 'name', 'age', 'city', 'active']
['1', 'Alice', '28', 'New York', 'True']
['2', 'Bob', '34', 'London', 'False']
['3', 'Charlie', '22', 'Paris', 'True']
['4', 'Diana', '45', 'Berlin', 'True']
```

**What is happening?**
- `csv.reader(file)` creates a reader object configured for the file
- It handles quoting, delimiters, and line endings automatically
- Every value is returned as a **string** (you must convert types yourself)

---

### 2. `csv.DictReader` — Dictionary Reader (Recommended)

`csv.DictReader` creates an iterator that returns each row as a **dictionary**, using the first row (header) as keys.

```python
import csv

with open("users.csv", "r", encoding="utf-8", newline="") as file:
    reader = csv.DictReader(file)     # Automatically uses first row as keys
    
    for row in reader:
        print(f"Name: {row['name']}, Age: {row['age']}, City: {row['city']}, Active: {row['active']}")
```

**Output:**
```
Name: Alice, Age: 28, City: New York, Active: True
Name: Bob, Age: 34, City: London, Active: False
Name: Charlie, Age: 22, City: Paris, Active: True
Name: Diana, Age: 45, City: Berlin, Active: True
```

**Why use `DictReader`?**
- Much more readable and maintainable
- Access data by meaningful column names instead of indices (e.g., `row['name']` vs `row[1]`)
- Resistant to column reordering
- Ideal for most real-world applications

---

### Customizing the Reader

```python
reader = csv.reader(file, delimiter=',', quotechar='"')
reader = csv.DictReader(file, delimiter=';')   # For semicolon-separated files
```

---

## Writing CSV Files

### 1. `csv.writer`

Writes lists of data.

```python
import csv

data = [
    ["id", "name", "score"],
    [1, "Alice", 95.5],
    [2, "Bob", 87.0],
    [3, "Charlie", 92.3]
]

with open("scores.csv", "w", encoding="utf-8", newline="") as file:
    writer = csv.writer(file)          # Create writer object
    writer.writerows(data)             # Write all rows at once

print("scores.csv created!")
```

**Resulting `scores.csv`:**
```csv
id,name,score
1,Alice,95.5
2,Bob,87.0
3,Charlie,92.3
```

---

### 2. `csv.DictWriter`

Writes dictionaries using field names.

```python
import csv

students = [
    {"name": "Emma", "grade": "A", "age": 19},
    {"name": "Liam", "grade": "B", "age": 21}
]

fieldnames = ["name", "grade", "age"]

with open("students.csv", "w", encoding="utf-8", newline="") as file:
    writer = csv.DictWriter(file, fieldnames=fieldnames)
    writer.writeheader()               # Write the header row
    writer.writerows(students)

print("students.csv created with headers!")
```

**Resulting file:**
```csv
name,grade,age
Emma,A,19
Liam,B,21
```

---

## Important Parameters

- `newline=""` — Prevents extra blank lines on Windows
- `encoding="utf-8"` — Handles special characters correctly(e.g. Georgian characters)
- `delimiter=','` — Change separator
- `quoting=csv.QUOTE_ALL` — Force quotes around all fields

---

## Data Type Conversion

CSV stores **everything as strings**. You must convert manually:

```python
for row in reader:
    row["age"] = int(row["age"])                    # String → Integer
    row["active"] = row["active"].lower() == "true" # String → Boolean
    row["score"] = float(row.get("score", 0))       # Safe conversion
```

---

## Error Handling & Best Practices

```python
import csv

def read_csv_safely(filename):
    try:
        with open(filename, "r", encoding="utf-8", newline="") as file:
            reader = csv.DictReader(file)
            return list(reader)
    except FileNotFoundError:
        print(f"❌ File '{filename}' not found.")
        return []
    except Exception as e:
        print(f"❌ CSV Error: {e}")
        return []
```

**Best Practices:**
- Always use `with` statement
- Prefer `DictReader` / `DictWriter`
- Specify encoding and newline
- Convert data types explicitly
- Validate data when reading
- Use `pandas` for very large or complex CSV processing (advanced)

---

## Summary Table

| Component         | Returns          | Best For                        | Access Method          |
|-------------------|------------------|---------------------------------|------------------------|
| `csv.reader`      | List of strings  | Simple processing               | Index (`row[0]`)       |
| `csv.DictReader`  | Dict per row     | Most real applications          | Key (`row['name']`)    |
| `csv.writer`      | Writes lists     | Simple data                     | Lists                  |
| `csv.DictWriter`  | Writes dicts     | Most real applications          | Dictionaries + headers |

---

## Key Takeaways

- CSV is the universal format for tabular data
- Never parse CSV manually — always use the `csv` module
- `DictReader`/`DictWriter` are usually the best choice
- Everything in CSV is text — convert types yourself
- Always use context managers and proper encoding

---

## See also

- [[26 - File I-O]] — fundamental file operations; how `open()` works with modes and encodings
- [[28 - JSON]] — another popular data format for nested structures and APIs
- [[25 - Error Handling]] — handling file errors with try-except blocks
- [[10 - For loop]] — iterating over CSV rows efficiently
- [[14 - Dictionaries]] — DictReader returns dictionary objects for each row

---

## → What's Next

Now that you can work with CSV files, the next step is learning **JSON** — another extremely popular data format used in web development, APIs, and configuration files. You will also master serialization and deserialization.

Continue with **[[28 - JSON]]**
