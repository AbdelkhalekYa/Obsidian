---
tags:
  - python
  - sets
  - summary
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Sets - MOC]]"
---

# Sets 6 — Summary & Self-Test

← Previous: [[Sets 5 - Performance & Real-World Use Cases]]

---

## Quick-Reference Summary

| Operation | Syntax | Notes |
|---|---|---|
| Create | `{1, 2, 3}` or `set(iterable)` | `{}` alone is a **dict**, not a set |
| Empty set | `set()` | there is no `{}` empty-set literal |
| Length | `len(s)` | — |
| Membership | `x in s` / `x not in s` | O(1) average — fast |
| Add one | `s.add(x)` | no-op if already present |
| Add many | `s.update(iterable)` | accepts list/tuple/string/set |
| Remove (error if missing) | `s.remove(x)` | `KeyError` if not found |
| Remove (safe) | `s.discard(x)` | no error if missing |
| Remove arbitrary | `s.pop()` | `KeyError` if empty; no control over which item |
| Empty the set | `s.clear()` | — |
| Copy | `s.copy()` | shallow copy, independent set |
| Union | `a \| b` / `a.union(b)` | everything from both |
| Intersection | `a & b` / `a.intersection(b)` | only shared elements |
| Difference | `a - b` / `a.difference(b)` | in `a`, not in `b` (order matters) |
| Symmetric difference | `a ^ b` / `a.symmetric_difference(b)` | in either, not both |
| Subset check | `a <= b` / `a.issubset(b)` | is `a` fully inside `b`? |
| Superset check | `a >= b` / `a.issuperset(b)` | does `a` fully contain `b`? |
| Disjoint check | `a.isdisjoint(b)` | zero overlap? |
| Immutable version | `frozenset(iterable)` | hashable — usable as dict key / set member |

---

## Self-Test

1. What does `type({})` evaluate to, and why does this surprise beginners? → `<class 'dict'>` — `{}` is reserved for empty dict; use `set()` for an empty set.
2. What happens to `{1, 1, 2, 2, 3}` when evaluated? → `{1, 2, 3}` — duplicates are automatically collapsed.
3. Difference between `.remove()` and `.discard()`? → `.remove()` raises `KeyError` if the value isn't present; `.discard()` does nothing.
4. What's the result of `{1, 2, 3} - {2, 3, 4}`? → `{1}`.
5. What's the result of `{1, 2, 3} ^ {2, 3, 4}`? → `{1, 4}`.
6. Why can't a regular `set` be used as a dictionary key? → because `set` is mutable, and dict keys must be hashable/immutable — use `frozenset` instead.
7. Why is `x in my_set` generally faster than `x in my_list`? → sets use a hash table for O(1) average lookups; lists require a linear O(n) scan.
8. How do you deduplicate a list **while preserving order**? → `list(dict.fromkeys(my_list))` (plain `set()` does not preserve order).
9. What's the difference between `a < b` and `a <= b` for sets? → `<` requires a *proper* subset (subset and not equal); `<=` allows equality.

---