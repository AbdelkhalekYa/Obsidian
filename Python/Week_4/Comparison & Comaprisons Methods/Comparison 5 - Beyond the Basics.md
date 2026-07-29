---
tags:
  - python
  - operators
  - comparison
source: Fully supplementary — not covered anywhere in the provided PDF excerpt
up: "[[Python Comparison Operators - MOC]]"
---

# Comparison 5 — Beyond the Basics

← Previous: [[Comparison 4 - is vs == Deep Dive]]

---

## 1. Comparisons Are Customizable — the Dunder Methods

When you write `a == b`, Python actually calls `a.__eq__(b)` behind the scenes. Every comparison operator has a corresponding "dunder" (double-underscore) method:

| Operator | Method called |
|---|---|
| `==` | `__eq__` |
| `!=` | `__ne__` |
| `<` | `__lt__` |
| `>` | `__gt__` |
| `<=` | `__le__` |
| `>=` | `__ge__` |

This is *why* `[1,2] == [1,2]` compares content instead of identity — the `list` type defines `__eq__` to compare element-by-element (which is also what implements the lexicographic rules from [[Comparison 2 - Lexicographic Comparison of Sequences]]).

### Defining custom comparisons on your own classes
```python
class Card:
    def __init__(self, rank):
        self.rank = rank

    def __eq__(self, other):
        return self.rank == other.rank

    def __lt__(self, other):
        return self.rank < other.rank

king = Card(13)
queen = Card(12)
king == queen    # False -- uses YOUR __eq__
king > queen       # True  -- uses YOUR __lt__ (Python derives > from < automatically in some cases, but not always — see below)
```

---

## 2. `functools.total_ordering` — Define Less, Get More

Manually writing all six comparison methods (`__eq__`, `__lt__`, `__le__`, `__gt__`, `__ge__`, `__ne__`) is repetitive. `functools.total_ordering` fills in the rest for you if you provide `__eq__` and just **one** of `__lt__`/`__le__`/`__gt__`/`__ge__`:

```python
from functools import total_ordering

@total_ordering
class Card:
    def __init__(self, rank):
        self.rank = rank

    def __eq__(self, other):
        return self.rank == other.rank

    def __lt__(self, other):
        return self.rank < other.rank

# total_ordering automatically derives __le__, __gt__, __ge__ from the two you defined
king, queen = Card(13), Card(12)
king >= queen   # True — works even though __ge__ was never written by hand
```

---

## 3. Sorting With Custom Comparisons — `key=`

This connects directly back to [[Lists 3 - Methods]]'s coverage of `.sort()`. When the default ordering isn't what you want, pass a `key` function instead of relying on the object's own `<`:

```python
words = ['banana', 'kiwi', 'apple', 'fig']
words.sort(key=len)                    # sort by LENGTH, not alphabetically
# ['kiwi', 'fig', 'apple', 'banana']

people = [{'name': 'Bob', 'age': 25}, {'name': 'Alice', 'age': 30}]
people.sort(key=lambda p: p['age'])      # sort dicts by a specific field
```

`sorted()` works identically but returns a **new** list instead of sorting in place — the same "returns new vs. mutates" distinction from [[Lists 3 - Methods]]:
```python
sorted(words, key=len)   # new list; 'words' itself is untouched
```

---

## 4. The `operator` Module — Comparisons as Functions

Sometimes you need a comparison **as a function value** (e.g., to pass to another function) rather than writing it inline. The `operator` module provides exactly that:

```python
import operator

operator.eq(1, 1)     # True  -- same as 1 == 1
operator.lt(1, 2)       # True  -- same as 1 < 2

# Common use: as the key/comparator argument elsewhere
data = [(1, 'b'), (2, 'a')]
sorted(data, key=operator.itemgetter(1))   # sort by the SECOND element of each tuple
```

---

## 5. Security / CTF-Relevant Use Cases

- **Byte-for-byte comparison timing** — naive `==` comparison of secrets (e.g., comparing a submitted token/hash against the correct one with plain `==`) can leak information via **timing side-channels**, since `==` on strings/bytes short-circuits at the first mismatched byte. The standard defense is `hmac.compare_digest()`, which runs in constant time regardless of where the mismatch occurs — directly relevant once your CTF/security work touches authentication or cryptographic checks.
  ```python
  import hmac
  hmac.compare_digest(submitted_token, correct_token)   # constant-time — safe against timing attacks
  ```
- **Version/offset comparisons in exploit scripts** — comparing leaked libc addresses, computing whether an offset falls within an expected range, or checking ASLR-randomized addresses against known bounds all rely directly on the ordering rules from [[Comparison 1 - The Six Operators & Type Rules]].
- **Sorting solve-order or challenge triage data** — the `key=`/`operator` module patterns above are exactly how you'd programmatically sort a scraped list of CTF challenges by solve count or point value during the competition-day triage phase referenced in your CTF roadmap.

---

## Next
→ [[Comparison 6 - Summary & Self-Test]]
