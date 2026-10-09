---
tags:
  - intermediate
  - python
  - oop
  - error-handling
stage: 2
difficulty: Intermediate
---

# Custom Exceptions

**Prev:** [[22 - Custom Serialization]] | **Next:** [[24 - Testing with pytest]]

> In this lesson you will learn how to define your own **exception classes** — giving errors in your code specific, meaningful names instead of relying only on generic built-in exceptions like `ValueError` or `Exception`. You'll learn how to build a clear exception hierarchy, attach useful data to errors, and catch failures precisely.

---

## The Problem with Generic Exceptions

Built-in exceptions like `ValueError` and `Exception` work, but they blur together different failure modes that should be handled differently.

```python
def withdraw(balance, amount):
    if amount <= 0:
        raise ValueError("Amount must be positive")
    if amount > balance:
        raise ValueError("Insufficient funds")
    return balance - amount
```

If calling code wants to handle "insufficient funds" differently from "invalid amount," it has no clean way to tell them apart — both raise the exact same `ValueError` type.

```python
try:
    withdraw(100, -50)
except ValueError as e:
    print(f"Something went wrong: {e}")   # Which problem was it, exactly?
```

### Real-Life Analogy

Think of a **hospital triage system**:

- Labeling every patient simply as "sick" tells staff nothing about how urgently or how to treat them
- Specific categories — "fracture," "allergic reaction," "cardiac event" — let the right specialist respond appropriately
- A generic `Exception` is like the label "sick" — technically true, but useless for deciding what to do next

Custom exceptions give your errors that same specificity.

---

## Defining a Custom Exception

A custom exception is just a class that inherits from `Exception` (or one of its built-in subclasses) — this connects directly to [[02 - OOP — Inheritance]].

```python
class InsufficientFundsError(Exception):
    pass


def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError("Not enough funds to complete withdrawal")
    return balance - amount


try:
    withdraw(100, 500)
except InsufficientFundsError as e:
    print(f"Withdrawal failed: {e}")
```

**Output:**
```
Withdrawal failed: Not enough funds to complete withdrawal
```

Even with just `pass` as the body, this is already useful — `InsufficientFundsError` is now a distinct, catchable, self-documenting error type.

---

## Building an Exception Hierarchy

Related errors should share a common base class, so calling code can catch them broadly *or* specifically, depending on what it needs.

```python
class BankError(Exception):
    """Base class for all banking-related errors"""
    pass

class InsufficientFundsError(BankError):
    pass

class InvalidAmountError(BankError):
    pass

class AccountFrozenError(BankError):
    pass


def withdraw(balance, amount, is_frozen):
    if is_frozen:
        raise AccountFrozenError("Cannot withdraw from a frozen account")
    if amount <= 0:
        raise InvalidAmountError("Amount must be positive")
    if amount > balance:
        raise InsufficientFundsError("Not enough funds")
    return balance - amount
```

Now calling code can choose its level of precision:

```python
# Catch a specific error
try:
    withdraw(100, 500, is_frozen=False)
except InsufficientFundsError:
    print("Please deposit more funds")

# Catch any banking error, regardless of specific type
try:
    withdraw(100, -50, is_frozen=False)
except BankError as e:
    print(f"Banking operation failed: {e}")
```

This mirrors the class hierarchies from [[02 - OOP — Inheritance]] and [[03 - OOP — Multiple Inheritance]] — `except BankError` catches `InsufficientFundsError`, `InvalidAmountError`, and `AccountFrozenError` alike, because they all inherit from it.

---

## Attaching Extra Data to Exceptions

A custom exception is a real class — this connects to [[07 - OOP — Dunder Methods]] and [[12 - Dataclasses]] — so it can carry structured data beyond just a message string, using its own `__init__`.

```python
class InsufficientFundsError(Exception):
    def __init__(self, balance, requested_amount):
        self.balance = balance
        self.requested_amount = requested_amount
        self.shortfall = requested_amount - balance
        super().__init__(
            f"Cannot withdraw {requested_amount}: balance is {balance} "
            f"(short by {self.shortfall})"
        )


def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError(balance, amount)
    return balance - amount


try:
    withdraw(100, 250)
except InsufficientFundsError as e:
    print(e)                    # Cannot withdraw 250: balance is 100 (short by 150)
    print(e.shortfall)           # 150 - the calling code can act on this directly
```

Calling `super().__init__(message)` sets up the standard exception message (what `print(e)` shows), while the extra attributes (`e.shortfall`) give calling code structured information to act on — not just text to display.

---

## Catching Multiple Exception Types

```python
try:
    withdraw(100, -50)
except (InvalidAmountError, InsufficientFundsError) as e:
    print(f"Withdrawal rejected: {e}")
except AccountFrozenError:
    print("This account is frozen. Please contact support.")
```

A tuple of exception types in one `except` clause catches any of them; separate `except` clauses let different error types trigger different handling entirely.

---

## Re-Raising and Chaining Exceptions

Sometimes you want to catch a low-level error, add context, and raise a more meaningful one — without losing the original cause.

```python
class ConfigError(Exception):
    pass


def load_config(path):
    try:
        with open(path, encoding="utf-8") as f:
            return f.read()
    except FileNotFoundError as e:
        raise ConfigError(f"Configuration file not found: {path}") from e


try:
    load_config("settings.json")
except ConfigError as e:
    print(e)
    print(e.__cause__)   # The original FileNotFoundError, preserved
```

`raise ... from e` explicitly links the new exception to the original one — this connects to [[15 - Context Managers]] (the `with open(...)` pattern) and [[20 - Encoding]] (explicit UTF-8). The traceback shows both errors, making it clear the `ConfigError` was *caused by* a missing file, not an unrelated bug.

---

## Custom Exceptions with Serialization

This connects directly to [[22 - Custom Serialization]] — a common real-world pattern is raising a specific error when data doesn't match the expected shape during deserialization.

```python
import json

class DeserializationError(Exception):
    def __init__(self, field, reason):
        self.field = field
        self.reason = reason
        super().__init__(f"Failed to deserialize '{field}': {reason}")


def user_from_json(json_string):
    data = json.loads(json_string)
    if "name" not in data:
        raise DeserializationError("name", "field is missing")
    if not isinstance(data.get("age"), int):
        raise DeserializationError("age", "must be an integer")
    return data


try:
    user_from_json('{"name": "Alice", "age": "thirty"}')
except DeserializationError as e:
    print(f"Invalid user data: {e}")   # Invalid user data: Failed to deserialize 'age': must be an integer
```

---

## Real-World Example: A Small API Client

```python
class ApiError(Exception):
    """Base class for all API-related errors"""
    pass

class NotFoundError(ApiError):
    def __init__(self, resource_id):
        self.resource_id = resource_id
        super().__init__(f"Resource '{resource_id}' not found")

class UnauthorizedError(ApiError):
    pass

class RateLimitError(ApiError):
    def __init__(self, retry_after_seconds):
        self.retry_after_seconds = retry_after_seconds
        super().__init__(f"Rate limited. Retry after {retry_after_seconds} seconds")


def fetch_resource(resource_id, status_code):
    if status_code == 404:
        raise NotFoundError(resource_id)
    if status_code == 401:
        raise UnauthorizedError("Invalid or missing API key")
    if status_code == 429:
        raise RateLimitError(retry_after_seconds=30)
    return {"id": resource_id, "data": "..."}


def safe_fetch(resource_id, status_code):
    try:
        return fetch_resource(resource_id, status_code)
    except NotFoundError as e:
        print(f"Not found: {e.resource_id}")
    except RateLimitError as e:
        print(f"Back off for {e.retry_after_seconds}s and retry")
    except ApiError as e:
        print(f"Unexpected API error: {e}")


safe_fetch("user-42", 404)   # Not found: user-42
safe_fetch("user-42", 429)   # Back off for 30s and retry
```

Each error type carries exactly the data calling code needs to respond appropriately — a retry delay, a resource ID — instead of forcing the caller to parse a message string to figure out what happened.

---

## Benefits of Custom Exceptions

1. **Precise Error Handling** — Calling code can catch exactly the failure it knows how to handle
2. **Self-Documenting** — `InsufficientFundsError` communicates intent far better than a generic `ValueError`
3. **Structured Data** — Exceptions can carry attributes (`shortfall`, `retry_after_seconds`), not just a message
4. **Clear Hierarchies** — A shared base class lets code catch broadly or narrowly, as needed
5. **Better Debugging** — `raise ... from e` preserves the original cause, instead of hiding it

---

## Common Mistakes

1. Raising generic `Exception` or `ValueError` everywhere, forcing callers to parse message strings to distinguish error types
2. Forgetting to call `super().__init__(message)`, leaving `str(exception)` empty or unhelpful
3. Catching an exception and re-raising a new one without `from e`, losing the original traceback context
4. Building overly deep or overly granular exception hierarchies for a small project, adding complexity without real benefit
5. Catching `Exception` broadly when a more specific custom exception type would communicate intent far more clearly

---

## Best Practices

1. **Create a base exception class per module or package** (e.g., `BankError`), with specific exceptions inheriting from it
2. **Call `super().__init__(message)`** so the exception still has a sensible string representation
3. **Attach relevant data as attributes**, not just inside the message string, so callers can act on it programmatically
4. **Use `raise ... from e`** when wrapping a lower-level exception in a more meaningful one
5. **Only create a new exception type when callers genuinely need to distinguish it** from other errors — don't over-engineer a hierarchy nobody will catch differently

---

## See also

- [[02 - OOP — Inheritance]] and [[03 - OOP — Multiple Inheritance]] — the class hierarchy mechanics exception hierarchies rely on
- [[07 - OOP — Dunder Methods]] — `__init__` and `super()` as used to build informative custom exceptions
- [[22 - Custom Serialization]] — raising specific errors when serialized data doesn't match expectations
- [[24 - Testing with pytest]] — verifying the correct exception type is raised with `pytest.raises()`

---

## → What's next

You now understand **Custom Exceptions**:

- Why generic exceptions make it hard for calling code to respond appropriately to different failures
- How to define a custom exception class and build a clear hierarchy with a shared base
- How to attach structured data to an exception, not just a message string
- How `raise ... from e` preserves the original cause when wrapping a lower-level exception

In the next lesson, we will explore **Testing with pytest** — how to write automated tests that verify your code behaves correctly, including checking that the right exceptions are raised.

Continue with **[[24 - Testing with pytest]]**
