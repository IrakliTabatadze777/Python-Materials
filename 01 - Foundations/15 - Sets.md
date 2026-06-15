---
tags:
  - foundations
  - python
  - data-structures
  - collections
stage: 1
difficulty: Beginner
---

# Sets in Python

**Prev:** [[14 - Dictionaries]] | **Next:** [[16 - Defining Functions]]

> Sets are unordered collections of **unique** elements. They are extremely useful for mathematical operations, removing duplicates, and fast membership testing.

---

## What is a Set?

A **set** is an unordered, mutable collection of **unique** and **hashable** elements. Sets are based on hash tables, which makes many operations very fast.

```python
# Examples of sets
fruits = {"apple", "banana", "cherry", "apple"}   # Duplicates are removed
numbers = {1, 2, 3, 4, 5}
empty_set = set()                                 # Important: {} creates empty dict!
mixed = {1, "hello", 3.14, True}
```

**Key Characteristics:**
- Unordered (no guaranteed order)
- Unique elements only (automatically removes duplicates)
- Mutable (you can add/remove elements)
- Elements must be hashable (immutable)
- Very fast membership testing (`in` operator)

---

## Creating Sets

```python
# Different ways to create sets

# 1. Literal syntax
colors = {"red", "green", "blue"}

# 2. From a list (great for removing duplicates)
numbers_list = [1, 2, 2, 3, 3, 4, 1]
unique_numbers = set(numbers_list)          # {1, 2, 3, 4}

# 3. Using set() constructor
letters = set("Python")                     # {'P', 'y', 't', 'h', 'o', 'n'}

# 4. Set comprehension
squares = {x**2 for x in range(10)}
```

---

## Basic Set Operations

### Adding and Removing Elements

```python
skills = {"Python", "SQL"}

skills.add("Git")                    # Add single item
skills.update(["Django", "Docker"])  # Add multiple items

skills.remove("SQL")                 # Raises KeyError if not found
skills.discard("Java")               # Safe remove (no error if missing)
popped = skills.pop()                # Remove and return random item
skills.clear()                       # Empty the set
```

**`add(item)`** — adds one element. If the item is already in the set, nothing happens (sets ignore duplicates silently).

```python
tags = {"python", "sql"}
tags.add("python")       # no error — set stays {"python", "sql"}
tags.add("git")
# tags → {"python", "sql", "git"}
```

**`update(*others)`** — adds many elements at once. Accepts another set, a list, a tuple, or any iterable. Like calling `add()` in a loop, but faster.

```python
a = {1, 2}
a.update([2, 3, 4], {4, 5})   # merge from multiple iterables
# a → {1, 2, 3, 4, 5}
```

**`remove(item)` vs `discard(item)`** — both delete one element. The difference is error handling:

```python
s = {1, 2, 3}

s.remove(2)        # s → {1, 3}
# s.remove(99)     # KeyError — element not found

s.discard(99)      # no error — set unchanged
```

Use **`remove()`** when a missing element means something went wrong. Use **`discard()`** when "remove if present" is enough.

**`pop()`** — removes and returns an **arbitrary** element (not random in the statistical sense, but the set is unordered, so you cannot predict which one). Raises `KeyError` on an empty set.

```python
s = {"a", "b", "c"}
item = s.pop()     # e.g. "b" — which item you get is not guaranteed
# s → {"a", "c"} (two remaining items)
```

**`clear()`** — removes all elements, leaving an empty set (`set()`).

---

## Mathematical Set Operations

Sets are modeled on mathematical set theory. Each operation below has an **operator** form (`set1 | set2`) and a **method** form (`set1.union(set2)`). Both return a **new set** — the original sets are unchanged.

There are also **in-place** versions that modify the left-hand set — either as operators (`|=`, `&=`, `-=`, `^=`) or as methods (`update()`, `intersection_update()`, etc.). Same idea as `+=` for lists.

```python
set1 = {1, 2, 3}
set2 = {3, 4, 5}
```

### 1. Union (`|` or `.union()`) — everything from both sets

**Union** combines all unique elements from both sets. Think: "all items that appear in set1 **or** set2 (or both)."

```python
print(set1 | set2)                    # {1, 2, 3, 4, 5}
print(set1.union(set2))               # same result

# Union of more than two sets
print(set1.union(set2, {5, 6}))       # {1, 2, 3, 4, 5, 6}
print({1, 2} | {2, 3} | {3, 4})       # {1, 2, 3, 4}
```

**In-place:** `set1 |= set2` or `set1.update(set2)` adds all elements from `set2` into `set1`. (For union, the method is `update()` — there is no `union_update()`.)

```python
a = {1, 2}
a |= {2, 3}          # a → {1, 2, 3}

b = {1, 2}
b.update({2, 3})     # b → {1, 2, 3} — same effect
```

**Real use:** merge two lists of tags, user IDs, or permissions without duplicates.

```python
admin_perms = {"read", "write", "delete"}
guest_perms = {"read"}
all_perms = admin_perms | guest_perms   # {"read", "write", "delete"}
```

### 2. Intersection (`&` or `.intersection()`) — only what both share

**Intersection** keeps elements that appear in **both** sets. Think: "what do they have in common?"

```python
print(set1 & set2)                    # {3}
print(set1.intersection(set2))        # {3}

# Empty intersection — no shared elements
print({1, 2} & {3, 4})                # set()
```

**In-place:** `set1 &= set2` or `set1.intersection_update(set2)` keeps only elements that exist in both sets.

```python
a = {1, 2, 3}
a &= {2, 3, 4}                    # a → {2, 3}

b = {1, 2, 3}
b.intersection_update({2, 3, 4})  # b → {2, 3} — same effect
```

**Real use:** find skills a candidate and a job posting both require.

```python
my_skills = {"Python", "SQL", "Git"}
job_needs = {"Python", "Django", "SQL"}
matches = my_skills & job_needs       # {"Python", "SQL"}
```

### 3. Difference (`-` or `.difference()`) — in first, not in second

**Difference** returns elements in the **first** set that are **not** in the second. Order matters: `set1 - set2` is not the same as `set2 - set1`.

```python
print(set1 - set2)                    # {1, 2} — in set1 but not in set2
print(set2 - set1)                    # {4, 5} — in set2 but not in set1
print(set1.difference(set2))          # {1, 2}

# Difference from multiple sets
print({1, 2, 3, 4} - {2} - {3})       # {1, 4}
```

**In-place:** `set1 -= set2` or `set1.difference_update(set2)` removes from `set1` every element that appears in `set2`.

```python
a = {1, 2, 3}
a -= {2, 99}                    # a → {1, 3}

b = {1, 2, 3}
b.difference_update({2, 99})    # b → {1, 3} — same effect
```

**Real use:** find users who visited site A but not site B, or permissions one role has that another lacks.

```python
all_users = {"alice", "bob", "carol"}
premium = {"alice", "carol"}
free_only = all_users - premium       # {"bob"}
```

### 4. Symmetric Difference (`^` or `.symmetric_difference()`) — in one set or the other, not both

**Symmetric difference** returns elements that appear in **exactly one** of the two sets — not in the overlap.

```python
print(set1 ^ set2)                    # {1, 2, 4, 5}
print(set1.symmetric_difference(set2)) # {1, 2, 4, 5}

# Equivalent mental model:
print((set1 | set2) - (set1 & set2))  # {1, 2, 4, 5}
```

**In-place:** `set1 ^= set2` or `set1.symmetric_difference_update(set2)` keeps only elements that are in one set but not both.

```python
a = {1, 2, 3}
a ^= {3, 4, 5}                              # a → {1, 2, 4, 5}

b = {1, 2, 3}
b.symmetric_difference_update({3, 4, 5})    # b → {1, 2, 4, 5} — same effect
```

**Real use:** find what changed between two versions — items added or removed, but not unchanged.

```python
yesterday = {"apple", "banana", "cherry"}
today = {"apple", "banana", "date"}
changed = yesterday ^ today           # {"cherry", "date"}
```

### 5. Subset and Superset (`<=`, `>=`, `.issubset()`, `.issuperset()`)

These **compare** sets rather than build a new one. They return `True` or `False`.

**Subset** — every element of the first set is also in the second:

```python
a = {1, 2}
b = {1, 2, 3, 4}

print(a <= b)              # True — a is a subset of b
print(a.issubset(b))       # True
print(b >= a)              # True — b is a superset of a
print(b.issuperset(a))     # True

print(a <= {1, 2})         # True — every set is a subset of itself
print(a < {1, 2, 3})       # True — proper subset (strictly smaller)
```

**Real use:** check whether required permissions are covered, or whether one category of tags fits inside another.

```python
required = {"read", "write"}
user_has = {"read", "write", "delete"}
print(required <= user_has)   # True — user has everything required
```

### Set Operations

| Operation            | Operator | In-place  |
| -------------------- | -------- | --------- |
| Union                | `a \| b` | `a \|= b` |
| Intersection         | `a & b`  | `a &= b`  |
| Difference           | `a - b`  | `a -= b`  |
| Symmetric Difference | `a ^ b`  | `a ^= b`  |

### Method Equivalents

| Operation            | Method                       | In-place Method                    |
|----------------------|------------------------------|------------------------------------|
| Union                | `a.union(b)`                 | `a.update(b)`                      |
| Intersection         | `a.intersection(b)`          | `a.intersection_update(b)`         |
| Difference           | `a.difference(b)`            | `a.difference_update(b)`           |
| Symmetric Difference | `a.symmetric_difference(b)`  | `a.symmetric_difference_update(b)` |

### Set Relationships

| Relationship   | Operator | Method            |
|----------------|----------|-------------------|
| Subset         | `a <= b` | `a.issubset(b)`   |
| Proper Subset  | `a < b`  | —                 |
| Superset       | `a >= b` | `a.issuperset(b)` |

Methods accept **multiple arguments** (`union(a, b, c)` or `intersection_update(a, b, c)`), while operators chain only two at a time unless you parenthesize: `a | b | c`.

---

## Set Methods Summary

| Method                               | Description                                  | Operator |
| ------------------------------------ | -------------------------------------------- | -------- |
| `add(item)`                          | Add one element; ignores duplicates          | —        |
| `update(*iterables)`                 | Add many elements; in-place union            | `\|=`    |
| `remove(item)`                       | Remove element; raises `KeyError` if missing | —        |
| `discard(item)`                      | Remove element safely; no error if missing   | —        |
| `pop()`                              | Remove and return an arbitrary element       | —        |
| `clear()`                            | Remove all elements                          | —        |
| `union(*others)`                     | All unique elements from all sets            | `\|=`    |
| `intersection(*others)`              | Elements common to all sets                  | `&`      |
| `intersection_update(*others)`       | In-place intersection                        | `&=`     |
| `difference(*others)`                | In first set but not in any other            | `-`      |
| `difference_update(*others)`         | In-place difference                          | `-=`     |
| `symmetric_difference(other)`        | Elements in exactly one of two sets          | `^`      |
| `symmetric_difference_update(other)` | In-place symmetric difference                | `^=`     |
| `issubset(other)`                    | True if every element is in `other`          | `<=`     |
| `issuperset(other)`                  | True if `other` is a subset of this set      | `>=`     |
| `isdisjoint(other)`                  | True if the two sets share no elements       | —        |
| `copy()`                             | Shallow copy of the set                      | —        |

**`isdisjoint(other)`** — checks whether two sets have **no** elements in common (same as `len(a & b) == 0`, but clearer):

```python
{1, 2}.isdisjoint({3, 4})    # True — no overlap
{1, 2}.isdisjoint({2, 3})    # False — 2 is shared
```

**`copy()`** — creates a new set with the same elements. Assignment (`b = a`) makes both names point to the **same** set; changing one changes both:

```python
a = {1, 2, 3}
b = a.copy()     # independent copy
b.add(4)
print(a)         # {1, 2, 3} — unchanged
print(b)         # {1, 2, 3, 4}
```

---

## Membership Testing

Sets are extremely fast for checking if an item exists:

```python
large_set = set(range(1000000))

# Very fast
if 999999 in large_set:
    print("Found!")
```

This is much faster than checking in a list.

---

## Real-World Examples

### 1. Removing Duplicates from a List

```python
data = ["apple", "banana", "apple", "cherry", "banana"]
unique = list(set(data))          # ['apple', 'banana', 'cherry']
```

### 2. Finding Common Items

```python
my_skills = {"Python", "SQL", "Git", "Docker"}
job_requirements = {"Python", "Django", "SQL", "AWS"}

common = my_skills & job_requirements
print(f"Matching skills: {common}")
```

### 3. Finding Unique Visitors

```python
visitors = ["user1", "user2", "user1", "user3", "user2"]
unique_visitors = set(visitors)
print(len(unique_visitors))       # Number of unique visitors
```

---

## FrozenSet

A **frozenset** is an immutable version of a set:

```python
frozen = frozenset([1, 2, 3, 4])

# Can be used as dictionary key
my_dict = {frozen: "some value"}
```

---

## Common Mistakes & Gotchas

1. **Using `{}` creates an empty dictionary**, not a set. Use `set()` instead.
2. **Sets are unordered** — never rely on element order.
3. **Only hashable (immutable) elements** can be added.
4. **Modifying a set while iterating** can cause errors.
5. **Sets remove duplicates silently** — be careful when this matters.

---

## Best Practices

- Use sets when you need uniqueness
- Use sets for fast membership testing
- Use sets for mathematical operations (union, intersection, etc.)
- Convert back to list when order matters
- Prefer `discard()` over `remove()` when you don't want errors

---

## See also

- [[14 - Dictionaries]] — keys are unique like set elements; both use hashing
- [[12 - Lists]] — converting lists to sets removes duplicates
- [[13 - Tuples]] — immutable sequences that can be set elements
- [[07 - Comparisons and Logical Operators]] — membership with `in` is O(1) for sets
- [[10 - For loop]] — iterating over sets (order not guaranteed)

---

## → What's Next

Sets are excellent for working with unique data and performing fast membership tests and mathematical operations.

Continue with **[[16 - Defining Functions]]**
