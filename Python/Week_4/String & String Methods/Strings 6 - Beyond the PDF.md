---
tags:
  - python
  - strings
source: Fully supplementary — not covered anywhere in the provided PDF excerpt
up: "[[Python Strings - MOC]]"
---

# Strings 6 — Beyond the PDF

← Previous: [[Strings 5 - String Formatting]]

---

## 1. `str` vs `bytes` — The Distinction That Matters Most for CTF/Network Work

Already introduced in [[Operators 3 - Type Conversion]] — worth reinforcing here since it's the single biggest string-related "gotcha" once you move past pure text processing:

```python
s = 'hello'        # str  — text, Unicode code points
b = b'hello'         # bytes — raw binary data, 0-255 per element

type(s)    # <class 'str'>
type(b)     # <class 'bytes'>

s + b        # TypeError -- can't mix str and bytes
```

| | `str` | `bytes` |
|---|---|---|
| Represents | text/Unicode | raw binary data |
| Literal | `'hello'` | `b'hello'` |
| Convert to the other | `.encode()` → bytes | `.decode()` → str |
| Used for | anything the user reads | sockets, files opened in `'rb'` mode, raw payloads |

```python
'hello'.encode()      # b'hello'
b'hello'.decode()       # 'hello'
```

> [!tip] Why this matters for pwntools
> `pwntools`' `remote()`/`process()` objects send and receive **bytes**, not `str` — `.sendline('data')` will actually fail or need to encode first in real usage; you'll almost always see `.sendline(b'data')` or explicit `.encode()` calls in real exploit scripts. Most string *methods* covered in this series (`.split()`, `.strip()`, `.replace()`, etc.) also exist as **bytes methods** with identical behavior — `b'a,b,c'.split(b',')` works exactly like the `str` version.

---

## 2. Regular Expressions — A Preview

For anything beyond simple `.find()`/`.replace()`, Python's `re` module handles pattern matching:

```python
import re

re.search(r'\d+', 'order #12345')          # match object, matched text '12345'
re.findall(r'\d+', 'a1 b22 c333')            # ['1', '22', '333'] -- ALL matches
re.sub(r'\d+', '#', 'a1 b22 c333')             # 'a# b# c#' -- replace all matches
```

| Basic pattern | Matches |
|---|---|
| `\d` | a digit |
| `\w` | a word character (letter/digit/underscore) |
| `\s` | whitespace |
| `+` | one or more of the preceding |
| `*` | zero or more of the preceding |
| `.` | any character |

Regex deserves (and will likely get) its own dedicated note — this is intentionally just enough to recognize it and know when to reach for it: whenever `.find()`/`.split()`/`.replace()` alone can't express the pattern you need (e.g., "any sequence of digits", not one specific literal string).

> [!tip] Why raw strings matter here
> This is exactly why [[Strings 1 - Literals, Escapes & Immutability]] introduced raw strings (`r'...'`) — regex patterns are full of backslashes (`\d`, `\w`, `\s`) that you don't want Python's own escape-sequence rules interfering with.

---

## 3. String Interning & Identity (Trivia, But Explains a Common Confusion)

```python
a = 'hello'
b = 'hello'
a is b   # True (usually) -- CPython "interns" short, simple string literals, reusing the same object

c = 'hello world with spaces and stuff'
d = 'hello world with spaces and stuff'
c is d    # not guaranteed True -- longer/more complex strings may not be interned
```
This ties back to [[Operators 2 - Comparison Operators]]'s warning about `is` vs `==`: this is precisely the kind of implementation-detail behavior that makes relying on `is` for string comparison a bug waiting to happen. **Always use `==` for comparing string content.**

---

## 4. Performance Note: Building Strings in a Loop

```python
# Slow for large n: creates a NEW string object on every iteration (strings are immutable!)
result = ''
for word in big_list_of_words:
    result += word + ' '

# Fast: build a list, join once at the end
result = ' '.join(big_list_of_words)
```
Because strings are immutable (see [[Strings 1 - Literals, Escapes & Immutability]]), every `+=` in the first version silently discards the old string and allocates an entirely new one — O(n²) behavior overall for n concatenations. `.join()` builds the final string once. This is the string-specific version of the same "know your data structure's real cost" lesson from [[Sets 5 - Performance & Real-World Use Cases]].

---

## Next
→ [[Strings 7 - Summary & Self-Test]]
