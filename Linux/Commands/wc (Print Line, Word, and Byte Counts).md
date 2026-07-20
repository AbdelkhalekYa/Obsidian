#commands 

## `wc` — Print Line, Word, and Byte Counts

`wc` ("word count") counts lines, words, characters, and bytes in a file or input stream. Simple on the surface, but it's a workhorse in scripting and log analysis for quickly quantifying data without opening it. Syntax: `wc [OPTIONS] [FILE...]`

---

### 1. (no flag) — basic usage

Prints line count, word count, and byte count, in that order, followed by the filename.

```bash
wc access.log
```

Output: `1284 9821 84213 access.log` (1284 lines, 9821 words, 84213 bytes)

---

### 2. `-l` (lines only)

Prints only the line count — by far the most commonly used flag, especially in scripts and pipelines.

```bash
wc -l access.log
```

Output: `1284 access.log`

```bash
grep "Failed password" /var/log/auth.log | wc -l
```

Instantly tells you _how many_ failed login attempts exist, without needing to read them — extremely common SOC pattern.

---

### 3. `-w` (words only)

Prints only the word count (fields separated by whitespace).

```bash
wc -w essay.txt
```

Output: `542 essay.txt`

---

### 4. `-c` (bytes only)

Prints only the byte count — useful for checking exact file size, especially binary files where "characters" and "bytes" might differ.

```bash
wc -c payload.bin
```

Output: `256 payload.bin` Confirms your shellcode/payload is exactly the size you expect — critical in PWN work where buffer sizes and offsets matter precisely.

---

### 5. `-m` (characters only)

Prints the character count — differs from `-c` (bytes) when the file contains multi-byte UTF-8 characters, since one "character" can be multiple bytes.

```bash
wc -m international_names.txt
```

On a pure-ASCII file, `-m` and `-c` give the same number; on a file with emoji or non-Latin scripts, they diverge.

---

### 6. `-L` (longest line length)

Prints the length (in characters) of the longest line in the file — useful for spotting anomalies like an unusually long line hiding an injected payload or an encoded string in a log.

```bash
wc -L access.log
```

Output: `4096 access.log` A log line that's dramatically longer than normal (e.g., 4096 chars in a log that's normally ~150) is often worth investigating — could be a long injected query string, base64 payload, or buffer-overflow attempt logged verbatim.

---

### 7. Multiple files — per-file counts plus a total

```bash
wc -l *.log
```

Output:

```
   842 access.log
   103 error.log
    56 auth.log
  1001 total
```

Automatically adds a `total` row when given multiple files — useful for a quick multi-file overview.

---

### 8. Combining flags

```bash
wc -lw report.txt
```

Output: `120 890 report.txt` Shows only lines and words (skips bytes) — pick exactly the metrics you need.

---

### 9. Reading from stdin (no filename, or `-`)

When no file is given, `wc` reads from standard input — this is how it's used in the vast majority of real pipelines.

```bash
cat access.log | wc -l
```

Or more efficiently (skip the useless `cat`):

```bash
wc -l < access.log
```

Note: this form prints just the number with no filename, since `wc` doesn't know the input came from a "file" — technically more efficient since it avoids spawning `cat`.

---

### 10. `--files0-from=FILE` (read filenames from a NUL-delimited list)

Reads a list of filenames to process from another file, NUL-separated (pairs with `find -print0`) — safely handles filenames with spaces or special characters.

```bash
find . -name "*.log" -print0 | wc -l --files0-from=-
```

---

### 11. `--total=WHEN`

Controls when the summary "total" line is printed: `auto` (default, only with 2+ files), `always`, `only` (just the total, no per-file breakdown), or `never`.

```bash
wc -l --total=only *.log
```

Output: just `1001` — the combined total, nothing else. Useful in scripts where you only want the aggregate number.

---

### The classic pattern: counting matches

This is the single most common real-world use of `wc` — combined with `grep`:

```bash
grep -c "ERROR" app.log        # grep's own -c does this without piping to wc
grep "ERROR" app.log | wc -l   # equivalent, more common habit since it composes with any command
```

Both give the same count, but `command | wc -l` is the more general pattern since it works after _any_ filtering command, not just `grep`.

---

### Practical SOC use cases

```bash
grep "Failed password" /var/log/auth.log | wc -l
```

Quick count of total failed SSH login attempts — a fast severity indicator before diving into per-IP breakdown.

```bash
awk '{print $1}' access.log | sort -u | wc -l
```

Counts the number of _unique_ visitor IPs — combined with the total line count, gives you a quick sense of traffic diversity vs. concentration (a low unique-IP count relative to total requests can indicate a single-source flood/scan).

```bash
find /home -name "*.sh" | wc -l
```

Quick inventory count — e.g., "how many shell scripts exist across all user home directories" during a host audit.

---

### Practical CTF/PWN use cases

```bash
python3 -c "print('A'*100)" | wc -c
```

Sanity-checks the exact byte length of a generated cyclic/padding pattern before sending it — confirms your offset-finding payload is precisely the size you intended.

```bash
wc -c shellcode.bin
```

Confirms shellcode size fits within a buffer's known space constraints — critical when a challenge has a strict size limit for injected shellcode.

---

**Relevant to your work:** `grep ... | wc -l` is one of the most reflexive commands you'll type in SOC triage — "how many times did this happen" is often the very first question in any investigation, and this pattern answers it instantly without needing a SIEM dashboard. In your PWN work, `wc -c` is a small but important precision check: exploit development is byte-exact, and using `wc -c` to verify payload/shellcode length before sending it catches off-by-one mistakes before they cost you a debugging session.

Want the next command?