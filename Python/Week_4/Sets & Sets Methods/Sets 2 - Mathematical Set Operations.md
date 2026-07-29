---
tags:
  - python
  - sets
  - methods
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Sets - MOC]]"
---

# Sets 2 — Mathematical Set Operations

← Previous: [[Sets 1 - Basics & Creation]]

This is where sets earn their keep — they implement real mathematical set theory (Venn-diagram operations) directly as operators.

---

## 1. Union (`|`) — Everything from Both

Combines all unique elements from both sets.

```python
a = {1, 2, 3}
b = {3, 4, 5}

a | b               # {1, 2, 3, 4, 5}
a.union(b)           # {1, 2, 3, 4, 5}   — method form, identical result
```

**Diagram:** everything inside either circle.

```
 A       B
┌───┐ ┌───┐
│ 1 │ │   │
│ 2 │3│ 4 │
│   │ │ 5 │
└───┘ └───┘
```

---

## 2. Intersection (`&`) — Only What's Shared

Elements present in **both** sets.

```python
a = {1, 2, 3}
b = {3, 4, 5}

a & b                # {3}
a.intersection(b)     # {3}
```

**Use case:** finding common elements between two data sets — e.g., IPs that appear in *both* today's and yesterday's log files.

---

## 3. Difference (`-`) — In One, Not the Other

Elements in `a` but **not** in `b`. **Order matters here** — `a - b` ≠ `b - a`.

```python
a = {1, 2, 3}
b = {3, 4, 5}

a - b                # {1, 2}   — in a, not in b
b - a                # {4, 5}   — in b, not in a
a.difference(b)       # {1, 2}
```

**Use case:** "which hosts from my scan list have I *not* already tested?" → `all_hosts - tested_hosts`.

---

## 4. Symmetric Difference (`^`) — In Either, but Not Both

Elements that are in exactly **one** of the two sets (excludes the overlap).

```python
a = {1, 2, 3}
b = {3, 4, 5}

a ^ b                          # {1, 2, 4, 5}   — everything except the shared '3'
a.symmetric_difference(b)       # {1, 2, 4, 5}
```

Equivalent to `(a | b) - (a & b)`.

---

## 5. Operator vs. Method — Side-by-Side

| Operation | Operator | Method | Notes |
|---|---|---|---|
| Union | `a \| b` | `a.union(b)` | method accepts *any* iterable (list, tuple...), operator requires both sides to be sets |
| Intersection | `a & b` | `a.intersection(b)` | same |
| Difference | `a - b` | `a.difference(b)` | same |
| Symmetric difference | `a ^ b` | `a.symmetric_difference(b)` | same |

```python
a = {1, 2, 3}
a.union([3, 4, 5])       # works — method form accepts a list directly
a | [3, 4, 5]             # TypeError — operator form requires a set on both sides
```

> [!tip] Rule of thumb
> Use the **operator** (`|`, `&`, `-`, `^`) when both sides are already sets — it's more concise. Use the **method** (`.union()`, etc.) when you need to combine a set with a list/tuple/other iterable without converting it first.

---

## 6. In-Place Versions (Update the Set Itself)

Each non-mutating operation above has an **in-place equivalent** that modifies the original set instead of returning a new one:

| Returns a new set | Modifies in place |
|---|---|
| `a \| b` / `a.union(b)` | `a.update(b)` |
| `a & b` / `a.intersection(b)` | `a.intersection_update(b)` |
| `a - b` / `a.difference(b)` | `a.difference_update(b)` |
| `a ^ b` / `a.symmetric_difference(b)` | `a.symmetric_difference_update(b)` |

```python
a = {1, 2, 3}
a.intersection_update({2, 3, 4})
print(a)   # {2, 3}   -- a itself was changed
```

This mirrors the list pattern of "operator returns new value" vs. "method mutates in place" — see [[Lists 5 - Mutability & References]] for the same idea applied to lists.

---

## Next
→ [[Sets 3 - Set Methods]]
