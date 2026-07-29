---
tags:
  - python
  - strings
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Strings - MOC]]"
---

# Strings 1 — Literals, Escapes & Immutability

> [!info] Prerequisite
> This assumes you already know strings behave like a "list of characters" (indexing, slicing, `len()`, `in`) — that's covered in [[Lists 4 - Tuples & Strings (List-Like Types)]]. This page starts where that one leaves off.

---

## 1. Quoting Styles

```python
'single quotes'
"double quotes"
'''triple single quotes — spans multiple lines'''
"""triple double quotes — spans multiple lines, common for docstrings"""
```

Single and double quotes are functionally identical — pick whichever lets you avoid escaping:
```python
'That is Alice's cat.'    # SyntaxError -- the apostrophe ends the string early
"That is Alice's cat."     # fine -- double quotes let the apostrophe pass through
```

### Triple-quoted strings span multiple lines literally
```python
message = """Dear Alice,

How are you?

Sincerely,
Bob"""
```
Newlines inside a triple-quoted string are preserved exactly as typed — no need for explicit `\n`.

---

## 2. Escape Characters

A backslash `\` inside a string starts an **escape sequence** — a way to include characters that would otherwise be hard or impossible to type directly.

| Escape | Meaning |
|---|---|
| `\'` | single quote |
| `\"` | double quote |
| `\\` | backslash itself |
| `\n` | newline |
| `\t` | tab |
| `\r` | carriage return |
| `\b` | backspace |
| `\xHH` | a byte by its hex value (e.g. `\x41` = `'A'`) |

```python
print('Say hi to Bob\'s mother.')
print('Hello there!\nHow are you?\nI\'m doing fine.')
```
```
Say hi to Bob's mother.
Hello there!
How are you?
I'm doing fine.
```

---

## 3. Raw Strings — Ignore Escapes

Prefix with `r` to treat backslashes as **literal characters**, not escape sequences:

```python
print(r'C:\Users\name\newfolder')   # prints exactly as typed, \n is NOT a newline here
```
Extremely common for **regular expressions** and **Windows file paths**, where backslashes are meaningful in the string's own syntax and you don't want Python's escape rules interfering:
```python
import re
re.match(r'\d+', '123abc')   # r'\d+' — the \d is meant for regex, not a Python escape
```

---

## 4. f-Strings — Embedding Expressions Directly (Preview)

```python
name = 'Alice'
age = 30
print(f'{name} is {age} years old.')
# Alice is 30 years old.
```
Prefix a string with `f` and put any expression inside `{}` — it's evaluated and inserted automatically. Full formatting details (including format specifiers like `.2f`) are covered in [[Strings 5 - String Formatting]].

---

## 5. Strings Are Immutable — Recap and Why It Matters More Here

Already established in [[Lists 4 - Tuples & Strings (List-Like Types)]] and [[Lists 5 - Mutability & References]]:

```python
name = 'Zophie a cat'
name[7] = 'the'   # TypeError: 'str' object does not support item assignment
```

**Every string method you'll see in this series returns a brand-new string — none of them modify the original.** This is worth internalizing before the methods pages, because it explains a very common beginner bug:

```python
spam = 'hello'
spam.upper()      # returns 'HELLO', but doesn't change spam!
print(spam)         # still 'hello'

spam = spam.upper()   # you must reassign to actually keep the result
print(spam)              # 'HELLO'
```

> [!warning] String methods never mutate — always reassign
> Unlike list methods such as `.append()` or `.sort()` (which mutate in place and return `None` — see [[Lists 3 - Methods]]), **every** string method returns a new string and leaves the original untouched. If you don't capture the return value, the "change" is silently lost.

---

## Next
→ [[Strings 2 - Case, Search & Whitespace Methods]]
