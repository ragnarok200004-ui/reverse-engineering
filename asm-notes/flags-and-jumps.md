# Flags, CMP, TEST, Conditional Jumps

## Flags (RFLAGS)

| Flag | Set to 1 when |
|------|---------------|
| ZF | Result = 0 |
| CF | Carry out of MSB (unsigned) |
| SF | MSB = 1 (negative) |
| OF | Signed overflow |

Changed by: ADD, SUB, CMP, TEST.
Not changed by: MOV, LEA, JMP, CALL, RET.

## CMP

\`\`\`asm
CMP RAX, RBX    ; RAX - RBX (result not stored)
\`\`\`

- ZF = 1 if RAX == RBX
- CF = 1 if RAX < RBX (unsigned)
- SF = 1 if result is negative

## TEST

\`\`\`asm
TEST RAX, RAX   ; RAX & RAX
\`\`\`

- ZF = 1 if RAX == 0
- CF = 0, OF = 0 always

## Conditional Jumps

| Command | Condition |
|---------|-----------|
| JZ / JE | ZF = 1 |
| JNZ / JNE | ZF = 0 |
| JC | CF = 1 |
| JNC | CF = 0 |
| JS | SF = 1 |
| JNS | SF = 0 |
| JO | OF = 1 |
| JNO | OF = 0 |

## C to ASM

\`\`\`c
if (a == b)     ->  CMP + JZ
if (a != b)     ->  CMP + JNZ
if (x == 0)     ->  TEST + JZ
if (a < b)      ->  CMP + JC (unsigned) / JL (signed)
\`\`\`

## GDB practice on Wheredakey

Breakpoint at \`call strcmp\` (0x5555555552df), then \`stepi\`:

- \`$rdi\` = "test" (user input)
- \`$rsi\` = "BashQc3fZ16AjD701x0O" (reference)
- \`eax = 0x32\` (50) — strings differ
- \`eflags = 0x202\` — ZF not set
