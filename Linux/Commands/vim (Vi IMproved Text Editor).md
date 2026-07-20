#commands 

## `vim` — Vi IMproved Text Editor

`vim` is a powerful, modal text editor — the "improved" successor to the classic `vi`. Unlike `nano`, it has a steeper learning curve (multiple editing modes, no on-screen key hints by default) but offers vastly more powerful editing capabilities: macros, registers, plugins, scripting, and efficient keyboard-only workflows. It's the default editor on most servers and a near-universal skill in sysadmin/security work. Syntax: `vim [OPTIONS] [FILE]`

---

### 1. (no flag) — basic usage

Opens a file for editing (creates it on save if it doesn't exist).

```bash
vim config.yaml
```

---

### 2. `-R` (read-only mode)

Opens the file in read-only mode — you can view and navigate freely, but accidental edits are blocked. Safer than a normal open when you just need to inspect something sensitive.

```bash
vim -R /etc/shadow
```

Equivalent to running `view file` instead of `vim file` (`view` is literally a symlink to vim with `-R` baked in).

---

### 3. `+N` (open at a specific line)

Opens the file with the cursor placed directly at line N — same concept as `nano +LINE`.

```bash
vim +42 exploit.py
```

---

### 4. `+/PATTERN` (open at first match of a search)

Opens the file and jumps immediately to the first line matching a pattern, rather than a fixed line number.

```bash
vim +/system exploit.py
```

Jumps straight to the first occurrence of "system" — useful when you know what you're looking for but not the exact line.

---

### 5. `-o` / `-O` (open multiple files in split windows)

`-o` splits horizontally, `-O` splits vertically — opens several files at once, each visible simultaneously.

```bash
vim -o exploit.py notes.txt
vim -O binary.c binary.h
```

Genuinely useful for comparing a source file against its disassembly notes, or an exploit script against its output log, side by side.

---

### 6. `-p` (open multiple files as tabs)

Opens each given file in its own tab instead of a split — useful when juggling several files you don't need visible simultaneously.

```bash
vim -p file1.py file2.py file3.py
```

---

### 7. `-d` (diff mode)

Opens two (or more) files in side-by-side diff view, highlighting the differences — genuinely useful for comparing two versions of a config, script, or a patched vs. original binary's disassembly dump.

```bash
vim -d original_config.conf modified_config.conf
```

Equivalent to running `vimdiff`.

---

### 8. `-c COMMAND` (run a command on startup)

Executes a Vim command (Ex command) immediately after opening the file — useful for scripting automated edits.

```bash
vim -c "%s/http:/https:/g" -c "wq" urls.txt
```

Opens the file, runs a global find-and-replace, then saves and quits — all in one non-interactive command. Genuinely powerful for batch text processing without opening an interactive session at all.

---

### 9. `-n` (no swap file)

Disables Vim's swap file (`.filename.swp`) creation — normally used for crash recovery, but sometimes you want to disable it (e.g., editing on a read-only or network filesystem, or when you don't want stray swap files left behind during a sensitive operation).

```bash
vim -n /mnt/readonly_share/notes.txt
```

---

### 10. `-u FILE` (use alternate config)

Loads a specific `vimrc` config file instead of the default (`~/.vimrc`) — useful for testing a config change safely, or using a minimal/portable config on an unfamiliar system.

```bash
vim -u NONE script.py          # no config at all — pure vanilla vim
vim -u /tmp/test.vimrc file.py  # a specific alternate config
```

---

### 11. `-x` (encrypt on save)

Prompts for a password and encrypts the file when saved — a built-in (if fairly weak by modern standards) encryption feature.

```bash
vim -x secrets.txt
```

---

### 12. `-b` (binary mode)

Opens the file in binary-safe mode — disables text-mode assumptions (like automatic line-ending conversion) that could corrupt non-text data. Relevant when using Vim as an impromptu hex/byte-level editor.

```bash
vim -b payload.bin
```

Often paired with the `:%!xxd` trick (see below) for actual hex editing.

---

### 13. `--` (end of options marker)

Signals that everything after it is a filename, not a flag — useful if a filename itself starts with a dash.

```bash
vim -- -weird-filename.txt
```

---

### Essential modes (the core concept that makes vim different from nano)

|Mode|How to enter|Purpose|
|---|---|---|
|**Normal**|`Esc` (default on open)|Navigation, running commands|
|**Insert**|`i`, `a`, `o`|Actually typing text|
|**Visual**|`v`, `V`, `Ctrl+v`|Selecting text (character/line/block)|
|**Command**|`:`|Running Ex commands (save, quit, search/replace)|

---

### Core keybindings worth knowing immediately

|Command|Action|
|---|---|
|`i`|Enter insert mode before cursor|
|`Esc`|Return to normal mode|
|`:w`|Save|
|`:q`|Quit|
|`:wq` or `ZZ`|Save and quit|
|`:q!`|Quit without saving (discard changes)|
|`dd`|Delete current line|
|`yy`|Copy (yank) current line|
|`p`|Paste|
|`u`|Undo|
|`Ctrl+r`|Redo|
|`/pattern`|Search forward|
|`n` / `N`|Next / previous search match|
|`:%s/old/new/g`|Find and replace, whole file|
|`gg` / `G`|Go to top / bottom of file|
|`:42`|Jump to line 42|

---

### The hex-editing trick worth knowing for CTF work

Vim can act as a basic hex editor using the built-in `xxd` filter:

```bash
vim -b payload.bin
```

Then inside vim:

```
:%!xxd
```

Converts the buffer to a hex dump you can edit directly. To convert back before saving:

```
:%!xxd -r
```

Genuinely useful for quick binary patching without needing a dedicated hex editor installed.

---

### `vim` vs `nano` — when each makes sense

|Situation|Better choice|
|---|---|
|Quick one-off edit, minimal learning curve|`nano`|
|Heavy scripting, macros, repeated complex edits|`vim`|
|Editing on almost any Unix system ever (vi is basically universal)|`vim`/`vi` — guaranteed to exist even on minimal/embedded systems|
|Batch non-interactive edits via `-c` commands in scripts|`vim`|

---

### Practical SOC use cases

```bash
vim -R /var/log/auth.log
```

Read-only inspection of a sensitive log — prevents any accidental edit to evidence.

```bash
vim -d current_sshd_config /backup/sshd_config.orig
```

Diff-compares the live config against a known-good backup — fast way to spot unauthorized changes.

```bash
vim -c "g/Failed password/p" -c "q" /var/log/auth.log
```

Non-interactive: prints every line containing "Failed password" and exits — (though `grep` is more natural for this specific case; this is mainly to illustrate `-c` scripting).

### Practical CTF/PWN use cases

```bash
vim -b shellcode.bin
:%!xxd
```

Quick binary patching workflow — inspect and modify raw bytes directly.

```bash
vim -O exploit.py leaked_offsets.txt
```

Side-by-side view of your exploit script and a notes file with leaked addresses/offsets — very common workflow while building a multi-stage exploit.

---


> [!tip]+
> **Relevant to your work:** `vim -R` is a habit worth building for the same reason `nano -v` and `cat` (versus editors generally) matter in SOC/forensic work — opening evidence or sensitive configs read-only by default protects against an accidental keystroke altering something you need to preserve unmodified. For your PWN work, the `-d` diff mode and `-O` split-window mode are genuinely practical once your exploit scripts start referencing multiple files (leaked addresses, offsets, template code) simultaneously — worth learning even if you stick with `nano` for quick edits day-to-day.
