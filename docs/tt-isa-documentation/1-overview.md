---
project: tt-isa-documentation
github: https://github.com/tenstorrent/tt-isa-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-isa-documentation/1-overview
---

# Overview

Relevant source files
*   [Diagrams/Out/Bits32_MOVB2A.svg](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Out/Bits32_MOVB2A.svg)
*   [Diagrams/Src/BabyRISCV.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/BabyRISCV.lua)
*   [Diagrams/Src/Bits32.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua)
*   [Diagrams/Src/EdgeTile.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/EdgeTile.lua)
*   [Diagrams/Src/L1.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/L1.lua)
*   [Diagrams/Src/NoC.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/NoC.lua)
*   [Diagrams/Src/PackerPipeline.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/PackerPipeline.lua)
*   [Diagrams/Src/TensixFrontend.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/TensixFrontend.lua)
*   [README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/README.md?plain=1)
*   [WormholeB0/EthernetTile/BabyRISCV/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/EthernetTile/BabyRISCV/README.md?plain=1)
*   [WormholeB0/NoC/Alignment.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Alignment.md?plain=1)
*   [WormholeB0/NoC/Counters.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Counters.md?plain=1)
*   [WormholeB0/NoC/MemoryMap.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1)
*   [WormholeB0/NoC/Ordering.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Ordering.md?plain=1)
*   [WormholeB0/NoC/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1)
*   [WormholeB0/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1)
*   [WormholeB0/TensixTile/BabyRISCV/Mailboxes.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/Mailboxes.md?plain=1)
*   [WormholeB0/TensixTile/BabyRISCV/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1)
*   [WormholeB0/TensixTile/L1.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1)
*   [WormholeB0/TensixTile/PIC.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/PIC.md?plain=1)
*   [WormholeB0/TensixTile/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1)
*   [WormholeB0/TensixTile/SoftReset.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/SoftReset.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/BackendConfiguration.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOPExpander.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/MOVB2A.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOVB2A.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/Packers/InputAddressGenerator.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Packers/InputAddressGenerator.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/Packers/OutputAddressGenerator.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Packers/OutputAddressGenerator.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/RWCs.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/RWCs.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/SETDMAREG_Special.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/SETDMAREG_Special.md?plain=1)

This documentation describes the Instruction Set Architecture (ISA) and hardware architecture of Tenstorrent's Wormhole B0 and Blackhole A0 AI accelerator ASICs. It provides comprehensive technical reference for programming these chips, covering:

*   Hardware architecture from ASIC-level down to individual execution units
*   Instruction set specifications for RISC-V cores and specialized coprocessors
*   Data formats, memory addressing, and transaction protocols
*   NoC (Network-on-Chip) communication mechanisms
*   Programming models and optimization techniques

For architectural differences between Wormhole B0 and Blackhole A0, see [Architecture Comparison](https://deepwiki.com/tenstorrent/tt-isa-documentation/1.1-architecture-comparison-(wormhole-vs-blackhole)). For detailed tile architecture and interconnect topology, see [Chip Architecture and Tile Types](https://deepwiki.com/tenstorrent/tt-isa-documentation/1.2-chip-architecture-and-tile-types). For information about the SVG diagram generation system, see [Documentation System](https://deepwiki.com/tenstorrent/tt-isa-documentation/1-overview#1.3).

## ASIC Architecture Overview

**Diagram: ASIC-Level Architecture**

The ASIC contains 120+ tiles organized into three functional categories, all interconnected by dual independent NoCs. Each NoC implements a 2D torus topology with routers at every tile, providing redundant paths and load balancing. The dual NoC design (NoC #0 and NoC #1) flow in opposite directions, with NoC #0 using X-major routing and NoC #1 using Y-major routing.

**Sources:**[WormholeB0/README.md 1-14](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1#L1-L14)[WormholeB0/NoC/README.md 1-23](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L1-L23)[Diagrams/Src/NoC.lua 1-331](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/NoC.lua#L1-L331)

## Tensix Tile Architecture

The Tensix tile is the primary compute unit, containing heterogeneous processing elements designed for AI workloads:

**Diagram: Tensix Tile Architecture with Memory-Mapped Addresses**

The Tensix tile implements a three-layer architecture:

1.   **Control Layer**: Five Baby RISC-V cores (RV32IM ISA) orchestrate execution. RISC-V B handles data movement and controls the other cores. RISC-V T0/T1/T2 specialize in different compute phases (UNPACK/MATH/PACK), pushing instructions to their respective execution units via memory-mapped interfaces at `INSTRN_BUF_BASE` (0xFFE40000).

2.   **Compute Layer**: The Tensix Coprocessor contains specialized units for different operations. The Matrix Unit (FPU) performs low-precision (19-bit) operations at high throughput. The Vector Unit (SFPU) handles FP32 operations across 32 lanes. Both units operate on shared register files (SrcA, SrcB, Dst).

3.   **Memory/Communication Layer**: 1464 KiB of L1 RAM organized into 16 banks enables parallel access. The Mover handles local DMA, while TDMA-RISC orchestrates NoC-based transfers. Mailboxes provide inter-core communication.

**Sources:**[WormholeB0/TensixTile/README.md 1-17](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L1-L17)[WormholeB0/TensixTile/BabyRISCV/README.md 1-120](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L1-L120)[WormholeB0/TensixTile/BabyRISCV/README.md 78-106](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L78-L106)

## Execution Model

**Diagram: Instruction Processing Pipeline**

The execution pipeline transforms high-level kernels into hardware operations through multiple expansion stages:

1.   **Software Layer**: Kernels compiled to RV32IM instructions execute on RISC-V cores
2.   **Instruction Expansion**: MOP Expander at `TENSIX_MOP_CFG_BASE` (0xFFB80000) expands single instructions into thousands of micro-operations. The Replay Expander further unrolls sequences.
3.   **Synchronization**: Wait Gate checks semaphores (at 0xFFE80020) and configuration state before dispatch
4.   **Execution**: Matrix and Vector units operate in parallel on distinct data paths

The RISC-V cores push instructions via memory-mapped writes to `INSTRN_BUF_BASE` addresses, allowing precise control over timing and sequencing.

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 78-106](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L78-L106)[Diagrams/Src/TensixFrontend.lua 1-203](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/TensixFrontend.lua#L1-L203)

## Memory Hierarchy

**Diagram: Memory Hierarchy and Access Latencies**

The memory hierarchy provides multiple levels optimized for different access patterns:

*   **Local RAM**: Core-private 2-4 KiB at `MEM_LOCAL_BASE` with 2-cycle latency, ideal for stack and frequently-accessed variables
*   **L1 RAM**: 1464 KiB shared scratchpad at `MEM_L1_BASE` (0x00000000) with ≥8 cycle latency, organized into 16 banks to support parallel access from multiple clients (defined in [WormholeB0/TensixTile/L1.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/L1.md?plain=1))
*   **Coprocessor Registers**: SrcA/SrcB (64x16 each), Dst (1024x16), and LReg (32x17) provide high-bandwidth operand storage
*   **Remote Access**: NoC transactions to other tiles' L1 or DRAM, initiated via `NOC0_REGS_START_ADDR` (0xFFB20000) and `NOC1_REGS_START_ADDR` (0xFFB30000)

Each L1 bank can service one access per cycle, but bank conflicts introduce additional latency. Software must carefully orchestrate address patterns to maximize throughput.

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 60-66](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L60-L66)[WormholeB0/TensixTile/BabyRISCV/README.md 117-120](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L117-L120)[Diagrams/Src/L1.lua 1-230](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/L1.lua#L1-L230)

## NoC Transaction Architecture

**Diagram: NoC Transaction Flow with Register Names**

NoC transactions follow a multi-stage protocol:

1.   **Initiation**: Software configures a request initiator by writing to registers at `NIU_BASE + i * NOC_CMD_BUF_OFFSET` (i ∈ {0,1,2,3}). Key registers:

    *   `NOC_TARG_ADDR_LO/MID`: 36-bit address + 6-bit X/Y coordinates for destination
    *   `NOC_RET_ADDR_LO/MID`: Return address for read responses
    *   `NOC_CTRL`: Request type (read/write/atomic), flags (`NOC_CMD_RESP_MARKED`, `NOC_CMD_VC_STATIC`, `NOC_CMD_BRCST_PACKET`)
    *   `NOC_AT_LEN_BE`: Length (for reads/writes) or atomic opcode
    *   `NOC_CMD_CTRL`: Writing 1 to bit 0 initiates the request

2.   **Virtual Channel Assignment**: Hardware assigns a 4-bit VC number (dateline | class[2] | buddy) to prevent deadlock. Class bits distinguish request types: 0b00/0b01 for unicast requests, 0b10 for broadcasts, 0b11 for responses.

3.   **Routing**: Packets traverse routers with 9-cycle latency per hop. NoC #0 uses X-major routing, NoC #1 uses Y-major routing.

4.   **Completion Tracking**: Counters at `NIU_BASE + 0x200` enable software to monitor progress:

    *   `NIU_MST_REQS_OUTSTANDING_ID(i)`: Incremented on request, decremented on completion
    *   `NIU_MST_WR_ACK_RECEIVED`: Counts write acknowledgements
    *   `NIU_MST_RD_RESP_RECEIVED`: Counts read responses

**Sources:**[WormholeB0/NoC/MemoryMap.md 30-157](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L30-L157)[WormholeB0/NoC/Counters.md 1-174](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Counters.md?plain=1#L1-L174)[WormholeB0/NoC/README.md 23-52](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L23-L52)

## Data Format Support

The architecture supports heterogeneous data formats optimized for AI workloads:

| **Register File** | **Supported Formats** | **Precision** | **Use Case** |
| --- | --- | --- | --- |
| SrcA, SrcB | BF16, FP16, FP8, INT8/16, BFP2/4/8 | 19-bit | Matrix Unit inputs |
| Dst | BF16, FP16, FP32, FP8, INT8/16/32 | 16/32-bit | Accumulator |
| LReg | FP32 | 32-bit | Vector Unit operations |
| L1 | All formats + compressed | Variable | Storage and movement |

**Table: Data Format Support by Register Type**

The Unpacker units (`UNPACR`) perform format conversion when loading from L1 to registers. The Packer units (`PACR`) perform reverse conversion when storing from Dst back to L1. Block Floating-Point (BFP) formats use shared exponents across 16 datums to reduce memory bandwidth requirements.

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/Unpackers/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Unpackers/README.md?plain=1)[WormholeB0/TensixTile/TensixCoprocessor/Packers/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Packers/README.md?plain=1)

## Key Addressing Constants

Critical memory-mapped addresses for programming:

| **Symbol** | **Address** | **Purpose** | **Access** |
| --- | --- | --- | --- |
| `MEM_L1_BASE` | 0x00000000 | L1 scratchpad RAM (1464 KiB) | All cores, NoC |
| `MEM_LOCAL_BASE` | 0xFFB00000 | Core-local data RAM (2-4 KiB) | Per-core only |
| `RISCV_TDMA_REGS_START_ADDR` | 0xFFB11000 | TDMA-RISC configuration | All cores |
| `NOC0_REGS_START_ADDR` | 0xFFB20000 | NoC #0 configuration | All cores |
| `NOC1_REGS_START_ADDR` | 0xFFB30000 | NoC #1 configuration | All cores |
| `TENSIX_MOP_CFG_BASE` | 0xFFB80000 | MOP expander configuration | T0/T1/T2 |
| `MEM_NCRISC_IRAM_BASE` | 0xFFC00000 | NC core instruction RAM (16 KiB) | NC only |
| `REGFILE_BASE` | 0xFFE00000 | Tensix GPRs | T0/T1/T2 |
| `INSTRN_BUF_BASE` | 0xFFE40000 | Push Tensix instructions | T0/T1/T2 |
| `TENSIX_MAILBOX0_BASE` | 0xFFEC0000 | Inter-core mailboxes | All cores |
| `TENSIX_CFG_BASE` | 0xFFEF0000 | Tensix backend configuration | B/T0/T1/T2 |

**Table: Memory Map Constants**

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 78-106](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L78-L106)

## Programming Model Summary

Software follows a hierarchical dispatch model:

1.   **Host Software**: Dispatches work to the ASIC via PCIe (NoC transactions initiated at PCI Express tile)
2.   **RISC-V B/NC**: Orchestrate data movement between L1, DRAM, and other tiles using NoC and TDMA-RISC
3.   **RISC-V T0/T1/T2**: Push compute instructions to Tensix Coprocessor execution units via `INSTRN_BUF_BASE`
4.   **Tensix Coprocessor**: MOP/Replay expanders multiply instruction throughput, feeding Unpackers, Matrix/Vector Units, and Packers
5.   **Synchronization**: Mailboxes (at `TENSIX_MAILBOX0_BASE`), semaphores (at 0xFFE80020), and NoC counters coordinate execution

The dual NoC design enables concurrent data movement and computation, while the specialized RISC-V cores and coprocessor units provide both programmability and high performance for AI workloads.

**Sources:**[WormholeB0/TensixTile/README.md 1-17](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L1-L17)[WormholeB0/TensixTile/BabyRISCV/README.md 1-120](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L1-L120)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-isa-documentation/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh