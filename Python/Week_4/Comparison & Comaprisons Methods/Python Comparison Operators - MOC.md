---
tags:
  - python
  - comparison
  - intro
---

# Python Comparison Operators — Map of Content

> [!info] Source note
> Condensed versions of this topic already exist in [[Operators 2 - Comparison Operators]] and [[Booleans 2 - Comparison Operators]]. This series is the deeper standalone treatment — it adds lexicographic sequence comparison, chained-comparison pitfalls, a full `is` vs `==` deep dive, and how comparisons work for custom objects. None of this expanded material is in the provided PDF excerpt.

1. [[Comparison 1 - The Six Operators & Type Rules]] — `==` `!=` `<` `>` `<=` `>=`, which types can be compared, `TypeError` for unorderable types
2. [[Comparison 2 - Lexicographic Comparison of Sequences]] — how lists/tuples/strings actually compare element-by-element *(new depth beyond earlier notes)*
3. [[Comparison 3 - Chained Comparisons & Pitfalls]] — chaining mechanics, the NaN trap, common mistakes
4. [[Comparison 4 - is vs == Deep Dive]] — identity vs equality, small-int caching, string interning, when each is correct
5. [[Comparison 5 - Beyond the Basics]] — custom `__eq__`/`__lt__`, `functools.total_ordering`, sorting with `key=`, the `operator` module, CTF relevance
6. [[Comparison 6 - Summary & Self-Test]] — quick-reference table + practice questions

## Related
- [[Operators 2 - Comparison Operators]]
- [[Booleans 2 - Comparison Operators]]
- [[Python Booleans - MOC]]
