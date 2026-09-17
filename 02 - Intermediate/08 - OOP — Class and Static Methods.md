---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# OOP — Class and Static Methods

**Prev:** [[07 - OOP — Dunder Methods]] | **Next:** [[09 - Closures]]

> In this lesson you will learn about **Class Methods** and **Static Methods** — two special kinds of methods that belong to the class itself rather than to any single instance. You'll learn when to use each, and how they differ from the regular instance methods you already know.

---

## Three Kinds of Methods

So far, every method you've written has been an **instance method** — it takes `self` and operates on one specific object.

Python offers two more kinds of methods:

| Type              | First parameter | Operates on          | Decorator        |
| ------------------ | ---------------- | ---------------------- | ------------------ |
| Instance method    | `self`           | One specific object    | *(none)*            |
| Class method        | `cls`            | The class itself        | `@classmethod`      |
| Static method       | *(none)*          | Neither — just lives in the class namespace | `@staticmethod`     |

### Real-Life Analogy

Think of a **car factory**:

- An **instance method** is like adjusting the mirrors on *one specific car* — it only affects that car
- A **class method** is like changing the factory's *blueprint* — it affects how every future car of that model gets built
- A **static method** is like the factory's *safety manual* — useful information stored inside the factory, but it doesn't touch any particular car or the blueprint at all

---

## Instance Methods (Review)

```python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def give_raise(self, amount):
        """Instance method - operates on one specific employee"""
        self.salary += amount


alice = Employee("Alice", 5000)
alice.give_raise(500)
print(alice.salary)  # 5500
```

Instance methods need `self` because they read or change data that belongs to **one particular object**.

---

## Class Methods with `@classmethod`

A **class method** receives the class itself (`cls`) instead of an instance. It can't access instance-specific data (`self.x`), but it can access or modify **class-level** data, and it's commonly used to create **alternative constructors**.

```python
class Employee:
    company = "TechCorp"  # Class attribute, shared by all instances

    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    @classmethod
    def change_company(cls, new_company):
        """Class method - affects the class, not one instance"""
        cls.company = new_company


emp1 = Employee("Alice", 5000)
emp2 = Employee("Bob", 6000)

Employee.change_company("NewTech")

print(emp1.company)  # NewTech
print(emp2.company)  # NewTech - affects every instance!
```

### Alternative Constructors: The Most Common Use Case

Class methods are the standard, Pythonic way to offer multiple ways to create an object.

```python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    @classmethod
    def from_string(cls, employee_str):
        """Alternative constructor: create an Employee from 'Name-Salary'"""
        name, salary = employee_str.split("-")
        return cls(name, int(salary))

    @classmethod
    def from_dict(cls, data):
        """Alternative constructor: create an Employee from a dictionary"""
        return cls(data["name"], data["salary"])

    def __repr__(self):
        return f"Employee({self.name}, {self.salary})"


# Standard constructor
emp1 = Employee("Alice", 5000)

# Alternative constructors - same result, different input formats
emp2 = Employee.from_string("Bob-6000")
emp3 = Employee.from_dict({"name": "Carol", "salary": 7000})

print(emp1)  # Employee(Alice, 5000)
print(emp2)  # Employee(Bob, 6000)
print(emp3)  # Employee(Carol, 7000)
```

Using `cls(...)` instead of `Employee(...)` is important — it means the method still works correctly if a subclass inherits it (see below).

### Why `cls` Instead of the Class Name?

```python
class Employee:
    @classmethod
    def from_string(cls, employee_str):
        name, salary = employee_str.split("-")
        return cls(name, int(salary))  # Uses cls, not Employee


class Manager(Employee):
    def __init__(self, name, salary):
        super().__init__(name, salary)
        self.is_manager = True


mgr = Manager.from_string("Dave-8000")
print(type(mgr))  # <class '__main__.Manager'> - correctly creates a Manager!
```

If `from_string` had used `Employee(...)` directly instead of `cls(...)`, calling it from `Manager` would incorrectly create a plain `Employee` instead of a `Manager`.

---

## Static Methods with `@staticmethod`

A **static method** takes no automatic first argument at all — no `self`, no `cls`. It behaves like a regular function that just happens to live inside the class, grouped there because it's logically related.

```python
class TemperatureConverter:
    @staticmethod
    def celsius_to_fahrenheit(celsius):
        """Doesn't need self or cls - pure utility logic"""
        return (celsius * 9 / 5) + 32

    @staticmethod
    def fahrenheit_to_celsius(fahrenheit):
        return (fahrenheit - 32) * 5 / 9


print(TemperatureConverter.celsius_to_fahrenheit(100))   # 212.0
print(TemperatureConverter.fahrenheit_to_celsius(32))     # 0.0

# Also callable from an instance, though it's unusual to do so:
converter = TemperatureConverter()
print(converter.celsius_to_fahrenheit(0))  # 32.0
```

Static methods are used for **utility/helper logic** that's conceptually related to the class but doesn't need to read or modify any instance or class data.

---

## Choosing the Right One

```python
class Order:
    tax_rate = 0.08  # Class attribute

    def __init__(self, item, price):
        self.item = item
        self.price = price

    def total_with_tax(self):
        """Instance method - needs self.price, specific to this order"""
        return self.price * (1 + Order.tax_rate)

    @classmethod
    def set_tax_rate(cls, new_rate):
        """Class method - changes shared class data"""
        cls.tax_rate = new_rate

    @staticmethod
    def is_valid_price(price):
        """Static method - general utility, no instance or class data needed"""
        return price > 0


order = Order("Laptop", 1000)
print(order.total_with_tax())          # 1080.0

Order.set_tax_rate(0.10)
print(order.total_with_tax())          # 1100.0

print(Order.is_valid_price(-5))        # False
```

**Decision guide:**

1. Does it need data from **one specific object**? → **Instance method** (`self`)
2. Does it need to read/modify **class-level data**, or build an alternative constructor? → **Class method** (`cls`)
3. Does it need **neither**, but logically belongs with the class? → **Static method** (no special first argument)

---

## Real-World Example: Configuration Manager

```python
class DatabaseConnection:
    _instance_count = 0
    default_timeout = 30

    def __init__(self, host, port):
        self.host = host
        self.port = port
        DatabaseConnection._instance_count += 1

    @classmethod
    def from_url(cls, url):
        """Alternative constructor: parse 'host:port' style URLs"""
        host, port = url.split(":")
        return cls(host, int(port))

    @classmethod
    def connections_created(cls):
        """Reads class-level state"""
        return cls._instance_count

    @staticmethod
    def is_valid_port(port):
        """Pure utility check, no class or instance data needed"""
        return 0 < port <= 65535


db1 = DatabaseConnection.from_url("localhost:5432")
db2 = DatabaseConnection("192.168.1.1", 5432)

print(DatabaseConnection.connections_created())   # 2
print(DatabaseConnection.is_valid_port(5432))      # True
print(DatabaseConnection.is_valid_port(99999))     # False
```

---

## Benefits of Class and Static Methods

1. **Alternative Constructors** — Class methods offer clear, named ways to build objects (`from_string`, `from_dict`, `from_url`)
2. **Shared State Management** — Class methods can safely read/modify data shared across all instances
3. **Logical Organization** — Static methods keep related utility logic inside the class instead of scattered as free functions
4. **Inheritance-Friendly** — Using `cls(...)` in class methods ensures subclasses build the correct type
5. **Clarity** — The decorator signals intent immediately: does this method need an instance, the class, or neither?

---

## Common Mistakes

1. Using `@staticmethod` when the method actually needs `cls` for an alternative constructor
2. Hardcoding the class name (`Employee(...)`) instead of `cls(...)` inside a class method, which breaks subclasses
3. Overusing static methods for logic that would be clearer as a standalone module-level function
4. Forgetting that class methods modify **shared** class attributes — changes affect every instance
5. Adding `self` to a class or static method decorator by mistake, causing a `TypeError`

---

## Best Practices

1. **Use `@classmethod` for alternative constructors** — it's the most common and useful case
2. **Always use `cls(...)`, not the hardcoded class name**, inside class methods
3. **Use `@staticmethod` sparingly** — if the logic doesn't relate to the class at all, consider a plain function instead
4. **Keep class-level state minimal** — too many mutable class attributes can lead to confusing shared-state bugs
5. **Name alternative constructors clearly** (`from_string`, `from_dict`, `from_json`) so their purpose is obvious

---

## See also

- [[01 - OOP — Classes and Objects]] — foundation of classes, objects, and instance methods
- [[02 - OOP — Inheritance]] — how `cls` ensures subclasses build the correct type
- [[07 - OOP — Dunder Methods]] — `__init__` as the standard constructor these methods complement
- [[09 - Closures]] — how nested functions remember their enclosing scope
- [[10 - Dataclasses]] — reducing boilerplate for classes that mostly store data

---

## → What's next

You now understand **Class and Static Methods**:

- The difference between instance, class, and static methods
- How to use `@classmethod` to build alternative constructors with `cls`
- How to use `@staticmethod` for utility logic that belongs with the class
- How to choose the right method type based on what data it needs

In the next lesson, we will explore **Closures** — how a nested function can "remember" variables from the scope it was created in, even after that outer function has finished running.

Continue with **[[09 - Closures]]**
