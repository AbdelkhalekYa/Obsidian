#commands 

## `file` — Determine File Type

`file` identifies a file's actual type by examining its content (magic bytes/signatures), not its extension or name — meaning it can't be fooled by a misleading filename the way `ls` or a file manager might be. Extremely important in both CTF/RE work and SOC/forensics, since attackers routinely rename malicious files to look benign. Syntax: `file [OPTIONS] FILE...`

---

### 1. (no flag) — basic usage

Prints a human-readable description of the file's type.

```bash
file mystery_file
```

Output: `mystery_file: ELF 64-bit LSB executable, x86-64, dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, ...`

---

### 2. Multiple files at once

```bash
file *.bin
```

Output:

```
challenge1.bin: ELF 64-bit LSB executable, x86-64, statically linked
challenge2.bin: PE32+ executable (console) x86-64, for MS Windows
notes.bin:      ASCII text
```

Instantly tells you what you're actually dealing with across an entire challenge folder before opening anything.

---

### 3. `-b` (brief — no filename prefix)

Omits the filename from the output, printing just the type description — useful in scripts or when piping into other commands.

```bash
file -b suspicious.exe
```

Output: `PE32 executable (GUI) Intel 80386, for MS Windows`

---

### 4. `-i` / `--mime` (MIME type output)

Prints the MIME type instead of the human-readable description — useful for scripting, web-related checks, or matching against expected content types.

```bash
file -i uploaded_file
```

Output: `uploaded_file: application/pdf; charset=binary`

Genuinely important in a security context: if a file is named `invoice.pdf` but `file -i` reports `application/x-dosexec`, that mismatch between claimed and actual type is a strong red flag (classic malicious-attachment disguise).

---

### 5. `--mime-type` (MIME type only, no charset)

Narrower version of `-i` — just the MIME type, no charset info.

```bash
file --mime-type -b document.docx
```

Output: `application/vnd.openxmlformats-officedocument.wordprocessingml.document`

---

### 6. `--mime-encoding` (character encoding only)

Shows just the detected text encoding (useful for text files: `utf-8`, `us-ascii`, `binary`, etc.)

```bash
file --mime-encoding config.txt
```

---

### 7. `-z` (look inside compressed files)

Peers inside compressed/archive files and reports on the content within, not just "it's a gzip file."

```bash
file -z archive.tar.gz
```

Output: `archive.tar.gz: gzip compressed data... (was: POSIX tar archive)` — tells you it's a tarball inside the gzip, not just a generic compressed blob.

---

### 8. `-k` (keep going — show all matching types)

Normally `file` stops at the first strong match; `-k` continues checking and reports every type signature that matches, even weaker ones. Useful for ambiguous or crafted files (relevant in CTF steganography/polyglot challenges).

```bash
file -k polyglot_challenge.bin
```

Might reveal that a file matches both "PNG image" and "ZIP archive" signatures simultaneously — a classic CTF file-polyglot trick where one file is valid as two different formats depending on which tool reads it.

---

### 9. `-s` (special files — read block/character devices)

By default `file` won't try to read special device files (to avoid hanging on things like `/dev/sda`); `-s` forces it to attempt reading them anyway.

```bash
file -s /dev/sda1
```

Useful during disk/partition forensics to identify filesystem type directly from a device node.

---

### 10. `-L` (dereference symlinks)

By default, `file` reports on the symlink itself in some contexts; `-L` follows the link and reports on the type of the file it actually points to.

```bash
file -L suspicious_symlink
```

---

### 11. `-f LISTFILE` (read filenames from a list file)

Reads a list of files to check from a file (one path per line) instead of passing them as arguments — useful for batch-checking a large number of files gathered by another tool (like `find`).

```bash
find /tmp -type f > /tmp/filelist.txt
file -f /tmp/filelist.txt
```

---

### 12. `-p` (preserve timestamps)

Doesn't update the access time of files it examines — relevant in forensic contexts where you don't want your own investigation to alter file metadata (atime) that might be part of the evidence.

```bash
file -p evidence_sample.bin
```

---

### 13. `-r` (raw — don't translate unprintable characters)

Prints unprintable/non-ASCII characters in the output as-is instead of escaping them — rarely needed but occasionally relevant when the type description itself contains unusual bytes.

---

### 14. `-e TEST` (exclude a specific test)

Skips a particular detection test (`ascii`, `encoding`, `tokens`, `cdf`, `compress`, `elf`, `soft`, `apptype`) — useful to speed up scans or avoid a specific test that's giving false results on unusual files.

```bash
file -e compress large_binary
```

Skips compression-detection, useful if you know it's not compressed and want a faster/more direct result.

---

### 15. `-C` (compile a custom magic file)

Compiles a custom magic-signature file into `file`'s binary database format — for advanced use when you need to teach `file` to recognize a custom/proprietary file format (e.g., a CTF's custom file type).

```bash
file -C -m custom.magic
```

---

### 16. `-m FILE` (use a custom magic file)

Uses a specified magic database instead of (or in addition to) the system default — pairs with `-C` above, or can point directly at a `.magic` source file.

```bash
file -m /path/to/custom.magic unknown_file
```

---

### `file` vs relying on the file extension — the core lesson

This is the entire point of the tool: **never trust a filename's extension.**

```bash
mv malware.exe totally_safe.jpg
file totally_safe.jpg
```

Output: `totally_safe.jpg: PE32 executable (GUI) Intel 80386, for MS Windows` — `file` sees straight through the rename because it reads the actual magic bytes at the start of the file, not the name.

---

### Practical CTF/RE use cases

```bash
file ./challenge
```

The very first command run on almost any CTF binary — instantly tells you architecture (x86/x64/ARM), whether it's statically or dynamically linked, stripped or not, and OS target — informing your entire approach before opening Ghidra or GDB.

```bash
file -k stego_challenge.png
```

Checks for polyglot signatures — common in forensics/misc CTF categories where a PNG might have appended ZIP data or similar tricks.

```bash
for f in *; do file "$f"; done
```

Quick type-audit of every file in a challenge folder before triaging.

---

### Practical SOC use cases

```bash
file -i uploaded_attachment
```

Verifies actual content type against claimed extension — core check in email/upload security analysis.

```bash
find /tmp /var/tmp -type f -exec file {} \; 2>/dev/null
```

Type-audits everything in common malware-drop directories — quickly flags an executable disguised with a `.txt` or `.jpg` extension.

```bash
file -z suspicious_archive.zip
```

Peeks inside an archive's contents type without fully extracting it first.

---


> [!tip]+
> **Relevant to your work:** `file` is genuinely one of the first two or three commands you run on _anything_ unfamiliar in both your PWN/RE world and SOC world — in CTF, `file ./binary` sets your entire analysis strategy (architecture, static/dynamic, stripped/not); in SOC, `file -i` catching a mismatch between a file's claimed extension and its real content type is one of the fastest, lowest-effort ways to catch a disguised malicious attachment before it's even opened. Pair it with `-z` and `-k` when something feels off — those two flags are specifically built for the "this file might not be what it appears to be" scenario that comes up constantly in both disciplines.
