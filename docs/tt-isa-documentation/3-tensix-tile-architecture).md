---
project: tt-isa-documentation
github: https://github.com/tenstorrent/tt-isa-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-isa-documentation/3-tensix-tile-architecture)
---

# Tensix Tile Architecture

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/README.md?plain=1)
*   [WormholeB0/EthernetTile/BabyRISCV/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/EthernetTile/BabyRISCV/README.md?plain=1)
*   [WormholeB0/TensixTile/BabyRISCV/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1)
*   [WormholeB0/TensixTile/L1.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1)
*   [WormholeB0/TensixTile/PIC.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/PIC.md?plain=1)
*   [WormholeB0/TensixTile/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1)
*   [WormholeB0/TensixTile/SoftReset.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/SoftReset.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/Packers/InputAddressGenerator.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Packers/InputAddressGenerator.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/Packers/OutputAddressGenerator.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Packers/OutputAddressGenerator.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/SETDMAREG_Special.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/SETDMAREG_Special.md?plain=1)

## Purpose and Scope

This page introduces the Tensix Tile as the fundamental compute unit in Wormhole and Blackhole architectures. It describes the tile's major components, their roles, and how RISC-V control processors orchestrate specialized coprocessors to achieve high performance. For detailed information about specific subsystems, see the child pages: [Baby RISC-V Cores](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.1-baby-risc-v-cores), [Memory Hierarchy and L1 RAM](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.2-memory-hierarchy-and-l1-ram), [Inter-Core Communication](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.3-inter-core-communication), [Data Movement](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.4-programmable-interrupt-controller-(pic)), and [PIC](https://deepwiki.com/tenstorrent/tt-isa-documentation/3-tensix-tile-architecture)#3.5). For the Tensix Coprocessor's internal architecture and instruction sets, see [Tensix Coprocessor Architecture](https://deepwiki.com/tenstorrent/tt-isa-documentation/4-tensix-coprocessor-architecture).

**Sources:**[WormholeB0/TensixTile/README.md 1-17](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L1-L17)

## Overview

A Tensix Tile is the primary compute unit in Tenstorrent ASICs. Each Wormhole ASIC contains 80 Tensix Tiles arranged in a grid, connected via dual Networks-on-Chip (NoC). Each tile is a self-contained processor with:

*   **1464 KiB L1 scratchpad RAM** organized as 16 banks
*   **Five RV32IM RISC-V cores** ("Baby RISC-Vs") for control and orchestration
*   **Tensix Coprocessor** for high-performance AI computation
*   **Data movement engines** (Mover, TDMA-RISC) for efficient memory operations
*   **Dual NoC interfaces** for inter-tile and off-chip communication
*   **NoC Overlay** coprocessor for stream-based data delivery

The design philosophy emphasizes **separation of control and compute**: RISC-V cores provide flexible software control while the Tensix Coprocessor delivers computational throughput. RISC-V cores are not expected to achieve high performance on their own—their role is to configure and orchestrate the specialized hardware that drives performance.

**Sources:**[WormholeB0/TensixTile/README.md 1-17](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L1-L17)

## Tensix Tile Component Architecture

**Tensix Tile Component Architecture with Memory-Mapped Addresses**

This diagram shows major functional blocks with specific memory-mapped address ranges and L1 port assignments. RISC-V cores communicate via memory-mapped instruction buffers (`INSTRN_BUF_BASE`), control buffers (`PCBufs`), and synchronization primitives (`TTSync`, `Mailboxes`). All components access the 1464 KiB L1 RAM through 16 dedicated ports to avoid contention.

**Sources:**[WormholeB0/TensixTile/README.md 1-17](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L1-L17)[WormholeB0/TensixTile/BabyRISCV/README.md 1-120](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L1-L120)[WormholeB0/TensixTile/L1.md 1-70](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1#L1-L70)

## Control and Compute Separation

The Tensix Tile architecture enforces a clear division between **control** (software-programmable flexibility) and **compute** (fixed-function throughput):

### Control Layer: Five RISC-V Cores

Each tile contains five 32-bit in-order single-issue RISC-V cores implementing RV32IM. They execute at 1 GHz with the goal of one instruction per cycle. These cores are optimized for area and power efficiency rather than raw performance.

**RISC-V B** (Brisc) serves as the master coordinator:

*   Orchestrates the other four RISC-V cores
*   Manages data movement via Mover and NoC
*   Has 4 KiB local data RAM
*   Typically runs data movement kernels in TT-Metalium

**RISC-V T0, T1, T2** (Trisc) specialize in Tensix Coprocessor control:

*   T0 pushes instructions to Unpackers (UNPACK phase)
*   T1 pushes instructions to Matrix and Vector Units (MATH phase)
*   T2 pushes instructions to Packers (PACK phase)
*   Each has 2 KiB local data RAM
*   T0 and T2 have 2 KiB instruction cache; T1 has 0.5 KiB
*   Typically run compute kernels in TT-Metalium

**RISC-V NC** (Ncrisc) handles additional data movement:

*   Controls TDMA-RISC for NoC-based DMA
*   Has 4 KiB local data RAM
*   Unique 16 KiB instruction RAM (in addition to 0.5 KiB cache)
*   Typically runs data movement kernels in TT-Metalium

The cores communicate via [Mailboxes](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.3-inter-core-communication) (FIFO queues), [PCBufs](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.3-inter-core-communication) (control message buffers), and [TTSync](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.3-inter-core-communication) (synchronization primitives).

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 1-14](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L1-L14)[WormholeB0/TensixTile/README.md 1-17](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L1-L17)

### Compute Layer: Tensix Coprocessor

The Tensix Coprocessor executes compute-intensive operations at high throughput. RISC-V cores push instructions into the coprocessor's execution units, which then operate autonomously:

| Execution Unit | Function | Peak Performance (Wormhole) |
| --- | --- | --- |
| **Matrix Unit (FPU)** | Low-precision matrix operations (BF16/FP16/19-bit) | 4.096 TFLOP/s @ 1 GHz |
| **Vector Unit (SFPU)** | 32-lane SIMD operations (FP32) | 0.064 TFLOP/s @ 1 GHz |
| **Scalar Unit (ThCon)** | Control flow, configuration, L1 access with atomics | N/A (utility unit) |
| **Unpackers (×2)** | Load and transform data from L1 to registers | 5×128-bit reads/cycle |
| **Packers (×4)** | Store and transform data from registers to L1 | 4×128-bit writes/cycle |

The coprocessor contains three main register files:

*   **SrcA, SrcB**: 64×16 entries, 19-bit precision (Matrix Unit inputs)
*   **Dst**: 1024×16 entries, 16/32-bit precision (accumulator, shared across units)

For detailed coprocessor architecture, see [Tensix Coprocessor Architecture](https://deepwiki.com/tenstorrent/tt-isa-documentation/4-tensix-coprocessor-architecture).

**Sources:**[WormholeB0/TensixTile/README.md 8-13](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L8-L13)[WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md 1-20](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md?plain=1#L1-L20)

## RISC-V Instruction Push Mechanism

RISC-V cores push Tensix instructions by writing 32-bit instruction words to memory-mapped buffers using standard `sw` (store word) instructions. Each T-core has specific address mappings that route instructions to different pipeline stages:

**RISC-V to Tensix Coprocessor Instruction Pipeline**

The instruction push mechanism uses per-thread pipelines with three expansion stages. RISC-V T0/T1/T2 write to `INSTRN_BUF_BASE` (address 0xFFE4_0000, which maps to different FIFOs depending on which core accesses it). RISC-V B can bypass the MOP Expander by writing directly to post-MOP FIFOs via `INSTRN1_BUF_BASE` (0xFFE5_0000) and `INSTRN2_BUF_BASE` (0xFFE6_0000). Each thread maintains independent configuration in `MopCfg[9]` arrays accessible via `TENSIX_MOP_CFG_BASE` (0xFFB8_0000, write-only from T-cores).

**MOP Expander Templates:**

*   **Template 0**: Masked emission controlled by 33-bit mask (`MaskHi` from `MOP_CFG` + `MaskLo` from `MOP` instruction), emits `InsnA0` or `SkipA0` per bit, optionally followed by `InsnA1/A2/A3` and `InsnB`
*   **Template 1**: Nested loops with `OuterCount × InnerCount` iterations, emitting `StartOp`, repeated `LoopOp` or alternating `LoopOp`/`LoopOp1`, and `EndOp0`/`EndOp1`

**Replay Expander**: Buffers up to 32 instructions and repeats them 1-32 times as specified by `REPLAY` instruction's `Count` field. This enables low-overhead looping without RISC-V involvement.

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 80-106](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L80-L106)[WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md 1-120](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md?plain=1#L1-L120)

## L1 Scratchpad Memory

Each Tensix Tile contains **1464 KiB of L1 RAM** organized as 16 banks of 91.5 KiB each. Despite the "L1" name suggesting a cache, this is **scratchpad RAM**—software explicitly manages all data placement.

### Bank Organization and Access Ports

L1 provides 16 access ports, where any port can access any bank. Each bank supports:

*   One 128-bit read OR one 128-bit write per cycle
*   Narrow reads (< 128 bits): implemented as 128-bit read with unused data discarded
*   Narrow writes (< 128 bits): implemented as 5-cycle atomic read-modify-write
*   Atomic operations: increment, compare-and-swap (5 cycles)
*   Near-memory accumulate (FP32/FP16/BF16/INT32): 2-5 cycles depending on mode

**Bank conflicts** occur when multiple ports access the same bank simultaneously—all but one port must wait. **Port conflicts** occur when multiple clients share a port.

### Access Port Assignment

L1 provides 16 independently arbitrated access ports. Each port connects to one or more clients, with round-robin arbitration when conflicts occur:

| Port # | Primary Client(s) | Secondary Client(s) | Bandwidth |
| --- | --- | --- | --- |
| 0 | RISC-V B load | — | 32-bit read / 7 cycles (~18.3 bpc sustained) |
| 1 | RISC-V B store, ifetch | — | 32-bit write / 5 cycles (~6.4 bpc) |
| 2 | Unpacker 0 read | Packer 0 write, Packer 2 write | 128-bit read/write per cycle |
| 3 | Unpacker 0 read | — | 128-bit read per cycle |
| 4 | Unpacker 0 read | — | 128-bit read per cycle |
| 5 | Unpacker 1 read | Packer 0 write, Packer 0 read (L1→L1) | 128-bit read/write per cycle |
| 6 | — | Packer 3 write | 128-bit write per cycle |
| 7 | Mover write | — | ~93.1 bpc (8×128-bit / 11 cycles) |
| 8 | Mover read | — | ~93.1 bpc (8×128-bit / 11 cycles) |
| 9 | RISC-V T0 load | — | 32-bit read / 7+ cycles (~18.3 bpc) |
| 10 | RISC-V T0 store, ifetch | Scalar Unit (ThCon) | 32-bit write / 5 cycles (~6.4 bpc) |
| 11 | RISC-V T1 load | — | 32-bit read / 7+ cycles |
| 12 | RISC-V T1 store, ifetch | — | 32-bit write / 5 cycles |
| 13 | RISC-V T2 load | — | 32-bit read / 7+ cycles |
| 14 | RISC-V T2 store, ifetch | RISC-V NC load/store/ifetch | 32-bit write / 5 cycles |
| 15 | NIU #0 read/write (2×128-bit each) | NIU #1 read/write (2×128-bit each), debug clients | 256-bit read + 256-bit write per NoC |

**Peak Theoretical Bandwidths:**

*   **NoC (per NoC)**: 256 bits read/cycle + 256 bits write/cycle
*   **Unpackers (both active)**: 5×128-bit reads/cycle + BFP exponent stream
*   **Packers (all active)**: 4×128-bit writes/cycle or 4×128-bit accumulates / 5 cycles (atomic) or / 2 cycles (non-atomic)
*   **Mover**: ~93.1 bits read/cycle + ~93.1 bits write/cycle (measured)
*   **RISC-V cores**: ~18.3 bits read/cycle, ~6.4 bits write/cycle (narrow access penalty)

**Sources:**[WormholeB0/TensixTile/L1.md 15-70](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1#L15-L70)

### RISC-V Performance Characteristics

RISC-V cores have **poor L1 performance** compared to other clients:

*   **Stores**: Always narrow (32-bit), thus 5-cycle read-modify-write → 6.4 bits/cycle peak
*   **Loads**: Sustained throughput ~4 loads every 7 cycles (18.3 bits/cycle)
*   **Dependent loads**: One every 8 cycles (4 bits/cycle)

RISC-V cores are **strongly encouraged** to use other clients (Mover, Unpackers/Packers, NoC, Scalar Unit) for L1 access rather than directly accessing L1 themselves.

For detailed L1 access patterns and optimization, see [Memory Hierarchy and L1 RAM](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.2-memory-hierarchy-and-l1-ram).

**Sources:**[WormholeB0/TensixTile/L1.md 1-70](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1#L1-L70)

## Data Movement Engines

Two specialized DMA engines provide efficient data movement without consuming RISC-V cycles:

### Mover (Local L1 DMA)

The **Mover** performs DMA within a single tile's L1 RAM. It has dedicated read and write connections to L1, achieving:

*   Measured: 8×128-bit reads and 8×128-bit writes every 11 cycles
*   Theoretical: ~93.1 bits read/cycle + ~93.1 bits write/cycle

RISC-V cores configure the Mover, then issue commands. The Mover operates autonomously while RISC-V cores perform other tasks. It is controlled via memory-mapped registers or Tensix `XMOV` instructions.

### TDMA-RISC (NoC DMA Engine)

**TDMA-RISC** (Tensor DMA RISC) performs DMA between:

*   Local L1 and remote tiles (via NoC)
*   Local L1 and DRAM tiles
*   Local L1 and host memory (via PCIe)

RISC-V NC typically programs TDMA-RISC by writing to its memory-mapped configuration registers at `RISCV_TDMA_REGS_START_ADDR` (0xFFB1_1000 - 0xFFB1_1FFF). TDMA-RISC then autonomously manages NoC transactions, freeing RISC-V NC for other tasks.

Both engines are essential for achieving high memory bandwidth without saturating RISC-V instruction execution. See [Data Movement: Mover and TDMA-RISC](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.4-programmable-interrupt-controller-(pic)) for detailed programming interfaces.

**Sources:**[WormholeB0/TensixTile/L1.md 31-33](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1#L31-L33)[WormholeB0/TensixTile/README.md 14](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L14-L14)[WormholeB0/TensixTile/BabyRISCV/README.md 80-106](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L80-L106)

## Inter-Core Communication

The five RISC-V cores within a tile communicate via three mechanisms:

### Mailboxes

**Mailboxes** are FIFO queues between pairs of RISC-V cores, mapped to addresses `TENSIX_MAILBOX0_BASE` through `TENSIX_MAILBOX3_BASE` (0xFFEC_0000 - 0xFFEC_3FFF). They provide:

*   Atomic push and pop operations
*   Blocking reads (wait for non-empty)
*   Typical use: passing control messages between cores

### PCBufs (Program Counter Buffers)

**PCBufs** are specialized control buffers from RISC-V B to each T-core (T0, T1, T2), mapped at `PC_BUF_BASE`, `PC1_BUF_BASE`, `PC2_BUF_BASE`. RISC-V B writes control words; T-cores read them. Typical use: RISC-V B signals T-cores to begin executing specific functions.

### TTSync (Synchronization Primitives)

**TTSync** provides synchronization between RISC-V B and T-cores, allowing:

*   Wait for T-core to reach a specific point in execution
*   Signal completion of tasks
*   Coordinate MOP Expander configuration changes

Mapped at addresses starting at 0xFFE8_0004, with separate instances for T0, T1, T2.

These mechanisms enable efficient coordination without polling L1 memory or consuming excessive cycles. See [Inter-Core Communication](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.3-inter-core-communication) for detailed usage.

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 80-106](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L80-L106)[WormholeB0/TensixTile/L1.md 64-69](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1#L64-L69)

## NoC Connectivity

Each Tensix Tile connects to both NoC #0 and NoC #1 via two **NIU (NoC Interface Units)**. The NoCs provide:

*   **2D torus topology** connecting all tiles
*   **256-bit bidirectional links** per NoC
*   **Independent operation** (NoC #0 and #1 can operate simultaneously)
*   **Transaction types**: reads, writes, atomics

RISC-V cores configure NoC transactions by writing to memory-mapped registers:

*   NoC 0: `NOC0_REGS_START_ADDR` (0xFFB2_0000 - 0xFFB2_FFFF)
*   NoC 1: `NOC1_REGS_START_ADDR` (0xFFB3_0000 - 0xFFB3_FFFF)

Each NoC has **four 128-bit connections to L1** (2 read, 2 write), allowing:

*   Theoretical: 256 bits read/cycle and 256 bits write/cycle per NoC
*   Can read from one tile's L1, transit multiple hops, and write to another tile's L1
*   Can perform "local" transactions (same tile) without using network fabric

The dual NoC design provides redundancy, bandwidth, and load balancing. For NoC programming details, see [Network-on-Chip](https://deepwiki.com/tenstorrent/tt-isa-documentation/2-network-on-chip-(noc)).

**Sources:**[WormholeB0/TensixTile/README.md 6](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L6-L6)[WormholeB0/TensixTile/L1.md 51-53](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1#L51-L53)[WormholeB0/TensixTile/BabyRISCV/README.md 80-106](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L80-L106)

## NoC Overlay and PIC

### NoC Overlay

The **NoC Overlay** is a stream-based coprocessor that assists with NoC transactions. It can:

*   Autonomously move data streams between tiles
*   Automatically configure streams from L1-stored descriptors
*   Raise interrupts upon phase transitions

Configuration mapped at `NOC_OVERLAY_START_ADDR` (0xFFB4_0000 - 0xFFB7_FFFF). Particularly useful for pipelined data movement patterns. For details, see [NoC Overlay Streams](https://deepwiki.com/tenstorrent/tt-isa-documentation/3-tensix-tile-architecture)#2.5).

### PIC (Programmable Interrupt Controller)

The **PIC** tracks 11 IRQ sources including:

*   NoC Overlay stream events (phase start/end)
*   Debug timestamper full conditions
*   NoC Overlay automatic configuration completion

The PIC can optionally **clock-gate RISC-V NC** when no IRQs are pending, saving power. RISC-V cores poll for IRQs via `PIC_STATUS` (0xFFB1_3000) rather than using interrupt vectors.

Configuration mapped at addresses 0xFFB1_3000 - 0xFFB1_3FFF. See [Programmable Interrupt Controller](https://deepwiki.com/tenstorrent/tt-isa-documentation/3-tensix-tile-architecture)#3.5) for detailed programming.

**Sources:**[WormholeB0/TensixTile/README.md 7](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L7-L7)[WormholeB0/TensixTile/PIC.md 1-101](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/PIC.md?plain=1#L1-L101)[WormholeB0/TensixTile/BabyRISCV/README.md 80-106](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L80-L106)

## Memory Map Summary

The complete Tensix Tile address space shows clear separation between data RAM (low addresses) and control registers (high addresses). Some regions have **per-core mappings** where the same address accesses different resources depending on which RISC-V core performs the access.

| Constant Name | Address Range | Access (B/T0/T1/T2/NC) | NoC | Description |
| --- | --- | --- | --- | --- |
| `MEM_L1_BASE` | 0x0000_0000 - 0x0016_FFFF | All cores | Yes | L1 scratchpad RAM (1464 KiB, 16 banks) |
| `MEM_LOCAL_BASE` | 0xFFB0_0000 - 0xFFB0_0FFF | Per-core view | No | Local data RAM: 4 KiB (B, NC), 2 KiB (T0/T1/T2) |
| `RISCV_TDMA_REGS_START_ADDR` | 0xFFB1_1000 - 0xFFB1_1FFF | All cores | Yes | TDMA-RISC configuration and command registers |
| `RISCV_DEBUG_REGS_START_ADDR` | 0xFFB1_2000 - 0xFFB1_2FFF | All cores | Yes | Tile control / debug / status (includes `SOFT_RESET_0`) |
| — | 0xFFB1_3000 - 0xFFB1_3FFF | All cores | Yes | PIC registers: `PIC_STATUS`, `PIC_ENABLE`, `PIC_CLKGT_EN` |
| `NOC0_REGS_START_ADDR` | 0xFFB2_0000 - 0xFFB2_FFFF | All cores | Yes | NoC 0 NIU configuration and command interface |
| `NOC1_REGS_START_ADDR` | 0xFFB3_0000 - 0xFFB3_FFFF | All cores | Yes | NoC 1 NIU configuration and command interface |
| `NOC_OVERLAY_START_ADDR` | 0xFFB4_0000 - 0xFFB7_FFFF | All cores | Yes | NoC Overlay stream configuration (256 KiB) |
| `TENSIX_MOP_CFG_BASE` | 0xFFB8_0000 - 0xFFB8_0023 | T0/T1/T2 only | No | MOP Expander `MopCfg[9]` arrays (write-only, per-thread) |
| `MEM_NCRISC_IRAM_BASE` | 0xFFC0_0000 - 0xFFC0_FFFF | NC only | No | RISC-V NC instruction RAM (64 KiB, not LSU-accessible) |
| `REGFILE_BASE` | 0xFFE0_0000 - 0xFFE0_0FFF | T0/T1/T2, B | No | Tensix Scalar Unit `GPRs[3][64]` (per-thread) |
| `INSTRN_BUF_BASE` | 0xFFE4_0000 - 0xFFE4_FFFF | B: post-MOP T0 T0/T1/T2: pre-MOP | No | Tensix instruction push (per-core view) |
| `INSTRN1_BUF_BASE` | 0xFFE5_0000 - 0xFFE5_FFFF | B only | No | Tensix T1 post-MOP instruction push |
| `INSTRN2_BUF_BASE` | 0xFFE6_0000 - 0xFFE6_FFFF | B only | No | Tensix T2 post-MOP instruction push |
| `PC_BUF_BASE` | 0xFFE8_0000 - 0xFFE8_0003 | B: write side T0/T1/T2: read side | No | PCBuf FIFOs (B → T0/T1/T2) |
| — | 0xFFE8_0004 - 0xFFE8_001F | T0/T1/T2 only | No | TTSync registers (per-thread synchronization) |
| — | 0xFFE8_0020 - 0xFFE8_FFFF | B/T0/T1/T2 | No | Tensix semaphores (backend synchronization) |
| `PC1_BUF_BASE` | 0xFFE9_0000 - 0xFFE9_FFFF | B only | No | PCBuf FIFO (B → T1, B side write) |
| `PC2_BUF_BASE` | 0xFFEA_0000 - 0xFFEA_FFFF | B only | No | PCBuf FIFO (B → T2, B side write) |
| `TENSIX_MAILBOX0_BASE` | 0xFFEC_0000 - 0xFFEC_0FFF | B/T0/T1/T2 | No | Mailbox FIFOs (4×4 matrix between B/T0/T1/T2) |
| `TENSIX_MAILBOX1_BASE` | 0xFFEC_1000 - 0xFFEC_1FFF | B/T0/T1/T2 | No | Mailbox FIFOs |
| `TENSIX_MAILBOX2_BASE` | 0xFFEC_2000 - 0xFFEC_2FFF | B/T0/T1/T2 | No | Mailbox FIFOs |
| `TENSIX_MAILBOX3_BASE` | 0xFFEC_3000 - 0xFFEC_3FFF | B/T0/T1/T2 | No | Mailbox FIFOs |
| `TENSIX_CFG_BASE` | 0xFFEF_0000 - 0xFFEF_FFFF | B/T0/T1/T2 | No | Tensix backend: `Config[2][CFG_STATE_SIZE×4]`, `ThreadConfig[3][THD_STATE_SIZE]` |

**Key Patterns:**

*   **0x0000_0000 - 0x001F_FFFF**: Data RAM (L1 scratchpad)
*   **0xFFB0_0000 - 0xFFB7_FFFF**: Control registers for data movement (local RAM, TDMA, NoC, Overlay)
*   **0xFFB8_0000 - 0xFFC0_FFFF**: Tensix frontend configuration (MOP, NC IRAM)
*   **0xFFE0_0000 - 0xFFEF_FFFF**: Tensix backend (GPRs, instruction push, inter-core comm, configuration)

Most regions with "All cores" access are also NoC-accessible (exceptions: local RAM, MOP config). Tensix-specific regions (0xFFB8_0000+) are only accessible to cores that interact with the Tensix Coprocessor (B, T0, T1, T2).

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 76-106](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L76-L106)[WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md 1-20](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md?plain=1#L1-L20)

## Soft Reset Mechanism

Register `RISCV_DEBUG_REG_SOFT_RESET_0` at address 0xFFB1_2000 provides granular soft reset control. Each bit independently controls specific functional blocks. Software performs read-modify-write sequences (no atomic bit set/clear hardware).

**Bit Assignments and Effects:**

| Bit(s) | Constant Name | Subsystem | Enter Reset | While Held | Exit Reset |
| --- | --- | --- | --- | --- | --- |
| 0, 1, 7 | — | Unpackers | Abort `UNPACR`/`UNPACR_NOP` | Discard new instructions | No action |
| 2 | — | Packer 0 | Abort `PACR`/`PACR_SETREG`, clear buffers, zero `AccTileSize`/`LastTileSize` | Discard new instructions | Set `l1_dest_addr_offset = 0` |
| 3 | — | Packer 1 | Same as Packer 0 | Same | Same |
| 4 | — | Packer 2 | Same as Packer 0 | Same | Same |
| 5 | — | Packer 3 | Same as Packer 0 | Same | Same |
| 6 | — | Mover | Abort in-flight | Discard `XMOV`, TDMA mover commands | No action |
| 8 | — | TDMA-RISC, Glue | Set `SetRegBase`, `SetRegHiScaler`, clear mover command queue | Discard TDMA writes | No action |
| 9 | — | THCON Config, Scalar Unit | Zero `Config[0/1][THCON_CFGREG_BASE:GLOBAL_CFGREG_BASE]` | Discard config writes, block most ThCon instructions | No action |
| 10 | — | Matrix Unit, Vector Unit, SrcA (all) | Abort FPU/SFPU instructions, zero all SrcA columns | Block new FPU/SFPU instructions, discard SrcA writes | Reset PRNGs, zero `LaneConfig`, `LoadMacroConfig`, reset `LReg` |
| 11 | `RISCV_SOFT_RESET_0_BRISC` | RISC-V B | Abort instructions, discard MOP FIFO, clear mailboxes, zero debug `DR` regs | Block new instructions | Clear PCBuf B→T i, invalidate I-cache, zero GPRs, set `pc` |
| 12 | `RISCV_SOFT_RESET_0_TRISCS` | RISC-V T0 | Same as B | Same | Same, `pc = Config.TRISC_RESET_PC_SEC0_PC` or 0x06000 |
| 13 | `RISCV_SOFT_RESET_0_TRISCS` | RISC-V T1 | Same as B | Same | Same, `pc = Config.TRISC_RESET_PC_SEC1_PC` or 0x0A000 |
| 14 | `RISCV_SOFT_RESET_0_TRISCS` | RISC-V T2 | Same as B | Same | Same, `pc = Config.TRISC_RESET_PC_SEC2_PC` or 0x0E000 |
| 15 | — | SrcA `AllowedClient` | `MatrixUnit.SrcABank = 0`, `Unpackers[0].SrcBank = 0`, `SrcA[0/1].AllowedClient = SrcClient::Unpackers` | Block state changes | No action |
| 16 | — | SrcB `AllowedClient`, SrcB data | `MatrixUnit.SrcBBank = 0`, `Unpackers[1].SrcBank = 0`, `SrcB[0/1].AllowedClient = SrcClient::Unpackers`, zero all SrcB columns | Block state changes, discard SrcB writes | No action |
| 17 | — | Packer→Dst, ZEROACC | Invoke `ExponentHistogram.Reset()` on all packers | `PACR` reading Dst behaves strangely, discard `ZEROACC` | Reset Packer stochastic rounding PRNGs |
| 18 | `RISCV_SOFT_RESET_0_NCRISC` | RISC-V NC | Same as B | Same | Same, `pc = Config.NCRISC_RESET_PC_PC` or 0x12000 |
| 19 | — | SrcA data (columns 0-3) | Zero specified columns | Discard writes to columns | No action |
| 20 | — | SrcA data (columns 4-7) | Zero specified columns | Discard writes | No action |
| 21 | — | SrcA data (columns 8-11) | Zero specified columns | Discard writes | No action |
| 22 | — | SrcA data (columns 12-15) | Zero specified columns | Discard writes | No action |
| ≥23 | — | Reserved | No effect | No effect | No effect |

**Preferred Software Alternatives:**

*   Instead of SrcA/SrcB data reset (bits 10, 16, 19-22): Use `ZEROSRC` instruction
*   Instead of SrcA/SrcB `AllowedClient` reset (bits 15, 16): Use `CLEARDVALID` instruction
*   Instead of THCON config reset (bit 9): Write to `Config[i][STATE_RESET_EN_ADDR32]`
*   Instead of ZEROACC state reset (bit 17): Use `CLREXPHIST` instruction

**Typical Usage:**

1.   **Kernel initialization**: Assert reset bits for relevant subsystems, configure backend, deassert reset
2.   **Kernel switching**: Assert compute unit resets (bits 10, etc.), reconfigure, deassert
3.   **Error recovery**: Reset specific subsystem without affecting others

**Sources:**[WormholeB0/TensixTile/SoftReset.md 1-213](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/SoftReset.md?plain=1#L1-L213)[WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md 59-73](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md?plain=1#L59-L73)

## Typical Execution Model

A typical Tensix Tile execution sequence:

1.   **Initialization** (RISC-V B):

    *   Configure NoC overlay streams for data delivery
    *   Set up TDMA-RISC for memory transfers
    *   Initialize Tensix backend configuration
    *   Wake T-cores via PCBufs

2.   **Data Movement** (RISC-V B, NC):

    *   RISC-V B coordinates local data movement (Mover)
    *   RISC-V NC handles NoC-based transfers (TDMA-RISC)
    *   Data staged in L1 for coprocessor consumption

3.   **Compute Orchestration** (RISC-V T0, T1, T2):

    *   **T0**: Pushes `UNPACR` instructions → load data into SrcA/SrcB/Dst
    *   **T1**: Pushes `MVMUL`/`SFPMAD` instructions → perform computation
    *   **T2**: Pushes `PACR` instructions → store results back to L1

4.   **Synchronization**:

    *   T-cores use TTSync to coordinate with each other
    *   RISC-V B polls NoC counters or uses Mailboxes to detect completion
    *   Semaphores synchronize access to shared resources

5.   **Output Movement** (RISC-V B, NC):

    *   Move results from L1 to remote tiles or DRAM
    *   Signal host via NoC or prepare for next computation

This model achieves **instruction-level parallelism** (MOP/Replay expansion), **thread-level parallelism** (3 Tensix threads), and **data-level parallelism** (Matrix/Vector SIMD operations) while RISC-V cores focus on control logic rather than computation.

**Sources:**[WormholeB0/TensixTile/README.md 1-17](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L1-L17)[WormholeB0/TensixTile/BabyRISCV/README.md 13-14](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L13-L14)[WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md 1-6](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md?plain=1#L1-L6)

## Related Documentation

For detailed information on specific subsystems:

*   [Baby RISC-V Cores](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.1-baby-risc-v-cores) - Instruction set, pipeline, memory ordering
*   [Memory Hierarchy and L1 RAM](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.2-memory-hierarchy-and-l1-ram) - Bank conflicts, access patterns, optimization
*   [Inter-Core Communication](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.3-inter-core-communication) - Mailboxes, PCBufs, TTSync programming
*   [Data Movement](https://deepwiki.com/tenstorrent/tt-isa-documentation/3.4-programmable-interrupt-controller-(pic)) - Mover and TDMA-RISC configuration
*   [PIC](https://deepwiki.com/tenstorrent/tt-isa-documentation/3-tensix-tile-architecture)#3.5) - Interrupt handling and clock gating
*   [Tensix Coprocessor Architecture](https://deepwiki.com/tenstorrent/tt-isa-documentation/4-tensix-coprocessor-architecture) - Detailed compute unit internals
*   [Network-on-Chip](https://deepwiki.com/tenstorrent/tt-isa-documentation/2-network-on-chip-(noc)) - NoC programming and transaction ordering

Dismiss
Refresh this wiki

Enter email to refresh