
# 🕹️ UNGA BUNGA Console

A homebrew 8-bit fantasy console with a custom CPU architecture — **BUNGA–8**. Designed for learning and experimentation, it mimics retro-style hardware with modern HDL techniques.

---

## ⚙️ BUNGA–8 CPU Overview

| Field               | Value                  |
|---------------------|------------------------|
| **CPU Name**        | BUNGA–8                |
| **Data Width**      | 8-bit                  |
| **Address Width**   | 16-bit                 |
| **Memory Size**     | 64KB                   |
| **Endian**          | Little Endian          |
| **Pipeline**        | 3-stage (IF, ID, EXE)  |
| **Bus Type**        | Von Neumann            |

---

## 🧠 Register Set

| Name | Code  | Size | Description         |
|------|-------|------|---------------------|
| R0–R7 | 0000–0111 | 8  | General Purpose Registers |
| A    | 1000  | 8    | Accumulator         |
| SP   | 1001  | 16   | Stack Pointer       |
| PC   | 1010  | 16   | Program Counter     |
| FL   | 1011  | 8    | Flag Register (Z,C,N,V) |
| IO0–IO3 | 1100–1111 | 8 | I/O Registers (GPIO, Audio, etc.) |

---

## 🗺️ Memory Map

| Range         | Size  | Description       |
|---------------|-------|-------------------|
| 0x0000–0x1FFF | 8KB   | ROM               |
| 0x2000–0x3FFF | 8KB   | RAM               |
| 0x4000–0x5FFF | 8KB   | Video RAM         |
| 0x6000–0x6FFF | 4KB   | I/O Ports         |
| 0x7000–0x7FFF | 4KB   | Free              |
| 0x8000–0xFFFF | 32KB  | Secondary ROM     |

---

## 🚩 Flags

| Bit | Name | Description        |
|-----|------|--------------------|
| 000 | Z    | Zero               |
| 001 | C    | Carry              |
| 010 | N    | Negative (MSB)     |
| 011 | V    | Overflow           |
| 100 | I    | Interrupt Enable   |
| 101–111 | - | Reserved          |

---

## 🧾 Instruction Classes

- **1-Byte**: Simple operations (e.g., `NOP`, `INC Rn`)
- **2-Byte**: Register-to-register or short immediate ops
- **3-Byte**: Memory, I/O, and immediate addressing

---

## 🔡 Encoding Summary

### 1-Byte Instruction

```
OP Rn => [opcode:5b][reg:3b]
```

Example: `INC R0 => 00100 000`

### 2-Byte Instruction

```
OP Rd, Rs => [opcode:5b][mode:3b][Rd:4b][Rs:4b]
```

Example: `ADD R0, R1 => 00111 000 0000 0001`

### 3-Byte Instruction

```
OP Rn, [0x1234] => [opcode:5b][reg:3b][addr:16b]
```

Example: `LOAD R0, [0x250F] => 10001 000 00101001 00001111`

---

## 🧃 IO Instructions

- **Indirect (2B)**: `IOGET Rn, [IOn] => [opcode][mode][gp_reg][io_reg]`
- **Direct (3B)**: `IOGET Rn, [0x6123] => [opcode][mode][gp_reg][addr:12b]`

---

## 🧮 Sample Opcodes

### 1-Byte

| Opcode | Mnemonic | Description           |
|--------|----------|-----------------------|
| 00000  | NOP      | No operation          |
| 00001  | RET      | Return from subroutine|

### 2-Byte

| Opcode | Mnemonic | Mode | Description         |
|--------|----------|------|---------------------|
| 00010  | SETF     | imm8 | Load flags          |
| 00100  | CMP      | reg  | Compare and set flags|
| 01000  | ADD      | reg/imm | Add               |

### 3-Byte

| Opcode | Mnemonic | Description           |
|--------|----------|-----------------------|
| 10000  | LDR      | Load from memory      |
| 10010  | IOGET    | Read from IO          |
| 10011  | IOPUT    | Write to IO           |

---

## 🧩 Architecture Overview

- **Register File**: 8 GPRs + ACC, SP, PC, FL, IO0–IO3
- **ALU**: 16-bit capable, flag-aware
- **Decoder**: Supports 1B, 2B, 3B instruction formats
- **Memory Interface**: Unified data/instruction bus
- **Control Unit**: Hardcoded FSM (for now)

---

## 📌 Notes

- Avoid 3-byte instructions unless necessary (slowest)
- Use `LDRMI`, `STRMI` for efficient block transfer
- ISA includes planned space for future opcodes (0b10111–0b11111)

---

## 📣 Coming Soon

- Microcoded control logic
- Instruction pipeline optimization
- Fantasy game demo ROM

---

## 🧑‍💻 Author

**Venator158**  
[GitHub](https://github.com/venator158)

---

Inspired by retro consoles, built for modern HDL tinkerers!
