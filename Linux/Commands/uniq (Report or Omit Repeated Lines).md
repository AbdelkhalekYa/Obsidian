#commands 

## `uniq` — Report or Omit Repeated Lines

`uniq` filters out or reports **adjacent** duplicate lines in a file or input stream. The critical thing to understand upfront: `uniq` only catches duplicates that are directly next to each other — it does _not_ scan the whole file for duplicates like a database "distinct" operation would. Syntax: `uniq [OPTIONS] [INPUT [OUTPUT]]`

---

### 1. (no flag) — basic usage

Collapses consecutive duplicate lines into a single occurrence.

```bash
uniq access.log
```

If three identical lines appear back-to-back, only one remains in the output.

---

### 2. The critical gotcha: `uniq` needs sorted input

Since it only removes _adjacent_ duplicates, non-adjacent repeats are missed entirely. This is why `uniq` is almost always paired with `sort` first.

```bash
sort access.log | uniq
```

This is by far the most common real-world usage pattern — sort groups identical lines together, then `uniq` collapses them.

---

### 3. `-c` (count)

Prefixes each output line with the number of times it occurred consecutively — extremely useful for frequency analysis.

```bash
sort access.log | uniq -c
```

Output:

```
    847 GET /login
     12 POST /api/auth
      3 GET /admin
```

This exact pattern — `sort | uniq -c` — is one of the most-used one-liners in log analysis and CTF/SOC work generally.

---

### 4. `-d` (only show duplicates)

Prints only lines that appeared **more than once**, hiding unique (singly-occurring) lines entirely.

```bash
sort access.log | uniq -d
```

Instantly shows you which log lines repeat — useful for spotting repeated failed login attempts, repeated error messages, or repeated requests (potential brute-force/scanning activity).

---

### 5. `-u` (only show unique lines)

The opposite of `-d` — prints only lines that appear **exactly once**, filtering out anything that repeats.

```bash
sort access.log | uniq -u
```

Useful for finding the "odd one out" — e.g., a single unusual request buried among thousands of routine ones.

---

### 6. `-i` (case-insensitive comparison)

Treats lines differing only in case as duplicates.

```bash
sort -f names.txt | uniq -i
```

`"ERROR"` and `"error"` are treated as the same line for deduplication purposes.

---

### 7. `-f N` (skip fields)

Ignores the first N whitespace-separated fields when comparing lines for uniqueness — useful when leading data (like a timestamp) makes otherwise-identical lines look different.

```bash
uniq -f 1 timestamped_log.txt
```

Skips the first field (e.g., a timestamp) so two lines with the same message but different times still get treated as duplicates.

---

### 8. `-s N` (skip characters)

Like `-f`, but skips a fixed number of _characters_ rather than whitespace-delimited fields before comparing.

```bash
uniq -s 20 fixed_width_log.txt
```

Handy for fixed-width log formats where the first 20 characters are a timestamp and everything after is the actual content.

---

### 9. `-w N` (compare only N characters)

The inverse of `-s` — compares only the **first** N characters of each line, ignoring everything after.

```bash
sort ip_list.txt | uniq -w 7
```

Useful for grouping by a prefix — e.g., comparing only the first 7 characters of an IP address (like `192.168`) to group by subnet, ignoring the rest.

---

### 10. `--group[=METHOD]`

Instead of collapsing duplicates, prints every line but inserts blank-line separators between each group of duplicates — lets you see the full data while still visually grouping repeats. `METHOD` can be `separate`, `prepend`, `append`, or `both`.

```bash
sort access.log | uniq --group
```

---

### 11. `-z` (zero-terminated lines)

Uses NUL (`\0`) instead of newline as the line separator — needed when piping from tools like `find -print0` that null-delimit output to safely handle filenames with spaces/newlines.

```bash
find . -print0 | sort -z | uniq -z
```

---

### 12. `-D` (show all duplicate lines, not collapsed)

Like `-d`, but prints **every** occurrence of each duplicated line (not just one representative copy) — useful when you want to see all repeats together, not a collapsed summary.

```bash
sort emails.txt | uniq -D
```

---

### 13. `--all-repeated[=METHOD]`

Long form of `-D`, with the same `separate`/`prepend`/`none` grouping method options as `--group`.

```bash
sort access.log | uniq --all-repeated=separate
```

---

### The classic combo — frequency analysis, sorted by count

```bash
sort access.log | uniq -c | sort -nr
```

This three-stage pipeline is one of the single most useful one-liners in log analysis:

1. `sort` groups identical lines together
2. `uniq -c` counts occurrences of each
3. `sort -nr` sorts by count, numerically, descending — highest frequency first

Result: an instant "top offenders" / "most common line" report.

---

### Practical SOC use cases

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -20
```

Extracts the source IP field, then shows the top 20 most frequent visitors — a fast way to spot a scanner or brute-force source hammering your server.

```bash
grep "Failed password" /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -nr
```

Counts failed SSH login attempts by source IP — classic brute-force detection one-liner.

```bash
cut -d, -f3 users.csv | sort | uniq -d
```

Finds duplicate values in a specific CSV column (e.g., duplicate email addresses or usernames) — useful for data-quality checks.

---

### Practical CTF/RE use cases

```bash
strings ./binary | sort | uniq -c | sort -nr | head
```

Shows the most repeated strings in a binary — sometimes highlights an interesting repeated pattern, key, or encoded value worth investigating.

---

> [!tip]+
> **Relevant to your SOC work:** `sort | uniq -c | sort -nr` is genuinely one of the highest-value command combos you'll use constantly in log triage — whether it's counting failed logins per IP, most-requested URLs, or most frequent error codes, this pattern turns raw log noise into a ranked frequency table in seconds without needing a SIEM query. It's worth memorizing as muscle memory rather than looking up each time.
> 


