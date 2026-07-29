---
tags: [python, programming, tuples]
source: "Recap of Lists 4 (from the PDF's brief mention) plus supplementary depth"
up: "[[Python Tuples - MOC]]"
---

# Tuples 1 — Basics, Creation & Immutability

> [!info] Why this matters
> Tuples show up constantly in places you might not expect: every function that returns "more than one value" in Python is actually returning a tuple. `.items()` on a dict (from [[Dictionaries 2 - Iterating, Keys, Values & Items]]) yields tuples. Coordinates, RGB colors, and database rows are natural tuples. Once you notice them, they're everywhere.

---

## 1. What Is a Tuple?

A **tuple** is an ordered, **immutable** sequence of values — essentially a list that can never be changed after creation.

```python
eggs = ('hello', 42, 0.5)
eggs[0]     # 'hello'
eggs[1:3]   # (42, 0.5)
len(eggs)   # 3
```

- Written with parentheses `()` (though the parentheses are technically optional in many contexts — see below).
- Supports **indexing and slicing** exactly like a list (already covered in [[Lists 1 - Basics & Indexing]] — the same rules apply).
- **Cannot** be modified, added to, or removed from once created:
  ```python
  eggs[1] = 99   # TypeError: 'tuple' object does not support item assignment
  eggs.append(1)   # AttributeError: 'tuple' object has no attribute 'append'
  ```

---

## 2. Creating Tuples — All the Ways

```python
t1 = (1, 2, 3)        # standard literal
t2 = 1, 2, 3            # parentheses are OPTIONAL — this is called "tuple packing"
t3 = tuple([1, 2, 3])     # from a list
t4 = tuple('abc')           # from a string -> ('a', 'b', 'c')
t5 = ()                       # empty tuple
```

### The single-item comma trap (recap)
```python
t = (42,)     # a tuple containing one item — the comma is REQUIRED
not_a_tuple = (42)   # just the int 42 in parentheses — NOT a tuple!

type((42,))    # <class 'tuple'>
type((42))      # <class 'int'>
```
> [!warning] This is the #1 tuple mistake
> Forgetting the trailing comma on a single-item tuple is extremely common, and Python won't warn you — `(42)` silently evaluates to plain `42`. Always double-check single-item tuples specifically.

---

## 3. Why Does Immutability Exist? (The Actual Reasoning)

It's not an arbitrary restriction — immutability buys you three real things:

1. **Safety by intent** — a tuple signals to anyone reading the code "this sequence is not meant to change," the same way `const` does in other languages. If you see a tuple, you know nothing later in the code silently altered it.
2. **Hashability** — because tuples can't change, they *can* be hashed (if their contents are also hashable), which means they can be used as **dictionary keys** or **set members** — something lists can never do (see [[Sets 1 - Basics & Creation]] for why lists fail this). Covered in depth in [[Tuples 3 - Beyond the PDF]].
3. **Minor performance edge** — Python can make small optimizations for immutable objects (see [[Lists 4 - Tuples & Strings (List-Like Types)]]).

---

## 4. Nested Tuples & Mixed Nesting

Tuples can contain any type, including other tuples, lists, or dicts:

```python
point3d = (1, 2, 3)
line = ((0, 0), (5, 5))       # tuple of tuples — e.g., two coordinate points
record = ('Alice', 30, ['python', 'ctf'])   # tuple containing a MUTABLE list
```

> [!warning] "Immutable" only applies one level deep
> A tuple itself can't be reassigned or resized, but if it **contains** a mutable object (like a list), that inner object can still be changed:
> ```python
> record = ('Alice', [1, 2, 3])
> record[1].append(4)     # totally legal! the LIST inside the tuple is still mutable
> print(record)              # ('Alice', [1, 2, 3, 4])
> record[0] = 'Bob'            # TypeError -- the tuple's own slots are still locked
> ```
> This is the tuple version of the reference-sharing behavior from [[Lists 5 - Mutability & References]] — immutability protects the tuple's *structure* (what's at each position), not necessarily the *contents* of mutable objects sitting inside it.

---

## Next
→ [[Tuples 2 - Methods, Packing & Unpacking]]
