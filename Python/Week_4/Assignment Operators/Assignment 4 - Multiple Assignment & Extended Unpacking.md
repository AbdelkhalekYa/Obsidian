---
tags:
  - python
  - operators
  - assignment
source: Recap of Lists 2 / Tuples 2 plus supplementary extended-unpacking depth
up: "[[Python Assignment Operators - MOC]]"
---

# Assignment 4 — Multiple Assignment & Extended Unpacking

← Previous: [[Assignment 3 - Bitwise Augmented Assignment]]

---

## 1. Recap: Basic Multiple Assignment (Unpacking)

Already covered in [[Lists 2 - Modifying & Looping]] and [[Tuples 2 - Methods, Packing & Unpacking]] — briefly, for completeness in this "assignment operators" series:

```python
cat = ['fat', 'black', 'loud']
size, color, disposition = cat   # count must match exactly
```

> [!note] Not technically an "operator"
> Unpacking uses `=` itself — it's not a distinct operator — but it's included in this series because it's fundamentally an **assignment mechanism**, and the extended forms below aren't covered elsewhere in this vault.

---

## 2. Extended (Starred) Unpacking — `*`

Introduced briefly in [[Tuples 2 - Methods, Packing & Unpacking]] — here's the fuller picture. The `*` prefix captures "everything else" into a list, letting you unpack sequences of unknown/variable length.

```python
first, *rest = [1, 2, 3, 4, 5]
print(first)   # 1
print(rest)     # [2, 3, 4, 5]

*rest, last = [1, 2, 3, 4, 5]
print(rest)      # [1, 2, 3, 4]
print(last)        # 5

first, *middle, last = [1, 2, 3, 4, 5]
print(first, middle, last)   # 1 [2, 3, 4] 5
```

> [!warning] Only one starred target allowed
> ```python
> first, *a, *b, last = [1, 2, 3, 4, 5]   # SyntaxError — can't have two starred targets
> ```

---

## 3. Nested Unpacking

Unpacking works recursively through nested structures — you can mirror the shape of the data directly in the assignment target:

```python
(a, b), c = (1, 2), 3
print(a, b, c)   # 1 2 3

data = ['Alice', (30, 'engineer')]
name, (age, job) = data
print(name, age, job)   # Alice 30 engineer
```

---

## 4. Unpacking in `for` Loops

Every iteration variable assignment in a `for` loop is unpacking under the hood — this is *why* `for k, v in d.items():` works (see [[Dictionaries 2 - Iterating, Keys, Values & Items]]):

```python
pairs = [(1, 'a'), (2, 'b'), (3, 'c')]
for num, letter in pairs:
    print(num, letter)
```

### With starred unpacking too
```python
records = [('Alice', 90, 85, 88), ('Bob', 70, 75, 72)]
for name, *scores in records:
    print(name, sum(scores) / len(scores))
```

---

## 5. Ignoring Values You Don't Need — the `_` Convention

By convention (not a language rule), `_` signals "I'm unpacking this position but I don't care about the value":

```python
name, _, age = ('Alice', 'unused_middle_field', 30)

# common with enumerate() or .items() when you only need one side
for _, value in some_dict.items():
    process(value)
```

---

## Next
→ [[Assignment 5 - Beyond the Basics]]
