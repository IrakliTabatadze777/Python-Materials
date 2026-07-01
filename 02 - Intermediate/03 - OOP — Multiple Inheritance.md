---
tags:
  - intermediate
  - python
  - oop
stage: 2
difficulty: Intermediate
---

# OOP — Multiple Inheritance

**Prev:** [[02 - OOP — Inheritance]] | **Next:** [[04 - OOP — Encapsulation]]

> In this lesson you will learn **Multiple Inheritance** — when a class inherits from more than one parent class. This is a powerful but sometimes tricky feature. You will learn how it works step-by-step, with clear examples and real-world analogies.

---

## What is Multiple Inheritance?

So far, you have seen **Single Inheritance** — one child class inheriting from one parent.

**Multiple Inheritance** means a child class can inherit from **two or more** parent classes at the same time.

```python
class Parent1:
    def method_one(self):
        return "From Parent1"

class Parent2:
    def method_two(self):
        return "From Parent2"

class Child(Parent1, Parent2):   # Multiple Inheritance
    def method_three(self):
        return "From Child"
```

The child class gets attributes and methods from **all** its parents.

```python
obj = Child()
print(obj.method_one())   # From Parent1
print(obj.method_two())   # From Parent2
print(obj.method_three()) # From Child
```

**Why does Python support this?**

Multiple inheritance allows you to compose functionality from different sources. It's like building a character in a game by combining different skill sets, or assembling a vehicle by mixing capabilities from different vehicle types.

---

## Real-Life Analogy

Imagine a **Smartphone**:

- It is a **Phone** (can make calls, send messages)
- It is also a **Camera** (can take photos and videos)
- It is also a **Music Player** (can play songs)

Instead of writing all features from scratch, the Smartphone can inherit from Phone, Camera, and MusicPlayer classes.

---

## Basic Example

```python
class Flyer:
    def fly(self):
        return "Flying in the sky!"
    
    def get_type(self):
        return "I am a flyer"

class Swimmer:
    def swim(self):
        return "Swimming in the water!"
    
    def get_type(self):
        return "I am a swimmer"

# Multiple Inheritance
class Duck(Flyer, Swimmer):
    def __init__(self, name):
        self.name = name
    
    def speak(self):
        return f"{self.name} says Quack!"


duck = Duck("Donald")

print(duck.fly())      # From Flyer
print(duck.swim())     # From Swimmer
print(duck.speak())    # Duck's own method
print(duck.get_type()) # Which one wins? (explained below)
```

---

## How Multiple Inheritance Works: Method Resolution Order (MRO)

When a class inherits from multiple parents, Python uses a special rule called **Method Resolution Order (MRO)** to decide which method to use when there are conflicts.

### Understanding MRO

You can check the MRO like this:

```python
print(Duck.__mro__)
# Output:
# (<class '__main__.Duck'>, <class '__main__.Flyer'>, <class '__main__.Swimmer'>, <class 'object'>)
```

Or more readably:

```python
print(Duck.mro())
# [<class '__main__.Duck'>, <class '__main__.Flyer'>, <class '__main__.Swimmer'>, <class 'object'>]
```

Python searches for methods in this order:
1. The class itself (`Duck`)
2. First parent (`Flyer`)
3. Second parent (`Swimmer`)
4. Then grandparents, and so on...
5. Finally, `object` (the base of all classes)

This is why `duck.get_type()` returned "I am a flyer" — it found the method first in the `Flyer` class.

### The C3 Linearization Algorithm

Python uses an algorithm called **C3 linearization** (also known as C3 superclass linearization) to determine the MRO. This algorithm ensures:

1. **Children come before parents** — subclasses are checked before their base classes
2. **Parent order is preserved** — if `class C(A, B)`, then `A` comes before `B` in MRO
3. **No class appears twice** — each class appears exactly once in the MRO
4. **Monotonicity** — the order of parents is consistent across the hierarchy

**Why is this important?** It prevents confusing behavior where a method might be called from an unexpected parent class.

### Visualizing MRO

```python
class A:
    def method(self):
        print("A's method")

class B(A):
    def method(self):
        print("B's method")

class C(A):
    def method(self):
        print("C's method")

class D(B, C):  # Multiple inheritance
    pass

# Check MRO
print(D.mro())
# [<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>]

obj = D()
obj.method()  # Output: "B's method" (B comes before C in MRO)
```

The MRO goes: `D → B → C → A → object`

Even though both `B` and `C` inherit from `A`, Python ensures `A` is visited only once, and it comes after both `B` and `C`.

---

## Using `super()` with Multiple Inheritance

`super()` works with multiple inheritance too, but you must be careful. It follows the MRO to call the next method in the chain.

### Basic Example

```python
class A:
    def __init__(self):
        print("Initializing A")
        super().__init__()

class B:
    def __init__(self):
        print("Initializing B")
        super().__init__()

class C(A, B):
    def __init__(self):
        print("Initializing C")
        super().__init__()


c = C()
```

**Output:**
```
Initializing C
Initializing A
Initializing B
```

`super()` follows the MRO: `C → A → B → object`

Each `super().__init__()` calls the **next class in the MRO**, not necessarily the parent class. This ensures each parent is initialized only once.

### Why Use `super()` vs Direct Parent Calls?

**Using `super()` (Cooperative):**
```python
class A:
    def __init__(self):
        print("A")
        super().__init__()

class B:
    def __init__(self):
        print("B")
        super().__init__()

class C(A, B):
    def __init__(self):
        super().__init__()

C()  # Output: A, B (follows MRO, both called once)
```

**Direct parent calls (Non-cooperative):**
```python
class A:
    def __init__(self):
        print("A")

class B:
    def __init__(self):
        print("B")

class C(A, B):
    def __init__(self):
        A.__init__(self)
        B.__init__(self)

C()  # Output: A, B
```

**The difference becomes critical with the diamond problem:**

```python
class Base:
    def __init__(self):
        print("Base init")

class A(Base):
    def __init__(self):
        print("A init")
        Base.__init__(self)  # Direct call

class B(Base):
    def __init__(self):
        print("B init")
        Base.__init__(self)  # Direct call

class C(A, B):
    def __init__(self):
        A.__init__(self)
        B.__init__(self)

C()
# Output:
# A init
# Base init
# B init
# Base init    <-- Base initialized TWICE! This can cause bugs.
```

**With `super()`:**
```python
class Base:
    def __init__(self):
        print("Base init")
        super().__init__()

class A(Base):
    def __init__(self):
        print("A init")
        super().__init__()  # Cooperative

class B(Base):
    def __init__(self):
        print("B init")
        super().__init__()  # Cooperative

class C(A, B):
    def __init__(self):
        print("C init")
        super().__init__()

C()
# Output:
# C init
# A init
# B init
# Base init    <-- Base initialized ONCE (correct!)
```

**Key takeaway:** When using multiple inheritance, always use `super()` and ensure all classes in the hierarchy also use `super()`. This is called **cooperative multiple inheritance**.

### Passing Arguments with `super()`

When parents have different `__init__` signatures, you need to be careful:

```python
class Named:
    def __init__(self, name, **kwargs):
        self.name = name
        super().__init__(**kwargs)

class Aged:
    def __init__(self, age, **kwargs):
        self.age = age
        super().__init__(**kwargs)

class Person(Named, Aged):
    def __init__(self, name, age):
        super().__init__(name=name, age=age)

p = Person("Alice", 30)
print(p.name, p.age)  # Alice 30
```

Using `**kwargs` allows each class to extract its arguments and pass the rest along the MRO chain.

---

## Real-World Example: Character in a Game

```python
class Fighter:
    def __init__(self, name, strength):
        self.name = name
        self.strength = strength
        self.health = 100
    
    def attack(self):
        return f"{self.name} attacks with strength {self.strength}!"

class Mage:
    def __init__(self, name, mana):
        self.name = name
        self.mana = mana
    
    def cast_spell(self):
        if self.mana >= 20:
            self.mana -= 20
            return f"{self.name} casts a powerful spell!"
        return f"{self.name} is out of mana!"

class BattleMage(Fighter, Mage):
    def __init__(self, name, strength, mana):
        # Initialize both parents
        Fighter.__init__(self, name, strength)
        Mage.__init__(self, name, mana)
        self.level = 1
    
    def ultimate_attack(self):
        return f"{self.name} combines strength and magic for a devastating attack!"


hero = BattleMage("Elara", 75, 120)

print(hero.attack())       # From Fighter
print(hero.cast_spell())   # From Mage
print(hero.ultimate_attack())
print(f"Health: {hero.health} | Mana: {hero.mana}")
```

---

## The Diamond Problem

Multiple inheritance can cause issues when two parents inherit from the same grandparent. This is called the **Diamond Problem** because of its shape:

```
      Grandparent
     /           \
   Parent1      Parent2
     \           /
         Child
```

### The Problem Visualized

```python
class Animal:
    def __init__(self, name):
        print(f"Animal init: {name}")
        self.name = name
    
    def make_sound(self):
        return "Some generic sound"

class Mammal(Animal):
    def __init__(self, name, fur_color):
        print(f"Mammal init: {name}")
        super().__init__(name)
        self.fur_color = fur_color
    
    def make_sound(self):
        return "Mammal sound"

class Bird(Animal):
    def __init__(self, name, wing_span):
        print(f"Bird init: {name}")
        super().__init__(name)
        self.wing_span = wing_span
    
    def make_sound(self):
        return "Tweet tweet"

class Bat(Mammal, Bird):  # Diamond: both parents inherit from Animal
    def __init__(self, name, fur_color, wing_span):
        print(f"Bat init: {name}")
        super().__init__(name, fur_color)
        self.wing_span = wing_span
```

Let's see what happens:

```python
bat = Bat("Bruce", "brown", 30)
# Output:
# Bat init: Bruce
# Mammal init: Bruce
# Bird init: Bruce
# Animal init: Bruce
# (Notice: Animal is initialized only ONCE, not twice!)

print(Bat.mro())
# [<class 'Bat'>, <class 'Mammal'>, <class 'Bird'>, <class 'Animal'>, <class 'object'>]

print(bat.make_sound())  # "Mammal sound" (Mammal comes first in MRO)
```

### Why Diamond Problem is Tricky

**Questions that arise:**

1. **Which parent's method should the child use?** 
   - Python uses MRO to decide (left-to-right, depth-first)

2. **Should the grandparent be initialized once or twice?**
   - Once! (Initializing twice can cause data corruption or waste resources)

3. **How do we pass different arguments to different parents?**
   - Use `**kwargs` pattern (shown in the super() section above)

### How Python Solves It

Python's **C3 linearization algorithm** ensures:

- Each class appears exactly once in the MRO
- Parent order is respected (left parent before right parent)
- Each class comes before its parents

This prevents the grandparent from being initialized multiple times and makes method resolution predictable.

### A More Complex Diamond

```python
class A:
    def method(self):
        return "A"

class B(A):
    def method(self):
        return "B -> " + super().method()

class C(A):
    def method(self):
        return "C -> " + super().method()

class D(B, C):
    def method(self):
        return "D -> " + super().method()

obj = D()
print(obj.method())
# Output: "D -> B -> C -> A"

print(D.mro())
# [<class 'D'>, <class 'B'>, <class 'C'>, <class 'A'>, <class 'object'>]
```

Each class calls `super().method()`, which chains through the MRO:
- `D.method()` calls `super()` → goes to `B`
- `B.method()` calls `super()` → goes to `C` (not `A`!)
- `C.method()` calls `super()` → goes to `A`
- `A.method()` returns `"A"`

This is the power of cooperative multiple inheritance.

---

## When to Use Multiple Inheritance?

**Good uses:**
- Mixing capabilities (like `Flyer` + `Swimmer`)
- Creating complex objects from well-defined behaviors
- Working with frameworks that provide mixin classes

**Avoid when:**
- The relationship is not clear
- It makes your code too complicated
- You can solve the problem with composition (having objects inside other objects) instead

**Rule of thumb:** Use multiple inheritance sparingly and only when it truly makes sense.

---

## Mixins: The Best Use of Multiple Inheritance

A **Mixin** is a class that provides a specific, well-defined piece of functionality meant to be mixed with other classes via multiple inheritance.

**Key characteristics of Mixins:**

1. They don't stand alone (not meant to be instantiated directly)
2. They provide a specific capability or behavior
3. They typically don't have `__init__` (or use `super().__init__()` cooperatively)
4. Their names often end with "Mixin"

### Example: JSON Serialization Mixin

```python
import json

class JSONSerializableMixin:
    """Adds JSON serialization capability to any class"""
    
    def to_json(self):
        """Convert object to JSON string"""
        return json.dumps(self.__dict__, indent=2)
    
    @classmethod
    def from_json(cls, json_string):
        """Create object from JSON string"""
        data = json.loads(json_string)
        return cls(**data)


class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age


class SerializablePerson(Person, JSONSerializableMixin):
    """Person with JSON serialization capability"""
    pass


# Usage
person = SerializablePerson("Alice", 30)
print(person.to_json())
# Output:
# {
#   "name": "Alice",
#   "age": 30
# }

# Recreate from JSON
json_str = '{"name": "Bob", "age": 25}'
bob = SerializablePerson.from_json(json_str)
print(f"{bob.name} is {bob.age} years old")
```

### Example: Timestamp Mixin

```python
from datetime import datetime

class TimestampMixin:
    """Adds creation and modification timestamps to any class"""
    
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.created_at = datetime.now()
        self.updated_at = datetime.now()
    
    def touch(self):
        """Update the modification timestamp"""
        self.updated_at = datetime.now()


class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price
    
    def set_price(self, new_price):
        self.price = new_price


class TrackedProduct(TimestampMixin, Product):
    """Product with automatic timestamp tracking"""
    pass


# Usage
product = TrackedProduct("Laptop", 999.99)
print(f"Created: {product.created_at}")

import time
time.sleep(2)

product.set_price(899.99)
product.touch()
print(f"Updated: {product.updated_at}")
```

### Example: Comparison Mixin

```python
class ComparableMixin:
    """Adds comparison operations based on a 'value' attribute"""
    
    def __eq__(self, other):
        if not isinstance(other, self.__class__):
            return NotImplemented
        return self.value == other.value
    
    def __lt__(self, other):
        if not isinstance(other, self.__class__):
            return NotImplemented
        return self.value < other.value
    
    def __le__(self, other):
        return self == other or self < other
    
    def __gt__(self, other):
        return not self <= other
    
    def __ge__(self, other):
        return not self < other


class Score(ComparableMixin):
    def __init__(self, points):
        self.value = points  # ComparableMixin uses 'value'
        self.points = points
    
    def __repr__(self):
        return f"Score({self.points})"


# Usage
score1 = Score(85)
score2 = Score(92)
score3 = Score(85)

print(score1 < score2)   # True
print(score1 == score3)  # True
print(score2 >= score1)  # True

scores = [Score(78), Score(95), Score(82), Score(95), Score(71)]
print(sorted(scores))
# [Score(71), Score(78), Score(82), Score(95), Score(95)]
```

### Multiple Mixins

You can combine multiple mixins:

```python
class SuperProduct(TimestampMixin, JSONSerializableMixin, Product):
    """Product with timestamps AND JSON serialization"""
    pass

product = SuperProduct("Phone", 699.99)
print(product.to_json())
# Includes name, price, created_at, updated_at in JSON
```

**Why Mixins are Better than Utility Functions:**

```python
# Without mixin (procedural):
def to_json(obj):
    return json.dumps(obj.__dict__)

person = Person("Alice", 30)
json_str = to_json(person)  # External function

# With mixin (OOP):
person = SerializablePerson("Alice", 30)
json_str = person.to_json()  # Method on the object (more intuitive)
```

Mixins integrate functionality directly into the class, making the API cleaner and more discoverable.

---

## Common Mistakes

### 1. Not Calling Parent Constructors Properly

**Wrong:**
```python
class A:
    def __init__(self):
        self.a_value = "A"

class B:
    def __init__(self):
        self.b_value = "B"

class C(A, B):
    def __init__(self):
        A.__init__(self)
        # Forgot to call B.__init__!

c = C()
print(c.b_value)  # AttributeError: 'C' object has no attribute 'b_value'
```

**Correct:**
```python
class C(A, B):
    def __init__(self):
        super().__init__()  # Calls both A and B through MRO
```

### 2. Confusing Method Names Across Parents

**Problematic:**
```python
class Logger:
    def write(self, message):
        print(f"[LOG] {message}")

class FileWriter:
    def write(self, data):
        # Writes to file
        with open("output.txt", "w") as f:
            f.write(data)

class LoggedFileWriter(Logger, FileWriter):
    pass

obj = LoggedFileWriter()
obj.write("Hello")  # Which write() is called? (Logger.write, due to MRO)
```

**Fix:** Use different method names or override explicitly:

```python
class LoggedFileWriter(Logger, FileWriter):
    def write(self, data):
        Logger.write(self, f"Writing: {data}")  # Log first
        FileWriter.write(self, data)            # Then write to file
```

### 3. Creating Overly Complex Inheritance Trees

**Bad:**
```python
class A: pass
class B(A): pass
class C(A): pass
class D(B, C): pass
class E(A): pass
class F(D, E): pass
class G(B, C): pass
class H(F, G): pass  # MRO becomes very confusing
```

**Better:** Keep hierarchies shallow and use composition instead.

### 4. Forgetting About MRO When Debugging

**Issue:**
```python
class Base:
    def compute(self):
        return 0

class PluginA(Base):
    def compute(self):
        return 10

class PluginB(Base):
    def compute(self):
        return 20

class Combined(PluginA, PluginB):
    pass

result = Combined().compute()
print(result)  # 10 (not 20, because PluginA comes first in MRO)
```

**Always check MRO** when behavior is unexpected:

```python
print(Combined.mro())
# [Combined, PluginA, PluginB, Base, object]
```

### 5. Not Using Cooperative `super()`

**Non-cooperative (breaks with diamond inheritance):**
```python
class A:
    def __init__(self):
        print("A")

class B(A):
    def __init__(self):
        A.__init__(self)  # Direct call, not cooperative
        print("B")
```

**Cooperative (works with diamond inheritance):**
```python
class A:
    def __init__(self):
        super().__init__()
        print("A")

class B(A):
    def __init__(self):
        super().__init__()  # Cooperative call
        print("B")
```

### 6. Mixing Cooperative and Non-Cooperative Code

**Problematic:**
```python
class A:
    def __init__(self):
        print("A")
        # No super().__init__() call

class B:
    def __init__(self):
        super().__init__()  # This will fail because A doesn't call super()
        print("B")

class C(B, A):
    def __init__(self):
        super().__init__()

# C() might not work as expected
```

**Rule:** If you use `super()`, all classes in the hierarchy should use it too.

---

## Best Practices

1. **Keep multiple inheritance simple** — preferably 2 parents maximum
2. **Use clear class names** that describe their role
3. **Document your class** and its parents clearly
4. **Check MRO** (`ClassName.__mro__`) when behavior is unexpected
5. **Always initialize all parent classes** in the child’s `__init__`
6. **Use `super()` cooperatively** — ensure all classes in the hierarchy use `super()`
7. **Keep mixins small and focused** — each mixin should do one thing well
8. **Name mixins clearly** — use `XxxMixin` naming convention
9. **Avoid deep inheritance hierarchies** — 2-3 levels maximum

---

## See also

- [[02 - OOP — Inheritance]] — single inheritance basics
- [[01 - OOP — Classes and Objects]] — understanding classes, `self`, and methods
- [[16 - Defining Functions]] — understanding `super()` and method calls
- [[18 - Scope and Namespaces]] — how Python resolves names in class hierarchies

---

## → What's next

You now understand:

- How Multiple Inheritance works and when to use it
- **Method Resolution Order (MRO)** and the C3 linearization algorithm
- The **diamond problem** and how Python solves it with `super()`
- **Mixins** — the best practice pattern for multiple inheritance
- The trade-offs between **multiple inheritance vs composition**
- Common mistakes and how to avoid them

With your knowledge of classes, inheritance, and multiple inheritance, you're ready to learn how to protect and control access to your object's data. In the next lesson, we will explore **Encapsulation** — one of the core OOP principles that teaches you how to hide internal details, validate data, and create safer, more maintainable code using properties and naming conventions.

Continue with **[[04 - OOP — Encapsulation]]**