---
tags:
  - python
  - operators
  - summary
source: Combines general Python reference across this note series
up: "[[Python Assignment Operators - MOC]]"
---

# Assignment 6 — Summary & Self-Test

← Previous: [[Assignment 5 - Beyond the Basics]]

---

## Quick-Reference Summary

| Category | Operators | Notes |
|---|---|---|
| Basic | `=` | binds a name to an object |
| Chained | `a = b = c = 5` | all names point to the SAME object |
| Arithmetic augmented | `+=` `-=` `*=` `/=` `//=` `%=` `**=` | shorthand; mutates in place for lists |
| Bitwise augmented | `&=` `\|=` `^=` `<<=` `>>=` | flag manipulation, XOR ciphers, permission bits |
| Multiple assignment | `a, b, c = seq` | count must match exactly |
| Starred unpacking | `first, *rest = seq` | `rest` is always a list; only one `*` allowed |
| Assignment expression | `:=` (walrus, 3.8+) | assigns AND evaluates inside an expression |
| Scope-crossing | `global`, `nonlocal` | needed to assign to an outer-scope variable from inside a function |

---

## Self-Test

1. What's the difference between `a = b = 5` and `a, b = 5, 5`? → both end with the same values, but chained assignment binds both names to the literal *same* object (relevant for mutables); tuple unpacking assigns from a packed tuple of two separate values.
2. Why does `x = y = []` followed by `x.append(1)` also change `y`? → both names reference the identical list object; `.append()` mutates it in place.
3. What's the difference in behavior between `a += [3]` and `a = a + [3]` for a list? → `+=` mutates the existing list object in place (other references see the change); `a + [3]` builds a brand-new list and rebinds `a` only.
4. Why doesn't the `+=` mutation gotcha apply to strings or tuples? → they're immutable — there's no in-place mutation path available, so `+=` always creates a new object and rebinds.
5. How do you set, clear, toggle, and check a specific bit using augmented bitwise assignment? → set: `flags |= bit`; clear: `flags &= ~bit`; toggle: `flags ^= bit`; check: `bool(flags & bit)`.
6. What type does the starred variable become in `first, *rest = (1, 2, 3)`? → a `list` — `[2, 3]`, even though it was unpacked from a tuple.
7. What does the walrus operator (`:=`) let you do that plain `=` can't? → assign a value *inside* an expression (e.g., inside an `if` or `while` condition), since `:=` is an expression, not a statement.
8. Does Python enforce `ALL_CAPS` variables as unreassignable? → No — it's purely a naming convention; Python will let you reassign them freely.
9. Why does incrementing a global variable inside a function raise `UnboundLocalError` without the `global` keyword? → assigning to a name anywhere inside a function makes Python treat it as local to that function by default; without `global`, the increment tries to read a local variable before it's been locally assigned.
10. What special method does `+=` call if the target's type defines it, and what does that determine? → `__iadd__` — if present, it mutates in place; if absent, Python falls back to `__add__`, which returns a new object.

---

## Related Notes
- [[Python Assignment Operators - MOC]]
- [[Operators 1 - Assignment Operators]]
- [[Python Lists - MOC]]
- [[Python Booleans - MOC]]
