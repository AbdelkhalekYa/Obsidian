#commands 

## `which` — Display an Executable's Location

`which` searches your `$PATH` and prints the full path of the executable that would run if you typed that command name. Unlike the shell builtin `type`, `which` is typically a **separate external program** (though some shells provide it as a builtin/alias too) that only knows about actual files on disk in `$PATH` — it has no visibility into shell builtins, aliases, or functions. Syntax: `which [OPTIONS] COMMAND...`

---

### 1. (no flag) — basic usage

Prints the path of the first matching executable found in `$PATH`.

```bash
which python3
```

Output: `/usr/bin/python3`

```bash
which gdb
```

Output: `/usr/bin/gdb`

---

### 2. `-a` (all)

Lists **every** matching executable found across all directories in `$PATH`, not just the first (winning) one.

```bash
which -a python
```

Output might show:

```
/usr/local/bin/python
/usr/bin/python
```

Useful for spotting version conflicts — e.g., you installed a tool via `pip install --user` or a custom build, and want to confirm which copy actually wins.

---

### 3. `-i` (read aliases from stdin, csh-only historically)

On some `which` implementations (mainly older/BSD-derived), reads alias definitions piped in. Rarely relevant on modern Linux since GNU `which` doesn't know about shell aliases at all — this is actually `which`'s biggest limitation (see below).

---

### 4. `--skip-dot`

Skips directories in `$PATH` that are represented as `.` (current directory) — a security-relevant option, since having `.` in `$PATH` is a classic vector for tricking a user into running a malicious binary from their working directory instead of the real system one.

```bash
which --skip-dot nmap
```

---

### 5. `--skip-tilde`

Skips directories in `$PATH` written with `~` (home directory shorthand) during the search.

```bash
which --skip-tilde ghidra
```

---

### 6. `--skip-alias`

Ignores alias-related entries when searching (relevant mainly on shells/implementations where `which` does check aliases).

---

### 7. `-s` (silent)

No output — just sets the exit status (0 if found, 1+ if not). Ideal for use in scripts/conditionals.

```bash
if which -s docker; then
  echo "Docker found"
else
  echo "Docker not installed"
fi
```

---

### 8. `--tty-only`

Only produces output if running in an interactive terminal — suppresses output when called from a non-interactive script context.

---

### 9. `-v` / `--version`

Prints the `which` version itself.

```bash
which --version
```

---

### The critical limitation: `which` vs `type`

This is the single most important thing to understand about `which` — it **only searches `$PATH` for files**. It cannot see:

- Shell aliases
- Shell functions
- Shell builtins (it will either say "not found" or, on some systems, incorrectly report a path)

```bash
alias grep='grep --color=auto'
which grep     # → /usr/bin/grep   (doesn't mention the alias at all)
type grep      # → grep is aliased to 'grep --color=auto'   (shows the truth)
```

For `cd`, `echo` (as builtin), or any shell builtin/function, `which` often gives a misleading or empty answer, while `type` correctly identifies it. **Rule of thumb: use `type` when you need to know exactly what will execute (especially for security verification); use `which` when you just want the on-disk path of a known external binary.**

---

### Practical CTF/PWN use cases

```bash
which python3 gdb objdump readelf nasm gcc
```

Quick environment sanity check — confirms all your core tools are installed and shows exactly which binary each resolves to, useful when setting up a fresh Ubuntu 22.04 VM for your roadmap.

```bash
which -a gcc
```

If you've installed a specific `gcc` version via a package or built from source, this confirms whether the compiler you _think_ you're using is actually the one that runs.

---

> [!tip]+
> **Relevant to your SOC/security work:** During system triage, `which <suspicious_command>` is a fast first check, but never treat it as authoritative on its own — an attacker who has hijacked `$PATH` order or created a malicious alias can make `which` report a path that looks legitimate while a _different_ thing actually executes. Best practice in IR: run `type -a` and `which -a` together, and separately inspect `echo $PATH` for suspicious entries (like a writable directory or `.` placed early in the search order) — that combination catches the PATH-hijacking and alias-based evasion techniques that `which` alone would miss.
> 
