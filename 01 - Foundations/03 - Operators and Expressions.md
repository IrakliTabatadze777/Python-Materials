---
tags:
  - foundations
  - python
  - operators
  - expressions
stage: 1
difficulty: Beginner
---

# Operators and Expressions in Python

**Prev:** [[02 - Variables and Data Types]] | **Next:** [[04 - Control Flow (if-elif-else)]]

> Operators are symbols that tell Python to perform operations on values and variables. Expressions are combinations of values and operators that produce a result.

---

## What is an Operator?

An **operator** is a symbol that performs an action on one or more values.

### Example:

```python id="op1"
x = 10 + 5
```

Here:

* `+` is an operator
* `10` and `5` are operands
* `10 + 5` is an expression

---

## What is an Expression?

An **expression** is any valid combination of:

* values
* variables
* operators

that produces a result.

### Example:

```python id="op2"
result = (10 + 5) * 2
```

This is an expression because it evaluates to a value.

---

# 1. Arithmetic Operators

Used for mathematical operations.

| Operator | Meaning             | Example        |
| -------- | ------------------- | -------------- |
| `+`      | Addition            | `10 + 5 = 15`  |
| `-`      | Subtraction         | `10 - 5 = 5`   |
| `*`      | Multiplication      | `10 * 5 = 50`  |
| `/`      | Division            | `10 / 5 = 2.0` |
| `//`     | Floor Division      | `10 // 3 = 3`  |
| `%`      | Modulus (remainder) | `10 % 3 = 1`   |
| `**`     | Power               | `2 ** 3 = 8`   |

---

## Examples:

```python id="op3"
print(10 + 5)
print(10 / 3)
print(10 // 3)
print(10 % 3)
print(2 ** 4)
```

---

## Important Notes:

### Division always returns float:

```python id="op4"
print(10 / 2)  # 5.0
```

### Floor division removes decimals:

```python id="op5"
print(10 // 3)  # 3
```

---

# 2. Comparison Operators

Used to compare values. Result is always `True` or `False`.

| Operator | Meaning          |
| -------- | ---------------- |
| `==`     | equal            |
| `!=`     | not equal        |
| `>`      | greater than     |
| `<`      | less than        |
| `>=`     | greater or equal |
| `<=`     | less or equal    |

---

## Examples:

```python id="op6"
print(10 == 10)
print(10 != 5)
print(10 > 5)
print(10 < 5)
```

---

## Key idea:

Comparison operators always return a boolean:

```python id="op7"
result = 10 > 5
print(result)  # True
```

---

# 3. Logical Operators

Used to combine conditions.

| Operator | Meaning              |
| -------- | -------------------- |
| `and`    | both must be True    |
| `or`     | at least one is True |
| `not`    | reverses value       |

### Truth table for `and` and `or`

| A     | B     | A `and` B | A `or` B |
| ----- | ----- | --------- | -------- |
| True  | True  | True      | True     |
| True  | False | False     | True     |
| False | True  | False     | True     |
| False | False | False     | False    |

`and` returns `True` only when **both** operands are `True`.  
`or` returns `True` when **at least one** operand is `True`.

---

## Examples:

### AND

```python id="op8"
print(True and True)   # True
print(True and False)  # False
```

---

### OR

```python id="op9"
print(True or False)   # True
print(False or False)  # False
```

---

### NOT

```python id="op10"
print(not True)   # False
print(not False)  # True
```

---

## Real-world example:

```python id="op11"
age = 20
has_id = True

print(age >= 18 and has_id)
```

---

# 4. Assignment Operators

Used to assign and update values.

| Operator | Example  | Equivalent  |
| -------- | -------- | ----------- |
| `=`      | `x = 5`  | assign      |
| `+=`     | `x += 5` | `x = x + 5` |
| `-=`     | `x -= 5` | `x = x - 5` |
| `*=`     | `x *= 5` | `x = x * 5` |
| `/=`     | `x /= 5` | `x = x / 5` |

---

## Examples:

```python id="op12"
x = 10
x += 5
print(x)
```

---

```python id="op13"
x = 10
x *= 2
print(x)
```

---

# 5. Identity Operators

Used to check if two variables refer to the same object in memory.

| Operator | Meaning          |
| -------- | ---------------- |
| `is`     | same object      |
| `is not` | different object |

---

## Example:

```python id="op14"
a = [1, 2, 3]
b = a

print(a is b)  # True
```

---

## Different objects:

```python id="op15"
a = [1, 2, 3]
b = [1, 2, 3]

print(a == b)  # True (values same)
print(a is b)  # False (different objects)
```

---

# 6. Membership Operators

Used to check if a value exists inside a collection.

| Operator | Meaning        |
| -------- | -------------- |
| `in`     | exists         |
| `not in` | does not exist |

---

## Examples:

```python id="op16"
text = "Python"

print("P" in text)
print("x" in text)
```

---

```python id="op17"
numbers = [1, 2, 3, 4]

print(3 in numbers)
print(10 in numbers)
```

---

# 7. Expressions (Deep Understanding)

An expression is a combination of values and operators that produces a result.

---

## Example:

```python id="op18"
result = (10 + 5) * 2 - 3
```

Breakdown:

* `10 + 5` → 15
* `15 * 2` → 30
* `30 - 3` → 27

---

## Operator Precedence (Order of execution)

Python follows priority rules:

1. `()`
2. `**`
3. `* / // %`
4. `+ -`
5. comparisons
6. logical operators

---

## Example:

```python id="op19"
print(2 + 3 * 4)   # 14, not 20
```

---

## With parentheses:

```python id="op20"
print((2 + 3) * 4)  # 20
```

---

# 8. Summary

* Operators perform actions on values
* Expressions produce results
* Python has arithmetic, comparison, logical, assignment, identity, and membership operators
* `is` checks memory identity, `==` checks value equality
* Operator precedence determines evaluation order

```python id="op21"
print(10 + 5 * 2)
print((10 + 5) * 2)
```

---

Continue with **[[04 - Control Flow (if-elif-else)]]**

---
