# Sections in the program

## Text and data segments

Every assembly program is formatted as text and data sections. The text section, `.section .text`, encapsulates all the instructions. This forms the text segment of the program. The data section, `.section .data`, encapsulates all the symbols stored in the data memory. This forms the data segment of the program. Text section is mandatory in a program, while data section is not necessary. **The data section should be defined before the text section.**

```
.section .data   # Data segment
<data symbols>

.section .text   # Text segment
<instructions>
```

## Linker directives

Linker directives are used to map the different segments of a program to memory.

The directive `.org <addr>` is used to map the text and data sections. The `addr` is the base address of the segment to which the first instruction/data symbol is mapped. This directive is mandatory for any section and it should be 4-byte aligned. The directive should be defined immediately following the section. For eg:

```
.section .data
.org 0x40000000       # Data of the program is stored from this location
student:               # Data symbol student, addr = 0x40000000
.byte 1                # Data @(addr + 0) = 0x01, size = 1 byte
.word 95                # Data @(addr + 4) = 95, size = 4 bytes
.string "John Doe"      # String "John Doe" stored @(addr + 8), size = 9 bytes
.ascii '\n'              # Data @(addr + 17) = '\n', size = 1 byte

.section .text
.org 0x00000000        # First instr of the program is stored in this location
<instructions>
```

If the assembler is configured to generate a relocatable program binary, the text and data segments can be loaded to a different base address than the one set by the linker directive, as long as both segments are relocated together by the same offset.

The directive `.p2align <alignment>` is used to force the alignment of a data symbol to `2^(alignment)` bytes. This is useful to make the memory access efficient by making the data align with the native alignment of the processor. This directive should be defined immediately following the symbol declaration. For eg:

```
.section .data
.org 0x40000000        # Data of the program is stored from this location
city:                    # Data symbol city, addr = 0x40000000
.string "London"         # String "London" stored @(addr + 0), size = 7 bytes
student:                 # Data symbol student, addr = 0x40000007
.p2align 2               # Align student to 4 bytes by padding 1 zero byte
                          # The addr of student becomes 0x40000008
.byte 1                  # Data @(addr + 0) = 0x01, size = 1 byte
.word 95                  # Data @(addr + 4) = 95, size = 4 bytes
.string "John Doe"         # String "John Doe" stored @(addr + 8), size = 9 bytes
.ascii '\n'                 # Data @(addr + 17) = '\n', size = 1 byte
```

## Labels

Labels are used in the program to mark specific points in the code for reference purposes, making the code easier to maintain. They serve as a way to identify locations within a program, often for branching, looping, or jumping purposes. For eg:

```
.section .text    # Text segment
.org 0x00000000    # First instr of the program is stored in this location

START:              # Start of a program
<instruction 1>
```

## Symbols

Variables used in the program are stored in the data memory. They are referred to by the program instructions using symbols. The symbol represents the base address of the variable. A symbol can have a set of contiguous data under it of different data types.

| Data type | Usage | Description |
|-----------|-------|--------------|
| `.byte`   | `.byte 0xFF`<br>`.byte 255` | Byte, size = 1 byte. Supports initializing with unsigned/signed integer, hex value |
| `.hword`  | `.hword 0xABCD` | Naturally-aligned half-word, size = 2 bytes. Supports initializing with unsigned/signed integer, hex value |
| `.word`   | `.word 0xABCDEF01` | Naturally-aligned word, size = 4 bytes. Supports initializing with unsigned/signed integer, hex value |
| `.ascii`  | `.ascii 'A'` | Char byte, size = 1 byte. Supports initializing with a single ASCII character enclosed within single quotes. Supports all 7-bit ASCII characters from `0x20` to `0x7E`, `\n`, `\r`, `\t`. |
| `.string` | `.string "Hello"` | Null-terminated string, size = `<string size> + 1` byte. Supports initializing with ASCII characters enclosed within double quotes. Supports all 7-bit ASCII characters from `0x20` to `0x7E`, `\n`, `\r`, `\t`. |
| `.zero`   | `.zero 4` | Appends the specified number of zero bytes. |

## BSS segment

BSS segment is not directly supported. The user can however emulate BSS segment variables in a program by defining uninitialized symbols under the data section and explicitly initializing them to zero using the `.zero` keyword. For eg:

```
myvar:
.zero 4   # myvar is a symbol in memory of size 4 bytes & initialized to 0
```

However, this will take up space in the generated binary.

## Stack and other segments

The user should explicitly load the stack pointer if required, and other memory pointers with a start-up code. Currently, there are no assembly/linker directives to support these segments.
