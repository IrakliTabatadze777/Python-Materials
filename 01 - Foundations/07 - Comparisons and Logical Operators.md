---
tags:
  - foundations
  - python
  - operators
  - comparisons
  - logical
  - truthiness
stage: 1
difficulty: Beginner
---

# Comparisons and Logical Operators

**Prev:** [[06 - Operators and Expressions]] | **Next:** [[08 - Control Flow (if-elif-else)]]

> Comparison operators ask questions about values and return `True` or `False`. Logical operators combine those answers. Identity and membership operators ask different questions — "same object?" and "contained inside?" — and truthiness lets Python treat everyday values as yes/no in conditions.

---

## Why this matters

Every `if`, `while`, and filter depends on expressions that evaluate to true or false. `if user.is_active and user.age >= 18` combines comparison (`>=`), logical (`and`), and attribute access. Mixing up `==` and `is`, or assuming `and` always returns `True`/`False`, causes subtle bugs that pass review but fail in production.

This note covers **comparison**, **logical**, **identity**, and **membership** operators, plus **short-circuit evaluation** and **truthiness** — the decision-making layer of Python.

---

## Comparison operators

Comparison operators compare two values and return a **boolean** (`True` or `False`).

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| `==` | equal to | `10 == 10` | `True` |
| `!=` | not equal to | `10 != 5` | `True` |
| `>` | greater than | `8 > 3` | `True` |
| `<` | less than | `2 < 1` | `False` |
| `>=` | greater or equal | `5 >= 5` | `True` |
| `<=` | less or equal | `4 <= 10` | `True` |

Every comparison is an **expression** — it produces a value you can store or combine:

```python
print(10 == 10)    # True
print(10 != 5)     # True
print(8 > 3)       # True
print(2 < 1)       # False
print(5 >= 5)      # True
print(4 <= 10)     # True

result = (age >= 18)
print(result)      # True or False depending on age
```

---

### `==` vs `=` — do not confuse them

| | `=` | `==` |
|---|-----|------|
| **Purpose** | Assignment — store a value | Comparison — ask if values are equal |
| **Returns** | Nothing useful (statement) | `True` or `False` |
| **Example** | `x = 10` | `x == 10` |

```python
x = 10
print(x == 10)     # True  — question
# x = 10           # statement — already assigned above
```

---

### What `==` actually compares

`==` checks **value equality** — do the two objects represent the same value?

```python
print(10 == 10)        # True
print(10 == 10.0)      # True  — int and float, same numeric value
print("hi" == "hi")    # True
print([1, 2] == [1, 2])  # True  — same contents, different objects
```

For custom classes, `==` can be overridden with `__eq__`. For built-in types, Python knows how to compare their values.

---

### Ordering comparisons (`>`, `<`, `>=`, `<=`)

Work on numbers, strings, and other orderable types:

```python
print(17 > 10)         # True
print(3.5 < 4.0)       # True
print("apple" < "banana")   # True  — lexicographic (alphabetical) order
print("Z" < "a")       # True  — uppercase letters sort before lowercase in ASCII
```

**Strings compare character by character** using their underlying code points — not always intuitive for human sorting, but consistent.

**Mixed types** usually raise `TypeError`:

```python
# print(5 < "10")      # TypeError: '<' not supported between 'int' and 'str'
```

---

### Chained comparisons

Python allows mathematical-style chaining — and it is **not** the same as nesting:

```python
x = 7
print(3 < x < 10)      # True  — same as (3 < x) and (x < 10)
print(1 < x < 5)       # False — x is not less than 5
```

```python
# Wrong mental model:
# (3 < x) < 10  →  (True) < 10  →  TypeError in Python 3
```

Each pair is evaluated: `3 < 7` is `True`, `7 < 10` is `True`, both must hold.

Common pattern:

```python
score = 85
print(60 <= score <= 100)   # True — score in valid range
```

---

## Logical operators

Logical operators combine conditions. They are `and`, `or`, and `not`.

| Operator | Meaning | Example |
|----------|---------|---------|
| `and` | both must be truthy | `age >= 18 and has_ticket` |
| `or` | at least one truthy | `is_admin or is_moderator` |
| `not` | reverse truthiness | `not is_blocked` |

---

### Truth table for `and` and `or`

Using actual booleans:

| A | B | A `and` B | A `or` B |
|---|---|-----------|----------|
| `True` | `True` | `True` | `True` |
| `True` | `False` | `False` | `True` |
| `False` | `True` | `False` | `True` |
| `False` | `False` | `False` | `False` |

`and` returns `True` only when **both** are truthy.  
`or` returns `True` when **at least one** is truthy.

---

### `not`

`not` inverts truthiness:

```python
print(not True)        # False
print(not False)       # True
print(not 0)           # True  — 0 is falsy
print(not "")          # True  — empty string is falsy
print(not "hello")     # False — non-empty string is truthy
```

`not` has **high precedence** — bind it tightly to one operand:

```python
print(not True and False)   # False — (not True) and False
print(not (True and False)) # True
```

---

### Combining comparisons and logic

```python
age = 25
has_license = True

if age >= 18 and has_license:
    print("Can drive")     # Can drive

score = 55
if score >= 90:
    grade = "A"
elif score >= 80 and score < 90:
    grade = "B"
else:
    grade = "other"
```

De Morgan's laws (useful for refactoring conditions):

```python
# not (A and B)  ==  (not A) or (not B)
# not (A or B)   ==  (not A) and (not B)

print(not (True and False))   # True
print((not True) or (not False))  # True
```

---

## Identity operators: `is` and `is not`

Identity operators check whether two names refer to the **same object in memory** — not whether their values look the same.

| Operator | Meaning |
|----------|---------|
| `is` | same object |
| `is not` | different objects |

```python
a = [1, 2, 3]
b = a
c = [1, 2, 3]

print(a is b)      # True  — same list object
print(a is c)      # False — different objects, same contents
print(a == c)      # True  — same values
print(a is not c)  # True
```

---

### `==` vs `is` — the rule

| Question | Use |
|----------|-----|
| "Do these have the same **value**?" | `==` |
| "Are these the **exact same object**?" | `is` |

```python
x = 256
y = 256
print(x == y)      # True
print(x is y)      # True for small ints (CPython caches -5..256)

a = 1000
b = 1000
print(a == b)      # True
print(a is b)      # False — different int objects (outside cache)
```

**Never use `is` to compare values** except for singletons where identity *is* the meaning:

```python
# Correct uses of `is`
if value is None:
    ...

if flag is True:   # rarely needed — prefer `if flag:`
    ...
```

```python
# Wrong
name = "hello"
other = "hello"
# if name is other:   # unreliable for strings — use ==
if name == other:    # correct
    ...
```

---

### Why identity matters

When you mutate a shared object, `==` can still hold for copies but `is` reveals sharing:

```python
original = [1, 2, 3]
alias = original
alias.append(4)

print(original == alias)   # True  — same contents
print(original is alias)   # True  — same object
print(original)            # [1, 2, 3, 4]
```

---

## Membership operators: `in` and `not in`

Membership tests whether a value is **contained inside** a collection (or substring in a string).

| Operator | Meaning |
|----------|---------|
| `in` | value is present |
| `not in` | value is absent |

Returns `True` or `False`.

---

### Strings — substring search

```python
text = "Python"

print("P" in text)       # True
print("thon" in text)    # True
print("Java" in text)    # False
print("x" not in text)   # True
```

Membership is case-sensitive: `"python" in "Python"` is `False`.

---

### Lists, tuples, sets

```python
numbers = [10, 20, 30]

print(20 in numbers)     # True
print(50 in numbers)     # False
print(50 not in numbers) # True
```

Works the same for tuples and sets. For sets, membership is O(1) on average — very fast lookups.

---

### Dictionaries — keys only

`in` on a dict checks **keys**, not values:

```python
user = {"name": "Ada", "age": 36}

print("name" in user)    # True  — key exists
print("Ada" in user)     # False — "Ada" is a value, not a key
print("age" not in user) # False
```

Check values explicitly:

```python
print("Ada" in user.values())   # True
```

---

### Practical patterns

```python
allowed = {"GET", "POST", "PUT"}
method = "POST"

if method in allowed:
    print("OK")            # OK

if "@" not in email:
    print("Invalid email")
```

---

## Short-circuit evaluation

Python's `and` and `or` **stop early** when the result is already determined. They do not always evaluate both operands.

### `and` — stop at the first falsy value

```python
print(False and expensive_function())  # False — right side never called
print(0 and 99)                        # 0
print(5 and 0)                         # 0
print(3 and 4)                         # 4
```

Evaluation order:

1. Evaluate left operand.
2. If it is **falsy**, return it immediately — **right side is skipped**.
3. If it is **truthy**, evaluate and return the right operand.

### `or` — stop at the first truthy value

```python
print(True or expensive_function())    # True — right side never called
print(0 or 99)                         # 99
print(5 or 99)           # 5
print("" or "default")                 # default
```

Evaluation order:

1. Evaluate left operand.
2. If it is **truthy**, return it immediately — **right side is skipped**.
3. If it is **falsy**, evaluate and return the right operand.

---

### `and` / `or` return values, not always `bool`

This surprises many beginners. Logical operators return **one of the operands**, not necessarily `True`/`False`:

```python
print(0 and 5)         # 0   — first falsy
print(3 and 5)         # 5   — both truthy, returns last
print(0 or 5)          # 5   — first falsy, returns second
print("hello" or "default")  # hello — first truthy
```

Common idioms built on this:

```python
name = "" or "Guest"
print(name)            # Guest

count = 0
count = count or 1     # use 1 if count is falsy (0, None, "")
print(count)           # 1
```

**Guard pattern** — only call something if safe:

```python
user = None
# user.get_name()           # AttributeError if user is None
name = user and user.get_name()   # short-circuits — no call if user is falsy
```

**Side-effect reliance** (use carefully):

```python
items = []
# items[0]                  # IndexError
if items and items[0] == "x":
    print("found")          # items checked first — safe
```

---

### Short-circuit summary

| Operator | Stops when | Returns |
|----------|------------|---------|
| `and` | Left is falsy | That falsy value |
| `and` | Left is truthy | Right operand (evaluated) |
| `or` | Left is truthy | That truthy value |
| `or` | Left is falsy | Right operand (evaluated) |

---

## Truthiness

Python can treat **any value** as `True` or `False` in conditions (`if`, `while`, `and`, `or`, `not`). You do not always need an explicit comparison.

```python
if name:
    print(f"Hello, {name}")
```

This runs when `name` is **truthy** — not necessarily the boolean `True`.

Use `bool()` to see the truth value explicitly:

```python
print(bool(1))         # True
print(bool(0))         # False
print(bool("hi"))      # True
print(bool(""))        # False
```

---

### Falsy values — evaluate as `False`

These are **falsy** in Python:

| Value | Example |
|-------|---------|
| Boolean false | `False` |
| None | `None` |
| Zero (int) | `0` |
| Zero (float) | `0.0` |
| Empty string | `""` |
| Empty list | `[]` |
| Empty tuple | `()` |
| Empty dict | `{}` |
| Empty set | `set()` |

```python
values = [False, None, 0, 0.0, "", [], {}, set(), ()]

for v in values:
    print(v, "→", bool(v))
# all → False
```

---

### Truthy values — everything else

If it is not falsy, it is **truthy**:

```python
print(bool(1))           # True
print(bool(-5))          # True  — any non-zero number
print(bool("hello"))     # True  — non-empty string
print(bool([0]))         # True  — list with items (even if item is 0)
print(bool([" "]))       # True  — whitespace string is not empty
print(bool("False"))     # True  — string "False" is not boolean False!
```

**Common trap:** non-empty containers are truthy even when their content is "empty" in meaning:

```python
data = [0, 0, 0]
if data:
    print("Has data")    # Has data — list is not empty
```

---

### Truthiness in conditions

```python
name = input("Name: ")   # user presses Enter → ""

if name:
    print(f"Hello, {name}!")
else:
    print("You didn't enter a name.")
```

```python
items = []
if not items:
    print("List is empty")   # List is empty
```

Prefer **explicit comparisons** when the distinction matters:

```python
# Ambiguous — is count missing or zero?
if count:
    ...

# Clear
if count is not None:
    ...

if count > 0:
    ...
```

---

### Falsy is not the same as `== False`

Different falsy values are not interchangeable — and falsy does not mean `== False`:

```python
print(None == False)     # False
print("" == False)       # False
print([] == False)       # False
print(None is False)     # False

print(0 == False)        # True   — quirk: bool is a subclass of int
print(0 is False)        # False  — different objects
```

`0 == False` is `True` (historical quirk — `bool` inherits from `int`), but **checking `is False` or `== False` is poor style**. Prefer truthiness or explicit comparisons:

```python
if not is_active:       # good
if is_active is False:  # only when you must distinguish False from None
```

See [[05 - Boolean and None]] for `None`, `False`, and `True` in depth.

---

## Operator precedence (this note)

When mixing comparison and logical operators:

| Precedence (high → low) | Operators |
|-------------------------|-----------|
| Comparison | `==`, `!=`, `<`, `<=`, `>`, `>=` |
| Logical NOT | `not` |
| Logical AND | `and` |
| Logical OR | `or` |

```python
print(not False and True)     # True  — not first, then and
print(True or False and False)  # True  — and before or
# same as: True or (False and False)

print(5 > 3 and 10 < 20)      # True
```

Use parentheses when readability matters:

```python
if (age >= 18 and age <= 65) or is_veteran:
    ...
```

---

## Common mistakes / Gotchas

**Using `is` instead of `==` for value comparison.**

```python
a = [1, 2]
b = [1, 2]
# if a is b:   # almost always False for lists
if a == b:     # correct
```

**Using `=` inside a condition.**

```python
# if x = 5:     # SyntaxError
if x == 5:      # correct
```

**Assuming `and`/`or` always return `True`/`False`.** They return operands — see short-circuit section.

**Relying on truthiness when zero or empty string is valid data.**

```python
quantity = 0
if quantity:           # skips block — 0 is falsy
    ship_order()
# use: if quantity is not None and quantity >= 0
```

**Checking dict membership for values with `in` alone** — `in` tests keys.

**Forgetting string case in membership:** `"py" in "Python"` is `False`.

---

## See also

- [[05 - Boolean and None]] — `True`, `False`, `None`, and truthiness foundations
- [[06 - Operators and Expressions]] — arithmetic, assignment, and precedence
- [[02 - Variables and Data Types]] — identity (`is`) vs equality (`==`) for objects
- [[08 - Control Flow (if-elif-else)]] — using conditions in `if`, `elif`, and `else`
- [Python docs — Comparisons](https://docs.python.org/3/reference/expressions.html#comparisons)
- [Python docs — Boolean operations](https://docs.python.org/3/reference/expressions.html#boolean-operations)

---

## → What's next

You can compare values and build compound conditions. The natural next step is putting those boolean expressions to work — choosing different code paths with `if`, `elif`, and `else` — in [[08 - Control Flow (if-elif-else)]].
