# Registers

Following registers are supported by the ISA and assembler ABI.

| Register Name | ABI Name | Description                       |
|----------------|----------|------------------------------------|
| `x0`           | `zero`   | Hard-wired Zero                    |
| `x1`           | `ra`     | Return Address                     |
| `x2`           | `sp`     | Stack Pointer                      |
| `x3`           | `gp`     | Global Pointer                     |
| `x4`           | `tp`     | Thread Pointer                     |
| `x5`–`x7`      | `t0`–`t2`| Temporary Registers                |
| `x8`           | `s0`/`fp`| Saved Register/Frame Pointer       |
| `x9`           | `s1`     | Saved Register                     |
| `x10`–`x11`    | `a0`–`a1`| Function Arg/Return Val Registers  |
| `x12`–`x17`    | `a2`–`a7`| Function Arg Registers             |
| `x18`–`x27`    | `s2`–`s11`| Saved Registers                   |
| `x28`–`x31`    | `t3`–`t6`| Temporary Registers                |

*Table 2.1: Registers with ABI acronyms*

Register names are case-insensitive, so `x5`, `X5`, `t0`, and `T0` are all equivalent.
