---
tags: [python, programming, tuples, summary]
source: Combines general Python reference across this note series
up: "[[Python Tuples - MOC]]"
---

# Tuples 4 — Summary & Self-Test

← Previous: [[Tuples 3 - Beyond the PDF]]

---

## Quick-Reference Summary

| Operation | Syntax | Notes |
|---|---|---|
| Create | `(1, 2, 3)` or `1, 2, 3` | parentheses optional in many contexts |
| Single-item tuple | `(42,)` | comma is **required** — `(42)` is just an int |
| Empty tuple | `()` | — |
| From another type | `tuple(x)` | works on lists, strings, any iterable |
| Index / slice | `t[0]`, `t[1:3]` | same rules as lists |
| Length | `len(t)` | — |
| Count occurrences | `t.count(x)` | one of only two methods |
| Find index | `t.index(x)` | raises `ValueError` if missing |
| Modify | ❌ not possible | `TypeError` — tuples are immutable |
| Packing | `x = 1, 2, 3` | groups values into a tuple |
| Unpacking | `a, b, c = t` | must match length exactly (or use `*`) |
| Starred unpacking | `first, *rest = t` | `rest` becomes a **list** |
| Swap trick | `a, b = b, a` | pack + unpack in one line |
| As dict key / set member | `{(1,2): 'val'}` | only possible because tuples are hashable |
| Named fields | `collections.namedtuple` | tuple performance + `.field` readability |

---

## Self-Test

1. What's the only real functional difference between a list and a tuple? → tuples are immutable — can't be modified after creation.
2. Why does `(42)` NOT create a tuple? → without a trailing comma, parentheses are just grouping syntax; `(42,)` is required.
3. What are the only two methods a tuple has, and why so few? → `.count()` and `.index()` — because tuples are immutable, no method that adds/removes/reorders items can exist.
4. Can a tuple contain a mutable object like a list? Can that inner list still be changed? → yes, and yes — immutability only locks the tuple's own structure (what's at each position), not the contents of mutable objects stored inside it.
5. Why can tuples be used as dictionary keys while lists cannot? → tuples are hashable (immutable); lists are mutable and therefore unhashable.
6. What does `a, b = b, a` do, and how does it avoid needing a temp variable? → swaps the two values; Python packs the right side into a temporary tuple first, then unpacks it into the left side in one step.
7. What type does the `middle` variable become in `first, *middle, last = (1,2,3,4,5)`? → a `list` — `[2, 3, 4]`, even though it was unpacked from a tuple.
8. What does `collections.namedtuple` give you that a plain tuple doesn't? → named field access (`p.x` instead of `p[0]`) while keeping tuple immutability, performance, and hashability.
9. Give one real-world CTF/security example of tuples-as-dict-keys or tuples-as-set-members. → tracking visited `(x, y)` coordinates or `(node_a, node_b)` graph edges during BFS/DFS traversal of a binary's control-flow graph.

---

## Related Notes
- [[Python Tuples - MOC]]
- [[Lists 4 - Tuples & Strings (List-Like Types)]]
- [[Python Sets - MOC]]
- [[Python Dictionaries - MOC]]
