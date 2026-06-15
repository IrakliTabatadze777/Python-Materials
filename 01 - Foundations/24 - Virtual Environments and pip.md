---
tags:
  - foundations
  - python
  - tools
  - setup
stage: 1
difficulty: Beginner
---

# Virtual Environments and pip in Python

**Prev:** [[23 - Modules and Imports]] | **Next:** [[25 - File Handling]]

> In this lesson you will learn how to manage Python packages and isolate project dependencies using **virtual environments** and **`pip`**. This is essential knowledge for every Python developer working on real projects.

---

## Built-in Modules vs Third-Party Packages

Python comes with a rich **Standard Library** — many useful modules that are already installed and available as soon as you install Python (e.g., `math`, `random`, `datetime`, `os`, `sys`, `json`, etc.).

You can use these modules immediately with `import`:

```python
import math
import random
from datetime import datetime
```

However, as your projects grow, you will often need functionality that is **not** included in the standard library. For example:

- Making HTTP requests (`requests`)
- Working with data tables (`pandas`)
- Creating beautiful terminal output (`rich`)
- Building web applications (`flask`, `django`)
- Handling images (`pillow`)
- Machine learning (`scikit-learn`, `tensorflow`)

These are called **third-party packages**. They are developed by the Python community and hosted on **PyPI** (Python Package Index).

To install them, we use **`pip`** — Python’s package manager. And to keep everything organized and conflict-free, we use **virtual environments**.

---

## Why Do We Need Virtual Environments?

Imagine you have two projects:

- **Project A** needs `requests==2.25.1`
- **Project B** needs `requests==2.28.0`

If you install packages globally (directly on your computer), one project will break the other when you upgrade a package.

**Virtual Environment** solves this by creating an **isolated Python environment** for each project.

### Benefits of Virtual Environments

- **Isolation** — Each project has its own set of installed packages
- **Reproducibility** — Anyone can recreate the exact same environment
- **Avoid version conflicts**
- **Clean system Python** — Your global Python stays untouched
- **Easy dependency management**
- **Team collaboration** — Everyone works with the same versions

---

## What is pip?

**pip** is the standard package installer for Python. It downloads packages from PyPI and installs them into your current environment.

**Common pip commands:**

```bash
pip install package_name
pip install package_name==1.2.3          # specific version
pip install package_name>=2.0,<3.0       # version range
pip uninstall package_name
pip list                                 # show installed packages
pip freeze                               # list packages with exact versions
```

---

## Creating a Virtual Environment

Python includes the `venv` module (recommended for beginners).

### Step-by-step

**1. Create a new project folder**
```bash
mkdir my_project
cd my_project
```

**2. Create the virtual environment**

```bash
# Windows
python -m venv venv

# macOS / Linux
python3 -m venv venv
```

xThis creates a folder called `venv` (or `.venv`) with a complete isolated copy of Python and pip.

---

## Activating the Virtual Environment

**Windows:**
```cmd
venv\Scripts\activate
```

**macOS / Linux:**
```bash
source venv/bin/activate
```

**After activation, your terminal prompt changes** to show `(venv)`:
```
(venv) $
```

This tells you that any `pip install` or `python` command will now use the isolated environment.

---

## Working Inside the Virtual Environment

```bash
# Check which Python you're using
python --version
which python     # macOS/Linux
where python     # Windows

# Install packages
pip install requests rich
```

Now you can use third-party packages in your code:

```python
# main.py
import requests
from rich.console import Console

console = Console()
response = requests.get("https://api.github.com")
console.print(f"Status: [green]{response.status_code}[/green]")
```

---

## Deactivating the Environment

```bash
deactivate
```

---

## requirements.txt — Making Projects Reproducible

This file records exactly which packages (and versions) your project needs.

### Generate the file

```bash
pip freeze > requirements.txt
```

**Example `requirements.txt`:**
```txt
requests==2.32.3
rich==13.7.1
```

### Install dependencies from the file

```bash
pip install -r requirements.txt
```

**Pro tip:** Always commit `requirements.txt` to your version control (Git), but **never** commit the `venv` folder.

---

## Complete Project Setup Example

**Folder structure:**
```
my_project/
├── venv/                  # ← ignored in .gitignore
├── requirements.txt
├── main.py
└── utils.py
```

**Workflow:**
1. Create venv → `python -m venv venv`
2. Activate it
3. Install packages → `pip install requests rich`
4. Save dependencies → `pip freeze > requirements.txt`
5. Write your code
6. Share the project — others just run `pip install -r requirements.txt`

---

## Advanced pip Usage

```bash
# Upgrade a package
pip install --upgrade requests

# Install from a specific source
pip install git+https://github.com/user/repo.git

# Show package information
pip show requests

# Check for outdated packages
pip list --outdated
```

---

## Common Pitfalls & Troubleshooting

- **"pip command not found"** → Use `python -m pip install ...`
- **Permission errors** → Never use `sudo pip`. Always use virtual environments.
- **Wrong Python version** → Specify version: `python3.11 -m venv venv`
- **Environment not activating** → Double-check path and use correct command
- **Conflicting packages** → Virtual environments prevent most of these issues

---

## Best Practices

- **Always** create a virtual environment for every new project
- Name it `venv` or `.venv` and add it to `.gitignore`
- Keep `requirements.txt` up to date
- Document required Python version in `README.md`
- Use `pip install -r requirements.txt --upgrade` when updating
- Consider tools like `pip-tools` or `poetry` for larger projects (later topics)

**Recommended `.gitignore`:**
```gitignore
venv/
.venv/
__pycache__/
*.pyc
.env
```

---

## Summary Table

| Command / Action                     | Purpose                                      |
|--------------------------------------|----------------------------------------------|
| `python -m venv venv`                | Create virtual environment                   |
| `source venv/bin/activate`           | Activate (macOS/Linux)                       |
| `venv\Scripts\activate`              | Activate (Windows)                           |
| `deactivate`                         | Deactivate                                   |
| `pip install requests`               | Install third-party package                  |
| `pip freeze > requirements.txt`      | Save exact dependencies                      |
| `pip install -r requirements.txt`    | Install all project dependencies             |
| `pip list`                           | View installed packages                      |

---

## Key Takeaways

- Python has excellent **built-in modules**, but real projects need **third-party packages**
- Use **pip** to install packages
- Always work inside a **virtual environment** to avoid conflicts
- Use `requirements.txt` to make your project shareable and reproducible
- This is the standard professional workflow used by Python developers worldwide

---

## → What's Next

Now that you know how to manage packages with virtual environments and pip, continue with **Error Handling** — learning how to make your code robust when things go wrong.

Continue with **[[25 - Error Handling]]**

