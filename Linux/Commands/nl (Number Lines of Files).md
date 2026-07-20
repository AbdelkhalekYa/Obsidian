#commands 

## `nl` — Number Lines of Files

`nl` numbers the lines of a file and writes the result to standard output — similar to `cat -n`, but far more configurable (custom numbering formats, skip blank lines, restart numbering per section, etc.). Syntax: `nl [OPTIONS] [FILE]`

---

### 1. (no flag) — basic usage

Numbers every non-blank line by default (blank lines are _not_ numbered unless told to).

```bash
nl script.py
```

Output:

```
     1  #!/usr/bin/env python3
     2  from pwn import *
       
     3  elf = ELF('./binary')
```

Notice the blank line stays unnumbered by default — different from `cat -n`, which numbers _every_ line including blanks.

---

### 2. `-b TYPE` (body numbering style)

Controls which lines get numbered. This is the most important flag to understand:

- `-b a` — number **all** lines, including blank ones
- `-b t` — number only **non-blank** lines (this is the default)
- `-b n` — number **no** lines at all
- `-b pREGEX` — number only lines matching a regular expression

```bash
nl -b a script.py
```

Now every line gets a number, blanks included — matches `cat -n` behavior.

```bash
nl -b pERROR log.txt
```

Numbers only lines containing "ERROR" — everything else is left blank/unnumbered, letting you quickly spot how many error lines exist and where, without noise from irrelevant lines.

---

### 3. `-n FORMAT` (number format/alignment)

Controls how the number itself is formatted: `ln` (left-justified, no leading zeros), `rn` (right-justified, no leading zeros — this is the default), `rz` (right-justified, zero-padded).

```bash
nl -n ln script.py
```

Left-aligns numbers instead of the default right-alignment.

```bash
nl -n rz script.py
```

Output uses zero-padded numbers: `000001`, `000002`, etc. — useful when you want consistent-width numbering for later text processing/sorting.

---

### 4. `-w WIDTH` (number field width)

Sets how many characters wide the number column is (default is 6).

```bash
nl -w 3 script.py
```

Narrower number column — better fit for short files where 6-character-wide numbers look excessive.

---

### 5. `-s STRING` (separator)

Sets the string placed between the line number and the actual text content (default is a tab).

```bash
nl -s ": " script.py
```

Output:

```
     1: #!/usr/bin/env python3
     2: from pwn import *
```

---

### 6. `-i N` (increment)

Sets the step between consecutive line numbers instead of the default increment of 1.

```bash
nl -i 5 script.py
```

Output: `5, 10, 15, 20...` — useful for numbering schemes matching external references that already use a stepped scheme (e.g., matching line numbers to a compiled binary's debug symbols that reference every 5th source line, or building a custom outline/index).

---

### 7. `-v N` (starting value)

Sets the first line number instead of starting at 1.

```bash
nl -v 100 script.py
```

Numbering starts at 100 instead of 1 — useful when numbering a file that represents a continuation of another (e.g., splitting a large log into parts but keeping numbering continuous).

---

### 8. `-p` (no restart at logical page breaks)

By default, `nl` treats certain lines (page break markers) as resetting the line numbering back to the start value. `-p` disables that, keeping numbering continuous throughout the whole file regardless of any embedded page-break markers.

```bash
nl -p document.txt
```

---

### 9. `-l N` (blank line count for numbering)

Controls how many consecutive blank lines must appear before they start being numbered (when using `-b a`) — e.g., only number every _other_ blank line in a run of blanks, to avoid overly dense numbering in sparse sections.

```bash
nl -b a -l 2 script.py
```

---

### 10. `-h TYPE` / `-f TYPE` (header/footer numbering style)

`nl` supports the classic Unix "logical page" concept with header, body, and footer sections (delimited by special marker lines like `\:\:\:`, `\:\:`, `\:`), each with its own numbering style setting via `-h` and `-f` (same style codes as `-b`: `a`, `t`, `n`, `pREGEX`). Rarely relevant for typical CTF/SOC work, but occasionally seen when processing formally structured documents.

```bash
nl -h a -b t -f a report.txt
```

---

### 11. `-d STRING` (section delimiter override)

Changes the character sequence used to detect logical page section breaks from the default `\:`.

```bash
nl -d '**' formatted_doc.txt
```

---

### `nl` vs `cat -n` — when to reach for which

|Situation|Better choice|
|---|---|
|Just want quick line numbers on everything, simple case|`cat -n`|
|Want to skip numbering blank lines|`nl` (its default behavior)|
|Want to number only lines matching a pattern|`nl -b pREGEX`|
|Need custom formatting, custom start value, custom step|`nl`|

In practice, `cat -n` covers 90% of "I just want to see line numbers" needs — `nl` is the tool you reach for when you need one of its specific configurable behaviors.

---

### Practical use cases

```bash
nl -b pERROR application.log
```

Numbers only the ERROR lines in a log — a fast way to see error density/frequency and reference specific ones ("check error #7") without numbering thousands of irrelevant lines.

```bash
grep -A 5 "Segmentation fault" crash.log | nl
```

Numbers the context lines around a crash for easy reference when discussing them with a teammate or writing up a report.

```bash
nl -ba exploit.py | grep -A2 -B2 "system("
```

Numbers the whole exploit script (including blanks, for accurate line references) and shows just the context around a `system()` call — handy when documenting an exploit script in a writeup.

---


> [!tip]+
> **Relevant to your work:** `nl` is a niche but genuinely handy tool when writing CTF writeups or SOC incident reports — being able to reference "see line 47" in a shared script or log excerpt is much clearer than describing content vaguely, and `nl -b pREGEX` in particular is a nice trick for numbering just the _interesting_ lines (errors, matches) in a long file rather than drowning them in numbers for every irrelevant line too.
