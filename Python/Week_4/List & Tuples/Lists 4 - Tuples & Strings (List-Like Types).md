---
tags:
  - python
  - lists
  - tuples
  - strings
source: Automate the Boring Stuff with Python — Chapter 4
up: "[[Python Lists - MOC]]"
---

# Lists 4 — Tuples & Strings (List-Like Types)

← Previous: [[Lists 3 - Methods]]

---

## 1. Strings Are List-Like

Strings behave like a "list of characters" — indexing, slicing, `len()`, looping, and `in`/`not in` all work the same way:

```python
name = 'Zophie'
name[0]     # 'Z'
name[-2]    # 'i'
name[0:4]   # 'Zoph'
'Zo' in name   # True
for i in name:
    print('* * * ' + i + ' * * *')
```

---

## 2. Tuples — The Immutable Cousin of Lists

Written with `()` instead of `[]`. Behaves like a list for reading, but **cannot be modified**:

```python
eggs = ('hello', 42, 0.5)
eggs[0]     # 'hello'
eggs[1:3]   # (42, 0.5)
len(eggs)   # 3

eggs[1] = 99   # TypeError: 'tuple' object does not support item assignment
```

- **Single-item tuple** needs a trailing comma: `('hello',)` — without it, `('hello')` is just a string in parentheses, not a tuple.
  ```python
  type(('hello',))   # <class 'tuple'>
  type(('hello'))    # <class 'str'>
  ```
- Use a tuple to signal to readers of your code "this sequence is not meant to change." As a bonus, tuples are slightly faster than lists because Python can optimize immutable data.

### Converting between types
```python
tuple(['cat', 'dog', 5])   # ('cat', 'dog', 5)
list(('cat', 'dog', 5))    # ['cat', 'dog', 5]
list('hello')              # ['h', 'e', 'l', 'l', 'o']
```

---

## 3. Comparison: List vs. Tuple vs. String

| Feature | List | Tuple | String |
|---|---|---|---|
| Syntax | `[ ]` | `( )` | `' '` / `" "` |
| Mutable? | ✅ Yes | ❌ No | ❌ No |
| Indexing / Slicing | ✅ | ✅ | ✅ |
| Typical use | data that changes over time | a fixed, "locked" sequence | text |
| Speed | normal | slightly faster (immutability lets Python optimize) | — |

---

## Next
→ [[Lists 5 - Mutability & References]]
