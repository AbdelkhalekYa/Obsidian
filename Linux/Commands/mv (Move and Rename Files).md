#commands 

## `mv` — Move and Rename Files

`mv` moves files/directories to a new location, and is also how Linux handles renaming (a rename is just a "move" within the same directory). Syntax: `mv [OPTIONS] SOURCE DESTINATION`

Unlike `cp`, `mv` doesn't have a `-r` flag — it always moves directories and their full contents by default, since it's just updating the file's location pointer (or doing a copy + delete across filesystems).

---

### 1. `-i` (interactive)

Prompts before overwriting an existing destination file.

```bash
mv -i exploit.py exploit_final.py
```

If `exploit_final.py` already exists: `overwrite exploit_final.py? (y/n)`

---

### 2. `-f` (force)

Overwrites the destination without asking — even overrides a previous `-i` if both are specified (last flag wins).

```bash
mv -f draft.txt final.txt
```

Silently replaces `final.txt` with `draft.txt`'s content, no confirmation.

---

### 3. `-n` (no-clobber)

Never overwrites an existing file — skips the move instead.

```bash
mv -n payload.bin /opt/payloads/
```

If `/opt/payloads/payload.bin` already exists, nothing happens — the original is preserved.

---

### 4. `-v` (verbose)

Shows what's being moved/renamed — useful when moving many files in a script.

```bash
mv -v *.log /var/archive/logs/
```

Prints each move: `'access.log' -> '/var/archive/logs/access.log'`

---

### 5. `-u` (update)

Moves only if the source is newer than an existing destination file, or if the destination doesn't exist.

```bash
mv -u report_draft.docx /shared/reports/
```

If `/shared/reports/report_draft.docx` already exists and is newer, the move is skipped — protects against overwriting someone else's more recent work.

---

### 6. `-b` (backup)

Makes a backup of any file that would be overwritten (default suffix `~`).

```bash
mv -b config.ini /etc/app/config.ini
```

The old file becomes `/etc/app/config.ini~` before being replaced.

---

### 7. `--backup=CONTROL`

Fine-tunes the backup naming scheme: `none`, `simple` (always `~`), `numbered` (`.~1~`, `.~2~`), or `existing` (numbered if numbered backups already exist, simple otherwise).

```bash
mv --backup=numbered payload.py payload.py
```

Useful for iterative script edits where you want a numbered history rather than a single overwritten backup.

---

### 8. `-t` (target-directory)

Explicitly names the destination directory — handy in pipelines where the destination needs to be specified first.

```bash
find . -name "*.pcap" | xargs mv -t /mnt/captures/
```

---

### 9. `-T` (no-target-directory)

Treats the destination as a regular file/name, not a directory — even if a directory with that name exists. Prevents `mv` from "moving into" a same-named folder by mistake.

```bash
mv -T old_project new_project
```

Without `-T`, if `new_project` already exists as a directory, `mv` would move `old_project` _inside_ it rather than renaming it.

---

### 10. `-Z` (context, SELinux systems)

Sets the SELinux security context of the destination to the default type — relevant on hardened/SOC-monitored systems using SELinux.

```bash
mv -Z evidence.bin /secure/evidence/
```

---

### 11. `--strip-trailing-slashes`

Removes any trailing `/` from source arguments before processing — avoids subtle bugs when source paths end in `/`.

```bash
mv --strip-trailing-slashes myfolder/ /backup/
```

---

### 12. `-S` (suffix)

Specifies a custom backup suffix instead of the default `~` (used with `-b` or `--backup`).

```bash
mv -b -S .bak old_config.conf new_config.conf
```

Backup becomes `old_config.conf.bak` instead of `old_config.conf~`.

---

### Common everyday use — renaming

This is the use case you'll hit constantly, since Linux has no separate "rename" command:

```bash
mv old_name.txt new_name.txt
```

### Practical combo

```bash
mv -iv ~/Downloads/*.pcap ~/soc_cases/case_2026_07/
```

Interactive + verbose move of capture files into a case folder — asks before any overwrite and shows exactly what moved.

---


> [!tip]+
> **Relevant to your SOC/media work:** Be careful with `mv` during **incident response / evidence handling** — moving a file changes its parent directory metadata and, on some filesystems, can affect ctime (change time), which matters for chain-of-custody and timeline reconstruction. In forensics contexts, prefer `cp -p` (copy, preserving attributes) over `mv` when you need to relocate evidence but keep the original in place and untouched. For your media/marketing file organization (assets, exports, drafts), `-i` and `-n` are your safety nets against accidentally clobbering a finished file with a draft.

