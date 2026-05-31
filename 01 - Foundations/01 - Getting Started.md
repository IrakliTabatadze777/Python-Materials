---
tags:
  - foundations
  - python
  - setup
stage: 1
difficulty: Beginner
---

# Getting Started with Python

**Prev:** — | **Next:** [[02 - Variables and Data Types]]

> Python is a powerful, beginner-friendly programming language that lets you write clean, readable code to solve real problems — from automating tasks to building complex applications.

---

## What is Python?

**Python** is a **high-level, interpreted, general-purpose** programming language created by Guido van Rossum and first released in 1991.

It is one of the most popular programming languages in the world because it is:
- Easy to read and write
- Very versatile
- Has a huge ecosystem of libraries and tools
- Excellent for beginners while powerful enough for professionals

### What does "High-Level Programming Language" mean?

To understand this deeply, let's compare:

**Low-level languages** (like C, C++, Assembly):
- Very close to how the computer actually works (memory management, processor instructions)
- You have to tell the computer exactly how to do things step by step
- Very fast, but hard to write and easy to make mistakes
- You manage memory manually

**High-level languages** (like Python, JavaScript, Ruby):
- Far away from hardware details
- Closer to human language
- The language handles many complex details for you (memory management, garbage collection, etc.)
- You focus on solving problems, not managing the machine
- Trade-off: slightly slower than low-level languages, but much faster to develop

**Python is a very high-level language.** It abstracts away most technical complexity so you can write powerful programs with very few lines of code.

For example, in Python you can write:
```python
print("Hello, World!")
```
In lower-level languages, the same task requires many more lines and much more knowledge about the system.

### Key Features of Python

| Feature                  | Simple Explanation                                      |
|--------------------------|---------------------------------------------------------|
| **Interpreted**          | Code runs line by line. No need to compile before running. |
| **Dynamically Typed**    | You don't need to declare variable types. Python figures it out. |
| **Readable**             | Uses indentation instead of braces `{}`. Looks clean. |
| **Batteries Included**   | Comes with many useful tools (no need to install everything) |
| **Multi-paradigm**       | You can write procedural, object-oriented, or functional code |

---

## What is Python Used For? (Real Jobs & Applications)

Python is used everywhere in the real world:

**Common Jobs & Fields:**
- **Data Scientist / Data Analyst** — analyzing data, creating visualizations
- **Machine Learning / AI Engineer** — building AI models (TensorFlow, PyTorch)
- **Web Developer** — building websites and backends (Django, Flask, FastAPI)
- **Automation Engineer** — writing scripts to automate repetitive tasks
- **DevOps Engineer** — infrastructure automation, deployment tools
- **Scientific Computing** — used by researchers at NASA, universities
- **Cybersecurity** — penetration testing and security tools
- **Game Development** (with libraries like Pygame)
- **Finance** — algorithmic trading and risk analysis

**Big Companies Using Python:**
- Google, Instagram, Netflix, Spotify, Dropbox, Reddit, Uber, Airbnb

---

## How to Install Python

### Step 1: Check if Python is already installed

Open your terminal (macOS/Linux) or Command Prompt / PowerShell (Windows) and type:

```bash
python --version
```

or

```bash
python3 --version
```

If you see a version like `Python 3.12.x`, Python is installed.

### Step 2: Install Python (Recommended: Latest Version 3.12 or 3.13)

**Windows:**

***Option 1 - Official installer (recommended)***

1. Go to [https://www.python.org/downloads/](https://www.python.org/downloads/)
2. Download the latest Python 3 installer
3. **Important**: Check “Add python.exe to PATH”
4. Click “Install Now”

***Option 2 - Microsoft Store***

1. Open **Microsoft Store**
2. Search for **Python**
3. Select the latest version published by the **Python Software Foundation**
4. Click **Install**

> Note: The Microsoft Store version is easier to install but may be slightly less flexible for advanced development setups compared to the official installer.

**macOS:**
```bash
brew install python
```
Or download from python.org

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install python3 python3-pip
```

---

## Running Python for the First Time

### 1. Interactive Shell (REPL) — Best for Beginners

Type in your terminal:

```bash
python
```

or

```bash
python3
```

You will see `>>>` prompt. Try these commands:

```python
>>> print("Hello, World!")
>>> 10 + 25
>>> name = "Luka"
>>> print("My name is", name)
>>> exit()        # to quit
```

### 2. Writing Your First Python Program

Create a file called `hello.py` using any text editor (VS Code is recommended).

```python
# hello.py
# This is a comment

print("Hello, World!")
print("Welcome to Python!")
print("I am learning Python")

# Simple calculation
result = 7 * 8
print("7 multiplied by 8 is:", result)
```

**How to run it:**

```bash
python hello.py
```

or

```bash
python3 hello.py
```

---

## Virtual Environments (Best Practice)

Never install packages globally. Use virtual environments:

```bash
# Create environment
python -m venv .venv

# Activate it
# macOS / Linux:
source .venv/bin/activate

# Windows:
.venv\Scripts\activate
```

After activation you will see `(.venv)` in your prompt.

To deactivate:
```bash
deactivate
```

---

## Summary

* Python installed and working (`python --version`)
* Ran Python in interactive mode
* Created and ran your first `.py` file
* Understood what a high-level language is
* Learned what Python is used for in real jobs

---

## See also

- [[02 - Variables and Data Types]] — names, types, and how Python stores values in memory
- [Python.org — Downloads](https://www.python.org/downloads/) — official installers for your platform
- [Python docs — Tutorial](https://docs.python.org/3/tutorial/index.html) — official getting-started guide
- [Python docs — venv](https://docs.python.org/3/library/venv.html) — virtual environment reference

---

## → What's next

You have Python installed and a mental model of how the interpreter runs your code. The next step is storing and working with data — how names bind to objects, what types exist, and why mutability matters — starting with [[02 - Variables and Data Types]].


