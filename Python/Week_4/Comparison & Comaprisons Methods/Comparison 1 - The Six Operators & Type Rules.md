---
tags:
  - python
  - comparison
  - operators
source: Recap of Operators 2 / Booleans 2 plus supplementary type-rule depth
up: "[[Python Comparison Operators - MOC]]"
---

# Comparison 1 — The Six Operators & Type Rules

---

## 1. Recap: The Six Operators

| Operator | Meaning |
|---|---|
| `==` | equal to |
| `!=` | not equal to |
| `<` | less than |
| `>` | greater than |
| `<=` | less than or equal to |
| `>=` | greater than or equal to |

Every comparison always evaluates to a `bool` — `True` or `False`. Full basics already covered in [[Operators 2 - Comparison Operators]] and [[Booleans 2 - Comparison Operators]] — this series builds on that foundation.

---

## 2. Equality (`==`, `!=`) Works Across (Almost) Any Type — Ordering Doesn't

This is the key distinction this page adds: **`==`/`!=` are far more permissive than `<`/`>`/`<=`/`>=`.**

```python
42 == '42'          # False -- allowed, just evaluates to False (no error)
42 == [1, 2]           # False -- allowed, different types are simply "not equal"
42 < '42'                # TypeError: '<' not supported between instances of 'int' and 'str'
```

> [!note] Why the asymmetry?
> Equality between unrelated types is always a well-defined (if boring) question — "is an int equal to a list? No." But *ordering* unrelated types ("is 42 less than the string `'42'`?") has no sensible answer, so Python refuses outright with `TypeError` rather than guessing. Older Python 2 allowed cross-type ordering with arbitrary (and confusing) rules — Python 3 deliberately removed this.

---

## 3. The Numeric Tower — Numbers Compare Across Subtypes

Unlike the strict "different types are just unequal" rule above, Python's numeric types are a special case: `int`, `float`, `complex` (partially), and `bool` all compare **by mathematical value**, not by type.

```python
42 == 42.0          # True  -- int and float compare by numeric value
True == 1              # True  -- bool IS an int subtype (see [[Booleans 1 - Basics & Truthy-Falsy]])
False == 0                # True
1 < 1.5 < 2                  # True -- chained, mixing int and float freely
```

| Comparable across types? | Example |
|---|---|
| `int` ↔ `float` | ✅ `1 == 1.0` |
| `bool` ↔ `int` | ✅ `True == 1` |
| `int` ↔ `str` (equality) | ✅ but always `False`: `1 == '1'` → `False` |
| `int` ↔ `str` (ordering) | ❌ `TypeError` |
| `list` ↔ `tuple` (equality) | ❌ always `False`, even with identical contents: `[1,2] == (1,2)` → `False` |

> [!warning] `list == tuple` is always `False`, even with matching contents
> ```python
> [1, 2, 3] == (1, 2, 3)   # False -- different TYPES, even though the contents "look" the same
> ```
> Equality in Python generally requires the same type family (with the numeric tower as the deliberate exception above). This is a common surprise when converting between the two — see [[Tuples 1 - Basics, Creation & Immutability]] for `tuple()`/`list()` conversions.

---

## 4. `float('nan')` — The One Value That's Never Equal to Anything, Including Itself

```python
nan = float('nan')
nan == nan     # False!!
nan != nan       # True
```
This isn't a bug — it's the IEEE 754 floating-point standard's defined behavior for "Not a Number." Covered in more depth (with the practical implication) in [[Comparison 3 - Chained Comparisons & Pitfalls]].

---

## Next
→ [[Comparison 2 - Lexicographic Comparison of Sequences]]
