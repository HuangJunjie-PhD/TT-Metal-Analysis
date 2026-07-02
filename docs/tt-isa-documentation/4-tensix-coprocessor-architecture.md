---
project: tt-isa-documentation
github: https://github.com/tenstorrent/tt-isa-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-isa-documentation/4-tensix-coprocessor-architecture
---

# Tensix Coprocessor Architecture

Relevant source files
*   [Diagrams/Out/Bits32_MOVB2A.svg](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Out/Bits32_MOVB2A.svg)
*   [README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/README.md?plain=1)
*   [WormholeB0/TensixTile/BabyRISCV/Mailboxes.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/Mailboxes.md?plain=1)
*   [WormholeB0/TensixTile/L1.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1)
*   [WormholeB0/TensixTile/SoftReset.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/SoftReset.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/MOVB2A.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOVB2A.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/Packers/InputAddressGenerator.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Packers/InputAddressGenerator.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/Packers/OutputAddressGenerator.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Packers/OutputAddressGenerator.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/RWCs.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/RWCs.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/SETDMAREG_Special.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/SETDMAREG_Special.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/UNPACR_FlushCache.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_FlushCache.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/UNPACR_IncrementContextCounter.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_IncrementContextCounter.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/Unpackers/FormatConversion.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Unpackers/FormatConversion.md?plain=1)

## Purpose and Scope

This page provides an architectural overview of the Tensix Coprocessor, the specialized AI compute engine within each Tensix tile. The Tensix Coprocessor executes `.ttinsn` instructions pushed by the Baby RISC-V cores (specifically RISCV T0, T1, and T2) and performs high-throughput AI operations including matrix multiplication, vector operations, data format conversion, and data movement.

The Tensix Coprocessor operates independently from the RISC-V cores - once instructions are pushed, they execute asynchronously. This allows RISC-V cores to set up multiple operations in advance and then perform other tasks while the coprocessor executes.

For information about the RISC-V control cores, see [Baby RISC-V Cores](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.1-baby-risc-v-cores). For instruction-level details, see [Instruction Set Reference](https://deepwiki.com/tenstorrent/tt-isa-documentation/5-instruction-set-reference).

## Architectural Overview

The Tensix Coprocessor is organized into two major subsystems: a **frontend** responsible for instruction scheduling and expansion, and a **backend** containing the execution units that perform actual computation.

### Coprocessor Architecture Diagram

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md 1-88](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md?plain=1#L1-L88)[WormholeB0/TensixTile/L1.md 1-70](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1#L1-L70)[WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md 1-121](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md?plain=1#L1-L121)

### Three-Thread Architecture

The Tensix Coprocessor operates as a 3-way threaded system with independent frontend pipelines but shared backend execution units. Each thread corresponds to one RISC-V Trisc core:

*   **Thread 0** (`CurrentThread = 0`): Controlled by RISCV T0, pushes to `INSTRN_BUF_BASE` (0xFFB2_0000). Typically runs UNPACKER phase kernels.
*   **Thread 1** (`CurrentThread = 1`): Controlled by RISCV T1, pushes to `INSTRN1_BUF_BASE` (0xFFB2_1000). Typically runs MATH phase kernels.
*   **Thread 2** (`CurrentThread = 2`): Controlled by RISCV T2, pushes to `INSTRN2_BUF_BASE` (0xFFB2_2000). Typically runs PACK phase kernels.

Each thread maintains independent state in `RWCs[CurrentThread]`, `ThreadConfig[CurrentThread]`, and per-thread configuration contexts within the frontend expanders. The backend execution units are shared: any unit can execute instructions from any thread, enabling parallel execution of unpack-math-pack pipelines across the three threads.

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md 9-10](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md?plain=1#L9-L10)[WormholeB0/TensixTile/TensixCoprocessor/RWCs.md 1-18](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/RWCs.md?plain=1#L1-L18)

## Frontend Pipeline

The frontend processes instructions from RISCV T0/T1/T2 through multiple stages before dispatching them to backend execution units. Each thread has its own independent frontend pipeline.

### Frontend Pipeline Stages

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md 12-111](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md?plain=1#L12-L111)

### Stage Implementation Details

**Stage 1: Instruction FIFO**

*   Memory-mapped write-only region at `INSTRN_BUF_BASE` (T0), `INSTRN1_BUF_BASE` (T1), `INSTRN2_BUF_BASE` (T2)
*   RISC-V cores push 32-bit `.ttinsn` instructions via `sw` instruction
*   FIFO depths: 32 entries (T0), 16 entries (T1/T2) on Wormhole B0
*   No explicit full/empty checking; writes stall if FIFO is full

**Stage 2: MOP Expander**

*   Processes `MOP` and `MOP_CFG` instructions
*   Template 0: Emits up to 32 instructions with optional masking via `MaskHi:MaskLo` (up to 32 bits)
*   Template 1: Nested loop structure with `OuterCount × InnerCount` iterations
*   Configuration via `MopCfg[9]` array at `TENSIX_MOP_CFG_BASE` (write-only)
*   Passes through all other instructions unchanged
*   Sustains 1 instruction/cycle output after expansion begins

**Stage 3: Replay Expander**

*   32-entry circular buffer loaded via `REPLAY` instruction with `Load=true`
*   Executes 1-32 iterations of buffered sequence via `REPLAY` instruction with `Load=false`
*   Independent 32-entry buffer per thread
*   Configured via `ReplayCfg[32]` written by `REPLAY` instruction

**Stage 4: Wait Gate**

*   Enforces synchronization based on `WaitInstr` configuration (32-bit per thread)
*   Checks semaphores and backend unit availability
*   `STALLWAIT` instruction can insert wait conditions dynamically
*   Prevents instruction dispatch until conditions are satisfied

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md 12-121](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md?plain=1#L12-L121)

## Backend Execution Units

The backend contains nine types of execution units that perform actual computation. Instructions dispatched from the wait gate are routed to the appropriate unit(s) based on their opcode.

### Backend Unit Organization

**Sources:**[Diagrams/Src/TensixFrontend.lua 123-158](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/TensixFrontend.lua#L123-L158)

### Backend Execution Unit Details

| Unit | Key Instructions | Latency | Throughput | Data Structures |
| --- | --- | --- | --- | --- |
| **Sync Unit** | `STALLWAIT`, `SEMWAIT`, `SEMPOST`, `SEMINIT` | 1 cycle | 1 IPC | `Semaphores[8]` (4-bit counters) |
| **Unpackers[2]** | `UNPACR`, `UNPACR_NOP` | 2+ cycles | 4×128 bpc | `Unpackers[i].ContextCounter[3]`, `ADCs[i].Unpacker[i].Channel[2]` |
| **Matrix Unit (FPU)** | `MVMUL`, `ELWMUL`, `ELWADD`, `SETRWC` | 1-5 cycles | 4.096 TFLOP/s | `RWCs[3]`, `MatrixUnit.SrcABank`, `MatrixUnit.SrcBBank` |
| **Vector Unit (SFPU)** | `SFPMAD`, `SFPLOAD`, `SFPSTORE`, `SFPLUT` | 2+ cycles | 0.064 TFLOP/s | `LReg[17]`, `LaneConfig[16]`, `LoadMacroConfig` |
| **Packers[4]** | `PACR`, `PACR_SETREG` | Multi-cycle | 4×128 bpc | `Packers[i].AccTileSize[3]`, `Packers[i].LastTileSize`, `ADCs[i].Packers.Channel[2]` |
| **Scalar Unit (ThCon)** | `LOADIND`, `STOREIND`, `ATINCGET`, `ATCAS` | 3-7+ cycles | 1 req/3 cycles | `GPRs[CurrentThread][64]` (32-bit) |
| **Config Unit** | `SETC16`, `WRCFG`, `RMWCIB`, `REG2FLOP` | 1-3 cycles | 1 IPC | `Config[2][CFG_STATE_SIZE*4]`, `ThreadConfig[3][THD_STATE_SIZE]` |
| **Mover** | `XMOV` | Multi-cycle | 93.1 bpc R+W | L1 ports 7 (write), 8 (read) |
| **Misc Unit** | `SETADC`, `SETADCXY`, `INCADCXY`, `ADDRCRZW` | 1 cycle | 1 IPC | `ADCs[3]` (per-thread addressing counters) |

**Instruction Distribution**: After passing through the wait gate, instructions are decoded and routed to the appropriate execution unit based on their opcode. Multiple units can execute simultaneously on instructions from different threads.

**Key State Variables**:

*   `CurrentThread`: Identifies which thread (0, 1, or 2) issued the instruction
*   `Config[StateID]`: Thread-agnostic configuration selected by `ThreadConfig[CurrentThread].CFG_STATE_ID_StateID`
*   `RWCs[CurrentThread]`: Per-thread addressing counters for Matrix/Vector units (Dst, SrcA, SrcB rows, FidelityPhase)
*   `ADCs[i]`: Per-thread addressing counters for Unpackers/Packers (X, Y, Z, W coordinates)

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md 1-76](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md?plain=1#L1-L76)[WormholeB0/TensixTile/TensixCoprocessor/RWCs.md 1-78](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/RWCs.md?plain=1#L1-L78)[WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md 1-88](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md?plain=1#L1-L88)[WormholeB0/TensixTile/L1.md 32-53](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1#L32-L53)

## Register Files

The Tensix Coprocessor uses specialized register files optimized for matrix and vector operations. These are distinct from the RISC-V GPRs and are only accessible via `.ttinsn` instructions.

### Internal Register Files

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md 1-570](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1#L1-L570)[WormholeB0/TensixTile/SoftReset.md 52-127](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/SoftReset.md?plain=1#L52-L127)

### Register File Specifications

**SrcA and SrcB** (Matrix Unit Inputs):

*   Structure: `SrcA[bank][row][col]` and `SrcB[bank][row][col]` where `bank ∈ {0,1}`, `row ∈ [0,63]`, `col ∈ [0,15]`
*   Element format: 19-bit with internal representation varying by data type (TF32, BF16, FP16, Integer "8", Integer "16")
*   Bank control: `MatrixUnit.SrcABank` and `MatrixUnit.SrcBBank` select active bank; `SrcA[bank].AllowedClient` and `SrcB[bank].AllowedClient` control write access (`SrcClient::Unpackers` vs `SrcClient::MatrixUnit`)
*   Addressing: Via `RWCs[CurrentThread].SrcA` and `RWCs[CurrentThread].SrcB` (6-bit row indices) plus column offset
*   Write sources: `UNPACR` (via `Unpackers[0].SrcRow[CurrentThread]` and `Unpackers[1].SrcRow[CurrentThread]`), `STOREIND`, `MOVB2A`, `MOVD2A`, `ZEROSRC`
*   Read source: Matrix Unit instructions (bank determined by `MatrixUnit.SrcABank` and `MatrixUnit.SrcBBank`)

**Dst** (Accumulator/Result Buffer):

*   Structure: `Dst[bank][row][col]` with `bank ∈ {0,1}`, addressable as `Dst16b[row][col]` (16-bit view) or `Dst32b[row][col]` (32-bit view)
*   Row addressing: `RWCs[CurrentThread].Dst` (10-bit) plus potential remapping via `Config.DEST_ADDR_CTRL_XY_REG_2_*` configuration
*   Write sources: Matrix Unit accumulation, Vector Unit computation, `UNPACR` (when `UnpackToDst=true`), `MOVB2D`, `MOVA2D`
*   Read sources: Packers (via `Config.DEST_TARGET_REG_CFG_PACK_SEC[i].Offset`), Vector Unit (`SFPLOAD`), Matrix Unit move instructions
*   Reset: `ZEROACC` instruction or soft reset bit 17

**LReg** (Vector Unit Registers):

*   Structure: `LReg[index][lane]` where `index ∈ [0,16]` and `lane ∈ [0,31]`
*   Read-only registers: `LReg[8]` through `LReg[10]`, `LReg[15]` (constants like 0, 1, 2, etc.)
*   Writable registers: `LReg[0-7]`, `LReg[11-14]`, `LReg[16]`
*   Access: Via `SFPLOAD` (Dst → LReg), `SFPSTORE` (LReg → Dst), Vector Unit instructions operate on `LReg`
*   Configuration: Per-lane configuration via `LaneConfig[16]` (each covers 2 lanes)

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md 324-373](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1#L324-L373)[WormholeB0/TensixTile/SoftReset.md 52-127](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/SoftReset.md?plain=1#L52-L127)[WormholeB0/TensixTile/TensixCoprocessor/Packers/InputAddressGenerator.md 1-102](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Packers/InputAddressGenerator.md?plain=1#L1-L102)

## Backend Configuration System

The backend is configured through two separate arrays: `Config` for thread-agnostic settings and `ThreadConfig` for per-thread settings. These are memory-mapped into the RISC-V address space starting at `TENSIX_CFG_BASE`.

### Backend Configuration System

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md 1-88](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md?plain=1#L1-L88)

### Configuration Structure Details

**Config Array**: `Config[2][CFG_STATE_SIZE * 4]`

*   Memory-mapped at `TENSIX_CFG_BASE` (0xFFB1_0000) for all RISC-V cores except NC
*   2 state banks; active bank selected by `ThreadConfig[CurrentThread].CFG_STATE_ID_StateID` (1-bit field)
*   32-bit aligned access required; unaligned access causes undefined behavior
*   Size: `CFG_STATE_SIZE * 4` = 354 registers (Wormhole B0) or 398 registers (Blackhole A0)
*   Contains packer/unpacker configuration, address bases, strides, tile descriptors, format settings
*   Example fields: `Config[StateID].THCON_SEC[0].REG0_TileDescriptor`, `Config[StateID].UNP[0].ADDR_BASE_REG_1_Base`, `Config.PRNG_SEED_Seed_Val_ADDR32`
*   Shorthand: `Config.Field` when `Field_ADDR32 >= GLOBAL_CFGREG_BASE_ADDR32` (global fields written to both banks)

**ThreadConfig Array**: `ThreadConfig[3][THD_STATE_SIZE]`

*   Memory-mapped immediately after `Config` array
*   Structure: `{uint16_t Value, Padding[7];}` per entry (only low 16 bits used)
*   Indexed by `CurrentThread`: `ThreadConfig[0]` for T0, `ThreadConfig[1]` for T1, `ThreadConfig[2]` for T2
*   Contains per-thread state: `ADDR_MOD_AB_SEC`, `ADDR_MOD_DST_SEC`, `ADDR_MOD_PACK_SEC`, `CFG_STATE_ID_StateID`, `SRCA_SET_Base`, etc.
*   Not writable via RISC-V `sw` instructions; must use `SETC16` instruction

**Key Configuration Fields**:

*   Unpacker: `THCON_SEC[0/1].REG0_TileDescriptor`, `THCON_SEC[0/1].REG2_Out_data_format`, `UNP[0/1].ADDR_BASE_REG_1_Base`
*   Packer: `Config[StateID].Packers[i].Out_data_format`, `Config[StateID].Packers[i].L1_Dest_addr`, `PCK0_ADDR_CTRL_XY_REG_0_Xstride`
*   Matrix Unit: `ALU_ACC_CTRL_Zero_Flag_disabled_src`, `ALU_FORMAT_SPEC_REG0_SrcAUnsigned`
*   Address modes: `ThreadConfig[CurrentThread].ADDR_MOD_AB_SEC[0..7]`, `ThreadConfig[CurrentThread].ADDR_MOD_DST_SEC[0..7]`

**Synchronization Considerations**: RISC-V stores to `Config` are asynchronous with Tensix instruction execution. To prevent race conditions:

1.   Use `WRCFG`/`RMWCIB`/`REG2FLOP` instructions instead of RISC-V `sw` when possible
2.   Use `STALLWAIT` (condition C13, block mask covering relevant unit) after RISC-V `sw` to `Config`
3.   Use RISC-V memory ordering (fence, load-consume-store sequence) to ensure RISC-V `sw` completes before Tensix instruction push

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md 1-88](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md?plain=1#L1-L88)[WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md 34-56](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1#L34-L56)[WormholeB0/TensixTile/TensixCoprocessor/Packers/InputAddressGenerator.md 8-60](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Packers/InputAddressGenerator.md?plain=1#L8-L60)

## Instruction Flow Summary

### Complete Instruction Flow Diagram

**Sources:** Diagram 3 from high-level overview, [WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md 1-121](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md?plain=1#L1-L121)

### Key Points

1.   **Software Layer**: TT-Metalium calls TT-LLK, which contains pre-written `.ttinsn` sequences compiled into RISC-V executables

2.   **RISC-V Orchestration**: RISCV B typically coordinates overall execution flow and configures the other cores. RISCV T0/T1/T2 push instruction sequences to their respective threads.

3.   **Frontend Processing**: Instructions pass through expansion (MOP → micro-ops, REPLAY → replayed sequences) and synchronization (wait gate) before reaching backend units

4.   **Backend Execution**: Multiple units can execute in parallel across different threads. Common pattern: Thread T0 runs unpackers, Thread T1 runs matrix/vector units, Thread T2 runs packers

5.   **Memory Flow**: Typical data flow is L1 → Unpackers → SrcA/SrcB → Matrix Unit → Dst → Packers → L1, with Vector Unit operating on Dst in parallel

6.   **Asynchronous Operation**: Once instructions are pushed, RISC-V cores are free to do other work. Synchronization uses `STALLWAIT`, semaphores, or mailboxes.

**Sources:** Diagram 3 and Diagram 4 from high-level overview

## Performance Characteristics

### Peak Throughput Specifications

| Component | Peak Throughput | Details |
| --- | --- | --- |
| **Instruction Push** | 1 instr/cycle per RISC-V | RISC-V `sw` to `INSTRN_BUF_BASE`/`INSTRN1_BUF_BASE`/`INSTRN2_BUF_BASE` |
| **Frontend (per thread)** | 1 instr/cycle output | After MOP/REPLAY expansion; transition penalty between non-MOP and MOP |
| **Matrix Unit** | 4.096 TFLOP/s @ 1 GHz | `MVMUL` with 1 fidelity phase: 64×16 (SrcA) × 16×16 (SrcB) = 64×16 (Dst) per 5 cycles |
| **Vector Unit** | 0.064 TFLOP/s @ 1 GHz | `SFPMAD`: 32 lanes × FP32 FMA per 2 cycles = 16 GFLOP/s per lane per GHz |
| **Unpacker 0 (solo)** | 4×128 bpc from L1 | Uses L1 ports 2, 3, 4, 5 (can throttle to x1 or x2 via `Throttle_mode`) |
| **Unpacker 1 (solo)** | 4×128 bpc from L1 | Shares ports 2, 3, 4; dedicated port 5 |
| **Both Unpackers** | 5×128 bpc total from L1 | Shared bandwidth; see [WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md 583-598](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1#L583-L598) for contention table |
| **Packers (4x)** | 4×128 bpc to L1 | Packer 0: ports 2, 5; Packer 1: port 2; Packer 2: port 5; Packer 3: port 6 |
| **Mover** | ~93.1 bpc R + ~93.1 bpc W | Measured: 8×128b R + 8×128b W per 11 cycles; L1 ports 7 (write), 8 (read) |
| **Scalar Unit** | 1 req / 3 cycles | `LOADIND`/`STOREIND` via L1 port 10; narrow writes are 5 cycles |

**Fidelity Phase Impact on Matrix Unit**: `MVMUL` throughput scales inversely with number of fidelity phases:

*   1 phase: 4.096 TFLOP/s (5 cycles per operation)
*   2 phases: 2.048 TFLOP/s (2 × 5 cycles)
*   3 phases: 1.366 TFLOP/s (3 × 5 cycles)
*   4 phases: 1.024 TFLOP/s (4 × 5 cycles)

Required fidelity phases depend on data types: TF32×TF32 = 1 phase, BF16×BF16 = 2-3 phases, FP16×FP16 = 2-4 phases

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md 58-76](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md?plain=1#L58-L76)[WormholeB0/TensixTile/L1.md 32-53](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1#L32-L53)[WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md 573-598](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1#L573-L598)

### Execution Model

The Tensix Coprocessor uses a decoupled execution model:

*   **Frontend**: 3 independent threads, each processing 1 instruction/cycle
*   **Backend**: Shared execution units that can run instructions from any thread
*   **Parallelism**: Different units can execute simultaneously on different instructions from different threads
*   **Pipeline Depth**: Instructions can be in various stages (wait gate, execution, completion) simultaneously

This design enables high utilization: while the Matrix Unit executes a T1 instruction, unpackers can prepare data for the next operation (T0 instruction) and packers can write out previous results (T2 instruction).

**Sources:**[Diagrams/Src/TensixFrontend.lua 28-122](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/TensixFrontend.lua#L28-L122)[WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md 34-50](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md?plain=1#L34-L50)

Dismiss
Refresh this wiki

Enter email to refresh