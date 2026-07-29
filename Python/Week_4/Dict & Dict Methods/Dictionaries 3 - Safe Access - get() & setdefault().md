---
tags:
  - python
  - dictionaries
  - methods
source: Automate the Boring Stuff with Python — Chapter 5 (intro)
up: "[[Python Dictionaries - MOC]]"
---

# Dictionaries 3 — Safe Access: `get()` & `setdefault()`

← Previous: [[Dictionaries 2 - Iterating, Keys, Values & Items]]

---

## 1. `.get(key, fallback)` — Read Safely

It's tedious (and error-prone) to check whether a key exists before reading it. `.get()` returns a **fallback value** instead of raising `KeyError` if the key is missing.

```python
picnicItems = {'apples': 5, 'cups': 2}

picnicItems.get('cups', 0)   # 2   -- key exists, returns its value
picnicItems.get('eggs', 0)   # 0   -- key missing, returns the fallback instead of erroring
```

Without `.get()`, this would crash:
```python
picnicItems['eggs']   # KeyError: 'eggs'
```

> [!note] Default fallback
> If you omit the second argument, `.get()` returns `None` instead of raising an error: `picnicItems.get('eggs')` → `None`.

---

## 2. `.setdefault(key, value)` — Ensure a Key Exists

Sets a key **only if it doesn't already have a value**, and returns that key's value either way. It replaces this common pattern:

```python
spam = {'name': 'Pooka', 'age': 5}
if 'color' not in spam:
    spam['color'] = 'black'
```

...with one line:

```python
spam = {'name': 'Pooka', 'age': 5}
spam.setdefault('color', 'black')
# spam -> {'name': 'Pooka', 'age': 5, 'color': 'black'}
# returns 'black'

spam.setdefault('color', 'white')
# key already exists -> NOT overwritten, still 'black'
# returns 'black' (the pre-existing value, 'white' is ignored)
```

> [!warning] `setdefault()` never overwrites
> Unlike a plain assignment `spam['color'] = 'white'`, `setdefault()` **only sets** the value if the key is absent. If you actually want to force-overwrite an existing key, just use `spam[key] = value` directly (see [[Dictionaries 4 - Modifying Dictionaries]]).

---

## 3. Classic Use Case: Frequency Counting

```python
message = 'It was a bright cold day in April, and the clocks were striking thirteen.'
count = {}

for character in message:
    count.setdefault(character, 0)
    count[character] = count[character] + 1

print(count)
# {' ': 13, ',': 1, '.': 1, 'A': 1, 'I': 1, 'a': 4, 'c': 3, 'b': 1, 'e': 5, ...}
```

The `.setdefault(character, 0)` call guarantees `count[character]` exists (starting at `0`) before you try to increment it — without it, the very first time you see a new character, `count[character] = count[character] + 1` would raise `KeyError`.

> [!tip] This pattern is everywhere
> Counting occurrences with a dict shows up constantly: word-frequency analysis, log-line tallying, and — relevant to your CTF prep — **byte-frequency analysis** of ciphertext/encoded data (a heavily skewed byte distribution is often the first clue you're looking at a weak XOR or substitution cipher rather than real encryption). See [[Dictionaries 6 - Beyond the PDF]] for an even cleaner way to do this with `collections.Counter`.

---

## 4. `.get()` vs `.setdefault()` — When to Use Which

| | `.get()` | `.setdefault()` |
|---|---|---|
| Modifies the dict? | ❌ No | ✅ Yes, if key missing |
| Use when | you just want to **read** a value safely | you want to **guarantee** a key exists going forward |
| Typical use case | "give me this config value, or a default if unset" | "start this counter/list at a default the first time I see this key" |

---

## Next
→ [[Dictionaries 4 - Modifying Dictionaries]]
