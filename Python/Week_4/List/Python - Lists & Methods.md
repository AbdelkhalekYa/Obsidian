---
tags:
  - data-structures
  - lists
  - methods
  - tuples
  - python
source: Automate the Boring Stuff with Python — Chapter 4
Date: 2026-07-21
---

# Python Lists

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
> Picture a list as a row of numbered mailboxes. The variable doesn't hold the mail directly — it holds the *address* of that row. This becomes critical later in [[#16. References — The Trickiest but Most Important Concept]].

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

## 5. Changing Values by Index

Normally the variable goes on the left of `=`. But you can also target an index:

```python
spam = ['cat', 'bat', 'rat', 'elephant']
spam[1] = 'aardvark'
# spam -> ['cat', 'aardvark', 'rat', 'elephant']

spam[2] = spam[1]   # copy a value from one slot to another
spam[-1] = 12345    # can assign any type, not just strings
```
This works because lists are **mutable** — see [[#15. Mutable vs. Immutable]].

---

## 6. Concatenation and Replication

**Concatenation (`+`)** joins two lists into a new one:
```python
[1, 2, 3] + ['A', 'B', 'C']   # [1, 2, 3, 'A', 'B', 'C']
```

**Replication (`*`)** repeats a list's contents a given number of times:
```python
['X', 'Y', 'Z'] * 3   # ['X','Y','Z','X','Y','Z','X','Y','Z']
```

```python
spam = [1, 2, 3]
spam = spam + ['A', 'B', 'C']   # rebuilds spam with the new combined list
```

---

## 7. Removing Values with `del`

```python
spam = ['cat', 'bat', 'rat', 'elephant']
del spam[2]
# spam -> ['cat', 'bat', 'elephant']
del spam[2]
# spam -> ['cat', 'bat']
```
Every item after the deleted index shifts up by one to fill the gap. `del` can also delete a plain variable entirely (rare in practice — mostly used on list items).

---

## 8. Working With Lists — Why Bother?

Beginners often reach for many separate variables (`catName1`, `catName2`, `catName3`...) instead of a list. This is a trap: the program can never handle more items than you hand-wrote variables for, and the code duplicates itself endlessly.

**Bad — hardcoded, doesn't scale:**
```python
print('Enter the name of cat 1:')
catName1 = input()
print('Enter the name of cat 2:')
catName2 = input()
# ...repeats forever, capped at a fixed number
```

**Good — a list grows to fit any amount of input:**
```python
catNames = []
while True:
    print('Enter a cat name (or nothing to stop):')
    name = input()
    if name == '':
        break
    catNames = catNames + [name]   # list concatenation

print('The cat names are:')
for name in catNames:
    print(' ' + name)
```

> [!tip] Takeaway
> Whenever you catch yourself numbering variables (`x1, x2, x3...`), that's almost always a sign you should be using a list instead.

---

## 9. Looping Over Lists

```python
for i in range(4):
    print(i)
```
`range(4)` behaves like the list-like sequence `[0, 1, 2, 3]` — a `for` loop technically repeats its block once per value in a list (or list-like value).

```python
for i in [0, 1, 2, 3]:
    print(i)   # identical output to the range() version above
```

### range(len(list)) pattern — index *and* value together
```python
supplies = ['pens', 'staplers', 'flame-throwers', 'binders']
for i in range(len(supplies)):
    print('Index ' + str(i) + ' in supplies is: ' + supplies[i])
```
Handy because it scales automatically no matter how many items `supplies` contains.

### `in` and `not in`
Test membership directly, without writing a loop yourself:

```python
'howdy' in ['hello', 'hi', 'howdy', 'heyas']   # True
'cat' in ['hello', 'hi']                        # False
'cat' not in ['hello', 'hi']                    # True
```

**Practical example:**
```python
myPets = ['Zophie', 'Pooka', 'Fat-tail']
name = input('Enter a pet name: ')
if name not in myPets:
    print('I do not have a pet named ' + name)
else:
    print(name + ' is my pet.')
```

---

## 10. The Multiple Assignment Trick

Unpack list items straight into separate variables in one line:

```python
cat = ['fat', 'black', 'loud']

# instead of:
size = cat[0]
color = cat[1]
disposition = cat[2]

# do this:
size, color, disposition = cat
```

> [!warning] Count must match exactly
> ```python
> size, color, disposition, name = cat   # only 3 items in cat!
> # ValueError: need more than 3 values to unpack
> ```

---

## 11. Augmented Assignment Operators

Shorthand for "update a variable using its current value":

| Full form | Shorthand |
|---|---|
| `spam = spam + 1` | `spam += 1` |
| `spam = spam - 1` | `spam -= 1` |
| `spam = spam * 1` | `spam *= 1` |
| `spam = spam / 1` | `spam /= 1` |
| `spam = spam % 1` | `spam %= 1` |

`+=` and `*=` also work on **lists and strings**:

```python
spam = 'Hello'
spam += ' world!'      # 'Hello world!'

bacon = ['Zophie']
bacon *= 3              # ['Zophie', 'Zophie', 'Zophie']
```

---

## 12. Methods — Functions Tied to a Type

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

### `.index()`
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

### `.append()` and `.insert()`
```python
spam = ['cat', 'dog', 'bat']
spam.append('moose')          # ['cat', 'dog', 'bat', 'moose']

spam = ['cat', 'dog', 'bat']
spam.insert(1, 'chicken')     # ['cat', 'chicken', 'dog', 'bat']
```

> [!warning] Common bug
> `.append()` and `.insert()` return `None`. **Never** write `spam = spam.append('x')` — this silently sets `spam` to `None`. The list is changed **in place**; there's nothing to reassign.

### `.remove()`
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

### `.sort()`
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

## 13. Example Program: Magic 8 Ball, Refactored with a List

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

## 14. List-Like Types: Strings & Tuples

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

### Tuples — the immutable cousin of lists
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

### Comparison: List vs. Tuple vs. String

| Feature | List | Tuple | String |
|---|---|---|---|
| Syntax | `[ ]` | `( )` | `' '` / `" "` |
| Mutable? | ✅ Yes | ❌ No | ❌ No |
| Indexing / Slicing | ✅ | ✅ | ✅ |
| Typical use | data that changes over time | a fixed, "locked" sequence | text |
| Speed | normal | slightly faster (immutability lets Python optimize) | — |

---

## 15. Mutable vs. Immutable

- **Mutable** (list): can be changed **in place** — items added, removed, or altered without creating a new object.
- **Immutable** (string, tuple, int): cannot be changed. Any "modification" actually builds a **new** object.

```python
name = 'Zophie a cat'
newName = name[0:7] + 'the' + name[8:12]   # builds a brand-new string
# `name` itself is completely untouched: 'Zophie a cat'
```

```python
eggs = [1, 2, 3]
eggs = [4, 5, 6]   # this REPLACES what eggs points to; the original [1,2,3] list still existed momentarily but is now orphaned
```
To *actually* mutate the original list object into holding different values (rather than pointing `eggs` at a new one), you'd use `del`/`.append()` on the existing list:
```python
eggs = [1, 2, 3]
del eggs[2]; del eggs[1]; del eggs[0]
eggs.append(4); eggs.append(5); eggs.append(6)
# eggs -> [4, 5, 6]   (same underlying list object, values changed in place)
```

---

## 16. References — The Trickiest but Most Important Concept

> [!warning] This is the #1 source of confusing bugs for beginners.

Simple values (`int`, `str`) are copied **by value**. Lists are copied **by reference** — the variable stores a *pointer* to the list object, not the list's contents directly.

```python
spam = 42
cheese = spam     # cheese gets a COPY of the value 42
spam = 100
cheese             # still 42 — unaffected, because ints are simple values
```

```python
spam = [0, 1, 2, 3, 4, 5]
cheese = spam            # cheese now points to the SAME list object as spam
cheese[1] = 'Hello!'
print(spam)     # [0, 'Hello!', 2, 3, 4, 5]  <- changed too!
print(cheese)   # [0, 'Hello!', 2, 3, 4, 5]
```
Both variables refer to the exact same underlying list — no second list was ever created. Think of the variable as a box containing not the list, but an **ID number pointing to** the list stored elsewhere in memory.

### Why this matters for function calls (Passing References)

```python
def eggs(someParameter):
    someParameter.append('Hello')

spam = [1, 2, 3]
eggs(spam)
print(spam)   # [1, 2, 3, 'Hello']   <- the function mutated the ORIGINAL list
```
Unlike immutable arguments (ints, strings), a function can permanently modify a list passed into it — with **no `return` statement needed**. This is a common source of "wait, why did calling this function change my variable?" bugs.

### Breaking the link: the `copy` module

| Function | What it does |
|---|---|
| `copy.copy(x)` | **Shallow copy** — a genuinely new list, but any *nested* lists/dicts inside are still shared references |
| `copy.deepcopy(x)` | **Deep copy** — recursively duplicates everything, fully independent from the original |

```python
import copy
spam = ['A', 'B', 'C', 'D']
cheese = copy.copy(spam)
cheese[1] = 42
print(spam)     # ['A', 'B', 'C', 'D']   <- unaffected
print(cheese)   # ['A', 42, 'C', 'D']
```
Use `deepcopy()` specifically when your list contains **nested** lists/dicts that you also want fully isolated from the original.

---

## 17. Quick-Reference Summary

| Operation | Syntax | Notes |
|---|---|---|
| Create | `[1, 2, 3]` | — |
| Index | `list[i]` | 0-based; negative counts from end |
| Slice | `list[a:b]` | excludes index `b` |
| Length | `len(list)` | — |
| Change value | `list[i] = x` | mutable |
| Concatenate | `list1 + list2` | new list |
| Replicate | `list * n` | new list |
| Delete by index | `del list[i]` | shifts remaining items |
| Delete by value | `list.remove(x)` | first match only |
| Add to end | `list.append(x)` | in place, returns `None` |
| Add at index | `list.insert(i, x)` | in place, returns `None` |
| Find index | `list.index(x)` | raises `ValueError` if missing |
| Sort | `list.sort()` / `list.sort(reverse=True)` | in place; use `key=str.lower` for case-insensitive |
| Membership | `x in list` / `x not in list` | Boolean |
| Copy (shallow) | `copy.copy(list)` | nested objects still shared |
| Copy (deep) | `copy.deepcopy(list)` | fully independent |

---

## 18. Self-Test (from the book's practice questions)

1. What is `[]`? → an empty list.
2. Assign `'hello'` as the 3rd value in `spam = [2, 4, 6, 8, 10]` → `spam[2] = 'hello'`.
3. For `spam = ['a', 'b', 'c', 'd']`: what does `spam[-1]` evaluate to? → `'d'`.
4. What does `spam[:2]` evaluate to? → `['a', 'b']`.
5. Difference between `.append()` and `.insert()`? → append always adds to the end; insert lets you specify the index.
6. Two ways to remove values from a list? → `del list[i]` (by index) or `list.remove(value)` (by value).
7. Name ways lists are similar to strings → both support indexing, slicing, `len()`, `for` loops, `in`/`not in`.
8. Difference between lists and tuples? → tuples are immutable.
9. How do you write a tuple containing just the integer `42`? → `(42,)`.
10. Variables that "contain" lists don't actually contain the list directly — what do they contain? → a **reference** (pointer) to the list.
11. Difference between `copy.copy()` and `copy.deepcopy()`? → shallow copy vs. fully recursive copy.

---

## Related Notes
- [[Python - Dictionaries]]
- [[Python - Strings]]
- [[Python - Functions & Scope]]
