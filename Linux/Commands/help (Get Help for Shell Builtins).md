#commands 

## `help` — Get Help for Shell Builtins

`help` is a **bash shell builtin** that displays documentation for other builtins and shell keywords (`cd`, `for`, `if`, `type`, `export`, etc.) — things that don't have their own man pages since they're part of the shell itself, not separate executables. Syntax: `help [OPTIONS] [PATTERN]`

**Important honesty note:** unlike `cp`, `mv`, `rm`, or `ln`, `help` is a small builtin with only a handful of real flags — it doesn't have 10+ meaningfully distinct options. I'll give you all of its actual flags in full detail rather than padding the list with filler, since accuracy matters more than hitting a number.

---

### 1. (no flag) — basic usage

Shows a short description and usage synopsis for a builtin.

```bash
help cd
```

Output:

```
cd: cd [-L|[-P [-e]] [-@]] [dir]
    Change the shell working directory.
    ...
```

---

### 2. `PATTERN` with no flags

You can pass any builtin/keyword name directly as the pattern.

```bash
help export
help for
help type
```

Each prints that specific builtin's full help text.

---

### 3. Wildcard pattern matching

`help` supports glob-style patterns to match multiple builtins at once.

```bash
help re*
```

Output lists every builtin starting with `re`: `read`, `readarray`, `readonly`, etc. — useful when you remember only part of a command's name.

---

### 4. `-d` (short description)

Prints only a one-line summary for each matching builtin, instead of the full help text. Great for a quick overview.

```bash
help -d cd
```

Output: `cd - Change the shell working directory.`

```bash
help -d
```

With no pattern, `-d` prints a one-line description for **every** builtin — effectively a full builtin cheat-sheet.

---

### 5. `-m` (man-page style / pseudo-manpage format)

Formats the output like a traditional man page (NAME, SYNOPSIS, DESCRIPTION sections) instead of bash's normal compact help format.

```bash
help -m cd
```

Output:

```
NAME
    cd - Change the shell working directory.

SYNOPSIS
    cd [-L|[-P [-e]] [-@]] [dir]

DESCRIPTION
    Change the current directory to DIR...
```

Useful if you find the standard `man`-page layout easier to read than bash's default terse format.

---

### 6. `-s` (short usage/synopsis only)

Prints just the usage synopsis line — no description, no options list. The most compact output.

```bash
help -s cd
```

Output: `cd: cd [-L|[-P [-e]] [-@]] [dir]`

---

### 7. `help` with no arguments at all

Lists every available builtin with a brief usage line for each — a full index of everything the shell provides natively.

```bash
help
```

---

### `help` vs `man` vs `--help` — knowing which to reach for

|You want docs for...|Use|
|---|---|
|A shell builtin (`cd`, `export`, `type`, `alias`, `for`)|`help <name>`|
|A real external program (`grep`, `nmap`, `gdb`)|`man <name>`|
|Quick flag summary for most CLI tools|`<name> --help`|
|Bash itself as a whole|`man bash` (very long, covers all builtins too)|

Example of the confusion this avoids:

```bash
man cd        # → often fails or redirects to bash's man page, since cd has no standalone man page
help cd       # → works correctly, gives you the builtin's actual documentation
```

---

> [!tip]+
> **Relevant to your work:** `help` isn't something you'll use constantly, but it's genuinely useful while learning bash scripting for automating SOC tasks (log parsing, alert triage scripts) or building CTF exploit/recon scripts — when you hit a builtin like `read`, `trap`, `getopts`, or `local` and forget the exact syntax, `help <name>` is faster than searching online and always matches the exact bash version installed on that machine (important since builtin behavior can vary slightly between bash versions).
> 
