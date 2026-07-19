#commands 

## `whatis` — Display One-line Manual Page Descriptions

`whatis` prints a single-line description for a command, taken directly from the "NAME" section of its man page — an exact-name lookup, not a keyword search. It's the fastest way to get a quick "what does this even do?" answer. Syntax: `whatis [OPTIONS] NAME...`

Same honesty note as `help` and `apropos`: `whatis` is a small, focused tool without 10+ genuinely distinct flags. Here's the complete real set, explained thoroughly.

---

### 1. (no flag) — basic usage

Prints the one-line description for an exact name match.

```bash
whatis grep
```

Output: `grep (1) - print lines that match patterns`

```bash
whatis strace
```

Output: `strace (1) - trace system calls and signals`

---

### 2. Multiple names at once

You can query several commands in a single call — much faster than running `whatis` repeatedly.

```bash
whatis gdb objdump readelf nm strings
```

Output:

```
gdb (1)      - The GNU Debugger
objdump (1)  - display information from object files
readelf (1)  - display information about ELF files
nm (1)       - list symbols from object files
strings (1)  - print the strings of printable characters in files
```

Great for a quick "what do all these tools do" sanity check when setting up your CTF environment.

---

### 3. `-w` (wildcard)

Interprets the name as a shell-style wildcard pattern instead of requiring an exact match.

```bash
whatis -w 'ssh*'
```

Matches every man page name starting with "ssh" — `ssh`, `sshd`, `ssh-keygen`, `ssh-copy-id`, etc.

---

### 4. `-r` (regex)

Interprets the name as a POSIX regular expression.

```bash
whatis -r '^py.*3$'
```

Matches names starting with "py" and ending in "3" — e.g., `python3`.

---

### 5. `-s` (section)

Restricts the lookup to a specific man page section — necessary when a name exists in multiple sections and you want a specific one.

```bash
whatis -s 3 printf
```

Output: `printf (3) - formatted output conversion` — the C library function specifically, not the shell command (section 1).

---

### 6. `-l` (long, don't truncate)

Prevents the description line from being cut off to terminal width.

```bash
whatis -l openssl
```

---

### 7. `-m` (alternate system source)

Looks up names in an alternate operating system's man page set, if installed.

---

### 8. `-M` (manpath override)

Searches a custom man page directory instead of the system default.

```bash
whatis -M /opt/mytool/man mytool
```

---

### 9. `-C` (config file)

Uses an alternate configuration file instead of the default.

---

### `whatis` vs `apropos` — the key distinction

This is the one thing worth really internalizing:

|Tool|Match type|Searches|
|---|---|---|
|`whatis NAME`|**Exact** name only|Man page names|
|`apropos KEYWORD`|**Partial**/keyword|Names **and** descriptions|

```bash
whatis director        # → nothing found (no exact command called "director")
apropos director        # → mkdir, rmdir, etc. (partial match within descriptions/names)
```

If `whatis` returns "nothing appropriate," it doesn't mean the tool doesn't exist — it might just mean you don't have the exact name right. Switch to `apropos` in that case.

---

### Why results might be empty

Like `apropos`, `whatis` depends on the `mandb` index being built and up to date.

```bash
sudo mandb
```

Run this if a command you know is installed returns nothing.

---

### Practical CTF/RE use cases

```bash
whatis checksec ROPgadget one_gadget angr ghidra
```

A fast "what does each of my installed tools actually do" reference check — useful early in your roadmap (Week 1) when you're installing a whole toolchain and want quick reminders without opening full man pages for each.

```bash
whatis -s 2 ptrace
whatis -s 5 elf
```

Targeted lookups by section — confirms you're reading about the syscall (`ptrace`) or file format (`elf`) rather than an unrelated same-named page.

---


> [!tip]+
> **Relevant to your work:** In SOC triage, `whatis` is a fast, low-friction way to double check what an unfamiliar command found in a script, cron job, or shell history actually does, before deciding whether it's benign or worth flagging — much lighter weight than opening a full man page when you just need the one-line summary. Combine it with `apropos` (broad discovery) and `man` (full detail) as a natural three-step escalation: `whatis` → `apropos` → `man`.
