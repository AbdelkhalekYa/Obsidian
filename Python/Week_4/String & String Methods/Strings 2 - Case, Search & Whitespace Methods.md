---
tags:
  - python
  - strings
  - methods
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Strings - MOC]]"
---

# Strings 2 — Case, Search & Whitespace Methods

← Previous: [[Strings 1 - Literals, Escapes & Immutability]]

> [!note] Reminder
> Every method below returns a **new** string — see [[Strings 1 - Literals, Escapes & Immutability#5. Strings Are Immutable — Recap and Why It Matters More Here]].

---

## 1. Case Conversion

| Method | Effect | Example |
|---|---|---|
| `.upper()` | all uppercase | `'hi'.upper()` → `'HI'` |
| `.lower()` | all lowercase | `'HI'.lower()` → `'hi'` |
| `.title()` | Capitalize Each Word | `'the cat sat'.title()` → `'The Cat Sat'` |
| `.capitalize()` | capitalize only the first character | `'hello world'.capitalize()` → `'Hello world'` |
| `.swapcase()` | flip case of every character | `'Hello'.swapcase()` → `'hELLO'` |

### Common use: case-insensitive comparison
```python
user_input = 'YES'
if user_input.lower() == 'yes':
    print('confirmed')
```

### `.isupper()` / `.islower()` — check rather than convert
```python
'HELLO'.isupper()   # True
'Hello'.isupper()    # False -- must be ENTIRELY uppercase (and contain at least one letter)
'123'.isupper()       # False -- no cased characters at all
```

---

## 2. Whitespace Stripping

| Method | Removes whitespace from | Example |
|---|---|---|
| `.strip()` | both ends | `'  hi  '.strip()` → `'hi'` |
| `.lstrip()` | left end only | `'  hi  '.lstrip()` → `'hi  '` |
| `.rstrip()` | right end only | `'  hi  '.rstrip()` → `'  hi'` |

All three accept an optional argument specifying **which characters** to strip (not just whitespace):
```python
'xxhelloxx'.strip('x')     # 'hello'
'###log line###\n'.strip('#\n')   # 'log line'
```

> [!tip] Extremely common in real scripts
> `.strip()` is one of the most-used string methods in practice — cleaning up user input, trimming trailing newlines from lines read out of a file (`for line in f: line = line.strip()`), or cleaning noisy output from a subprocess/socket.

---

## 3. Searching Within a String

| Method | Purpose | Behavior if not found |
|---|---|---|
| `.find(sub)` | index of first occurrence | returns `-1` |
| `.index(sub)` | index of first occurrence | raises `ValueError` |
| `.count(sub)` | how many times `sub` appears | returns `0` |
| `.startswith(sub)` | does the string begin with `sub`? | returns `False` |
| `.endswith(sub)` | does the string end with `sub`? | returns `False` |
| `sub in string` | does `sub` appear anywhere? | returns `False` |

```python
s = 'hello world'
s.find('world')       # 6
s.find('xyz')            # -1  -- no error, just -1
s.index('xyz')             # ValueError: substring not found

s.count('l')                # 3
s.startswith('hello')         # True
s.endswith('world')            # True
'world' in s                     # True
```

> [!note] `.find()` vs `.index()` — the same trade-off as list's `.index()`
> This mirrors the `del` vs `.remove()` / `.get()` vs `[key]` pattern seen throughout [[Python Lists - MOC]] and [[Python Dictionaries - MOC]]: pick `.find()` when "not found" is a normal, expected outcome (avoids `try/except`); pick `.index()` when you're certain it should be there and want a loud failure if it's not.

---

## Next
→ [[Strings 3 - Split, Join & Replace]]
