---
tags:
  - python
  - operators
  - comparison
source: Recap plus supplementary pitfalls not covered elsewhere
up: "[[Python Comparison Operators - MOC]]"
---

# Comparison 3 — Chained Comparisons & Pitfalls

← Previous: [[Comparison 2 - Lexicographic Comparison of Sequences]]

---

## 1. Recap: How Chaining Works

Already introduced in [[Operators 2 - Comparison Operators]] and [[Booleans 2 - Comparison Operators]]:

```python
1 < x < 10        # same as: 1 < x and x < 10
```

---

## 2. The Real Mechanics: Each Link Is Evaluated Only Once, Left to Right

This detail isn't in the earlier notes: in `a < b < c`, the middle expression `b` is only ever **evaluated once**, even though it participates in two comparisons — important if `b` is an expression with a side effect (like a function call).

```python
def get_value():
    print('called!')
    return 5

1 < get_value() < 10
# prints 'called!' only ONCE, not twice
```
This is different from manually writing `1 < get_value() and get_value() < 10`, which **would** call the function twice. Chained comparison is a genuine single construct, not simple syntactic sugar for two independent comparisons glued with `and` — this is the detail that matters if any part of the chain has side effects.

---

## 3. Pitfall: `float('nan')` Breaks Every Comparison, Including Chains

From [[Comparison 1 - The Six Operators & Type Rules]]:
```python
nan = float('nan')
nan == nan     # False
1 < nan < 10     # False -- EVERY comparison involving NaN is False, even nan < nan or nan > nan
nan < nan        # False
nan > nan         # False
```

> [!warning] `if x == nan:` never works — use `math.isnan()`
> A very real bug: if a value can end up as `NaN` (e.g., from `0.0 / 0.0` handling, malformed sensor/log data, or certain floating-point edge cases in exploit math), checking `if x == float('nan'):` will **always silently fail**, since nothing equals NaN. Use `math.isnan(x)` instead:
> ```python
> import math
> math.isnan(nan)   # True -- the correct way to check
> ```

---

## 4. Pitfall: Chaining Doesn't Mean What It Looks Like for Non-Transitive-Feeling Operators

```python
1 == 1 == 2     # False -- same as: (1 == 1) and (1 == 2) -> True and False -> False
1 == 1 == 1       # True
```
This is usually intuitive, but it's worth explicitly confirming `==` chains the same way `<` does — every comparison operator supports chaining, not just the ordering ones.

### A genuinely confusing one: mixing operators in a chain
```python
3 > 2 == 2      # True -- same as: (3 > 2) and (2 == 2) -> True and True -> True
3 > 2 != 1        # True -- same as: (3 > 2) and (2 != 1) -> True and True -> True
```
Mixed-operator chains are legal Python, but they read poorly. Prefer explicit `and` with parentheses for anything beyond a simple range check (`1 < x < 10`) — readability matters more than cleverness here, echoing the general precedence advice from [[Booleans 4 - Short-Circuit Evaluation & Precedence]].

---

## 5. Pitfall: Comparing `None` With Ordering Operators

```python
None == None    # True -- equality works fine
None < 5           # TypeError: '<' not supported between instances of 'NoneType' and 'int'
```
`None` supports equality checks but not ordering — a common crash source if a value that's sometimes `None` (e.g., a failed lookup via `.get()`, see [[Dictionaries 3 - Safe Access - get() & setdefault()]]) later gets compared with `<`/`>` without a None-check first.

```python
value = some_dict.get('key')   # could be None if missing
if value is not None and value > 10:   # guard with `is not None` first — short-circuit protects the rest
    ...
```
This mirrors the guard-clause pattern from [[Booleans 4 - Short-Circuit Evaluation & Precedence]].

---

## Next
→ [[Comparison 4 - is vs == Deep Dive]]
