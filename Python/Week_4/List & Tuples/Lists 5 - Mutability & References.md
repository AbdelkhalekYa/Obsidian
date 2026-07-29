---
tags:
  - python
  - lists
source: Automate the Boring Stuff with Python — Chapter 4
up: "[[Python Lists - MOC]]"
---

# Lists 5 — Mutability & References

← Previous: [[Lists 4 - Tuples & Strings (List-Like Types)]]

---

## 1. Mutable vs. Immutable

- **Mutable** (list): can be changed **in place** — items added, removed, or altered without creating a new object.
- **Immutable** (string, tuple, int): cannot be changed. Any "modification" actually builds a **new** object.

```python
name = 'Zophie a cat'
newName = name[0:7] + 'the' + name[8:12]   # builds a brand-new string
# `name` itself is completely untouched: 'Zophie a cat'
```

```python
eggs = [1, 2, 3]
eggs = [4, 5, 6]   # this REPLACES what eggs points to; the original [1,2,3] list still existed momentarily but is now orphaned
```
To *actually* mutate the original list object into holding different values (rather than pointing `eggs` at a new one), you'd use `del`/`.append()` on the existing list:
```python
eggs = [1, 2, 3]
del eggs[2]; del eggs[1]; del eggs[0]
eggs.append(4); eggs.append(5); eggs.append(6)
# eggs -> [4, 5, 6]   (same underlying list object, values changed in place)
```

---

## 2. References — The Trickiest but Most Important Concept

> [!warning] This is the #1 source of confusing bugs for beginners.

Simple values (`int`, `str`) are copied **by value**. Lists are copied **by reference** — the variable stores a *pointer* to the list object, not the list's contents directly.

```python
spam = 42
cheese = spam     # cheese gets a COPY of the value 42
spam = 100
cheese             # still 42 — unaffected, because ints are simple values
```

```python
spam = [0, 1, 2, 3, 4, 5]
cheese = spam            # cheese now points to the SAME list object as spam
cheese[1] = 'Hello!'
print(spam)     # [0, 'Hello!', 2, 3, 4, 5]  <- changed too!
print(cheese)   # [0, 'Hello!', 2, 3, 4, 5]
```
Both variables refer to the exact same underlying list — no second list was ever created. Think of the variable as a box containing not the list, but an **ID number pointing to** the list stored elsewhere in memory.

---

## 3. Why This Matters for Function Calls (Passing References)

```python
def eggs(someParameter):
    someParameter.append('Hello')

spam = [1, 2, 3]
eggs(spam)
print(spam)   # [1, 2, 3, 'Hello']   <- the function mutated the ORIGINAL list
```
Unlike immutable arguments (ints, strings), a function can permanently modify a list passed into it — with **no `return` statement needed**. This is a common source of "wait, why did calling this function change my variable?" bugs.

---

## 4. Breaking the Link: the `copy` Module

| Function | What it does |
|---|---|
| `copy.copy(x)` | **Shallow copy** — a genuinely new list, but any *nested* lists/dicts inside are still shared references |
| `copy.deepcopy(x)` | **Deep copy** — recursively duplicates everything, fully independent from the original |

```python
import copy
spam = ['A', 'B', 'C', 'D']
cheese = copy.copy(spam)
cheese[1] = 42
print(spam)     # ['A', 'B', 'C', 'D']   <- unaffected
print(cheese)   # ['A', 42, 'C', 'D']
```
Use `deepcopy()` specifically when your list contains **nested** lists/dicts that you also want fully isolated from the original.

---

## Next
→ [[Lists 6 - Summary & Self-Test]]
