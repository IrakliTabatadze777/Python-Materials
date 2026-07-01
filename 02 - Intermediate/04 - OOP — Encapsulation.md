---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# OOP — Encapsulation

**Prev:** [[03 - OOP — Multiple Inheritance]] | **Next:** [[05 - OOP — Polymorphism]]

> In this lesson you will learn **Encapsulation** — one of the core principles of Object-Oriented Programming. You will understand how to protect your object's data, control how it is accessed and modified, and write safer, more professional code.

---

## What is Encapsulation?

**Encapsulation** is the idea of **bundling data (attributes) and methods** that work on that data inside a single unit (class), while **hiding** some of the internal details from the outside.

It’s like a capsule or a black box:
- You know **what** it does
- You don’t need to know **how** it does it internally

### Real-Life Analogy

Think of a **Coffee Machine**:
- You press a button (public interface)
- You don’t need to know how the machine heats water, grinds beans, or manages pressure
- The internal parts are protected

In Python, encapsulation means:
- Some attributes should not be accessed or changed directly from outside the class
- We provide controlled ways (methods) to interact with the data

---

## Public vs Private Attributes

In Python, we use naming conventions to indicate the level of access:

| Naming   | Meaning                   | Accessible from outside?  | When to use                         |
| -------- | ------------------------- | ------------------------- | ----------------------------------- |
| `name`   | Public                    | Yes                       | Part of public API                  |
| `_name`  | Protected (by convention) | Yes, but discouraged      | Internal implementation             |
| `__name` | Private (name mangling)   | Technically yes, but hard | Avoid name conflicts in inheritance |

### Understanding Name Mangling

When you use `__name` (double underscore), Python **mangles** the name by adding `_ClassName` prefix:

```python
class BankAccount:
    def __init__(self, owner, balance):
        self.owner = owner              # Public: accessible as account.owner
        self._balance = balance         # Protected: accessible but discouraged
        self.__transaction_log = []     # Private: mangled to _BankAccount__transaction_log
    
    def deposit(self, amount):
        if amount > 0:
            self._balance += amount
            self.__transaction_log.append(f"Deposit: +${amount}")
            return True
        return False
    
    def get_balance(self):
        return self._balance

account = BankAccount("Alice", 1000)

print(account.owner)                    # Works - public
print(account._balance)                 # Works but discouraged
# print(account.__transaction_log)      # AttributeError!
print(account._BankAccount__transaction_log)  # Works but ugly - name mangling revealed
```

**When to use each:**
- **Public (`name`)**: Attributes and methods meant for external use
- **Protected (`_name`)**: Most common for internal attributes; signals "please don't access directly"
- **Private (`__name`)**: Rarely needed; mainly to avoid name conflicts in complex inheritance hierarchies

---

## Why Encapsulation Matters

### 1. Data Protection — Prevent Invalid States

**Without encapsulation:**
```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

rect = Rectangle(10, 5)
rect.width = -3  # Oops! Negative width makes no sense, but nothing stops this
```

**With encapsulation:**
```python
class Rectangle:
    def __init__(self, width, height):
        self._width = width
        self._height = height
    
    @property
    def width(self):
        return self._width
    
    @width.setter
    def width(self, value):
        if value <= 0:
            raise ValueError("Width must be positive!")
        self._width = value

rect = Rectangle(10, 5)
# rect.width = -3  # Raises ValueError - invalid state prevented!
```

### 2. Control — Centralized Logic

When all changes go through a method/property, you can add logging, validation, or side effects:

```python
class Temperature:
    def __init__(self, celsius):
        self._celsius = celsius
    
    @property
    def celsius(self):
        return self._celsius
    
    @celsius.setter
    def celsius(self, value):
        if value < -273.15:  # Absolute zero
            raise ValueError("Temperature below absolute zero!")
        print(f"Temperature changed from {self._celsius}°C to {value}°C")
        self._celsius = value
```

### 3. Flexibility — Change Implementation Without Breaking Code

```python
# Version 1: Store full name
class Person:
    def __init__(self, full_name):
        self._full_name = full_name
    
    @property
    def name(self):
        return self._full_name

# Later, change to first + last name internally, but external code still works!
class Person:
    def __init__(self, first_name, last_name):
        self._first_name = first_name
        self._last_name = last_name
    
    @property
    def name(self):
        return f"{self._first_name} {self._last_name}"  # Implementation changed

# External code using person.name doesn't need to change!
```

### 4. Maintainability — Easier to Find Bugs

If data is accessed directly, bugs can come from anywhere. With encapsulation, you have one place to check.

---

## Getters and Setters

The most common way to implement encapsulation is using **getter** and **setter** methods.

```python
class Student:
    def __init__(self, name, age):
        self._name = name
        self._age = age          # Protected
    
    # Getter
    def get_age(self):
        return self._age
    
    # Setter with validation
    def set_age(self, new_age):
        if new_age < 0:
            print("Age cannot be negative!")
            return False
        if new_age > 150:
            print("Age seems unrealistic!")
            return False
        self._age = new_age
        return True
    
    # Getter for name
    def get_name(self):
        return self._name


student = Student("Alice", 20)

print(student.get_age())      # 20
student.set_age(21)           # Valid change
student.set_age(-5)           # Invalid - rejected
```

---

## Pythonic Way: Properties

Python provides a cleaner way using the `@property` decorator. Instead of calling getter/setter methods, you access attributes naturally while still having control.

### Basic Property with Getter and Setter

```python
class Product:
    def __init__(self, name, price):
        self.name = name
        self._price = price
    
    @property
    def price(self):
        """Getter for price"""
        return self._price
    
    @price.setter
    def price(self, new_price):
        """Setter for price with validation"""
        if new_price < 0:
            raise ValueError("Price cannot be negative!")
        self._price = new_price

laptop = Product("MacBook", 1200)

print(laptop.price)      # Uses getter - looks like attribute access
laptop.price = 1100      # Uses setter - looks like assignment
# laptop.price = -50     # Raises ValueError
```

### Read-Only Properties (No Setter)

```python
class Product:
    def __init__(self, name, price):
        self.name = name
        self._price = price
    
    @property
    def price(self):
        return self._price
    
    @price.setter
    def price(self, new_price):
        if new_price < 0:
            raise ValueError("Price cannot be negative!")
        self._price = new_price
    
    @property
    def price_with_tax(self):
        """Read-only computed property"""
        return round(self._price * 1.2, 2)

laptop = Product("MacBook", 1200)
print(laptop.price_with_tax)  # 1440.0
# laptop.price_with_tax = 1500  # AttributeError - can't set!
```

### Property with Deleter

```python
class User:
    def __init__(self, email):
        self._email = email
    
    @property
    def email(self):
        return self._email
    
    @email.setter
    def email(self, value):
        if "@" not in value:
            raise ValueError("Invalid email")
        self._email = value
    
    @email.deleter
    def email(self):
        print("Removing email...")
        self._email = None

user = User("alice@example.com")
del user.email  # Calls the deleter
print(user.email)  # None
```

### Why Properties are Better than Getters/Setters

```python
# Old style (verbose)
student.set_age(21)
age = student.get_age()

# Pythonic with properties (clean and natural)
student.age = 21
age = student.age
```

Properties let you **start with simple attributes** and **add validation later** without changing how external code uses your class.

---

## Real-World Example: User Profile

```python
class UserProfile:
    def __init__(self, username, email):
        self.username = username          # Public
        self._email = email               # Protected
        self.__password = None            # Private
        self._is_active = True
    
    @property
    def email(self):
        return self._email
    
    @email.setter
    def email(self, new_email):
        if "@" not in new_email:
            raise ValueError("Invalid email address")
        self._email = new_email
        print("Email updated successfully")
    
    def set_password(self, new_password):
        if len(new_password) < 8:
            raise ValueError("Password must be at least 8 characters")
        self.__password = new_password  # In real app, hash the password!
        print("Password set successfully")
    
    def is_valid_user(self):
        return self._is_active and self.__password is not None


user = UserProfile("alice92", "alice@example.com")

print(user.email)
user.email = "newalice@example.com"

user.set_password("securepass123")
print(user.is_valid_user())
```

---

## Benefits of Encapsulation

- **Safety** — Prevents invalid data (negative age, invalid email)
- **Abstraction** — User of the class doesn’t need to know internal details
- **Easier Debugging** — Issues are contained within the class
- **Future-Proof** — You can change internal implementation without breaking code that uses the class

---

## Common Mistakes

### 1. Making Everything Public

**Bad:**
```python
class User:
    def __init__(self, username):
        self.username = username
        self.password = "1234"  # Exposed!

user = User("alice")
user.password = ""  # Anyone can set it to anything!
```

**Better:**
```python
class User:
    def __init__(self, username):
        self.username = username
        self._password_hash = None  # Protected
    
    def set_password(self, password):
        if len(password) < 8:
            raise ValueError("Password too short!")
        self._password_hash = hash(password)
```

### 2. Overusing Private Variables (`__name`)

**Unnecessary:**
```python
class Counter:
    def __init__(self):
        self.__count = 0  # Overkill for a simple counter
```

**Better:**
```python
class Counter:
    def __init__(self):
        self._count = 0  # Protected is usually enough
```

**When `__name` IS useful:**
```python
class Base:
    def __init__(self):
        self.__id = 1  # Won't conflict with child class's __id

class Child(Base):
    def __init__(self):
        super().__init__()
        self.__id = 2  # Different from Base's __id due to name mangling
```

### 3. Putting Too Much Logic in Setters

**Problematic:**
```python
@email.setter
def email(self, value):
    # Too much responsibility in a setter!
    if not self._validate_email(value):
        raise ValueError("Invalid email")
    self._email = value
    self._send_verification_email()
    self._update_database()
    self._notify_admins()
```

**Better:**
```python
@email.setter
def email(self, value):
    if "@" not in value:
        raise ValueError("Invalid email")
    self._email = value  # Just validate and set

def update_email(self, new_email):
    """Use a separate method for complex operations"""
    self.email = new_email  # This validates
    self._send_verification_email()
    self._update_database()
```

### 4. Forgetting Properties Make Code Cleaner

If you find yourself writing `get_x()` and `set_x()` for many attributes, consider using `@property` instead.

---

## Best Practices

1. **Start with protected attributes** (`_name`) for most internal data
2. **Use properties** for important attributes that need control
3. **Validate data** in setters and constructors
4. **Provide clear methods** for actions instead of exposing raw data
5. **Document public interface** (what users of your class should use)
6. **Don’t over-encapsulate** simple classes

---


## Key Takeaways

1. **Encapsulation** bundles data and methods together while controlling access
2. Use **`_name`** (protected) for most internal attributes — this is the Python convention
3. Use **`__name`** (private) only when you need to avoid name conflicts in inheritance
4. **`@property`** is the Pythonic way to create controlled attributes with validation
5. Properties can be **read-only** (no setter), **read-write** (with setter), or include a **deleter**
6. Start simple with public attributes; add encapsulation when you need control or validation
7. Don't over-encapsulate — Python trusts developers ("we're all consenting adults")

---

## See also

- [[01 - OOP — Classes and Objects]] — understanding classes, `self`, and methods
- [[02 - OOP — Inheritance]] — building upon parent classes
- [[03 - OOP — Multiple Inheritance]] — understanding `super()` and protected attributes
- [[05 - OOP — Polymorphism]] — next core OOP principle
- [[16 - Defining Functions]] — decorators like `@property`

---

## → What's next

You now understand **Encapsulation**:

- How to protect data with naming conventions (`_name`, `__name`)
- Why and when to use `@property` decorators
- How to validate data with setters
- The benefits of controlled access to attributes

In the next lesson, we will explore **Polymorphism** — the ability to use different classes with the same interface, making your code more flexible and allowing objects of different types to be treated uniformly.

Continue with **[[05 - OOP — Polymorphism]]**