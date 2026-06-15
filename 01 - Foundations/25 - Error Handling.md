---
tags:
  - foundations
  - python
  - error-handling
stage: 1
difficulty: Beginner
---

# Error Handling in Python

**Prev:** [[24 - Virtual Environments and pip]] | **Next:** [[26 - File I-O]]

> In this lesson you will learn how to handle errors gracefully using **try-except** blocks, understand Python’s exception hierarchy, prevent program crashes, and write robust, professional code. Error handling is what turns fragile scripts into reliable applications.

---

## What Are Errors (Exceptions) in Python?

When Python encounters a problem it cannot solve during execution, it **raises an exception**. This is Python’s way of saying “Something went wrong.” If not handled, the program stops and shows a traceback.

### Common Examples

```python
print(10 / 0)                    # ZeroDivisionError
int("abc")                       # ValueError
open("missing.txt")              # FileNotFoundError
numbers = [1, 2, 3]
print(numbers[10])               # IndexError
```

---

## Python Exception Hierarchy

All exceptions in Python follow a class inheritance hierarchy. Understanding this helps you catch errors at the right level of specificity.

### The Hierarchy (Simplified Tree)

```
BaseException
    ├── SystemExit
    ├── KeyboardInterrupt
    ├── GeneratorExit
    └── Exception
            ├── ArithmeticError
            │     ├── ZeroDivisionError
            │     ├── OverflowError
            │     └── FloatingPointError
            ├── LookupError
            │     ├── IndexError
            │     └── KeyError
            ├── ValueError
            ├── TypeError
            ├── FileNotFoundError
            ├── PermissionError
            ├── ImportError
            ├── ModuleNotFoundError
            ├── NameError
            ├── AttributeError
            ├── OSError
            └── ... (many more)
```

**Key Points:**

- **`BaseException`** — Root of all exceptions. You should **almost never** catch this directly.
- **`Exception`** — Base class for **most** errors that your code should handle. This is what you usually catch.
- Specific exceptions (like `ZeroDivisionError`) inherit from broader categories (like `ArithmeticError` → `Exception`).

**Practical implication:**
```python
try:
    risky_code()
except Exception as e:        # Catches almost everything (recommended)
    print(f"Error: {e}")

# Too broad (avoid unless necessary)
try:
    risky_code()
except BaseException:         # Catches SystemExit, KeyboardInterrupt too
    pass
```

**Best practice:** Catch the most specific exception first, then broader ones.

---

## The Foundation: try and except

### What is `try`?
The `try` block contains code that **might raise an exception**. Python tries to execute it normally.

### What is `except`?
The `except` block runs only if an exception occurs in the `try` block. It lets you handle the error gracefully.

### Basic Syntax

```python
try:
    # Potentially dangerous code
    result = some_risky_operation()
except SpecificErrorType:
    # Handle this specific problem
    print("Handled the error gracefully")
```

---

### Simple Example

```python
try:
    number = int(input("Enter a number: "))
    result = 100 / number
    print(f"100 / {number} = {result}")
except ZeroDivisionError:
    print("❌ Cannot divide by zero!")
except ValueError:
    print("❌ Please enter a valid number!")
except Exception as e:          # Fallback for any other error
    print(f"❌ Unexpected error: {type(e).__name__} - {e}")
```

---

## Adding `else` and `finally`

### `else` — Runs only if no exception occurred

```python
try:
    number = int(input("Enter a number: "))
    result = 100 / number
except ZeroDivisionError:
    print("Cannot divide by zero!")
except ValueError:
    print("Invalid input!")
else:
    # This runs ONLY if try block succeeded completely
    print(f"✅ Success! Result = {result}")
    # You can safely continue with result here
```

### `finally` — Always runs (for cleanup)

```python
try:
    file = open("data.txt", "r")
    content = file.read()
    print(content)
except FileNotFoundError:
    print("File not found!")
finally:
    # This ALWAYS executes — even if error occurred or return was used
    print("🧹 Cleanup: Closing resources...")
    if 'file' in locals() and not file.closed:
        file.close()
```

**Real use cases for `finally`**: Closing files, releasing database connections, unlocking resources.

---

## Complete Practical Example

```python
def safe_divide(a, b):
    """Safely divides two numbers with full error handling."""
    print(f"🔄 Trying to divide {a} by {b}...")
    
    try:
        if not isinstance(a, (int, float)) or not isinstance(b, (int, float)):
            raise TypeError("Both a and b must be numbers!")
        
        result = a / b
    except ZeroDivisionError:
        print("❌ Error: Division by zero!")
        return None
    except TypeError as e:
        print(f"❌ Type Error: {e}")
        return None
    except Exception as e:          # Broad catch
        print(f"❌ Unexpected {type(e).__name__}: {e}")
        return None
    else:
        print("✅ Division successful!")
        return result
    finally:
        print("🧹 Operation finished.")


print(safe_divide(20, 5))
print(safe_divide(10, 0))
print(safe_divide(5, "hello"))
```

---

## Raising Exceptions Yourself

```python
def set_age(age):
    if age < 0:
        raise ValueError(f"Age cannot be negative! Got: {age}")
    if age > 150:
        raise ValueError(f"{age} is not a realistic age!")
    print(f"Age set to {age}")


try:
    set_age(-5)
except ValueError as e:
    print(f"Validation Error: {e}") # Age cannot be negative! Got: -5
```

---

## Custom Exceptions

```python
class NegativeAgeError(ValueError):
    """Custom exception for negative age validation."""
    pass


class InsufficientFundsError(Exception):
    """Raised when trying to withdraw more than available balance."""
    pass


def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError(f"Insufficient funds. Balance: {balance}")
    return balance - amount
```

---

## Best Practices

- Catch **specific** exceptions before general ones
- Avoid bare `except:` — it hides bugs
- Provide clear, helpful error messages
- Use `finally` for cleanup
- Use `else` for code that should run only on success
- Raise meaningful exceptions with good messages
- Create custom exceptions when it improves code clarity

---

## Summary Table

| Keyword      | Purpose                                      | Runs When                     |
|--------------|----------------------------------------------|-------------------------------|
| `try`        | Wrap code that might fail                    | Always                        |
| `except`     | Handle specific or general exceptions        | When matching error occurs    |
| `else`       | Code that runs on success                    | Only if no exception          |
| `finally`    | Cleanup code                                 | Always                        |
| `raise`      | Manually raise an exception                  | When you want to signal error |

---

## Key Takeaways

- Python has a clear **exception hierarchy** — understand it to catch errors effectively
- Use `try-except` to prevent crashes and handle problems gracefully
- Be specific with exceptions, use `finally` for cleanup
- Raise exceptions to enforce rules
- Good error handling makes your programs professional and user-friendly

---

## See also

- [[08 - Control Flow (if-elif-else)]] — `if` conditions before risky operations
- [[26 - File I-O]] — file operations raise `FileNotFoundError`, `PermissionError`
- [[16 - Defining Functions]] — functions can raise exceptions with `raise`
- [[05 - Boolean and None]] — `None` as a safe return when errors occur
- [Python docs — Exceptions](https://docs.python.org/3/library/exceptions.html)

---

## → What's Next

Now that you understand how to handle errors gracefully and Python’s exception hierarchy, it's time to learn **File I/O** — how to read from and write to files on your computer.

Continue with **[[26 - File I-O]]**
