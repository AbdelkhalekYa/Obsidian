---
tags: [python, programming, operators, comparison, summary]
source: Combines general Python reference across this note series
up: "[[Python Comparison Operators - MOC]]"
---

# Comparison 6 — Summary & Self-Test

← Previous: [[Comparison 5 - Beyond the Basics]]

---

## Quick-Reference Summary

| Topic | Key fact |
|---|---|
| The six operators | `==` `!=` `<` `>` `<=` `>=` — always return `bool` |
| Cross-type equality | allowed, just `False` (e.g. `42 == '42'`) |
| Cross-type ordering | `TypeError` unless part of the numeric tower |
| Numeric tower | `int`/`float`/`bool` compare by value across types |
| List/tuple equality | requires same type — `[1,2] == (1,2)` is `False` |
| Sequence ordering | lexicographic — element-by-element, first difference decides |
| Short sequence vs. prefix | shorter one is "less" if all shared elements match |
| Set ordering (`<`/`>`) | means subset/superset, NOT lexicographic |
| Dict ordering | not supported — `TypeError` |
| Chained comparisons | `1 < x < 10`; middle expression evaluated only once |
| `float('nan')` | never equal to anything, including itself — use `math.isnan()` |
| `is` | identity check — correct almost only for `is None` |
| `==` | value/content check — correct for everything else |
| Small int / string caching | CPython implementation detail — never rely on `is` for it |
| Custom comparisons | `__eq__`, `__lt__`, etc.; `functools.total_ordering` fills in the rest |
| Sort by custom rule | `.sort(key=...)` / `sorted(iterable, key=...)` |
| Timing-safe comparison | `hmac.compare_digest()` for secrets/tokens |

---

## Self-Test

1. Why does `42 < '42'` raise `TypeError` while `42 == '42'` just returns `False`? → equality across unrelated types is always well-defined (they're simply not equal); ordering has no sensible meaning across unrelated types, so Python refuses rather than guessing.
2. Is `[1, 2, 3] == (1, 2, 3)` `True` or `False`? → `False` — list and tuple are different types, even with matching contents.
3. What does `[1, 2] < [1, 2, 3]` evaluate to, and why? → `True` — all shared elements are equal, and the shorter sequence is considered "less."
4. What does `nan == nan` evaluate to for `nan = float('nan')`, and what should you use instead to check for NaN? → `False`; use `math.isnan(x)`.
5. What's the difference between `==` and `is`? → `==` checks value/content (customizable via `__eq__`); `is` checks object identity (never customizable).
6. Why might `a is b` return `True` for `a = 5, b = 5` but `False` for `a = 1000, b = 1000`? → CPython caches small integers (roughly -5 to 256) as shared objects; larger integers are separate objects each time.
7. When is `is` the *correct*, idiomatic choice? → checking against `None` (`x is None` / `x is not None`).
8. What does `functools.total_ordering` do? → given `__eq__` and one ordering method (e.g. `__lt__`), it automatically derives the rest (`__le__`, `__gt__`, `__ge__`).
9. How do you sort a list of dictionaries by a specific field? → `my_list.sort(key=lambda d: d['field'])` or `sorted(my_list, key=lambda d: d['field'])`.
10. Why should you use `hmac.compare_digest()` instead of `==` when comparing a secret token? → `==` short-circuits at the first mismatched character, which can leak timing information; `compare_digest()` runs in constant time regardless of where the mismatch occurs.

---

## Related Notes
- [[Python Comparison Operators - MOC]]
- [[Operators 2 - Comparison Operators]]
- [[Booleans 2 - Comparison Operators]]
- [[Python Sets - MOC]]
