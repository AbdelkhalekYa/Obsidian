---
title: Type Conversion in Python
tags:
  - python
  - data-types
aliases:
  - Type Casting in Python
  - Implicit and Explicit Conversion
created: 2026-07-31
---

# Type Conversion in Python

In Python, **Type Conversion** (also known as **Type Casting** or **Type Coercion**) is the process of converting an object of one data type into another data type. Python provides two mechanisms for type conversion: **Implicit** (handled automatically by the interpreter) and **Explicit** (handled manually by the programmer).

---

## 1. Implicit Type Conversion (Type Coercion)

Implicit type conversion occurs automatically during evaluation when mixing different numerical data types in expressions. Python evaluates operations by promoting the "narrower" or "smaller" data type to a "broader" or "wider" data type to avoid loss of numerical precision.

> [!NOTE] Type Promotion Hierarchy
> When evaluating mixed numeric types, Python promotes types according to the following hierarchy:
> `bool` $\rightarrow$ `int` $\rightarrow$ `float` $\rightarrow$ `complex`

### Examples of Implicit Conversion

#### Numeric Coercion
```python
num_int = 10      # int
num_float = 2.5   # float

# Python implicitly converts `num_int` to float before addition
result = num_int + num_float

print(result)       # Output: 12.5
print(type(result)) # Output: <class 'float'>
```

#### Boolean in Numeric Contexts
In Python, `bool` is a subclass of `int`. `True` evaluates to `1` and `False` evaluates to `0`.

```python
print(True + 5)     # Output: 6
print(False * 10.5) # Output: 0.0
```

> [!WARNING] Implicit Conversion Limitations
> Python is a **strongly typed** dynamic language. It will **not** implicitly convert incompatible types, such as strings and numbers:
> ```python
> # Raises TypeError: unsupported operand type(s) for +: 'int' and 'str'
> total = 100 + "50" 
> ```

---

## 2. Explicit Type Conversion (Type Casting)

Explicit conversion is performed manually using Python's built-in conversion functions. This is required when implicit conversion is impossible or when you need strict control over data formats.

### A. Primitive Data Type Conversions

| Function | Source Types | Behavior / Notes | Example | Output |
| :--- | :--- | :--- | :--- | :--- |
| `int(x, base=10)` | `float`, `str`, `bool` | Truncates float toward zero; parses string digits | `int("1010", 2)` | `10` |
| `float(x)` | `int`, `str`, `bool` | Converts to IEEE 754 float | `float("3.14159")` | `3.14159` |
| `complex(real, imag)` | `int`, `float`, `str` | Creates complex number ($a + bi$) | `complex(3, 4)` | `(3+4j)` |
| `str(x)` | Any object | Invokes object's `__str__()` method | `str(123.45)` | `'123.45'` |
| `bool(x)` | Any object | Evaluates truthiness via `__bool__()` or `__len__()` | `bool("")` | `False` |

#### Detailed Primitive Examples
```python
# String to Integer with Base Conversion
binary_str = "1101"
decimal_val = int(binary_str, 2)  # Base 2 to Base 10 -> 13

hex_str = "1A"
decimal_val_hex = int(hex_str, 16) # Base 16 to Base 10 -> 26

# Float Truncation (not rounding!)
print(int(9.999))  # Output: 9
print(int(-2.8))   # Output: -2
```

### B. Base & Character Conversions

| Function | Description | Example | Result |
| :--- | :--- | :--- | :--- |
| `bin(x)` | Integer to binary string (`'0b...'`) | `bin(10)` | `'0b1010'` |
| `oct(x)` | Integer to octal string (`'0o...'`) | `oct(10)` | `'0o12'` |
| `hex(x)` | Integer to hexadecimal string (`'0x...'`) | `hex(255)` | `'0xff'` |
| `ord(c)` | Character to Unicode code point | `ord('A')` | `65` |
| `chr(i)` | Unicode code point to character | `chr(65)` | `'A'` |

```python
# Unicode conversions
char_code = ord('🐍')
print(char_code)        # Output: 128013
print(chr(128013))      # Output: '🐍'
```

### C. Collection & Sequence Conversions

Python allows seamlessly converting between container types (`list`, `tuple`, `set`, `frozenset`, `dict`).

```python
# String to List / Set / Tuple
text = "hello"
print(list(text))       # ['h', 'e', 'l', 'l', 'o']
print(set(text))        # {'h', 'e', 'l', 'o'} (unordered, deduplicated)

# Key-Value Pair Sequences to Dictionary
pairs = [("a", 1), ("b", 2)]
d = dict(pairs)
print(d)                # {'a': 1, 'b': 2}

# Zip two lists into a dictionary
keys = ['name', 'age', 'role']
values = ['Alice', 30, 'Engineer']
user_dict = dict(zip(keys, values))
print(user_dict)        # {'name': 'Alice', 'age': 30, 'role': 'Engineer'}
```

---

## 3. Custom Class Type Conversion (Magic Methods)

You can define custom conversion behavior for your own classes by implementing Python's dunder (double underscore) magic methods:

- `__int__(self)`: Defines behavior for `int(obj)`
- `__float__(self)`: Defines behavior for `float(obj)`
- `__bool__(self)`: Defines truthiness for `bool(obj)`
- `__str__(self)`: Readable string representation for `str(obj)` or `print(obj)`
- `__repr__(self)`: Unambiguous string representation for interactive shells and `repr(obj)`
- `__index__(self)`: Lossless integer representation for slicing and bin/hex/oct conversion

> [!TIP] Custom Class Conversion Example
```python
class Distance:
    def __init__(self, meters: float):
        self.meters = meters

    def __int__(self) -> int:
        return int(self.meters)

    def __float__(self) -> float:
        return float(self.meters)

    def __str__(self) -> str:
        return f"{self.meters} m"

    def __bool__(self) -> bool:
        return self.meters > 0

d = Distance(150.75)
print(int(d))    # 150
print(float(d))  # 150.75
print(str(d))    # "150.75 m"
print(bool(d))   # True
```

---

## 4. Common Pitfalls, Edge Cases & Exceptions

### 1. `ValueError` on Invalid String Conversion
If a string does not match the expected syntax for numeric conversion, Python raises `ValueError`.

```python
# Raises ValueError: invalid literal for int() with base 10: '12.34'
# Note: int() cannot directly convert float strings!
int("12.34") 

# Correct multi-step conversion:
int(float("12.34")) # Returns 12
```

### 2. Floating-Point Precision & Rounding Issues
Converting floats to ints always **truncates toward zero** rather than rounding.

```python
import math

val = 4.9
print(int(val))        # 4 (truncation)
print(round(val))      # 5 (rounding)
print(math.floor(val)) # 4 (floor)
print(math.ceil(val))  # 5 (ceiling)
```

### 3. Truthy and Falsy Values
`bool(x)` uses internal rules to decide truthiness. The following evaluate to `False`:
- `None`
- Numeric zeros: `0`, `0.0`, `0j`
- Empty collections/sequences: `""`, `()`, `[]`, `{}`, `set()`, `range(0)`

Everything else evaluates to `True`.

---

## 5. Defensive Conversion & Best Practices

> [!CHECK] Best Practice 1: Use `try-except` for Safe Casting
> Rather than manually parsing or using fragile string methods, follow Python's `EAFP` (Easier to Ask for Forgiveness than Permission) style.

```python
def safe_int_convert(val, default=0) -> int:
    try:
        return int(val)
    except (ValueError, TypeError):
        return default

print(safe_int_convert("100"))     # 100
print(safe_int_convert("invalid")) # 0
print(safe_int_convert(None))      # 0
```

> [!CHECK] Best Practice 2: `isinstance()` over Type Checks
> Prefer checking types with `isinstance()` before casting if you need to handle polymorphic behavior.

```python
data = 42
if isinstance(data, (int, float)):
    print(f"Numeric value: {float(data)}")
```

---

## Related Notes in Vault
- [[Python Data Structures]]
- [[Python Control Flow]]
- [[Python Magic Methods]]
- [[Error and Exception Handling in Python]]
