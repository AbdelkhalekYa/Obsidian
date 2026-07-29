---
tags:
  - python
  - lists
  - methods
source: Automate the Boring Stuff with Python — Chapter 4
up: "[[Python Lists - MOC]]"
---

# Lists 2 — Modifying & Looping

← Previous: [[Lists 1 - Basics & Indexing]]

---

## 1. Changing Values by Index

Normally the variable goes on the left of `=`. But you can also target an index:

```python
spam = ['cat', 'bat', 'rat', 'elephant']
spam[1] = 'aardvark'
# spam -> ['cat', 'aardvark', 'rat', 'elephant']

spam[2] = spam[1]   # copy a value from one slot to another
spam[-1] = 12345    # can assign any type, not just strings
```
This works because lists are **mutable** — see [[Lists 5 - Mutability & References]].

---

## 2. Concatenation and Replication

**Concatenation (`+`)** joins two lists into a new one:
```python
[1, 2, 3] + ['A', 'B', 'C']   # [1, 2, 3, 'A', 'B', 'C']
```

**Replication (`*`)** repeats a list's contents a given number of times:
```python
['X', 'Y', 'Z'] * 3   # ['X','Y','Z','X','Y','Z','X','Y','Z']
```

```python
spam = [1, 2, 3]
spam = spam + ['A', 'B', 'C']   # rebuilds spam with the new combined list
```

---

## 3. Removing Values with `del`

```python
spam = ['cat', 'bat', 'rat', 'elephant']
del spam[2]
# spam -> ['cat', 'bat', 'elephant']
del spam[2]
# spam -> ['cat', 'bat']
```
Every item after the deleted index shifts up by one to fill the gap. `del` can also delete a plain variable entirely (rare in practice — mostly used on list items).

---

## 4. Working With Lists — Why Bother?

Beginners often reach for many separate variables (`catName1`, `catName2`, `catName3`...) instead of a list. This is a trap: the program can never handle more items than you hand-wrote variables for, and the code duplicates itself endlessly.

**Bad — hardcoded, doesn't scale:**
```python
print('Enter the name of cat 1:')
catName1 = input()
print('Enter the name of cat 2:')
catName2 = input()
# ...repeats forever, capped at a fixed number
```

**Good — a list grows to fit any amount of input:**
```python
catNames = []
while True:
    print('Enter a cat name (or nothing to stop):')
    name = input()
    if name == '':
        break
    catNames = catNames + [name]   # list concatenation

print('The cat names are:')
for name in catNames:
    print(' ' + name)
```

> [!tip] Takeaway
> Whenever you catch yourself numbering variables (`x1, x2, x3...`), that's almost always a sign you should be using a list instead.

---

## 5. Looping Over Lists

```python
for i in range(4):
    print(i)
```
`range(4)` behaves like the list-like sequence `[0, 1, 2, 3]` — a `for` loop technically repeats its block once per value in a list (or list-like value).

```python
for i in [0, 1, 2, 3]:
    print(i)   # identical output to the range() version above
```

### `range(len(list))` pattern — index *and* value together
```python
supplies = ['pens', 'staplers', 'flame-throwers', 'binders']
for i in range(len(supplies)):
    print('Index ' + str(i) + ' in supplies is: ' + supplies[i])
```
Handy because it scales automatically no matter how many items `supplies` contains.

### `in` and `not in`
Test membership directly, without writing a loop yourself:

```python
'howdy' in ['hello', 'hi', 'howdy', 'heyas']   # True
'cat' in ['hello', 'hi']                        # False
'cat' not in ['hello', 'hi']                    # True
```

**Practical example:**
```python
myPets = ['Zophie', 'Pooka', 'Fat-tail']
name = input('Enter a pet name: ')
if name not in myPets:
    print('I do not have a pet named ' + name)
else:
    print(name + ' is my pet.')
```

---

## 6. The Multiple Assignment Trick

Unpack list items straight into separate variables in one line:

```python
cat = ['fat', 'black', 'loud']

# instead of:
size = cat[0]
color = cat[1]
disposition = cat[2]

# do this:
size, color, disposition = cat
```

> [!warning] Count must match exactly
> ```python
> size, color, disposition, name = cat   # only 3 items in cat!
> # ValueError: need more than 3 values to unpack
> ```

---

## 7. Augmented Assignment Operators

Shorthand for "update a variable using its current value":

| Full form | Shorthand |
|---|---|
| `spam = spam + 1` | `spam += 1` |
| `spam = spam - 1` | `spam -= 1` |
| `spam = spam * 1` | `spam *= 1` |
| `spam = spam / 1` | `spam /= 1` |
| `spam = spam % 1` | `spam %= 1` |

`+=` and `*=` also work on **lists and strings**:

```python
spam = 'Hello'
spam += ' world!'      # 'Hello world!'

bacon = ['Zophie']
bacon *= 3              # ['Zophie', 'Zophie', 'Zophie']
```

---

## Next
→ [[Lists 3 - Methods]]
