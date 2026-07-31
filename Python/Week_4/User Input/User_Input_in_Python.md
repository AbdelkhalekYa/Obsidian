---
title: User Input in Python
tags:
  - python
  - input-output
aliases:
  - Python input() Function
  - Reading User Input
created: 2026-07-31
---

# User Input in Python

Accepting user input allows Python programs to become interactive. In Python 3, the primary mechanism for receiving input from the terminal/console is the built-in `input()` function.

---

## 1. The Basics of `input()`

The `input()` function pauses program execution, displays an optional prompt to the standard output, and waits for the user to type text and press **Enter**.

> [!NOTE] Fundamental Rule of `input()`
> The `input()` function **always returns a string (`str`)**, regardless of what the user types (even if they enter a number like `42` or `3.14`).

### Syntax & Simple Example
```python
# The optional string inside input() is the prompt shown to the user
name = input("Enter your name: ")
print(f"Hello, {name}!")
```

---

## 2. Converting Input Types

Because `input()` returns text, numeric or boolean operations require explicit type conversion (type casting).

### Reading Integers and Floats
```python
age = int(input("Enter your age: "))
height = float(input("Enter your height in meters: "))

print(f"Next year, you will be {age + 1} years old.")
print(f"Height in cm: {height * 100}")
```

### Reading Multiple Inputs in One Line
Use `.split()` to parse space-delimited or comma-delimited user input into a list of strings, and optionally map them to numbers:

```python
# Split input by spaces: "10 20 30" -> ["10", "20", "30"]
x, y, z = input("Enter three values separated by spaces: ").split()

# Convert multiple inputs to integers using map()
a, b, c = map(int, input("Enter three numbers: ").split())
print(f"Sum: {a + b + c}")

# Reading a list of floats separated by commas
numbers = list(map(float, input("Enter numbers separated by commas: ").split(',')))
```

---

## 3. Input Validation Patterns

Relying on direct type casting like `int(input())` can crash your program if the user enters invalid characters (e.g., typing `"five"` instead of `5`).

> [!CHECK] Best Practice: Robust Validation Loop
> Use a `while True` loop with a `try-except` block to repeatedly request input until a valid value is entered.

```python
def get_valid_integer(prompt: str) -> int:
    while True:
        try:
            return int(input(prompt))
        except ValueError:
            print("Invalid entry! Please enter a valid whole number.")

# Usage
user_age = get_valid_integer("Enter your age: ")
```

---

## 4. Advanced Input Techniques

### A. Secure Password Input (`getpass`)
When asking for sensitive data like passwords, standard `input()` displays characters on screen. The `getpass` module hides typed characters.

```python
import getpass

username = input("Username: ")
password = getpass.getpass("Password: ")  # Characters will not echo to screen
```

### B. Multi-Line Input
To read input across multiple lines until an empty line (or EOF) is entered:

```python
print("Enter your notes (press Enter on an empty line to finish):")
lines = []
while True:
    line = input()
    if not line:
        break
    lines.append(line)

multi_line_text = "
".join(lines)
```

Alternatively, to read directly from standard input until end-of-file (Ctrl+D on Linux/macOS, Ctrl+Z on Windows):

```python
import sys

print("Paste your long text here (Ctrl+D / Ctrl+Z to submit):")
full_text = sys.stdin.read()
```

---

## 5. Security & Edge Cases

> [!WARNING] Never Use `eval()` for Input Handling!
> Beginner tutorials sometimes recommend `eval(input())` to parse numbers or expressions automatically. **This is a severe security vulnerability** (Arbitrary Code Execution).
> 
> ```python
> # ❌ EXTREMELY DANGEROUS:
> user_data = eval(input()) # User could enter: __import__('os').system('rm -rf /')
> 
> # ✅ SAFE ALTERNATIVE for evaluating literals:
> import ast
> user_data = ast.literal_eval(input("Enter a list or dict: "))
> ```

### Gracefully Handling Keyboard Interrupts (`Ctrl+C` / `Ctrl+D`)
Users might forcefully quit your program during an input prompt. You can catch these built-in exceptions:

```python
try:
    user_data = input("Type something (or press Ctrl+C to cancel): ")
except KeyboardInterrupt:
    print("

Input cancelled by user. Exiting gracefully...")
except EOFError:
    print("

End of file encountered. Exiting...")
```

---

## Related Notes in Vault
- [[Type Conversion in Python]]
- [[Error and Exception Handling in Python]]
- [[Python Standard Library - sys & argparse]]
- [[Python Security Best Practices]]
