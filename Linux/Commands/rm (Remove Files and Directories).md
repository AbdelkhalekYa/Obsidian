#commands 

## `rm` — Remove Files and Directories

`rm` permanently deletes files and directories. **There is no recycle bin/trash by default** — once removed, recovery is difficult or impossible without specialized forensic tools. Syntax: `rm [OPTIONS] FILE...`

---

### 1. `-r` / `-R` (recursive)

Required to delete directories and their entire contents. Without it, `rm` refuses to touch a directory.

```bash
rm -r old_project/
```

Deletes `old_project` and everything inside it, recursively.

---

### 2. `-f` (force)

Ignores nonexistent files and never prompts for confirmation — even overrides write-protection warnings. This is the most dangerous flag combined with `-r`.

```bash
rm -f temp_file.txt
```

Deletes silently, no error even if the file doesn't exist, no confirmation asked.

---

### 3. `-i` (interactive)

Prompts before every removal — the safety-first opposite of `-f`.

```bash
rm -i *.log
```

Asks `remove access.log?` for each matching file individually.

---

### 4. `-I` (interactive, less intrusive)

Prompts only once before a recursive delete of 3+ files, or when deleting recursively — much less annoying than `-i` for bulk operations while still giving one safety checkpoint.

```bash
rm -rI old_backups/
```

Asks once: `remove 47 arguments recursively?` instead of once per file.

---

### 5. `-v` (verbose)

Prints each file as it's deleted — useful for auditing what a script actually removed.

```bash
rm -rv /tmp/scan_results/
```

Outputs `removed '/tmp/scan_results/host1.xml'` for every file.

---

### 6. `-d` (dir)

Removes empty directories (without needing `-r`). Fails if the directory has contents.

```bash
rm -d empty_folder/
```

---

### 7. `--preserve-root` (default) / `--no-preserve-root`

By default, `rm` refuses to operate recursively on `/` (protects against catastrophic mistakes). `--no-preserve-root` disables that protection — **extremely dangerous**, essentially never needed.

```bash
rm -rf --no-preserve-root /   # DO NOT RUN — wipes the entire filesystem
```

Mentioned here mainly so you recognize it in scripts and know to be alarmed if you see it.

---

### 8. `--interactive=WHEN`

Fine-grained control over prompting: `never`, `once` (same as `-I`), or `always` (same as `-i`).

```bash
rm --interactive=once -r cache/
```

---

### 9. `--one-file-system`

When removing recursively, skips any directory that's on a different filesystem than the one being deleted from. Prevents accidentally wiping a separately-mounted drive nested inside the target path.

```bash
rm -r --one-file-system /mnt/data/
```

---

### 10. `-v` combined with `-i` (common safe pattern)

```bash
rm -riv suspicious_downloads/
```

Confirms before each delete and logs exactly what was removed — good habit when cleaning up anything you're not 100% sure about.

---

### 11. `rm -rf` (the infamous combo)

Force + recursive together — deletes everything, no prompts, no errors on missing files. Extremely common in scripts, extremely destructive if the path is wrong.

```bash
rm -rf build/ dist/ node_modules/
```

Standard for clearing build artifacts — but always double-check the path before hitting enter, especially with variables:

```bash
rm -rf "$BUILD_DIR"/   # if $BUILD_DIR is unset/empty, this can become `rm -rf /`
```

---

### Safer alternatives worth knowing

- `rm -I` instead of `rm -rf` for bulk deletes — one confirmation instead of zero.
- Many people alias `rm` to `rm -i` in their shell config (`alias rm='rm -i'`) to force the safety prompt by default.
- `trash-cli` (a separate tool, `trash` command) sends files to a recoverable trash folder instead of permanently deleting — worth installing if you're worried about mistakes.

---


> [!tip]+
> **Relevant to your SOC/security work:** In incident response, **never `rm` anything related to an active investigation** — evidence and logs should be preserved, hashed, and backed up before any deletion. If you're cleaning up after a CTF/lab exercise, `rm -rf` on your practice/scratch directories is fine, but get in the habit of double-checking the path (`pwd` first, or use tab-completion instead of typing paths from memory) since a mistyped `rm -rf` is one of the most common ways people destroy real work by accident. For your media/marketing files, consider `trash-cli` for anything client-facing or unrecoverable — cheap insurance against a bad keystroke.
