---
tags:
  - python
  - operators
  - assignment
source: Fully supplementary — not covered anywhere in the provided PDF excerpt
up: "[[Python Assignment Operators - MOC]]"
---

# Assignment 5 — Beyond the Basics

← Previous: [[Assignment 4 - Multiple Assignment & Extended Unpacking]]

---

## 1. The Walrus Operator `:=` (Python 3.8+)

Already introduced in [[Booleans 5 - Beyond the Basics]] since it's most often seen inside conditions — the assignment-operator angle, for completeness here:

```python
# Without walrus — two lines
data = get_data()
if data:
    process(data)

# With walrus — assignment happens INSIDE the expression
if (data := get_data()):
    process(data)
```
Unlike `=`, `:=` is an **expression**, not a statement — meaning it produces a value and can appear inside `if`, `while`, list comprehensions, etc., wherever a plain `=` statement would be syntactically illegal.

```python
# Filtering while keeping the computed value, without calling the function twice
results = [y for x in data if (y := expensive(x)) is not None]
```

---

## 2. Why Python Has No True Constants

Unlike languages with a `const` keyword, Python has no built-in way to make a variable truly unreassignable. `ALL_CAPS` naming is a **convention**, not enforcement:

```python
MAX_RETRIES = 5     # convention signals "treat this as a constant"
MAX_RETRIES = 10      # ...but Python will happily let you reassign it anyway — no error
```

The closest built-in tools for genuine immutability are the immutable *types* covered elsewhere in this vault — using a `tuple` instead of a `list` ([[Tuples 1 - Basics, Creation & Immutability]]), or `frozenset` instead of `set` ([[Sets 4 - Comparisons, Subsets & Frozensets]]) — but the *variable name itself* pointing at that object can always be reassigned to something else entirely.

> [!tip] Practical takeaway
> Treat `ALL_CAPS` names as a strict team convention ("do not reassign this"), not a language guarantee. If genuine enforcement matters (rare in practice), some codebases use `typing.Final` as a type-checker hint — but even that's checked by external tools, not the Python runtime itself.

---

## 3. `global` and `nonlocal` — Assignment Across Scopes

By default, assigning to a variable **inside a function** creates a new *local* variable, even if a same-named variable exists outside — this trips up a lot of people the first time they hit it.

```python
count = 0

def increment():
    count += 1   # UnboundLocalError! Python sees the assignment and treats 'count' as local
                  # to this function — but it's used before being locally defined
```

### `global` — explicitly modify a module-level variable
```python
count = 0

def increment():
    global count
    count += 1

increment()
print(count)   # 1
```

### `nonlocal` — modify a variable from an enclosing (but not global) scope
```python
def outer():
    count = 0
    def inner():
        nonlocal count
        count += 1
    inner()
    print(count)   # 1

outer()
```

| Keyword | Targets | Use when |
|---|---|---|
| `global` | module-level variable | modifying a variable defined outside all functions, from inside a function |
| `nonlocal` | enclosing function's variable | modifying a variable from an outer (but non-global) function, inside a nested function |

> [!warning] Both are usually a design smell in larger programs
> Reaching for `global`/`nonlocal` frequently is often a sign the code should instead pass values as arguments and return values explicitly, or use a class to hold shared state. They're worth knowing (you'll see them in real code, and small scripts benefit from the simplicity), but overusing them makes code harder to reason about as it grows — exactly the same "why do I need this" instinct that motivates returning tuples ([[Tuples 2 - Methods, Packing & Unpacking]]) instead of mutating shared globals.

---

## 4. What `+=` Actually Does Under the Hood

Referenced from [[Assignment 2 - Arithmetic Augmented Assignment]] — the mechanism, for the curious:

When Python evaluates `a += b`, it first looks for a special method called `__iadd__` ("in-place add") on `a`'s type:

- **If `__iadd__` exists** (lists, sets, dicts define it) → it's called, and the object is mutated **in place**, then the (same) object is reassigned to the name.
- **If `__iadd__` does NOT exist** (ints, strings, tuples — all immutable types) → Python silently falls back to `a = a.__add__(b)`, which creates a **new** object and rebinds the name.

```python
a = [1, 2]
a += [3]     # calls list.__iadd__ -> mutates in place

a = 5
a += 1        # int has no __iadd__ -> falls back to a = a.__add__(1) -> new int object
```

This is the precise technical reason behind the mutable/immutable distinction flagged as a warning in [[Assignment 2 - Arithmetic Augmented Assignment]] — it's not a special case Python carved out for lists specifically, it's a general rule about how `+=` resolves for *any* type, and mutable container types simply choose to implement `__iadd__` for efficiency.

---

## Next
→ [[Assignment 6 - Summary & Self-Test]]
