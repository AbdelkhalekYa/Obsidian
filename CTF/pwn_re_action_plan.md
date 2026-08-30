# PWN & RE — Detailed Action Plan (per skill, per point)

> Same structure as the Skills Map. For every point: **Goal** = the concrete signal that you've actually got it (not "read about it"), **Do** = the specific actions that get you there.

---

## 1. Core Foundations

### x86/x64 assembly
- **Goal:** Read a `objdump -d` listing of an unfamiliar function and narrate what it's doing out loud, without a mnemonic cheat sheet.
- **Do:**
  - Memorize the core set: `mov`, `push`/`pop`, `call`/`ret`, `lea`, `cmp`/`test`, `jmp` family (`je`, `jne`, `jg`, `jle`...)
  - Write 5–10 tiny asm programs by hand with NASM, assemble and run them
  - Every time you see an unfamiliar instruction in a real challenge, look it up once and never look it up again

### Registers & calling convention
- **Goal:** Given any C function signature, immediately know which register holds which argument without checking.
- **Do:**
  - Drill System V AMD64 order: RDI, RSI, RDX, RCX, R8, R9, then stack
  - Compile a few multi-argument C functions with `-O0`, read the prologue in objdump, confirm the mapping yourself
  - Do this until it's reflex, not lookup

### Stack frame mechanics
- **Goal:** From a raw disassembly, draw the stack layout on paper — return address, saved RBP, locals, args — correctly, first try.
- **Do:**
  - Trace 3–5 different functions in GDB, printing `$rsp`/`$rbp` at each step
  - Manually compute offsets of local variables from `rbp`
  - Confirm your paper drawing against `x/20gx $rsp` in GDB

### C memory model
- **Goal:** Explain exactly what byte gets overwritten and why, for a given buffer overflow, before running it.
- **Do:**
  - Write a small vulnerable C program (`gets()`/`strcpy()` into a fixed buffer)
  - Predict the crash before you run it, then confirm in GDB
  - Repeat with different buffer sizes/types until predictions are consistently right

### ELF format
- **Goal:** Given `readelf -a` output on an unfamiliar binary, point to the section that answers "where does execution start" and "where are external function addresses resolved."
- **Do:**
  - Run `readelf -a` and `objdump -d` on 5+ different binaries
  - Identify `.text`, `.data`, `.bss`, `.got`, `.plt`, `.dynsym`, `.dynstr` in each
  - Cross-reference against the ELF man page for anything unclear

### GOT/PLT + dynamic linking
- **Goal:** Explain lazy binding start-to-finish: first call to a libc function vs the second call, and why that difference matters for exploitation.
- **Do:**
  - Watch a GOT/PLT explainer video once, then draw the mechanism from memory
  - Step through a real binary in GDB, breaking on the PLT stub, watching the GOT entry change after first resolution
  - Explain it back to yourself (or write it in Obsidian) without notes

### Linux process basics
- **Goal:** Read an `strace` output and know what every syscall is doing.
- **Do:**
  - Run `strace ./binary` and `ltrace ./binary` on a few simple programs
  - Look up any unfamiliar syscall in `man 2 <syscall>`
  - Understand `execve`, `fork`, `mmap`, `brk` specifically — they show up constantly in PWN

---

## 2. PWN Skill Tree

### Tier 0 — Recon
- **Goal:** Under 2 minutes per binary, you know: arch, protections, stripped or not, and a first guess at bug class.
- **Do:**
  - Run `file`, `checksec`, `strings` on every binary before anything else — make it automatic
  - Build a personal recon checklist/script that runs all three plus `objdump -d main` in one command

### Tier 1 — Stack basics
- **Buffer overflow — Goal:** Crash a binary on purpose and explain exactly which saved value you clobbered.
  - **Do:** Write a vulnerable C program, overflow it with a cyclic pattern, confirm the crash address matches your offset math.
- **Offset finding — Goal:** Find the exact overflow offset for an unfamiliar binary in under a minute using pwntools.
  - **Do:** Practice `cyclic()`/`cyclic_find()` until it's muscle memory; verify manually against GDB once to trust the tool.
- **ret2win — Goal:** Solve ropemporium's ret2win without looking at the writeup.
  - **Do:** Attempt cold first. If stuck >45 min, read the writeup, then immediately redo it from scratch without notes the next day.
- **Stack canaries — Goal:** Explain what a canary blocks and what it doesn't, and identify one in a disassembly.
  - **Do:** Compile the same vulnerable program with and without `-fstack-protector`, diff the disassembly, find the canary check.
- **Shellcode injection — Goal:** Write a working `execve("/bin/sh")` shellcode by hand, then reproduce it with `pwntools.shellcraft`.
  - **Do:** Write it manually first (forces understanding), test byte-by-byte in GDB, then compare to the shellcraft-generated version.

### Tier 2 — Bypassing protections
- **ROP — Goal:** Build a ROP chain from scratch (no template) that calls `system("/bin/sh")` on a NX-enabled binary.
  - **Do:** Find gadgets with `ROPgadget`/`ropper`; solve ropemporium `split` → `callme` → `write4` in order; write the chain by hand before letting pwntools help.
- **ret2libc — Goal:** Write a two-stage exploit (leak, then shell) against a binary with ASLR on, unaided.
  - **Do:** Stage 1: leak a libc address via `puts()`+GOT. Stage 2: compute libc base, find `system()`/`"/bin/sh"`, call it. Practice on 2–3 different binaries until the pattern is automatic.
- **ASLR / info leaks — Goal:** Given any binary with a print primitive, produce a working leak within 15 minutes.
  - **Do:** Practice recognizing leak primitives (any function that echoes memory back to you) across several different challenge styles.
- **libc offset calculation — Goal:** Given a leaked address, correctly identify the libc version and compute the base without external tools, then confirm with `libc-database`.
  - **Do:** Do the subtraction math by hand a few times before relying on automated lookup — you want to trust *why* it works, not just the tool.
- **PIE bypass — Goal:** Exploit a PIE-enabled binary using a partial overwrite or a leaked binary base.
  - **Do:** Read up on partial overwrite technique, then apply it to a real PIE challenge; confirm in GDB that only the intended bytes changed.
- **ret2csu / pivoting — Goal:** Use `__libc_csu_init` gadgets to control a call when your usual `pop rdi; ret` gadget doesn't exist.
  - **Do:** Solve ropemporium `pivot` and `ret2csu`; these are deliberately awkward — expect them to take longer than earlier tiers.

### Tier 3 — Format strings
- **printf bug recognition — Goal:** Spot a format string vuln in source or decompiled code in under 10 seconds.
  - **Do:** Compare `printf(buf)` vs `printf("%s", buf)` side by side until the difference is obviously dangerous on sight.
- **%p / %N$p leaks — Goal:** Manually map out what's on the stack using positional format specifiers.
  - **Do:** Feed a vulnerable binary `%p.%p.%p...` and `%7$p` style inputs, correlate each leaked value to what you'd expect on the stack.
- **%n arbitrary write — Goal:** Overwrite a specific 4-byte memory location (e.g. a GOT entry) using a format string write primitive.
  - **Do:** Work through the ir0nstone format string write walkthrough once, then reproduce the *technique* (not the exact script) on a different binary.
- **GOT overwrite — Goal:** Redirect a function call by overwriting its GOT entry via a format string write.
  - **Do:** Pick a target function GOT entry, overwrite it to point at `system()` or a `win()` function, confirm redirection in GDB before relying on the full exploit.

### Tier 4 — Heap exploitation
- **malloc internals — Goal:** Draw chunk layout (`prev_size`, `size`, `fd`, `bk`) from memory and label a real `x/8gx` heap dump correctly.
  - **Do:** Run `how2heap` examples in GDB with `pwndbg`'s `heap`/`bins`/`vis_heap_chunks` after every malloc/free; draw the state on paper before checking the tool output.
- **Bin types — Goal:** Given a sequence of mallocs/frees, predict which bin a freed chunk lands in.
  - **Do:** Deliberately vary chunk sizes and free order, predict bin placement, verify with `pwndbg bins`.
- **UAF / double-free — Goal:** Explain the exact memory-safety violation and demonstrate it crashing or misbehaving predictably.
  - **Do:** Run the relevant `how2heap` examples; modify them slightly and predict the new behavior before running.
- **Tcache poisoning — Goal:** Get an arbitrary-address allocation via `fd` pointer corruption on a real challenge, unaided.
  - **Do:** Fully complete the `how2heap` tcache_poisoning example in GDB, then find and solve one real CTF heap challenge using the same technique.
- **Fastbin dup — Goal:** Produce two overlapping allocations via a double-free.
  - **Do:** Same approach — `how2heap` example first, then apply to a real challenge from `nightmare`.
- **one_gadget — Goal:** Find a working one_gadget and verify its register constraints are satisfied before using it.
  - **Do:** Run `one_gadget` against a target libc, check constraints in GDB (`info registers` at the point of use), don't fire blind.

### Tier 5 — Odds and ends
- **Integer overflow/underflow — Goal:** Recognize when a size check can be bypassed via integer wraparound.
  - **Do:** Read 1–2 writeups demonstrating this bug class; note the pattern (signed/unsigned mismatch, subtraction near zero).
- **Race conditions / TOCTOU — Goal:** Understand the concept well enough to recognize it if a challenge description hints at it.
  - **Do:** Read a single good explainer; this is low priority unless you hit a challenge that needs it.
- **LD_PRELOAD hooking — Goal:** Use it to bypass a local anti-debug check (e.g. hook `ptrace` to return 0).
  - **Do:** Write a tiny `.so` that overrides `ptrace`, `LD_PRELOAD` it, confirm the anti-debug check is defeated locally.

---

## 3. Reverse Engineering Skill Tree

### Tier 0 — Recon
- **Goal:** Know what you're dealing with (language, packer, stripped, compiler) before opening Ghidra.
- **Do:** Run `file` + `strings` + Detect-It-Easy on every RE challenge as a fixed first step, every time, no exceptions.

### Tier 1 — Static analysis
- **Ghidra navigation — Goal:** Move between Decompiler/Listing/Symbol Tree without hunting for menus.
  - **Do:** Deliberately practice keyboard shortcuts until switching views is instant.
- **Renaming discipline — Goal:** A finished analysis has zero `sub_401234`-style names left in the functions you touched.
  - **Do:** Rename the moment you understand something — don't defer it, you'll forget your own reasoning.
- **Loop pattern recognition — Goal:** Identify `for`/`while`/`do-while` shape in raw disassembly, not just decompiled C.
  - **Do:** Compile a few loop variants yourself, compare source to disassembly side by side.
- **Switch/jump table recovery — Goal:** Reconstruct a switch statement from a jump table in the Listing view.
  - **Do:** Find a real binary with a switch statement, manually trace the jump table before trusting Ghidra's auto-recovery.
- **Struct recovery — Goal:** Define a custom struct in Ghidra from offset-access patterns and apply it, making the decompiler output readable.
  - **Do:** Find repeated `[base+0x8]`, `[base+0x10]`-style accesses, group them, define the struct, re-decompile.
- **Vtable/C++ dispatch — Goal:** Identify a vtable and correctly trace a virtual call to its real target function.
  - **Do:** Practice on one C++ crackme; this is a narrower skill — don't over-invest unless challenges call for it.

### Tier 2 — Dynamic analysis
- **GDB + static cross-check — Goal:** Confirm every static hypothesis with a live breakpoint before writing a solver.
  - **Do:** Make it a rule: no exploit/solver script until you've confirmed behavior live at least once.
- **strace/ltrace — Goal:** Read a trace and immediately know what the program is doing at a high level.
  - **Do:** Run both on unfamiliar binaries as a fast orientation step, especially when static analysis is slow going.

### Tier 3 — Crypto/encoding identification
- **XOR loop spotting — Goal:** Recognize single-byte XOR encoding in decompiled code within seconds.
  - **Do:** Look for a loop with `^=` against a fixed small value or repeating key — practice on a handful of CyberChef "Magic"-solvable samples first to build pattern recognition.
- **Base64 recognition — Goal:** Spot the base64 alphabet table or its use immediately.
  - **Do:** Memorize the alphabet's fingerprint (`ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/`) so it's recognizable even truncated.
- **Crypto magic constants — Goal:** Recognize MD5/SHA/AES by their initialization constants or S-box bytes on sight.
  - **Do:** Keep a personal cheat-sheet of the 3–4 most common constants; check any unfamiliar-looking constant array against it.
- **CyberChef fluency — Goal:** Chain a 3+ step decode recipe (e.g. Base64 → XOR → Hex) without trial and error.
  - **Do:** Practice building recipes manually before relying on "Magic"; understanding *why* a chain works matters more than the auto-detect.

### Tier 4 — Symbolic execution (angr)
- **Core API — Goal:** Write a working `explore()`-based solver from a blank file in under 20 minutes.
  - **Do:** Complete `angr_ctf` levels 0–7 minimum; re-derive a solved level from memory a few days later to check retention.
- **Constraint solving — Goal:** Extract a valid input via `state.posix.dumps(0)` and confirm it actually works against the real binary.
  - **Do:** Always test the angr-derived answer against the real challenge — don't assume the solver is right.
- **Knowing when to use it — Goal:** Correctly decide "manual trace" vs "reach for angr" within the first few minutes of looking at a challenge.
  - **Do:** After each RE challenge, ask yourself in hindsight whether angr would've been faster — build the instinct over time.

### Tier 5 — Advanced RE
- **Custom VM/bytecode — Goal:** Produce a working Python disassembler for an unfamiliar custom instruction set.
  - **Do:** Find the fetch-decode-execute loop (usually a big switch on an opcode byte), map every opcode to its handler, document behavior, then script the disassembler.
- **Packer identification/unpacking — Goal:** Get a packed binary into a state Ghidra can meaningfully analyze.
  - **Do:** Identify the packer with DIE first; try automatic unpacking (`upx -d`) before manual OEP-hunting + memory dump in GDB.
- **Obfuscation (OLLVM, anti-debug) — Goal:** Recognize a control-flow-flattening dispatcher pattern and patch out a `ptrace`-based anti-debug check.
  - **Do:** Read 2–3 writeups on OLLVM flattening to learn the visual pattern before attempting one live; practice NOPing out an anti-debug check in Ghidra.

---

## 4. Shared Skills

### pwntools fluency
- **Goal:** Write a full exploit skeleton (`process()`/`remote()`, packing, sending, receiving) from memory, no docs open.
- **Do:** Rebuild your exploit template from scratch periodically rather than copy-pasting your last one — the goal is recall, not a saved file.

### checksec literacy
- **Goal:** From checksec output alone, name the 2–3 most likely viable techniques before reading anything else.
- **Do:** Practice this explicitly — checksec first, technique guess second, description read third.

### Docker / patchelf
- **Goal:** Get a local exploit working against the *exact* remote libc/loader before ever testing remote.
- **Do:** Keep doing what you're already doing — this habit is already solid per your notes; just make sure it's the very first step on every remote challenge, not an afterthought.

### Documentation habit
- **Goal:** Every solved challenge produces one Obsidian entry: bug class, primitive used, one sentence on what you missed initially.
- **Do:** Write the entry immediately after solving, while the reasoning is still fresh — not at the end of a study session.

---

## How to sequence this

Don't attempt all of this linearly — that's what the old week-by-week plan was for. Instead:

1. Pick **one Core Foundations item** you're least confident in, close it first (these underpin everything).
2. Pick **one PWN tier** and **one RE tier** to focus on in parallel, matched to whatever tripped you up at ASIS.
3. Work the **Goal** for each point as a personal pass/fail check — if you can't hit the goal, that's your next practice target, not a reason to move on.
4. Revisit **Tier 0 recon** habits constantly — they're cheap to build and pay off on every single challenge regardless of category.
