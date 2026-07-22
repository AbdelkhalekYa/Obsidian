

Since you didn't specify a language, I'll cover **Python lists**, since that's the language used throughout pwntools and most CTF/security scripting (and it's the most common context for "lists & methods" as a study topic). If you meant a different language (JavaScript, C++, etc.), let me know and I'll adjust.

## 1. What is a List?

A **list** in Python is an **ordered, mutable (changeable) collection** of items. Items can be of any type, and you can mix types in one list.

```python
my_list = [1, 2, 3, "hello", True, 3.14]
```

**Key characteristics:**

|Property|Meaning|
|---|---|
|Ordered|Items keep the position you put them in|
|Mutable|You can change, add, remove items after creation|
|Indexed|Access items by position, starting at `0`|
|Allows duplicates|`[1, 1, 2]` is valid|
|Dynamic size|Grows/shrinks as needed|

---

## 2. Creating Lists

```python
empty = []
numbers = [1, 2, 3, 4, 5]
mixed = [1, "two", 3.0, [4, 5]]      # lists can contain lists (nested)
from_range = list(range(5))          # [0, 1, 2, 3, 4]
from_string = list("hello")          # ['h', 'e', 'l', 'l', 'o']
```

---

## 3. Indexing & Slicing

```python
nums = [10, 20, 30, 40, 50]

nums[0]     # 10   (first element)
nums[-1]    # 50   (last element)
nums[1:3]   # [20, 30]   (slice: index 1 up to, not including, 3)
nums[:2]    # [10, 20]   (from start to index 2)
nums[2:]    # [30, 40, 50]  (from index 2 to end)
nums[::-1]  # [50, 40, 30, 20, 10]  (reversed copy)
nums[::2]   # [10, 30, 50]  (every 2nd item)
```

**Comparison — indexing vs slicing:**

|Operation|Returns|Example|
|---|---|---|
|`nums[i]`|Single element|`nums[2]` → `30`|
|`nums[i:j]`|New list (sublist)|`nums[1:3]` → `[20, 30]`|

---

## 4. Core List Methods (with examples)

### Adding elements

|Method|Description|Example|Result|
|---|---|---|---|
|`.append(x)`|Adds `x` to the **end**|`[1,2].append(3)`|`[1, 2, 3]`|
|`.insert(i, x)`|Inserts `x` at index `i`|`[1,3].insert(1, 2)`|`[1, 2, 3]`|
|`.extend(iterable)`|Adds all items from another iterable|`[1,2].extend([3,4])`|`[1, 2, 3, 4]`|

> [!attention]+
> **Common mistake:** `append` vs `extend`
> 
> ```python
> a = [1, 2]
> a.append([3, 4])   # [1, 2, [3, 4]]  <- adds the whole list as ONE item
> b = [1, 2]
> b.extend([3, 4])   # [1, 2, 3, 4]    <- adds each item individually
> ```
> 

### Removing elements

|Method|Description|Example|Result|
|---|---|---|---|
|`.remove(x)`|Removes **first occurrence** of value `x`|`[1,2,2,3].remove(2)`|`[1, 2, 3]`|
|`.pop(i)`|Removes & **returns** item at index `i` (default: last)|`[1,2,3].pop()`|returns `3`, list becomes `[1, 2]`|
|`.clear()`|Removes **all** items|`[1,2,3].clear()`|`[]`|
|`del list[i]`|Deletes item at index (not a method, a statement)|`del a[0]`|removes index 0|

```python
a = [10, 20, 30]
removed = a.pop(1)   # removed = 20, a = [10, 30]
```

### Searching & Counting

|Method|Description|Example|Result|
|---|---|---|---|
|`.index(x)`|Returns index of first match|`[5,6,7].index(6)`|`1`|
|`.count(x)`|Counts occurrences of `x`|`[1,1,2,1].count(1)`|`3`|
|`x in list`|Membership check (returns bool)|`3 in [1,2,3]`|`True`|

```python
a = [10, 20, 30, 20]
a.index(20)   # 1 (first occurrence)
a.count(20)   # 2
```

### Ordering

|Method|Description|Example|Result|
|---|---|---|---|
|`.sort()`|Sorts **in place** (modifies original)|`[3,1,2].sort()`|`[1, 2, 3]`|
|`.sort(reverse=True)`|Sorts descending|`[3,1,2].sort(reverse=True)`|`[3, 2, 1]`|
|`.reverse()`|Reverses **in place**|`[1,2,3].reverse()`|`[3, 2, 1]`|
|`sorted(list)`|Returns a **new sorted list**, original untouched|`sorted([3,1,2])`|`[1, 2, 3]`|

> [!caution]
> **Key comparison — `.sort()` vs `sorted()`:**
> 
> ```python
> a = [3, 1, 2]
> b = sorted(a)     # b = [1, 2, 3], a is UNCHANGED: [3, 1, 2]
> 
> a.sort()          # a itself becomes [1, 2, 3], returns None
> ```
> This trips people up constantly: `x = a.sort()` gives `x = None`, not the sorted list.




**Sorting with a custom key:**

```python
words = ["banana", "kiwi", "apple"]
words.sort(key=len)          # sort by string length → ['kiwi', 'apple', 'banana']

people = [("Ali", 25), ("Sara", 19)]
people.sort(key=lambda p: p[1])   # sort by age → [('Sara', 19), ('Ali', 25)]
```

### Copying

|Method|Description|
|---|---|
|`.copy()`|Shallow copy of the list|
|`list(a)`|Also creates a shallow copy|
|`a[:]`|Slicing the full list also copies it|

⚠️ **Important pitfall — reference vs copy:**

```python
a = [1, 2, 3]
b = a          # b points to the SAME list as a
b.append(4)
print(a)       # [1, 2, 3, 4]  <- a changed too!

c = a.copy()   # c is a NEW, independent list
c.append(5)
print(a)       # [1, 2, 3, 4]  <- unaffected
```

This is one of the most common bugs for beginners — assignment (`=`) doesn't copy a list, it just creates another name pointing to the same object.

---

## 5. Other Useful Built-in Functions (not methods, but work with lists)

```python
len([1,2,3])         # 3
max([4,2,9,1])        # 9
min([4,2,9,1])        # 1
sum([1,2,3])           # 6
list(reversed([1,2,3])) # [3, 2, 1]  (returns iterator, wrap in list())
```

---

## 6. List Comprehensions (a Pythonic shortcut)

Instead of:

```python
squares = []
for x in range(5):
    squares.append(x*x)
```

You write:

```python
squares = [x*x for x in range(5)]   # [0, 1, 4, 9, 16]
```

With a condition:

```python
evens = [x for x in range(10) if x % 2 == 0]   # [0, 2, 4, 6, 8]
```

---

## 7. Lists vs Other Data Structures (comparison table)

|Feature|List `[]`|Tuple `()`|Set `{}`|Dict `{k:v}`|
|---|---|---|---|---|
|Ordered|✅|✅|❌ (unordered)|✅ (insertion order, 3.7+)|
|Mutable|✅|❌|✅|✅|
|Duplicates allowed|✅|✅|❌|Keys: ❌, Values: ✅|
|Indexed access|✅ `a[0]`|✅ `a[0]`|❌|❌ (by key only)|
|Use case|General collection, order matters|Fixed/constant data|Uniqueness, fast membership test|Key-value mapping|

---

## 8. Complexity Cheat Sheet (good to know for interviews/CTF scripting)

|Operation|Time Complexity|
|---|---|
|`a[i]` (index access)|O(1)|
|`a.append(x)`|O(1) amortized|
|`a.pop()` (from end)|O(1)|
|`a.pop(0)` (from front)|O(n) — shifts everything|
|`x in a`|O(n)|
|`a.sort()`|O(n log n)|
|`a.insert(0, x)`|O(n) — shifts everything|

If you're frequently inserting/removing from the **front** of a list, a `collections.deque` is far more efficient than a `list`.

---

## 9. Real Example Tying It to CTF Work

Since you're building pwntools exploit scripts, lists show up constantly — e.g., building a ROP chain:

```python
from pwn import *

rop_chain = []
rop_chain.append(p64(pop_rdi_gadget))
rop_chain.append(p64(bin_sh_addr))
rop_chain.append(p64(system_addr))

payload = b"A" * offset + b"".join(rop_chain)
```

Or filtering leaked data:

```python
leaked_bytes = [0x41, 0x42, 0x43]
leaked_str = bytes(leaked_bytes).decode()   # 'ABC'
```

---

Want me to go deeper into any specific part — like **list comprehensions**, **nested lists / 2D arrays**, **dictionaries and their methods** as a follow-up, or how lists specifically get used in **pwntools payload construction**?