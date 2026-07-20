#commands 

## `nano` — Simple Terminal Text Editor

`nano` is a lightweight, beginner-friendly command-line text editor — unlike `vim` or `emacs`, it shows its keyboard shortcuts on-screen at all times and requires no modal editing knowledge to get started. It's usually the default editor on many Linux distros for quick edits. Syntax: `nano [OPTIONS] [FILE]`

---

### 1. (no flag) — basic usage

Opens a file for editing (creates it if it doesn't exist).

```bash
nano config.yaml
```

---

### 2. `-l` (line numbers)

Displays line numbers down the left side of the editor — useful when referencing specific lines, especially in scripts or config files.

```bash
nano -l exploit.py
```

---

### 3. `-c` (constant cursor position)

Keeps a status line at the bottom always showing the current cursor's line/column position, instead of only showing it when you move.

```bash
nano -c script.sh
```

---

### 4. `-m` (mouse support)

Enables mouse support — click to move the cursor, click menu items at the bottom, in terminals that support it.

```bash
nano -m notes.txt
```

---

### 5. `-w` (no line wrapping)

Disables automatic wrapping of long lines — long lines extend off-screen instead of auto-breaking, useful for code or config files where wrapping would visually distort the actual line structure.

```bash
nano -w long_config.conf
```

---

### 6. `-i` (auto-indent)

New lines automatically match the indentation of the previous line — useful when editing code or structured config files.

```bash
nano -i script.py
```

---

### 7. `-t` (tabsize)

Sets how many spaces wide a tab character displays as.

```bash
nano -T 4 Makefile
```

(Note: the actual flag is `-T`, uppercase, taking a number argument — sets tab display width to 4.)

---

### 8. `-B` (backup)

Creates a backup of the original file (with a `~` suffix) before saving changes — a safety net against accidental overwrites.

```bash
nano -B /etc/hosts
```

Saving creates `/etc/hosts~` containing the pre-edit version.

---

### 9. `-v` (view mode / read-only)

Opens the file in view-only mode — you can scroll and search, but can't accidentally modify it. Useful for safely inspecting sensitive or critical files.

```bash
nano -v /var/log/auth.log
```

---

### 10. `+LINE,COLUMN` (jump to position)

Opens the file with the cursor already positioned at a specific line (and optionally column) — useful when a compiler error or `grep -n` told you exactly where to look.

```bash
nano +42 exploit.py
```

Opens the file with the cursor immediately at line 42.

```bash
nano +42,10 exploit.py
```

Line 42, column 10.

---

### 11. `-A` (smart home key)

Makes the Home key behave "smartly" — first press goes to the first non-blank character on the line, second press goes to the true start of the line (column 0). Matches behavior in many modern editors.

---

### 12. `-S` (smooth scrolling)

Scrolls line-by-line instead of jumping a full screen at a time — smoother visual navigation for large files.

---

### 13. `-Y SYNTAX` (syntax highlighting)

Forces a specific syntax-highlighting mode regardless of the file extension — nano auto-detects syntax for known extensions, but this overrides that guess.

```bash
nano -Y python script_without_extension
```

---

### 14. `--nowrap` (long form of `-w`)

```bash
nano --nowrap access.log
```

---

### Essential in-editor shortcuts (not flags, but critical to actually using it)

nano displays its shortcuts at the bottom of the screen (`^` means Ctrl), which is its whole design philosophy — but the core ones worth memorizing:

|Shortcut|Action|
|---|---|
|`Ctrl+O`|Save (write out)|
|`Ctrl+X`|Exit|
|`Ctrl+K`|Cut current line|
|`Ctrl+U`|Paste (uncut)|
|`Ctrl+W`|Search|
|`Ctrl+\`|Search and replace|
|`Ctrl+G`|Show help|
|`Ctrl+_`|Go to specific line number|
|`Alt+U`|Undo|
|`Alt+E`|Redo|

---

### `nano` vs `vim` — when each makes sense

|Situation|Better choice|
|---|---|
|Quick one-off edit, don't want to learn a new editor|`nano`|
|Editing over SSH on a remote/minimal system where you need something reliable and fast|`nano`|
|Heavy scripting/coding work, want powerful editing (macros, multiple registers, plugins)|`vim`|
|Working alongside a team that uses vim keybindings everywhere|worth learning `vim`, but `nano` is fine to start|

There's no wrong answer for casual editing — `nano`'s whole design goal is "no learning curve," which is exactly why it's still the friendliest option for quick config edits during SOC work or CTF scripting.

---

### Practical SOC use cases

```bash
sudo nano -B /etc/ssh/sshd_config
```

Backup flag before editing a critical security config — if your edit breaks SSH, you have `/etc/ssh/sshd_config~` to revert to immediately.

```bash
nano -v /var/log/syslog
```

Read-only mode when inspecting logs you want to browse/search but absolutely don't want to risk accidentally modifying.

### Practical CTF/PWN use cases

```bash
nano +15 exploit.py
```

Jump straight to a known problem line after a Python traceback points you there.

```bash
nano -w payload_notes.txt
```

Keeps long byte-string payloads (shellcode, ROP chain dumps) on single unwrapped lines for easier copy-pasting into scripts.

---


> [!tip]+
> **Relevant to your work:** `nano -B` is a genuinely good habit before editing any critical system config during SOC work (`sshd_config`, `sudoers`, firewall rules) — a one-character typo in a live security config can lock you out or break a service, and having an automatic `~` backup means a fast revert instead of a scramble. For your CTF scripting, `nano +LINE` paired with Python's own traceback line numbers (or `grep -n`) makes for a very fast edit-debug loop without needing a full IDE.
