---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# OOP — Polymorphism

**Prev:** [[04 - OOP — Encapsulation]] | **Next:** *Coming Soon*

> In this lesson you will learn **Polymorphism** — the final core principle of Object-Oriented Programming. You will discover how to use different objects in the same way, making your code more flexible, reusable, and elegant.

---

## What is Polymorphism?

**Polymorphism** means "**many forms**".

In OOP, it means that different classes can be used in the same way, even though they implement things differently.

You can call the same method name on different objects, and each object responds in its own appropriate way.

### Real-Life Analogy

Think about pressing the "Start" button:

- A **Car** starts its engine
- A **Computer** boots up
- A **Washing Machine** begins its cycle

Same action ("start"), different behavior depending on the object.

---

## Simple Example

```python
class Dog:
    def speak(self):
        return "Woof! Woof!"

class Cat:
    def speak(self):
        return "Meow!"

class Cow:
    def speak(self):
        return "Moo!"


# Same code works for different animals
animals = [Dog(), Cat(), Cow()]

for animal in animals:
    print(animal.speak())
```

**Output:**
```
Woof! Woof!
Meow!
Moo!
```

Even though they are completely different classes, we can treat them the same way because they all have a `speak()` method.

---

## Types of Polymorphism

### 1. Method Overriding (Runtime Polymorphism)

This is the most common type — child classes override parent methods.

```python
class Shape:
    def area(self):
        return 0

class Rectangle(Shape):
    def __init__(self, length, width):
        self.length = length
        self.width = width
    
    def area(self):
        return self.length * self.width

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    
    def area(self):
        return 3.14 * self.radius * self.radius


shapes = [Rectangle(5, 3), Circle(4)]

for shape in shapes:
    print(f"Area: {shape.area()}")
```

---

### 2. Operator Overloading (Special Methods)

Python allows you to define how operators (`+`, `*`, `==`, etc.) work with your objects.

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def __add__(self, other):
        """Overloading the + operator"""
        return Point(self.x + other.x, self.y + other.y)
    
    def __str__(self):
        return f"Point({self.x}, {self.y})"


p1 = Point(2, 3)
p2 = Point(5, 7)

p3 = p1 + p2        # Uses our __add__ method
print(p3)           # Point(7, 10)
```

---

## Real-World Example: Payment System

```python
class PaymentProcessor:
    def process_payment(self, amount):
        raise NotImplementedError("Subclasses must implement this method")


class CreditCardPayment(PaymentProcessor):
    def process_payment(self, amount):
        return f"Processed ${amount} using Credit Card"


class PayPalPayment(PaymentProcessor):
    def process_payment(self, amount):
        return f"Processed ${amount} using PayPal"


class BankTransferPayment(PaymentProcessor):
    def process_payment(self, amount):
        return f"Processed ${amount} using Bank Transfer"


def make_payment(payment_method, amount):
    """This function works with any PaymentProcessor"""
    print(payment_method.process_payment(amount))


# Using polymorphism
payments = [
    CreditCardPayment(),
    PayPalPayment(),
    BankTransferPayment()
]

for payment in payments:
    make_payment(payment, 99.99)
```

---

## Duck Typing in Python

Python is very flexible with polymorphism thanks to **Duck Typing**:

> "If it walks like a duck and quacks like a duck, then it is a duck."

You don’t need to inherit from a common parent class — as long as the object has the required methods, it works.

```python
class Book:
    def read(self):
        return "Reading a physical book"

class EBook:
    def read(self):
        return "Reading on a tablet"

class Audiobook:
    def read(self):
        return "Listening to audiobook"


def start_reading(item):
    print(item.read())

start_reading(Book())
start_reading(EBook())
start_reading(Audiobook())
```

---

## Benefits of Polymorphism

1. **Flexibility** — Write code that works with many different types
2. **Code Reuse** — One function can handle many kinds of objects
3. **Extensibility** — Easy to add new classes without changing existing code
4. **Cleaner Code** — Less `if-elif` checking of object types
5. **Maintainability** — Changes in one class don’t break the whole system

---

## Common Mistakes

1. Creating unnecessary inheritance just to use polymorphism
2. Overloading too many operators (can make code confusing)
3. Not implementing required methods in child classes
4. Checking object types with `isinstance()` instead of using polymorphism

---

## Best Practices

1. **Design clear interfaces** — make method names consistent
2. **Use meaningful method names** (e.g., `process_payment` instead of `do_it`)
3. **Prefer composition over inheritance** when possible
4. **Keep polymorphic methods simple and focused**
5. **Document what methods a class should implement**

---

## See also

- [[01 - OOP — Classes and Objects]] — foundation of classes, objects, and methods
- [[02 - OOP — Inheritance]] — building class hierarchies
- [[03 - OOP — Multiple Inheritance]] — method resolution order and cooperative inheritance
- [[04 - OOP — Encapsulation]] — protecting data with properties
- [[16 - Defining Functions]] — understanding how functions and methods work
- [[19 - Lambda Functions]] — anonymous functions often used with polymorphic patterns

---

## → What's next

You now understand the four main pillars of Object-Oriented Programming:

- **Classes & Objects** — creating custom data types with behavior
- **Inheritance** — building specialized classes from general ones
- **Encapsulation** — controlling access to data
- **Polymorphism** — treating different objects uniformly
