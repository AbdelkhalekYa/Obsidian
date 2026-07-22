---
tags: [python, programming, booleans]
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Booleans - MOC]]"
---

# Booleans 1 — Basics & Truthy/Falsy

> [!info] Why this matters
> Every `if`, `while`, and conditional in a program ultimately boils down to a Boolean value. In CTF/pwn scripting, this is the language of exploit flow control: "did the leak succeed?", "does this byte match a bad character?", "is the response what I expected?" — all Booleans under the hood.

---

## 1. The `bool` Type

Python has a dedicated Boolean type with exactly **two** possible values: `True` and `False` (capitalized, no quotes — they're keywords, not strings).

```python
spam = True
eggs = False
type(spam)   # <class 'bool'>
```

> [!note] `bool` is technically a subclass of `int`
> `True` behaves as `1` and `False` behaves as `0` in numeric contexts. This isn't just trivia — it has real, usable consequences:
> ```python
> True + True     # 2
> True == 1        # True
> False == 0       # True
> sum([True, True, False, True])   # 3 — counts how many are True
> ```
> That last line is a genuinely common pattern: summing a list of Booleans (or a comprehension of comparisons) to **count how many conditions were true**.

---

## 2. Where Booleans Come From

Booleans are usually **produced**, not typed literally — most commonly as the result of a comparison (see [[Booleans 2 - Comparison Operators]]) or a boolean operator (see [[Booleans 3 - Boolean Operators & Truth Tables]]):

```python
5 > 3          # True
5 == 6          # False
'a' in 'cat'    # True
```

They're then **consumed** by anything that needs a yes/no decision:

```python
if 5 > 3:
    print('yes')

while not done:
    ...
```

---

## 3. Truthy and Falsy Values — Beyond Literal `True`/`False`

Python doesn't require an actual Boolean in an `if` statement — **any value** can be evaluated in a Boolean context, and Python decides whether it counts as "truthy" or "falsy."

### The complete list of falsy values in Python
```python
False
None
0        # any zero: 0, 0.0, 0j
''       # empty string
[]       # empty list
{}       # empty dict
set()    # empty set
()       # empty tuple
```

**Everything else is truthy** — including things beginners often assume are "empty" or "false-ish" but aren't:

```python
bool('False')    # True   -- a NON-EMPTY string, even the word "False", is truthy!
bool(' ')         # True   -- a space is still a non-empty string
bool([0])         # True   -- a list containing one falsy item is still a non-empty list
bool(-1)          # True   -- any nonzero number, including negatives, is truthy
```

> [!warning] Classic beginner trap
> `bool('False')` is `True`. The **string** `'False'` is non-empty, so it's truthy — Python doesn't inspect the *contents* of the string. If you're checking user input against the word "False", you need an explicit string comparison (`if answer == 'False':`), not `if answer:`.

### Practical use — this is why empty-check idioms work
```python
my_list = []
if not my_list:
    print('list is empty')

# equivalent, but less idiomatic:
if len(my_list) == 0:
    print('list is empty')
```
Both work, but `if not my_list:` is the more "Pythonic" style — it relies on the falsy-empty-collection rule above.

---

## Next
→ [[Booleans 2 - Comparison Operators]]
