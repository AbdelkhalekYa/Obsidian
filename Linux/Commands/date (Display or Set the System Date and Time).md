#commands 

## `date` — Display or Set the System Date and Time

`date` displays (or, with root privileges, sets) the current system date and time, and can format that output in countless ways — essential for timestamping logs, scripts, filenames, and reports, and directly relevant to timeline work in SOC investigations. Syntax: `date [OPTIONS] [+FORMAT]`

---

### 1. (no flag) — basic usage

Prints the current date and time in the system's default format.

```bash
date
```

Output: `Mon Jul 20 09:14:03 EEST 2026`

---

### 2. `+FORMAT` (custom output format)

The real power of `date` — a format string starting with `+`, using `%` specifiers to control exactly what's shown and how. This is used constantly in scripting.

```bash
date +"%Y-%m-%d"
```

Output: `2026-07-20`

Common format specifiers:

|Specifier|Meaning|Example|
|---|---|---|
|`%Y`|4-digit year|`2026`|
|`%m`|Month (01–12)|`07`|
|`%d`|Day of month|`20`|
|`%H`|Hour, 24h format|`09`|
|`%M`|Minute|`14`|
|`%S`|Second|`03`|
|`%A`|Full weekday name|`Monday`|
|`%a`|Abbreviated weekday|`Mon`|
|`%B`|Full month name|`July`|
|`%Z`|Timezone name|`EEST`|
|`%s`|Unix epoch timestamp|`1721460843`|

```bash
date +"%Y-%m-%d %H:%M:%S"
```

Output: `2026-07-20 09:14:03` — the classic sortable, unambiguous log timestamp format, and genuinely worth memorizing since you'll type it constantly.

---

### 3. `%s` (Unix epoch timestamp)

Prints the current time as seconds since Jan 1, 1970 — the universal format for precise time math and cross-referencing with tools that use epoch time internally (like `stat`'s `%Y`/`%Z`/`%X` output, covered in the last lesson).

```bash
date +%s
```

Output: `1721460843`

```bash
stat -c "%Y" file.txt
date -d @1721413391
```

Converts a raw epoch timestamp from `stat` output back into a human-readable date — directly useful when cross-referencing forensic timestamps.

---

### 4. `-d STRING` (display a specific date, not "now")

Parses and displays an arbitrary date/time instead of the current moment — accepts very flexible natural-language input.

```bash
date -d "2026-01-15"
date -d "yesterday"
date -d "3 days ago"
date -d "next Monday"
date -d "2026-07-20 09:00:00 +2 hours"
```

Same flexible parsing as `touch -d`, covered earlier — `date -d` and `touch -d` share the same underlying date-parsing engine.

---

### 5. `-d @EPOCH` (convert epoch to human-readable)

Converts a raw Unix timestamp into a readable date — the exact reverse operation of `date +%s`.

```bash
date -d @1721460843
```

Output: `Mon Jul 20 09:14:03 EEST 2026`

Genuinely essential when reading raw epoch timestamps out of logs, `stat` output, or binary data during investigation.

---

### 6. `-u` (UTC / Coordinated Universal Time)

Displays the time in UTC instead of the local system timezone — critical when correlating logs/events across systems in different timezones, or when a source (e.g., cloud provider logs, many SIEM tools) reports everything in UTC.

```bash
date -u
```

Output: `Mon Jul 20 06:14:03 UTC 2026`

```bash
date -u +"%Y-%m-%dT%H:%M:%SZ"
```

Output: `2026-07-20T06:14:03Z` — this exact format is **ISO 8601**, the standard format used in most structured logs, APIs, and SIEM timestamps. Worth memorizing as your default "correlate this across systems" format.

---

### 7. `-R` (RFC 2822 format)

Outputs in the standard format used in email headers and some log formats.

```bash
date -R
```

Output: `Mon, 20 Jul 2026 09:14:03 +0300`

---

### 8. `-I[TIMESPEC]` (ISO 8601 shortcut)

Quick shortcut for ISO 8601 formatting without manually building the `+FORMAT` string — `TIMESPEC` can be `date`, `hours`, `minutes`, or `seconds` for varying precision.

```bash
date -I
```

Output: `2026-07-20`

```bash
date -Iseconds
```

Output: `2026-07-20T09:14:03+03:00`

---

### 9. `-r FILE` (show a file's modification time)

Displays the modification timestamp of a file, formatted like any other `date` output — essentially a shortcut alternative to `stat -c %y file`.

```bash
date -r suspicious_file.log
```

Output: `Mon Jul 20 08:47:12 EEST 2026`

```bash
date -r suspicious_file.log +"%Y-%m-%d %H:%M:%S"
```

Custom-formatted mtime — quick one-liner alternative to full `stat` output when you only need the modification time specifically.

---

### 10. `-s STRING` (set the system date/time — requires root)

Sets the actual system clock — a genuinely dangerous flag if used carelessly, since a wrong system time can break TLS certificate validation, authentication tokens (time-based OTP), log correlation, and scheduled tasks.

```bash
sudo date -s "2026-07-20 09:30:00"
```

Rarely needed manually on modern systems (NTP handles clock sync automatically), but relevant to know it exists — and that an unexpectedly wrong system clock is itself sometimes a sign of tampering (some malware manipulates system time to interfere with certificate validation or time-based security controls).

---

### 11. `--rfc-3339=TIMESPEC`

Similar to `-I`, but RFC 3339 format (very close to ISO 8601, slightly different separator conventions) — `TIMESPEC` is `date`, `seconds`, or `ns` (nanoseconds).

```bash
date --rfc-3339=seconds
```

Output: `2026-07-20 09:14:03+03:00`

---

### 12. `-f DATEFILE` (batch process multiple dates from a file)

Reads multiple date strings from a file, one per line, and processes/formats each — useful for batch-converting a list of timestamps.

```bash
date -f epoch_list.txt +"%Y-%m-%d %H:%M:%S"
```

---

### Practical SOC use cases

```bash
date -u +"%Y-%m-%dT%H:%M:%SZ"
```

Generates a UTC ISO 8601 timestamp for consistent incident report/log entry timestamping — the standard practice when documenting findings that might be cross-referenced against systems in other timezones.

```bash
date -d @$(stat -c %Z suspicious_file) 
```

Converts a raw ctime epoch value (from `stat`, covered last lesson) into a human-readable date — directly closes the loop on that earlier discussion of comparing atime/mtime/ctime for timestomping detection.

```bash
echo "$(date '+%Y-%m-%d %H:%M:%S') - Investigated auth.log for failed logins" >> investigation_notes.txt
```

Timestamped note-logging pattern — good habit for building a defensible investigation timeline as you work.

```bash
find / -newermt "2026-07-19 00:00:00" -type f 2>/dev/null
```

`find`'s `-newermt` accepts a `date`-style string directly — finds all files modified after a specific point in time, using the same flexible date parsing `date -d` uses.

---

### Practical CTF/PWN use cases

```bash
date +%s
```

Quick way to generate a unique numeric value for test filenames, temp directories, or log naming during exploit development (`mkdir /tmp/test_$(date +%s)`).

```bash
date -d @$(python3 -c "print(1721460843)")
```

Converting a Unix timestamp found embedded in a binary or challenge output into something human-readable — occasionally comes up in forensics/misc CTF challenges involving timestamps.

---


> [!tip]+
> **Relevant to your SOC work:** `date -u +"%Y-%m-%dT%H:%M:%SZ"` (UTC, ISO 8601) is worth adopting as your default timestamp format for any investigation notes or reports — it's unambiguous, sorts correctly as plain text, and matches what most logging systems and SIEMs already use internally, which makes cross-referencing across different log sources far less error-prone than mixing local-timezone, 12-hour, or locale-dependent formats. Combined with `date -d @EPOCH` for converting raw timestamps from `stat` or log data, and `-r FILE` as a quick single-timestamp alternative to full `stat` output, you've got the core toolkit for timeline reconstruction covered.
