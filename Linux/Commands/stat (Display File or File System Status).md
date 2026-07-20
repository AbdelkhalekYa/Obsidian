#commands 

## `stat` — Display File or File System Status

`stat` displays detailed metadata about a file — far more than `ls -l` shows, including all three timestamps (atime, mtime, ctime), exact permissions in multiple formats, inode number, and more. This is the tool that directly answers the timestomping/forensics questions raised back in the `touch` and `ls` lessons. Syntax: `stat [OPTIONS] FILE...`

---

### 1. (no flag) — basic usage

Prints full metadata for a file.

```bash
stat exploit.py
```

Output:

```
  File: exploit.py
  Size: 8432            Blocks: 24         IO Block: 4096   regular file
Device: 802h/2050d       Inode: 1441792     Links: 1
Access: (0755/-rwxr-xr-x)  Uid: (1000/abdelkhalek)   Gid: (1000/abdelkhalek)
Access: 2026-07-20 09:14:03.482910000 +0300
Modify: 2026-07-19 22:03:11.192021000 +0300
Change: 2026-07-19 22:03:11.192021000 +0300
 Birth: 2026-07-15 14:22:08.001100000 +0300
```

This single command shows the three critical timestamps (Access/Modify/Change) that `ls` alone can't display together — exactly what you need to spot a mismatch indicating timestomping.

---

### 2. `-f` (filesystem status, not file status)

Instead of info about the file itself, shows info about the **filesystem** the file resides on — total size, free space, filesystem type.

```bash
stat -f /var/log
```

Output includes filesystem type (ext4, xfs, etc.), block size, total/free blocks — useful for quick disk-space or filesystem-type checks during triage.

---

### 3. `-c FORMAT` (custom output format)

Prints only specific fields you choose, using format specifiers — extremely useful for scripting since it avoids parsing the full verbose output.

```bash
stat -c "%n %s %Y" exploit.py
```

Output: `exploit.py 8432 1721370191` — filename, size in bytes, modification time as Unix epoch.

Common format specifiers:

|Specifier|Meaning|
|---|---|
|`%n`|Filename|
|`%s`|Size in bytes|
|`%a`|Permissions in octal|
|`%A`|Permissions in human-readable form (`-rwxr-xr-x`)|
|`%U`|Owner username|
|`%G`|Group name|
|`%u` / `%g`|Numeric UID / GID|
|`%X`|Last access time (Unix epoch)|
|`%Y`|Last modify time (Unix epoch)|
|`%Z`|Last change time (Unix epoch)|
|`%i`|Inode number|
|`%F`|File type (e.g., "regular file", "directory")|

```bash
stat -c "%Y %X %Z %n" *.php
```

Prints mtime, atime, ctime (all as raw epoch numbers) side by side for every PHP file — perfect for scripting a timestomping-detection check across many files at once, since you can directly compare the numbers programmatically.

---

### 4. `--format=FORMAT` (long form of `-c`)

```bash
stat --format="%U %n" *.sh
```

Shows owner and filename for every shell script — quick ownership audit.

---

### 5. `--printf=FORMAT` (like `-c`, but interprets backslash escapes)

Same idea as `-c`, but processes `\n`, `\t` etc. as actual newlines/tabs, and doesn't automatically add a trailing newline after each entry (you control it explicitly) — gives finer control for building custom-formatted reports.

```bash
stat --printf="%n: %s bytes\n" *.log
```

---

### 6. `-t` (terse output)

Prints all the same info as default `stat`, but in a single compact space-separated line instead of the multi-line human-readable block — designed for easy parsing by scripts.

```bash
stat -t exploit.py
```

Output: `exploit.py 8432 24 81ed 1000 1000 802 1441792 1 0 0 1721460843 1721413391 1721413391 1721025728 4096`

---

### 7. `-L` / `--dereference` (follow symlinks)

By default, `stat` on a symlink shows info about the **symlink itself**; `-L` follows it and shows info about the actual target file instead.

```bash
stat -L my_symlink
```

---

### Without `-L` — inspecting the symlink itself (the default)

```bash
stat my_symlink
```

Output shows `File: my_symlink -> /real/target/path` and reports the symlink's own (usually tiny) size and its own timestamps — useful for the reverse question: when was the _link itself_ created or changed, separate from its target.

---

### The three timestamps side by side — direct payoff from earlier lessons

This is where `stat` ties together everything from the `touch` and `ls` discussions:

```bash
stat suspicious_file.so
```

```
Access: 2026-07-20 09:00:00.000000000 +0300   (atime — read)
Modify: 2020-01-15 10:00:00.000000000 +0300   (mtime — content changed, possibly FORGED)
Change: 2026-07-20 08:47:12.384921000 +0300   (ctime — metadata changed, CANNOT be forged by touch)
```

If mtime claims the file is 6 years old but ctime shows it was actually touched minutes ago, that gap is your timestomping evidence — exactly the check flagged as important in the `touch` lesson, and `stat` is the tool that actually shows you all three values together to make the comparison.

---

### Practical SOC/forensics use cases

```bash
stat /etc/passwd /etc/shadow /etc/sudoers
```

Quick integrity spot-check on critical system files — unexpected recent ctime on any of these warrants investigation.

```bash
for f in /var/www/html/*.php; do stat -c "%Y %Z %n" "$f"; done | awk '{if ($1 != $2) print}'
```

Scripted timestomping detection across a whole webroot — flags any file where mtime and ctime don't align (a forged mtime rarely matches ctime exactly).

```bash
stat -c "%a %U %G %n" /etc/shadow
```

Quick permission/ownership check on a sensitive file — confirms it's still `640 root shadow` (or whatever the expected baseline is) and hasn't been loosened.

---

### Practical CTF/RE use cases

```bash
stat ./challenge_binary
```

Confirms exact file size (cross-check against what `file`/`readelf` report), permissions (is it executable?), and inode — useful basic recon alongside `file` and `checksec`.

```bash
stat -c "%i %n" *.bin
```

Compares inode numbers across multiple filenames — reveals if two differently-named files are actually the same underlying data via a hard link (ties back to the `ln` lesson).

---


> [!tip]+
> **Relevant to your SOC work:** `stat` is the direct payoff of the timestomping discussion from the `touch` lesson — it's the tool that actually displays atime, mtime, _and_ ctime together so you can spot the mismatch that reveals a forged timestamp. Building a habit of `stat`-checking any file you're suspicious of (rather than trusting `ls -l`'s single mtime column) is a meaningfully more rigorous forensic practice, and the scriptable `-c "%Y %Z"` comparison pattern is genuinely useful to keep in your back pocket for auditing an entire directory at once rather than checking files one by one.
