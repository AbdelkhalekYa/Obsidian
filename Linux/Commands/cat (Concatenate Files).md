#commands 

## `cat` — Concatenate and Display Files

`cat` (short for "concatenate") reads files sequentially and writes their content to standard output — the simplest way to view a file's contents, join multiple files together, or feed file content into a pipeline. Syntax: `cat [OPTIONS] [FILE...]`

---

### 1. (no flag) — basic usage

Prints the entire contents of a file to the terminal.

```bash
cat notes.txt
```

---

### 2. Multiple files — concatenation

Prints files one after another, back to back — the tool's original core purpose.

```bash
cat part1.txt part2.txt part3.txt
```

Outputs all three files concatenated in order, as one continuous stream.

---

### 3. `>` redirect — merge files into a new file

Not a flag of `cat` itself, but the classic pairing that gives the command its name.

```bash
cat part1.txt part2.txt > combined.txt
```

Joins two files into a single new file.

---

### 4. `-n` (number all lines)

Prefixes every line with a line number — useful when discussing specific lines of a config, log, or script with someone.

```bash
cat -n exploit.py
```

Output:

```
     1  #!/usr/bin/env python3
     2  from pwn import *
     3  
     4  elf = ELF('./binary')
```

---

### 5. `-b` (number non-blank lines only)

Same as `-n`, but skips numbering blank lines — keeps the numbering focused on actual content.

```bash
cat -b exploit.py
```

---

### 6. `-A` (show all — non-printing characters)

Displays hidden/non-printing characters: `$` at end of each line (line ending), `^I` for tabs, and other control characters shown visibly. Extremely useful for spotting invisible issues.

```bash
cat -A config.txt
```

Reveals trailing whitespace, mixed tabs/spaces, or Windows-style line endings (`^M$` = carriage return + newline) that are otherwise invisible.

---

### 7. `-E` (show line endings)

Shows just the `$` at the end of each line, without the other non-printing character markers of `-A`.

```bash
cat -E script.sh
```

Great for diagnosing files with inconsistent or unexpected line endings (e.g., a script written on Windows causing `^M` issues on Linux).

---

### 8. `-T` (show tabs)

Displays tab characters as `^I` instead of an actual tab space — helps spot tabs-vs-spaces inconsistency in code or config files.

```bash
cat -T Makefile
```

Particularly relevant for `Makefile`s, which require actual tab characters for indentation — a common source of "missing separator" errors if spaces sneak in.

---

### 9. `-s` (squeeze blank lines)

Collapses multiple consecutive blank lines down into a single blank line — cleans up output readability.

```bash
cat -s messy_log.txt
```

---

### 10. `-v` (show non-printing characters, minus tabs/newlines)

Similar to `-A` but doesn't mark tabs or line-ends specifically — shows other control/binary characters using `^` and `M-` notation. Useful for spotting stray binary/control bytes in a text file that should be plain text.

```bash
cat -v suspicious_output.txt
```

---

### 11. `--number-nonblank` (long form of `-b`)

```bash
cat --number-nonblank script.py
```

---

### 12. `--squeeze-blank` (long form of `-s`)

```bash
cat --squeeze-blank access.log
```

---

### 13. `-` (dash as filename — read from stdin)

Tells `cat` to read from standard input at that point, letting you mix piped input with real files in one command.

```bash
echo "extra line" | cat header.txt - footer.txt
```

Outputs `header.txt`, then the piped-in text, then `footer.txt` — all combined.

---

### The heredoc trick — creating files with `cat` (no flag, but essential pattern)

```bash
cat > payload.py << 'EOF'
from pwn import *
p = process('./binary')
p.interactive()
EOF
```

A very common way to quickly write a small script or config file directly from the terminal without opening an editor — widely used in CTF writeups and setup scripts.

---

### `cat` vs better tools for large files

`cat` dumps the _entire_ file at once — for large files this floods your terminal. Know when to reach for alternatives:

|Situation|Better tool|
|---|---|
|File is long, want to scroll/search|`less filename`|
|Only need the first N lines|`head -n 20 filename`|
|Only need the last N lines (e.g. live logs)|`tail -f filename`|
|Want syntax-highlighted, numbered code view|`bat filename` (if installed)|

```bash
cat huge_pcap_dump.txt   # floods your terminal, bad idea
less huge_pcap_dump.txt  # scrollable, searchable — correct choice
```

---

### Practical CTF/RE use cases

```bash
cat /proc/[pid]/maps          # view a running process's memory mappings — essential for PWN
cat flag.txt
cat /etc/passwd               # classic first check after LFI/RCE in a challenge
cat -A suspicious_script.sh   # spot hidden characters that might be evading detection
```

### Practical SOC use cases

```bash
cat /var/log/auth.log | grep "Failed password"
cat -A /etc/crontab            # check for hidden/non-printing characters in scheduled jobs — a known persistence-hiding trick
cat /proc/version              # quick kernel version check during host triage
```

---

**Relevant to your work:** `cat -A` is a genuinely underused security tool — attackers occasionally hide payloads or evade simple text-based detection using unusual whitespace, null bytes, or non-standard line endings inside scripts/configs, and `cat -A` makes those invisible tricks visible instantly. In your CTF/PWN work, `cat /proc/<pid>/maps` is a command you'll return to constantly once you get into Phase 3 (heap/ASLR work) — it's the fastest way to see a running process's actual memory layout (stack, heap, libc base, PIE base) outside of GDB.

Want the next command?