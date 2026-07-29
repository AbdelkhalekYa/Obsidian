---
tags:
  - python
  - assignment
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Assignment Operators - MOC]]"
---

# Assignment 1 — Basic & Chained Assignment

> [!info] Why this matters
> Assignment looks like the simplest thing in the language, but the "point a name at an object" model (rather than "copy bytes into a box") is the single mental model that explains almost every confusing bug involving lists, dicts, and function arguments elsewhere in this vault.

---

## 1. Basic Assignment (`=`)

```python
spam = 42
name = 'Zophie'
my_list = [1, 2, 3]
```
`=` binds a **name** to an **object**. It is always "make this name point at this object" — never "copy the object's bytes into this variable." This distinction is invisible for simple immutable values like numbers, but becomes very visible with mutable objects — see [[Lists 5 - Mutability & References]] for the full explanation.

> [!note] `=` vs `==`
> `=` **assigns**. `==` **compares** (see [[Operators 2 - Comparison Operators]]). `if spam = 42:` is a `SyntaxError` — Python won't let you accidentally assign inside a condition, unlike some other languages.

---

## 2. Chained Assignment — One Value, Multiple Names

```python
a = b = c = 5
print(a, b, c)   # 5 5 5
```
All three names are bound to the **same** object. Python evaluates the right-hand side once, then assigns it to each target name from left to right.

### Safe for immutable types
```python
a = b = 5
a = 10
print(b)   # still 5 — reassigning 'a' just points it at a NEW object, 'b' is untouched
```

### Dangerous for mutable types
```python
x = y = []
x.append(1)
print(y)   # [1]  <- y changed too! Both names still point at the SAME list object.
```

> [!warning] Chained assignment ≠ independent copies
> This is the exact same reference-sharing mechanism from [[Lists 5 - Mutability & References]], just triggered by chained assignment instead of `cheese = spam`. If you need independent objects, assign separately or use `copy.copy()`/`copy.deepcopy()`:
> ```python
> x = []
> y = []          # two SEPARATE empty lists — safe
> # or:
> import copy
> x = []
> y = copy.copy(x)   # explicit independent copy
> ```

---

## 3. Assignment Doesn't Copy — It Rebinds

A related, often-missed detail: reassigning a name never modifies the object it *used* to point to — it just points the name somewhere else.

```python
spam = [1, 2, 3]
cheese = spam        # cheese points to the SAME list as spam
spam = [4, 5, 6]        # spam now points to a NEW list
print(cheese)             # [1, 2, 3]  <- unaffected — cheese still points to the ORIGINAL list
```
Compare this carefully with mutating in place (`spam.append(4)`), which *does* affect anything else pointing at the same object. The difference between **rebinding a name** and **mutating an object** is the crux of nearly every list/dict reference bug — see [[Lists 5 - Mutability & References]] for the deepest treatment of this.

---

## Next
→ [[Assignment 2 - Arithmetic Augmented Assignment]]
