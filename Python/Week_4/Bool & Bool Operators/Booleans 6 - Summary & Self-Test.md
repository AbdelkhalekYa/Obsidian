---
tags: [python, programming, booleans, summary]
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Booleans - MOC]]"
---

# Booleans 6 — Summary & Self-Test

← Previous: [[Booleans 5 - Beyond the Basics]]

---

## Quick-Reference Summary

| Concept | Syntax | Notes |
|---|---|---|
| The type | `True`, `False` | keywords, capitalized, `bool` is a subclass of `int` |
| Equal / not equal | `==`, `!=` | works on any comparable types |
| Ordering | `<`, `>`, `<=`, `>=` | — |
| Chained comparison | `1 < x < 10` | same as `1 < x and x < 10` |
| Identity vs equality | `is` vs `==` | `is` checks same object in memory; use `is None` specifically |
| AND | `and` | both sides must be truthy |
| OR | `or` | at least one side must be truthy |
| NOT | `not` | flips truthy ↔ falsy |
| Short-circuit | `and`/`or` skip evaluating the right side once the result is known | used deliberately as a "guard clause" |
| Falsy values | `False, None, 0, '', [], {}, set(), ()` | everything else is truthy |
| Convert to bool | `bool(x)` | — |
| All items truthy? | `all(iterable)` | `True` on empty iterable |
| Any item truthy? | `any(iterable)` | `False` on empty iterable |
| Assign-and-test | `:=` (walrus, 3.8+) | assigns inside an expression |
| Negation simplification | De Morgan's laws | `not (A and B) == (not A) or (not B)` |

### Operator precedence (high → low)
`()` → arithmetic → comparisons (`==`,`<`,`in`,`is`, etc.) → `not` → `and` → `or`

---

## Self-Test

1. What are the only two values of type `bool`? → `True` and `False`.
2. Is `bool('False')` `True` or `False`? → `True` — it's a non-empty string, so it's truthy regardless of what text it contains.
3. Name three falsy values besides `False` itself. → any of: `None`, `0`, `''`, `[]`, `{}`, `set()`, `()`.
4. Difference between `==` and `is`? → `==` checks equal *content*; `is` checks the exact same object in memory (identity).
5. What does `1 < x < 10` mean? → shorthand for `1 < x and x < 10` (a chained comparison).
6. What does `False and expensive_function()` do, and why? → never calls `expensive_function()` — `and` short-circuits once the left side is already `False`.
7. What does `0 or 10` evaluate to? → `10` — `0` is falsy, so `or` moves on and returns the next value.
8. What does `5 and 10` evaluate to? → `10` — both are truthy, `and` returns the last evaluated operand.
9. What does `all([])` return, and why is that the "sensible" default? → `True` — there are no items to fail the condition, so it vacuously holds.
10. What does `any([])` return? → `False` — there's nothing truthy to find.
11. What's the guard-clause purpose of `if my_list and my_list[0] == 'x':`? → the first check (`my_list` truthy) prevents `my_list[0]` from raising `IndexError` on an empty list, thanks to short-circuiting.
12. What's the walrus operator, and what problem does it solve? → `:=`, introduced in Python 3.8; lets you assign a value and test it in the same expression, avoiding a separate assignment line (e.g., in `while (chunk := file.read(1024)):`).

---

## Related Notes
- [[Python Booleans - MOC]]
- [[Python Lists - MOC]]
- [[Python Sets - MOC]]
- [[Python Dictionaries - MOC]]
