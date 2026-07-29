---
tags: [python, programming, tuples, tuple-methods]
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Tuples - MOC]]"
---

# Tuples 2 — Methods, Packing & Unpacking

← Previous: [[Tuples 1 - Basics, Creation & Immutability]]

---

## 1. Tuples Only Have Two Methods

Because tuples are immutable, they can't have any method that would add, remove, or reorder items — that rules out `.append()`, `.insert()`, `.remove()`, `.sort()`, etc. (all covered for lists in [[Lists 3 - Methods]]). Only **read-only** methods remain:

| Method | Purpose | Example |
|---|---|---|
| `.count(x)` | how many times `x` appears | `(1, 2, 2, 3).count(2)` → `2` |
| `.index(x)` | index of the first occurrence of `x` | `(1, 2, 3).index(3)` → `2` |

```python
t = ('a', 'b', 'a', 'c', 'a')
t.count('a')     # 3
t.index('c')       # 3
t.index('z')         # ValueError: tuple.index(x): x not in tuple
```

> [!note] Same behavior as their list counterparts
> `.count()` and `.index()` on a tuple behave identically to the same-named list methods covered in [[Lists 3 - Methods]] — including `.index()` raising `ValueError` if the value isn't found, and returning only the **first** match with duplicates.

---

## 2. Tuple Packing — Multiple Values Into One Tuple

"Packing" just means grouping several values together with commas — the parentheses are optional:

```python
point = 3, 4          # packs into (3, 4) automatically
point = (3, 4)          # identical, more explicit
```

Every function that appears to "return multiple values" is actually packing them into a tuple:
```python
def min_max(numbers):
    return min(numbers), max(numbers)   # packs two values into one tuple

result = min_max([4, 1, 9, 2])
print(result)        # (1, 9)
print(type(result))    # <class 'tuple'>
```

---

## 3. Tuple Unpacking — One Tuple Into Multiple Variables

The reverse operation — already introduced for lists in [[Lists 2 - Modifying & Looping]], but it's tuples where this pattern is used constantly in real code:

```python
point = (3, 4)
x, y = point
print(x, y)   # 3 4
```

**Directly unpacking a function's return value** (the most common real-world use):
```python
def min_max(numbers):
    return min(numbers), max(numbers)

low, high = min_max([4, 1, 9, 2])
print(low, high)   # 1 9
```

### The classic swap trick
```python
a, b = 1, 2
a, b = b, a     # swaps them — no temp variable needed!
print(a, b)      # 2 1
```
Under the hood, Python packs the right side into a tuple `(b, a)` first, then unpacks it into `a, b` — packing and unpacking happening in a single line.

### Unpacking in a `for` loop over `.items()`
This is the exact mechanism behind the pattern from [[Dictionaries 2 - Iterating, Keys, Values & Items]]:
```python
d = {'a': 1, 'b': 2}
for k, v in d.items():   # each .items() entry IS a tuple, unpacked here
    print(k, v)
```

---

## 4. Starred Unpacking (`*`) — Grabbing "the Rest"

```python
first, *middle, last = (1, 2, 3, 4, 5)
print(first)     # 1
print(middle)      # [2, 3, 4]   -- NOTE: this becomes a LIST, not a tuple
print(last)          # 5
```
Useful when you know you want the first and/or last item specifically, but don't want to hardcode exactly how many items are in between.

```python
first, *rest = (10, 20, 30)
print(first, rest)   # 10 [20, 30]
```

---

## Next
→ [[Tuples 3 - Beyond the PDF]]
