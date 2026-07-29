---
tags:
  - python
  - strings
  - methods
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Strings - MOC]]"
---

# Strings 3 — Split, Join & Replace

← Previous: [[Strings 2 - Case, Search & Whitespace Methods]]

---

## 1. `.split()` — String → List

Breaks a string into a **list** of substrings, using whitespace by default:

```python
'my name is Simon'.split()
# ['my', 'name', 'is', 'Simon']
```

With a specific separator:
```python
'2026-07-29'.split('-')
# ['2026', '07', '29']

'a,b,,c'.split(',')
# ['a', 'b', '', 'c']   -- empty strings ARE kept if the separator repeats
```

### `maxsplit` — limit how many splits happen
```python
'a:b:c:d'.split(':', 1)
# ['a', 'b:c:d']   -- only splits once, from the left
```

### `.splitlines()` — split on line breaks specifically
```python
'line1\nline2\nline3'.splitlines()
# ['line1', 'line2', 'line3']
```

---

## 2. `.join()` — List → String (the Reverse of `.split()`)

Called **on the separator string**, passed **the list** to join — this order trips up almost everyone the first time:

```python
','.join(['a', 'b', 'c'])
# 'a,b,c'

' '.join(['my', 'name', 'is', 'Simon'])
# 'my name is Simon'

''.join(['h', 'e', 'l', 'l', 'o'])
# 'hello'
```

> [!warning] Common beginner mistake
> ```python
> ['a', 'b', 'c'].join(',')   # AttributeError: 'list' object has no attribute 'join'
> ```
> `.join()` belongs to the **string** (the glue/separator), not the list. Read it as *"glue".join(list_of_pieces)*.

### `.split()` and `.join()` are inverses
```python
s = 'a,b,c'
pieces = s.split(',')      # ['a', 'b', 'c']
rebuilt = ','.join(pieces)   # 'a,b,c'  -- back to the original
```

---

## 3. `.replace(old, new)` — Substring Substitution

```python
'Hello world'.replace('world', 'there')
# 'Hello there'

'aaa'.replace('a', 'b')
# 'bbb'   -- replaces ALL occurrences by default

'aaa'.replace('a', 'b', 1)
# 'baa'    -- optional 3rd argument limits how many replacements happen
```

---

## 4. `.partition()` — Split Into Exactly Three Pieces

Less commonly taught but genuinely useful: splits on the **first** occurrence only, always returning exactly 3 items (before, separator, after) as a tuple:

```python
'key=value=extra'.partition('=')
# ('key', '=', 'value=extra')
```
Handy when you specifically want "the first delimiter, and everything before/after it" without worrying about how many delimiters exist total (compare to `.split('=', 1)`, which gives a similar result but as a 2-item list, dropping the separator).

---

## 5. Worked Example: Parsing a Log Line

```python
line = '  2026-07-29 14:32:01 ERROR connection timeout  '

line = line.strip()                    # remove leading/trailing whitespace
parts = line.split(' ', 2)               # ['2026-07-29', '14:32:01', 'ERROR connection timeout']
date, time, rest = parts                  # multiple assignment — see [[Lists 2 - Modifying & Looping]]
level, message = rest.split(' ', 1)         # ['ERROR', 'connection timeout']

print(f'{date} {time} — [{level}] {message}')
```
This kind of split → unpack → reassemble pipeline is one of the most common real-world string-processing patterns, whether you're parsing application logs or the raw text output of a CTF challenge binary.

---

## Next
→ [[Strings 4 - The is Methods & Validation]]
