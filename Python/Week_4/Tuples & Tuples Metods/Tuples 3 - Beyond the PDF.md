---
tags: [python, programming, tuples, advanced]
source: Fully supplementary — not covered anywhere in the provided PDF excerpt
up: "[[Python Tuples - MOC]]"
---

# Tuples 3 — Beyond the PDF

← Previous: [[Tuples 2 - Methods, Packing & Unpacking]]

---

## 1. Tuples as Dictionary Keys / Set Members

This is the single biggest practical reason to reach for a tuple over a list: **hashability** (see [[Tuples 1 - Basics, Creation & Immutability]] and [[Sets 1 - Basics & Creation]] for why lists can't do this).

```python
locations = {
    (0, 0): 'origin',
    (1, 2): 'point A',
    (3, 4): 'point B',
}
locations[(1, 2)]   # 'point A'
```

```python
visited = set()
visited.add((0, 0))
visited.add((1, 1))
(0, 0) in visited   # True
```

> [!tip] Classic use case: grid/graph coordinates
> Tuples-as-dict-keys is the standard way to represent a 2D (or 3D) grid position, graph node, or "visited" set in pathfinding/traversal code — e.g., a BFS/DFS `visited` set of `(x, y)` coordinates, exactly the kind of pattern referenced in [[Sets 5 - Performance & Real-World Use Cases]] for control-flow-graph traversal during reverse engineering.

### Reminder: nested mutable contents break this
```python
{(1, [2, 3]): 'value'}   # TypeError: unhashable type: 'list'
```
A tuple containing a list is *not* hashable, because hashability requires everything inside to also be immutable — see the nesting warning in [[Tuples 1 - Basics, Creation & Immutability]].

---

## 2. `namedtuple` — Tuples With Field Names

Plain tuples are positional — `point[0]`, `point[1]` — which gets unreadable fast. `collections.namedtuple` gives you tuple performance/immutability **with named field access**, without the overhead of a full class:

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
p = Point(3, 4)

p.x       # 3
p.y        # 4
p[0]         # 3  -- still works positionally too!
p == (3, 4)    # True -- still compares as a regular tuple
```

**Comparison: plain tuple vs. namedtuple vs. dict**

| | Plain tuple | `namedtuple` | `dict` |
|---|---|---|---|
| Access | `p[0]` | `p.x` or `p[0]` | `p['x']` |
| Mutable | ❌ | ❌ | ✅ |
| Memory-efficient | ✅ | ✅ (same as tuple) | less efficient |
| Self-documenting | ❌ (magic numbers) | ✅ (named fields) | ✅ (named keys) |
| Hashable | ✅ | ✅ | ❌ |

> [!tip] When to reach for `namedtuple`
> Any time you'd otherwise return/pass around a tuple of 3+ values and keep having to remember "wait, was the third element the timestamp or the status code?" — `namedtuple` fixes exactly that readability problem while keeping tuple performance and hashability.

---

## 3. Tuple vs. List — Full Decision Table

| Situation | Use |
|---|---|
| Data that will change over time | `list` |
| Fixed collection, "this shouldn't change" | `tuple` |
| Need it as a dict key or set member | `tuple` (list is impossible) |
| Returning multiple values from a function | `tuple` (Python's own convention) |
| Coordinates, RGB values, database rows | `tuple` (fixed-size, fixed-meaning-per-position) |
| A growing/shrinking collection | `list` |
| Want named-field readability + immutability | `namedtuple` |

---

## 4. Performance Note

Tuples are marginally faster to create and iterate than lists, and use slightly less memory — because Python doesn't need to allocate extra space for potential future growth (something it does for lists to make `.append()` fast on average). This difference is usually negligible for small scripts, but matters at scale (e.g., millions of fixed-size records) — see [[Lists 4 - Tuples & Strings (List-Like Types)]] for the original mention of this.

---

## 5. Security / CTF-Relevant Use Cases

- **`(host, port)` tuples** are the standard way sockets are addressed in Python's `socket` module and, by extension, in `pwntools`' `remote()` calls — you'll see `(target_ip, target_port)` constantly.
- **Immutable exploit constants** — grouping fixed offsets/addresses that must never accidentally be reassigned mid-script (`OFFSET = (0x28, 0x38, 'pop rdi ; ret')`) signals intent clearly and prevents an accidental overwrite bug in a long exploit script.
- **Graph/CFG traversal** — as mentioned above, `(node_a, node_b)` edge tuples or `(x, y)` coordinate tuples as set members/dict keys are the standard representation when scripting BFS/DFS over a binary's control-flow graph with `angr` or a hand-rolled disassembler walker.
- **Struct-like return values** — when writing a Python disassembler for a custom VM (your CTF roadmap's Week 18), you'll likely return `(opcode, operand)` tuples per decoded instruction — a `namedtuple` would make that code significantly more readable than raw indices.

---

## Next
→ [[Tuples 4 - Summary & Self-Test]]
