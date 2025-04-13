

# UNGA BUNGA Console

## Bunga -- 8 CPU

|                    |                      |
|--------------------|----------------------|
| CPU Name           | Bunga -- 8           |
| Data Width         | 8 Bit                |
| Address Width      | 16 Bit               |
| Addressable Memory | 64KB                 |
| Endian             | Little Endian        |
| Pipeline           | 3 Stage (IF, ID, EXE)|
| Bus Type           | Von Neuman           |

---

## Register Set

| Name | Code | Size | Description       |
|------|------|------|-------------------|
| R0   | 0000 | 16   | GPR               |
| R1   | 0001 | 16   | GPR               |
| R2   | 0010 | 16   | GPR               |
| R3   | 0011 | 16   | GPR               |
| R4   | 0100 | 16   | GPR               |
| R5   | 0101 | 16   | GPR               |
| R6   | 0110 | 16   | GPR               |
| R7   | 0111 | 16   | GPR               |
| A    | 1000 | 16   | Accumulator       |
| SP   | 1001 | 16   | Stack Pointer     |
| PC   | 1010 | 16   | Program Counter   |
| FL   | 1011 | 8    | Flag (Z, C, N, V) |
| IO0  | 1100 | 16   | GPIO              |
| IO1  | 1101 | 16   | I/O port addr     |
| IO2  | 1110 | 16   | GPU/PPU control   |
| IO3  | 1111 | 16   | Audio Output      |

---

## Memory Map

| Range           | Size | Description     |
|-----------------|------|-----------------|
| 0x0000-0x1FFF   | 8KB  | ROM             |
| 0x2000-0x3FFF   | 8KB  | RAM             |
| 0x4000-0x5FFF   | 8KB  | VRAM            |
| 0x6000-0x6FFF   | 4KB  | I/O Ports       |
| 0x7000-0x7FFF   | 4KB  | Free            |
| 0x8000-0xFFFF   | 32KB | Secondary ROM   |

---

## Flags

| Code | Name | Description       |
|------|------|-------------------|
| 000  | Z    | Zero              |
| 001  | C    | Carry             |
| 010  | N    | Negative (MSB)    |
| 011  | V    | Overflow          |
| 100  | I    | Interrupt Enable  |
| 101  | -    | For future use    |
| 110  | -    | For future use    |
| 111  | -    | For future use    |

---

# Instruction Set Architecture (ISA)

There are three classes of instructions:

1. 1-Byte
2. 2-Byte
3. 3-Byte

Each class has its own encoding scheme to maximize bit efficiency.

---

### 1-Byte Instructions

- Contains opcode and an optional flag or register.
- 5-bit opcode, 3-bit register (lower bits only → R0–R7).

#### Format:

OP Rn => [opcode][mode/reg_id] 5b 3b


#### Example:

INC R0 => 00100 000


---

### 2-Byte Instructions

- Used for register-register operations.
- 5-bit opcode, 3-bit mode, 4-bit destination, 4-bit source.

#### Format:

OP Rd, Rs => [opcode][mode][Rd][Rs] 5b 3b 4b 4b


#### Example:

ADD R0, R1 => 00111 000 0000 0001


> 📝 Register-immediate operations **not supported** in 2-byte format due to reg field limits. Use 3-byte instead.

#### Indirect memory access:

LDR R0, [R1]


Uses the same encoding as register-register.

---

## IO Specific Instructions

Special `IOGET` and `IOPUT` instructions replace standard `INN` and `OUT`.

- I/O space: `0x6000 – 0x6FFF` (12-bit addressing)
- Modes: direct (3B), indirect (2B)

### Indirect Example:

IOGET Rn, [IOn] => [opcode][mode][gp_reg_id][io_reg_id] 5b 3b 4b 3b


---

### 3-Byte Instructions

Most complex but powerful.

#### Memory Access Format:

OP Rn, [0x1234] => [opcode][reg_id][bin_12][bin_34] 5b 3b 8b 8b


#### Example:

LOAD R0, [0x250F] => 10001 000 00101001 00001111


---

#### Multi-load example:

LDRMI R0-R7, [PC], #0x8


Loads 8 blocks into R0-R7 from address in PC (auto-increment by 2B).

---

#### Immediate ALU Operation

ADDI R0, #0x123


#### Encoding:

ADDI R0, #0x123 => 11000 000 0000 000100100011


---

### 3-Byte IO Direct Addressing

IOGET Rn, [0x6123] => [opcode][mode][gp_reg_id][IO_mem_addr] 5b 3b 4b 12b


#### Example:

IOGET R0, [0x6123] => 11100 000 0000 0110000100100011
