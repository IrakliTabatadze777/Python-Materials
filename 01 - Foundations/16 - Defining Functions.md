---
tags:
  - foundations
  - python
  - functions
stage: 1
difficulty: Beginner
---

# Defining Functions in Python

**Prev:** [[15 - Sets]] | **Next:** [[17 - Arguments and Parameters.md]]

> Functions are reusable blocks of code that perform a specific task. They help you organize your program, avoid repetition, and make your code easier to read and maintain.

---

## What is a Function?

A **function** is a named block of code that runs only when it is called.

Instead of writing the same logic multiple times, you define it once and reuse it.

```python
print("Hello")
print("Hello")
print("Hello")
````

With a function:

```python
def greet():
    print("Hello")

greet()
greet()
greet()
```

---

## Defining a Function

In Python, functions are defined using the `def` keyword.

```python
def function_name():
    # code block
    pass
```

### Example:

```python
def say_hello():
    print("Hello, world!")
```

### Key parts:

* `def` → keyword used to define a function
* `function_name` → name of the function (should be descriptive)
* `()` → parentheses (will hold parameters later)
* `:` → starts the function body
* Indented block → code inside the function

---

## Calling a Function

A function does nothing until you call it.

```python
def greet():
    print("Hello!")

greet()   # function call
```

### Output:

```
Hello!
```

---

## Return Statement

Functions can return values using `return`.

```python
def add():
    return 5 + 3

result = add()
print(result)
```

### Output:

```
8
```

### Why use `return`?

* It sends data back to the caller
* It allows reuse of computed results
* It stops function execution immediately

```python
def example():
    return 10
    print("This will never run")  # this line is ignored because return exits the function
```

---

## Function Without Return

If a function has no `return`, it returns `None` automatically.

```python
def greet():
    print("Hello")

result = greet()
print(result)
```

### Output:

```
Hello
None
```

### Explanation:

* The function prints `"Hello"`
* But it does not return a value → so Python returns `None`

---

## Function With Multiple Returns

A function can return multiple values as a tuple.

```python
def calculate():
    return 10, 20, 30

a, b, c = calculate()
print(a, b, c)
```

### Output:

```
10 20 30
```

### Explanation:

* The function returns a tuple `(10, 20, 30)`
* Values are unpacked into variables `a`, `b`, `c`

---

## Docstrings (Function Documentation)

A **docstring** explains what a function does.

```python
def greet():
    """
    Prints a greeting message to the user.
    """
    print("Hello!")
```

### Explanation:

* Docstring is used for documentation
* It describes the purpose of the function

```python
print(greet.__doc__)
```

### Output:

```
Prints a greeting message to the user.
```

---

## Function Body and Indentation

Python uses indentation to define the function body.

```python
def test():
    print("Line 1")
    print("Line 2")
```

### Explanation:

* Both lines belong to the function body
* Indentation defines scope

Incorrect indentation:

```python
def test():
print("Wrong indentation")  # ❌ IndentationError
```

### Explanation:

* Missing indentation causes syntax error
* Python relies on indentation instead of braces

---

## Nested Functions

A function can be defined inside another function. This is called a **nested function**.

```python
def outer():
    print("Outer function started")

    def inner():
        print("Inner function running")

    inner()  # calling inner function inside outer

outer()
```

### Output:

```
Outer function started
Inner function running
```

### Explanation:

* `inner()` exists only inside `outer()`
* It is not accessible from outside
* It is used for helper logic inside a function

---

## Function Naming Rules

Good function names:

* Should be descriptive
* Use lowercase letters
* Use underscores (`snake_case`)

```python
def calculate_sum():
    pass
```

Bad examples:

```python
def a():
    pass

def CalculateSum():
    pass
```

### Explanation:

* `calculate_sum` clearly describes purpose
* `a` and `CalculateSum` are unclear or inconsistent

---

## Functions Are First-Class Objects

In Python, functions behave like variables.

```python
def say_hi():
    return "Hi"

x = say_hi
print(x())
```

### Output:

```
Hi
```

### Explanation:

* Function is assigned to variable `x`
* `x()` calls the same function

---

## Simple Examples

### 1. Function with no parameters

```python
def welcome():
    return "Welcome!"

print(welcome())
```

### Output:

```
Welcome!
```

### Explanation:

* Returns a fixed string
* No input required

---

### 2. Function performing calculation

```python
def square():
    return 4 * 4

print(square())
```

### Output:

```
16
```

### Explanation:

* Returns square of 4
* Pure computation function

---

### 3. Function returning processed data

```python
def format_name():
    first = "John"
    last = "Doe"
    return first + " " + last

print(format_name())
```

### Output:

```
John Doe
```

### Explanation:

* Combines two strings
* Returns formatted full name

---

## Why Use Functions?

Functions help you:

* Avoid repeating code
* Organize logic into blocks
* Make debugging easier
* Improve readability
* Reuse logic across programs

---

## Function Execution Flow

```python
def step_one():
    print("Step 1")

def step_two():
    print("Step 2")

step_one()
step_two()
```

### Output:

```
Step 1
Step 2
```

### Explanation:

* Functions run in order they are called
* Each function executes independently

---

## Common Mistakes

### 1. Forgetting parentheses

```python
greet   # function object, not executed
greet() # correct
```

### Explanation:

* Without `()`, function is not executed
* It only references the function object

---

### 2. Misusing indentation

```python
def greet():
print("Hello")  # ❌ wrong indentation
```

### Explanation:

* Python requires indentation for function body
* Otherwise it raises `IndentationError`

---

### 3. Forgetting return when value is needed

```python
def add():
    print(5 + 3)
```

### Explanation:

* Function prints result but does not return it
* You cannot reuse result in expressions

---

### 4. Confusing print vs return

```python
def a():
    print(10)

def b():
    return 10
```

### Explanation:

* `print()` shows output only
* `return` sends value for reuse

---

## Best Practices

* Keep functions small and focused
* Use meaningful names
* Prefer `return` over `print` for logic
* Add docstrings for clarity
* Avoid repeating code — extract it into functions

---

## → What's Next

Now that you understand how to define and structure functions (including nested ones), the next step is learning how to pass data into them.

Continue with **[[17 - Arguments and Parameters]]**
