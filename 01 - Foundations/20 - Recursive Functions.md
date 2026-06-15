---
tags:
  - foundations
  - python
  - functions
  - recursion
stage: 1
difficulty: Intermediate
---

# Recursive Functions in Python

**Prev:** [[19 - Lambda Functions]] | **Next:** [[21 - Higher Order Functions]]

> A recursive function is a function that calls itself. Recursion is a powerful technique for solving problems that can be broken into smaller versions of the same problem.

---

## What is Recursion?

**Recursion** happens when a function calls itself during its execution.

```python
def greet():
    print("Hello")
    greet()

greet()
```

### What happens?

1. `greet()` is called.
2. It prints `"Hello"`.
3. It calls `greet()` again.
4. The new call prints `"Hello"`.
5. It calls `greet()` again.
6. This continues forever.

Eventually Python stops the program and raises:

```python
RecursionError: maximum recursion depth exceeded
```

### Important

Every recursive function must have a way to stop itself.

This stopping condition is called the **base case**.

---

## The Two Parts of Every Recursive Function

A recursive function usually contains:

1. **Base Case** → stops recursion
2. **Recursive Case** → calls itself

```python
def example(n):
    # Base case
    if n == 0:
        return

    # Recursive case
    print(n)
    example(n - 1)
```

---

## First Recursive Example

Let's count down from 5 to 1.

```python
def countdown(n):
    if n == 0:
        return

    print(n)
    countdown(n - 1)

countdown(5)
```

### Output

```text
5
4
3
2
1
```

### Step-by-Step Execution

#### First call

```python
countdown(5)
```

- `n` is 5
- not base case
- print 5
- call `countdown(4)`

#### Second call

```python
countdown(4)
```

- print 4
- call `countdown(3)`

#### Third call

```python
countdown(3)
```

- print 3
- call `countdown(2)`

#### Fourth call

```python
countdown(2)
```

- print 2
- call `countdown(1)`

#### Fifth call

```python
countdown(1)
```

- print 1
- call `countdown(0)`

#### Sixth call

```python
countdown(0)
```

Base case reached.

Function returns and recursion stops.

---

## Understanding the Call Stack

Each recursive call is placed onto the **call stack**.

For:

```python
countdown(3)
```

The stack grows like:

```text
countdown(3)
countdown(2)
countdown(1)
countdown(0)
```

When the base case is reached, calls return in reverse order:

```text
countdown(0) returns
countdown(1) returns
countdown(2) returns
countdown(3) returns
```

Think of recursion like stacking plates:

- every call adds a plate
- every return removes a plate

---

## Factorial Using Recursion

Factorial is a classic recursion example.

Mathematically:

```text
5! = 5 × 4 × 3 × 2 × 1
```

Result:

```text
5! = 120
```

### Recursive Definition

```text
n! = n × (n - 1)!
```

Base case:

```text
0! = 1
```

### Code

```python
def factorial(n):
    if n == 0:
        return 1

    return n * factorial(n - 1)

print(factorial(5))
```

### Output

```text
120
```

---

## Step-by-Step Factorial Execution

Let's evaluate:

```python
factorial(5)
```

Python sees:

```python
5 * factorial(4)
```

Now it must calculate:

```python
factorial(4)
```

Which becomes:

```python
4 * factorial(3)
```

Then:

```python
3 * factorial(2)
```

Then:

```python
2 * factorial(1)
```

Then:

```python
1 * factorial(0)
```

Base case:

```python
factorial(0)
```

returns:

```python
1
```

Now Python starts resolving:

```python
1 * 1 = 1
2 * 1 = 2
3 * 2 = 6
4 * 6 = 24
5 * 24 = 120
```

Final result:

```text
120
```

---

## Recursive Sum

Calculate the sum of numbers from 1 to n.

```python
def recursive_sum(n):
    if n == 1:
        return 1

    return n + recursive_sum(n - 1)

print(recursive_sum(5))
```

### Output

```text
15
```

### Explanation

Python computes:

```text
5 + 4 + 3 + 2 + 1
```

Which becomes:

```text
15
```

---

## Recursion with Strings

Print characters one by one.

```python
def print_chars(text):
    if text == "":
        return

    print(text[0])
    print_chars(text[1:])

print_chars("CAT")
```

### Output

```text
C
A
T
```

### Explanation

First call:

```python
print_chars("CAT")
```

prints:

```text
C
```

Calls:

```python
print_chars("AT")
```

prints:

```text
A
```

Calls:

```python
print_chars("T")
```

prints:

```text
T
```

Calls:

```python
print_chars("")
```

Base case reached.

---

## Recursive Fibonacci

The Fibonacci sequence:

```text
0 1 1 2 3 5 8 13 ...
```

Rule:

```text
fib(n) = fib(n-1) + fib(n-2)
```

### Code

```python
def fib(n):
    if n <= 1:
        return n

    return fib(n - 1) + fib(n - 2)

print(fib(6))
```

### Output

```text
8
```

### Explanation

To calculate:

```python
fib(6)
```

Python calculates:

```python
fib(5) + fib(4)
```

Then each of those creates more recursive calls.

This is why naive Fibonacci recursion becomes very slow.

---

## Lambda Recursion

Most recursive functions are written using `def`.

However, recursion can also be done with lambda functions.

### Example: Factorial Lambda

```python
factorial = lambda n: 1 if n == 0 else n * factorial(n - 1)

print(factorial(5))
```

### Output

```text
120
```

### How It Works

The lambda is assigned to the variable:

```python
factorial
```

Inside the lambda:

```python
factorial(n - 1)
```

calls the same lambda again.

Python resolves it exactly like the normal recursive function.

---

## Lambda Recursive Countdown

```python
countdown = lambda n: None if n == 0 else (
    print(n),
    countdown(n - 1)
)

countdown(5)
```

### Output

```text
5
4
3
2
1
```

### Explanation

This works, but it is difficult to read.

For this reason recursive lambdas are rarely used in real projects.

A normal function is usually better.

---

## Why Recursion Can Be Difficult

Recursion requires thinking in two directions:

1. Going deeper into recursive calls
2. Returning back through previous calls

Many beginners try to follow the entire execution at once and become confused.

Instead:

- Focus on the current function call
- Trust that smaller recursive calls work
- Always identify the base case

---

## Recursion vs Loops

### Using a Loop

```python
for i in range(5, 0, -1):
    print(i)
```

### Using Recursion

```python
def countdown(n):
    if n == 0:
        return

    print(n)
    countdown(n - 1)
```

Both produce:

```text
5
4
3
2
1
```

---

## When Should You Use Recursion?

Recursion is useful for:

- Tree traversal
- Directory traversal
- Graph algorithms
- Divide-and-conquer algorithms
- Mathematical definitions (factorial, Fibonacci)

---

## Common Mistakes

### 1. Forgetting the Base Case

```python
def func(n):
    return func(n - 1)
```

Problem:

- Never stops
- Causes `RecursionError`

---

### 2. Base Case Never Reached

```python
def func(n):
    if n == 0:
        return

    func(n + 1)
```

Problem:

- Moving away from base case
- Infinite recursion

---

### 3. Making Recursion Too Complex

```python
def complicated(data):
    ...
```

If a recursive solution becomes difficult to understand, consider using a loop instead.

---

## Best Practices

- Always define a clear base case
- Make recursive calls move toward the base case
- Keep recursive functions simple
- Use recursion when it naturally fits the problem
- Prefer normal functions over recursive lambdas

---

## Summary

| Concept | Description |
|----------|------------|
| Recursion | Function calling itself |
| Base Case | Condition that stops recursion |
| Recursive Case | Part that calls itself |
| Call Stack | Stores active function calls |
| Recursive Lambda | Lambda that calls itself |

---

## See also

- [[16 - Defining Functions]] — base concepts; recursion adds self-calling
- [[08 - Control Flow (if-elif-else)]] — base cases use conditionals
- [[09 - While loop]] — iterative alternatives to recursion
- [[12 - Lists]] — recursive list processing patterns
- [[25 - Error Handling]] — `RecursionError` when base case is missing

---

## → What's Next

Now that you understand recursion, the next step is learning about **Higher-Order Functions** — functions that can receive other functions as arguments or return functions as results.

Continue with **[[21 - Higher Order Functions]]**