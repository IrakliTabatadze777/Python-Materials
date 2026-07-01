---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# OOP — Classes and Objects

**Prev:** [[28 - JSON]] | **Next:** [[02 - OOP — Inheritance]]

> In this lesson you will learn **Object-Oriented Programming (OOP)** — one of the most important ways to organize code in Python. If you only know basic Python (variables, functions, lists, dictionaries, loops), this lesson will explain everything step by step with simple examples and real-life analogies.

---

## What is Object-Oriented Programming?

Imagine you are building a big program — like a game, a website backend, or a banking system.

In normal Python (what you already know), you use:
- Variables to store data
- Functions to do actions
- Lists and dictionaries to group data

**Object-Oriented Programming (OOP)** is a better way to organize your code when your program gets bigger.

Instead of having data and functions separate, OOP lets you create **objects** that contain **both data and functions** that belong together.

### Real-Life Analogy

Think of a **Car**:
- Data (attributes): color, model, year, current speed, fuel level
- Actions (methods): start engine, accelerate, brake, honk

In OOP, you create a "Car" blueprint. Then you can make many different cars (objects) from that blueprint.

```python
# Without OOP (harder to manage)
car1_color = "red"
car1_speed = 0

def accelerate(car_speed):
    return car_speed + 10

# With OOP (much cleaner)
class Car:
    def __init__(self, color):
        self.color = color
        self.speed = 0
    
    def accelerate(self):
        self.speed += 10
        return self.speed
```

---

## Core Concepts (The Building Blocks)

| Concept       | Meaning                                      | Real-life Example      |
|---------------|----------------------------------------------|------------------------|
| **Class**     | Blueprint / Template                         | House plan             |
| **Object**    | Actual thing created from the blueprint      | A real house           |
| **Attribute** | Data / Property belonging to the object      | House color, number of rooms |
| **Method**    | Action / Function belonging to the object    | Open door, turn on lights |

---

## Defining Class

Let's create a simple `Dog` class:

```python
class Dog:
    """This is a blueprint for creating dogs."""
    
    def __init__(self, name, age):
        self.name = name      # Each dog remembers its own name
        self.age = age        # Each dog remembers its own age
    
    def bark(self):
        return f"{self.name} says Woof! Woof!"
    
    def get_info(self):
        return f"{self.name} is {self.age} years old."
```

**Explanation of each part:**

- `class Dog:` → Creates a new blueprint called `Dog`
- `"""This is..."""` → Docstring (description of the class)
- `__init__` → Special method that runs automatically when you create a new dog
- `self` → Refers to the specific dog being created (explained in detail below)
- `def bark(self):` → A method (action) that the dog can do

---

## Understanding `self` (Very Important!)

**`self`** is the most common thing that confuses beginners. Here is the simplest explanation:

> **`self` means "this object" or "myself".**

When you create two dogs:

```python
dog1 = Dog("Buddy", 5)
dog2 = Dog("Luna", 3)
```

- `dog1` is one dog
- `dog2` is another dog

When you call `dog1.bark()`, Python does this behind the scenes:

1. It calls the `bark` method
2. It automatically sends `dog1` as the first argument (`self`)
3. Inside the method, `self.name` means *"the name of this dog (Buddy)"*

**Rules you must remember:**
1. Every method inside a class must have `self` as the **first parameter**
2. You **do not** write `self` when calling the method
3. Use `self.something` to access or change the object's data

```python
# Correct way
dog1 = Dog("Buddy", 5)
print(dog1.bark())        # Buddy says Woof! Woof!
print(dog1.get_info())    # Buddy is 5 years old.
```

---

## Creating Objects (Instances)

```python
# Creating objects from the class
dog1 = Dog("Buddy", 5)
dog2 = Dog("Luna", 3)
dog3 = Dog("Max", 7)

print(dog1.name)           # Buddy
print(dog2.age)            # 3
print(dog1.bark())         # Buddy says Woof! Woof!
print(dog3.get_info())     # Max is 7 years old.
```

Each object has its own data. Changing one dog doesn't affect the others.

---

## Class Attributes vs Instance Attributes

```python
class Dog:
    species = "Canis familiaris"   # Class attribute (same for all dogs)
    
    def __init__(self, name, age):
        self.name = name           # Instance attribute (unique to each dog)
        self.age = age
```

```python
dog1 = Dog("Buddy", 5)
dog2 = Dog("Luna", 3)

print(dog1.species)   # Canis familiaris
print(dog2.species)   # Canis familiaris

# You can also access it from the class
print(Dog.species)
```

**Summary:**
- **Class attribute**: Shared by all objects (like species)
- **Instance attribute**: Unique to each object (like name and age)

---

## The `__init__` Method (Constructor)

`__init__` stands for "initialize". It sets up the object when it is created.

```python
class Student:
    def __init__(self, name, grade=0):
        self.name = name
        self.grade = grade
        self.attendance = 100   # default value
    
    def study(self, hours):
        self.grade += hours * 5
        if self.grade > 100:
            self.grade = 100
        return f"{self.name} studied and now has grade {self.grade}"

# Creating students
alice = Student("Alice", 75)
bob = Student("Bob")           # grade defaults to 0

print(alice.study(4))
print(bob.study(6))
```

---

## Real-World Example: Simple Bank Account

```python
class BankAccount:
    """A simple bank account class."""
    
    def __init__(self, owner, balance=0):
        self.owner = owner
        self.balance = balance
    
    def deposit(self, amount):
        if amount > 0:
            self.balance += amount
            return f"Deposited ${amount}. New balance: ${self.balance}"
        return "Amount must be positive"
    
    def withdraw(self, amount):
        if amount > self.balance:
            return "Not enough money!"
        self.balance -= amount
        return f"Withdrew ${amount}. Remaining: ${self.balance}"
    
    def get_balance(self):
        return f"{self.owner}'s balance: ${self.balance}"


# Using the class
my_account = BankAccount("Alice", 500) # owner = Alice, balance = 500
print(my_account.get_balance())
print(my_account.deposit(300))
print(my_account.withdraw(200))
print(my_account.get_balance())
```

---

## Why This is Better

- Code is **organized** — everything about a bank account is in one place
- **Reusable** — you can create many accounts easily
- **Readable** — `account.deposit(100)` is clearer than many separate functions
- **Maintainable** — if you want to add new features (like interest), you know where to put them

---

## Common Mistakes Beginners Make

1. Forgetting `self` in method definitions
2. Using `self` incorrectly inside methods
3. Putting too much code in one class
4. Forgetting that each object has its own data

---

## Best Practices (For Now)

1. Use clear names: `class BankAccount`, `def deposit()`
2. Always include `self` as the first parameter in methods
3. Write a short docstring for your class
4. Keep your classes focused on one thing
5. Start simple — don't try to make everything perfect at once

---

## See also

- [[16 - Defining Functions]] — understanding methods, which are functions inside classes
- [[18 - Scope and Namespaces]] — how Python resolves names and why `self` is needed
- [[14 - Dictionaries]] — classes are like advanced dictionaries with behavior
- [[28 - JSON]] — converting objects to JSON format for storage
- [[26 - File I-O]] — saving and loading object data

---

## → What's next

You now understand what classes and objects are, how to create them, and most importantly — how `self` works.

With this foundation, you're ready to learn how classes can build upon each other, reusing and extending functionality through **Inheritance** — one of the most powerful features of Object-Oriented Programming.

Continue with **[[02 - OOP — Inheritance]]**
