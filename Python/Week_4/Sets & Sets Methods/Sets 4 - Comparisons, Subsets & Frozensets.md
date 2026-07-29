---
tags:
  - python
  - sets
  - methods
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Sets - MOC]]"
---

# Sets 4 — Comparisons, Subsets & Frozensets

← Previous: [[Sets 3 - Set Methods]]

---

## 1. Subset & Superset Checks

```python
a = {1, 2}
b = {1, 2, 3, 4}

a.issubset(b)      # True   -> everything in a is also in b
b.issuperset(a)     # True   -> b contains everything in a
a.issubset(a)       # True   -> a set is always a subset of itself
```

| Method | Meaning |
|---|---|
| `a.issubset(b)` | is every element of `a` also in `b`? |
| `a.issuperset(b)` | does `a` contain every element of `b`? |
| `a.isdisjoint(b)` | do `a` and `b` share **zero** elements? |

```python
{1, 2}.isdisjoint({3, 4})   # True — no overlap
{1, 2}.isdisjoint({2, 3})   # False — '2' is shared
```

---

## 2. Comparison Operators (Same Meaning, Operator Form)

```python
a = {1, 2}
b = {1, 2, 3}

a <= b    # True  -> a is a subset of b (same as a.issubset(b))
a < b     # True  -> a is a PROPER subset (subset AND not equal)
b >= a    # True  -> b is a superset of a
b > a     # True  -> proper superset
a == b    # False -> equal sets (same elements, order irrelevant)
```

> [!note] Proper vs. non-proper
> `<=`/`>=` allow equality (`{1,2} <= {1,2}` → `True`). `<`/`>` require the sets to actually differ (`{1,2} < {1,2}` → `False`, but `{1,2} < {1,2,3}` → `True`). This mirrors regular number comparisons.

### Set equality ignores order and duplicates by definition
```python
{1, 2, 3} == {3, 2, 1}   # True — order never matters for sets
```
Compare this to lists, where `[1,2,3] == [3,2,1]` is `False` (see [[Python - Dictionaries]] for the same "order doesn't matter for equality" idea applied to dicts).

---

## 3. `frozenset` — The Immutable Version

A **`frozenset`** is exactly like a `set`, except it's **immutable** — no `.add()`, `.remove()`, `.update()`, etc. once created.

```python
fs = frozenset([1, 2, 3])
fs.add(4)   # AttributeError: 'frozenset' object has no attribute 'add'
```

### Why would you want an immutable set?

**Because it becomes hashable** — meaning a `frozenset` can be:
- used as a **dictionary key**
- placed **inside another set**

Neither of those is possible with a regular (mutable) `set`:

```python
regular_set = {1, 2, 3}
{regular_set: 'value'}          # TypeError: unhashable type: 'set'
{regular_set, {4, 5}}            # TypeError: unhashable type: 'set'

frozen = frozenset([1, 2, 3])
{frozen: 'value'}                # OK — {frozenset({1, 2, 3}): 'value'}
{frozen, frozenset([4, 5])}       # OK — a set containing frozensets
```

### List / Set / Frozenset — full comparison

| Feature | `list` | `set` | `frozenset` |
|---|---|---|---|
| Syntax | `[1, 2]` | `{1, 2}` | `frozenset([1, 2])` |
| Ordered | ✅ | ❌ | ❌ |
| Duplicates allowed | ✅ | ❌ | ❌ |
| Mutable | ✅ | ✅ | ❌ |
| Hashable (usable as dict key / set member) | ❌ | ❌ | ✅ |
| Indexable (`x[0]`) | ✅ | ❌ | ❌ |
| Union/intersection/etc. operators | ❌ | ✅ | ✅ |

---

## Next
→ [[Sets 5 - Performance & Real-World Use Cases]]
