#commands 

## `touch` — Create Empty Files and Update Timestamps

`touch` creates a new empty file if it doesn't exist, or updates the access/modification timestamps of an existing file if it does — without changing its content. Simple tool, but genuinely important in both scripting and forensics contexts, since timestamps carry real meaning. Syntax: `touch [OPTIONS] FILE...`

---

### 1. (no flag) — basic usage

Creates the file if it doesn't exist (empty, 0 bytes); if it already exists, updates its access and modification time to "now" without touching its content.

```bash
touch notes.txt
```

---

### 2. Multiple files at once

```bash
touch file1.txt file2.txt file3.txt
```

Creates (or timestamps-updates) all three in one call — common in scripting when you need to guarantee a set of files exists before something else writes to them.

---

### 3. `-a` (access time only)

Updates only the **access time** (atime) — the timestamp for when the file was last read — leaving the modification time unchanged.

```bash
touch -a config.yaml
```

---

### 4. `-m` (modification time only)

Updates only the **modification time** (mtime) — when the file's _content_ was last changed — leaving access time unchanged.

```bash
touch -m script.py
```

---

### 5. `-c` / `--no-create` (don't create)

If the file doesn't already exist, does nothing (no error, no file created) — only updates timestamps on files that already exist.

```bash
touch -c maybe_exists.log
```

Useful in scripts where you want to "refresh" a file's timestamp _if_ it's there, but don't want to accidentally create stray empty files if it isn't.

---

### 6. `-d STRING` (set a specific date/time)

Sets the timestamp to an arbitrary date/time you specify, rather than "now" — accepts a wide range of human-readable date formats.

```bash
touch -d "2025-01-15 10:30:00" report.txt
```

```bash
touch -d "yesterday" backup.tar
touch -d "3 days ago" old_file.txt
touch -d "next Monday" deadline.txt
```

---

### 7. `-t STAMP` (set timestamp, compact format)

Sets the timestamp using a fixed compact format: `[[CC]YY]MMDDhhmm[.ss]`.

```bash
touch -t 202501151030 report.txt
```

Sets the date to January 15, 2025, 10:30 AM. Slightly less readable than `-d`, but useful in scripts wanting a strict, unambiguous format.

---

### 8. `-r FILE` (reference — copy another file's timestamp)

Copies the timestamp from an existing reference file instead of specifying a date manually — useful for matching timestamps between related files.

```bash
touch -r original.txt copy.txt
```

Sets `copy.txt`'s timestamps to exactly match `original.txt`'s.

---

### 9. `--date=STRING` (long form of `-d`)

```bash
touch --date="2026-07-20 09:00:00" evidence_placeholder.txt
```

---

### 10. `--time=WORD` (which timestamp to change)

Controls which timestamp(s) get updated when combined with `-a`/`-m` behavior: `access`/`atime`/`use` (same as `-a`), or `modify`/`mtime` (same as `-m`).

```bash
touch --time=access somefile.txt
```

---

### 11. `-h` / `--no-dereference` (act on symlink itself)

When the target is a symbolic link, updates the **symlink's own** timestamp rather than following it and updating the timestamp of the file it points to.

```bash
touch -h my_symlink
```

---

### 12. `--no-dereference` combined with `-r`

```bash
touch --no-dereference -r reference_link target_link
```

Copies timestamp from one symlink to another without following either to their targets.

---

### The three Linux timestamps — essential background

Every file has three separate timestamps, and `touch` interacts mainly with the first two:

|Timestamp|Meaning|Changed by|
|---|---|---|
|**atime** (access)|Last time content was _read_|Opening/reading the file|
|**mtime** (modify)|Last time content was _changed_|Writing/editing the file|
|**ctime** (change)|Last time metadata changed (permissions, ownership, **or** content)|chmod, chown, mv (on some filesystems), content edits|

**Critical point: `touch` cannot directly set ctime.** ctime is automatically updated by the kernel whenever _anything_ about the file changes — including running `touch` itself. This is forensically significant: you can use `touch -d` to make mtime/atime say whatever you want, but ctime will still reflect the real moment the touch command ran, which is exactly why forensic investigators check ctime as a harder-to-fake reference point.

```bash
stat somefile.txt
```

Shows all three timestamps clearly labeled — the tool to actually inspect them (touch only sets, doesn't display).

---

### Practical scripting use cases

```bash
touch -c pidfile.lock
```

"Refresh if exists, don't create if missing" — common lockfile/heartbeat pattern in scripts.

```bash
for f in *.txt; do touch -d "2026-01-01" "$f"; done
```

Normalizes timestamps across a batch of files — sometimes needed before archiving/comparing file sets where original timestamps don't matter but consistency does.

```bash
mkdir -p project/{src,tests,docs} && touch project/{README.md,.gitignore}
```

Classic project-scaffolding pattern — creates a directory structure and placeholder files in one line.

---

### Practical CTF/RE and SOC use cases

```bash
touch -r legit_system_file.so suspicious_dropped_file.so
```

**This is exactly what attackers do** — "timestomping" — using `touch -r` (or `-d`) to make a malicious file's timestamps match a legitimate system file's, to blend in and evade timeline-based detection. Understanding this technique is directly relevant to your SOC work: it's why investigators don't trust mtime/atime alone during timeline reconstruction.

```bash
stat suspicious_file.so
```

The corresponding **defensive** check — comparing ctime against mtime/atime. If mtime/atime were forged to look old but ctime shows a very recent change, that mismatch itself is a strong indicator of timestomping.

```bash
find / -newer /etc/passwd -type f 2>/dev/null
```

Finds files modified more recently than a known reference point — a common IR technique for narrowing down what changed during a suspected compromise window (not `touch` itself, but directly related to the timestamp concepts `touch` manipulates).

---


> [!tip]+
> **Relevant to your SOC work:** This is genuinely one of the more important commands to understand _defensively_, not just for its own syntax — "timestomping" (forging mtime/atime with tools like `touch -r`/`-d` to make malware look old or match a legitimate file) is a real, common anti-forensics technique. Knowing that `touch` **cannot** forge ctime is the key defensive insight: whenever you're investigating a suspicious file, running `stat` and checking for a mismatch between ctime and the other timestamps is a fast, reliable way to catch this specific evasion attempt.
