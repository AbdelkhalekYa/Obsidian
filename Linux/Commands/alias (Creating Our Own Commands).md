#commands #concepts 

## `alias` — Creating Our Own Commands

`alias` is a **shell builtin** that lets you define a shortcut name for a longer command (or command with preset flags). Once defined, typing the alias runs the full command it represents — genuinely one of the highest-value habits for both CTF work and SOC scripting, since it turns long, error-prone commands into short, memorable ones. Syntax: `alias [NAME[=VALUE] ...]`

Same honesty note as `help`/`apropos`/`whatis`: `alias` itself doesn't have a large flag set — its power comes from _how you use it_, not from options. Here's the complete real flag set plus the practical patterns that matter.

---

### 1. (no arguments) — list all current aliases

```bash
alias
```

Prints every alias currently defined in your shell session, in `name='value'` format.

---

### 2. `alias NAME='command'` — basic creation

Defines a new alias. **Single quotes** are recommended so variables inside aren't expanded at definition time.

```bash
alias ll='ls -la'
```

Now typing `ll` runs `ls -la`.

---

### 3. `alias NAME` (single name, no `=`) — look up one alias

Prints the definition of just that one alias, if it exists.

```bash
alias ll
```

Output: `alias ll='ls -la'`

---

### 4. `-p` (print, POSIX-compliant format)

Explicitly prints all aliases in reusable `alias name='value'` format — same as running `alias` with no args, but guarantees the portable/scriptable output format.

```bash
alias -p
```

Useful for redirecting your current aliases into a file:

```bash
alias -p > my_aliases.sh
```

---

### 5. Chaining multiple commands in one alias

```bash
alias update='sudo apt update && sudo apt upgrade -y'
```

Runs both commands in sequence with one word.

---

### 4. Aliases with pipes and complex logic

```bash
alias ports='sudo netstat -tulnp | grep LISTEN'
```

Turns a full recon one-liner into a single word — extremely common in SOC/pentest workflows.

---

### CTF/PWN-specific aliases worth setting up

```bash
alias pwninit='python3 -m pwninit'
alias r2g='r2 -A -d'                          # radare2, auto-analyze + debug mode
alias checksec-all='for f in ./*; do echo "== $f =="; checksec --file="$f"; done'
alias gdbq='gdb -q'                           # quiet GDB, no startup banner
alias pyserv='python3 -m http.server 8000'    # quick file server for exfil/transfer in labs
```

### SOC/security-specific aliases worth setting up

```bash
alias myip='curl -s ifconfig.me'
alias listening='sudo ss -tulnp'
alias watchlogs='sudo tail -f /var/log/auth.log'
alias grepip='grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}"'
```

---

### Removing an alias: `unalias`

Not a flag of `alias` itself, but its direct counterpart — worth knowing together.

```bash
unalias ll        # removes one alias
unalias -a        # removes ALL aliases in the current session
```

---

### Making aliases permanent

By default, aliases only last for your **current shell session** — close the terminal and they're gone. To make them permanent, add them to your shell's startup file:

```bash
echo "alias ll='ls -la'" >> ~/.bashrc
source ~/.bashrc     # reload without restarting the terminal
```

(Use `~/.zshrc` instead if you're on zsh.)

---

### Important gotchas

**1. Aliases don't take parameters like functions do.**

```bash
alias greet='echo Hello'
greet World    # → "Hello World"  (World is just appended as an argument, works here by luck)
```

For anything needing real parameter logic (`$1`, `$2`, conditionals), use a **shell function** instead:

```bash
extract_ip() {
  grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}" "$1"
}
```

**2. Aliases can shadow real commands — a genuine security consideration.** This connects directly back to what we covered with `type`:

```bash
alias ls='rm -rf ~'    # malicious example
type ls                 # reveals: "ls is aliased to 'rm -rf ~'"
which ls                # would misleadingly show /bin/ls — doesn't see the alias
```

This is exactly why, when investigating an unfamiliar or potentially compromised system, checking `alias` output and `type -a <command>` for critical commands is a real IR step — attackers sometimes plant malicious aliases in a victim's `.bashrc` as a persistence/credential-harvesting technique (e.g., aliasing `sudo` or `ssh` to a wrapper that logs input before running the real thing).

**3. Aliases don't expand inside scripts by default.** Aliases are a strictly interactive-shell feature; non-interactive bash scripts ignore them unless you explicitly enable `shopt -s expand_aliases` at the top of the script. For anything you intend to run as a script (not typed live), use a function or a real script file instead.

---


> [!tip]+
> **Relevant to your work:** Building a personal alias set is genuinely one of the best low-effort productivity wins for both your CTF roadmap and your SOC/marketing dual role — e.g., aliasing your most common `checksec`/`gdb`/recon one-liners for PWN work, and log-triage one-liners for SOC work, saves real time over months of repeated typing. On the security side, remember the flip: always audit `~/.bashrc`/`~/.zshrc` for unexpected aliases when triaging a system you don't fully trust — it's a cheap, often-overlooked persistence check.
