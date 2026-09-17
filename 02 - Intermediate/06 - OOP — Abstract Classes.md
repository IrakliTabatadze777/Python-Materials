---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# OOP — Abstract Classes

**Prev:** [[05 - OOP — Polymorphism]] | **Next:** [[07 - OOP — Dunder Methods]]

> In this lesson you will learn about **Abstract Classes** — a way to design blueprints for other classes. You will discover how to force subclasses to implement specific methods, preventing incomplete or inconsistent objects from ever being created.

---

## What is an Abstract Class?

An **abstract class** is a class that **cannot be instantiated on its own**. It exists only to be **subclassed**.

It defines a common interface — a set of methods that every subclass **must** implement — without necessarily providing the full implementation itself.

### Real-Life Analogy

Think of a **blueprint for a vehicle**:

- The blueprint says every vehicle must have a `start_engine()` and `stop_engine()` method
- You can't drive a blueprint — it's not a real vehicle
- Only concrete vehicles (Car, Motorcycle, Truck) built from that blueprint can actually be driven

The blueprint guarantees that anything built from it behaves in a predictable way.

---

## Why Not Just Use a Regular Class?

Without abstraction, nothing stops a subclass from forgetting to implement an important method:

```python
class PaymentProcessor:
    def process_payment(self, amount):
        pass  # Forgot to raise an error or implement anything


class CreditCardPayment(PaymentProcessor):
    pass  # Oops — never implemented process_payment!


payment = CreditCardPayment()
payment.process_payment(100)  # Silently does nothing — bug!
```

This bug is easy to miss. Abstract classes catch it immediately, at the moment you try to create the object.

---

## The `abc` Module

Python provides the built-in `abc` (Abstract Base Classes) module to create true abstract classes.

```python
from abc import ABC, abstractmethod

class PaymentProcessor(ABC):
    @abstractmethod
    def process_payment(self, amount):
        """Every subclass must implement this method"""
        pass


class CreditCardPayment(PaymentProcessor):
    def process_payment(self, amount):
        return f"Processed ${amount} using Credit Card"


# Trying to instantiate the abstract class directly fails:
# processor = PaymentProcessor()  # TypeError!

payment = CreditCardPayment()
print(payment.process_payment(100))  # Processed $100 using Credit Card
```

**Output:**
```
Processed $100 using Credit Card
```

If `CreditCardPayment` forgets to implement `process_payment`, Python raises an error the moment you try to create an instance:

```python
class BrokenPayment(PaymentProcessor):
    pass

payment = BrokenPayment()
# TypeError: Can't instantiate abstract class BrokenPayment
# with abstract method process_payment
```

---

## Abstract Classes Can Still Have Regular Methods

An abstract class isn't *only* abstract methods — it can mix abstract methods with fully implemented ones that subclasses inherit as-is.

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        """Subclasses must calculate their own area"""
        pass

    @abstractmethod
    def perimeter(self):
        """Subclasses must calculate their own perimeter"""
        pass

    def describe(self):
        """Shared, concrete method — no need to override"""
        return f"This shape has an area of {self.area()} and a perimeter of {self.perimeter()}"


class Rectangle(Shape):
    def __init__(self, length, width):
        self.length = length
        self.width = width

    def area(self):
        return self.length * self.width

    def perimeter(self):
        return 2 * (self.length + self.width)


rect = Rectangle(5, 3)
print(rect.describe())
```

**Output:**
```
This shape has an area of 15 and a perimeter of 16
```

`describe()` is written once in the abstract class and reused by every subclass — only `area()` and `perimeter()` need to be reimplemented.

---

## Enforcing Multiple Abstract Methods

You can require as many abstract methods as your design needs. A subclass must implement **all** of them before it can be instantiated.

```python
from abc import ABC, abstractmethod

class Employee(ABC):
    @abstractmethod
    def calculate_salary(self):
        pass

    @abstractmethod
    def calculate_bonus(self):
        pass


class FullTimeEmployee(Employee):
    def __init__(self, base_salary):
        self.base_salary = base_salary

    def calculate_salary(self):
        return self.base_salary

    def calculate_bonus(self):
        return self.base_salary * 0.1


employee = FullTimeEmployee(5000)
print(employee.calculate_salary())  # 5000
print(employee.calculate_bonus())   # 500.0
```

---

## Abstract Properties

You can also require subclasses to implement a **property**, not just a method:

```python
from abc import ABC, abstractmethod

class Vehicle(ABC):
    @property
    @abstractmethod
    def max_speed(self):
        pass


class SportsCar(Vehicle):
    @property
    def max_speed(self):
        return 300


car = SportsCar()
print(car.max_speed)  # 300
```

---

## Real-World Example: Notification System

```python
from abc import ABC, abstractmethod

class Notifier(ABC):
    @abstractmethod
    def send(self, message):
        pass

    def notify(self, message):
        """Shared logic reused by every notifier"""
        print(f"Sending notification...")
        self.send(message)
        print(f"Notification sent.")


class EmailNotifier(Notifier):
    def send(self, message):
        print(f"Email: {message}")


class SMSNotifier(Notifier):
    def send(self, message):
        print(f"SMS: {message}")


notifiers = [EmailNotifier(), SMSNotifier()]

for notifier in notifiers:
    notifier.notify("Your order has shipped!")
```

This is the same pattern behind Python's own `PaymentProcessor`-style base classes seen in [[05 - OOP — Polymorphism]] — but here, `abc` **enforces** the contract instead of relying on `NotImplementedError`.

---

## Abstract Classes vs. `NotImplementedError`

You may have seen this pattern before:

```python
class PaymentProcessor:
    def process_payment(self, amount):
        raise NotImplementedError("Subclasses must implement this method")
```

This works, but the error only appears **when the method is called at runtime** — a forgotten implementation can slip through unnoticed until that code path runs.

With `abc`, the error appears **immediately when the object is created**, which catches mistakes much earlier.

| Approach              | When the error appears         | Enforced by       |
| ---------------------- | ------------------------------- | ------------------ |
| `NotImplementedError`  | When the method is called       | Convention only     |
| `abc.abstractmethod`   | When the object is instantiated | The Python interpreter |

---

## Benefits of Abstract Classes

1. **Enforced Contracts** — Subclasses can't forget to implement required behavior
2. **Early Error Detection** — Missing implementations fail at instantiation, not deep in runtime
3. **Clear Design Intent** — Communicates "this class is a template, not a finished product"
4. **Shared Code Reuse** — Concrete helper methods can live alongside abstract ones
5. **Better Documentation** — The abstract class itself describes the required interface

---

## Common Mistakes

1. Forgetting to inherit from `ABC` (then `@abstractmethod` has no effect)
2. Making a class abstract when it will only ever have one subclass
3. Putting too much implementation in the abstract class, defeating the purpose of forcing subclasses to define behavior
4. Confusing abstract classes with interfaces from other languages — Python's abstract classes can hold state and concrete methods too

---

## Best Practices

1. **Use `ABC` and `@abstractmethod`** instead of relying on `NotImplementedError`
2. **Keep abstract methods focused** — one clear responsibility each
3. **Add concrete helper methods** to the abstract class when logic is shared across subclasses
4. **Name abstract classes clearly** (e.g., `Shape`, `PaymentProcessor`) to signal they are blueprints
5. **Only make a class abstract when you expect multiple subclasses** with varying implementations

---

## See also

- [[01 - OOP — Classes and Objects]] — foundation of classes, objects, and methods
- [[02 - OOP — Inheritance]] — building class hierarchies
- [[03 - OOP — Multiple Inheritance]] — method resolution order and cooperative inheritance
- [[04 - OOP — Encapsulation]] — protecting data with properties
- [[05 - OOP — Polymorphism]] — using different objects through a shared interface
- [[07 - OOP — Dunder Methods]] — customizing how your objects behave with built-in operators and functions

---

## → What's next

You now understand **Abstract Classes**:

- Why forcing subclasses to implement certain methods prevents bugs
- How to use `ABC` and `@abstractmethod` to define required behavior
- How to mix abstract methods with shared, concrete methods
- The difference between `abc` enforcement and the `NotImplementedError` convention

You've now covered all the core Object-Oriented Programming principles: Classes & Objects, Inheritance, Encapsulation, Polymorphism, and Abstract Classes.

In the next lesson, we will explore **Dunder Methods** — the special "magic" methods (like `__init__`, `__str__`, and `__add__`) that let your objects work naturally with Python's built-in syntax and functions.

Continue with **[[07 - OOP — Dunder Methods]]**
