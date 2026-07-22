---
tags: [python, programming, booleans, operators]
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Booleans - MOC]]"
---

# Booleans 4 — Short-Circuit Evaluation & Precedence

← Previous: [[Booleans 3 - Boolean Operators & Truth Tables]]

---

## 1. Short-Circuit Evaluation

Python evaluates `and`/`or` **left to right**, and stops as soon as the overall result is already determined — it doesn't bother evaluating the right-hand side if it doesn't need to. This is called **short-circuiting**.

### `and` short-circuits on the first `False`
If the left side of `and` is `False`, the whole expression **must** be `False` — so Python never even looks at the right side.
```python
def expensive_check():
    print('expensive_check ran!')
    return True

False and expensive_check()
# prints NOTHING — expensive_check() was never called
```

### `or` short-circuits on the first `True`
If the left side of `or` is `True`, the whole expression **must** be `True` — so the right side is skipped.
```python
True or expensive_check()
# prints NOTHING — expensive_check() was never called
```

---

## 2. Why This Matters — The "Guard Clause" Pattern

Short-circuiting isn't just a performance detail — it's routinely used **on purpose** to prevent errors:

```python
my_list = []

if len(my_list) > 0 and my_list[0] == 'target':
    print('found it')
```
If `my_list` is empty, `len(my_list) > 0` is `False`, so Python **never evaluates** `my_list[0]` — which would otherwise raise `IndexError` on an empty list. The first condition acts as a "guard" that protects the second.

```python
config = None

if config is not None and config.get('debug'):
    print('debug mode on')
```
If `config` is `None`, `config is not None` is `False`, so `config.get('debug')` is never called — avoiding an `AttributeError: 'NoneType' object has no attribute 'get'`.

> [!tip] Order your conditions deliberately
> When combining conditions with `and`, put the **cheap/safe check first** and the **check that could fail or be expensive second**. This is one of the most useful practical habits in this entire topic.

---

## 3. What `and`/`or` Actually Return (Not Always a Boolean!)

A subtlety that trips up a lot of people: `and` and `or` in Python don't strictly return `True`/`False` — they return **one of the two original operands**.

```python
5 and 10       # 10   -- both truthy, 'and' returns the LAST evaluated operand
0 and 10        # 0    -- short-circuits on the falsy 0, returns it directly

5 or 10          # 5    -- 'or' short-circuits on the first truthy value, returns it
0 or 10           # 10   -- 0 is falsy, so 'or' moves on and returns 10
```

### Practical use — the "default value" idiom
```python
name = user_input or 'Anonymous'
```
If `user_input` is truthy (a non-empty string), `name` becomes `user_input`. If `user_input` is falsy (empty string, `None`, `0`), `name` falls back to `'Anonymous'`. This is an extremely common one-liner default-value pattern in real code — functionally similar to `dict.get(key, default)` from [[Dictionaries 3 - Safe Access - get() & setdefault()]], but for plain variables.

---

## 4. Operator Precedence

When mixing arithmetic, comparisons, and Boolean operators in one expression, Python evaluates them in this order (highest precedence first):

| Priority | Operators |
|---|---|
| 1 (highest) | `()` parentheses |
| 2 | arithmetic: `**`, `*`, `/`, `//`, `%`, `+`, `-` |
| 3 | comparisons: `==`, `!=`, `<`, `>`, `<=`, `>=`, `in`, `is` |
| 4 | `not` |
| 5 | `and` |
| 6 (lowest) | `or` |

```python
x = 5
y = 10
result = x + 3 > 5 and y - 5 == 5
# step by step:
# x + 3 > 5     -> 8 > 5      -> True
# y - 5 == 5     -> 5 == 5     -> True
# True and True    -> True
```

> [!tip] When in doubt, parenthesize
> Precedence rules are worth knowing, but relying on memorized precedence for anything non-trivial makes code harder to read. Adding explicit parentheses — `(x + 3 > 5) and (y - 5 == 5)` — costs nothing and removes all ambiguity for future-you (or a teammate) reading the code later. This especially matters in exploit scripts where a misread condition can silently skip a critical check.

---

## Next
→ [[Booleans 5 - Beyond the Basics]]
