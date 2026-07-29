---
tags:
  - python
  - strings
  - summary
source: Combines general Python reference across this note series
up: "[[Python Strings - MOC]]"
---

# Strings 7 — Summary & Self-Test

← Previous: [[Strings 6 - Beyond the PDF]]

---

## Quick-Reference Summary

| Category | Method/Syntax | Notes |
|---|---|---|
| Case | `.upper()`, `.lower()`, `.title()`, `.capitalize()`, `.swapcase()` | all return new strings |
| Whitespace | `.strip()`, `.lstrip()`, `.rstrip()` | accepts custom chars to strip |
| Search (safe) | `.find(sub)` | returns `-1` if not found |
| Search (strict) | `.index(sub)` | raises `ValueError` if not found |
| Search (bool) | `.startswith()`, `.endswith()`, `sub in s` | — |
| Count | `.count(sub)` | — |
| Split | `.split(sep, maxsplit)` | string → list |
| Join | `sep.join(list)` | list → string; called on the separator |
| Replace | `.replace(old, new, count)` | replaces all by default |
| Partition | `.partition(sep)` | splits into exactly 3: before/sep/after |
| Validation | `.isdigit()`, `.isalpha()`, `.isalnum()`, `.isspace()`, `.isupper()`, `.islower()` | all `False` on empty string |
| f-string | `f'{expr:spec}'` | preferred formatting method |
| `.format()` | `'{}'.format(x)` | older but still common |
| `%` operator | `'%s' % x` | legacy, printf-style |
| str → bytes | `.encode()` | needed before sending over a socket |
| bytes → str | `.decode()` | — |
| Raw string | `r'...'` | ignores escape sequences — used for regex, paths |

---

## Self-Test

1. What does `'hello'.upper()` return, and does it change the original `'hello'` string? → `'HELLO'`; the original is untouched — strings are immutable, you must reassign.
2. Why does `['a','b'].join(',')` fail? → `.join()` is a *string* method, called on the separator, not on the list: `','.join(['a','b'])`.
3. Difference between `.find()` and `.index()` when the substring isn't present? → `.find()` returns `-1`; `.index()` raises `ValueError`.
4. What does `'  hi  '.strip()` return? → `'hi'`.
5. Does `.isdigit()` correctly validate `'-5'` as a number? → No — it returns `False` because the minus sign isn't a digit; use `try`/`except int()` for robust numeric validation.
6. What's the current recommended way to format strings in Python? → f-strings (`f'{expr}'`).
7. What does `f'{255:#x}'` produce? → `'0xff'`.
8. Why can't you concatenate `b'hello'` and `'world'` directly? → one is `bytes`, the other is `str` — they must match types; encode/decode first.
9. What's a raw string for, and give an example use case. → `r'...'` disables escape-sequence processing; used for regex patterns and Windows file paths where backslashes are meaningful in their own right.
10. Why is `' '.join(list_of_words)` generally faster than building the same string with a `+=` loop? → strings are immutable, so each `+=` allocates an entirely new string (O(n²) overall); `.join()` builds the result once.

---

## Related Notes
- [[Python Strings - MOC]]
- [[Lists 4 - Tuples & Strings (List-Like Types)]]
- [[Operators 3 - Type Conversion]]
- [[Python Booleans - MOC]]
