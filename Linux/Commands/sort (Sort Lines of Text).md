#commands 

## `sort` — Sort Lines of Text

`sort` reorders the lines of a file or input stream — alphabetically, numerically, by specific fields, in reverse, and more. It's one of the most-used building blocks in shell pipelines, especially paired with `uniq` (as we just saw). Syntax: `sort [OPTIONS] [FILE...]`

---

### 1. (no flag) — basic usage

Sorts lines alphabetically (technically, by byte value / locale collation order).

```bash
sort names.txt
```

---

### 2. `-n` (numeric sort)

Sorts by numeric value instead of lexicographic (string) order — critical distinction, since string sort puts `"10"` before `"9"` (compares character by character), while `-n` correctly puts `9` before `10`.

```bash
sort -n port_list.txt
```

Without `-n`: `1, 10, 100, 2, 20, 3` (string order) With `-n`: `1, 2, 3, 10, 20, 100` (correct numeric order)

---

### 3. `-r` (reverse)

Sorts in descending order instead of ascending — combine with other flags freely.

```bash
sort -nr scores.txt
```

Numeric sort, highest value first.

---

### 4. `-u` (unique)

Removes duplicate lines as part of sorting — a shortcut that saves piping into `uniq` separately when you don't need `uniq`'s extra options (counting, etc.).

```bash
sort -u ip_addresses.txt
```

Equivalent to `sort ip_addresses.txt | uniq` but in a single command.

---

### 5. `-k N` (sort by a specific field/column)

Sorts based on the Nth whitespace-delimited field instead of the whole line. Extremely useful for structured data like logs or CSVs.

```bash
sort -k 3 access.log
```

Sorts by the 3rd space-separated field (e.g., often the timestamp or status code, depending on log format).

You can also specify a field _range_ and add modifiers:

```bash
sort -k 2,2n data.txt
```

Sorts numerically (`n`) using only field 2 (the `2,2` range means "start and end at field 2" — otherwise sort would use field 2 through end-of-line as the sort key).

---

### 6. `-t DELIM` (specify field separator)

Changes the field delimiter from the default (whitespace) to a custom character — essential for CSV or colon-separated data.

```bash
sort -t, -k2,2n data.csv
```

Sorts a CSV by the 2nd column, numerically, using comma as the delimiter.

```bash
sort -t: -k3,3n /etc/passwd
```

Sorts `/etc/passwd` by UID (3rd colon-separated field) — a genuinely useful SOC/sysadmin check to see users ordered by UID and spot anything unexpected (e.g., a new UID 0 account planted for persistence).

---

### 7. `-f` (fold case / case-insensitive)

Treats uppercase and lowercase as equivalent when sorting.

```bash
sort -f mixed_case_names.txt
```

`"Apple"`, `"banana"`, `"Cherry"` sort correctly by letter regardless of case, instead of all-uppercase sorting before all-lowercase (ASCII default behavior).

---

### 8. `-h` (human-numeric sort)

Understands human-readable sizes with suffixes (`K`, `M`, `G`) and sorts them by actual magnitude — pairs perfectly with `du -h` or `ls -lh` output.

```bash
du -h /var/log/* | sort -h
```

Correctly sorts `1.2K`, `500M`, `2G` in true size order — plain `sort` or even `sort -n` would get this wrong since it can't interpret the suffix.

---

### 9. `-M` (month sort)

Sorts abbreviated month names (`Jan`, `Feb`, `Mar`...) in calendar order rather than alphabetical order.

```bash
sort -M log_by_month.txt
```

Without `-M`: `Apr, Aug, Dec, Feb...` (alphabetical, wrong) With `-M`: `Jan, Feb, Mar, Apr...` (correct chronological order)

---

### 10. `-R` (random sort / shuffle)

Randomizes line order instead of sorting — useful for sampling or randomizing test data, wordlists, etc.

```bash
sort -R wordlist.txt | head -20
```

Grabs 20 random lines from a wordlist — handy for quick sampling during testing.

---

### 11. `-c` (check if already sorted)

Doesn't sort — just verifies whether the input is already sorted, and reports an error (with the first out-of-order line) if not. Useful in scripts as a validation step before an operation that requires sorted input (like `uniq` or `join`).

```bash
sort -c access.log
```

Exit code 0 if sorted, non-zero with an error message pointing to the first disordered line if not.

---

### 12. `-b` (ignore leading whitespace)

Ignores leading blanks when determining sort keys — prevents inconsistent indentation from throwing off the sort order.

```bash
sort -b indented_data.txt
```

---

### 13. `-s` (stable sort)

Preserves the original relative order of lines that compare as equal (instead of arbitrarily reordering ties) — important when doing a multi-pass sort or when tie-breaking order matters for reproducibility.

```bash
sort -s -k1,1 data.txt
```

---

### 14. `-o FILE` (output to file)

Writes sorted output directly to a file — safer than `sort file > file` (which can corrupt the file, since the shell truncates it before `sort` finishes reading).

```bash
sort access.log -o access_sorted.log
```

This is the **correct** way to sort a file in place — `sort file.txt > file.txt` is a classic beginner mistake that empties the file.

---

### 15. `-z` (zero-terminated)

Uses NUL as the line separator instead of newline — pairs with `find -print0` for safely handling filenames containing spaces or newlines.

```bash
find . -print0 | sort -z
```

---

### Combining flags — real-world patterns

```bash
sort -t, -k3,3nr sales.csv | head -10
```

Top 10 rows of a CSV sorted by the 3rd column, numerically, descending — a very typical "top N" data analysis one-liner.

```bash
sort -k5,5n -k1,1 access.log
```

Multi-key sort: primary sort by field 5 (numeric), then field 1 as a tiebreaker for equal values.

---

### Practical SOC use cases

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -20
```

(The same pipeline from the `uniq` lesson — `sort` does the heavy lifting twice here: once to group IPs for counting, once to rank by frequency.)

```bash
sort -t: -k3,3n /etc/passwd | awk -F: '{print $1, $3}'
```

Lists all users sorted by UID — quick way to spot anomalies like duplicate UID 0 accounts (a privilege-escalation persistence trick).

```bash
sort -k1,1 -k2,2n firewall_events.log
```

Sort security events first by source, then chronologically within each source — useful for building a per-host timeline during investigation.

---

### Practical CTF/RE use cases

```bash
strings ./binary | sort -u
```

Deduplicates extracted strings for cleaner review.

```bash
nm ./binary | sort -k1
```

Sorts symbol table entries by address — makes it easier to scan through functions/symbols in memory order.

---


> [!tip]+
> **Relevant to your SOC work:** `sort -t: -k3,3n /etc/passwd` is a genuinely useful quick host-integrity check — UID 0 should only belong to `root`; seeing any other UID-0 entry sorted to the top is a red flag for a backdoor account. More broadly, mastering `sort -k` with custom delimiters (`-t`) is what lets you meaningfully sort structured log data (CSV exports, `/etc/passwd`, firewall logs) by the _field that matters_ rather than just alphabetically by the whole line — a skill you'll lean on constantly moving from ad-hoc `grep`ing into more structured log analysis.


Want the next command?