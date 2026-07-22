---
tags:
  - python
  - data-structures
  - lists
source: Automate the Boring Stuff with Python — Chapter 4
up: "[[!Python Lists - MOC]]"
---

# Lists 1 — Basics & Indexing

> [!info] Why this matters
> Lists are the single most-used data structure in everyday Python. They're how you hold "a bunch of related things" — rows from a spreadsheet, lines from a file, bytes of a payload, results from a loop. In offensive-security scripting (`pwntools`), payloads are frequently built up as lists of bytes/gadgets before being joined together, so getting comfortable with list mechanics now directly transfers.

---

## 1. What Is a List?

A **list** is a single value that holds multiple values in a specific, ordered sequence.

```python
spam = ['cat', 'bat', 'rat', 'elephant']
```

- Written with square brackets `[]`.
- Items are separated by commas ("comma-delimited").
- `[]` is an **empty list** — the list equivalent of `''`, the empty string.
- The list itself is *one* value (you can store it in a variable, pass it to a function, return it) — don't confuse "the list" with "the items inside the list."

```python
>>> [1, 2, 3]
[1, 2, 3]
>>> ['hello', 3.1415, True, None, 42]   # lists can mix types
['hello', 3.1415, True, None, 42]
```

> [!tip] Mental model
> Picture a list as a row of numbered mailboxes. The variable doesn't hold the mail directly — it holds the *address* of that row. This becomes critical later in [[Lists 5 - Mutability & References]].

---

## 2. Indexing — Getting a Single Value

Every item has a position number called an **index**, starting at **0**.

```python
spam = ['cat', 'bat', 'rat', 'elephant']
spam[0]   # 'cat'
spam[1]   # 'bat'
spam[3]   # 'elephant'
```

| Index | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| Value | `'cat'` | `'bat'` | `'rat'` | `'elephant'` |

**Errors to know:**

```python
spam[10000]   # IndexError: list index out of range
spam[1.0]     # TypeError: list indices must be integers, not float
```
Indexes must always be `int`. If you have a float index (e.g. from division), wrap it: `spam[int(1.0)]`.

### Negative indexes
Negative numbers count backward from the end of the list.

```python
spam[-1]   # 'elephant'  <- last item
spam[-3]   # 'bat'       <- 3rd from the end
```

### Lists inside lists (nested)
Chain indexes to reach into a "list of lists":

```python
spam = [['cat', 'bat'], [10, 20, 30, 40, 50]]
spam[0]      # ['cat', 'bat']   <- picks which inner list
spam[0][1]   # 'bat'            <- picks the item inside that list
spam[1][4]   # 50
```
The first index picks *which* list; the second picks *which item* within it. Using only one index returns the whole inner list.

---

## 3. Slicing — Getting Multiple Values

A **slice** pulls a *sub-list* using two indexes separated by a colon: `list[start:end]`. It returns a **brand-new list** and goes **up to, but not including**, the `end` index.

```python
spam = ['cat', 'bat', 'rat', 'elephant']
spam[0:4]   # ['cat', 'bat', 'rat', 'elephant']
spam[1:3]   # ['bat', 'rat']
spam[0:-1]  # ['cat', 'bat', 'rat']
```

**Shortcuts** — omit either side of the colon:

```python
spam[:2]   # ['cat', 'bat']              same as spam[0:2]
spam[1:]   # ['bat', 'rat', 'elephant']  from index 1 to the end
spam[:]    # ['cat', 'bat', 'rat', 'elephant']   a full copy
```

> [!note] Index vs. Slice
> - `spam[2]` → an **index** — one integer, returns a single value.
> - `spam[1:4]` → a **slice** — two integers, returns a *new list*.

---

## 4. `len()`

```python
spam = ['cat', 'dog', 'moose']
len(spam)   # 3
```
Works the same way it does for counting characters in a string.

---

## Next
→ [[Lists 2 - Modifying & Looping]]
