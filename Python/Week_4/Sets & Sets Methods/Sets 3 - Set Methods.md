---
tags:
  - python
  - data-structures
  - sets
  - methods
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Sets - MOC]]"
---

# Sets 3 — Set Methods

← Previous: [[Sets 2 - Mathematical Set Operations]]

These are the methods for adding/removing individual elements — the set equivalents of list's `.append()`, `.remove()`, etc.

---

## 1. Adding Elements

### `.add(x)` — add a single element
```python
spam = {'cat', 'bat'}
spam.add('rat')
# spam -> {'cat', 'bat', 'rat'}

spam.add('cat')   # already present -> silently does nothing, no error
```

### `.update(iterable)` — add multiple elements at once
```python
spam = {'cat', 'bat'}
spam.update(['rat', 'elephant'])
# spam -> {'cat', 'bat', 'rat', 'elephant'}

spam.update('xyz')   # strings are iterable too -> adds 'x', 'y', 'z' individually
```

| Method | Adds | Accepts |
|---|---|---|
| `.add(x)` | one element | a single hashable value |
| `.update(iter)` | many elements | any iterable (list, tuple, string, another set...) |

---

## 2. Removing Elements

### `.remove(x)` — remove a specific value, error if missing
```python
spam = {'cat', 'bat', 'rat'}
spam.remove('bat')
# spam -> {'cat', 'rat'}

spam.remove('dog')   # KeyError: 'dog'
```

### `.discard(x)` — remove a specific value, **no error** if missing
```python
spam = {'cat', 'bat', 'rat'}
spam.discard('dog')   # no error, nothing happens
spam.discard('bat')   # removes it
```

> [!tip] `.remove()` vs `.discard()`
> This is exactly the same trade-off as `dict.get()` vs `dict[key]`, or list's `del` vs `.remove()`: pick `.discard()` when "it might not be there" is a normal, expected case (avoids wrapping every call in `try/except`).

### `.pop()` — remove and return an **arbitrary** element
```python
spam = {'cat', 'bat', 'rat'}
x = spam.pop()   # removes and returns SOME element — you don't control which
```
Because sets are unordered, there's no "last" or "first" element like `list.pop()` has — you get *whichever* element the internal hash table gives up first. Raises `KeyError` if the set is empty.

### `.clear()` — remove everything
```python
spam = {'cat', 'bat', 'rat'}
spam.clear()
# spam -> set()
```

---

## 3. Copying a Set

```python
spam = {'cat', 'bat'}
cheese = spam.copy()
cheese.add('rat')
# spam is untouched: {'cat', 'bat'}
# cheese: {'cat', 'bat', 'rat'}
```
Like lists, sets are **mutable**, and `cheese = spam` would just create a second reference to the *same* set (see [[Lists 5 - Mutability & References]] — identical concept applies here). `.copy()` gives you an independent set.

---

## 4. Full Method Reference Table

| Method | Purpose | Errors on missing/empty? |
|---|---|---|
| `.add(x)` | add one element | No (silently no-ops if already present) |
| `.update(iter)` | add many elements | No |
| `.remove(x)` | remove one element | ✅ `KeyError` |
| `.discard(x)` | remove one element | No |
| `.pop()` | remove & return arbitrary element | ✅ `KeyError` if empty |
| `.clear()` | empty the set | No |
| `.copy()` | shallow copy | No |

---

## Next
→ [[Sets 4 - Comparisons, Subsets & Frozensets]]
