---
tags:
  - python
  - dictionaries
  - methods
source: Partially beyond the provided PDF excerpt — .update(), .pop(), .popitem(), del, .clear(), merging, and comprehensions are supplementary general-Python reference
up: "[[Python Dictionaries - MOC]]"
---

# Dictionaries 4 — Modifying Dictionaries

← Previous: [[Dictionaries 3 - Safe Access - get() & setdefault()]]

> [!info] Beyond the PDF
> The book excerpt you provided only showed adding a single key with `spam[key] = value` and `setdefault()`. Everything else on this page — `.update()`, `.pop()`, `.popitem()`, `del`, `.clear()`, merging operators, and dict comprehensions — is supplementary material covering the full set of dictionary-modification tools.

---

## 1. Adding / Overwriting a Single Key

```python
spam = {'name': 'Pooka'}
spam['age'] = 5              # adds a new key
spam['name'] = 'Zophie'       # OVERWRITES the existing value — no error, no warning
```
Unlike `.setdefault()`, direct assignment with `[]` **always** overwrites if the key already exists.

---

## 2. `.update()` — Merge in Multiple Keys at Once

Adds/overwrites many key-value pairs in one call. Accepts another dict, or an iterable of `(key, value)` pairs, or keyword arguments.

```python
spam = {'name': 'Pooka', 'age': 5}
spam.update({'age': 6, 'color': 'black'})
# spam -> {'name': 'Pooka', 'age': 6, 'color': 'black'}
# 'age' was overwritten, 'color' was added

spam.update(size='fat', mood='happy')   # keyword-argument form also works
```

> [!tip] Compare to sets
> This is the dict equivalent of [[Sets 3 - Set Methods|set's `.update()`]] — same name, same "merge many things in at once" idea.

---

## 3. Removing Keys

### `del dict[key]` — remove by key, error if missing
```python
spam = {'name': 'Pooka', 'age': 5}
del spam['age']
# spam -> {'name': 'Pooka'}

del spam['nope']   # KeyError: 'nope'
```

### `.pop(key, default)` — remove and **return** the value
```python
spam = {'name': 'Pooka', 'age': 5}
age = spam.pop('age')        # age = 5, spam -> {'name': 'Pooka'}
spam.pop('nope', 'N/A')       # returns 'N/A' instead of raising KeyError (fallback supplied)
spam.pop('nope')              # KeyError: 'nope' (no fallback given)
```

### `.popitem()` — remove and return an **arbitrary** key-value pair
```python
spam = {'a': 1, 'b': 2}
spam.popitem()   # ('b', 2) in modern Python — pops the LAST inserted item (LIFO order since 3.7+)
```
> [!note] Not truly random
> Since Python 3.7 dictionaries are insertion-ordered, so `.popitem()` specifically removes the **most recently added** pair (like `list.pop()` on the end of a list) — it's not arbitrary the way `set.pop()` is.

### `.clear()` — remove everything
```python
spam = {'a': 1, 'b': 2}
spam.clear()
# spam -> {}
```

### Comparison table

| Method | Removes | Returns | Error if missing/empty? |
|---|---|---|---|
| `del d[key]` | one key | nothing | ✅ `KeyError` |
| `d.pop(key)` | one key | the value | ✅ `KeyError` (unless fallback given) |
| `d.pop(key, default)` | one key | the value, or `default` | ❌ No |
| `d.popitem()` | last-inserted pair | `(key, value)` tuple | ✅ `KeyError` if empty |
| `d.clear()` | everything | nothing | No |

---

## 4. Merging Dictionaries (Python 3.9+)

```python
a = {'x': 1, 'y': 2}
b = {'y': 99, 'z': 3}

merged = a | b        # {'x': 1, 'y': 99, 'z': 3}   -- new dict; b's values win on conflict
a |= b                 # in-place merge equivalent to a.update(b)
```

**Older / more portable syntax (works in any Python 3 version):**
```python
merged = {**a, **b}    # dictionary unpacking — same result, b overrides a on conflict
```

---

## 5. Dictionary Comprehensions

Like a list comprehension, but builds a `dict` directly:

```python
squares = {x: x**2 for x in range(6)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16, 5: 25}

# Filtering with a condition
evens_only = {x: x**2 for x in range(10) if x % 2 == 0}
# {0: 0, 2: 4, 4: 16, 6: 36, 8: 64}

# Building from two lists (pairing with zip)
keys = ['a', 'b', 'c']
values = [1, 2, 3]
{k: v for k, v in zip(keys, values)}
# {'a': 1, 'b': 2, 'c': 3}
```

---

## Next
→ [[Dictionaries 5 - Nested Structures & Pretty Printing]]
