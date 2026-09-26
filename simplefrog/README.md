# Simple Frog — crackmes.one writeup

**Author:** Glitch_Baby  
**Language:** C/C++  
**Platform:** Linux (ELF x86-64)  
**Difficulty:** 3.0  
**Link:** https://crackmes.one/crackme/6aae098bcab6678aefe9de31

## Tools used
- Ghidra 12.1.4
- GDB
- Kali Linux (VirtualBox)

## Analysis

1. Loaded the ELF binary into Ghidra and ran auto-analysis.
2. The binary uses heavy obfuscation: `entry` calls a dummy function (`FUN_002015c0`) that only does `syscall` in a loop, then crashes with `invalidInstructionException`.
3. Found the real check inside `FUN_002015c0` by looking for the success string `"Croak! Correct serial.\n"`.
4. Identified the core cryptographic function `FUN_00202650`, which takes three arguments:
   - `param_1` — input buffer (or reference key)
   - `param_2` — salt/constant
   - `param_3` — output buffer (16 bytes)
5. The function runs three loops:
   - Initialization (0x17..0x3f)
   - Main mixing (5..0x65)
   - Output generation (0..0x300) — produces a bit array (0s and 1s)
6. The check is performed after two calls to `FUN_00202650`:
   - First call at `0x201fed` — generates reference bit array from a constant.
   - Second call at `0x202404` — generates bit array from user input.
7. The comparison is done at `0x202586`:
   ```asm
   CMP BPL, 0x1
   JNZ wrong
   TEST ECX, ECX
   JNZ wrong
   Success requires BPL == 1 and ECX == 0.

GDB session
Breakpoint at 0x201fed (first call):

text
break *0x201fed
run
At the breakpoint, registers were:

text
rdi = 0x7fffffffd6d0  (buffer with constant)
rsi = 0x7df9f0e6cdf88cc5  (salt)
rdx = 0x7fffffffd760  (output buffer)
After stepping over the call (breakpoint at 0x201ff2), the output buffer contained a 16-byte bit array:

text
0x7fffffffd760: 0x00 0x01 0x00 0x00 0x00 0x01 0x01 0x00
0x7fffffffd768: 0x01 0x01 0x01 0x01 0x00 0x00 0x01 0x00
Second call at 0x202404 — input buffer was empty (the program had not yet read the serial). At the check (0x202586), registers were:

text
bpl = 0x0
ecx = 0xff
The check failed.

Result
The crackme is not fully solved. The algorithm of FUN_00202650 is complex (custom mixing with XOR, multiplications, rotations). Full solution requires either:

Reversing the algorithm and writing a keygen.

Patching the binary (not a valid solution for crackmes.one).

What I learned
Working with obfuscated binaries when Ghidra's decompiler fails.

Using GDB to inspect runtime buffers and registers.

Setting breakpoints on calls and after them.

Not every crackme is solvable in one session — but every session adds experience.
