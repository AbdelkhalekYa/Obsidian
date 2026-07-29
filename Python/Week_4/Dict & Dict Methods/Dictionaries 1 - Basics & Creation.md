---
tags:
  - python
  - dictionaries
source: Automate the Boring Stuff with Python — Chapter 5 (intro)
up: "[[Python Dictionaries - MOC]]"
---

# Dictionaries 1 — Basics & Creation

> [!info] Why this matters
> Dictionaries are how you represent **structured, labeled data** — config files, parsed JSON API responses, `{symbol_name: address}` tables when scripting exploits, `{opcode: handler}` maps when reversing a custom VM. If lists are "an ordered row," dictionaries are "a labeled filing cabinet."

---

## 1. What Is a Dictionary?

A **dictionary** (`dict`) is a collection of multiple values, like a list — but instead of accessing items by numeric position, you access them by a **key**.

```python
myCat = {'size': 'fat', 'color': 'gray', 'disposition': 'loud'}
myCat['size']   # 'fat'
```

- Written with curly braces `{}`.
- Each `key: value` pairing is called a **key-value pair**.
- Keys can be strings, numbers, or tuples (anything **immutable/hashable** — same rule as [[Sets 1 - Basics & Creation|set elements]]). Keys do **not** have to start at 0 or be sequential.

```python
spam = {12345: 'Luggage Combination', 42: 'The Answer'}
```

> [!tip] Mental model
> A list is like a numbered row of mailboxes. A dictionary is like a filing cabinet where every folder has a **name tag** (the key) instead of a number — you grab a folder by its label, not its position in the drawer.

---

## 2. Accessing Values

```python
myCat = {'size': 'fat', 'color': 'gray', 'disposition': 'loud'}
'My cat has ' + myCat['color'] + ' fur.'   # 'My cat has gray fur.'
```

### `KeyError` — the dict equivalent of `IndexError`

```python
spam = {'name': 'Zophie', 'age': 7}
spam['color']
# KeyError: 'color'
```
Trying to access a key that doesn't exist raises `KeyError`, just like an out-of-range index raises `IndexError` on a list. Section 3 ([[Dictionaries 3 - Safe Access - get() & setdefault()]]) covers how to avoid this.

---

## 3. Dictionary vs. List — Core Comparison
#comparison 

| Feature | List | Dictionary |
|---|---|---|
| Access by | index (int, ordered position) | key (any immutable/hashable type) |
| Equality check | `['a','b'] == ['b','a']` → `False` (order matters) | `{'a':1,'b':2} == {'b':2,'a':1}` → `True` (order doesn't matter) |
| Sliceable? | ✅ Yes | ❌ No |
| Missing item error | `IndexError` | `KeyError` |
| Duplicate keys/indexes | — | ❌ not possible — assigning to an existing key overwrites it |

```python
spam  = ['cats', 'dogs', 'moose']
bacon = ['dogs', 'moose', 'cats']
spam == bacon   # False — order matters for lists

eggs = {'name': 'Zophie', 'species': 'cat', 'age': '8'}
ham  = {'species': 'cat', 'age': '8', 'name': 'Zophie'}
eggs == ham     # True — order doesn't matter for dict equality
```

> [!note] Compare to sets
> This "order doesn't matter for equality" behavior is the same rule [[Sets 4 - Comparisons, Subsets & Frozensets|sets]] follow — both are built on the same underlying hash-table mechanism. The difference: a set only stores values, a dict stores key→value pairs.

---

## 4. Creating Dictionaries — All the Ways

```python
# Literal
myCat = {'size': 'fat', 'color': 'gray'}

# Empty dict
empty = {}          # NOTE: {} is an empty DICT, not an empty set — see [[Sets 1 - Basics & Creation]]

# From a list of 2-item tuples/lists
dict([('a', 1), ('b', 2)])     # {'a': 1, 'b': 2}

# From two parallel lists with zip()
keys = ['a', 'b', 'c']
values = [1, 2, 3]
dict(zip(keys, values))         # {'a': 1, 'b': 2, 'c': 3}

# Using keyword arguments (keys must be valid identifiers, no quotes needed)
dict(size='fat', color='gray')  # {'size': 'fat', 'color': 'gray'}
```

---

## Next
→ [[Dictionaries 2 - Iterating, Keys, Values & Items]]
