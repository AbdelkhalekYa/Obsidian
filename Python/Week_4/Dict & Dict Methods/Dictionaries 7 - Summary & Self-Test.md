---
tags:
  - python
  - data-structures
  - dictionaries
  - summary
source: Combines Automate the Boring Stuff Chapter 5 (intro) with supplementary material from this note series
up: "[[Python Dictionaries - MOC]]"
---

# Dictionaries 7 — Summary & Self-Test

← Previous: [[Dictionaries 6 - Beyond the PDF]]

---

## Quick-Reference Summary

| Operation | Syntax | Notes |
|---|---|---|
| Create | `{'a': 1}` | `{}` alone is an empty dict |
| Access | `d[key]` | `KeyError` if missing |
| Safe access | `d.get(key, default)` | never raises, returns `default` (or `None`) if missing |
| Ensure key exists | `d.setdefault(key, default)` | sets only if missing, doesn't overwrite existing |
| Add / overwrite | `d[key] = value` | always overwrites if key exists |
| Merge many | `d.update(other_dict)` | in place; overwrites on key conflict |
| Merge (new dict) | `d1 \| d2` (3.9+) or `{**d1, **d2}` | later dict wins on conflict |
| Remove by key | `del d[key]` | `KeyError` if missing |
| Remove & return | `d.pop(key, default)` | fallback avoids `KeyError` |
| Remove last-inserted | `d.popitem()` | `KeyError` if empty |
| Empty the dict | `d.clear()` | — |
| Membership (keys) | `key in d` | shorthand for `key in d.keys()` |
| Membership (values) | `value in d.values()` | — |
| Loop keys | `for k in d:` or `d.keys()` | — |
| Loop values | `for v in d.values():` | — |
| Loop both | `for k, v in d.items():` | — |
| Length | `len(d)` | number of key-value pairs |
| Pretty print | `pprint.pprint(d)` | sorted, readable formatting |
| Build from keys | `dict.fromkeys(keys, default)` | ⚠️ shared mutable default gotcha |
| Auto-default dict | `collections.defaultdict(factory)` | e.g. `defaultdict(int)`, `defaultdict(list)` |
| Frequency counting | `collections.Counter(iterable)` | `.most_common(n)` |
| Dict ↔ JSON | `json.dumps(d)` / `json.loads(s)` | — |

---

## Self-Test

1. What does an empty dict look like in code? → `{}`.
2. What does a dict with key `'foo'` and value `42` look like? → `{'foo': 42}`.
3. Main difference between a dict and a list? → dict accesses values by key, not by numeric position.
4. What happens if you access `spam['foo']` when `spam` is `{'bar': 100}`? → `KeyError: 'foo'`.
5. Difference between `'cat' in spam` and `'cat' in spam.keys()`? → none — they're equivalent; `in` on a dict directly checks keys.
6. Difference between `'cat' in spam` and `'cat' in spam.values()`? → the first checks keys, the second checks values — a value matching `'cat'` won't be found by the first form.
7. Shortcut for `if 'color' not in spam: spam['color'] = 'black'`? → `spam.setdefault('color', 'black')`.
8. Module + function to "pretty print" a dict? → `pprint.pprint()`.
9. Difference between `.pop(key)` and `del d[key]`? → `.pop()` **returns** the removed value; `del` does not.
10. Why does `dict.fromkeys(keys, [])` cause a bug if you later `.append()` to one entry? → all keys share the **same** list object (a reference), so mutating one appears to mutate all of them.
11. What error do you get from modifying a dict's size while iterating over it directly? → `RuntimeError: dictionary changed size during iteration` — iterate over `list(d.keys())` instead.
12. Since which Python version do dicts guarantee insertion order? → 3.7.

---
