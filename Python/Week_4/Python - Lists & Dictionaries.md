---
tags:
  - python
source: Automate the Boring Stuff with Python — Chapter 4 (Lists) & Chapter 5 intro (Dictionaries)
---

# Python Lists & Dictionaries

> [!info] Why this matters beyond "just Python basics"
> Lists and dictionaries are the two data structures you will use **constantly**, whether you're automating a spreadsheet or writing a `pwntools` exploit script. In offensive security tooling, `pwntools` payloads are almost always built as byte strings assembled from **lists of gadgets/offsets**, and parsed binary data (like ELF symbol tables) is usually handled as **dictionaries** (`{symbol_name: address}`). Mastering these two types now pays off directly later.

---

## 1. Lists — The Core Concept

A **list** is a single value that holds multiple values in a specific, numbered order.

```python
spam = ['cat', 'bat', 'rat', 'elephant']
```

- Written with square brackets `[]`
- Items are separated by commas ("comma-delimited")
- `[]` = an empty list (the list equivalent of `''` the empty string)
- The **list itself** is one value — you can pass it to a function, store it in a variable, etc. The items *inside* it are separate values.

> [!tip] Mental model
> Think of a list as a row of numbered mailboxes. The list variable doesn't hold the mail directly — it holds the *address* of the row of mailboxes. (This becomes critical later in the **References** section.)

---

## 2. Indexing — Getting One Value

Every item in a list has a position number called an **index**, starting at **0**.

```python
spam = ['cat', 'bat', 'rat', 'elephant']
spam[0]   # 'cat'
spam[1]   # 'bat'
spam[3]   # 'elephant'
```

| Index | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| Value | 'cat' | 'bat' | 'rat' | 'elephant' |

**Errors to know:**
- `spam[10000]` → `IndexError: list index out of range`
- `spam[1.0]` → `TypeError: list indices must be integers, not float` (indexes must be `int`)

### Negative indexes
Negative numbers count from the *end* of the list.

```python
spam[-1]   # 'elephant' (last item)
spam[-3]   # 'bat'      (3rd from the end)
```

### Lists inside lists (nested)
Use two sets of brackets to reach into a "list of lists":

```python
spam = [['cat', 'bat'], [10, 20, 30, 40, 50]]
spam[0]      # ['cat', 'bat']   -> which inner list
spam[0][1]   # 'bat'            -> which item inside that list
spam[1][4]   # 50
```

---

## 3. Slicing — Getting Multiple Values

A **slice** pulls out a *sub-list* using two indexes separated by a colon: `list[start:end]`. It returns a **new list**, and it goes **up to but not including** the `end` index.

```python
spam = ['cat', 'bat', 'rat', 'elephant']
spam[1:3]    # ['bat', 'rat']
spam[0:-1]   # ['cat', 'bat', 'rat']
```

**Shortcuts** — you can omit either side of the colon:

```python
spam[:2]   # ['cat', 'bat']              same as spam[0:2]
spam[1:]   # ['bat', 'rat', 'elephant']  goes to the end
spam[:]    # full copy of the list
```

> [!note] Index vs. Slice
> - `spam[2]` → an **index** (single value, one integer)
> - `spam[1:4]` → a **slice** (a new list, two integers)

---

## 4. `len()`, Changing Values, Concatenation, Replication

```python
len(['a', 'b', 'c'])   # 3

spam = ['cat', 'bat', 'rat', 'elephant']
spam[1] = 'aardvark'   # changes value AT that index (list is mutable)
```

**Concatenation (`+`)** joins two lists into a new one. **Replication (`*`)** repeats a list's contents:

```python
[1, 2, 3] + ['A', 'B', 'C']   # [1, 2, 3, 'A', 'B', 'C']
['X', 'Y', 'Z'] * 3           # ['X','Y','Z','X','Y','Z','X','Y','Z']
```

### Removing values with `del`
```python
spam = ['cat', 'bat', 'rat', 'elephant']
del spam[2]     # spam is now ['cat', 'bat', 'elephant']
```
Everything after the deleted index shifts up by one. `del` can also fully delete a variable (rare in practice).

---

## 5. Looping Over Lists

```python
for i in range(4):
    print(i)
```
`range(4)` behaves like the list-like sequence `[0, 1, 2, 3]` — that's why a `for` loop can iterate over it.

**Common CTF/automation pattern** — loop by index when you need *both* the position and the value:

```python
supplies = ['pens', 'staplers', 'flame-throwers', 'binders']
for i in range(len(supplies)):
    print(f'Index {i} is: {supplies[i]}')
```

### `in` and `not in`
Check membership without a loop:

```python
'howdy' in ['hello', 'hi', 'howdy', 'heyas']   # True
'cat' not in ['hello', 'hi']                    # True
```

---

## 6. Multiple Assignment & Augmented Assignment

**Multiple assignment trick** — unpack a list straight into variables (count must match exactly, or you get a `ValueError`):

```python
cat = ['fat', 'black', 'loud']
size, color, disposition = cat
```

**Augmented assignment operators** — shorthand for "update a variable using itself":

| Full form | Shorthand |
|---|---|
| `spam = spam + 1` | `spam += 1` |
| `spam = spam - 1` | `spam -= 1` |
| `spam = spam * 1` | `spam *= 1` |
| `spam = spam / 1` | `spam /= 1` |
| `spam = spam % 1` | `spam %= 1` |

`+=` also does string/list concatenation and `*=` does replication:

```python
bacon = ['Zophie']
bacon *= 3   # ['Zophie', 'Zophie', 'Zophie']
```

---

## 7. Methods vs. Functions

> [!note] Key distinction
> A **method** is a function "attached" to a value's type, called with dot-notation: `spam.index('hello')`. Only the data type that owns the method can use it — calling `.append()` on a string raises `AttributeError`.

### List methods cheat sheet

| Method | Purpose | Returns | Modifies in place? |
|---|---|---|---|
| `.index(x)` | Find the index of first occurrence of `x` | index (int) | No |
| `.append(x)` | Add `x` to the **end** | `None` | Yes |
| `.insert(i, x)` | Insert `x` **at index `i`** | `None` | Yes |
| `.remove(x)` | Delete first occurrence **of value `x`** | `None` | Yes |
| `.sort()` | Sort ascending (or `reverse=True`) | `None` | Yes |

```python
spam = ['cat', 'bat', 'rat', 'elephant']
spam.index('rat')        # 2 (first match if duplicates exist)
spam.append('moose')     # -> [...,'moose'] added at end
spam.insert(1, 'chicken')# insert at specific index
spam.remove('bat')       # removes VALUE 'bat', not an index
spam.sort()               # alphabetical / numeric ascending
spam.sort(reverse=True)   # descending
```

> [!warning] Common bug
> `.append()`, `.insert()`, `.remove()`, and `.sort()` all return `None`. Never write `spam = spam.append('x')` — that will silently overwrite `spam` with `None`. They modify the list **in place**.

### `del` vs `.remove()`
| | `del list[i]` | `list.remove(value)` |
|---|---|---|
| Removes by | index | value |
| Use when | you know the position | you know the content |
| Errors if | index out of range | value not found (`ValueError`) |

### Sorting quirks
- **Cannot mix** numbers and strings in one `.sort()` call → `TypeError`.
- Sorting strings uses **"ASCIIbetical" order**, not true alphabetical: all uppercase letters sort before all lowercase letters (`'Z' < 'a'`).
- Fix with `spam.sort(key=str.lower)` to sort case-insensitively.

---

## 8. List-Like Types: Strings & Tuples

Strings behave like lists of characters — you can index, slice, loop, and use `len()`, `in`, `not in` on them exactly the same way:

```python
name = 'Zophie'
name[0]     # 'Z'
name[-2]    # 'i'
name[0:4]   # 'Zoph'
```

### Tuples
A **tuple** is almost identical to a list but written with `()` instead of `[]` — with one crucial difference: **tuples are immutable**.

```python
eggs = ('hello', 42, 0.5)
eggs[1] = 99   # TypeError: 'tuple' object does not support item assignment
```

- **Single-item tuple** needs a trailing comma: `('hello',)` — without the comma, `('hello')` is just a string in parentheses.
- Convert between types: `list(eggs)`, `tuple(spam)`.

### Comparison: List vs. Tuple vs. String

| Feature | List | Tuple | String |
|---|---|---|---|
| Syntax | `[ ]` | `( )` | `' '` / `" "` |
| Mutable? | ✅ Yes | ❌ No | ❌ No |
| Indexing/Slicing | ✅ | ✅ | ✅ |
| Use case | Data that changes | Fixed sequence, "this won't change" | Text |
| Performance | Normal | Slightly faster (immutability allows optimization) | — |

---

## 9. Mutable vs. Immutable — Why It Matters

- **Mutable** (list, dict): can be changed in place — items added, removed, or altered without creating a new object.
- **Immutable** (string, integer, tuple): cannot be changed. "Modifying" a string actually builds a **brand-new** string.

```python
name = 'Zophie a cat'
newName = name[0:7] + 'the' + name[8:12]   # builds a NEW string
# `name` itself is untouched
```

```python
eggs = [1, 2, 3]
eggs = [4, 5, 6]   # this REPLACES the list reference, doesn't mutate the original
```

---

## 10. References — The Trickiest but Most Important Concept

> [!warning] This is the #1 source of confusing bugs for beginners.

Simple values (int, string) are copied by **value**. Lists (and dicts) are copied by **reference** — the variable stores a *pointer* to the list, not the list itself.

```python
spam = [0, 1, 2, 3, 4, 5]
cheese = spam          # cheese now points to the SAME list as spam
cheese[1] = 'Hello!'
print(spam)    # [0, 'Hello!', 2, 3, 4, 5]  <- changed too!
print(cheese)  # [0, 'Hello!', 2, 3, 4, 5]
```

Both variables refer to the same underlying list object — there was never a second list created.

### Why this matters for function calls (Passing References)

```python
def eggs(someParameter):
    someParameter.append('Hello')

spam = [1, 2, 3]
eggs(spam)
print(spam)   # [1, 2, 3, 'Hello']  <- the function mutated the ORIGINAL list
```

Unlike immutable arguments, mutable arguments (lists/dicts) passed to a function can be **changed permanently** by that function, even with no `return` statement.

### Breaking the link: `copy` module

| Function | What it does |
|---|---|
| `copy.copy(x)` | **Shallow copy** — new list, but nested lists/dicts inside are still shared references |
| `copy.deepcopy(x)` | **Deep copy** — recursively copies everything, fully independent |

```python
import copy
spam = ['A', 'B', 'C', 'D']
cheese = copy.copy(spam)
cheese[1] = 42
# spam is untouched: ['A', 'B', 'C', 'D']
# cheese: ['A', 42, 'C', 'D']
```

Use `deepcopy()` when your list contains **nested lists/dicts** you also want isolated.

---

## 11. Dictionaries — Introduction

A **dictionary** (`dict`) is also a collection of multiple values, but instead of numeric indexes, you access values by **keys** — which can be strings, numbers, or tuples (not lists — keys must be immutable).

```python
myCat = {'size': 'fat', 'color': 'gray', 'disposition': 'loud'}
myCat['size']   # 'fat'
```

Each `key: value` pairing is called a **key-value pair**.

### List vs. Dictionary — Core Comparison

| Feature | List | Dictionary |
|---|---|---|
| Access by | index (int, ordered position) | key (any immutable type) |
| Ordered? | Yes (position matters) | Insertion-ordered in modern Python, but equality doesn't care about order |
| Equality check | `['a','b'] == ['b','a']` → `False` | `{'a':1,'b':2} == {'b':2,'a':1}` → `True` |
| Sliceable? | Yes | No |
| Missing item error | `IndexError` | `KeyError` |

```python
spam = ['cats', 'dogs', 'moose']
bacon = ['dogs', 'moose', 'cats']
spam == bacon   # False — order matters for lists

eggs = {'name': 'Zophie', 'species': 'cat', 'age': '8'}
ham  = {'species': 'cat', 'age': '8', 'name': 'Zophie'}
eggs == ham     # True — order doesn't matter for dict equality
```

```python
spam = {'name': 'Zophie', 'age': 7}
spam['color']   # KeyError: 'color'
```

---

## 12. `.keys()`, `.values()`, `.items()`

These return special **list-like** views (`dict_keys`, `dict_values`, `dict_items`) — iterable, but not true lists (no `.append()`).

```python
spam = {'color': 'red', 'age': 42}

for v in spam.values(): print(v)        # red, 42
for k in spam.keys():   print(k)        # color, age
for i in spam.items():  print(i)        # ('color','red'), ('age',42)
```

Convert to a real list if needed: `list(spam.keys())`.

**Unpacking both key and value at once:**
```python
for k, v in spam.items():
    print(f'Key: {k} Value: {v}')
```

### Membership checks
```python
'name' in spam.keys()      # checks keys
'Zophie' in spam.values()  # checks values
'color' in spam            # shorthand for 'color' in spam.keys()
```

---

## 13. `.get()` and `.setdefault()` — Avoiding `KeyError`

### `.get(key, fallback)`
Returns the fallback value instead of raising `KeyError` if the key is missing:

```python
picnicItems = {'apples': 5, 'cups': 2}
picnicItems.get('eggs', 0)   # 0 (not an error)
```

### `.setdefault(key, value)`
Sets a key **only if it doesn't already exist**, and returns its (possibly pre-existing) value. This is a one-line replacement for:

```python
if 'color' not in spam:
    spam['color'] = 'black'
```

```python
spam = {'name': 'Pooka', 'age': 5}
spam.setdefault('color', 'black')   # sets it -> returns 'black'
spam.setdefault('color', 'white')   # already exists -> returns 'black' (unchanged)
```

> [!tip] Classic use case: frequency counting
> ```python
> message = 'It was a bright cold day in April...'
> count = {}
> for character in message:
>     count.setdefault(character, 0)
>     count[character] = count[character] + 1
> ```
> This pattern (count occurrences with a dict) shows up everywhere — log analysis, word frequency, byte-frequency analysis of encoded/encrypted CTF data, etc.

### `.get()` vs `.setdefault()`

| | `.get()` | `.setdefault()` |
|---|---|---|
| Modifies the dict? | No | Yes, if key missing |
| Use when | You just want to *read* a value safely | You want to *ensure* a key exists going forward |

---

## 14. Pretty Printing with `pprint`

```python
import pprint
pprint.pprint(count)          # prints directly, keys sorted, readable formatting
text = pprint.pformat(count)  # same output, but returned as a STRING instead of printed
```

Especially useful for nested dictionaries/lists that would otherwise print as one dense unreadable line.

---

## 15. Modeling Real-World Data with Nested Structures

Dictionaries and lists can contain each other, letting you represent structured, real-world data.

**Example — Tic-tac-toe board as a dictionary:**
```python
theBoard = {'top-L': ' ', 'top-M': ' ', 'top-R': ' ',
            'mid-L': ' ', 'mid-M': 'X', 'mid-R': ' ',
            'low-L': ' ', 'low-M': ' ', 'low-R': ' '}
```
A function like `printBoard(board)` can read this structure and render it — the *data* and the *code that interprets it* are separate, which is the essence of "modeling."

**Example — Dictionary of dictionaries (picnic guest list):**
```python
allGuests = {'Alice': {'apples': 5, 'pretzels': 12},
             'Bob':   {'ham sandwiches': 3, 'apples': 2},
             'Carol': {'cups': 3, 'apple pies': 1}}

def totalBrought(guests, item):
    numBrought = 0
    for k, v in guests.items():
        numBrought += v.get(item, 0)   # .get() avoids KeyError if guest didn't bring that item
    return numBrought
```

This pattern — dict of dicts, looped with `.items()` and read safely with `.get()` — is a very common real-world data-processing shape (e.g., parsing JSON API responses, config files, or structured log data).

---

## 16. Quick-Reference Summary Table

| Concept | List | Dictionary |
|---|---|---|
| Literal syntax | `[1, 2, 3]` | `{'a': 1}` |
| Access | `list[index]` | `dict[key]` |
| Mutable | ✅ | ✅ |
| Ordered access by position | ✅ | ❌ (access by key, not position) |
| Slicing | ✅ | ❌ |
| Safe access without error | `x if i < len(list) else default` (manual) | `dict.get(key, default)` |
| Add missing default automatically | N/A | `dict.setdefault(key, default)` |
| Common error when missing | `IndexError` | `KeyError` |

---

## 17. Practice Question Highlights (for self-testing)

- `[]` → an empty list.
- Assign `'hello'` as 3rd value in `spam = [2,4,6,8,10]` → `spam[2] = 'hello'`.
- Difference between `.append()` and `.insert()`: append always adds to the end; insert lets you choose the index.
- Two ways to remove a value: `del list[i]` (by index) or `list.remove(value)` (by value).
- Difference between list and tuple: tuples are immutable.
- Single-item tuple syntax: `(42,)` — the comma is mandatory.
- Variables holding lists/dicts actually hold **references**, not the data itself.
- `copy.copy()` = shallow copy; `copy.deepcopy()` = recursive/full copy.

---

## Related Notes to Build Next
- [[Python - Strings]]
- [[Python - Functions & Scope]]
- [[Python - Reading and Writing Files]] *(Chapter 8 referenced above for saving dictionary data permanently)*
