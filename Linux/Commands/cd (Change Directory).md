#commands 

## `cd` — Change Directory

`cd` is a **shell builtin** (not a standalone binary) that changes your shell's current working directory. Syntax: `cd [OPTIONS] [DIRECTORY]`

Same honesty note as `help`/`apropos`/`whatis`/`tee`: `cd` genuinely has very few flags — its usefulness comes almost entirely from special path shortcuts and argument patterns, not options. Here's the complete real set plus the shortcuts that matter far more day-to-day.

---

### 1. (no argument) — go home

Running `cd` with nothing after it takes you straight to your home directory — identical to `cd ~`.

```bash
cd
```

---

### 2. `cd DIRECTORY` — basic usage

Moves into the specified directory, relative or absolute.

```bash
cd /var/log
cd project/src
```

---

### 3. `cd -` (previous directory)

Switches back to the **last directory you were in** before your most recent `cd` — a toggle between two locations. Genuinely one of the most useful shortcuts in daily shell use.

```bash
cd /var/log
cd /etc
cd -        # back to /var/log
cd -        # back to /etc
```

Prints the path it switched to each time, so you always know where you landed.

---

### 4. `cd ~` (home directory)

Explicitly goes to your home directory — same as bare `cd`.

```bash
cd ~
```

---

### 5. `cd ~USERNAME` (another user's home directory)

Jumps to a specific user's home directory (if you have permission to enter it).

```bash
cd ~otheruser
```

---

### 6. `cd ..` (parent directory)

Moves up one directory level.

```bash
cd ..
cd ../..        # up two levels
cd ../sibling_folder    # up one, then into a sibling directory
```

---

### 7. `cd .` (current directory — rarely useful alone)

Stays in place — mostly relevant as a building block in scripts/paths rather than typed alone.

---

### 8. `-L` (logical — follow symlinks, default behavior)

When changing into a path that goes through a symlink, `-L` (the default) keeps the _logical_ path — `$PWD` shows the symlink path you typed, not the real resolved location.

```bash
cd -L /var/log/symlinked_dir
pwd    # shows the symlink path
```

---

### 9. `-P` (physical — resolve symlinks)

Resolves all symlinks in the path, so your new working directory reflects the **actual physical location** on disk, not the symlink path you used to get there.

```bash
cd -P /var/log/symlinked_dir
pwd    # shows the real, resolved path
```

Useful when you need the true filesystem path for scripting, logging, or verifying you're not being misdirected by a symlink (relevant if investigating a system where a symlink might be used to obscure the real file location).

---

### 10. `-e` (used with `-P`, exit on failure)

If `-P` is used and the physical directory can't actually be determined (broken symlink chain, permission issue), `cd -e` forces a non-zero exit status instead of silently succeeding with an unreliable `$PWD`. Mainly relevant in scripts that need strict correctness guarantees.

```bash
cd -Pe /some/path || echo "failed to resolve real path"
```

---

### 11. `@` (used with `-P`, extended attribute directories — macOS/some systems)

Rarely relevant on standard Linux; skip unless you're on a system that uses this feature.

---

### `CDPATH` — an underused shell feature worth knowing

If you set the `CDPATH` environment variable, `cd` will also search those directories automatically when you give a relative name, even if you're not currently inside them.

```bash
export CDPATH=.:~/projects:~/ctf
cd my_project
```

If `my_project` isn't in your current directory but exists under `~/projects`, `cd` finds it automatically — saves typing full paths for frequently-visited project folders.

---

### `pushd` / `popd` — `cd`'s more powerful cousins (not `cd` itself, but directly related)

For navigating between more than two locations (where `cd -` only handles two), bash provides a directory _stack_:

```bash
pushd /var/log       # go there, remember where you came from
pushd /etc            # go there too, stack now has 2 remembered dirs
popd                  # back to /var/log
popd                  # back to original directory
dirs -v               # view the current stack
```

Worth knowing once `cd -` starts feeling limiting — e.g., juggling multiple CTF challenge directories during a competition.

---

### Practical patterns

```bash
cd /opt/ctf/challenge_03 && ls -la
```

Common pattern: `cd` chained with `&&` so the following command only runs if the directory change actually succeeded (protects against typos silently running commands in the wrong place).

```bash
cd "$(dirname "$0")"
```

Common in shell scripts — changes into the directory the _script itself_ lives in, regardless of where it was called from, so relative paths inside the script behave predictably.

---

### Practical SOC/CTF use cases

```bash
cd /var/log && tail -f auth.log
cd -P /suspicious/symlinked_path && pwd
```

`-P` here is genuinely useful during investigation — if an attacker planted a symlink to make a directory _appear_ to be somewhere benign, `cd -P` followed by `pwd` reveals the real underlying path rather than the potentially misleading symlink name.

---

**Relevant to your work:** `cd -` is the shortcut you'll build muscle memory for fastest — constantly bouncing between a challenge directory and your tools directory during CTF work, or between a log directory and a working/scratch directory during SOC triage, is exactly the two-location pattern `cd -` is built for. `cd -P` is a smaller but real security-relevant tool: if you ever suspect a symlink is being used to disguise a file's true location (a legitimate, if uncommon, evasion technique), resolving the physical path removes any ambiguity.

Want the next command?