---
tags:
  - python
  - dictionaries
  - methods
source: Automate the Boring Stuff with Python — Chapter 5 (intro)
up: "[[Python Dictionaries - MOC]]"
---

# Dictionaries 2 — Iterating, Keys, Values & Items

← Previous: [[Dictionaries 1 - Basics & Creation]]

---

## 1. `.keys()`, `.values()`, `.items()`

These three methods return **list-like views** of a dictionary's contents — iterable, but not true lists (they have no `.append()`, for example). Their actual types are `dict_keys`, `dict_values`, and `dict_items`.

```python
spam = {'color': 'red', 'age': 42}

for v in spam.values():
    print(v)
# red
# 42

for k in spam.keys():
    print(k)
# color
# age

for i in spam.items():
    print(i)
# ('color', 'red')
# ('age', 42)
```

Notice `.items()` gives you **tuples** of `(key, value)`.

### Converting to a real list
```python
spam.keys()          # dict_keys(['color', 'age'])
list(spam.keys())     # ['color', 'age']   -- an actual list
```

### Unpacking both key and value in one loop (very common pattern)
```python
spam = {'color': 'red', 'age': 42}
for k, v in spam.items():
    print(f'Key: {k} Value: {v}')
# Key: color Value: red
# Key: age Value: 42
```
This uses the same multiple-assignment unpacking trick covered in [[Lists 2 - Modifying & Looping]], applied to each `(key, value)` tuple as the loop goes.

---

## 2. Membership Checks — `in` / `not in`

```python
spam = {'name': 'Zophie', 'age': 7}

'name' in spam.keys()      # True — checks KEYS
'Zophie' in spam.values()   # True — checks VALUES
'color' in spam.keys()      # False
'color' not in spam.keys()  # True
```

> [!tip] Shortcut
> `x in spam` on its own is shorthand for `x in spam.keys()` — checking a dict directly with `in` always tests the **keys**, never the values, unless you explicitly call `.values()`.
> ```python
> 'color' in spam   # same as 'color' in spam.keys() -> False
> ```

---

## 3. Full Worked Example — Birthday Database

```python
birthdays = {'Alice': 'Apr 1', 'Bob': 'Dec 12', 'Carol': 'Mar 4'}

while True:
    print('Enter a name: (blank to quit)')
    name = input()
    if name == '':
        break
    if name in birthdays:
        print(birthdays[name] + ' is the birthday of ' + name)
    else:
        print('I do not have birthday information for ' + name)
        print('What is their birthday?')
        bday = input()
        birthdays[name] = bday
        print('Birthday database updated.')
```

This demonstrates the full loop: check membership with `in`, read with `[]`, and **write/update** with `[] =` (covered further in [[Dictionaries 4 - Modifying Dictionaries]]). Note that everything typed in is lost when the program ends — persisting it to disk is a file-I/O topic, not a dictionary one.

---

## Next
→ [[Dictionaries 3 - Safe Access - get() & setdefault()]]
