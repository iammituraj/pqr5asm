# General Info

`pqr5asm` is an assembler which translates RISC-V assembly to binary/hex code.

| Field          | Value                                                                     |
|----------------|----------------------------------------------------------------------------|
| ISA compliance | RV32IM (User-Level ISA v2.2) — 45 base instructions + pseudo/custom instructions |
| Input          | Assembly program with `.s` extension                                     |
| Output         | Binary/Hex code for program & data in ASCII text and `.bin` formats:<br>`sample_imem.bin` – Program binary<br>`sample_imem_bin.txt` – Binary text file of program, human readable<br>`sample_imem_hex.txt` – Hex text file of program, human readable<br>`sample_dmem.bin` – Data binary<br>`sample_dmem_bin.txt` – Binary text file of data, human readable<br>`sample_dmem_hex.txt` – Hex text file of data, human readable |

## Syntax rules

1. One instruction per line.
2. Every program requires the program section, `.section .text`, and the base address of the program should be defined by the linker directive, `.org`, for eg:
   ```
   .section .text
   .org 0x00000000
   ```
3. Supports `<space>`, `<comma>`, and `<linebreak>` as delimiters, for eg:
   ```
   LUI x5 255
   LUI x5, 255
   ```
4. Use `#` for inline/newline comments, for eg:
   ```
   LUI x5, 255  # This is a sample comment
   ```
5. Supports 32-bit signed/unsigned integer, `0x` hex literals for immediate.
   For eg: `255`, `0xFF`, `-255`

   Immediates support parenthesis format for instructions with immediate offset.
   ```
   addi x1, x0, 2 <=> addi x1, 2(x0)
   ```
   Immediate gets truncated to 20-bit or 12-bit based on instruction.
6. Registers support different ABI names which are case-insensitive.
7. `%hi()` and `%lo()` are assembly functions which can be used to extract the most significant 20 bits and least significant 12 bits from a 32-bit symbol. This is useful to generate a 32-bit address for load/store operations, or load a 32-bit constant to a register. For eg:
   ```
   # a4 <-load- from myvar in memory
   LUI a5, %hi(myvar)
   LW  a4, %lo(myvar)(a5)

   # a4 -store-> to myvar2 in memory
   LUI a5, %hi(myvar2)
   SW  a4, %lo(myvar2)(a5)

   # x1 <-load- "0xdeadbeef"
   LUI x1, %hi(0xdeadbeef)
   ADDI x1, x1, %lo(0xdeadbeef)
   ```
8. `%pcrel_hi()` and `%pcrel_lo()` are similar to `%hi()` and `%lo()`, but the value returned is PC-relative rather than absolute. The pair is used together to call subroutines anywhere across the addressing space. For eg:
   ```
   # ra <-load- return address and jump to myfunc()
   AUIPC ra, %pcrel_hi(myfunc)
   JALR  ra, %pcrel_lo(myfunc)(ra)
   ```
   These two functions are expected to be used together always, since `%pcrel_lo()` uses its counterpart `%pcrel_hi()` as the reference to generate the PC-relative address.
9. Supports labels for jump/branch instructions:
   - Label is recommended to be of max. 16 ASCII characters
   - Label should be stand-alone in a new line, for eg:
     ```
     FIBONACC:
         mvi x1, 1
     ```
   - Label is case-sensitive.
10. Supports ASCII characters in the immediate values for instructions like `MVI`, `LI`.
    For eg: `MVI x0, 'A'` — equivalent to `MVI x0, 0x41`
    Supports all 7-bit ASCII characters from `0x20` to `0x7E`, `\n`, `\r`, `\t`.

## Invoking the assembler

```
python pqr5asm.py -file=<assembly source file path> -pcrel
```

`-pcrel`: Applying this flag uses PC-relative addressing for instructions `LA`, `JA`. This helps in generating relocatable binary code. If this flag is not used, absolute address is loaded by the instructions, and the generated binary code may not be relocatable.

## Assembler flow

- Assembly code file → validate all sections, linker directives → initial formatting →
- Pre-processing: decode all labels and symbols and resolve all addresses → resolve all assembly functions and immediates → final formatting →
- Parse instructions line-by-line → dump binary code files on successful compilation.

!!! note "This manual is generated from source, not a static PDF"
    Earlier versions of this manual shipped only as `pqr5asm_imanual.pdf`. That file is a snapshot — if the assembler changes and nobody remembers to re-export the PDF, the docs quietly go stale (this happened: the PDF still says RV32I / 37 instructions after RV32M support was added). These pages are markdown living next to the source, built fresh on every push — see [Changelog](changelog.md) for what's new.
