---
tags:
  - python
  - sets
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Sets - MOC]]"
---

# Sets 1 — Basics & Creation

> [!info] Why this matters
> Sets are the tool you reach for whenever you care about **"does this exist?" and "what's unique/overlapping between two groups?"** rather than order or position. In security/automation work: deduplicating scraped IPs, tracking visited nodes in a graph traversal (BFS/DFS over a binary's control-flow graph), or diffing two wordlists all lean on sets.

---

## 1. What Is a Set?

A **set** is an **unordered collection of unique, hashable values**.

```python
spam = {'cat', 'bat', 'rat', 'elephant'}
```

Three defining properties, all different from lists:

| Property | List | Set |
|---|---|---|
| Ordered? | ✅ Yes | ❌ No — no guaranteed order |
| Duplicates allowed? | ✅ Yes | ❌ No — duplicates are silently collapsed |
| Indexable (`x[0]`)? | ✅ Yes | ❌ No — sets can't be indexed or sliced |

```python
>>> {1, 2, 2, 3, 3, 3}
{1, 2, 3}   # duplicates are automatically removed
```

> [!tip] Mental model
> Think of a set like a bag of unique marbles, not a numbered row of mailboxes. You can ask "is this marble in the bag?" instantly, but you can't ask "what's marble #2?" — there is no #2, only "in the bag" or "not in the bag."

---

## 2. Creating a Set

### Literal syntax with curly braces
```python
spam = {'cat', 'bat', 'rat'}
```

> [!warning] The `{}` trap
> `{}` on its own creates an **empty dictionary**, not an empty set — dictionaries claimed the `{}` literal first. To make an empty set, you **must** use the `set()` constructor:
> ```python
> empty_dict = {}          # dict!
> empty_set  = set()       # set — correct way
> type({})                 # <class 'dict'>
> type(set())              # <class 'set'>
> ```

### The `set()` constructor — converting other types
```python
set([1, 2, 2, 3])        # {1, 2, 3}   from a list — dedupes automatically
set('hello')              # {'h', 'e', 'l', 'o'}   from a string — unique characters
set((1, 2, 3))            # {1, 2, 3}   from a tuple
set({'a': 1, 'b': 2})     # {'a', 'b'}  from a dict — takes the KEYS only
```

### Set comprehension (like a list comprehension, but with `{}`)
```python
squares = {x**2 for x in range(6)}
# {0, 1, 4, 9, 16, 25}
```

---

## 3. What Can Go Inside a Set? (Hashability)

Elements of a set must be **hashable** — meaning immutable. This is the same requirement as dictionary *keys* (see [[Python - Dictionaries]]).

```python
{1, 'two', 3.0, (4, 5)}   # OK — int, str, float, tuple are all hashable

{[1, 2], 3}                # TypeError: unhashable type: 'list'
{ {1, 2}, 3 }               # TypeError: unhashable type: 'set'
```

| Allowed inside a set | Not allowed inside a set |
|---|---|
| `int`, `float`, `str`, `bool` | `list` |
| `tuple` (if its contents are also hashable) | `dict` |
| `frozenset` (see [[Sets 4 - Comparisons, Subsets & Frozensets]]) | `set` (regular, mutable sets) |

This mirrors *why* sets are unordered and fast: internally a set is built on the same hash-table mechanism as a dictionary (just values only, no keys attached) — see [[Sets 5 - Performance & Real-World Use Cases]] for why that matters.

---

## 4. Basic Operations You Already Know

```python
spam = {'cat', 'bat', 'rat'}

len(spam)          # 3
'cat' in spam       # True   — very fast, see performance note
'dog' in spam       # False
'cat' not in spam   # False

for item in spam:   # order is NOT guaranteed
    print(item)
```

> [!warning] Don't rely on iteration order
> Since Python 3.7, dictionaries preserve insertion order — but **sets still do not** guarantee any particular order. Two runs of the same program can print set items in different sequences (though within one run, order is typically stable unless the set is mutated).

---

## Next
→ [[Sets 2 - Mathematical Set Operations]]
