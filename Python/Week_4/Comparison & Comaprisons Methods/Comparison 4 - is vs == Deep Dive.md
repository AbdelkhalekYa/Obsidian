---
tags:
  - python
  - operators
  - comparison
source: Fully supplementary — expands briefly-mentioned material from Operators 2 and Strings 6
up: "[[Python Comparison Operators - MOC]]"
---

# Comparison 4 — `is` vs `==` Deep Dive

← Previous: [[Comparison 3 - Chained Comparisons & Pitfalls]]

---

## 1. The Core Distinction, Restated

| | `==` | `is` |
|---|---|---|
| Checks | equal **value/content** | same **object identity** (same memory address) |
| Calls | the type's `__eq__` method (customizable — see [[Comparison 5 - Beyond the Basics]]) | never customizable — always a raw identity check |

```python
a = [1, 2, 3]
b = [1, 2, 3]
a == b   # True  -- same content
a is b    # False -- two distinct objects that happen to contain equal values

c = a
c is a    # True -- literally the same object in memory
```

---

## 2. Why `id()` Explains Everything Here

Every Python object has a unique identity, retrievable with `id()` — `is` is really just shorthand for `id(a) == id(b)`.

```python
a = [1, 2, 3]
b = [1, 2, 3]
id(a), id(b)     # two DIFFERENT numbers
a is b               # False, consistent with the ids differing

c = a
id(c) == id(a)         # True
c is a                    # True
```

---

## 3. CPython Implementation Detail: Small Integer Caching

This is the classic trap that makes `is` *seem* to work for numbers sometimes:

```python
a = 5
b = 5
a is b    # True   -- CPython pre-caches and reuses small integers (typically -5 to 256)

a = 1000
b = 1000
a is b     # False  -- outside the cached range, these are separate objects!
```

> [!warning] Never rely on this — it's an implementation detail, not a language guarantee
> This caching behavior is specific to CPython (the standard Python interpreter) and the exact cached range isn't part of the language specification — other implementations (PyPy, etc.) may cache differently or not at all. Code that happens to work with `is` for small ints is a **latent bug**, not a valid technique. Always use `==` for comparing numeric (or any) values.

---

## 4. String Interning — the Same Trap for Strings

Already flagged briefly in [[Strings 6 - Beyond the PDF]] — restated with the mechanism explained:

```python
a = 'hello'
b = 'hello'
a is b    # True (usually) -- short, simple string literals get "interned" (reused) by CPython

c = 'hello world with spaces'
d = 'hello world with spaces'
c is d      # not guaranteed — longer/more complex strings may not be interned
```
Same rule as integer caching: an implementation detail, not something to depend on. **Always use `==` for string content comparison.**

---

## 5. When `is` IS the Correct Choice

Despite all the warnings above, `is` has real, correct uses — specifically for **singleton** objects, where there's guaranteed to only ever be one instance in the entire program:

```python
if x is None:        # ✅ CORRECT and idiomatic — there's only ever ONE None object
if x is not None:      # ✅ CORRECT

if x is True:            # ⚠️ works, but == is more conventional/flexible here
if x is False:              # same
```

| Use `is` for | Use `==` for |
|---|---|
| `x is None` / `x is not None` | comparing numbers |
| Checking two variables reference the literal same object (rare, intentional) | comparing strings |
| — | comparing list/dict/set/tuple contents |
| — | basically everything else |

> [!tip] Rule of thumb
> If you're checking against `None`, use `is`. For everything else, default to `==` unless you have a specific, deliberate reason to test object identity.

---

## Next
→ [[Comparison 5 - Beyond the Basics]]
