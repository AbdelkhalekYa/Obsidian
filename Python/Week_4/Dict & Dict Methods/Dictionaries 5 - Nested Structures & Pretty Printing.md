---
tags:
  - python
  - dictionaries
  - methods
source: Automate the Boring Stuff with Python — Chapter 5 (intro)
up: "[[Python Dictionaries - MOC]]"
---

# Dictionaries 5 — Nested Structures & Pretty Printing

← Previous: [[Dictionaries 4 - Modifying Dictionaries]]

---

## 1. Pretty Printing with `pprint`

Dense dictionaries print as one unreadable line with `print()`. The `pprint` module formats them cleanly, with sorted keys:

```python
import pprint

message = 'It was a bright cold day in April, and the clocks were striking thirteen.'
count = {}
for character in message:
    count.setdefault(character, 0)
    count[character] = count[character] + 1

pprint.pprint(count)
```
```
{' ': 13,
 ',': 1,
 '.': 1,
 'A': 1,
 'I': 1,
 'a': 4,
 ...
```

- `pprint.pprint(x)` — prints directly to the screen.
- `pprint.pformat(x)` — returns the same formatted output as a **string** instead of printing it (useful if you want to write it to a file or log it).

```python
pprint.pprint(someDict)
print(pprint.pformat(someDict))   # equivalent output
```

`pprint` is especially valuable once dictionaries get nested (see below) — plain `print()` on a nested structure is nearly unreadable.

---

## 2. Modeling Real-World Data

Dictionaries (and dicts nested inside dicts, or dicts nested inside lists) let you represent structured, real-world data as code.

### Example: Tic-Tac-Toe Board

```python
theBoard = {'top-L': ' ', 'top-M': ' ', 'top-R': ' ',
            'mid-L': ' ', 'mid-M': 'X', 'mid-R': ' ',
            'low-L': ' ', 'low-M': ' ', 'low-R': ' '}

def printBoard(board):
    print(board['top-L'] + '|' + board['top-M'] + '|' + board['top-R'])
    print('-+-+-')
    print(board['mid-L'] + '|' + board['mid-M'] + '|' + board['mid-R'])
    print('-+-+-')
    print(board['low-L'] + '|' + board['low-M'] + '|' + board['low-R'])

printBoard(theBoard)
```
```
 | |
-+-+-
 |X|
-+-+-
 | |
```

The key idea: `printBoard()` doesn't care what the actual X/O values are — it only knows the **shape** of the data structure (9 keys, always present). If a key is missing, `KeyError` is raised the moment that key is read:

```python
del theBoard['mid-L']
printBoard(theBoard)
# KeyError: 'mid-L'
```

This is why, when you design your own dictionary-based data model, you should be consistent about which keys are always present — or use `.get()` (see [[Dictionaries 3 - Safe Access - get() & setdefault()]]) to tolerate missing ones.

---

## 3. Dictionary of Dictionaries — Picnic Example

```python
allGuests = {'Alice': {'apples': 5, 'pretzels': 12},
             'Bob':   {'ham sandwiches': 3, 'apples': 2},
             'Carol': {'cups': 3, 'apple pies': 1}}

def totalBrought(guests, item):
    numBrought = 0
    for k, v in guests.items():
        numBrought = numBrought + v.get(item, 0)
    return numBrought

print('Number of things being brought:')
print(' - Apples ' + str(totalBrought(allGuests, 'apples')))
print(' - Cups ' + str(totalBrought(allGuests, 'cups')))
print(' - Cakes ' + str(totalBrought(allGuests, 'cakes')))
```
```
Number of things being brought:
 - Apples 7
 - Cups 3
 - Cakes 0
```

**Why `.get(item, 0)` and not `v[item]`?** Because not every guest brings every item — Alice's dict has no `'cups'` key. Using `.get(item, 0)` treats "guest didn't bring this" as `0` instead of crashing with `KeyError`. This is the exact pattern from [[Dictionaries 3 - Safe Access - get() & setdefault()]] applied one level deeper.

> [!tip] This shape is everywhere in practice
> "Dict of dicts, looped with `.items()`, read safely with `.get()`" is one of the most common real-world data shapes — parsed JSON API responses, config files, and structured log data almost always look exactly like this.

---

## Next
→ [[Dictionaries 6 - Beyond the PDF]]
