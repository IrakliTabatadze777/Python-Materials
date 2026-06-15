---
tags:
  - foundations
  - python
  - functions
  - higher-order-functions
stage: 1
difficulty: Intermediate
---

# Higher-Order Functions in Python

**Prev:** [[20 - Recursive Functions]] | **Next:** [[22 - Built-in Functions]]

> Higher-order functions are functions that can accept other functions as arguments, return functions as results, or both. They are a key concept in Python and form the foundation for decorators, callbacks, and functional programming.

---

## Functions Are Objects

Before understanding higher-order functions, you must understand that functions in Python are **first-class objects**.

This means functions can:

- Be assigned to variables
- Be passed as arguments
- Be returned from other functions
- Be stored in data structures

```python
def greet():
    return "Hello"

message_function = greet

print(message_function())
````

### Output

```text
Hello
```

### Explanation

* `greet` is a function object.
* `message_function = greet` stores a reference to the function (not its result).
* `message_function()` executes the same function.
* Both names point to the same callable object.

---

## What Is a Higher-Order Function?

A higher-order function is a function that:

1. Accepts another function as an argument
2. Returns another function
3. Or does both

---

## Passing a Function as an Argument

```python
def greet():
    print("Hello")

def execute(func):
    func()
```

Usage:

```python
execute(greet)
```

### Output

```text
Hello
```

### Step-by-Step Explanation

When Python executes:

```python
execute(greet)
```

1. The function object `greet` is passed into `execute`.
2. Inside `execute`, parameter `func` refers to `greet`.
3. `func()` executes the function.
4. `"Hello"` is printed.

---

## Example: Processing Different Behaviors

```python
def increment(x):
    return x + 1

def double(x):
    return x * 2

def process(value, operation):
    return operation(value)
```

Usage:

```python
print(process(5, increment))
print(process(5, double))
```

### Output

```text
6
10
```

### Explanation

First call:

```python
process(5, increment)
```

Becomes:

```python
increment(5)
```

Result:

```text
6
```

Second call:

```python
process(5, double)
```

Becomes:

```python
double(5)
```

Result:

```text
10
```

The same `process()` function behaves differently depending on the function passed to it.

---

## Returning Functions

```python
def create_greeting():
    def greet():
        print("Hello")

    return greet
```

Usage:

```python
greeting_function = create_greeting()
greeting_function()
```

### Output

```text
Hello
```

### Step-by-Step Explanation

1. `create_greeting()` runs.
2. It defines an inner function `greet`.
3. It returns the function object `greet` (not calling it).
4. `greeting_function` stores that function.
5. Calling `greeting_function()` executes `greet()`.

---

## Returning Functions Based on Input

```python
def choose_operation(operation_name):

    def add(a, b):
        return a + b

    def multiply(a, b):
        return a * b

    if operation_name == "add":
        return add

    return multiply
```

Usage:

```python
operation = choose_operation("add")
print(operation(3, 4))
```

### Output

```text
7
```

### Explanation

* `choose_operation("add")` returns the `add` function.
* `operation` becomes a reference to `add`.
* `operation(3, 4)` becomes `add(3, 4)`.

---

## Closures

A closure is a function that remembers variables from its enclosing scope.

```python
def make_multiplier(multiplier):

    def multiply(number):
        return number * multiplier

    return multiply
```

Usage:

```python
double = make_multiplier(2)

print(double(5))
```

### Output

```text
10
```

### Step-by-Step Explanation

1. `make_multiplier(2)` is called.
2. `multiplier = 2` is stored in the outer function scope.
3. `multiply()` is returned.
4. Even after `make_multiplier()` finishes, `multiply()` still remembers `multiplier`.
5. `double(5)` uses the remembered value:

   * `5 * 2 = 10`

This preserved outer state is called a **closure**.

---

## Why Higher-Order Functions Matter

They allow you to:

* Reuse code
* Avoid duplication
* Write flexible APIs
* Create decorators
* Build callback-based systems
* Improve modular design

---

## Real-World Example: Validation System

```python
def validate_username(username):
    return len(username) >= 3

def validate_age(age):
    return age >= 18

def run_validation(value, validator):
    return validator(value)
```

Usage:

```python
print(run_validation("alex", validate_username))
print(run_validation(25, validate_age))
```

### Output

```text
True
True
```

### Explanation

First call:

```python
run_validation("alex", validate_username)
```

Becomes:

```python
validate_username("alex")
```

Second call:

```python
run_validation(25, validate_age)
```

Becomes:

```python
validate_age(25)
```

The validation logic is completely separated from `run_validation()`.

---

## Real-World Example: Message Processing

```python
def upper_case(text):
    return text.upper()

def lower_case(text):
    return text.lower()

def format_message(message, formatter):
    return formatter(message)
```

Usage:

```python
print(format_message("Hello World", upper_case))
print(format_message("Hello World", lower_case))
```

### Output

```text
HELLO WORLD
hello world
```

### Explanation

* `format_message()` does not know how formatting works.
* It delegates behavior to the passed function.
* This makes the function reusable and flexible.

---

## Common Mistakes

### 1. Calling the Function Instead of Passing It

Wrong:

```python
execute(greet())
```

Explanation:

* `greet()` is executed immediately.
* Its return value is passed instead of the function.

Correct:

```python
execute(greet)
```

Explanation:

* The function object is passed.
* `execute()` decides when to call it.

---

### 2. Forgetting That Functions Are Objects

Wrong:

```python
operation = increment()
```

Explanation:

* The function is executed immediately.

Correct:

```python
operation = increment
```

Explanation:

* The function reference is stored.

---

### 3. Returning a Function Instead of Calling It

Wrong:

```python
def create():
    def inner():
        print("Hello")

    return inner()
```

Explanation:

* `inner()` executes immediately.
* Return value is not a function.

Correct:

```python
def create():
    def inner():
        print("Hello")

    return inner
```

Explanation:

* The function itself is returned.
* It can be executed later.


---

## → What's Next

Now that you understand higher-order functions and how functions can be passed around and returned like values, the next step is learning about Python’s **built-in functions**.

Built-in functions are ready-made tools provided by Python that help you perform common operations quickly without writing everything manually.

Continue with [[22 - Built-in Functions]]
