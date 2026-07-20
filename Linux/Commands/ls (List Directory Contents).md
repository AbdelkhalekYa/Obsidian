#commands 

## `ls` — List Directory Contents

`ls` lists files and directories. It's the single most-typed command in any Linux shell session, and its flags are essential daily tools — especially in security work where permissions, ownership, hidden files, and timestamps all carry investigative meaning. Syntax: `ls [OPTIONS] [FILE/DIRECTORY...]`

---

### 1. (no flag) — basic usage

Lists filenames in the current directory, in columns, alphabetically.

```bash
ls
```

---

### 2. `-l` (long format)

Shows detailed info per file: permissions, link count, owner, group, size, modification date, and name. The single most-used flag.

```bash
ls -l
```

Output:

```
-rwxr-xr-x 1 root root  8432 Jul 20 09:14 exploit.py
drwxr-xr-x 2 user user  4096 Jul 19 22:03 tools
```

---

### 3. `-a` (all — include hidden files)

Shows hidden files (those starting with `.`), including the special `.` (current dir) and `..` (parent dir) entries. Critical in security contexts — attackers routinely hide files/configs as dotfiles.

```bash
ls -a
```

```bash
ls -la ~
```

Shows hidden config files like `.bashrc`, `.ssh/`, `.bash_history` — often the first thing worth checking on a newly-accessed system.

---

### 4. `-A` (almost all)

Like `-a`, but excludes `.` and `..` — cleaner output when you just want hidden files, not the two navigation entries.

```bash
ls -lA
```

---

### 5. `-h` (human-readable sizes)

Displays file sizes in KB/MB/GB instead of raw bytes — almost always paired with `-l`.

```bash
ls -lh
```

Output shows `4.2M` instead of `4404019` — dramatically easier to read at a glance.

---

### 6. `-t` (sort by modification time, newest first)

Sorts by mtime instead of alphabetically — genuinely one of the highest-value combos for investigation.

```bash
ls -lt
```

```bash
ls -lat /tmp
```

Instantly surfaces the most recently modified files in a directory — the natural first check when you suspect something was recently dropped or edited (e.g., in `/tmp`, `/var/www`, or a user's home directory).

---

### 7. `-r` (reverse order)

Reverses whatever sort order is currently in effect — combine with `-t` to get oldest-first instead of newest-first.

```bash
ls -ltr
```

Very common combo: newest-last output, so the most recent files appear at the bottom of your terminal (right above your prompt) instead of scrolling off the top — easier to spot without scrolling.

---

### 8. `-S` (sort by size, largest first)

```bash
ls -lhS
```

Quickly finds the biggest files in a directory — useful for disk-usage triage or spotting an unexpectedly large file (e.g., a dumped memory image, exfiltrated data staged for transfer, or a bloated log).

---

### 9. `-R` (recursive)

Lists the contents of all subdirectories recursively, not just the top level.

```bash
ls -R /etc/cron.d
```

---

### 10. `-d` (directory itself, not contents)

Shows info about a directory itself rather than listing what's inside it — useful with `-l` to check a directory's own permissions/ownership.

```bash
ls -ld /var/www
```

Output: `drwxr-xr-x 5 www-data www-data 4096 Jul 15 10:00 /var/www` — shows the directory's own metadata, not the files inside it.

---

### 11. `-i` (inode number)

Shows each file's inode number — useful for confirming whether two different-looking filenames actually point to the same underlying data (relevant for hard link investigation, covered back in the `ln` lesson).

```bash
ls -li
```

---

### 12. `-F` (classify — append type indicator)

Appends a symbol to each entry indicating its type: `/` for directories, `*` for executables, `@` for symlinks, `|` for named pipes, `=` for sockets.

```bash
ls -F
```

Output:

```
exploit.py*
tools/
config.yaml
link_to_binary@
```

Fast visual scan for what's executable or a symlink without needing full `-l` output.

---

### 13. `-1` (one entry per line)

Forces single-column output — useful for piping `ls` output into another command (`wc -l`, `grep`, a loop) where column-formatted output would break parsing.

```bash
ls -1 *.log | wc -l
```

---

### 14. `--color=auto`

Colorizes output by file type (directories, executables, symlinks, archives all get different colors) — usually on by default via alias on most distros, but worth knowing explicitly.

```bash
ls --color=auto -l
```

---

### 15. `-G` (no group column, with `-l`)

Omits the group name column from long-format output — slightly more compact.

---

### 16. `-n` (numeric UID/GID)

Like `-l`, but shows numeric user/group IDs instead of resolved names — useful when a UID doesn't map to a known user (e.g., orphaned files from a deleted account, or a container/chroot context where `/etc/passwd` doesn't have the mapping).

```bash
ls -ln
```

Output: `-rw-r--r-- 1 1001 1001 220 Jul 20 09:00 file.txt` — if `1001` doesn't correspond to anyone in `/etc/passwd`, that's often a red flag worth investigating.

---

### 17. `-u` (sort/show by access time instead of modification time)

Combined with `-l`, shows atime instead of mtime; combined with `-t`, sorts by atime instead of mtime.

```bash
ls -lu
```

---

### 18. `-c` (sort/show by change time — ctime)

Shows/sorts by ctime (metadata change time) instead of mtime — as covered in the `touch` lesson, ctime is the harder-to-forge timestamp, making this genuinely useful for spotting timestomped files.

```bash
ls -lct
```

Sorted by ctime, newest first — if a file's ctime is suspiciously recent while its displayed mtime (from a normal `ls -lt`) looks old, that mismatch is a strong timestomping indicator.

---

### 19. `--full-time`

Shows the complete timestamp (year, exact time down to the second, timezone) instead of the abbreviated default — more forensically precise.

```bash
ls -l --full-time
```

Output: `-rw-r--r-- 1 root root 220 2026-07-20 09:14:03.482910000 +0300 file.txt`

---

### Combining flags — the go-to security/investigation one-liner

```bash
ls -lath
```

Long format, all files (including hidden), sorted by time (newest first), human-readable sizes — genuinely the single most useful `ls` combo for a quick "what's here and what changed recently" first look at any directory.

---

### Practical SOC/IR use cases

```bash
ls -lat /tmp /var/tmp /dev/shm
```

Checks common malware-drop locations, newest-first — a fast first move on a suspect host.

```bash
ls -la ~/.ssh
```

Checks for unauthorized SSH keys (`authorized_keys` modifications are a classic persistence technique).

```bash
ls -ln /home/*/. 2>/dev/null
```

Numeric UID check across home directories — spots orphaned/mismatched ownership.

---

### Practical CTF/PWN use cases

```bash
ls -la /                    # quick recon after landing a shell — what's exposed at root
ls -la ~/                   # check for interesting dotfiles, history, SSH keys
ls -lS /var/www/html        # find unusually large files that might be a webshell or data dump
```

---


> [!tip]+
> **Relevant to your work:** `ls -lath` (or the ctime variant `ls -lcth`) is genuinely one of the first commands you'll run on any host during SOC triage — it's your fastest way to answer "what's in this directory and what changed recently" without needing anything fancier. Understanding the mtime/ctime distinction here connects directly back to the `touch`/timestomping discussion: `ls -lt` alone can be fooled by forged mtimes, so pairing it with `ls -lct` (ctime-sorted) is the more reliable check when you suspect a file's timestamps have been deliberately manipulated.
