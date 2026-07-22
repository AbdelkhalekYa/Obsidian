---
tags:
  - python
  - booleans
  - operators
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Booleans - MOC]]"
---

# Booleans 3 — Boolean Operators & Truth Tables

← Previous: [[Booleans 2 - Comparison Operators]]

While comparison operators (`==`, `<`, etc.) compare *values*, **Boolean operators** (`and`, `or`, `not`) combine *Boolean expressions* themselves.

---

## 1. `and` — Both Must Be True

```python
True and True     # True
True and False     # False
False and True      # False
False and False      # False
```

### Full truth table

| A | B | `A and B` |
|---|---|---|
| `True` | `True` | `True` |
| `True` | `False` | `False` |
| `False` | `True` | `False` |
| `False` | `False` | `False` |

**Example:**
```python
age = 25
has_id = True

if age >= 18 and has_id:
    print('allowed in')
```
Both conditions must hold for the combined expression to be `True`.

---

## 2. `or` — At Least One Must Be True

```python
True or True     # True
True or False      # True
False or True       # True
False or False        # False
```

### Full truth table

| A | B | `A or B` |
|---|---|---|
| `True` | `True` | `True` |
| `True` | `False` | `True` |
| `False` | `True` | `True` |
| `False` | `False` | `False` |

**Example:**
```python
name = 'Alice'
if name == 'Alice' or name == 'Bob':
    print('recognized name')
```

---

## 3. `not` — Flips a Boolean

`not` is **unary** — it takes just one operand and inverts it.

```python
not True     # False
not False     # False -> True... wait, careful:
```
Correctly:
```python
not True    # False
not False   # True
```

### Full truth table

| A | `not A` |
|---|---|
| `True` | `False` |
| `False` | `True` |

**Example:**
```python
spam = True
if not spam:
    print('spam is False')
else:
    print('spam is True')   # this branch runs
```

---

## 4. Combining All Three

Boolean operators can be chained and nested exactly like arithmetic:

```python
age = 25
has_ticket = True
is_banned = False

if (age >= 18 and has_ticket) and not is_banned:
    print('let them in')
```

```python
username = 'admin'
password = 'wrong'

if username == 'admin' and (password == 'admin123' or password == 'backup456'):
    print('access granted')
else:
    print('access denied')   # this branch runs
```

---

## 5. Boolean Operators vs. Comparison Operators — Quick Distinction

| | Comparison operators | Boolean operators |
|---|---|---|
| Examples | `==`, `!=`, `<`, `>`, `<=`, `>=` | `and`, `or`, `not` |
| Operates on | two **values** | one or two **Boolean expressions** |
| Purpose | "how do these two values relate?" | "how do these two true/false facts combine?" |
| Typical position | between values: `x > 5` | between conditions: `(x > 5) and (y < 10)` |

---

## Next
→ [[Booleans 4 - Short-Circuit Evaluation & Precedence]]
