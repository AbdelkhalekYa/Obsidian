#commands 

## `history` — Display Command History

`history` is a **bash shell builtin** that shows a list of previously executed commands from your current session (and, once saved, from past sessions too, via `~/.bash_history`). It's essential for retracing your own steps, re-running past commands, and — in a security context — for investigating what a user (or attacker with shell access) actually did on a system. Syntax: `history [OPTIONS] [N]`

---

### 1. (no flag) — basic usage

Lists your entire command history with line numbers.

```bash
history
```

Output:

```
  501  cd /var/log
  502  tail -f auth.log
  503  grep "Failed password" auth.log
```

---

### 2. `history N` (show last N entries)

Limits output to just the last N commands — much more usable than scrolling through hundreds of lines.

```bash
history 20
```

Shows only your 20 most recent commands.

---

### 3. `!N` (re-run a specific command by number)

Not a flag of `history` itself, but its direct companion — instantly re-executes the command at that history line number.

```bash
history          # find that command was #503
!503              # re-runs it
```

---

### 4. `!!` (re-run the last command)

Repeats your most recent command exactly — extremely common, especially paired with `sudo`.

```bash
apt update
sudo !!           # re-runs "apt update" with sudo prepended
```

---

### 5. `!STRING` (re-run the most recent command starting with STRING)

Searches backward for the last command that _starts with_ the given text and re-runs it.

```bash
!grep             # re-runs the most recent command that began with "grep"
```

---

### 6. `Ctrl+R` (reverse interactive search — not a flag, but essential)

Interactively searches your history as you type — press `Ctrl+R`, start typing, and it live-matches the most recent matching command. Press `Ctrl+R` again to cycle to older matches, `Enter` to run, `Esc` to cancel.

```
(reverse-i-search)`gdb': gdb -q ./vuln_binary
```

This is genuinely one of the highest-value shell habits to build — far faster than scrolling through `history` output manually for a command you typed 20 minutes ago.

---

### 7. `-c` (clear history)

Deletes the entire history list for the current session.

```bash
history -c
```

**Security-relevant note:** this is exactly what an attacker with shell access often runs to cover their tracks — clearing evidence of what commands they executed. Recognizing this as a red flag (an unexpectedly short or empty `.bash_history` for an account that should show activity) is a real IR indicator.

---

### 8. `-d N` (delete a specific entry)

Removes just one specific line from history by its number, rather than clearing everything.

```bash
history -d 503
```

---

### 9. `-w` (write history to file)

Forces the current session's history to be written out to the history file (`~/.bash_history`) immediately, rather than waiting for the shell to exit normally.

```bash
history -w
```

---

### 10. `-r` (read history from file)

Reads the history file's contents into the current session's history list — useful for merging history from another session/file.

```bash
history -r ~/.bash_history
```

---

### 11. `-a` (append)

Appends the current session's new history lines to the history file, without overwriting existing entries — used internally by bash when multiple terminal sessions are open, to merge histories rather than clobber each other.

```bash
history -a
```

---

### 12. `-n` (read new lines only)

Reads history lines not already read from the history file into the current session — the counterpart to `-a`, used for syncing history across multiple open terminals.

---

### 13. `-p` (print, without executing)

Performs history expansion on the given arguments and prints the result, **without** actually running it — a safe way to preview what a `!` expansion would do before committing to it.

```bash
history -p !!
```

Shows what `!!` would expand to, without executing it — good habit before blindly re-running something with `sudo !!`.

---

### 14. `-s` (append arguments as a new history entry)

Adds the given text to history as if it were a typed command, without actually running it — useful for documentation/scripting purposes.

```bash
history -s "# investigated auth.log at 14:32"
```

---

### Environment variables that control history behavior (not flags, but critical context)

|Variable|Purpose|
|---|---|
|`HISTSIZE`|How many commands are kept in memory during a session|
|`HISTFILESIZE`|How many commands are kept in the history _file_ long-term|
|`HISTFILE`|Path to the history file (default `~/.bash_history`)|
|`HISTCONTROL`|Controls duplicate/space-prefixed command handling (`ignoredups`, `ignorespace`, `ignoreboth`)|
|`HISTTIMEFORMAT`|Adds timestamps to history entries|

```bash
export HISTTIMEFORMAT="%F %T  "
history
```

Output now includes real timestamps:

```
  501  2026-07-20 09:14:03  cd /var/log
  502  2026-07-20 09:14:11  tail -f auth.log
```

**This is genuinely one of the most valuable SOC-relevant settings** — without `HISTTIMEFORMAT`, `history` shows commands with no timing information at all, which is far less useful for reconstructing a timeline of what happened when.

---

### The important security caveat: `HISTCONTROL=ignorespace`

If a user's shell has `ignorespace` set (common default in many configs), any command **prefixed with a space** is deliberately excluded from history. This is a known technique — both legitimate (avoiding logging a command with a password in it) and malicious (an attacker hiding specific commands while leaving the rest of their session visible).

```bash
 rm -rf /evidence    # leading space — won't appear in history if ignorespace is set
```

Worth knowing as an investigator: **`history` output should never be treated as a complete record** — it can be selectively cleared (`-c`), selectively excluded (leading space), or the file itself deleted/edited outside the shell entirely.

---

### Practical SOC/IR use cases

```bash
cat ~/.bash_history
```

Direct file inspection — sometimes more reliable than the `history` builtin, since it reads the persisted file rather than just the current session's in-memory list (though remember: also fakeable/editable by anyone with write access).

```bash
find /home -name ".bash_history" -exec wc -l {} \;
```

Quick check across all user accounts on a box — an unusually short or missing `.bash_history` for an active account is a red flag worth investigating.

```bash
export HISTTIMEFORMAT="%F %T  "; history | tail -50
```

Timestamped recent activity — one of the first things to pull when investigating a specific user session during an incident.

---

### Practical CTF use cases

```bash
history | grep "python3 exploit"
!503                                    # re-run a specific exploit attempt without retyping it
```

Genuinely useful during iterative exploit development — you'll run near-identical commands dozens of times with small tweaks, and `history`/`!N`/`Ctrl+R` save real time over retyping long pwntools invocations.

---


> [!tip]+
> **Relevant to your SOC work:** Treat `.bash_history` as a _useful but untrustworthy_ artifact during investigation — it's easily cleared (`history -c`), selectively hidden (`HISTCONTROL=ignorespace`), or edited directly as a plain text file, so a suspiciously clean history is itself an indicator worth flagging, not proof of innocence. For more reliable command-level auditing on hardened systems, SOC teams typically rely on `auditd` or shell logging solutions (like `snoopy` or session recording) rather than trusting the shell's own self-reported history — worth knowing as a limitation even though `history` is still a useful first-look tool.
