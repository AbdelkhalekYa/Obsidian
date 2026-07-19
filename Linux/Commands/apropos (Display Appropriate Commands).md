#commands 

## `apropos` — Display Appropriate Commands

`apropos` searches the short descriptions (and optionally full page names) of all man pages for a given keyword, returning every command that might be relevant — even if you don't know its exact name. It's functionally identical to `man -k`. Syntax: `apropos [OPTIONS] KEYWORD...`

Same honesty note as with `help`: `apropos` is a small, focused tool — it doesn't genuinely have 10+ distinct flags. I'll give you the complete real set with detailed explanations rather than padding it out.

---

### 1. (no flag) — basic usage

Searches man page names and descriptions for the keyword (matches whole words by default).

```bash
apropos partition
```

Output: lists every man page whose name or description contains "partition," e.g.:

```
fdisk (8)     - manipulate disk partition table
parted (8)    - a partition manipulation program
```

---

### 2. `-r` (regex)

Treats the keyword as a POSIX regular expression instead of plain text — this is actually the **default** behavior on most modern `man-db` systems, but explicitly forcing it guarantees regex interpretation.

```bash
apropos -r '^ssh'
```

Matches any man page name starting with "ssh" — `ssh`, `sshd`, `ssh-keygen`, `sshfs`, etc.

---

### 3. `-e` (exact match)

Matches the keyword as a literal, exact string rather than a regex or wildcard — useful when your search term contains characters that would otherwise be interpreted as regex metacharacters.

```bash
apropos -e "printf"
```

---

### 4. `-w` (wildcard)

Interprets the keyword using shell-style wildcards (`*`, `?`) instead of regex.

```bash
apropos -w 'ssh*'
```

Matches any page name starting with "ssh".

---

### 5. `-a` (AND logic for multiple keywords)

By default, multiple keywords are OR'd (any match qualifies). `-a` requires **all** given keywords to appear in the description.

```bash
apropos -a network scan
```

Only returns pages whose description contains both "network" AND "scan" — e.g., `nmap`.

Without `-a`:

```bash
apropos network scan
```

Would return anything matching "network" OR "scan" separately — a much noisier result set.

---

### 6. `-s` (section)

Restricts results to specific man page sections (same numbering as `man`: 1 = commands, 2 = syscalls, 3 = library functions, 5 = file formats, 8 = admin commands).

```bash
apropos -s 2 socket
```

Only shows section 2 (system call) results for "socket" — filters out unrelated library or file-format matches.

---

### 7. `-l` (long output / don't truncate)

Ensures the full description line is shown without truncating to terminal width — useful when piping to a file or when descriptions are getting cut off.

```bash
apropos -l encryption
```

---

### 8. `-m` (system / alternate man page source)

Searches man pages associated with a different operating system's page set, if such alternate sets are installed (rarely used on typical single-OS systems).

```bash
apropos -m linux socket
```

---

### 9. `-M` (manpath override)

Searches a custom man page path instead of the system default — same concept as `man -M`, relevant if you have locally-installed tool documentation outside standard directories.

```bash
apropos -M /opt/mytool/man config
```

---

### 10. `-C` (config file)

Uses a specified alternate configuration file instead of the default `man.conf`/`man_db.conf`.

```bash
apropos -C ~/.man.conf debug
```

---

### 11. `--long` (long form of `-l`)

Same as above, spelled out.

```bash
apropos --long "buffer overflow"
```

---

### `apropos` vs `man -k` vs `whatis`

|Command|What it does|
|---|---|
|`apropos KEYWORD`|Searches names **and** descriptions for a keyword match anywhere|
|`man -k KEYWORD`|Identical to `apropos` — it's literally the same underlying tool|
|`whatis NAME`|Exact name match only, one-line description (no keyword/partial search)|

```bash
whatis ls        # → ls (1) - list directory contents   (exact name only)
apropos director  # → matches "ls", "rmdir", "mkdir", etc. (partial keyword search)
```

---

### Why `apropos` sometimes returns "nothing appropriate"

This is the most common frustration with the tool — it depends entirely on the **`mandb`** search index being built. If a system is freshly installed or man pages were added manually, the index may be stale or missing.

```bash
sudo mandb
```

Rebuilds the search database. Run this if `apropos` returns unexpectedly empty results for something you know has a man page.

---

### Practical CTF/security use cases

```bash
apropos -a "disassemble" 
apropos debugger
apropos -s 2 ptrace
apropos "elf format"
```

Genuinely useful when you're on an unfamiliar system (e.g., a CTF pwn box, a jump host during an engagement) and need to discover what binary analysis or system tools are actually available without knowing exact names in advance.

---


> [!tip]+
> **Relevant to your SOC work:** `apropos` is a solid first move during IR on an unfamiliar Linux box — running something like `apropos -a network monitor` or `apropos process` quickly surfaces what native investigative tools exist on that specific host (which varies a lot between distros/hardening levels) without needing internet access or prior knowledge of that system's toolset. Just remember to check `sudo mandb` first if the results seem suspiciously empty — that's almost always an index problem, not a "the tool doesn't exist" problem.
