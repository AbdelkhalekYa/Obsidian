---
tags:
  - python
  - dictionaries
  - extra
source: Fully supplementary — not covered anywhere in the provided PDF excerpt
up: "[[Python Dictionaries - MOC]]"
---

# Dictionaries 6 — Beyond the PDF

← Previous: [[Dictionaries 5 - Nested Structures & Pretty Printing]]

> [!info] This entire page is supplementary
> None of this was in your PDF excerpt. It's included because these are things you'll run into constantly in real scripts and CTF tooling once you go past "beginner tutorial" dictionary usage.

---

## 1. Dictionaries Are Ordered (Since Python 3.7)

Historically dictionaries were explicitly **unordered** (this is why the book emphasizes that key order doesn't matter for equality — see [[Dictionaries 1 - Basics & Creation]]). Since **Python 3.7**, dictionaries officially guarantee **insertion order**: keys come back out in the order they were first added.

```python
d = {}
d['z'] = 1
d['a'] = 2
d['m'] = 3
list(d.keys())   # ['z', 'a', 'm']  -- insertion order, NOT alphabetical
```

> [!warning] Order ≠ meaning
> Equality still ignores order (`{'a':1,'b':2} == {'b':2,'a':1}` is still `True`). Order only affects things like iteration and `.popitem()` — it doesn't change how dicts compare or hash.

---

## 2. `.fromkeys()` — Build a Dict from a List of Keys

Creates a new dict where every key from an iterable maps to the **same** default value.

```python
keys = ['a', 'b', 'c']
dict.fromkeys(keys, 0)
# {'a': 0, 'b': 0, 'c': 0}
```

**Practical use — order-preserving deduplication** (referenced back in [[Sets 5 - Performance & Real-World Use Cases]]):
```python
raw = [3, 1, 2, 3, 2, 1]
list(dict.fromkeys(raw))
# [3, 1, 2]   -- duplicates removed, ORIGINAL ORDER preserved (unlike set())
```

> [!warning] Mutable default trap
> If the default value is mutable (e.g., a list), **every key shares the exact same object** — not independent copies:
> ```python
> d = dict.fromkeys(['a', 'b'], [])
> d['a'].append(1)
> d   # {'a': [1], 'b': [1]}  <- both changed! Same underlying list.
> ```
> This is the dict-flavored version of the [[Lists 5 - Mutability & References|references]] gotcha. Use a dict comprehension instead if you need independent lists: `{k: [] for k in ['a', 'b']}`.

---

## 3. `collections.defaultdict` — Auto-Initializing Values

Instead of manually calling `.setdefault()` every time (see [[Dictionaries 3 - Safe Access - get() & setdefault()]]), a `defaultdict` automatically creates a default value the **first time** a new key is accessed.

```python
from collections import defaultdict

count = defaultdict(int)   # int() returns 0 -> default value for missing keys is 0
message = 'mississippi'
for character in message:
    count[character] += 1   # no setdefault() needed — defaultdict handles it

print(dict(count))
# {'m': 1, 'i': 4, 's': 4, 'p': 2}
```

```python
groups = defaultdict(list)   # list() returns [] -> default value is an empty list
groups['fruits'].append('apple')
groups['fruits'].append('banana')
groups['veggies'].append('carrot')
print(dict(groups))
# {'fruits': ['apple', 'banana'], 'veggies': ['carrot']}
```

| | Manual `setdefault()` | `defaultdict` |
|---|---|---|
| Boilerplate per access | one extra line every time | zero — handled automatically |
| Default value source | you supply it inline each time | supplied once when creating the dict (a *factory function*: `int`, `list`, `set`, or a custom function) |

---

## 4. `collections.Counter` — Purpose-Built Frequency Counting

The cleanest way to do the "count occurrences" pattern from [[Dictionaries 3 - Safe Access - get() & setdefault()]]:

```python
from collections import Counter

message = 'mississippi'
count = Counter(message)
print(count)
# Counter({'i': 4, 's': 4, 'p': 2, 'm': 1})

count.most_common(2)   # [('i', 4), ('s', 4)]  -- top 2 most frequent
```

`Counter` is a `dict` subclass, so everything else you know (`.items()`, `.get()`, `in`) still works — it just adds counting-specific conveniences like `.most_common()`.

> [!tip] CTF relevance
> `Counter` is the fastest way to eyeball byte/character frequency in a suspicious blob — a very lopsided `Counter` on ciphertext bytes is often your first hint you're dealing with a weak cipher (single-byte XOR, substitution) rather than something like AES.
> ```python
> from collections import Counter
> Counter(ciphertext_bytes).most_common(5)
> ```

---

## 5. Performance: Dicts Are Hash Tables Too

Just like [[Sets 5 - Performance & Real-World Use Cases|sets]], dictionaries are built on hash tables — key lookup, insertion, and deletion are all **O(1) average**, regardless of how many items the dict holds.

| Operation | `list` (searching by value) | `dict` (by key) |
|---|---|---|
| Lookup | O(n) | **O(1)** average |
| Insert | O(1) append / O(n) elsewhere | **O(1)** average |
| Delete | O(n) (must find it first) | **O(1)** average |

This is *why* the "parallel lists" anti-pattern (`names = [...]`, `ages = [...]`, matched by position) is worse than a single `{name: age}` dict for anything beyond trivial scripts — besides being more error-prone, list-based lookups don't scale.

---

## 6. Dicts and JSON

Python dictionaries map almost 1:1 onto JSON objects — this is why `dict` is the natural in-memory representation for API responses, config files, and CTF challenge metadata files.

```python
import json

data = {'name': 'Zophie', 'age': 8, 'tags': ['cat', 'fat']}
json_string = json.dumps(data)          # dict -> JSON string
parsed_back = json.loads(json_string)    # JSON string -> dict
```

Everything covered in this note series (`.get()`, nested dict-of-dicts, `.items()` looping) applies directly the moment you `json.loads()` any API response or config file.

---

## 7. Gotcha: Don't Modify a Dict While Iterating Over It

```python
spam = {'a': 1, 'b': 2, 'c': 3}
for key in spam:
    if spam[key] == 2:
        del spam[key]
# RuntimeError: dictionary changed size during iteration
```

**Fix — iterate over a separate list copy of the keys first:**
```python
for key in list(spam.keys()):
    if spam[key] == 2:
        del spam[key]
```
This mirrors a general Python rule: never add/remove items from a collection (list, set, or dict) while a `for` loop is actively iterating over the *live* collection — iterate over a copy instead.

---

## Next
→ [[Dictionaries 7 - Summary & Self-Test]]
