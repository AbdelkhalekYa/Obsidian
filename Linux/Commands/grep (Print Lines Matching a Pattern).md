#commands 

## `grep` — Print Lines Matching a Pattern

`grep` ("global regular expression print") searches input for lines matching a pattern and prints them. It's arguably the single most-used tool in both SOC log analysis and CTF/RE work — mastering its flags pays off constantly. Syntax: `grep [OPTIONS] PATTERN [FILE...]`

---

### 1. (no flag) — basic usage

Prints every line containing the pattern.

```bash
grep "error" app.log
```

---

### 2. `-i` (ignore case)

Case-insensitive matching — catches `Error`, `ERROR`, `error` all at once.

```bash
grep -i "failed" auth.log
```

---

### 3. `-v` (invert match)

Prints lines that **don't** match the pattern — useful for filtering out noise to see what's left.

```bash
grep -v "200 OK" access.log
```

Shows every log line except successful 200 responses — great for quickly surfacing errors/anomalies in a huge access log.

---

### 4. `-c` (count matches)

Prints only the number of matching lines, not the lines themselves.

```bash
grep -c "Failed password" auth.log
```

Output: `47` — instant count without needing `wc -l`.

---

### 5. `-n` (show line numbers)

Prefixes each match with its line number in the file — essential when you need to jump to that exact spot in an editor or reference it precisely.

```bash
grep -n "TODO" exploit.py
```

Output: `42:# TODO: handle edge case for null bytes`

---

### 6. `-r` / `-R` (recursive)

Searches through all files in a directory and its subdirectories. `-R` additionally follows symbolic links (`-r` doesn't).

```bash
grep -rn "api_key" ./project/
```

Searches every file under `project/` for hardcoded API keys — a common quick secrets-scan before committing code or during a security review.

---

### 7. `-l` (files with matches only)

Prints just the **filenames** that contain at least one match, not the matching lines themselves — useful when you only care _which_ files are relevant, not the specific content yet.

```bash
grep -rl "password" /etc/
```

Lists every file under `/etc/` containing the word "password" — a fast first pass before drilling into specific files.

---

### 8. `-L` (files without matches)

The inverse of `-l` — lists files that **don't** contain the pattern at all.

```bash
grep -rL "Copyright" ./src/
```

Finds source files missing a required copyright header.

---

### 9. `-w` (whole word match)

Matches the pattern only as a complete word, not as a substring inside a longer word.

```bash
grep -w "root" /etc/passwd
```

Matches `root` as a standalone word, but won't match `rootkit` or `chroot` — prevents false positives from substring matches.

---

### 10. `-o` (only matching part)

Prints only the exact matched text, not the whole line it appeared on — hugely useful when extracting specific data (IPs, emails, hashes) rather than full log lines.

```bash
grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}" access.log
```

Extracts just the IP addresses from every line, one per output line — a classic SOC data-extraction one-liner (often piped into `sort | uniq -c` afterward).

---

### 11. `-E` (extended regex)

Enables extended regular expressions (ERE) — lets you use `+`, `?`, `|`, `{}`, `()` without escaping them with backslashes. Equivalent to using `egrep`.

```bash
grep -E "error|warning|critical" app.log
```

Matches any line containing "error" OR "warning" OR "critical" — without `-E`, you'd need `grep "error\|warning\|critical"` (uglier, basic regex syntax).

---

### 12. `-A N` / `-B N` / `-C N` (context lines)

Shows N lines **A**fter, **B**efore, or **C**ontext (both before and after) each match — critical for understanding what happened around an event, not just the matching line itself.

```bash
grep -A 5 "Segmentation fault" crash.log
```

Shows the crash line plus the 5 lines that follow it — often reveals the stack trace or relevant state.

```bash
grep -B 3 -A 3 "authentication failure" auth.log
```

Shows 3 lines before and after each match — full picture of what led up to and followed a failed auth event.

---

### 13. `-e` (multiple patterns)

Specifies multiple patterns to search for, each with its own `-e` — matches lines containing _any_ of them (OR logic).

```bash
grep -e "ERROR" -e "CRITICAL" -e "FATAL" app.log
```

---

### 14. `-f FILE` (patterns from a file)

Reads patterns to search for from a file, one pattern per line — useful when you have a large list of IOCs (indicators of compromise), IPs, or keywords to check against a log.

```bash
grep -f known_bad_ips.txt access.log
```

Checks the access log against every IP in a threat-intel list, one search, no loop needed.

---

### 15. `-x` (whole line match)

Requires the entire line to match the pattern exactly, not just a portion of it.

```bash
grep -x "200" status_codes.txt
```

---

### 16. `--color=auto` (highlight matches)

Highlights the matching portion of each line in color — often enabled by default via alias (`alias grep='grep --color=auto'`), but worth knowing explicitly.

```bash
grep --color=auto -i "warning" app.log
```

---

### 17. `-z` (NUL-separated / treat input as one blob)

Treats the entire input as one giant "line" separated by NUL bytes instead of newlines — lets patterns match **across** multiple lines, which normal `grep` can't do (grep is fundamentally line-based).

```bash
grep -Pzo "BEGIN.*?END"s malware_sample.txt
```

---

### 18. `-P` (Perl-compatible regex)

Enables PCRE syntax — more powerful regex features than basic/extended (`\d`, `\s`, lookaheads, non-greedy `.*?`, etc.). Not available on all `grep` builds (notably some BSD/macOS versions), but standard on Linux.

```bash
grep -P "\d{3}-\d{2}-\d{4}" records.txt
```

Matches SSN-pattern-like sequences (`\d` shorthand for digits isn't available in basic/extended regex without `-P`).

---

### 19. `-q` (quiet — exit status only)

Suppresses all output; only sets the exit code (0 = found, 1 = not found) — used for conditional logic in scripts, not for viewing results.

```bash
if grep -q "root:" /etc/passwd; then
  echo "root account exists"
fi
```

---

### 20. `--include` / `--exclude` (filter by filename pattern, with `-r`)

Restricts recursive search to only certain filenames, or excludes certain ones.

```bash
grep -r --include="*.py" "eval(" ./project/
```

Searches only `.py` files for a potentially dangerous `eval(` call — much faster and more targeted than scanning every file type.

```bash
grep -r --exclude="*.log" "password" ./app/
```

Searches everything except log files.

---

### Combining flags — real-world power patterns

```bash
grep -rniE "password|secret|api_key" --include="*.py" --include="*.js" ./repo/
```

Recursive, case-insensitive, extended regex, line numbers, restricted to Python/JS files — a solid quick secrets-scan.

```bash
grep -oP "(?<=user=)[a-zA-Z0-9]+" access.log
```

Perl regex with a lookbehind — extracts just the value after `user=` in each line without matching "user=" itself.

---

### Practical SOC use cases

```bash
grep -c "Failed password" /var/log/auth.log
grep -B2 -A5 "CRITICAL" application.log
grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}" access.log | sort | uniq -c | sort -nr
grep -rl "eval(base64_decode" /var/www/          # webshell/backdoor hunting in PHP files
```

### Practical CTF/PWN use cases

```bash
strings ./binary | grep -i "flag"
grep -a "flag{" memory_dump.bin                   # -a treats binary file as text, forces search
objdump -d ./binary | grep -A 10 "<main>:"        # jump straight to main's disassembly
checksec --file=./binary | grep -i "canary"
```

---


> [!tip]+
> **Relevant to your work:** `grep` is genuinely the tool you'll use more than any other single command across both your SOC and PWN work — the combination of `-r` (recursive), `-i` (case-insensitive), `-n` (line numbers), and `-E` or `-P` (regex power) covers the vast majority of real tasks: hunting for secrets in code, extracting IOCs from logs, or finding specific functions/strings in a binary. The `-A`/`-B`/`-C` context flags in particular are underused by beginners but are what turns a single matching line into an actual understanding of _what happened around it_ — critical for both incident timelines and crash analysis.
