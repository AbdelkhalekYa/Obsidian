---
tags:
  - python
  - sets
source: Supplementary (not in the provided Automate the Boring Stuff excerpt) — general Python reference
up: "[[Python Sets - MOC]]"
---

# Sets 5 — Performance & Real-World Use Cases

← Previous: [[Sets 4 - Comparisons, Subsets & Frozensets]]

---

## 1. Why Sets Are Fast: Hash Tables

A `set` (like a `dict`) is built on a **hash table** internally. When you check `x in my_set`, Python computes a hash of `x` and jumps almost directly to where it would be stored — it doesn't have to scan every element.

A `list` has no such shortcut — checking `x in my_list` must potentially compare against **every item** until it finds a match (or reaches the end).

### Big-O comparison

| Operation | `list` | `set` / `dict` |
|---|---|---|
| Membership test (`x in ...`) | O(n) — average, scans the whole list | **O(1)** — average, near-instant |
| Add an item | O(1) (`.append()`) | O(1) (`.add()`) |
| Remove a specific item | O(n) (`.remove()` must search) | **O(1)** (`.remove()`/`.discard()`) |
| Preserves order | ✅ | ❌ |
| Allows duplicates | ✅ | ❌ |

> [!tip] Practical rule of thumb
> If your code repeatedly does `if x in my_collection:` inside a loop, and `my_collection` has more than a handful of items, converting it to a `set` first can turn an O(n²) script into an O(n) one. This is one of the most common "why is my script so slow" fixes in real-world Python.

```python
# Slow-ish for large lists: O(n) check, done n times = O(n²) overall
blocked_ips = [ ... thousands of entries ... ]
for ip in incoming_ips:
    if ip in blocked_ips:      # O(n) scan every time
        block(ip)

# Fast: O(1) check, done n times = O(n) overall
blocked_ips_set = set(blocked_ips)
for ip in incoming_ips:
    if ip in blocked_ips_set:   # O(1) lookup
        block(ip)
```

---

## 2. Deduplication

The single most common real-world use of sets:

```python
raw = [1, 2, 2, 3, 3, 3, 4]
unique = list(set(raw))
# [1, 2, 3, 4]   -- but ORDER IS NOT GUARANTEED to match the original
```

> [!warning] Sets don't preserve order
> If you need to dedupe **and keep the original order**, use a `dict` instead (Python 3.7+ dicts preserve insertion order, and `dict.fromkeys()` drops duplicate keys automatically):
> ```python
> raw = [3, 1, 2, 3, 2, 1]
> ordered_unique = list(dict.fromkeys(raw))
> # [3, 1, 2]   -- order preserved, duplicates removed
> ```

---

## 3. Comparing Two Collections

Sets make "what changed between these two things" trivial — far more readable than nested loops.

```python
scanned_last_week = {'10.0.0.1', '10.0.0.2', '10.0.0.3'}
scanned_today       = {'10.0.0.2', '10.0.0.3', '10.0.0.4'}

new_hosts       = scanned_today - scanned_last_week     # {'10.0.0.4'}
dropped_hosts   = scanned_last_week - scanned_today      # {'10.0.0.1'}
unchanged_hosts = scanned_today & scanned_last_week       # {'10.0.0.2', '10.0.0.3'}
```

---

## 4. Security / CTF-Relevant Use Cases

Since these notes feed into your CTF prep, here's where sets specifically show up in that context:

- **Graph traversal (BFS/DFS) over a binary's control-flow graph** — a `visited` set tracks which basic blocks/nodes you've already explored, giving O(1) "have I seen this address already?" checks instead of scanning a list every step. This exact pattern shows up when scripting with `angr`, `radare2`/`r2pipe`, or hand-rolled CFG walkers.
- **Deduplicating found strings/flags** across multiple binary samples or memory dumps.
- **Diffing two wordlists or symbol sets** — e.g., comparing exported functions between two versions of a library to spot what changed (`new_symbols = libc_v2_syms - libc_v1_syms`).
- **Tracking unique bytes/opcodes seen** while reverse-engineering a custom VM's bytecode — `set(bytecode)` instantly tells you how many distinct opcodes exist.
- **Character set analysis** in crypto/encoding challenges — `set(ciphertext)` quickly reveals if you're dealing with a small alphabet (e.g., hex, base64) vs. general byte noise.
- **Bad-character checks** in exploit dev — confirming your payload doesn't contain a forbidden byte: `set(payload) & set(bad_chars)` should be empty.

---

## 5. When *Not* to Use a Set

| Situation | Use instead |
|---|---|
| You need to preserve order | `list` (or `dict.fromkeys()` for ordered dedup) |
| You need duplicates | `list` |
| You need to access by position (`x[0]`) | `list` or `tuple` |
| You need key→value mapping | `dict` |
| Elements aren't hashable (e.g., lists of lists) | `list` (or convert inner lists to tuples first) |

---

## Next
→ [[Sets 6 - Summary & Self-Test]]
