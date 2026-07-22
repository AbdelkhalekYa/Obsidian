---
tags:
  - python
  - data-structures
  - lists
  - methods
source: Automate the Boring Stuff with Python — Chapter 4
up: "[[!Python Lists - MOC]]"
---

# Lists 3 — Methods

← Previous: [[Lists 2 - Modifying & Looping]]

---

## 1. Methods — Functions Tied to a Type

> [!note] Key distinction
> A **method** is a function "attached" to a value's type and called with dot-notation: `spam.index('hello')`. Only that data type can use its own methods.

```python
eggs = 'hello'
eggs.append('world')   # AttributeError: 'str' object has no attribute 'append'
```
(`append()` is a *list* method — strings don't have it.)

### List methods cheat sheet

| Method | Purpose | Return value | Modifies in place? |
|---|---|---|---|
| `.index(x)` | Find index of the **first** occurrence of `x` | index (`int`) | No |
| `.append(x)` | Add `x` to the **end** | `None` | Yes |
| `.insert(i, x)` | Insert `x` **at index `i`** | `None` | Yes |
| `.remove(x)` | Delete the **first** item equal to `x` | `None` | Yes |
| `.sort()` | Sort ascending (or `reverse=True`) | `None` | Yes |

---

## 2. `.index()`
```python
spam = ['hello', 'hi', 'howdy', 'heyas']
spam.index('hello')   # 0
spam.index('heyas')   # 3
spam.index('nope')    # ValueError: 'nope' is not in list
```
With duplicates, it always returns the **first** match:
```python
spam = ['Zophie', 'Pooka', 'Fat-tail', 'Pooka']
spam.index('Pooka')   # 1, not 3
```

---

## 3. `.append()` and `.insert()`
```python
spam = ['cat', 'dog', 'bat']
spam.append('moose')          # ['cat', 'dog', 'bat', 'moose']

spam = ['cat', 'dog', 'bat']
spam.insert(1, 'chicken')     # ['cat', 'chicken', 'dog', 'bat']
```

> [!warning] Common bug
> `.append()` and `.insert()` return `None`. **Never** write `spam = spam.append('x')` — this silently sets `spam` to `None`. The list is changed **in place**; there's nothing to reassign.

---

## 4. `.remove()`
```python
spam = ['cat', 'bat', 'rat', 'elephant']
spam.remove('bat')     # ['cat', 'rat', 'elephant']

spam.remove('chicken') # ValueError: list.remove(x): x not in list
```
With duplicates, only the **first** matching instance is removed:
```python
spam = ['cat', 'bat', 'rat', 'cat', 'hat', 'cat']
spam.remove('cat')   # ['bat', 'rat', 'cat', 'hat', 'cat']
```

### `del` vs `.remove()` — which to use

| | `del list[i]` | `list.remove(value)` |
|---|---|---|
| Removes by | index (position) | value (content) |
| Use when | you know **where** it is | you know **what** it is |
| Error if invalid | `IndexError` | `ValueError` |

---

## 5. `.sort()`
```python
spam = [2, 5, 3.14, 1, -7]
spam.sort()             # [-7, 1, 2, 3.14, 5]

spam = ['ants', 'cats', 'dogs', 'badgers', 'elephants']
spam.sort()              # alphabetical
spam.sort(reverse=True)  # reverse alphabetical
```

**Three gotchas:**
1. **Sorts in place**, returns `None` — don't write `spam = spam.sort()`.
2. **Cannot mix** numbers and strings in the same list → `TypeError: unorderable types`.
3. Sorting strings uses **"ASCIIbetical" order**, not true alphabetical order — *all* uppercase letters sort before *any* lowercase letter:
   ```python
   spam = ['Alice', 'ants', 'Bob', 'badgers', 'Carol', 'cats']
   spam.sort()
   # ['Alice', 'Bob', 'Carol', 'ants', 'badgers', 'cats']
   ```
   Fix with a case-insensitive key:
   ```python
   spam = ['a', 'z', 'A', 'Z']
   spam.sort(key=str.lower)
   # ['a', 'A', 'z', 'Z']
   ```

---

## 6. Example Program: Magic 8 Ball, Refactored with a List

Replacing a long `elif` chain with a list + random index is a classic beginner "aha" moment:

```python
import random

messages = ['It is certain', 'It is decidedly so', 'Yes definitely',
            'Reply hazy try again', 'Ask again later',
            'Concentrate and ask again', 'My reply is no',
            'Outlook not so good', 'Very doubtful']

print(messages[random.randint(0, len(messages) - 1)])
```
`random.randint(0, len(messages) - 1)` always produces a valid index no matter how many messages you add or remove later — the code doesn't need to change.

---

## Next
→ [[Lists 4 - Tuples & Strings (List-Like Types)]]
