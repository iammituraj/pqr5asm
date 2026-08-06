# Instructions

**Conventions used:**

| Symbol | Meaning |
|--------|---------|
| `rd`   | Destination register |
| `rs1`  | Source register-1 |
| `rs2`  | Source register-2 |
| `imm`  | 12/20-bit immediate |

## Upper-immediate instructions

| No. | Instruction | Syntax | Description |
|-----|-------------|--------|--------------|
| 1 | `LUI` | `LUI rd, imm` | **Load Upper Immediate.** Builds 32-bit constants. Loads 20-bit `imm[19:0]` into the upper 20-bit of `rd`. Loads the lower 12-bit of `rd` with zeroes. eg: `LUI x1, 0xFFF` |
| 2 | `AUIPC` | `AUIPC rd, imm` | **Add Upper Immediate PC.** Builds PC-relative addresses. Forms a 32-bit offset from 20-bit `imm[19:0]` by loading into the upper 20-bit of `rd`, and loading the lower 12-bit with zeroes. Adds this offset to the PC, then places the result in `rd`. |

## Control transfer instructions

| No. | Instruction | Syntax | Description |
|-----|-------------|--------|--------------|
| 3 | `JAL` | `JAL rd, label`<br>OR `JAL rd, imm` | **Jump And Link.** Unconditional jump, used to call subroutines. Stores the next instruction address, `pc+4`, in `rd` for return from subroutine. 20-bit `imm[19:0]` encodes signed offset in multiples of 2 bytes, added to the current pc to get the target address. Unconditional jump range = ±1 MB. |
| 4 | `JALR` | `JALR rd, rs1, offset` | **Jump And Link Register.** Unconditional indirect jump, used to call subroutines. Stores the next instruction address, `pc+4`, in `rd` for return from subroutine. 12-bit `imm[11:0]` encodes signed offset, added to `rs1`, then clears bit 0 of the result to get the target address. Unconditional jump range = ±2 kB (-2048 to +2047). |
| 5 | `BEQ` | `BEQ rs1, rs2, label`<br>OR `BEQ rs1, rs2, imm` | **Branch Equal.** Takes the branch if `rs1 == rs2`. 12-bit `imm[11:0]` encodes signed offset in multiples of 2 bytes, added to the current pc. Conditional branch range = ±4 KB. |
| 6 | `BNE` | `BNE rs1, rs2, label`<br>OR `BNE rs1, rs2, imm` | **Branch Not Equal.** Takes the branch if `rs1 != rs2`. |
| 7 | `BLT` | `BLT rs1, rs2, label`<br>OR `BLT rs1, rs2, imm` | **Branch Less Than.** Takes the branch if `signed(rs1) < signed(rs2)`. |
| 8 | `BGE` | `BGE rs1, rs2, label`<br>OR `BGE rs1, rs2, imm` | **Branch Greater Than or Equal.** Takes the branch if `signed(rs1) >= signed(rs2)`. |
| 9 | `BLTU` | `BLTU rs1, rs2, label`<br>OR `BLTU rs1, rs2, imm` | **Branch Less Than Unsigned.** Takes the branch if `rs1 < rs2`. |
| 10 | `BGEU` | `BGEU rs1, rs2, label`<br>OR `BGEU rs1, rs2, imm` | **Branch Greater Than or Equal Unsigned.** Takes the branch if `rs1 >= rs2`. |

## Load/store instructions

| No. | Instruction | Syntax | Description |
|-----|-------------|--------|--------------|
| 11 | `LB` | `LB rd, rs1, offset` | **Load Byte.** Loads 8-bit data from memory, sign-extends to 32-bit, puts into `rd`. Load address = `rs1 + signed(offset)`, expected to be 8-bit aligned. |
| 12 | `LH` | `LH rd, rs1, offset` | **Load Half-word.** Loads 16-bit data from memory, sign-extends to 32-bit, puts into `rd`. |
| 13 | `LW` | `LW rd, rs1, offset` | **Load Word.** Loads 32-bit data from memory, puts into `rd`. |
| 14 | `LBU` | `LBU rd, rs1, offset` | **Load Byte Unsigned.** Loads 8-bit data from memory, zero-extends to 32-bit, puts into `rd`. |
| 15 | `LHU` | `LHU rd, rs1, offset` | **Load Half-word Unsigned.** Loads 16-bit data from memory, zero-extends to 32-bit, puts into `rd`. |
| 16 | `SB` | `SB rs2, rs1, offset` | **Store Byte.** Stores lower 8-bit of `rs2` in memory. Store address = `rs1 + signed(offset)`, expected to be 8-bit aligned. |
| 17 | `SH` | `SH rs2, rs1, offset` | **Store Half-word.** Stores lower 16-bit of `rs2` in memory. |
| 18 | `SW` | `SW rs2, rs1, offset` | **Store Word.** Stores `rs2` in memory. |

## Integer computation instructions (ALU-I)

| No. | Instruction | Syntax | Description |
|-----|-------------|--------|--------------|
| 19 | `ADDI` | `ADDI rd, rs1, imm` | **Add Immediate.** `rd = rs1 + signed(imm)` (overflow ignored) |
| 20 | `SLTI` | `SLTI rd, rs1, imm` | **Set Less Than Immediate.** `rd = 1` if `signed(rs1) < signed(imm)`, else `0` |
| 21 | `SLTIU` | `SLTIU rd, rs1, imm` | **Set Less Than Immediate Unsigned.** `rd = 1` if `rs1 < signed(imm)`, else `0` |
| 22 | `XORI` | `XORI rd, rs1, imm` | **XOR Immediate.** `rd = rs1 XOR signed(imm)` |
| 23 | `ORI` | `ORI rd, rs1, imm` | **OR Immediate.** `rd = rs1 OR signed(imm)` |
| 24 | `ANDI` | `ANDI rd, rs1, imm` | **AND Immediate.** `rd = rs1 AND signed(imm)` |
| 25 | `SLLI` | `SLLI rd, rs1, shamnt` | **Logical Left Shift Immediate.** `rd = rs1 << shamnt[4:0]` |
| 26 | `SRLI` | `SRLI rd, rs1, shamnt` | **Logical Right Shift Immediate.** `rd = rs1 >> shamnt[4:0]` |
| 27 | `SRAI` | `SRAI rd, rs1, shamnt` | **Arithmetic Right Shift Immediate.** `rd = signed(rs1) >>> shamnt[4:0]` |

## Integer computation instructions (ALU-R)

| No. | Instruction | Syntax | Description |
|-----|-------------|--------|--------------|
| 28 | `ADD` | `ADD rd, rs1, rs2` | **Add.** `rd = rs1 + rs2` (overflow ignored) |
| 29 | `SUB` | `SUB rd, rs1, rs2` | **Subtract.** `rd = rs1 - rs2` (underflow ignored) |
| 30 | `SLL` | `SLL rd, rs1, rs2` | **Logical Left Shift.** `rd = rs1 << rs2[4:0]` |
| 31 | `SLT` | `SLT rd, rs1, rs2` | **Set Less Than.** `rd = 1` if `signed(rs1) < signed(rs2)`, else `0` |
| 32 | `SLTU` | `SLTU rd, rs1, rs2` | **Set Less Than Unsigned.** `rd = 1` if `rs1 < rs2`, else `0` |
| 33 | `XOR` | `XOR rd, rs1, rs2` | **XOR.** `rd = rs1 XOR rs2` |
| 34 | `SRL` | `SRL rd, rs1, rs2` | **Logical Right Shift.** `rd = rs1 >> rs2[4:0]` |
| 35 | `SRA` | `SRA rd, rs1, rs2` | **Arithmetic Right Shift.** `rd = signed(rs1) >>> rs2[4:0]` |
| 36 | `OR` | `OR rd, rs1, rs2` | **OR.** `rd = rs1 OR rs2` |
| 37 | `AND` | `AND rd, rs1, rs2` | **AND.** `rd = rs1 AND rs2` |

## Multiply/divide instructions (RV32M)

| No. | Instruction | Syntax | Description |
|-----|-------------|--------|--------------|
| 38 | `MUL` | `MUL rd, rs1, rs2` | **Multiply.** `rd = (rs1 * rs2)[31:0]` — lower 32 bits of the product. |
| 39 | `MULH` | `MULH rd, rs1, rs2` | **Multiply High (signed x signed).** `rd` = upper 32 bits of the full 64-bit signed product. |
| 40 | `MULHSU` | `MULHSU rd, rs1, rs2` | **Multiply High (signed x unsigned).** `rd` = upper 32 bits of the product, treating `rs1` as signed and `rs2` as unsigned. |
| 41 | `MULHU` | `MULHU rd, rs1, rs2` | **Multiply High (unsigned x unsigned).** `rd` = upper 32 bits of the full 64-bit unsigned product. |
| 42 | `DIV` | `DIV rd, rs1, rs2` | **Divide (signed).** `rd = signed(rs1) / signed(rs2)`. |
| 43 | `DIVU` | `DIVU rd, rs1, rs2` | **Divide (unsigned).** `rd = rs1 / rs2`. |
| 44 | `REM` | `REM rd, rs1, rs2` | **Remainder (signed).** `rd = signed(rs1) % signed(rs2)`. |
| 45 | `REMU` | `REMU rd, rs1, rs2` | **Remainder (unsigned).** `rd = rs1 % rs2`. |

## Pseudo/custom instructions

| No. | Instruction | Syntax | Description |
|-----|-------------|--------|--------------|
| 46 | `MV` | `MV rd, rs1`<br>= `ADDI rd, rs1, 0` | **Move.** `rd = rs1` |
| 47 | `MVI` | `MVI rd, imm`<br>= `ADDI rd, x0, imm` | **Move Immediate** (12-bit immediate). `rd = imm` |
| 48 | `NOP` | `NOP`<br>= `ADDI x0, x0, 0` | **No Operation.** |
| 49 | `J`/`J1` | `J label`<br>= `JAL x0, label`<br>`J1 label`<br>= `JAL x1, label` (saves return address) | **Plain Jump** (short jump). Jump to `label`. |
| 50 | `NOT` | `NOT rd, rs1`<br>= `XORI rd, rs1, -1` | **NOT.** `rd = NOT rs1` |
| 51 | `INV` | `INV rd`<br>= `XORI rd, rd, -1` | **Invert.** `rd = NOT rd` |
| 52 | `SEQZ` | `SEQZ rd, rs1`<br>= `SLTIU rd, rs1, 1` | **Set Equal to Zero.** `rd = 1` if `rs1 == 0`, else `0` |
| 53 | `SNEZ` | `SNEZ rd, rs2`<br>= `SLTU rd, x0, rs2` | **Set Not Equal to Zero.** `rd = 1` if `rs1 != 0`, else `0` |
| 54 | `BEQZ` | `BEQZ rs1, label`<br>= `BEQ rs1, x0, label` | **Branch Equal to Zero.** Jump to `label` if `rs1 == 0`, else no-op. |
| 55 | `BNEZ` | `BNEZ rs1, label`<br>= `BNE rs1, x0, label` | **Branch Not Equal to Zero.** Jump to `label` if `rs1 != 0`, else no-op. |
| 56 | `LI` | `LI rd, imm` **†**<br>= `LUI rd, U` + `ADDI rd, L` | **Load Immediate** (32-bit immediate). `rd = imm` |
| 57 | `LA` | `LA rd, label/symbol` **†**<br>= `LUI rd, U` + `ADDI rd, L`<br>with `-pcrel`:<br>= `AUIPC rd, UA` + `ADDI rd, LA` | **Load Address.** `rd = address(label)`. `AUIPC` is used whenever `-pcrel` is set, whether the reference is a `.text` label or a `.data` symbol. |
| 58 | `JA` | `JA rd, label` **†**<br>= `LUI rd, U` + `ADDI rd, L` + `JALR x0, rd, 0`<br>with `-pcrel`:<br>= `AUIPC rd, UA` + `ADDI rd, LA` + `JALR x0, rd, 0` | **Load and Jump to Address** (long jump). `rd = address(label)`. |
| 59 | `JR` | `JR rs1`<br>= `JALR x0, rs1, 0` | **Jump Register Address.** Jump to address = `rs1`. |
| 60 | `CALL` | `CALL label`<br>= `AUIPC ra, %UA` + `JALR ra, ra, %UL` | **Call subroutine.** Stores the return address in `ra` and jumps to `address(label)`. |
| 61 | `RET` | `RET`<br>= `JALR x0, ra, 0` | **Return from subroutine.** Jumps to the return address in `ra`. |

*Table 3.1: Instructions supported by Assembler*

**†** `U` and `L` are Upper 20-bit `%hi(imm)` and Lower 12-bit `%lo(imm)` values derived from the 32-bit `imm`.
`UA` and `LA` are Upper 20-bit `%pcrel_hi(imm)` and Lower 12-bit `%pcrel_lo(imm)` values derived from the 32-bit `imm`.
