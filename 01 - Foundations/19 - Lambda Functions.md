---
tags:
  - foundations
  - python
  - functions
  - lambda
stage: 1
difficulty: Beginner
---

# Lambda Functions in Python

**Prev:** [[18 - Scope and Namespaces]] | **Next:** [[20 - Recursive Functions]]

> Lambda functions are small, anonymous functions used for simple, one-line operations. They are useful when you need a quick function without formally defining it using `def`.

---

## What is a Lambda Function?

A **lambda function** is a small anonymous function defined using the `lambda` keyword.

Unlike normal functions:
- It has no name (usually)
- It contains only one expression
- It is used for short, simple logic

```python
# normal function
def add(a, b):
    return a + b

# lambda function equivalent
add = lambda a, b: a + b

print(add(2, 3))
````

### Output:

```
5
```

### Explanation:

* `lambda a, b` defines inputs
* `a + b` is the expression returned automatically
* No `return` keyword is needed

---

## Syntax of Lambda Functions

```python
lambda arguments: expression
```

### Example:

```python
square = lambda x: x * x

print(square(5))
```

### Output:

```
25
```

### Explanation:

* `x` is input
* `x * x` is evaluated and returned automatically

---

## Lambda vs Normal Function

### Normal function:

```python
def multiply(a, b):
    return a * b

print(multiply(3, 4))
```

### Lambda equivalent:

```python
multiply = lambda a, b: a * b

print(multiply(3, 4))
```

### Output:

```
12
```

### Explanation:

* Both produce the same result
* Lambda is shorter but less readable for complex logic

---

## Lambda with One Argument

```python
double = lambda x: x * 2

print(double(10))
```

### Output:

```
20
```

### Explanation:

* Takes one input
* Returns doubled value

---

## Lambda with Multiple Arguments

```python
add = lambda a, b, c: a + b + c

print(add(1, 2, 3))
```

### Output:

```
6
```

### Explanation:

* Multiple inputs are supported
* Expression is evaluated and returned

---

## Lambda in Function Calls

Lambda is often used directly without assigning to a variable.

```python
print((lambda x: x + 10)(5))
```

### Output:

```
15
```

### Explanation:

* Lambda is defined and immediately called
* `(5)` is the argument passed instantly

---

## Lambda with `sorted()`

Lambda is commonly used for sorting.

```python
data = ["apple", "banana", "kiwi", "cherry"]

sorted_data = sorted(data, key=lambda x: len(x))

print(sorted_data)
```

### Output:

```
['kiwi', 'apple', 'banana', 'cherry']
```

### Explanation:

* `key=lambda x: len(x)` sorts by string length
* Lambda provides custom sorting logic

---

## Lambda with `map()`

```python
numbers = [1, 2, 3, 4]

result = list(map(lambda x: x * 2, numbers))

print(result)
```

### Output:

```
[2, 4, 6, 8]
```

### Explanation:

* Each element is multiplied by 2
* `map()` applies lambda to all items

---

## Lambda with `filter()`

```python
numbers = [1, 2, 3, 4, 5, 6]

even_numbers = list(filter(lambda x: x % 2 == 0, numbers))

print(even_numbers)
```

### Output:

```
[2, 4, 6]
```

### Explanation:

* Lambda checks if number is even
* `filter()` keeps only True results

---

## Lambda Limitations

Lambda functions have restrictions:

### 1. Only one expression

```python
# ❌ Not allowed in lambda
lambda x: 
    print(x)
    return x * 2
```

### Explanation:

* Lambda cannot contain multiple statements

---

### 2. No complex logic

```python
# ❌ Not readable
lambda x: x * 2 if x > 0 else (x + 2 if x < 0 else 0)
```

### Explanation:

* Possible but not recommended
* Normal function is better here

---

## When to Use Lambda Functions

Use lambda when:

* Function is short
* Logic is simple
* Used once or temporarily

### Good use cases:

* Sorting
* Filtering
* Mapping
* Inline operations

---

## When NOT to Use Lambda

Avoid lambda when:

* Logic is complex
* Multiple steps are needed
* Code readability matters more

```python
# Better as normal function
def calculate_discount(price):
    if price > 100:
        return price * 0.9
    return price
```

---

## Real-World Example

### Sorting list of dictionaries

```python
students = [
    {"name": "A", "grade": 90},
    {"name": "B", "grade": 75},
    {"name": "C", "grade": 85}
]

sorted_students = sorted(students, key=lambda s: s["grade"])

print(sorted_students)
```

### Output:

```
[
    {'name': 'B', 'grade': 75},
    {'name': 'C', 'grade': 85},
    {'name': 'A', 'grade': 90}
]
```

### Explanation:

* Lambda extracts `"grade"` from each dictionary
* Sorting happens based on grade value

---

## Summary

| Feature  | Lambda Function           |
| -------- | ------------------------- |
| Name     | Usually anonymous         |
| Syntax   | `lambda args: expression` |
| Body     | Single expression only    |
| Return   | Automatic                 |
| Use case | Simple, short logic       |

---

## → What's Next

Now that you understand lambda functions, the next step is recursive functions, functions that calls themselves.

Continue with **[[20 - Recursive Functions]]**
