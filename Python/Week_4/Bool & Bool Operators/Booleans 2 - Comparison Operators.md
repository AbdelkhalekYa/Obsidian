---
tags: [python, programming, booleans, operators]
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Booleans - MOC]]"
---

# Booleans 2 — Comparison Operators

← Previous: [[Booleans 1 - Basics & Truthy-Falsy]]

Comparison operators (sometimes called **relational operators**) compare two values and always evaluate to a Boolean.

---

## 1. The Six Comparison Operators

| Operator | Meaning | Example | Result |
|---|---|---|---|
| `==` | equal to | `42 == 42` | `True` |
| `!=` | not equal to | `42 != 99` | `True` |
| `<` | less than | `2 < 5` | `True` |
| `>` | greater than | `2 > 5` | `False` |
| `<=` | less than or equal to | `5 <= 5` | `True` |
| `>=` | greater than or equal to | `4 >= 5` | `False` |

> [!warning] `==` vs `=`
> `=` is the **assignment** operator (`spam = 42` stores 42 in `spam`). `==` is the **comparison** operator (`spam == 42` asks "does spam equal 42?"). Using `=` where you meant `==` is one of the most common beginner syntax errors — Python usually catches it (`SyntaxError: invalid syntax` if used inside an `if`), but it's still worth being deliberate about.

---

## 2. Comparisons Work on More Than Numbers

```python
'hello' == 'hello'    # True
'hello' == 'Hello'     # False -- case-sensitive
[1, 2] == [1, 2]        # True -- lists compare by CONTENT, not identity
[1, 2] == [2, 1]         # False -- order matters for lists
{'a': 1} == {'a': 1}      # True -- dicts compare by content, order-independent
{1, 2} == {2, 1}           # True -- sets compare by content, order irrelevant
```

This ties directly into what's already covered elsewhere:
- List equality cares about order → [[Dictionaries 1 - Basics & Creation]]
- Dict/set equality ignores order → [[Sets 4 - Comparisons, Subsets & Frozensets]]

### Comparing different types
```python
42 == '42'     # False -- int and str are never equal, even with matching digits
42 == 42.0      # True  -- int and float CAN be equal if numerically equivalent
```

---

## 3. Chained Comparisons

Python allows chaining comparisons in a way that reads like math notation — this is a genuine Python feature, not just multiple `and`s glued together, but it *behaves* like one:

```python
x = 5
1 < x < 10        # True  -- same as: 1 < x AND x < 10
0 <= x <= 4        # False -- same as: 0 <= x AND x <= 4
```

> [!tip] Cleaner than the "obvious" version
> Instead of writing:
> ```python
> if x > 1 and x < 10:
> ```
> you can write:
> ```python
> if 1 < x < 10:
> ```
> Both work identically — the chained form is just more concise and closer to how you'd write it on paper.

---

## 4. Identity vs. Equality: `==` vs `is`

`==` checks if two values are **equal** in content. `is` checks if two variables point to the **exact same object in memory** (identity) — a completely different question.

```python
a = [1, 2, 3]
b = [1, 2, 3]
a == b   # True  -- same CONTENT
a is b    # False -- two DIFFERENT list objects that happen to look the same

c = a
c is a    # True -- c and a are literally the same object (see [[Lists 5 - Mutability & References]])
```

> [!warning] `is` is for identity, not equality
> A common beginner mistake is writing `if x is None:` — which is actually **correct and preferred** for `None` checks specifically (there's only ever one `None` object), but using `is` to compare regular values like numbers or strings is a bug waiting to happen, since it depends on implementation details of how Python may or may not reuse objects internally.
> ```python
> if x is None:      # ✅ correct, idiomatic
> if x == None:       # works, but not idiomatic Python style
> if x is 5:           # ⚠️ works by accident for small ints due to CPython caching — don't rely on it
> ```

---

## Next
→ [[Booleans 3 - Boolean Operators & Truth Tables]]
