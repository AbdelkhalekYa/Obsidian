---
tags:
  - python
  - operators
  - comparison
source: Fully supplementary — not covered anywhere in the provided PDF excerpt
up: "[[Python Comparison Operators - MOC]]"
---

# Comparison 2 — Lexicographic Comparison of Sequences

← Previous: [[Comparison 1 - The Six Operators & Type Rules]]

> [!info] What this page adds
> Earlier notes established *that* `[1,2,3] == [1,2,3]` is `True` and order matters for lists (see [[Dictionaries 1 - Basics & Creation]]) — but not *how* Python actually determines `[1, 2] < [1, 3]`. This page covers the actual algorithm.

---

## 1. How `<`/`>` Work on Lists, Tuples, and Strings

Sequences are compared **lexicographically** — the same principle as alphabetical/dictionary ordering: compare element-by-element from the start, and the first difference decides the result.

```python
[1, 2, 3] < [1, 2, 4]     # True  -- first two elements tie, third element decides: 3 < 4
[1, 2, 3] < [1, 3, 0]      # True  -- second element decides: 2 < 3 (never even looks at the third)
[1, 2] < [1, 2, 3]           # True  -- all shared elements tie, shorter sequence is "less"
[1, 2, 3] < [1, 2]             # False -- same rule, reversed
```

```python
'apple' < 'banana'    # True  -- 'a' < 'b' at the first character
'apple' < 'applesauce'  # True  -- 'apple' is a PREFIX of 'applesauce', so it's "less" (shorter wins ties)
```

> [!tip] This is exactly how a dictionary/phone book sorts words
> Compare letter-by-letter until you find a difference; if one word runs out first while still matching, the shorter one comes first. Python applies the identical rule to lists and tuples, just comparing *elements* instead of *letters*.

---

## 2. Comparisons Stop at the First Difference — Later Elements Are Irrelevant

```python
[1, 999, 3] < [1, 2, 0]     # False -- 999 < 2 is False, decided at position 1, position 2 never checked
```
Because comparison short-circuits at the first mismatch (conceptually similar to the short-circuit evaluation in [[Booleans 4 - Short-Circuit Evaluation & Precedence]], though this is a different mechanism), a single very "large" element early on can decide the entire comparison, regardless of what follows.

---

## 3. Mixed-Type Elements Inside a Sequence Comparison

The elements being compared still need to be comparable to each other under the type rules from [[Comparison 1 - The Six Operators & Type Rules]]:

```python
[1, 2] < [1, '2']    # TypeError -- fails trying to compare 2 < '2' at position 1
[1, 2] < ['1', 2]      # TypeError -- fails immediately at position 0: 1 < '1'
```
The comparison only proceeds past a position if that position's elements are **equal**; the moment it needs to determine `<`/`>` between incomparable types, it raises `TypeError` just like a standalone comparison would.

---

## 4. Sets and Dicts Do NOT Support Lexicographic `<`/`>`

Recall from [[Sets 4 - Comparisons, Subsets & Frozensets]]: for sets, `<` and `>` mean **subset/superset**, not "lexicographically smaller" — a completely different semantic borrowed from mathematical set theory rather than sequence ordering.

```python
{1, 2} < {1, 2, 3}    # True -- means "is a PROPER SUBSET of", not "comes before"
```

Dictionaries don't support ordering comparisons (`<`/`>`) at all:
```python
{'a': 1} < {'a': 2}   # TypeError: '<' not supported between instances of 'dict' and 'dict'
```

### Comparison semantics by type — summary

| Type | What `<` means |
|---|---|
| `int`/`float` | numeric magnitude |
| `str` | lexicographic (character-by-character, by code point) |
| `list`/`tuple` | lexicographic (element-by-element) |
| `set`/`frozenset` | subset relationship |
| `dict` | ❌ not supported |

---

## Next
→ [[Comparison 3 - Chained Comparisons & Pitfalls]]
