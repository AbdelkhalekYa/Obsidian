#concepts #commands 

## Wildcards in Searching (Shell Globbing)

Wildcards (also called **glob patterns**) are special characters the shell expands into matching filenames _before_ running a command — this expansion happens in the shell itself, not inside the command being run. This is a genuinely important distinction from regex (used by `grep`, `find -regex`, etc.), which is a completely different, more powerful pattern language interpreted by the _tool_, not the shell. Confusing the two is one of the most common beginner mistakes in Linux.

---

### 1. `*` (asterisk — match any number of characters, including none)

Matches zero or more of _any_ character.

```bash
ls *.log
```

Matches `access.log`, `error.log`, `.log` (if it existed) — any filename ending in `.log`.

```bash
rm temp*
```

Deletes `temp`, `temp1`, `temp_backup`, `temporary_file.txt` — anything starting with "temp".

```bash
cat *.txt
```

Concatenates every `.txt` file in the current directory.

---

### 2. `?` (question mark — match exactly one character)

Matches any single character, no more, no less.

```bash
ls file?.txt
```

Matches `file1.txt`, `fileA.txt`, `file_.txt` — but **not** `file10.txt` (two characters) or `file.txt` (zero characters).

```bash
ls log_202607??.txt
```

Matches `log_20260701.txt` through `log_20260731.txt` — any two-digit day.

---

### 3. `[]` (square brackets — match any one character from a set)

Matches exactly one character, but only from the specific set/range listed inside the brackets.

```bash
ls file[123].txt
```

Matches `file1.txt`, `file2.txt`, `file3.txt` — but not `file4.txt` or `file12.txt`.

```bash
ls log_2026070[1-9].txt
```

Matches days 01 through 09 for July 2026 — ranges (`1-9`, `a-z`, `A-Z`) are supported inside brackets.

```bash
ls [Rr]eadme*
```

Matches both `readme.md` and `Readme.md` — a common trick for case-flexible matching without needing a case-insensitive flag.

---

### 4. `[!...]` or `[^...]` (negated character class)

Matches any single character **NOT** in the given set.

```bash
ls file[!0-9].txt
```

Matches `fileA.txt`, `file_.txt` — anything where that position is _not_ a digit.

```bash
rm log[!.]*
```

Removes files starting with "log" where the next character isn't a literal dot (careful — behavior can vary slightly, always test with `ls` first before `rm`).

---

### 5. `{}` (brace expansion — NOT technically a glob, but works alongside it)

Generates every combination listed, comma-separated — this happens purely as text generation, even for files/paths that don't yet exist (unlike `*`/`?`/`[]`, which only match _existing_ files).

```bash
touch file{1,2,3}.txt
```

Creates `file1.txt`, `file2.txt`, `file3.txt` in one command.

```bash
cp config.yaml{,.bak}
```

A very common backup shortcut — expands to `cp config.yaml config.yaml.bak`.

```bash
mkdir -p project/{src,tests,docs}
```

Creates three subdirectories in one command (covered back in the `touch` lesson).

---

### 6. `{a..z}` / `{1..10}` (brace range expansion)

Generates a sequence automatically, without listing every value manually.

```bash
touch report_{01..12}.txt
```

Creates `report_01.txt` through `report_12.txt`.

```bash
for i in {1..5}; do echo "Attempt $i"; done
```

Common in loops — generates a numeric sequence for iteration, useful in exploit-retry scripts or scan automation.

---

### 7. `**` (globstar — recursive matching, requires `shopt -s globstar` in bash)

By default, `*` does **not** descend into subdirectories. Enabling `globstar` lets `**` match across any depth of nested directories.

```bash
shopt -s globstar
ls **/*.log
```

Matches `.log` files at any depth — `app/error.log`, `app/2026/07/access.log`, etc. Without `globstar` enabled, plain `*` only sees the current directory level.

---

### The critical distinction: glob (shell) vs regex (tool)

This trips up almost everyone at some point, so it's worth being explicit:

||Wildcards / Glob|Regex|
|---|---|---|
|Interpreted by|The **shell**, before the command even runs|The **command/tool itself** (`grep`, `find -regex`, `sed`)|
|`*` means|"zero or more of anything"|"zero or more of the **preceding character**"|
|`.` means|Literal dot|"any single character"|
|Used in|`ls`, `cp`, `rm`, `cd`, any command's file arguments|`grep`, `sed`, `awk`, `find -regex`, `egrep`|

```bash
ls *.txt        # glob: matches any file ending in .txt
grep ".*txt" *  # regex: .* means "any characters", matches "txt" anywhere, not just extension
```

```bash
grep "file.txt" list.txt
```

In regex, `.` matches _any_ character — so this actually also matches `fileXtxt`, `file5txt`, etc., not just a literal dot. If you want a literal dot in regex, you must escape it: `file\.txt`. Globs don't have this problem — `.` in a glob is always literal.

---

### Why quoting matters — preventing unwanted expansion

If a wildcard character needs to be passed _literally_ to a command (not expanded by the shell), wrap it in quotes.

```bash
grep "error*" logfile.txt
```

Here, since it's quoted, the shell does **not** expand `*` — it's passed straight to `grep`, which then interprets it as _its own_ regex syntax (where `*` means "zero or more of the preceding character," so `error*` actually means "erro" followed by zero-or-more "r"s — a common source of confusing behavior).

Without quotes:

```bash
grep error* logfile.txt
```

The **shell** tries to expand `error*` as a glob against filenames in the current directory _first_ — if any file happens to start with "error", that filename gets substituted in before `grep` ever runs, potentially producing wildly different (and confusing) results than intended.

**Rule of thumb: quote your regex patterns when using tools like `grep`, to make sure the shell doesn't "help" by expanding them as filenames first.**

---

### Practical SOC use cases

```bash
grep "Failed" auth.log*
```

Searches every log matching the glob (e.g., `auth.log`, `auth.log.1`, `auth.log.2.gz` if rotated) — glob picks the files, `grep` searches inside them.

```bash
find /var/www -name "*.php" -newer /etc/hostname
```

Combines a glob-style name pattern (`find`'s `-name` uses glob syntax, not regex, by default) with a time filter — classic webshell-hunting pattern.

```bash
rm /tmp/scan_*.tmp
```

Cleans up temp scan output files from an automated recon script, matching a consistent naming pattern.

```bash
cp /etc/passwd{,.bak.$(date +%s)}
```

Brace-expansion backup trick with a timestamp appended — quick, safe habit before editing any critical config.

---

### Practical CTF/PWN use cases

```bash
ls challenge_*
```

Quickly lists every file belonging to a multi-file challenge (binary, libc, source, README) sharing a common prefix.

```bash
python3 -c "print('A'*100)" > payload_[test].txt   # careful — brackets here are literal in most non-glob contexts unless matched against existing files
```

```bash
for f in *.bin; do checksec --file="$f"; done
```

Runs `checksec` against every binary in a challenge directory in one loop, using a glob to generate the file list.

---


> [!tip]+
> **Relevant to your work:** Wildcards are something you'll use reflexively hundreds of times a day without necessarily thinking of them as a distinct "feature" — but understanding the shell-expansion-vs-regex distinction specifically will save you real debugging time the first time a `grep` pattern behaves unexpectedly because the shell silently expanded part of it as a glob before `grep` even saw it. For SOC log analysis, brace expansion (`{}`) with timestamps is a genuinely good habit for quick, safe config backups before making any change to a security-critical file.
