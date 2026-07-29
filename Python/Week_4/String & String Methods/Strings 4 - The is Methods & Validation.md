---
tags:
  - python
  - strings
  - methods
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Strings - MOC]]"
---

# Strings 4 — The `is___` Methods & Validation

← Previous: [[Strings 3 - Split, Join & Replace]]

Python strings have a family of `is___()` methods that all return a `bool` — they're the string-specific companions to the truthy/falsy rules from [[Booleans 1 - Basics & Truthy-Falsy]].

---

## 1. The Full `is___` Family

| Method | `True` if the string is... |
|---|---|
| `.isalpha()` | entirely letters, and at least one character |
| `.isdigit()` | entirely digits (0-9), and at least one character |
| `.isalnum()` | entirely letters and/or digits, and at least one character |
| `.isspace()` | entirely whitespace, and at least one character |
| `.isupper()` | contains at least one cased character, all cased characters are uppercase |
| `.islower()` | contains at least one cased character, all cased characters are lowercase |
| `.istitle()` | title-cased (`.title()`-style capitalization) |
| `.isdecimal()` | stricter version of `.isdigit()` — no superscripts/fractions |

```python
'abc123'.isalnum()    # True
'abc 123'.isalnum()     # False -- the space isn't alphanumeric
'   '.isspace()           # True
''.isalpha()                # False -- empty string fails EVERY is___ check
```

> [!warning] Empty string always fails
> Every `is___()` method returns `False` on an empty string `''` — they all require "at least one character," so there's nothing to be all-of-anything.

---

## 2. Validation Loop Pattern

This exact pattern was referenced from [[Operators 4 - User Input]] — here's the fuller picture:

```python
while True:
    age = input('Enter your age (numbers only): ')
    if age.isdigit():
        age = int(age)
        break
    print('Please enter a valid number.')
```

### Chaining multiple checks — password strength example
```python
password = input('Choose a password: ')

has_upper = any(c.isupper() for c in password)     # from [[Booleans 5 - Beyond the Basics]]
has_digit = any(c.isdigit() for c in password)
long_enough = len(password) >= 8

if has_upper and has_digit and long_enough:
    print('Strong password')
else:
    print('Password must have an uppercase letter, a digit, and be 8+ characters.')
```
This combines string `is___` methods with the `all()`/`any()` and boolean-combination material from [[Python Booleans - MOC]] — a genuinely common real-world validation shape.

---

## 3. `.isdigit()` Gotcha — It Doesn't Mean "Can Be `int()`'d Safely"

```python
'-5'.isdigit()    # False -- the minus sign isn't a digit!
int('-5')           # works fine: -5
```
```python
'3.14'.isdigit()   # False -- the decimal point isn't a digit
```
`.isdigit()` only confirms "every character is 0-9" — it does **not** account for negative numbers or decimals. For robust numeric validation of arbitrary input (negatives, floats), a `try`/`except` around `int()`/`float()` (see [[Operators 4 - User Input]]) is more reliable than relying on `.isdigit()` alone.

---

## Next
→ [[Strings 5 - String Formatting]]
