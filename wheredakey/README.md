# Wheredakey — crackmes.one writeup

**Author:** Glitch_Baby  
**Language:** C/C++  
**Platform:** Linux (ELF x86-64)  
**Difficulty:** 1.0  
**Link:** https://crackmes.one/crackme/6ab4ce5195b976f8f1300b54

## Tools used
- Ghidra 12.1.4
- Kali Linux (VirtualBox)

## Analysis

1. Loaded the ELF binary into Ghidra and ran auto-analysis.
2. Opened `Window → Defined Strings` and found the string `"Nuh uh, Study more bruh"` (wrong-password message).
3. Right-clicked → `References → Show References to...` to find the code that uses it.
4. In the Decompiler, located the main function with the following logic:

\`\`\`c
builtin_strncpy(local_7a, "Bash", 4);
builtin_strncpy(local_7a + 4, "Qc3fZ1", 7);
builtin_strncpy(local_7a + 11, "6AjD701x0O", 0xb);
...
strcmp(local_48, s2);
\`\`\`

5. The password is built by concatenating three string literals.

## Password

\`\`\`
BashQc3fZ16AjD701x0O
\`\`\`

## Result

\`\`\`
Enter the Password: BashQc3fZ16AjD701x0O
Nice bruh you got it
\`\`\`

## What I learned
- How to trace string references in Ghidra.
- How to read `strncpy` + `strcat` + `strcmp` logic in Decompiler.
- How to run an ELF binary in Kali.
