#commands 

The cp command copies files or directories. It can be used two different ways.
	1. copies the single file or directory item1 to the file or directory item2.
	   `cp item1 item2`
	2. copies multiple items (either files or directories) into a directory.
	   `cp item... directory`

## Flags

### 1. `-r` / `-R` (recursive)

Copies directories and their entire contents recursively. Without this, `cp` fails on directories.

```bash
cp -r /home/user/project /home/user/backup
```

Copies the whole `project` folder — including subfolders and files — into `backup`.

---

### 2. `-i` (interactive)

Prompts for confirmation before overwriting an existing file. Useful to avoid accidentally destroying data.

```bash
cp -i notes.txt notes_backup.txt
```

If `notes_backup.txt` already exists, you'll see: `overwrite notes_backup.txt? (y/n)`

---

### 3. `-v` (verbose)

Prints each file as it's copied — helpful when copying many files and you want to track progress.

```bash
cp -rv /var/log/myapp /mnt/user-data/outputs/
```

Outputs a line like `'/var/log/myapp/error.log' -> '/mnt/user-data/outputs/myapp/error.log'` for every file.

---

### 4. `-f` (force)

Forces the copy by removing the destination file first if it can't be opened for writing (e.g., permission issues). Overrides `-i` if both are used (last one wins).

```bash
cp -f readonly_file.txt /tmp/
```

---

### 5. `-n` (no-clobber)

Never overwrites an existing file — silently skips it instead of asking or forcing. The opposite mindset of `-f`.

```bash
cp -n config.yaml /etc/myapp/config.yaml
```

If `config.yaml` already exists at the destination, it's left untouched.

---

### 6. `-u` (update)

Copies only when the source file is newer than the destination file, or when the destination doesn't exist yet. Great for syncing.

```bash
cp -ru ~/project/src /backup/project/
```

Only newer/changed files get copied — saves time on large directory syncs.

---

### 7. `-p` (preserve)

Preserves file attributes: timestamps, ownership, and permissions (mode). Important in security/forensics work where metadata matters.

```bash
cp -p evidence.bin /mnt/analysis/
```

Keeps the original modification time and permission bits intact — useful when you don't want to alter forensic timestamps.

---

### 8. `-a` (archive)

Shortcut for `-dR --preserve=all` — recursive copy that preserves links, file attributes, ownership, and timestamps. The go-to flag for full, faithful directory/backup copies.

```bash
cp -a /etc /root/etc_backup
```

Common in sysadmin and security work when you need an exact clone of a directory tree (e.g., before modifying system configs).

---

### 9. `-l` (link)

Instead of copying the file's contents, creates a hard link to the source file. Saves disk space, but changes to one affect the other (they share the same inode).

```bash
cp -l bigfile.iso bigfile_link.iso
```

Instant "copy" with zero extra disk usage — but not a true independent copy.

---

### 10. `-s` (symbolic link)

Creates a symbolic (soft) link instead of copying. Different from `-l`: this creates a special file that just points to the original path.

```bash
cp -s /opt/tools/ghidra ~/ghidra_shortcut
```

Handy for creating shortcuts to tools without duplicating large installations (e.g., linking Ghidra into your workspace).

---

### 11. `--backup[=CONTROL]`

Makes a backup of each existing destination file before overwriting it (appends `~` by default, or use a numbered scheme).

```bash
cp --backup=numbered exploit.py exploit.py
```

Creates `exploit.py.~1~`, `exploit.py.~2~`, etc., each time you overwrite — useful when iterating on exploit scripts and you want a history without git.

---

### 12. `-x` (one-file-system)

Stays on the same filesystem — won't cross into other mounted filesystems while copying recursively. Prevents accidentally copying huge mounted drives or network shares.

```bash
cp -avx / /mnt/backup_drive/
```

Common in **full system backups** — copies the root filesystem but skips `/proc`, `/sys`, or other mounted partitions.

---

### 13. `-t` (target-directory)

Explicitly specifies the destination directory — useful in scripts, especially combined with `xargs` or `find`, where the destination needs to come first syntactically.

```bash
find . -name "*.log" | xargs cp -t /var/backup/logs/
```

---

### Quick combo you'll actually use a lot

```bash
cp -av ~/ctf/labs/pwn_practice /mnt/backup/ctf_labs_$(date +%F)
```

Archive-mode, verbose copy of your CTF practice folder into a dated backup — preserves permissions/timestamps and shows progress.

---

> [!tip]+
> **Relevant to your SOC work:** `-p`/`-a` matter a lot in incident response and forensics — when collecting evidence (logs, binaries, memory dumps) you generally want to preserve original timestamps and permissions rather than let `cp` reset them to "now," since that metadata can be part of the timeline you're reconstructing. `-v` is also handy for evidence collection since it logs exactly what was copied.
> 
