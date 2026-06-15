---
tags:
  - foundations
  - python
  - functions
  - parameters
  - arguments
stage: 1
difficulty: Beginner
---

# Arguments and Parameters in Python

**Prev:** [[16 - Defining Functions]] | **Next:** [[18 - Scope and Namespaces]]

> Parameters and arguments allow functions to receive input, making them flexible and reusable instead of working with fixed values.

---

## What are Parameters and Arguments?

Although they are often used interchangeably, they are not the same thing:

- **Parameters** → variables defined in the function declaration
- **Arguments** → actual values passed to the function when calling it

```python
def greet(name):   # 'name' is a parameter (placeholder inside function)
    print("Hello", name)

greet("Alex")      # "Alex" is an argument (actual value passed to function)
````

---

## Parameters (Function Definition Side)

Parameters are placeholders inside a function definition.

```python
def add(a, b):   # 'a' and 'b' are parameters
    return a + b
```

Think of parameters as **empty containers** waiting for values.

---

## Arguments (Function Call Side)

Arguments are real values passed into a function when calling it.

```python
add(5, 10)   # 5 and 10 are arguments passed to the function
```

Mapping happens like this:

* `a = 5`
* `b = 10`

---

## Positional Arguments

Positional arguments are assigned based on their order.

```python
def subtract(a, b):
    return a - b

subtract(10, 3)  # a = 10, b = 3 (order matters)
```

⚠️ Changing order changes result:

```python
subtract(3, 10)  # a = 3, b = 10 → different output
```

---

## Keyword Arguments

Keyword arguments explicitly specify parameter names.

```python
def user_info(name, age):
    print(name, age)

# Arguments are passed using parameter names
user_info(name="Alex", age=30)
```

Order does not matter:

```python
# Same result, different order
user_info(age=30, name="Alex")
```

---

## Default Parameters

Default parameters provide fallback values if no argument is passed.

```python
def greet(name="Guest"):
    print("Hello", name)

greet()           # uses default value → "Guest"
greet("Alex")     # overrides default → "Alex"
```

### Why default parameters are useful:

* Avoid missing argument errors
* Provide optional behavior
* Simplify function usage

---

## Mixing Positional and Keyword Arguments

You can combine both types, but positional arguments must come first.

```python
def example(a, b, c):
    print(a, b, c)

# 'a' and 'b' are positional, 'c' is keyword
example(1, 2, c=3)
```

⚠️ Invalid example:

```python
# ❌ Keyword argument cannot come before positional argument
example(a=1, 2, 3)
```

---

## Variable-Length Arguments (*args)

`*args` allows a function to accept any number of positional arguments.

```python
def total(*args):
    # args is a tuple of all passed positional arguments
    print(args)

total(1, 2, 3, 4)
```

Output:

```python
(1, 2, 3, 4)
```

### Using *args in calculations:

```python
def total(*args):
    # sum all received values
    return sum(args)

print(total(1, 2, 3))  # 6
```

---

## Variable-Length Keyword Arguments (**kwargs)

`**kwargs` allows a function to accept any number of keyword arguments.

```python
def show_info(**kwargs):
    # kwargs is a dictionary of key-value pairs
    print(kwargs)

show_info(role="admin", level=5)
```

Output:

```python
{'role': 'admin', 'level': 5}
```

---

## Using *args and **kwargs Together

```python
def demo(*args, **kwargs):
    # args → tuple of positional arguments
    # kwargs → dictionary of keyword arguments
    print("args:", args)
    print("kwargs:", kwargs)

demo(1, 2, 3, status="active", debug=True)
```

---

## Rules for Ordering Parameters

When combining different parameter types, order must be:

```python
def function(positional, default=10, *args, **kwargs):
    pass
```

Order rules:

1. Positional parameters
2. Default parameters
3. `*args`
4. `**kwargs`

---

## Argument Unpacking

You can unpack collections into function arguments.

### Unpacking lists (*)

```python
def add(a, b, c):
    # adds three numbers
    return a + b + c

numbers = [1, 2, 3]

# '*' unpacks list into positional arguments
add(*numbers)
```

---

### Unpacking dictionaries (**)

```python
def user(name, age):
    # prints user information
    print(name, age)

data = {"name": "Alex", "age": 25}

# '**' unpacks dictionary into keyword arguments
user(**data)
```

---

## Common Mistakes

### 1. Wrong order of arguments

```python
def greet(name, age):
    pass

# ❌ Keyword argument cannot come before positional argument
greet(age=25, "Alex")
```

---

### 2. Mixing positional order incorrectly

```python
def divide(a, b):
    return a / b

# order changes result
divide(2, 10)  # 0.2
divide(10, 2)  # 5.0
```

---

### 3. Mutable default argument mistake

❌ Wrong:

```python
def append_item(item, lst=[]):
    # default list is shared between calls
    lst.append(item)
    return lst
```

✔ Correct:

```python
def append_item(item, lst=None):
    # create new list each time if not provided
    if lst is None:
        lst = []
    lst.append(item)
    return lst
```

---

## Real-World Examples

### 1. Flexible sum function

```python
def sum_numbers(*args):
    # returns sum of all provided numbers
    return sum(args)

print(sum_numbers(1, 2, 3, 4))
```

---

### 2. Dynamic user builder

```python
def create_user(**kwargs):
    # returns user data as dictionary
    return kwargs

print(create_user(role="admin", active=True))
```

---

### 3. Application configuration

```python
def configure_app(name, debug=False, **settings):
    # name → app name
    # debug → debug mode flag
    # settings → additional configuration options
    print(name, debug, settings)

configure_app("MyApp", debug=True, port=8000)
```

---

## Why Parameters and Arguments Matter

They allow functions to:

* Work with dynamic input
* Avoid hardcoded values
* Improve reusability
* Handle flexible data structures
* Make code easier to maintain

---

## Summary

| Concept       | Meaning                               |
| ------------- | ------------------------------------- |
| Parameter     | Variable defined in function          |
| Argument      | Value passed to function              |
| `*args`       | Multiple positional arguments         |
| `**kwargs`    | Multiple keyword arguments            |
| Default value | Fallback value if argument is missing |

---

## See also

- [[16 - Defining Functions]] — function structure and return values
- [[18 - Scope and Namespaces]] — how function parameters create local scope
- [[19 - Lambda Functions]] — lambda parameters work the same way
- [[12 - Lists]] — `*args` collects into a tuple, not a list
- [[14 - Dictionaries]] — `**kwargs` collects into a dict

---

## → What's Next

Now that you understand how functions receive input, the next step is understanding how Python manages variable visibility and memory scope.

Continue with **[[18 - Scope and Namespaces]]**