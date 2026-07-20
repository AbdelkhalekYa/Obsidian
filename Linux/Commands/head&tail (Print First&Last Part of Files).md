#commands 

## `head` / `tail` — Print First/Last Part of Files

`head` prints the beginning of a file/stream; `tail` prints the end. Both default to 10 lines. Together they're essential for quickly sampling large files (logs, dumps, output) without loading the whole thing — and `tail -f` is the backbone of live log monitoring. Syntax: `head [OPTIONS] [FILE...]` / `tail [OPTIONS] [FILE...]`

---

### 1. (no flag) — basic usage

Prints the first (or last) 10 lines by default.

```bash
head access.log
tail access.log
```

---

### 2. `-n N` (number of lines)

Specifies exactly how many lines to show instead of the default 10.

```bash
head -n 20 access.log
tail -n 50 error.log
```

Shorthand without the `-n` also works: `head -20 access.log` (older but still common syntax).

---

### 3. `-n +N` (from line N onward — `head` only, but conceptually pairs with tail's use)

Instead of "first N lines," prints everything **starting from** line N to the end.

```bash
head -n +5 file.txt
```

Skips the first 4 lines, prints line 5 onward — useful for stripping a known header block.

---

### 4. `-c N` (bytes instead of lines)

Shows the first/last N _bytes_ rather than lines — critical when working with binary data, where "lines" don't make sense.

```bash
head -c 100 binary_file.bin
tail -c 50 payload.bin
```

Common in CTF work for peeking at a file's magic bytes/header or footer without a hex editor.

```bash
head -c 4 mystery_file | xxd
```

Grabs just the first 4 bytes and hex-dumps them — fast way to identify a file type by its magic number (e.g., `7f 45 4c 46` = ELF).

---

### 5. `-f` (follow — `tail` only, extremely important)

Continuously outputs new lines as they're appended to the file — the standard way to live-monitor a growing log file in real time.

```bash
tail -f /var/log/auth.log
```

Stays running, printing each new line as it arrives. `Ctrl+C` to stop.

---

### 6. `-F` (follow + retry — `tail` only)

Like `-f`, but also handles the file being rotated/recreated (common with logrotate) — reattaches to the new file automatically instead of following a now-dead file handle.

```bash
tail -F /var/log/syslog
```

**Preferred over `-f` for long-running log monitoring**, since production logs often get rotated (renamed, compressed, replaced) while you're watching.

---

### 7. `-f -n N` combo (follow, starting N lines back)

Shows the last N lines immediately, then continues following live.

```bash
tail -f -n 50 app.log
```

Gives you recent context before switching into live-monitoring mode — much more useful than starting `-f` with zero context.

---

### 8. `-q` (quiet — suppress headers with multiple files)

When given multiple files, `head`/`tail` normally print a `==> filename <==` header before each file's output — `-q` suppresses that.

```bash
tail -q -n 5 *.log
```

Just the last 5 lines of each file, concatenated, no filename headers.

---

### 9. `-v` (verbose — always show headers)

The opposite of `-q` — forces the `==> filename <==` headers even with a single file.

```bash
head -v access.log
```

---

### 10. `--pid=PID` (`tail` only, used with `-f`)

Terminates the follow operation automatically when the specified process ID dies — useful for following a log tied to a specific running process.

```bash
tail -f --pid=$(pgrep myapp) app.log
```

---

### 11. `-s N` (sleep interval, `tail -f` only)

Sets how often (in seconds) `tail -f` checks the file for new data — default is usually 1 second; adjusting can reduce overhead on very large systems or increase responsiveness.

```bash
tail -f -s 0.5 fast_growing.log
```

---

### 12. `--retry` (`tail` only)

Keeps trying to open the file even if it doesn't exist yet — useful in scripts that start `tail -f` before the target log file has been created.

```bash
tail -F --retry /var/log/newapp/app.log
```

---

### 13. Combining `head` and `tail` — grabbing a middle slice

Neither command alone extracts an arbitrary middle range, but chaining them does.

```bash
head -n 100 access.log | tail -n 20
```

Gets lines 81–100 — `head` cuts off everything after line 100, then `tail` keeps only the last 20 of what remains.

---

### 14. Multiple files at once

```bash
head -n 5 *.log
```

Shows the first 5 lines of every `.log` file in the directory, each labeled with a `==>` header automatically.

---

### `head`/`tail` vs `less`

|Situation|Better tool|
|---|---|
|Just need a quick peek at start/end|`head` / `tail`|
|Need to scroll/search interactively|`less`|
|Need live-updating view **with** search/scrollback|`less +F` (covered earlier)|
|Need live-updating view, simplest option|`tail -f`|

---

### Practical SOC use cases

```bash
tail -f -n 100 /var/log/auth.log
```

Standard live-monitoring startup — recent context plus ongoing stream, the first command run in many terminal-based triage sessions.

```bash
tail -F /var/log/nginx/access.log | grep --line-buffered "POST /admin"
```

Live-monitors for a specific suspicious pattern as it happens (`--line-buffered` on `grep` ensures output isn't buffered/delayed when piped from a live stream).

```bash
head -n 1 access.log; tail -n 1 access.log
```

Quick way to see the time range covered by a log file (first and last timestamped entries) without opening the whole thing.

---

### Practical CTF/RE use cases

```bash
head -c 16 unknown_file | xxd
```

Checks magic bytes to identify file type before deeper analysis — e.g., confirming it's actually an ELF, a PNG, a ZIP, etc., despite a misleading extension.

```bash
tail -c +65 firmware.bin > stripped.bin
```

Strips the first 64 bytes (a header) from a binary file, keeping everything from byte 65 onward — common when extracting embedded payloads or removing a known header structure.

```bash
strings ./binary | head -50
```

Quick sample of the first 50 extracted strings — a fast initial recon step before deciding whether to dig deeper.

---


> [!tip]+
> **Relevant to your work:** `tail -F` (capital F) is what you actually want for real SOC log-monitoring sessions rather than lowercase `-f`, since production logs get rotated and `-F` survives that transparently — a subtle but important distinction that trips people up. In your PWN/RE work, `head -c N | xxd` is a fast, lightweight way to sanity-check a file's magic bytes or header structure before committing to full analysis in Ghidra — genuinely useful during Week 3–4 of your roadmap when you're getting comfortable with ELF structure and binary recon.
