#commands 

## `ln` — Create Links

`ln` creates links between files — pointers that let one or more names reference the same underlying data (hard links) or reference a path (symbolic/soft links). Syntax: `ln [OPTIONS] TARGET LINK_NAME`

There are two fundamentally different link types, so understanding the core distinction matters more than any flag:

- **Hard link** (default): a second directory entry pointing to the _same inode_ (same actual data on disk). No difference from the original — deleting the "original" leaves the hard link fully intact.
- **Symbolic link** (`-s`): a special file that just contains a _path string_ pointing to another file. Breaks if the target is moved/deleted ("dangling link").

---

### 1. `-s` (symbolic)

Creates a sym link instead of a hard link. By far the most commonly used flag — most people mean sym link when they say "create a link."

```bash
ln -s /opt/ghidra/ghidraRun ~/bin/ghidra
```

Creates a shortcut at `~/bin/ghidra` that points to the real Ghidra launcher — running `~/bin/ghidra` runs the target.

---

### 2. `-f` (force)

Removes the destination file first if it already exists, so the link creation doesn't fail with "file exists."

```bash
ln -sf /opt/tools/latest_pwntools ~/venv/pwntools
```

Overwrites an existing sym link to point somewhere new — common when switching between tool versions.

---

### 3. `-i` (interactive)

Prompts before overwriting an existing destination file.

```bash
ln -si target.txt link.txt
```

---

### 4. `-v` (verbose)

Prints the link created — useful in scripts creating many links.

```bash
ln -sv /etc/nginx/sites-available/app.conf /etc/nginx/sites-enabled/app.conf
```

Outputs `'/etc/nginx/sites-enabled/app.conf' -> '/etc/nginx/sites-available/app.conf'` — this exact pattern is the standard way nginx/apache manage enabled configs.

---

### 5. `-n` (no-dereference)

When the destination is itself a symlink to a directory, treat it as a normal file to overwrite rather than following it and dropping the new link inside that directory. Critical when combined with `-f`.

```bash
ln -sfn /opt/releases/v2.3 /opt/app/current
```

This is the classic **atomic deployment / symlink-swap pattern**: `current` always points to the "live" release, and redeploying just repoints it. Without `-n`, if `current` already exists as a symlink, `ln -sf` would follow it and create the new link _inside_ the target directory instead of replacing `current` itself.

---

### 6. `-b` (backup)

Backs up the destination file before it's overwritten/removed (used with `-f`).

```bash
ln -sbf new_config.yaml active_config.yaml
```

Old `active_config.yaml` becomes `active_config.yaml~` before being replaced by the new link.

---

### 7. `-T` (no-target-directory)

Treats `LINK_NAME` as a regular file path, not a directory — prevents `ln` from placing the link _inside_ an existing same-named directory.

```bash
ln -sT /data/project_v2 /data/project_current
```

---

### 8. `-r` (relative)

Creates a symlink using a relative path instead of an absolute one, calculated automatically based on the link's location. Useful for links inside portable directory trees (e.g., a repo you might move or copy elsewhere).

```bash
ln -sr /home/user/ctf/tools/pwninit ./pwninit
```

The resulting link uses `../tools/pwninit` (or similar) instead of the hardcoded absolute path — keeps working if the whole folder is moved.

---

### 9. `-P` (physical, no-dereference of target)

When the target itself is a symlink, `-P` makes the hard link point to the symlink itself rather than resolving through it to the final file. Rarely needed but relevant for advanced hard-link scenarios.

```bash
ln -P sym_target hardlink_name
```

---

### 10. `-L` (logical, dereference target)

The default-ish behavior for hard links to symlinks — resolves the symlink and hard-links to the actual underlying file rather than the link itself.

```bash
ln -L /usr/bin/python -> real_python_binary
```

---

### 11. `--relative` combined with `-s` (long form of `-r`)

```bash
ln -s --relative /var/log/app/current.log ./latest.log
```

---

### Hard link vs symlink — quick example to see the difference

```bash
echo "original data" > file1.txt
ln file1.txt hardlink.txt        # hard link
ln -s file1.txt symlink.txt      # symbolic link

rm file1.txt

cat hardlink.txt   # → "original data"  (still works, same inode)
cat symlink.txt    # → No such file or directory (dangling — path no longer resolves)
```

---


> [!attention]+
> **Important limitations to know:**
> 
> - Hard links **cannot** cross filesystems/partitions (both must be on the same filesystem) and **cannot** link to directories (with rare exceptions requiring root, and generally disallowed). Symlinks have neither restriction.
> - `ls -l` shows symlinks with an `l` at the start of the permissions string and a `->` pointing at the target, e.g. `lrwxrwxrwx ... shortcut -> /real/path`.


---


> [!tip]+
> **Relevant to your work:** In your CTF/PWN environment, `ln -s` is genuinely useful for keeping tool shortcuts (Ghidra, pwndbg configs, wordlists) accessible from a consistent path even as you update tool versions. In SOC/forensics contexts, be aware that symlinks can be abused — attackers sometimes use them for privilege escalation (symlink races) or to make a malicious file _appear_ to be something benign; when triaging a filesystem, `find / -type l` to enumerate symlinks and checking where they actually point is a useful hygiene check.

