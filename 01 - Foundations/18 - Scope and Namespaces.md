---
tags:
  - foundations
  - python
  - functions
  - scope
  - namespaces
stage: 1
difficulty: Beginner
---

# Scope and Namespaces in Python

**Prev:** [[17 - Arguments and Parameters.md]] | **Next:** [[19 - Lambda Functions]]

> Scope determines where a variable can be accessed in your code, while namespaces define how Python organizes and stores names (variables, functions, objects).

---

## What is a Namespace?

A **namespace** is a container that maps names to objects.

In simple terms:
- A variable name is stored in a namespace
- Python uses namespaces to avoid name conflicts

```python
x = 10  # global namespace

def example():
    y = 5  # local namespace
    print(y)

example()
````

### Explanation:

* `x` is in the global namespace (accessible everywhere in the file)
* `y` is in the local namespace (exists only inside the function)

---

## Types of Namespaces

Python has 3 main types of namespaces:

### 1. Built-in Namespace

Contains Python's built-in functions and exceptions.

```python
print(len("hello"))  # len is from built-in namespace
```

### Explanation:

* `print` and `len` are always available
* They come from Python's built-in namespace

---

### 2. Global Namespace

Contains variables defined at the top level of a file.

```python
a = 100  # global variable

def show():
    print(a)  # accessing global variable

show()
```

### Explanation:

* `a` is defined outside any function
* It is accessible inside functions (read-only by default)

---

### 3. Local Namespace

Contains variables defined inside a function.

```python
def func():
    value = 50  # local variable
    print(value)

func()
```

### Output:

```
50
```

### Explanation:

* `value` exists only inside `func`
* It cannot be accessed outside the function

---

## What is Scope?

**Scope** defines where a variable can be accessed.

There are 4 types of scope in Python:

### LEGB Rule:

Python resolves names in this order:

1. **Local**
2. **Enclosing**
3. **Global**
4. **Built-in**

---

## Local Scope

Variables defined inside a function.

```python
def test():
    x = 10  # local variable
    print(x)

test()
```

### Explanation:

* `x` exists only inside `test()`
* Cannot be accessed outside

---

## Global Scope

Variables defined outside functions.

```python
x = 20  # global variable

def show():
    print(x)

show()
print(x)
```

### Output:

```
20
20
```

### Explanation:

* `x` is accessible everywhere in the file

---

## Enclosing Scope (Nested Functions)

Occurs in nested functions.

```python
def outer():
    x = 100  # enclosing scope

    def inner():
        print(x)  # accessing enclosing variable

    inner()

outer()
```

### Explanation:

* `x` is defined in `outer()`
* `inner()` can access it due to enclosing scope

---

## Built-in Scope

Python’s built-in functions are always available.

```python
print(max([1, 2, 3]))  # max is built-in
```

### Explanation:

* `max` comes from built-in scope
* No need to define it

---

## Variable Lookup Order (LEGB Rule)

Python searches variables in this order:

```python
x = "global"

def outer():
    x = "enclosing"

    def inner():
        x = "local"
        print(x)

    inner()

outer()
```

### Output:

```
local
```

### Explanation:

* Python first checks Local scope
* If not found, it checks Enclosing
* Then Global
* Then Built-in

---

## Modifying Global Variables

To modify a global variable inside a function, use `global`.

```python
x = 10

def change():
    global x
    x = 20  # modifies global variable

change()
print(x)
```

### Output:

```
20
```

### Explanation:

* `global` allows modification of global variable
* Without it, Python creates a local variable instead

---

## Modifying Enclosing Variables (nonlocal)

Used in nested functions.

```python
def outer():
    x = 10

    def inner():
        nonlocal x
        x = 20  # modifies enclosing variable

    inner()
    print(x)

outer()
```

### Output:

```
20
```

### Explanation:

* `nonlocal` modifies variable in enclosing scope
* Useful in nested function structures

---

## What Happens Without global or nonlocal?

```python
x = 10

def change():
    x = 20  # creates a new local variable

change()
print(x)
```

### Output:

```
10
```

### Explanation:

* `x = 20` is local, not global
* Global variable remains unchanged

---

## Scope Isolation Example

```python
def func1():
    a = 1
    print(a)

def func2():
    a = 2
    print(a)

func1()
func2()
```

### Output:

```
1
2
```

### Explanation:

* Each function has its own local scope
* Variables do not interfere with each other

---

## Shadowing Variables

Local variables can override global ones.

```python
x = 50

def test():
    x = 100  # shadows global x
    print(x)

test() # prints local variable
print(x) # prints global variable
```

### Output:

```
100
50
```

### Explanation:

* Inside function, local `x` is used
* Global `x` remains unchanged

---

## Namespace Inspection

You can view current namespaces using `globals()` and `locals()`.

```python
x = 10

def show():
    y = 20
    print(locals())   # local namespace
    print(globals())  # global namespace

show()
```

### Explanation:

* `locals()` shows variables inside function
* `globals()` shows variables in global scope

---

## Common Mistakes

### 1. Using variable before definition

```python
def func():
    print(x)
    x = 10
```

### Explanation:

* Python sees `x` as local
* But it's used before assignment → error

---

### 2. Modifying global without global keyword

```python
x = 10

def change():
    x = 20  # creates local variable instead of modifying global
```

### Explanation:

* Global variable is NOT changed unintentionally

---

### 3. Confusing local and global scope

```python
x = 5

def func():
    print(x)  # works (reading global)
```

### Explanation:

* Reading global is allowed
* Writing requires `global`

---

## Best Practices

* Avoid using `global` unless necessary
* Prefer returning values instead of modifying globals
* Keep functions self-contained
* Minimize side effects
* Use `nonlocal` only when working with closures

---

## Summary

| Scope Type | Description              |
| ---------- | ------------------------ |
| Local      | Inside function          |
| Enclosing  | Inside nested function   |
| Global     | Top-level variables      |
| Built-in   | Python default functions |

---

## → What's Next

Now that you understand how Python manages variable visibility and scope, the next step is learning about anonymous functions.

Continue with **[[19 - Lambda Functions]]**
