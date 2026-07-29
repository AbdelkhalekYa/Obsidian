---
tags:
  - python
  - strings
  - methods
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Strings - MOC]]"
---

# Strings 5 — String Formatting

← Previous: [[Strings 4 - The is Methods & Validation]]

Python has accumulated **three** ways to build strings with embedded values over its history. All three still work; f-strings are the current best practice.

---

## 1. f-Strings (Python 3.6+) — Use This by Default

```python
name = 'Alice'
age = 30
f'{name} is {age} years old.'
# 'Alice is 30 years old.'
```

Any valid Python expression works inside `{}`, not just variable names:
```python
f'{2 + 2}'                # '4'
f'{name.upper()}'           # 'ALICE'
f'{"yes" if age >= 18 else "no"}'   # 'yes'
```

### Format specifiers — controlling how a value displays
Add `:spec` after the expression, inside the braces:

```python
pi = 3.14159265

f'{pi:.2f}'          # '3.14'      -- 2 decimal places
f'{1000000:,}'         # '1,000,000' -- thousands separator
f'{255:x}'               # 'ff'        -- hexadecimal
f'{255:#x}'                # '0xff'      -- hex with 0x prefix
f'{255:08b}'                  # '11111111'  -- binary, zero-padded to 8 digits
f'{42:5d}'                       # '   42'     -- right-align in a 5-char field
f'{42:<5d}'                        # '42   '     -- left-align in a 5-char field
```

> [!tip] CTF/security relevance
> The `:x`, `:#x`, and `:08b` specifiers are exactly what you'll reach for when printing leaked addresses or dumping bytes in a readable form during exploit development — cleaner than manually calling `hex()` and string-concatenating (see [[Operators 3 - Type Conversion]] for the underlying `hex()`/`bin()` functions these specifiers wrap).
> ```python
> leaked_addr = 140737345823776
> print(f'Leaked libc address: {leaked_addr:#x}')
> # Leaked libc address: 0x7ffff7a52020
> ```

---

## 2. `.format()` Method (Python 2.7+, Still Common in Older Code)

```python
'{} is {} years old.'.format(name, age)
# 'Alice is 30 years old.'

'{0} is {1}. {0} likes cats.'.format(name, age)   # numbered — can reuse/reorder
'{n} is {a} years old.'.format(n=name, a=age)       # named
```
Same format-specifier syntax as f-strings, just placed differently:
```python
'{:.2f}'.format(pi)   # '3.14'
```

---

## 3. `%` Operator (Old-Style, "printf-style" Formatting)

```python
'%s is %d years old.' % (name, age)
# 'Alice is 30 years old.'
```

| Specifier | Type |
|---|---|
| `%s` | string |
| `%d` | integer |
| `%f` | float |
| `%x` | hexadecimal |

You'll still encounter this style in older codebases and in some C-influenced contexts — worth being able to *read* even if you don't write new code this way.

> [!warning] Security note
> `%`-style and `.format()`-style templates are the classic vector for **format string vulnerabilities** in C's `printf` family (covered in your CTF roadmap's Phase 2, Week 10) — Python's versions are safe by comparison since Python doesn't let a format string read arbitrary stack memory the way C's does, but the naming/conceptual parallel (`%s`, `%d`, `%p`-style specifiers) is exactly why that C vulnerability class exists and is worth keeping in mind while studying it.

---

## 4. Comparison Table

| Style | Syntax | When to use |
|---|---|---|
| f-string | `f'{x}'` | **default choice** — clearest, fastest, supports any expression inline |
| `.format()` | `'{}'.format(x)` | reading/maintaining older code, or when the template string itself is built dynamically (e.g., loaded from a config file) |
| `%` operator | `'%s' % x` | legacy code, or matching C-style `printf` conventions |

---

## Next
→ [[Strings 6 - Beyond the PDF]]
