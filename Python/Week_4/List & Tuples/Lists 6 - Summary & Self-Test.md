---
tags:
  - python
  - lists
  - summary
source: Automate the Boring Stuff with Python — Chapter 4
up: "[[Python Lists - MOC]]"
---

# Lists 6 — Summary & Self-Test

← Previous: [[Lists 5 - Mutability & References]]

---

## Quick-Reference Summary

| Operation | Syntax | Notes |
|---|---|---|
| Create | `[1, 2, 3]` | — |
| Index | `list[i]` | 0-based; negative counts from end |
| Slice | `list[a:b]` | excludes index `b` |
| Length | `len(list)` | — |
| Change value | `list[i] = x` | mutable |
| Concatenate | `list1 + list2` | new list |
| Replicate | `list * n` | new list |
| Delete by index | `del list[i]` | shifts remaining items |
| Delete by value | `list.remove(x)` | first match only |
| Add to end | `list.append(x)` | in place, returns `None` |
| Add at index | `list.insert(i, x)` | in place, returns `None` |
| Find index | `list.index(x)` | raises `ValueError` if missing |
| Sort | `list.sort()` / `list.sort(reverse=True)` | in place; use `key=str.lower` for case-insensitive |
| Membership | `x in list` / `x not in list` | Boolean |
| Copy (shallow) | `copy.copy(list)` | nested objects still shared |
| Copy (deep) | `copy.deepcopy(list)` | fully independent |

---

## Self-Test (from the book's practice questions)

1. What is `[]`? → an empty list.
2. Assign `'hello'` as the 3rd value in `spam = [2, 4, 6, 8, 10]` → `spam[2] = 'hello'`.
3. For `spam = ['a', 'b', 'c', 'd']`: what does `spam[-1]` evaluate to? → `'d'`.
4. What does `spam[:2]` evaluate to? → `['a', 'b']`.
5. Difference between `.append()` and `.insert()`? → append always adds to the end; insert lets you specify the index.
6. Two ways to remove values from a list? → `del list[i]` (by index) or `list.remove(value)` (by value).
7. Name ways lists are similar to strings → both support indexing, slicing, `len()`, `for` loops, `in`/`not in`.
8. Difference between lists and tuples? → tuples are immutable.
9. How do you write a tuple containing just the integer `42`? → `(42,)`.
10. Variables that "contain" lists don't actually contain the list directly — what do they contain? → a **reference** (pointer) to the list.
11. Difference between `copy.copy()` and `copy.deepcopy()`? → shallow copy vs. fully recursive copy.

---