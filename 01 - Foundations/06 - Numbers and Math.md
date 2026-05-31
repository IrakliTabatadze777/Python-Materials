---
tags:
  - foundations
  - python
  - numbers
  - math
  - arithmetic
stage: 1
difficulty: Beginner
---

# Numbers and Math in Python

**Prev:** [[05 - Encoding]] | **Next:** [[]]

> Numbers are one of the most fundamental data types in Python. They are used everywhere — from simple calculations to data science, web development, and machine learning.

---

## Types of Numbers in Python

Python supports several built-in numeric types:

### 1. Integers (`int`)

Whole numbers without a decimal point.

```python
x = 10
y = -5
z = 0

print(type(x))   # <class 'int'>
````

### 2. Floating Point Numbers (`float`)

Numbers with decimal points.

```python
a = 3.14
b = -0.5
c = 2.0

print(type(a))   # <class 'float'>
```

### 3. Complex Numbers (`complex`)

Numbers with real and imaginary parts.

```python
z = 2 + 3j

print(z.real)    # 2.0
print(z.imag)    # 3.0
```

---

## Basic Arithmetic Operations

Python supports standard mathematical operations.

```python
a = 10
b = 3

print(a + b)   # Addition -> 13
print(a - b)   # Subtraction -> 7
print(a * b)   # Multiplication -> 30
print(a / b)   # Division -> 3.333...
```

---

## Floor Division and Modulus

### Floor Division (`//`)

Removes the decimal part and returns the integer result.

```python
print(10 // 3)   # 3
print(7 // 2)    # 3
```

### Modulus (`%`)

Returns the remainder of division.

```python
print(10 % 3)    # 1
print(7 % 2)     # 1
```

**Use cases:**

* Checking even/odd numbers
* Splitting tasks evenly
* Cyclic patterns (like days of the week)

```python
print(10 % 2 == 0)   # True → even number
```

---

## Exponentiation

Raising a number to a power.

```python
print(2 ** 3)   # 8
print(5 ** 2)   # 25
print(9 ** 0.5) # square root of 9 -> 3.0
```

---

## Operator Precedence

Python follows mathematical order of operations:

1. Parentheses `()`
2. Exponents `**`
3. Multiplication / Division `* / // %`
4. Addition / Subtraction `+ -`

```python
result = 2 + 3 * 4
print(result)   # 14 (not 20)

result = (2 + 3) * 4
print(result)   # 20
```

---

## Built-in Math Functions

Python provides useful built-in functions for numbers.

```python
print(abs(-10))        # 10
print(round(3.1415))   # 3
print(round(3.1415, 2))# 3.14
print(pow(2, 3))       # 8
```

---

## The `math` Module

For advanced mathematical operations, Python provides the `math` module.

```python
import math
```

### Common Functions

```python
print(math.sqrt(16))     # 4.0
print(math.ceil(3.2))    # 4
print(math.floor(3.9))   # 3
print(math.factorial(5)) # 120
```

### Constants

```python
print(math.pi)   # 3.141592653589793
print(math.e)    # 2.718281828459045
```

---

## Type Conversion

You can convert between numeric types.

```python
print(int(3.9))     # 3
print(float(5))     # 5.0
print(float("10"))  # 10.0
```

**Be careful:**

```python
print(int("10.5"))  # Error!
```

---

## Working with User Input

User input is always a string, so conversion is required.

```python
age = input("Enter your age: ")
age = int(age)

print(age + 5)
```

---

## Common Pitfalls

### 1. Integer Division Confusion

```python
print(5 / 2)   # 2.5
print(5 // 2)  # 2
```

### 2. Float Precision Issues

```python
print(0.1 + 0.2)  # 0.30000000000000004
```

### 3. String vs Number

```python
print("10" + "5")   # "105"
print(10 + 5)       # 15
```

---

## Practical Examples

### Even or Odd Check

```python
num = 7

if num % 2 == 0:
    print("Even")
else:
    print("Odd")
```

### Simple Calculator

```python
a = 10
b = 5

print(a + b)
print(a - b)
print(a * b)
print(a / b)
```

---

## Best Practices

* Use meaningful variable names (`total_price` instead of `x`)
* Prefer `math` module for advanced calculations
* Always convert input data explicitly
* Be aware of integer vs float behavior
* Use parentheses to make expressions clearer

---

## Summary

* Python supports `int`, `float`, and `complex` numbers
* Basic operations include `+ - * / // % **`
* Use `math` module for advanced calculations
* Be careful with type conversion and precision issues

---

Continue with **[[]]**
