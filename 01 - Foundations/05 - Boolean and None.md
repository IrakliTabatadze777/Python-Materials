---
tags:
  - foundations
  - python
  - booleans
  - none
  - truthiness
stage: 1
difficulty: Beginner
---

# Boolean and None in Python

**Prev:** [[04 - Numbers and Math]] | **Next:** [[06 - Operators and Expressions]]

> Booleans and `None` are small but extremely important parts of Python. They power decision-making, conditionals, loops, comparisons, and many internal behaviors of the language.

---

## What is a Boolean?

A **Boolean** is a data type that can have only one of two values:

- `True`
- `False`

Notice that both start with a capital letter.

```python
is_logged_in = True
is_admin = False

print(type(is_logged_in))
```

Output:

```python
<class 'bool'>
```

---

## Why Booleans Matter

Computers constantly make decisions:

- Is the user authenticated?
- Is the password correct?
- Is the number positive?
- Is the file found?

The answer to all these questions is either:

```python
True
```

or

```python
False
```

---

## Creating Boolean Values

You can assign them directly:

```python
x = True
y = False
```

Or generate them from comparisons:

```python
print(5 > 3)
print(5 < 3)
print(10 == 10)
```

Output:

```python
True
False
True
```

---

## Comparison Operators

Comparison operators produce Boolean values.

| Operator | Meaning |
|-----------|----------|
| `==` | Equal to |
| `!=` | Not equal to |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal |
| `<=` | Less than or equal |

Examples:

```python
print(10 == 10)
print(10 != 5)
print(8 > 3)
print(2 < 1)
print(5 >= 5)
print(4 <= 10)
```

Output:

```python
True
True
True
False
True
True
```

---

## Equality vs Assignment

One of the most common beginner mistakes:

```python
x = 5
```

This is **assignment**.

```python
x == 5
```

This is **comparison**.

Example:

```python
x = 5

print(x == 5)
```

Output:

```python
True
```

---

## Boolean Operators

Python allows combining multiple Boolean values.

### `and`

Returns `True` only if both sides are true.

```python
print(True and True)
print(True and False)
```

Output:

```python
True
False
```

Example:

```python
age = 20
has_ticket = True

print(age >= 18 and has_ticket)
```

Output:

```python
True
```

---

### `or`

Returns `True` if at least one side is true.

```python
print(True or False)
print(False or False)
```

Output:

```python
True
False
```

Example:

```python
is_admin = False
is_moderator = True

print(is_admin or is_moderator)
```

Output:

```python
True
```

---

### `not`

Reverses a Boolean value.

```python
print(not True)
print(not False)
```

Output:

```python
False
True
```

Example:

```python
is_banned = False

print(not is_banned)
```

Output:

```python
True
```

---

## Combining Conditions

```python
age = 25
has_license = True

can_drive = age >= 18 and has_license

print(can_drive)
```

Output:

```python
True
```

---

## Order of Boolean Operations

Python evaluates:

1. `not`
2. `and`
3. `or`

Example:

```python
print(True or False and False)
```

Python sees:

```python
True or (False and False)
```

Result:

```python
True
```

Use parentheses when expressions become complicated.

```python
print((True or False) and False)
```

Result:

```python
False
```

---

# Truthy and Falsy Values

One of Python's most useful features is that many values can behave like Booleans.

Python automatically treats some values as:

- Truthy (`True`)
- Falsy (`False`)

---

## Falsy Values

The following values evaluate to `False`:

```python
False
None
0
0.0
0j
''
""
[]
()
{}
set()
range(0)
```

Examples:

```python
print(bool(0))
print(bool(""))
print(bool([]))
```

Output:

```python
False
False
False
```

---

## Truthy Values

Almost everything else is truthy.

Examples:

```python
print(bool(1))
print(bool("Hello"))
print(bool([1, 2, 3]))
```

Output:

```python
True
True
True
```

---

## The `bool()` Function

You can explicitly convert values into Booleans.

```python
print(bool(1))
print(bool(0))
print(bool("Python"))
print(bool(""))
```

Output:

```python
True
False
True
False
```

---

## Why Truthiness Exists

Instead of writing:

```python
if len(my_list) > 0:
    print("List has items")
```

Python allows:

```python
if my_list:
    print("List has items")
```

This is shorter and more readable.

---

# What is None?

`None` is a special object in Python representing the absence of a value.

Think of it as:

- No value
- Empty value
- Not yet assigned
- Missing data

---

## Creating None

```python
x = None

print(x)
```

Output:

```python
None
```

---

## Type of None

```python
print(type(None))
```

Output:

```python
<class 'NoneType'>
```

There is only one `None` object in Python.

---

## Why None Exists

Imagine a function that sometimes cannot return a result.

```python
def find_user(user_id):
    return None
```

`None` tells us:

> "No user was found."

---

## Common Use Cases for None

### Placeholder Value

```python
name = None
```

Later:

```python
name = "Giorgi"
```

---

### Default Function Arguments

```python
def greet(name=None):
    if name is None:
        print("Hello, Guest")
    else:
        print(f"Hello, {name}")
```

Usage:

```python
greet()
greet("Giorgi")
```

Output:

```python
Hello, Guest
Hello, Irakli
```

---

### Missing Data

```python
user_email = None
```

Meaning:

> The email is currently unknown.

---

## Checking for None

### Correct Way

Use `is`.

```python
x = None

print(x is None)
```

Output:

```python
True
```

---

### Incorrect Way

Avoid:

```python
x == None
```

While it works, Python style guidelines recommend:

```python
x is None
```

---

## Why Use `is`?

`is` checks whether two variables refer to the exact same object.

```python
x = None

print(x is None)
```

Output:

```python
True
```

Since Python has only one `None` object, identity comparison is ideal.

---

## Difference Between None and False

Many beginners think they are the same.

They are not.

```python
print(None == False)
```

Output:

```python
False
```

Example:

```python
value = None

print(bool(value))
```

Output:

```python
False
```

Even though `None` is falsy, it is not equal to `False`.

---

## Difference Between None and Zero

```python
print(None == 0)
```

Output:

```python
False
```

---

## Difference Between None and Empty String

```python
print(None == "")
```

Output:

```python
False
```

---

## Functions That Return None

Many Python functions return `None`.

Example:

```python
numbers = [3, 1, 2]

result = numbers.sort()

print(result)
```

Output:

```python
None
```

The list is modified in place, so nothing useful is returned.

---

## Short-Circuit Evaluation

Boolean operators can stop early.

### `and`

```python
print(False and 10 / 0)
```

Output:

```python
False
```

Python never evaluates:

```python
10 / 0
```

because the result is already known.

---

### `or`

```python
print(True or 10 / 0)
```

Output:

```python
True
```

Again, Python stops early.

---

## Using Truthy/Falsy with None

A common pattern:

```python
name = None

if name:
    print("Name exists")
else:
    print("No name")
```

Output:

```python
No name
```

However, if you specifically need to know whether the value is `None`, use:

```python
if name is None:
    print("No name")
```

---

## Best Practices

- Always use `True` and `False` (capitalized)
- Use `is None` instead of `== None`
- Understand the difference between falsy values and `False`
- Use truthiness for cleaner code
- Use `None` to represent missing or unknown values
- Keep Boolean expressions simple and readable

---

## Common Mistakes & Gotchas

### 1. Using lowercase booleans

```python
true
false
```

This causes an error.

Correct:

```python
True
False
```

---

### 2. Comparing with None using `==`

```python
if value == None:
```

Prefer:

```python
if value is None:
```

---

### 3. Confusing None with False

```python
None == False
```

Result:

```python
False
```

---

### 4. Assuming Empty Values Are the Same

These are all different:

```python
None
False
0
""
[]
{}
```

They may all be falsy, but they represent different concepts.

---

## Summary

- `bool` is a data type with two values: `True` and `False`
- Comparisons produce Boolean results
- `and`, `or`, and `not` combine Boolean expressions
- Python uses truthy and falsy values in conditions
- `None` represents the absence of a value
- Always check for `None` using `is None`
- `None`, `False`, `0`, and empty containers are different objects, even though many are falsy

---

## See also

- [[04 - Numbers and Math]] — numeric comparisons that produce `True` or `False`
- [[06 - Operators and Expressions]] — expressions vs statements when combining values
- [[07 - Comparisons and Logical Operators]] — `and`, `or`, `not`, and truthiness tables in depth
- [[08 - Control Flow (if-elif-else)]] — turning boolean results into branching logic

---

## → What's next

You understand booleans, `None`, and truthiness. Next, learn how Python *computes* and *stores* results — arithmetic operators, assignment, precedence, and the difference between expressions and statements — in [[06 - Operators and Expressions]].
