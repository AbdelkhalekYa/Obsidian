---
tags:
  - python
  - operators
  - assignment
source: Fully supplementary — not covered anywhere in the provided PDF excerpt
up: "[[Python Assignment Operators - MOC]]"
---

# Assignment 3 — Bitwise Augmented Assignment

← Previous: [[Assignment 2 - Arithmetic Augmented Assignment]]

> [!info] Not a typical beginner-tutorial topic
> Bitwise operators rarely show up in "Automate the Boring Stuff"-style intro material, but they're everywhere once you touch binary analysis, permission flags, networking, or low-level exploit scripting — directly relevant to your CTF track.

---

## 1. The Bitwise Operators (Non-Augmented Form First)

Bitwise operators act on the **individual bits** of integers, not the numbers as a whole.

| Operator | Name | Effect |
|---|---|---|
| `&` | AND | 1 only where **both** bits are 1 |
| `\|` | OR | 1 where **at least one** bit is 1 |
| `^` | XOR | 1 where the bits **differ** |
| `~` | NOT | flips every bit |
| `<<` | left shift | shifts bits left (multiply by 2 per shift) |
| `>>` | right shift | shifts bits right (divide by 2 per shift) |

```python
5 & 3     # 0b101 & 0b011 = 0b001 = 1
5 | 3      #  0b101 | 0b011 = 0b111 = 7
5 ^ 3       # 0b101 ^ 0b011 = 0b110 = 6
5 << 1        # 0b101 -> 0b1010 = 10
5 >> 1         # 0b101 -> 0b010 = 2
```

> [!tip] Reading binary literals directly
> Python lets you write binary literals with `0b`: `0b101` is `5`. Combined with the `bin()` function from [[Operators 3 - Type Conversion]], this makes bit-level reasoning much easier to follow than working entirely in decimal.

---

## 2. The Bitwise Augmented Assignment Operators

Exactly the same shorthand pattern as the arithmetic ones in [[Assignment 2 - Arithmetic Augmented Assignment]], applied to bitwise operators:

| Full form | Shorthand |
|---|---|
| `flags = flags & mask` | `flags &= mask` |
| `flags = flags \| mask` | `flags \|= mask` |
| `flags = flags ^ mask` | `flags ^= mask` |
| `flags = flags << 1` | `flags <<= 1` |
| `flags = flags >> 1` | `flags >>= 1` |

```python
flags = 0b0100
flags |= 0b0001     # set a bit  -> 0b0101
flags &= ~0b0100      # clear a bit -> 0b0001
flags ^= 0b0001         # toggle a bit -> 0b0000
```

---

## 3. Practical Pattern: Flag Manipulation

Bitwise operators are the standard way to work with **bit-flag** style data — where each bit independently represents an on/off setting, packed into a single integer.

```python
READ    = 0b001   # 1
WRITE   = 0b010   # 2
EXECUTE = 0b100   # 4

permissions = 0
permissions |= READ            # set the READ bit  -> 0b001
permissions |= WRITE             # set the WRITE bit -> 0b011

is_readable = bool(permissions & READ)     # True — check if a bit is set
is_executable = bool(permissions & EXECUTE)  # False

permissions &= ~WRITE            # clear the WRITE bit -> 0b001
```

| Operation | Bitwise idiom |
|---|---|
| Set a bit | `flags \|= bit` |
| Clear a bit | `flags &= ~bit` |
| Toggle a bit | `flags ^= bit` |
| Check a bit | `bool(flags & bit)` |

This is exactly the pattern already previewed in [[Booleans 5 - Beyond the Basics]] for ELF program header permission flags (`PF_R`, `PF_W`, `PF_X`) — this page is the fuller treatment of the mechanism behind that example.

---

## 4. Security / CTF-Relevant Use Cases

- **ELF/binary permission flags** — `checksec` and program headers encode readable/writable/executable as individual bits; reading raw ELF data with `readelf`/`pyelftools`, you'll often decode these with exactly this bitwise pattern.
- **Network protocol fields** — packet flags (e.g., TCP flags: SYN, ACK, FIN, RST) are classic bit-flag fields when working with raw sockets or packet crafting (e.g., `scapy`).
- **XOR-based encoding/ciphers** — `^=` is the operator behind single-byte and repeating-key XOR "encryption," a very common CTF crypto/RE challenge category (see the crypto-identification content referenced by your CTF roadmap's Phase 2, Week 12).
  ```python
  key = 0x42
  encoded = bytes(b ^ key for b in plaintext_bytes)
  ```
- **Shift operators for address/offset math** — `<<`/`>>` show up when computing page-aligned addresses (`addr & ~0xfff` to round down to a page boundary) or packing/unpacking multi-byte values manually, alongside the `int.to_bytes()`/`int.from_bytes()` material from [[Operators 3 - Type Conversion]].

---

## Next
→ [[Assignment 4 - Multiple Assignment & Extended Unpacking]]
