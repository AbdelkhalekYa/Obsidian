#commands 

## `man` — Display a Program's Manual Page

`man` displays the full manual page for commands, system calls, library functions, configuration files, and more — the traditional, most authoritative offline documentation source on Unix/Linux systems. Syntax: `man [OPTIONS] [SECTION] PAGE_NAME`

---

### 1. (no flag) — basic usage

Opens the manual page for the given command, using a pager (usually `less`).

```bash
man ls
```

Opens `ls`'s full manual — description, all options, examples, related commands.

---

### 2. Section numbers (not a flag, but essential context)

Man pages are organized into numbered sections. The same name can exist in multiple sections (e.g., `printf` the shell command vs. `printf` the C function), so specifying the section disambiguates.

```bash
man 1 printf     # Section 1: user commands
man 3 printf     # Section 3: C library functions
man 5 passwd     # Section 5: file formats (/etc/passwd structure)
man 8 iptables   # Section 8: system administration commands
```

Common sections: **1** = user commands, **2** = system calls, **3** = library functions, **5** = file formats, **7** = miscellaneous/conventions, **8** = admin/root commands.

---

### 3. `-k` (keyword search / same as `apropos`)

Searches all man page names and short descriptions for a keyword — useful when you don't know the exact command name.

```bash
man -k "list directory"
```

Output: lists every man page whose description mentions that phrase, e.g. `ls (1) - list directory contents`.

---

### 4. `-f` (whatis — short description only, same as `whatis` command)

Prints a one-line description for an exact name match, across all sections.

```bash
man -f printf
```

Output:

```
printf (1)  - format and print data
printf (3)  - formatted output conversion
```

Fast way to see which sections a name exists in before picking one.

---

### 5. `-a` (all matching pages)

Displays every man page matching the name across all sections, one after another (press `q` to move to the next).

```bash
man -a printf
```

Shows section 1's `printf`, then after you quit, section 3's `printf`.

---

### 6. `-w` (where / location only)

Prints the file path of the man page instead of displaying it — useful for scripting or checking if a page exists and where it's installed from.

```bash
man -w gdb
```

Output: `/usr/share/man/man1/gdb.1.gz`

---

### 7. `-P` (pager)

Overrides the default pager (`less`) with a different one for that invocation.

```bash
man -P cat ls
```

Dumps the whole man page directly to stdout via `cat` instead of opening an interactive pager — handy for piping into `grep` or a file.

---

### 8. `-l` (local file)

Interprets the argument as a literal filesystem path to a man page file, rather than searching the man database — useful for previewing a man page you're writing or one that isn't installed system-wide.

```bash
man -l ./mytool.1
```

---

### 9. `-S` (section list / restrict search order)

Limits or reorders which sections `man` searches through, colon-separated.

```bash
man -S 3:2 printf
```

Searches section 3 first, falls back to section 2 if not found there.

---

### 10. `-C` (config file)

Uses an alternate `man.conf` configuration file instead of the system default — relevant if you maintain a custom man page setup (e.g., for locally built tools).

```bash
man -C ~/.man.conf mytool
```

---

### 11. `--all` (long form of `-a`)

Same as `-a` above, spelled out.

```bash
man --all ssh
```

---

### 12. `-c` (copy-in / catman mode reformatting)

Reformats even if a cached (preformatted) version exists — forces regeneration. Rarely needed manually; mainly relevant to system man-db maintenance.

---

### 13. `-M` (manpath)

Overrides the search path used to look up man pages — specify a custom directory containing man pages (e.g., for tools installed outside standard system paths).

```bash
man -M /opt/mytool/man mytool
```

---

### Searching _within_ an open man page

Once inside `man` (which uses `less` as the pager), these keys matter as much as any flag:

|Key|Action|
|---|---|
|`/pattern`|Search forward for text|
|`n`|Jump to next match|
|`N`|Jump to previous match|
|`q`|Quit|
|`g` / `G`|Jump to top / bottom|

```bash
man objdump
# then inside: /disassemble
```

This search-inside-pager workflow is often more useful in practice than any command-line flag, since man pages for tools like `objdump`, `gdb`, or `openssl` are very long.

---

### Practical CTF/RE use cases

```bash
man 5 elf          # ELF file format reference — directly relevant to your Week 3 ELF study
man 2 ptrace       # ptrace syscall — how debuggers and anti-debug checks work
man 7 signal       # signal handling — relevant to crash analysis
man objdump
```

`man 5 elf` in particular is one of the best primary references for understanding ELF headers, program headers, and section headers — directly useful alongside `readelf -a` output during your roadmap's Week 3.

---


> [!tip]+
> **Relevant to your work:** `man -k` (or `apropos`) is genuinely useful in both SOC and CTF contexts when you half-remember a tool's purpose but not its exact name — e.g., `man -k "packet capture"` will surface `tcpdump`, `tshark`, etc. For deep binary/RE work, `man 2` (syscalls) and `man 5` (file formats like ELF) are the two sections you'll return to most, since they're the authoritative low-level references behind tools like `strace`, `readelf`, and `objdump`.
