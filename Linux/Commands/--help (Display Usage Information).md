#commands 

## `--help` — Display Usage Information

`--help` isn't a standalone command — it's a **near-universal convention** that most CLI programs (GNU/Linux utilities especially) implement as an option to print a quick usage summary and exit, without needing a man page or internet access. Syntax: `COMMAND --help`

Same honesty note as before: since `--help` itself is just a convention rather than a command with its own flag set, there aren't "10+ flags of `--help`." Instead, here's a thorough breakdown of how the convention works, its variations, and how to use it effectively — which is the genuinely useful thing to understand.

---

### 1. Standard GNU-style usage

Most GNU coreutils and many other tools follow this exact pattern.

```bash
ls --help
```

Output: usage synopsis, a list of all flags with short descriptions, and often a link to the full documentation at the bottom.

---

### 2. Short-flag equivalent: `-h`

Many (not all) tools also accept `-h` as shorthand for `--help`. **Caution:** on some tools `-h` means something else entirely (e.g., `-h` means "human-readable sizes" in `df`, `du`, `ls -lh`), so don't assume it's always help.

```bash
grep -h    # NOT help — suppresses filename prefix in output
grep --help # actual help
```

Always double-check with `--help` (the long form) if you're unsure, since it's far less likely to be overloaded for something else.

---

### 3. Combining with `grep` to search for a specific flag

A very common real-world pattern — when a `--help` output is long, pipe it through `grep` to find just what you need.

```bash
curl --help | grep -i proxy
```

Filters curl's huge help output down to just proxy-related options.

---

### 4. `--help=TOPIC` (some tools, e.g. `gcc`)

A few complex tools support a scoped help — showing only options related to a category.

```bash
gcc --help=optimizers
```

Shows only GCC's optimization-related flags instead of the full (huge) flag list.

---

### 5. Piping into `less` for long output

```bash
nmap --help | less
```

Since many tools (like `nmap`, `openssl`, `ffmpeg`) have very long `--help` output, piping into a pager lets you scroll/search instead of it flying past in the terminal.

---

### 6. `--help` on tools without it

Not every program implements `--help` — some are older, minimalist, or from a different tradition (BSD tools, some proprietary binaries). If it's unsupported, you'll usually see:

```bash
some_old_tool --help
# → invalid option -- '-'
# or: unrecognized option '--help'
```

In that case, fall back to `man some_old_tool` or `some_old_tool -h`.

---

### `--help` vs `man` vs `help` — the full comparison

|Tool|Best for|Works offline?|
|---|---|---|
|`COMMAND --help`|Quick flag reference, external programs|Yes|
|`man COMMAND`|Full detailed documentation, external programs|Yes|
|`help COMMAND`|Shell builtins only (`cd`, `export`, `for`)|Yes|
|`info COMMAND`|Very detailed docs for some GNU tools|Yes|
|`tldr COMMAND` (if installed)|Practical examples, community-maintained|Yes|

---

### Practical CTF/security use cases

```bash
objdump --help | grep -A2 "\-d"
python3 -c "import angr" 2>/dev/null || echo "angr not installed"
gdb --help
ROPgadget --help
one_gadget --help
```

When you're learning a new binary analysis tool mid-CTF and don't have time to read the full man page, `--help` piped through `grep` for the flag category you need (e.g., `--help | grep -i format` on `objdump` to find disassembly-format flags) is usually the fastest path to the answer.

---

> [!tip]+
> **Relevant to your work:** During SOC triage or CTF work, `--help` is often your first move on any unfamiliar binary you encounter — including ones found on a system during an investigation (with appropriate caution: running `--help` on a suspicious binary is generally low-risk since it usually just prints text and exits, but be mindful in a live IR scenario that even this can trigger unwanted behavior on a malicious binary — sandbox/isolate first if you suspect it could be adversary tooling rather than a legitimate tool).
> 
