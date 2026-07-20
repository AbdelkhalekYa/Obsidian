#commands 

## `find` — Search for Files and Directories

`find` searches a directory tree for files/directories matching criteria — name, type, size, permissions, modification time, and more — and can execute actions on whatever it finds. It's one of the most powerful (and flag-dense) tools in Linux, genuinely essential for both CTF recon and SOC/forensic investigation. Syntax: `find [PATH...] [OPTIONS/TESTS] [ACTIONS]`

---

### 1. (no flag) — basic usage

Lists every file and directory recursively under the given path.

```bash
find /home/user
```

---

### 2. `-name` (search by filename, case-sensitive)

Matches filenames using shell-style wildcards (`*`, `?`).

```bash
find /var/www -name "*.php"
```

Finds every PHP file under `/var/www` — a common first step when hunting for a planted webshell.

---

### 3. `-iname` (case-insensitive name search)

Same as `-name`, but ignores case.

```bash
find / -iname "*shell*" 2>/dev/null
```

---

### 4. `-type` (filter by file type)

Restricts results to a specific type: `f` (regular file), `d` (directory), `l` (symbolic link), `s` (socket), `p` (named pipe), `b`/`c` (block/character device).

```bash
find /etc -type f -name "*.conf"
find / -type l                      # list every symlink on the system
```

---

### 5. `-size` (filter by file size)

Finds files by size, with `+` (greater than) or `-` (less than) prefixes and unit suffixes (`c`=bytes, `k`=KB, `M`=MB, `G`=GB).

```bash
find / -size +100M                  # files larger than 100MB
find /tmp -size -1k                 # tiny files, possibly suspicious in /tmp
```

---

### 6. `-mtime` / `-atime` / `-ctime` (filter by timestamp, in days)

Finds files modified/accessed/changed N days ago, with `+N` (more than N days ago), `-N` (less than N days ago), or exact `N`.

```bash
find / -mtime -1                    # modified in the last 24 hours
find /etc -ctime -7                 # metadata changed in the last week
```

Extremely useful in IR to narrow down what changed during a suspected compromise window.

---

### 7. `-mmin` / `-amin` / `-cmin` (filter by timestamp, in minutes)

Same idea as above but minute-granularity — much more precise for active incident investigation.

```bash
find / -mmin -60                    # modified in the last hour
```

---

### 8. `-newer FILE` (modified more recently than a reference file)

Finds files modified after a known reference point — one of the single best IR one-liners for "what changed since this known-good moment."

```bash
find / -newer /etc/passwd -type f 2>/dev/null
```

Compares against a file with a known-safe modification time (e.g., right after last patch/install) to surface anything touched since.

---

### 9. `-perm` (filter by permissions)

Finds files matching exact or partial permission patterns — critical for spotting dangerous permission misconfigurations.

```bash
find / -perm -4000 -type f 2>/dev/null
```

Finds all **SUID** binaries system-wide (`-4000` = setuid bit set) — a classic privilege-escalation enumeration step in both CTF and real pentesting.

```bash
find / -perm -002 -type f 2>/dev/null
```

Finds world-writable files — another common privesc/security-audit check.

---

### 10. `-user` / `-group` (filter by owner)

Finds files owned by a specific user or group.

```bash
find / -user root -perm -4000 2>/dev/null
find /home -group www-data
```

---

### 11. `-exec COMMAND {} \;` (run a command on each match)

Executes a command on every matching file, with `{}` substituted for the filename and `\;` terminating the command (per-file execution).

```bash
find . -name "*.tmp" -exec rm {} \;
```

Deletes every `.tmp` file found. `{}` is replaced with each matched path individually.

```bash
find / -name "*.php" -exec grep -l "eval(base64_decode" {} \; 2>/dev/null
```

Combines file discovery with content inspection in one command — classic webshell hunting.

---

### 12. `-exec COMMAND {} +` (batch execution)

Like `-exec ... \;`, but batches multiple matched files into fewer command invocations (like `xargs`) — much faster for commands that accept multiple file arguments.

```bash
find . -name "*.log" -exec chmod 644 {} +
```

---

### 13. `-delete` (delete matches directly)

Deletes every matched file/directory — more efficient than `-exec rm {} \;` since it doesn't spawn a separate process per file. **Use with extreme caution** — always test with `-print` first.

```bash
find /tmp -name "*.tmp" -mtime +7 -print       # preview first!
find /tmp -name "*.tmp" -mtime +7 -delete       # then actually delete
```

---

### 14. `-maxdepth` / `-mindepth` (limit recursion depth)

Restricts how many directory levels deep `find` searches — `-maxdepth 1` limits to the starting directory only (non-recursive), useful for performance or precision.

```bash
find /var/log -maxdepth 1 -name "*.log"
```

Only top-level `.log` files, ignores anything in subdirectories.

---

### 15. `-not` / `!` (negate a test)

Excludes results matching a condition.

```bash
find . -type f -not -name "*.py"
```

Every file except Python files.

---

### 16. `-a` / `-o` (AND / OR logic)

Combines multiple conditions. `-a` (default, often implicit) requires all conditions; `-o` requires only one.

```bash
find / -name "*.log" -o -name "*.txt"
```

Matches either extension.

```bash
find /home -type f -name "*.sh" -perm -111
```

Files that are BOTH shell scripts AND executable (implicit AND between conditions).

---

### 17. `-print0` (NUL-terminated output)

Outputs filenames separated by NUL bytes instead of newlines — safely handles filenames containing spaces, newlines, or other unusual characters when piping into `xargs` or `sort`.

```bash
find . -name "*.log" -print0 | xargs -0 rm
```

The standard safe pattern whenever piping `find` output into another command that processes filenames.

---

### 18. `-empty` (find empty files/directories)

```bash
find . -type d -empty
```

Finds every empty directory — useful for cleanup or spotting oddly-placed empty dirs during investigation.

---

### 19. `-regex` (match full path with regex)

More powerful than `-name` — matches the entire path against a regular expression, not just the filename with a wildcard.

```bash
find . -regex ".*/[0-9]\{4\}-[0-9]\{2\}-[0-9]\{2\}\.log"
```

Matches log files named in a `YYYY-MM-DD.log` date pattern anywhere in the tree.

---

### Combining flags — real-world power patterns

```bash
find / -type f \( -perm -4000 -o -perm -2000 \) 2>/dev/null
```

Finds all SUID **or** SGID binaries system-wide — one of the very first commands run during Linux privilege-escalation enumeration in CTF/pentest work.

```bash
find / -mmin -120 -type f 2>/dev/null | grep -v "^/proc"
```

Files modified in the last 2 hours, excluding the noisy `/proc` filesystem — fast "what just changed" incident-response check.

---

### Practical SOC/IR use cases

```bash
find / -perm -4000 -type f 2>/dev/null                    # SUID binary audit
find / -newer /etc/passwd -type f 2>/dev/null              # changes since a known point
find /var/www -name "*.php" -mtime -1 2>/dev/null           # recently modified web files — webshell hunting
find / -name ".*" -type f -mtime -7 2>/dev/null             # recently modified hidden files
find /tmp /var/tmp /dev/shm -type f 2>/dev/null              # common malware-drop locations
```

### Practical CTF/PWN use cases

```bash
find / -perm -4000 2>/dev/null              # SUID enumeration — classic privesc step in CTF boxes
find / -writable -type d 2>/dev/null         # world-writable directories
find / -name "flag*" 2>/dev/null
find / -name "*.txt" -newer /etc/hostname 2>/dev/null
```

---


> [!tip]+
> **Relevant to your work:** `find / -perm -4000 2>/dev/null` (SUID binary enumeration) is one of the single most-repeated commands in both CTF privilege-escalation and real-world SOC/pentest work — it's often the very first move once you have shell access on a Linux box. On the defensive/SOC side, `find -newer` and `find -mmin`/`-mtime` are core IR tools for scoping a compromise timeline: given one known-good reference point (a patch date, a known-clean file), you can rapidly surface everything that changed since. The `2>/dev/null` at the end of most system-wide `find` commands is a habit worth keeping — it silences the constant "Permission denied" noise from directories you can't read, keeping actual results visible.
