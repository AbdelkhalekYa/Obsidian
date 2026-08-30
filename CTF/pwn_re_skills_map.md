# PWN & Reverse Engineering — Skills, Tools & Concepts Map

> Use this as a checklist, not a syllabus. Pick a challenge → get stuck → find the relevant section here → learn that one piece → go back to the challenge → read the writeup after. That loop teaches faster than reading top-to-bottom.

---

## 1. Core Foundations (you need these under everything else)

These aren't optional for either track — a shaky foundation here is usually the *real* reason a challenge feels impossible.

| Skill | What "solid" looks like |
|---|---|
| x86/x64 assembly | You can read a disassembly listing and mentally execute it without a cheat sheet |
| Registers & calling convention | You know RDI/RSI/RDX/RCX/R8/R9 argument order (System V AMD64) cold |
| Stack frame mechanics | You can draw, from memory, what's on the stack for any function: return address, saved RBP, locals, args |
| C memory model | You understand pointers, arrays vs pointers, struct layout, and how a buffer overflow physically overwrites adjacent memory |
| ELF format | You know what `.text`, `.data`, `.bss`, `.got`, `.plt`, `.dynsym` are and why they exist |
| GOT/PLT + dynamic linking | You can explain lazy binding and why it matters for exploitation |
| Linux process basics | Syscalls, `/proc`, how a process is loaded, what `execve` does |

**If any of these feel shaky**, that's the highest-leverage place to spend time before grinding more challenges — everything else builds on this.

---

## 2. PWN Skill Tree (roughly easy → hard)

### Tier 0 — Recon (do this on *every* binary before touching an exploit)
- `file`, `checksec` — architecture, NX, PIE, RELRO, canary, stripped?
- `strings`, `objdump -d`, `readelf -a`
- Run it locally, feed it garbage input, see what breaks

### Tier 1 — Stack basics
- **Buffer overflow** — overwriting a return address
- **Offset finding** — `pwntools cyclic()` / `cyclic_find()`
- **ret2win** — redirect execution to an existing "win" function
- **Stack canaries** — what they are, how they're checked, how leaks defeat them
- **Shellcode injection** — writing/using shellcode when NX is off

### Tier 2 — Bypassing protections
- **ROP (Return-Oriented Programming)** — chaining gadgets to do more than jump once
  - Finding gadgets: `ROPgadget`, `ropper`
  - Classic chain: `pop rdi; ret` → arg → `system()`
- **ret2libc** — calling existing libc functions instead of injecting code (bypasses NX)
- **ASLR** — why addresses are randomized, and why you need a **leak** before you can use any address
- **Info leaks** — using a format string, a `puts()` call, or any read primitive to leak a libc/binary address
- **libc offset calculation** — leaked address − known symbol offset = libc base
- **PIE bypass** — same idea as ASLR but for the binary itself; partial overwrites, leaking the binary base
- **ret2csu / stack pivoting** — advanced gadget tricks when you don't have the gadgets you want

### Tier 3 — Format strings
- `printf(user_input)` vs `printf("%s", user_input)` — the actual bug
- `%p` / `%N$p` — leaking stack/arbitrary values
- `%n` / `%hn` / `%hhn` — **arbitrary write** primitives
- Classic target: overwrite a GOT entry to redirect a function call

### Tier 4 — Heap exploitation (the big one — budget real time here)
- **malloc internals**: chunk structure (`prev_size`, `size`, `fd`, `bk`), how `malloc`/`free` actually work
- **Bins**: tcache, fastbin, smallbin, unsorted bin — what goes where and why
- **Use-after-free (UAF)** — using a pointer after `free()`
- **Double-free** — freeing the same chunk twice
- **Tcache poisoning** — corrupting the `fd` pointer to allocate at an arbitrary address (the modern glibc go-to)
- **Fastbin dup** — the classic double-free technique
- **`one_gadget`** — finding "one shot" shell gadgets in libc, and checking their register constraints in GDB

### Tier 5 — Odds and ends worth knowing exist
- Integer overflows/underflows as a bug class
- Race conditions / TOCTOU
- `LD_PRELOAD` hooking (useful for bypassing anti-debug locally)
- Kernel/pwn (usually out of scope unless a CTF specifically has a kernel category)

---

## 3. Reverse Engineering Skill Tree

### Tier 0 — Recon
- `file`, `strings`, **Detect-It-Easy (DIE)** — run this *before* opening a decompiler; it often tells you what you're dealing with (packer, compiler, language, crypto library) in seconds
- Stripped vs not stripped — changes your whole approach

### Tier 1 — Static analysis
- Navigating **Ghidra**: Decompiler, Listing, Symbol Tree, Functions window
- Renaming variables/functions as you understand them — RE *is* documentation
- Reading decompiler output as rough C, not raw assembly
- Recognizing loop patterns (`for`/`while`/`do-while`) in disassembly
- Recovering `switch` statements from jump tables
- Recovering structs from offset-based field access patterns
- Identifying C++ vtables / virtual dispatch

### Tier 2 — Dynamic analysis
- GDB stepping alongside static reading — confirms your hypothesis instead of guessing
- `strace` / `ltrace` — watching syscalls and library calls live
- Setting breakpoints at the exact comparison/validation logic

### Tier 3 — Crypto & encoding identification
- Spotting single-byte XOR loops
- Recognizing base64 tables
- Recognizing crypto magic constants (MD5: `0x67452301`, AES S-box bytes, etc.)
- **CyberChef** — chaining decode operations (Base64 → XOR → Hex, etc.), and its "Magic" auto-detect

### Tier 4 — Symbolic execution
- **angr**: `project`, `factory`, `simulation_manager`
- `explore(find=..., avoid=...)` to reach a target without manually solving the logic
- Extracting the answer: `state.posix.dumps(0)`
- Knowing *when* to reach for angr: many branches, no obvious pattern → don't manually trace 40 conditions

### Tier 5 — Advanced RE
- **Custom VMs / bytecode**: finding the fetch-decode-execute loop, mapping opcodes to handlers, writing a mini disassembler for the custom ISA
- **Packers**: identifying (DIE), unpacking (`upx -d`, or manual OEP-finding + memory dump in GDB)
- **Obfuscation**: OLLVM control-flow flattening (dispatcher-block pattern), anti-debug bypass (`ptrace` checks, patching them out)

---

## 4. Shared Skills (both tracks lean on these constantly)

- **pwntools** — not just PWN. `ELF()`, packing (`p64`/`u64`), `process()`/`remote()` are used everywhere
- **checksec literacy** — reading protections tells you what techniques are even possible
- **Docker / patchelf** — matching the exact remote libc so your local exploit works remotely too (you already do this — keep it up)
- **Writing things down as you go** — the habit of documenting *why* something worked, not just that it worked, is what turns one solve into a repeatable skill

---

## 5. Tool Reference

### PWN
| Tool | Purpose |
|---|---|
| `pwntools` | Python exploit framework — the backbone of every script |
| GDB + `pwndbg` | Dynamic analysis, heap visualization (`heap`, `bins`, `vis_heap_chunks`) |
| `ROPgadget` / `ropper` | Find ROP gadgets |
| `one_gadget` | Find magic shell gadgets in libc |
| `checksec` | Binary protection recon |
| `libc-database` | Identify remote libc from a leaked address |
| `patchelf` | Point a binary at a specific libc/loader locally |
| `strace` / `ltrace` | Trace syscalls / library calls |
| Docker | Replicate the exact remote environment |

### Reverse Engineering
| Tool | Purpose |
|---|---|
| Ghidra | Primary free disassembler + decompiler |
| `angr` | Symbolic execution |
| Detect-It-Easy (DIE) | Identify packers/compilers/crypto before anything else |
| CyberChef | Multi-layer decode/encode |
| `radare2` | Scriptable CLI disassembler (optional, good for automation) |
| `binwalk` | Firmware/embedded file extraction |
| `upx` | Unpack UPX-packed binaries |
| `strings` / `objdump` | First-look recon on any binary |

---

## 6. Quick Concept Glossary

- **NX/DEP** — marks memory non-executable; stops raw shellcode injection on the stack
- **ASLR** — randomizes memory addresses; defeated by leaks
- **PIE** — ASLR applied to the binary itself, not just libraries
- **Stack canary** — a value checked before return to detect stack overflows
- **RELRO** — restricts GOT writability (Partial vs Full)
- **GOT/PLT** — the mechanism that resolves function addresses lazily at runtime; a classic overwrite target
- **Gadget** — a short instruction sequence ending in `ret`, chained together in ROP
- **Chunk** — a unit of heap memory with metadata (`size`, `fd`, `bk`) tracked by malloc
- **Tcache** — per-thread cache of recently freed chunks (glibc 2.26+), the most commonly abused heap structure today

---

## 7. Practice-First Loop (how to actually use this now)

1. **Pick one weak area** from what tripped you up at ASIS.
2. **Find 2–3 matching challenges** at an appropriate difficulty:
   - `ropemporium.com` — best for stack/ROP fundamentals, one technique per challenge
   - `crackmes.one` — RE, filterable by difficulty/arch
   - `pwn.college` — guided, dojo-style, good when you need scaffolding
   - `guyinatuxedo.github.io` (nightmare repo) — organized by technique, comes with writeups already attached
3. **Timebox the attempt** (60–90 min). If stuck, that's fine — that's the signal for what to study.
4. **Read the writeup or watch a video** (John Hammond, LiveOverflow) *after* attempting — not before. Compare your approach to theirs.
5. **Log the technique** in your Obsidian vault: what the bug class was, what tool/primitive solved it, one sentence on what you missed.
6. **Repeat**, rotating weak areas so you're not just getting good at one thing.

This is a deliberate shift from the week-by-week roadmap: instead of front-loading theory, you're now pulling theory in exactly when a real challenge demands it — which tends to stick better and mirrors how CTF actually works under time pressure.
