---
tags:
  - python
  - operators
  - assignment
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Assignment Operators - MOC]]"
---

# Assignment 2 — Arithmetic Augmented Assignment

← Previous: [[Assignment 1 - Basic & Chained Assignment]]

---

## 1. The Full Arithmetic Table

Augmented assignment operators are shorthand for "update a variable using its current value."

| Full form | Shorthand | Meaning |
|---|---|---|
| `spam = spam + 1` | `spam += 1` | add |
| `spam = spam - 1` | `spam -= 1` | subtract |
| `spam = spam * 1` | `spam *= 1` | multiply |
| `spam = spam / 1` | `spam /= 1` | true division (always returns float) |
| `spam = spam // 1` | `spam //= 1` | floor division |
| `spam = spam % 1` | `spam %= 1` | modulo (remainder) |
| `spam = spam ** 1` | `spam **= 1` | exponent |

```python
spam = 10
spam += 5    # 15
spam -= 3     # 12
spam *= 2      # 24
spam //= 5      # 4   (24 // 5 = 4, floor division)
spam **= 2       # 16
```

---

## 2. Works on Strings and Lists Too, Not Just Numbers

```python
spam = 'Hello'
spam += ' world!'      # 'Hello world!'  -- string concatenation shorthand

bacon = ['Zophie']
bacon *= 3               # ['Zophie', 'Zophie', 'Zophie']  -- list replication shorthand
```

---

## 3. Critical Subtlety: `+=` Mutates Lists In Place, But `+` Does Not

This is the single most important thing to understand on this page, and it's a very common source of bugs.

```python
a = [1, 2]
b = a            # b points to the SAME list as a
a += [3]           # in-place mutation — modifies the existing list object
print(b)              # [1, 2, 3]  <- b changed too!
```

```python
a = [1, 2]
b = a
a = a + [3]        # creates a BRAND-NEW list, then rebinds 'a' to it
print(b)              # [1, 2]  <- b is UNCHANGED — it still points to the original list
```

> [!warning] `+=` on a list behaves like `.extend()`, not like `+`
> For lists specifically, `a += [x]` is internally equivalent to `a.extend([x])` — it mutates the existing list object in place. `a = a + [x]`, by contrast, builds a completely new list and rebinds `a` to it, leaving anything else that pointed at the old list untouched. This is the exact same distinction from [[Lists 5 - Mutability & References]] (mutation vs. rebinding), just easy to miss because `+=` *looks* like a simple shorthand.

| | `a += [x]` | `a = a + [x]` |
|---|---|---|
| Behavior | mutates the existing list in place | creates a new list, rebinds `a` |
| Affects other variables pointing to the same list? | ✅ Yes | ❌ No |
| Under the hood | calls `a.__iadd__([x])` if it exists (lists define it) | calls `a.__add__([x])`, returns a new object |

(`__iadd__` and `__add__` are covered in more depth in [[Assignment 5 - Beyond the Basics]].)

### Strings never have this issue
```python
s = 'hello'
t = s
s += ' world'
print(t)   # 'hello' — unaffected, because strings are IMMUTABLE
```
Since strings can't be mutated at all (see [[Strings 1 - Literals, Escapes & Immutability]]), `s += ' world'` for a string *always* creates a new string object and rebinds `s` — there's no in-place mutation path available, so this particular gotcha simply doesn't apply to strings, tuples, or numbers (all immutable).

---

## Next
→ [[Assignment 3 - Bitwise Augmented Assignment]]
