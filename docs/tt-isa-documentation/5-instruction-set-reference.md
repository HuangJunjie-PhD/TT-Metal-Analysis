---
project: tt-isa-documentation
github: https://github.com/tenstorrent/tt-isa-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-isa-documentation/5-instruction-set-reference
---

# Instruction Set Reference

Relevant source files
*   [BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1)
*   [Diagrams/Out/Bits32_MOVB2A.svg](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Out/Bits32_MOVB2A.svg)
*   [Miscellaneous/FMA/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/README.md?plain=1)
*   [Miscellaneous/FMA/fma.c](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/fma.c)
*   [WormholeB0/EthernetTile/BabyRISCV/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/EthernetTile/BabyRISCV/README.md?plain=1)
*   [WormholeB0/TensixTile/BabyRISCV/Mailboxes.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/Mailboxes.md?plain=1)
*   [WormholeB0/TensixTile/BabyRISCV/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1)
*   [WormholeB0/TensixTile/PIC.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/PIC.md?plain=1)
*   [WormholeB0/TensixTile/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/MOVB2A.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOVB2A.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/RWCs.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/RWCs.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1)

This page provides a comprehensive reference for all instruction sets in the Tenstorrent Wormhole B0 and Blackhole A0 architectures. The ISA consists of:

1.   **RV32IM Base Set** - Standard RISC-V integer and multiply/divide instructions executed by all five Baby RISC-V cores
2.   **Tensix Coprocessor Instructions** - 32-bit fixed-length instructions pushed to specialized compute units via memory-mapped instruction buffers
3.   **Configuration Instructions** - Instructions that modify backend configuration arrays and control unit behavior

All Tensix coprocessor instructions use 32-bit fixed-length encoding with an 8-bit opcode in bits [31:24]. The instruction format definitions are located in [Diagrams/Src/Bits32.lua 1-1700](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L1-L1700) which generates SVG diagrams of instruction bit layouts.

For detailed instruction semantics and usage examples, see the sub-pages linked in the navigation section at the end of this page.

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 1-119](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L1-L119)[Diagrams/Src/Bits32.lua 1-1700](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L1-L1700)

* * *

## Instruction Categories and Issuing Cores

Instructions are issued by Baby RISC-V cores via MMIO writes to memory-mapped instruction buffers. Each core has access to different instruction buffer addresses based on its role.

### Instruction Buffer Memory Map

| RISC-V Core | Instruction Buffer Address | Target Execution Unit | Access |
| --- | --- | --- | --- |
| **RISC-V B** | `0xFFE4_0000` (`INSTRN_BUF_BASE`) | T0 MOP Expander (post-expansion) | Write |
| **RISC-V B** | `0xFFE5_0000` (`INSTRN1_BUF_BASE`) | T1 MOP Expander (post-expansion) | Write |
| **RISC-V B** | `0xFFE6_0000` (`INSTRN2_BUF_BASE`) | T2 MOP Expander (post-expansion) | Write |
| **RISC-V T0** | `0xFFE4_0000` (`INSTRN_BUF_BASE`) | T0 Frontend (pre-expansion) | Write |
| **RISC-V T1** | `0xFFE4_0000` (`INSTRN_BUF_BASE`) | T1 Frontend (pre-expansion) | Write |
| **RISC-V T2** | `0xFFE4_0000` (`INSTRN_BUF_BASE`) | T2 Frontend (pre-expansion) | Write |
| **RISC-V T0/T1/T2** | `0xFFB8_0000` (`TENSIX_MOP_CFG_BASE`) | MOP Expander Configuration | Read/Write |
| **RISC-V B/T0/T1/T2** | `0xFFEF_0000` (`TENSIX_CFG_BASE`) | Backend Configuration | Read/Write |
| **RISC-V T0/T1/T2** | `0xFFE8_0020` | Tensix Semaphores | Read/Write |

### Instruction Category to Execution Unit Mapping

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 79-105](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L79-L105)[Diagrams/Src/TensixFrontend.lua 48-95](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/TensixFrontend.lua#L48-L95)

* * *

## Instruction Encoding Format

All Tensix coprocessor instructions use **32-bit fixed-length encoding** with an 8-bit opcode in bits [31:24]. The encoding format is defined in [Diagrams/Src/Bits32.lua 16-86](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L16-L86) via the `Bits32(fields)` function, which generates instruction format diagrams.

### Instruction Format Classes

Instructions are grouped by format structure:

| Format Class | Opcode Range | Common Fields | Examples |
| --- | --- | --- | --- |
| **Matrix Data Movement** | `0x08-0x13` | `DstRow[9:0]`, `SrcRow[5:0]`, `AddrMod[1:0]`, `UseDst32bLo` | MOVD2A, MOVB2A, MOVA2D, MOVB2D |
| **Matrix Compute** | `0x26-0x34` | `DstRow[9:0]`, `AddrMod[1:0]`, `FlipSrcA`, `FlipSrcB` | MVMUL, ELWMUL, GAPOOL, DOTPV |
| **Matrix Control** | `0x10-0x21` | `Imm10[9:0]`, `AddrMod[1:0]`, `UseDst32b` | ZEROACC, ZEROSRC, TRNSPSRCB |
| **Vector Arithmetic** | `0x84-0x86` | `VD[3:0]`, `VA[3:0]`, `VB[3:0]`, `VC[3:0]`, `Mod1[3:0]` | SFPMAD, SFPADD, SFPMUL |
| **Vector Load/Store** | `0x70-0x72` | `VD[3:0]`, `Imm10[9:0]`, `AddrMod[1:0]`, `Mod0[3:0]` | SFPLOAD, SFPSTORE, SFPLOADI |
| **Vector Special** | `0x73-0x98` | Varies by instruction | SFPLUT, SFPSHFT2, SFPCONFIG, SFPCAST |
| **Unpacker** | `0x42-0x43` | `WhichUnpacker`, `ContextNumber[2:0]`, `FlipSrc` | UNPACR, UNPACR_NOP |
| **Packer** | `0x41, 0x4A` | `PackerMask[3:0]`, `AddrMod[1:0]`, `Last`, `Flush` | PACR, PACR_SETREG |
| **Configuration** | `0xB0-0xB8` | `CfgIndex[10:0]`, `InputReg[5:0]`, `NewValue[15:0]` | WRCFG, RDCFG, SETC16, RMWCIB |
| **Synchronization** | `0xA2-0xA7` | `BlockMask[8:0]`, `ConditionMask` | STALLWAIT, SEMWAIT, SEMINIT |
| **Atomics** | `0x61-0x64` | `AddrReg[5:0]`, `DataReg[5:0]`, `Ofs[1:0]` | ATINCGET, ATSWAP, ATCAS |
| **DMA/Scalar** | `0x45-0x49` | `ResultHalfReg[6:0]`, `AddrReg[5:0]` | SETDMAREG, LOADIND, STOREIND |

### Example: MVMUL (0x26) - Matrix-Vector Multiplication

**Operation:**`Dst[DstRow:DstRow+15] = SrcA[RWC_A:RWC_A+15] @ SrcB[RWC_B:RWC_B+15]`

**Sources:**[Diagrams/Src/Bits32.lua 612-621](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L612-L621)

### Example: SFPMAD (0x84) - Vector Fused Multiply-Add

**Operation:**`LReg[VD] = LReg[VA] * LReg[VB] + LReg[VC]` (32-lane SIMD)

**Sources:**[Diagrams/Src/Bits32.lua 998-1007](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L998-L1007)

### Example: UNPACR (0x42) - Unpacker Data Movement

**Operation:** Reads data from L1, performs format conversion and decompression, writes to SrcA/SrcB/Dst register files.

**Sources:**[Diagrams/Src/Bits32.lua 1324-1343](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L1324-L1343)

* * *

## Instruction Pipeline and Frontend Architecture

RISC-V cores push `.ttinsn` instructions to Tensix execution units via MMIO writes to instruction buffer addresses. The Tensix frontend pipeline expands, buffers, and gates instructions before dispatch.

### Tensix Frontend Pipeline (Per Thread T0/T1/T2)

### MOP Expander Configuration

The MOP (Macro-Operation) Expander is configured via `TENSIX_MOP_CFG_BASE` (address `0xFFB8_0000`). It stores up to 9 instructions with repeat counts, allowing a single `MOP` instruction (opcode `0x01`) to expand into multiple repeated instructions.

| Register Offset | Purpose | Size |
| --- | --- | --- |
| `+0x00` to `+0x08` | MOP Slot 0 Instruction | 32 bits |
| `+0x0C` to `+0x14` | MOP Slot 1 Instruction | 32 bits |
| ... | ... | ... |
| `+0x60` to `+0x68` | MOP Slot 8 Instruction | 32 bits |

**Sources:**[Diagrams/Src/TensixFrontend.lua 16-199](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/TensixFrontend.lua#L16-L199)[WormholeB0/TensixTile/BabyRISCV/README.md 79-105](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L79-L105)

* * *

## Complete Opcode Reference Table

The following table provides a complete mapping of all instruction opcodes to their execution units and issuing constraints. Opcode values are defined in [Diagrams/Src/Bits32.lua 89-1700](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L89-L1700)

| Opcode | Mnemonic | Execution Unit | Issuing Cores | Brief Description |
| --- | --- | --- | --- | --- |
| `0x01` | MOP | MOP Expander | T0, T1, T2 | Macro-operation expansion |
| `0x02` | NOP | Frontend | T0, T1, T2 | No operation |
| `0x03` | MOP_CFG | MOP Expander | T0, T1, T2 | MOP configuration (high 16 bits) |
| `0x04` | REPLAY | Replay Expander | T0, T1, T2 | Instruction buffer replay control |
| `0x08` | MOVD2A | Matrix Unit | T1 | Move Dst → SrcA |
| `0x09` | MOVDBGA2D | Matrix Unit | T1 | Move Dst/SrcB/SrcA → Dst (8 rows) |
| `0x0A` | MOVD2B | Matrix Unit | T1 | Move Dst → SrcB |
| `0x0B` | MOVB2A | Matrix Unit | T1 | Move SrcB → SrcA |
| `0x10` | ZEROACC | Matrix Unit | T1 | Zero accumulator (Dst) |
| `0x11` | ZEROSRC | Matrix Unit | T1 | Zero SrcA/SrcB |
| `0x12` | MOVA2D | Matrix Unit | T1 | Move SrcA → Dst |
| `0x13` | MOVB2D | Matrix Unit | T1 | Move SrcB → Dst |
| `0x16` | TRNSPSRCB | Matrix Unit | T1 | Transpose SrcB |
| `0x17` | SHIFTXA | Matrix Unit | T1 | Shift SrcA columns |
| `0x18` | SHIFTXB | Matrix Unit | T1 | Shift SrcB columns |
| `0x21` | CLREXPHIST | Matrix Unit | T1 | Clear exponent histogram |
| `0x26` | MVMUL | Matrix Unit | T1 | Matrix-vector multiply |
| `0x27` | ELWMUL | Matrix Unit | T1 | Element-wise multiply |
| `0x28` | ELWADD | Matrix Unit | T1 | Element-wise add |
| `0x29` | DOTPV | Matrix Unit | T1 | Dot product of vectors |
| `0x30` | ELWSUB | Matrix Unit | T1 | Element-wise subtract |
| `0x33` | GMPOOL | Matrix Unit | T1 | Global max pooling |
| `0x34` | GAPOOL | Matrix Unit | T1 | Global average pooling |
| `0x35` | GATESRCRST | Matrix Unit | T1 | Gate source reset |
| `0x36` | CLEARDVALID | Matrix Unit | T1 | Clear destination valid |
| `0x37` | SETRWC | Matrix Unit | T1 | Set RWC (Read/Write/Column) pointers |
| `0x38` | INCRWC | Matrix Unit | T1 | Increment RWC pointers |
| `0x40` | XMOV | Mover | B, NC | DMA move operation |
| `0x41` | PACR | Packers | T2 | Pack Dst → L1 |
| `0x42` | UNPACR | Unpackers | T0 | Unpack L1 → Src/Dst |
| `0x43` | UNPACR_NOP | Unpackers | T0 | Unpacker NOP variants |
| `0x45` | SETDMAREG | Scalar Unit | B | Set DMA register |
| `0x46` | FLUSHDMA | Mover | B | Flush DMA operations |
| `0x48` | REG2FLOP | Scalar Unit | B | Register to configuration |
| `0x49` | LOADIND | Scalar Unit | B, T0-T2 | Indirect load from L1 |
| `0x4A` | PACR_SETREG | Packers | T2 | Set packer register |
| `0x50` | SETADC | Packers/Unpackers | T0, T2 | Set address counter |
| `0x51` | SETADCXY | Packers/Unpackers | T0, T2 | Set ADC X/Y components |
| `0x52` | INCADCXY | Packers/Unpackers | T0, T2 | Increment ADC X/Y |
| `0x53` | ADDRCRXY | Packers/Unpackers | T0, T2 | Add/reset CR X/Y |
| `0x54` | SETADCZW | Packers/Unpackers | T0, T2 | Set ADC Z/W components |
| `0x55` | INCADCZW | Packers/Unpackers | T0, T2 | Increment ADC Z/W |
| `0x56` | ADDRCRZW | Packers/Unpackers | T0, T2 | Add/reset CR Z/W |
| `0x57` | SETDVALID | Matrix Unit | T1 | Set destination valid flags |
| `0x58` | ADDDMAREG | Scalar Unit | B | Add DMA registers |
| `0x59` | SUBDMAREG | Scalar Unit | B | Subtract DMA registers |
| `0x5A` | MULDMAREG | Scalar Unit | B | Multiply DMA registers |
| `0x5B` | BITWOPDMAREG | Scalar Unit | B | Bitwise operation on DMA registers |
| `0x5C` | SHIFTDMAREG | Scalar Unit | B | Shift DMA register |
| `0x5D` | CMPDMAREG | Scalar Unit | B | Compare DMA registers |
| `0x5E` | SETADCXX | Packers/Unpackers | T0, T2 | Set ADC X components (extended) |
| `0x60` | DMANOP | Mover | B | DMA no operation |
| `0x61` | ATINCGET | Scalar Unit | B, NC | Atomic increment and get |
| `0x62` | ATINCGETPTR | Scalar Unit | B, NC | Atomic increment and get pointer |
| `0x63` | ATSWAP | Scalar Unit | B, NC | Atomic swap |
| `0x64` | ATCAS | Scalar Unit | B, NC | Atomic compare-and-swap |
| `0x66` | STOREIND | Scalar Unit | B, T0-T2 | Indirect store to L1/MMIO/Src |
| `0x67` | STOREREG | Scalar Unit | B | Store register to L1 |
| `0x68` | LOADREG | Scalar Unit | B | Load register from L1 |
| `0x70` | SFPLOAD | Vector Unit | T1 | Load LReg from Dst |
| `0x71` | SFPLOADI | Vector Unit | T1 | Load LReg immediate |
| `0x72` | SFPSTORE | Vector Unit | T1 | Store LReg to Dst |
| `0x73` | SFPLUT | Vector Unit | T1 | LUT lookup |
| `0x74` | SFPMULI | Vector Unit | T1 | Multiply immediate |
| `0x75` | SFPADDI | Vector Unit | T1 | Add immediate |
| `0x76` | SFPDIVP2 | Vector Unit | T1 | Divide by power of 2 |
| `0x77` | SFPEXEXP | Vector Unit | T1 | Extract exponent |
| `0x78` | SFPEXMAN | Vector Unit | T1 | Extract mantissa |
| `0x79` | SFPIADD | Vector Unit | T1 | Integer add |
| `0x7A` | SFPSHFT | Vector Unit | T1 | Shift |
| `0x7B` | SFPSETCC | Vector Unit | T1 | Set condition code |
| `0x7C` | SFPMOV | Vector Unit | T1 | Move LReg |
| `0x7D` | SFPABS | Vector Unit | T1 | Absolute value |
| `0x7E` | SFPAND | Vector Unit | T1 | Bitwise AND |
| `0x7F` | SFPOR | Vector Unit | T1 | Bitwise OR |
| `0x80` | SFPNOT | Vector Unit | T1 | Bitwise NOT |
| `0x81` | SFPLZ | Vector Unit | T1 | Leading zeros count |
| `0x82` | SFPSETEXP | Vector Unit | T1 | Set exponent |
| `0x83` | SFPSETMAN | Vector Unit | T1 | Set mantissa |
| `0x84` | SFPMAD | Vector Unit | T1 | Fused multiply-add |
| `0x85` | SFPADD | Vector Unit | T1 | Add |
| `0x86` | SFPMUL | Vector Unit | T1 | Multiply |
| `0x87` | SFPPUSHC | Vector Unit | T1 | Push condition stack |
| `0x88` | SFPPOPC | Vector Unit | T1 | Pop condition stack |
| `0x89` | SFPSETSGN | Vector Unit | T1 | Set sign bit |
| `0x8A` | SFPENCC | Vector Unit | T1 | Enable condition code |
| `0x8B` | SFPCOMPC | Vector Unit | T1 | Compare condition |
| `0x8C` | SFPTRANSP | Vector Unit | T1 | Transpose |
| `0x8D` | SFPXOR | Vector Unit | T1 | Bitwise XOR |
| `0x8E` | SFPSTOCHRND | Vector Unit | T1 | Stochastic rounding |
| `0x8F` | SFPNOP | Vector Unit | T1 | SFPU no operation |
| `0x90` | SFPCAST | Vector Unit | T1 | Format cast |
| `0x91` | SFPCONFIG | Vector Unit | T1 | SFPU configuration |
| `0x92` | SFPSWAP | Vector Unit | T1 | Swap LRegs |
| `0x93` | SFPLOADMACRO | Vector Unit | T1 | Load with macro |
| `0x94` | SFPSHFT2 | Vector Unit | T1 | Shift variant 2 |
| `0x95` | SFPLUTFP32 | Vector Unit | T1 | FP32 LUT lookup |
| `0x96` | SFPLE | Vector Unit | T1 | Less than or equal |
| `0x97` | SFPGT | Vector Unit | T1 | Greater than |
| `0x98` | SFPMUL24 | Vector Unit | T1 | 24-bit multiply |
| `0x99` | SFPARECIP | Vector Unit | T1 | Approximate reciprocal |
| `0xA0` | ATGETM | Sync Unit | T0, T1, T2 | Atomic get mutex |
| `0xA1` | ATRELM | Sync Unit | T0, T1, T2 | Atomic release mutex |
| `0xA2` | STALLWAIT | Sync Unit | T0, T1, T2 | Stall until conditions met |
| `0xA3` | SEMINIT | Sync Unit | T0, T1, T2 | Initialize semaphore |
| `0xA4` | SEMPOST | Sync Unit | T0, T1, T2 | Post semaphore |
| `0xA5` | SEMGET | Sync Unit | T0, T1, T2 | Get semaphore (decrement) |
| `0xA6` | SEMWAIT | Sync Unit | T0, T1, T2 | Wait for semaphore condition |
| `0xA7` | STREAMWAIT | Sync Unit | T0, T1, T2 | Wait for stream condition |
| `0xB0` | WRCFG | Config Unit | B, T0, T1, T2 | Write configuration register |
| `0xB1` | RDCFG | Config Unit | B, T0, T1, T2 | Read configuration register |
| `0xB2` | SETC16 | Config Unit | B, T0, T1, T2 | Set 16-bit config immediate |
| `0xB3-0xC2` | RMWCIB | Config Unit | B, T0, T1, T2 | Read-modify-write config (indexed) |
| `0xB7` | STREAMWRCFG | Config Unit | B | Write stream configuration |
| `0xB8` | CFGSHIFTMASK | Config Unit | B, T0, T1, T2 | Config shift and mask |

**Sources:**[Diagrams/Src/Bits32.lua 89-1700](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L89-L1700)

* * *

## Instruction Encoding Examples with Bit Layouts

The following examples show detailed bit-level encoding for representative instructions from each category. All encoding definitions are in [Diagrams/Src/Bits32.lua 89-1700](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L89-L1700)

### Matrix Unit: MVMUL (0x26)

**Operation:**`Dst[DstRow:DstRow+15] = SrcA[RWC_A:RWC_A+15] @ SrcB[RWC_B:RWC_B+15]`

| Bit Range | Field Name | Description | Example Value |
| --- | --- | --- | --- |
| [31:24] | Opcode | MVMUL opcode | `0x26` |
| [23] | FlipSrcB | Bank flip for SrcB (0/1) | `0` |
| [22] | FlipSrcA | Bank flip for SrcA (0/1) | `0` |
| [21:20] | Reserved | Must be 0 | `0b00` |
| [19] | BroadcastSrcBRow | Broadcast SrcB across rows (0/1) | `0` |
| [18:17] | Reserved | Must be 0 | `0b00` |
| [16:15] | AddrMod | Address mode indirection (2-bit) | `0b00` |
| [14:10] | Reserved | Must be 0 | `0b00000` |
| [9:0] | DstRow | Destination row (0-1023 16-bit, 0-511 32-bit) | `0x000` |

**Example Encoding:**`0x26000000` = MVMUL with DstRow=0, no broadcasting, no bank flips

**Sources:**[Diagrams/Src/Bits32.lua 612-621](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L612-L621)

### Vector Unit: SFPMAD (0x84)

**Operation:**`LReg[VD] = LReg[VA] * LReg[VB] + LReg[VC]` (32-lane SIMD FMA)

| Bit Range | Field Name | Description | Example Value |
| --- | --- | --- | --- |
| [31:24] | Opcode | SFPMAD opcode | `0x84` |
| [23:20] | Reserved | Must be 0 | `0x0` |
| [19:16] | VA | Source A LReg index (0-15) | `0x0` (LReg0) |
| [15:12] | VB | Source B LReg index (0-15) | `0x1` (LReg1) |
| [11:8] | VC | Source C (addend) LReg index (0-15) | `0x2` (LReg2) |
| [7:4] | VD | Destination LReg index (0-15) | `0x3` (LReg3) |
| [3:0] | Mod1 | Modifier flags (negation, etc.) | `0x0` |

**Example Encoding:**`0x84001230` = SFPMAD VD=LReg3, VA=LReg0, VB=LReg1, VC=LReg2, Mod1=0

**Sources:**[Diagrams/Src/Bits32.lua 998-1007](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L998-L1007)

### Unpacker: UNPACR (0x42)

**Operation:** Read from L1, convert format, decompress, write to SrcA/SrcB/Dst

| Bit Range | Field Name | Description | Example Value |
| --- | --- | --- | --- |
| [31:24] | Opcode | UNPACR opcode | `0x42` |
| [23] | WhichUnpacker | 0=Unpacker0, 1=Unpacker1 | `0` |
| [22:21] | Ch1YInc | Channel 1 Y increment (0-3) | `0b00` |
| [20:19] | Ch1ZInc | Channel 1 Z increment (0-3) | `0b00` |
| [18:17] | Ch0YInc | Channel 0 Y increment (0-3) | `0b01` |
| [16:15] | Ch0ZInc | Channel 0 Z increment (0-3) | `0b00` |
| [14] | Reserved | Must be 0 | `0` |
| [13] | Reserved | Must be 0 | `0` |
| [12:10] | ContextNumber | Context number (0-7) | `0b000` |
| [9:8] | ContextADC | Context ADC select (0-3) | `0b00` |
| [7] | MultiContextMode | Multi-context enable (0/1) | `0` |
| [6] | FlipSrc | Bank flip (0/1) | `0` |
| [5] | Reserved | Must be 0 | `0` |
| [4] | AllDatumsAreZero | Zero optimization (0/1) | `0` |
| [3] | UseContextCounter | Use context counter (0/1) | `0` |
| [2] | RowSearch | Row search enable (0/1) | `0` |
| [1] | FlushMode | 0=Normal, 1=Flush cache | `0` |
| [0] | Reserved | Must be 0 | `0` |

**Example Encoding:**`0x42020000` = UNPACR Unpacker0, Ch0YInc=1, all other fields=0

**Sources:**[Diagrams/Src/Bits32.lua 1324-1343](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L1324-L1343)

### Packer: PACR (0x41)

**Operation:** Read from Dst, convert format, compress, write to L1

| Bit Range | Field Name | Description | Example Value |
| --- | --- | --- | --- |
| [31:24] | Opcode | PACR opcode | `0x41` |
| [23] | Reserved | Must be 0 | `0` |
| [22:21] | Reserved | - | `0b00` |
| [20:17] | Reserved | - | `0b0000` |
| [16:15] | AddrMod | Address mode (2-bit) | `0b00` |
| [14:13] | Reserved | - | `0b00` |
| [12] | ZeroWrite | Zero write enable (0/1) | `0` |
| [11:8] | PackerMask | Packer selection (4-bit, one-hot) | `0b0001` (Packer0) |
| [7] | OvrdThreadId | Override thread ID (0/1) | `0` |
| [6:5] | Reserved | - | `0b00` |
| [4] | Concat | Concatenate mode (0/1) | `0` |
| [3:2] | Reserved | - | `0b00` |
| [1] | Flush | Flush packer (0/1) | `0` |
| [0] | Last | Last tile flag (0/1) | `1` |

**Example Encoding:**`0x41000101` = PACR Packer0, Last=1, Flush=0

**Sources:**[Diagrams/Src/Bits32.lua 182-194](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L182-L194)

### Configuration: WRCFG (0xB0)

**Operation:** Write RISC-V GPR to backend configuration register

| Bit Range | Field Name | Description | Example Value |
| --- | --- | --- | --- |
| [31:24] | Opcode | WRCFG opcode | `0xB0` |
| [23:22] | Reserved | Must be 0 | `0b00` |
| [21:16] | InputReg | Source GPR (0-31, encoded in 6 bits) | `0x05` (x5/t0) |
| [15] | Is128Bit | 0=32-bit write, 1=128-bit write | `0` |
| [14:11] | Reserved | Must be 0 | `0b0000` |
| [10:0] | CfgIndex | Config register index (0-353 WH, 0-397 BH) | `0x000` |

**Example Encoding:**`0xB0050000` = WRCFG CfgIndex=0, InputReg=x5, 32-bit write

**Sources:**[Diagrams/Src/Bits32.lua 268-275](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L268-L275)

### Synchronization: STALLWAIT (0xA2)

**Operation:** Stall execution until specified backend conditions are met

**Wormhole B0 Encoding:**

| Bit Range | Field Name | Description | Example Value |
| --- | --- | --- | --- |
| [31:24] | Opcode | STALLWAIT opcode | `0xA2` |
| [23:15] | BlockMask | Execution unit mask (9-bit) | `0x1FF` (all) |
| [14:0] | ConditionMask | Wait condition mask (15-bit) | `0x0001` |

**Blackhole A0 Encoding:**

| Bit Range | Field Name | Description | Example Value |
| --- | --- | --- | --- |
| [31:24] | Opcode | STALLWAIT opcode | `0xA2` |
| [23:15] | BlockMask | Execution unit mask (9-bit) | `0x1FF` (all) |
| [14:13] | Reserved | Must be 0 | `0b00` |
| [12:0] | ConditionMask | Wait condition mask (13-bit) | `0x0001` |

**Example Encoding (WH):**`0xA2FF8001` = STALLWAIT all units, condition bit 0

**Sources:**[Diagrams/Src/Bits32.lua 134-147](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L134-L147)

### Atomic: ATSWAP (0x63)

**Operation:** Atomic swap 128-bit value in L1 with bitmask

| Bit Range | Field Name | Description | Example Value |
| --- | --- | --- | --- |
| [31:24] | Opcode | ATSWAP opcode | `0x63` |
| [23] | Reserved | Must be 0 | `0` |
| [22] | SingleDataReg | Single register mode (0/1) | `0` |
| [21:14] | Mask | Byte mask for swap (8-bit) | `0xFF` |
| [13:12] | Reserved | - | `0b00` |
| [11:6] | DataReg | Data source GPR(s) (6-bit encoded) | `0x06` |
| [5:0] | AddrReg | Address GPR (6-bit encoded) | `0x05` |

**Example Encoding:**`0x633F8185` = ATSWAP AddrReg=x5, DataReg=x6, Mask=0xFF

**Sources:**[Diagrams/Src/Bits32.lua 98-106](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L98-L106)

* * *

## Instruction Issuing Constraints

Different instruction categories have specific issuing constraints based on the executing core and target unit:

### Core-Specific Instruction Access

**Sources:**[Diagrams/Src/TensixFrontend.lua 48-195](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/TensixFrontend.lua#L48-L195)[Diagrams/Src/Bits32.lua 1-1700](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L1-L1700)

* * *

## Instruction Latency and Throughput

Execution latencies vary significantly by instruction category:

| Instruction Type | Latency | Throughput | Notes |
| --- | --- | --- | --- |
| **RV32IM ALU** | 1 cycle | 1/cycle | Single-cycle integer operations |
| **RV32IM Multiply** | 2 cycles | 1/2 cycles | 32×32→32-bit multiply |
| **RV32IM Divide** | 2-33 cycles | Variable | Iterative algorithm |
| **UNPACR** | 8+ cycles | 4-16 tiles/cycle | Depends on format conversion |
| **MVMUL (1 phase)** | 4 cycles | 4 TFLOP/s | 16×16 matrix operation |
| **MVMUL (4 phases)** | 16 cycles | 1 TFLOP/s | Higher precision mode |
| **SFPMAD** | 2 cycles | 0.064 TFLOP/s (WH) 0.0864 TFLOP/s (BH) | 32-wide SIMD FMA |
| **PACR** | 8+ cycles | 4-16 tiles/cycle | Depends on compression |
| **WRCFG** | 1 cycle | 1/cycle | Immediate configuration update |
| **STALLWAIT** | Variable | - | Blocks until conditions met |

**Sources:**[Diagrams/Src/TensixFrontend.lua 1-203](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/TensixFrontend.lua#L1-L203)

* * *

## Opcode Allocation Map

The 8-bit opcode space (bits [31:24]) is organized as follows:

### Opcode Ranges

| Opcode Range | Category | Examples |
| --- | --- | --- |
| `0x00-0x03` | Control Flow | `0x01` MOP, `0x02` NOP, `0x03` MOP_CFG, `0x04` REPLAY |
| `0x08-0x21` | Matrix Unit Data Movement | `0x08` MOVD2A, `0x09` MOVDBGA2D, `0x0A` MOVD2B, `0x0B` MOVB2A, `0x12` MOVA2D, `0x13` MOVB2D |
| `0x10-0x11` | Accumulator Control | `0x10` ZEROACC, `0x11` ZEROSRC |
| `0x16-0x21` | Matrix Unit Manipulation | `0x16` TRNSPSRCB, `0x17` SHIFTXA, `0x18` SHIFTXB, `0x21` CLREXPHIST |
| `0x26-0x36` | Matrix Unit Compute | `0x26` MVMUL, `0x27` ELWMUL, `0x28` ELWADD, `0x29` DOTPV, `0x30` ELWSUB, `0x33` GMPOOL, `0x34` GAPOOL, `0x36` CLEARDVALID |
| `0x37-0x38` | RWC Control | `0x37` SETRWC, `0x38` INCRWC |
| `0x40-0x4A` | Data Movement | `0x40` XMOV, `0x41` PACR, `0x42` UNPACR, `0x43` UNPACR_NOP, `0x45` SETDMAREG, `0x46` FLUSHDMA, `0x48` REG2FLOP, `0x49` LOADIND, `0x4A` PACR_SETREG |
| `0x50-0x5E` | Address Generator Control | `0x50` SETADC, `0x51` SETADCXY, `0x52` INCADCXY, `0x53` ADDRCRXY, `0x54` SETADCZW, `0x55` INCADCZW, `0x56` ADDRCRZW, `0x57` SETDVALID, `0x5E` SETADCXX |
| `0x58-0x5D` | DMA ALU | `0x58` ADDDMAREG, `0x59` SUBDMAREG, `0x5A` MULDMAREG, `0x5B` BITWOPDMAREG, `0x5C` SHIFTDMAREG, `0x5D` CMPDMAREG |
| `0x60-0x68` | Memory Operations | `0x60` DMANOP, `0x61` ATINCGET, `0x62` ATINCGETPTR, `0x63` ATSWAP, `0x64` ATCAS, `0x66` STOREIND, `0x67` STOREREG, `0x68` LOADREG |
| `0x70-0x97` | Vector Unit (SFPU) | `0x70` SFPLOAD, `0x71` SFPLOADI, `0x72` SFPSTORE, `0x73` SFPLUT, `0x84` SFPMAD, `0x85` SFPADD, `0x86` SFPMUL, `0x8F` SFPNOP, `0x93` SFPLOADMACRO |
| `0xA0-0xA7` | Synchronization | `0xA0` ATGETM, `0xA1` ATRELM, `0xA2` STALLWAIT, `0xA3` SEMINIT, `0xA4` SEMPOST, `0xA5` SEMGET, `0xA6` SEMWAIT, `0xA7` STREAMWAIT |
| `0xB0-0xB8` | Configuration | `0xB0` WRCFG, `0xB1` RDCFG, `0xB2` SETC16, `0xB3-0xC2` RMWCIB, `0xB7` STREAMWRCFG, `0xB8` CFGSHIFTMASK |

**Sources:**[Diagrams/Src/Bits32.lua 89-1700](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L89-L1700)

* * *

## Navigation to Detailed References

For comprehensive documentation of each instruction category, including:

*   Complete instruction encoding details
*   Operand descriptions and constraints
*   Execution semantics and side effects
*   Cycle-accurate timing information
*   Code examples and usage patterns

Please refer to the following sub-pages:

1.   **[Baby RISC-V Instruction Set](https://deepwiki.com/tenstorrent/tt-isa-documentation/5.1-baby-risc-v-instruction-set)** - RV32IM base instructions plus TTInsn extensions (LOADIND, STOREIND, ATINCGET, etc.)

2.   **[Matrix Unit Instructions](https://deepwiki.com/tenstorrent/tt-isa-documentation/5.2-matrix-unit-instructions)** - MVMUL, GAPOOL, DOTPV, element-wise operations, data movement within register files

3.   **[Vector Unit Instructions](https://deepwiki.com/tenstorrent/tt-isa-documentation/5.3-vector-unit-core-instructions)** - SFPMAD (FMA), arithmetic operations, transcendentals, SFPLOAD/STORE, conditional execution

4.   **[Unpacker Instructions](https://deepwiki.com/tenstorrent/tt-isa-documentation/5.4-vector-unit-data-movement-instructions)** - UNPACR variants (regular, flush cache, increment context), format conversion, decompression

5.   **[Packer Instructions](https://deepwiki.com/tenstorrent/tt-isa-documentation/5.5-vector-unit-configuration-and-control-instructions)** - PACR instruction, address generator configuration, compression, accumulation modes

6.   **[Data Movement Instructions](https://deepwiki.com/tenstorrent/tt-isa-documentation/5.6-unpacker-instructions-(unpacr))** - LOADIND, STOREIND, XMOV, atomic operations (ATSWAP, ATCAS, ATINCGET)

7.   **[Configuration and Control Instructions](https://deepwiki.com/tenstorrent/tt-isa-documentation/5.7-packer-instructions-(pacr))** - WRCFG, RDCFG, SETC16, SETRWC, STALLWAIT, semaphore operations, replay control

**Sources:**[Diagrams/Src/Bits32.lua 1-1700](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua#L1-L1700)[Diagrams/Src/TensixFrontend.lua 1-203](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/TensixFrontend.lua#L1-L203)

Dismiss
Refresh this wiki

Enter email to refresh