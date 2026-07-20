#commands 

## `tee` — Read from Stdin, Output to Stdout and Files

`tee` reads from standard input and writes it both to standard output (so it keeps flowing down the pipeline) **and** to one or more files simultaneously — named after a plumbing T-fitting that splits a pipe into two directions. It's the tool you reach for when you want to _see_ output live while also _saving_ it, without running the command twice. Syntax: `COMMAND | tee [OPTIONS] FILE...`

Same honesty note as `help`/`apropos`/`whatis`/`alias`: `tee` is a small, focused tool with only a few real flags. Here's the complete set plus the patterns that make it genuinely useful.

---

### 1. (no flag) — basic usage

Writes input to both the terminal and a file.

```bash
nmap -sV target.com | tee scan_results.txt
```

You see the scan progress live on screen **and** it's saved to `scan_results.txt` simultaneously — no need to run the scan twice or wait until it finishes to redirect it.

---

### 2. `-a` (append)

Appends to the file instead of overwriting it — same distinction as `>>` vs `>` for redirection.

```bash
long_running_scan | tee -a full_log.txt
```

Without `-a`, each run of `tee` would overwrite `full_log.txt` from scratch; with `-a`, results accumulate across multiple runs.

---

### 3. Multiple output files

`tee` can write to several files at once, not just one.

```bash
scan_command | tee results.txt results_backup.txt
```

Both files get identical content — useful for keeping a working copy and an untouched backup simultaneously.

---

### 4. `-i` (ignore interrupts)

Makes `tee` ignore `SIGINT` (Ctrl+C) — so if you interrupt the _upstream_ command, `tee` keeps writing whatever's already buffered/flowing instead of dying immediately, helping ensure output already in the pipe gets fully written to the file.

```bash
long_scan | tee -i output.log
```

---

### 5. `-p` (pipe error diagnostics, GNU extension)

Sets the `SIGPIPE` behavior so `tee` reports a diagnostic if writing to the pipe fails, instead of silently dying — mainly relevant in complex multi-stage pipelines where you want to know if a downstream consumer closed early.

```bash
producer | tee -p log.txt | consumer
```

---

### 6. `--output-error=MODE` (GNU extension)

Controls what happens if `tee` fails to write to one of its output files — options include `warn` (default, print a warning and continue), `warn-nopipe`, `exit`, `exit-nopipe`. Useful in scripts writing to multiple destinations where one might fail (e.g., a full disk or unmounted drive) but you don't want the whole pipeline to die.

```bash
scan | tee --output-error=warn results.txt /mnt/backup/results.txt
```

---

### 7. The core power move: splitting a pipeline into multiple downstream processors

`tee` isn't just for saving to a file — it can feed the _same_ stream into multiple different commands at once, since `tee` itself just writes to stdout too.

```bash
nmap -sV target.com | tee scan.txt | grep "open"
```

Saves the full scan to `scan.txt` **and** simultaneously filters/shows only lines containing "open" on screen — one pass through the data, two uses of it.

---

### 8. Using `tee` with `sudo` to write to protected files

A genuinely useful trick: redirection (`>`) happens in the _shell_, not the command, so `sudo command > /protected/file` fails (the shell, not `sudo command`, tries to open the file, and the shell isn't running as root). `tee` solves this because `tee` itself is the thing being run with elevated privileges.

```bash
echo "new setting" | sudo tee -a /etc/sysctl.conf
```

This works where `echo "new setting" | sudo >> /etc/sysctl.conf` would fail — a very common Linux gotcha that trips up beginners.

Combine with `-a` for appending to protected config files, or without it to overwrite:

```bash
echo "127.0.0.1 blocked-domain.com" | sudo tee -a /etc/hosts
```

---

### 9. Discarding terminal output while still saving (using `/dev/null` as a trick)

```bash
long_command | tee full_output.log > /dev/null
```

Saves everything to the file but suppresses screen output — useful when a command is extremely verbose and you only want the saved record, not the live scroll.

---

### 10. Chaining multiple `tee` calls for multi-stage logging

```bash
scan | tee stage1.txt | grep "vuln" | tee stage2_filtered.txt | wc -l
```

Saves the raw output, saves the filtered output, and still counts matching lines at the end — three checkpoints in one pipeline, each preserved on disk.

---

### `tee` vs plain redirection (`>`) — when each wins

|Situation|Use|
|---|---|
|Just want to save output, don't need to see it live|`command > file.txt`|
|Want to see output live **and** save it|`command \| tee file.txt`|
|Want to save output **and** keep piping it to something else|`command \| tee file.txt \| next_command`|
|Need to write to a root-owned file with `sudo`|`command \| sudo tee file.txt` (not `sudo command > file`)|

---

### Practical SOC use cases

```bash
sudo tcpdump -i eth0 | tee capture_$(date +%F).log
```

Watch live packet capture while simultaneously logging it for later analysis — you don't lose the session if you need to react to something you see in real time.

```bash
nmap -sV -p- target | tee full_scan.txt | grep -i "open\|filtered"
```

Runs a full port scan, saves complete results, and shows only the interesting (open/filtered) lines on screen immediately.

```bash
echo "auditd" | sudo tee -a /etc/modules
```

Common pattern for editing system config files that require root, from a non-root shell session.

---

### Practical CTF/PWN use cases

```bash
python3 exploit.py | tee exploit_run.log
```

Watch your exploit's interactive output live while automatically keeping a saved transcript — useful for later reviewing exactly what happened if the exploit works (or fails) in a way you need to debug.

```bash
./fuzzer | tee fuzz_output.log | grep -i "crash\|segfault"
```

Runs a fuzzing session, logs everything, and surfaces crash indications on screen immediately without losing the full log for post-analysis.

---


> [!tip]+
> **Relevant to your work:** The `sudo tee` pattern is genuinely one of those "everyone hits this wall once" Linux lessons — the first time you try `sudo echo "x" > /etc/somefile` and get a confusing "Permission denied" despite using `sudo`, understanding _why_ (shell redirection happens before `sudo` privileges apply) and reaching for `| sudo tee -a` instead will save you real confusion. For your PWN/SOC work specifically, `tee` is what lets you watch a long-running scan or exploit attempt live without sacrificing the saved log you'll want afterward for a writeup or incident report — a small habit that pays off constantly.
