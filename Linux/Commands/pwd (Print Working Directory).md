#commands 

## `pwd` — Print Working Directory

`pwd` prints the full path of your current working directory. It exists both as a **shell builtin** (bash's own version) and as a standalone binary (`/bin/pwd`) — normally the builtin runs when you just type `pwd`, unless you explicitly call the binary path. Syntax: `pwd [OPTIONS]`

Same honesty note as `help`/`apropos`/`whatis`/`tee`/`cd`: `pwd` is about as minimal as commands get — it genuinely only has two real flags. I'll give you the complete real set in full detail, plus the context that actually makes it useful, rather than padding a list.

---

### 1. (no flag) — basic usage

Prints the current directory's absolute path.

```bash
pwd
```

Output: `/home/user/ctf/pwn/week8`

---

### 2. `-L` (logical — default behavior)

Prints the path as you navigated to it, **including any symlinks** in the path — i.e., if you `cd`'d through a symlink, `pwd -L` shows that symlink path rather than resolving it. This is the default, so plain `pwd` and `pwd -L` normally give identical output.

```bash
cd /var/log/symlinked_app_logs
pwd -L
```

Output: `/var/log/symlinked_app_logs` (the symlink path, not where it actually points)

---

### 3. `-P` (physical — resolve symlinks)

Prints the **real, resolved physical path**, following every symlink in the chain to show where you actually are on disk.

```bash
cd /var/log/symlinked_app_logs
pwd -P
```

Output: `/opt/apps/myapp/logs` (the true underlying location)

This directly parallels the `cd -L`/`cd -P` distinction covered earlier — `pwd -P` is how you confirm the _actual_ physical location after navigating through a symlink, which matters if you suspect a symlink is being used to disguise a file's real location.

---

### Builtin vs binary — a subtle but real distinction

```bash
type pwd
```

Output: `pwd is a shell builtin`

```bash
/bin/pwd --help
```

The standalone binary version (`/bin/pwd` or `/usr/bin/pwd`) supports the same `-L`/`-P` flags, plus standard `--help`/`--version`. The bash builtin version is what runs by default and behaves almost identically for everyday use — the distinction rarely matters in practice, but it's worth knowing they're technically two different implementations (this connects back to the `type` vs `which` lesson: `which pwd` might report `/bin/pwd`, but `type pwd` correctly shows the builtin actually takes precedence).

---

### `$PWD` and `$OLDPWD` — the environment variables behind the scenes

Bash automatically maintains these as you `cd` around, and `pwd` (the builtin) typically just reads `$PWD` rather than re-querying the filesystem each time:

```bash
echo $PWD        # current directory (same as running pwd)
echo $OLDPWD     # your previous directory (what `cd -` jumps back to)
```

---

### Practical combos and patterns

```bash
echo "Currently in: $(pwd)"
```

Embedding `pwd`'s output in a message — extremely common in scripts for logging/debugging where you are during execution.

```bash
cd "$(dirname "$0")" && pwd
```

Confirms a script correctly navigated to its own directory — a useful sanity check pattern in setup/install scripts.

```bash
export PROJECT_ROOT=$(pwd)
```

Captures the current directory as a variable early in a script, so later steps can reliably reference it even after `cd`-ing elsewhere.

---

### Practical CTF/SOC use cases

```bash
pwd -P
```

Confirms your _true_ location on disk before running a destructive command (`rm -rf`, `find -delete`) — genuinely worth doing as a habit before any risky operation, especially if you've been navigating through symlinks and aren't 100% sure where you physically are.

```bash
find / -type l -exec sh -c 'echo "{}"; ls -la "{}"' \; 2>/dev/null | grep -B1 "pwd\|/etc\|/root"
```

(more of a `find`+`ls` combo, but the underlying investigative goal — confirming real vs. apparent location — is exactly what `pwd -P` answers for your _current_ shell position specifically.)

---


> [!tip]+
> **Relevant to your work:** `pwd` itself is simple, but `pwd -P` earns a place in your habits for the same reason `cd -P` does — if you're ever navigating through directories during an investigation or before a destructive CTF/lab operation and a symlink might be involved, confirming the _physical_ path removes any doubt about where you actually are before you act. Otherwise, `pwd`'s main value is just the constant, reflexive "where am I right now" check that underlies almost every terminal session.
