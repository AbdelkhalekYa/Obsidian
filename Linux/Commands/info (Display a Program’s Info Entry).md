#commands 

## `info` — Display a Program's Info Entry

`info` is GNU's own documentation system — an alternative to `man` that presents documentation as a **hyperlinked, navigable manual** (similar in spirit to a mini-Wikipedia you browse inside your terminal) rather than one flat page. It's most commonly associated with GNU core tools (`coreutils`, `gcc`, `bash`, `gdb`, `make`) where GNU considers `info` the "primary" documentation and the man page just a condensed summary. Syntax: `info [OPTIONS] [TOPIC]`

---

### 1. (no flag) — basic usage

Opens the info document for a program, starting at its top-level node.

```bash
info ls
```

Opens `ls`'s info manual — often far more detailed than `man ls`, broken into linked sections (menu items) you navigate through.

---

### 2. Navigating within `info` (essential, not a flag, but critical to actually using it)

|Key|Action|
|---|---|
|`Enter` (on a menu item / `*Note` link)|Follow that link|
|`n`|Next node|
|`p`|Previous node|
|`u`|Up one level (back to parent menu)|
|`l`|Last (go back to where you just were, like browser "back")|
|`/pattern`|Search within the document|
|`q`|Quit|

This hyperlinked structure is the whole point of `info` — instead of one long scroll like `man`, related topics are separate linked "nodes" you jump between.

---

### 3. `topic subtopic` (drilling into a specific section)

You can jump directly to a nested section by chaining names.

```bash
info coreutils 'cp invocation'
```

Jumps straight to the `cp`-specific section within the larger coreutils manual, skipping the top-level menu.

---

### 4. `-f` (file)

Specifies an info file directly by name/path rather than looking it up by program name.

```bash
info -f /usr/share/info/gdb.info
```

---

### 5. `-n` (node)

Jumps directly to a specific named node within the document instead of the default top node.

```bash
info -n "Break Commands" gdb
```

Opens GDB's info manual directly at the breakpoint-commands section — skips manually navigating menus to get there.

---

### 6. `-o` (output)

Sends the selected node's text to a file instead of displaying it interactively — useful for extracting a section for later reading or `grep`ing.

```bash
info -o gdb_notes.txt gdb
```

---

### 7. `--subnodes`

When outputting (with `-o`), includes all subnodes recursively rather than just the top node — effectively dumps the entire manual as flat text.

```bash
info --subnodes -o full_gdb_manual.txt gdb
```

---

### 8. `-w` (where / print file location only)

Prints the path to the info file without displaying it — similar in purpose to `man -w`.

```bash
info -w gdb
```

Output: `/usr/share/info/gdb.info.gz`

---

### 9. `--usage`

Prints a short usage summary of the `info` command itself and exits (not the target program's docs — `info`'s own help).

```bash
info --usage
```

---

### 10. `-d` (directory)

Adds an extra directory to search for info files, useful if you've built/installed a tool locally and its info pages aren't in the standard system location.

```bash
info -d ~/local/share/info gdb
```

---

### 11. `--apropos` (keyword search across info docs)

Searches all installed info documents for a keyword — the `info` equivalent of `apropos`/`man -k`.

```bash
info --apropos "breakpoint"
```

---

### `info` vs `man` — when each wins

|Situation|Better choice|
|---|---|
|Quick flag lookup, most tools|`man` (more universal, virtually every tool has one)|
|Deep conceptual understanding of a GNU tool (bash, gcc, gdb, make, coreutils)|`info` (structured, tutorial-like, often more thorough)|
|Tool isn't GNU / doesn't ship info pages|`man` (info may not even exist)|

In practice, most people default to `man` for daily flag-checking and only reach for `info` when they need genuinely deep documentation on a core GNU tool — `info gdb` in particular is excellent and far more thorough than `man gdb`.

---

### Practical CTF/RE use case

```bash
info gdb
```

Then navigate: `Running` → breakpoints, watchpoints, examining memory. GDB's info manual is the authoritative, complete reference for every command and often has more usage detail and examples than `man gdb` or `gdb --help` combined — genuinely worth spending time in in Week 2 of your roadmap when mastering GDB commands.

```bash
info bash
```

The complete bash reference manual — extremely useful once you start writing more advanced exploit-automation or SOC triage scripts and need details on things like parameter expansion, arrays, or trap handling.

---


> [!tip]+
> **Relevant to your work:** `info gdb` is genuinely worth bookmarking for your PWN work — it documents advanced debugging features (like scripting GDB with Python, conditional breakpoints, reverse debugging) more completely than the man page. For SOC scripting, `info bash` is the deepest bash reference available offline, useful once you move past basic one-liners into writing more robust triage/automation scripts.
