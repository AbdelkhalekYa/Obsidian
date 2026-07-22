---
tags:
  - python
  - booleans
  - extra
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Booleans - MOC]]"
---

# Booleans 5 — Beyond the Basics

← Previous: [[Booleans 4 - Short-Circuit Evaluation & Precedence]]

---

## 1. `bool()` — Explicit Conversion

`bool(x)` converts any value to its truthy/falsy equivalent (see [[Booleans 1 - Basics & Truthy-Falsy]] for the full falsy list):

```python
bool(0)          # False
bool(42)          # True
bool('')           # False
bool('hi')          # True
bool([])             # False
bool([1, 2])          # True
bool(None)             # False
```

Useful for explicitly normalizing a value to a real Boolean rather than relying on an implicit truthy check, especially when storing the result (e.g., in a dict or returning it from a function where the caller expects an actual `bool`, not "some truthy value").

---

## 2. `all()` and `any()` — Boolean Logic Across a Collection

These two built-in functions apply `and`/`or` logic across an **entire iterable** at once, instead of chaining conditions manually.

### `all(iterable)` — True only if EVERY item is truthy
```python
all([True, True, True])     # True
all([True, False, True])     # False
all([])                       # True  -- vacuously true (empty = "no exceptions found")

nums = [2, 4, 6, 8]
all(n % 2 == 0 for n in nums)   # True -- every number is even
```

### `any(iterable)` — True if AT LEAST ONE item is truthy
```python
any([False, False, True])    # True
any([False, False, False])    # False
any([])                        # False -- vacuously false (nothing to find)

nums = [1, 3, 5, 6]
any(n % 2 == 0 for n in nums)   # True -- at least one even number exists
```

> [!tip] Replaces manual loops
> Instead of:
> ```python
> found = False
> for n in nums:
>     if n % 2 == 0:
>         found = True
>         break
> ```
> just write:
> ```python
> found = any(n % 2 == 0 for n in nums)
> ```
> Both `all()` and `any()` **short-circuit** too — `any()` stops at the first truthy item, `all()` stops at the first falsy one — so they're efficient even on large iterables.

---

## 3. The Walrus Operator `:=` (Python 3.8+) — Assign Inside a Condition

Lets you assign a value **and** test it in the same expression, avoiding a separate line:

```python
# Without walrus:
data = get_data()
if data:
    process(data)

# With walrus — same behavior, one line:
if (data := get_data()):
    process(data)
```

**Common use — avoiding a duplicate/expensive call inside a `while` loop:**
```python
while (chunk := file.read(1024)):
    process(chunk)
```

---

## 4. De Morgan's Laws — Simplifying Negated Boolean Expressions

A useful mental tool (from formal logic, not Python-specific) for simplifying conditions with `not` wrapped around `and`/`or`:

```python
not (A and B)   ==   (not A) or (not B)
not (A or B)     ==   (not A) and (not B)
```

```python
# Before (harder to read):
if not (is_admin and is_active):
    deny_access()

# After (equivalent, arguably clearer):
if (not is_admin) or (not is_active):
    deny_access()
```

Recognizing this pattern helps when reading (or writing) a dense negated condition — it's very common in permission checks and input-validation logic.

---

## 5. Security / CTF-Relevant Use Cases

Since this feeds into your CTF prep, here's where Boolean logic specifically matters in that context:

- **`checksec` output interpretation**: NX, PIE, RELRO, canary are all effectively Booleans (enabled/disabled) — your exploit branching logic ("if canary is enabled, leak it first") is straight Boolean control flow.
- **pwntools payload validation**: checking `all(b not in bad_chars for b in payload)` before sending — a direct real-world use of `all()` from this note, confirming *none* of your payload bytes are on the forbidden list.
- **Constraint satisfaction with `angr`**: when you call `.explore(find=addr, avoid=other_addrs)`, you're expressing Boolean conditions ("is this state at the target address AND not at an avoided one") that drive the symbolic execution engine.
- **Short-circuiting to avoid crashes in exploit scripts**: e.g., `if response and b'flag' in response:` — guards against calling `in` on a `None` response if the connection dropped, exactly the pattern from [[Booleans 4 - Short-Circuit Evaluation & Precedence]].
- **Bit-flag style Booleans in binary analysis**: permission bits, protection flags, and ELF program header flags (`PF_R`, `PF_W`, `PF_X`) are frequently combined and tested with bitwise operators (`&`, `|`, `^`, `~`) which behave like Boolean logic but operate per-bit rather than on a single `True`/`False` — worth knowing these are a *different* (though related) tool from `and`/`or`/`not`.

  ```python
  PF_X = 0x1   # executable
  PF_W = 0x2   # writable
  flags = 0x3  # both writable AND executable — a classic W^X violation flag to check for

  is_wx = bool(flags & PF_X) and bool(flags & PF_W)
  ```

---

## Next
→ [[Booleans 6 - Summary & Self-Test]]
