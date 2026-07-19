#commands 

## `type` — Display a Command's Type

`type` is a **shell builtin** (not a standalone binary) that tells you how the shell will interpret a given name if you type it — as a builtin, alias, function, keyword, or an executable file on disk. It's the go-to tool for answering "what actually runs when I type this?" Syntax: `type [OPTIONS] NAME...`

This matters a lot in security work because attackers (and CTF challenges) sometimes exploit the fact that a command name can resolve to something other than what you expect — a malicious alias, a shadowing function, or a binary earlier in `$PATH`.

---

### 1. (no flag) — basic usage

Shows what kind of command it is and, for executables, the resolved path.

```bash
type ls
```

Output: `ls is aliased to 'ls --color=auto'` (on many distros) — showing you it's _not_ the raw binary you might assume.

```bash
type cd
```

Output: `cd is a shell builtin`

```bash
type python3
```

Output: `python3 is /usr/bin/python3`

---

### 2. `-t` (type only)

Prints just a single word describing the type: `alias`, `keyword`, `function`, `builtin`, or `file`. Ideal for scripting/conditional checks.

```bash
type -t grep
```

Output: `file`

```bash
if [ "$(type -t docker)" = "file" ]; then echo "Docker is installed"; fi
```

Common pattern in install/setup scripts to check whether a tool exists before using it.

---

### 3. `-a` (all)

Shows **every** location/definition that matches the name, not just the one that would actually execute — reveals aliases, functions, and every binary in `$PATH` with that name.

```bash
type -a python
```

Output might show:

```
python is /usr/bin/python
python is /usr/local/bin/python
```

Extremely useful for debugging "wrong version runs" problems, e.g. when you have multiple Python installs and aren't sure which one `python` actually picks up.

---

### 4. `-p` (path)

If NAME is an executable file, prints only its full path (like `which`, but this is the shell's own builtin lookup). Prints nothing if it's a builtin/alias/function.

```bash
type -p nmap
```

Output: `/usr/bin/nmap`

```bash
type -p cd
```

Output: _(nothing — `cd` is a builtin, not a file)_

---

### 5. `-P` (force path search)

Forces a `$PATH` search and prints the path even if NAME is also a shell builtin or function — bypasses the normal precedence.

```bash
type -P echo
```

Output: `/usr/bin/echo` — even though plain `type echo` would say "echo is a shell builtin" (since the builtin normally takes precedence).

---

### 6. `-f` (suppress function lookup)

Skips shell functions when searching — useful if you've defined a function with the same name as a real command and want to see past it.

```bash
type -f ls
```

---

### Why `type` beats `which` for security work

`which` only searches `$PATH` for executable files — it has **no idea** about aliases, shell functions, or builtins, and can lie to you about what will actually run.

```bash
alias ls='rm -rf ~'    # hypothetical malicious alias
which ls                # → /bin/ls   (looks safe!)
type ls                 # → ls is aliased to 'rm -rf ~'   (reveals the truth)
```

This is a classic teaching example of why `which` is untrustworthy for verifying command safety, and `type` (or `type -a`) is the correct tool.

---

### Practical security/SOC use cases

```bash
type -a curl wget nc python3 bash
```

Quick sanity check during triage — confirms exact binary paths and flags any surprising aliases/functions on a system you're investigating (attackers sometimes alias common tools to hide malicious behavior or intercept credentials).

```bash
type sudo
```

Confirms `sudo` resolves to the real binary and not something planted earlier in `$PATH` — relevant to `$PATH` hijacking attacks, a known privilege-escalation technique.

---

> [!tip]+
> **Relevant to your SOC work:** `type -a` is genuinely one of the more underrated commands during incident response — if you suspect an attacker has planted a malicious binary or alias with a legitimate-sounding name (a common persistence/evasion technique), `type -a <command>` shows you every match across `$PATH`, aliases, and functions in one shot, letting you spot the imposter. It's also worth checking `$PATH` itself (`echo $PATH`) alongside `type`, since a manipulated `$PATH` order is what makes a malicious binary get picked over the legitimate one in the first place.
> 
