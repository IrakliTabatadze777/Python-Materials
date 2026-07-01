---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# OOP — Inheritance

**Prev:** [[01 - OOP — Classes and Objects]] | **Next:** [[03 - OOP — Multiple Inheritance]]

> In this lesson you will learn **Inheritance** — one of the most powerful features of Object-Oriented Programming. You will see how to create new classes based on existing ones, reuse code, and build logical class hierarchies.

---

## What is Inheritance?

**Inheritance** allows you to create a new class based on an existing class.

The new class (called **child** or **subclass**) automatically gets all the attributes and methods from the old class (called **parent** or **superclass**), and can add its own new features.

### Real-Life Analogy

Think of biology:
- **Animal** is a general class (parent)
  - Has attributes: `name`, `age`
  - Has methods: `eat()`, `sleep()`

- **Dog** is a specific type of Animal (child)
  - Inherits everything from Animal
  - Adds its own: `bark()`, `breed`

- **Cat** is another child of Animal
  - Inherits everything from Animal
  - Adds its own: `meow()`, `lives_left`

This way you don't repeat the same code for common things.

---

## Basic Inheritance Example

```python
# Parent class
class Animal:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def eat(self):
        return f"{self.name} is eating."
    
    def sleep(self):
        return f"{self.name} is sleeping."

# Child class
class Dog(Animal):          # Dog inherits from Animal
    def __init__(self, name, age, breed):
        super().__init__(name, age)   # Call parent constructor
        self.breed = breed
    
    def bark(self):
        return f"{self.name} says Woof! Woof!"
    
    def get_info(self):
        return f"{self.name} is a {self.breed} and is {self.age} years old."


# Using the classes
dog = Dog("Buddy", 5, "Golden Retriever")

print(dog.eat())       # Inherited from Animal
print(dog.bark())      # Dog's own method
print(dog.get_info())
```

**Key points:**
- `class Dog(Animal):` → Dog inherits from Animal
- `super().__init__(name, age)` → Calls the parent class constructor
- The child class gets all methods from the parent automatically

---

## Understanding `super()`

`super()` is a special function that lets you call methods from the **parent class**.

Most commonly used in `__init__` to initialize the parent part of the object.

```python
class Vehicle:
    def __init__(self, brand, year):
        self.brand = brand
        self.year = year
    
    def start(self):
        return f"{self.brand} is starting."

class Car(Vehicle):
    def __init__(self, brand, year, model):
        super().__init__(brand, year)   # Initialize the Vehicle part
        self.model = model              # Add Car-specific attribute
    
    def honk(self):
        return "Beep! Beep!"
```

---

## Adding New Features & Overriding Methods

### 1. Adding New Methods
Child classes can have completely new methods (as seen with `bark()`).

### 2. Overriding Methods
Child classes can **replace** (override) parent methods:

```python
class Animal:
    def make_sound(self):
        return "Some generic sound"

class Dog(Animal):
    def make_sound(self):           # Overriding the parent method
        return "Woof! Woof!"

class Cat(Animal):
    def make_sound(self):
        return "Meow!"

dog = Dog("Buddy", 5, "Labrador")
cat = Cat("Luna", 3)

print(dog.make_sound())   # Woof! Woof!
print(cat.make_sound())   # Meow!
```

---

## Real-World Example: Employee System

```python
class Employee:
    """Base class for all employees."""
    
    def __init__(self, name, employee_id, salary):
        self.name = name
        self.employee_id = employee_id
        self.salary = salary
    
    def get_details(self):
        return f"Employee: {self.name} (ID: {self.employee_id})"
    
    def calculate_bonus(self):
        return self.salary * 0.1   # 10% bonus by default


class Manager(Employee):
    def __init__(self, name, employee_id, salary, department):
        super().__init__(name, employee_id, salary)
        self.department = department
        self.team_size = 0
    
    def calculate_bonus(self):           # Overriding
        return self.salary * 0.2         # Managers get 20%
    
    def assign_task(self, task):
        return f"Manager {self.name} assigned task: {task}"


class Developer(Employee):
    def __init__(self, name, employee_id, salary, programming_language):
        super().__init__(name, employee_id, salary)
        self.programming_language = programming_language
    
    def code(self):
        return f"{self.name} is coding in {self.programming_language}"


# Usage
manager = Manager("Alice", 101, 80000, "Engineering")
dev = Developer("Bob", 102, 65000, "Python")

print(manager.get_details())
print(manager.calculate_bonus())
print(dev.code())
```

---

## Benefits of Inheritance

1. **Code Reuse** — Write common code once in the parent class
2. **Less Duplication** — Avoid copying and pasting code
3. **Logical Structure** — Clear relationship between classes
4. **Easier Maintenance** — Change code in parent, all children benefit
5. **Extensibility** — Easy to add new specialized classes

---

## Common Mistakes

1. **Overusing Inheritance** — Not everything needs to be inherited
2. **Deep Inheritance Chains** — Too many levels (A → B → C → D) becomes hard to understand
3. **Forgetting `super()`** — Child class doesn't properly initialize parent
4. **Overriding too much** — Making the child too different from the parent

**Bad Example:**
```python
class Bird(Animal):     # Probably wrong relationship
    ...
```

Better to ask: *"Is this a kind of that?"* (Dog is a kind of Animal → Yes)

---

## Best Practices

1. **Use inheritance only when it makes logical sense** ("is-a" relationship)
2. **Keep parent classes general**, child classes more specific
3. **Always call `super().__init__()`** when creating child classes
4. **Override methods thoughtfully** — only when the behavior should be different
5. **Write clear docstrings** explaining the class relationships
6. **Start simple** — don't create complex hierarchies too early

---

## See also

- [[01 - OOP — Classes and Objects]] — understanding classes and `self`
- [[16 - Defining Functions]] — methods are functions, understanding how they work
- [[18 - Scope and Namespaces]] — how `super()` and method resolution works
- [[14 - Dictionaries]] — understanding data structures that classes build upon

---

## → What's next

You now understand **Inheritance** — how to create specialized classes while reusing code from parent classes.

But what happens when you need a class to inherit from multiple parents at once? In the next lesson, we will explore **Multiple Inheritance** — when a class inherits from more than one parent class, how Python resolves method conflicts with MRO (Method Resolution Order), and when to use this powerful but complex feature.

Continue with **[[03 - OOP — Multiple Inheritance]]**
