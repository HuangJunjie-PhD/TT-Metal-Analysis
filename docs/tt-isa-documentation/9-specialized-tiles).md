---
project: tt-isa-documentation
github: https://github.com/tenstorrent/tt-isa-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-isa-documentation/9-specialized-tiles)
---

# Specialized Tiles

Relevant source files
*   [Diagrams/Src/BabyRISCV.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/BabyRISCV.lua)
*   [Diagrams/Src/Bits32.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/Bits32.lua)
*   [Diagrams/Src/EdgeTile.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/EdgeTile.lua)
*   [Diagrams/Src/L1.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/L1.lua)
*   [Diagrams/Src/NoC.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/NoC.lua)
*   [Diagrams/Src/PackerPipeline.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/PackerPipeline.lua)
*   [Diagrams/Src/TensixFrontend.lua](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/TensixFrontend.lua)
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

This page provides an overview of the specialized (non-Tensix) tile types in the Wormhole and Blackhole architectures. These tiles provide I/O, memory, and management functions that complement the Tensix compute tiles but do not contain the Tensix coprocessor.

**Key Specialized Tile Types:**

| Tile Type | Primary Function | Quantity (Wormhole) | Programmable |
| --- | --- | --- | --- |
| **Ethernet** | Chip-to-chip networking | 16 | Yes (1× RISC-V E) |
| **PCIe** | Host-device communication | 1 | No |
| **DRAM** | Off-chip GDDR6 memory | 18 | No |
| **ARC** | Chip management | 1 | No (firmware only) |
| **L2CPU** (Blackhole only) | General-purpose compute | 4 | Yes (1120 RISC-V harts) |

This page covers the high-level architecture and common characteristics. For detailed subsystem information:

*   Ethernet tile TX/RX subsystems and protocol details → [Ethernet Tile Architecture](https://deepwiki.com/tenstorrent/tt-isa-documentation/9.1-ethernet-tile-architecture)
*   PCIe translation mechanisms and ARC management functions → [PCIe and ARC Tiles](https://deepwiki.com/tenstorrent/tt-isa-documentation/9.2-edge-tiles:-pcie-arc-and-l2cpu)
*   DRAM controller architecture and L2CPU organization → [DRAM Tiles](https://deepwiki.com/tenstorrent/tt-isa-documentation/9.3-dram-tiles)

**Sources:**[WormholeB0/README.md 3-9](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1#L3-L9)[WormholeB0/NoC/MemoryMap.md 46-47](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L46-L47)

## Tile Distribution and Purpose

Each Wormhole ASIC contains specialized tiles distributed around the chip perimeter for I/O, memory, and management:

**Chip Architecture: Specialized Tiles in Wormhole**

**Sources:**[WormholeB0/README.md 3-9](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1#L3-L9)[WormholeB0/NoC/README.md 5-22](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L5-L22)

## Architectural Comparison

The following table compares all tile types in the Wormhole architecture:

| Feature | Tensix Tile | Ethernet Tile | PCIe Tile | DRAM Tile | ARC Tile |
| --- | --- | --- | --- | --- | --- |
| **Quantity per ASIC** | 64-80 | 16 | 1 | 18 | 1 |
| **Primary Purpose** | AI compute | Chip-to-chip networking | Host communication | Off-chip memory | Chip management |
| **RISC-V Cores** | 5× Baby RISC-V (B, T0, T1, T2, NC) | 1× Baby RISC-V (E) | None | None | 4× ARCv2 cores |
| **L1 Memory** | 1464 KiB | 256 KiB | N/A | N/A | 512 KiB CSM |
| **Coprocessor** | Tensix coprocessor | None | None | None | None |
| **Specialized Hardware** | Matrix Unit, Vector Unit, Unpackers, Packers | Ethernet MAC/PCS, TX/RX queues | PCIe PHY, iATU/oATU, DMA engines | GDDR6 PHY, Memory controller | Reset unit, SPI, I2C, eFuses |
| **External Interface** | None | 100 GbE per tile | PCIe Gen4 x16 | 2× 32-bit GDDR6 channels per tile | Board management interfaces |
| **NoC Connectivity** | 2× NIUs | 2× NIUs | 2× NIUs | 2× NIUs | 2× NIUs |
| **Software Control** | Full customer control | Cooperative firmware | Host-initiated only | Hardware-only | Tenstorrent firmware |
| **Can Initiate NoC Requests** | Yes (all 5 cores) | Yes (RISCV E) | No | No | Yes (ARC cores) |
| **Instruction Cache** | 2 KiB / ½ KiB | 2 KiB | N/A | N/A | Unknown |
| **Instruction RAM** | 16 KiB (NC only) | 16 KiB | N/A | N/A | Unknown |
| **Local Data RAM** | 2-4 KiB per core | 4 KiB | N/A | N/A | Unknown |

**Sources:**[WormholeB0/TensixTile/README.md 3-14](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L3-L14)[WormholeB0/EthernetTile/BabyRISCV/README.md 3-13](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/EthernetTile/BabyRISCV/README.md?plain=1#L3-L13)[WormholeB0/README.md 3-9](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1#L3-L9)[Diagrams/Src/EdgeTile.lua 1-290](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/EdgeTile.lua#L1-L290)

## Ethernet Tiles

Ethernet tiles provide 100 Gbps chip-to-chip networking. Each Wormhole ASIC contains **16 Ethernet tiles**.

**High-Level Ethernet Tile Block Diagram**

**Key Attributes:**

*   **1× RISC-V E core** at `RISCV_E`: Executes cooperative firmware (link management) + customer code (protocol layer)
*   **256 KiB L1** at address `0x0` - `0x3FFFF`: Packet staging and DMA buffers
*   **8 TX queues + 8 RX queues**: Hardware queues at `ETH_TXQ0_REGS_START` (`0xFFB9_0000`) and `ETH_RXQ0_REGS_START` (`0xFFB9_2000`)
*   **2× NIUs**: Standard NoC interfaces at `0xFFB2_0000` (NoC #0) and `0xFFB3_0000` (NoC #1)

**Execution Model:** Tenstorrent firmware running on `RISCV_E` handles link initialization, training, and fault recovery. Customer code registers callbacks for application-level protocol handling.

**→ For TX/RX queue configuration, DMA mechanisms, and packet framing details, see [Ethernet Tile Architecture](https://deepwiki.com/tenstorrent/tt-isa-documentation/9.1-ethernet-tile-architecture)**

**Sources:**[WormholeB0/EthernetTile/BabyRISCV/README.md 3-13](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/EthernetTile/BabyRISCV/README.md?plain=1#L3-L13)[WormholeB0/README.md 6](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1#L6-L6)

## PCIe Tile

The PCIe tile provides host-device communication. Each Wormhole ASIC contains **1 PCIe tile** supporting PCIe Gen4 x16.

**PCIe Transaction Flow (Host-to-Device)**

**Key Components:**

*   **PCIe PHY**: Synopsys DesignWare PCIe controller (Gen4 x16)
*   **iATU** (inbound address translation unit): Translates PCIe addresses to AXI
*   **Configurable TLBs**: Map host virtual addresses to NoC (x, y, addr) tuples
*   **oATU** (outbound address translation unit): Translates device-to-host responses back to PCIe addresses
*   **DMA engines**: Hardware DMA for bulk transfers

**Address Translation:** Host writes to PCIe BAR addresses → iATU translation → TLB lookup → NoC transaction construction → NIU initiates NoC request.

TLB configuration bits control:

*   `Broadcast`: Whether write becomes NoC broadcast to multiple tiles
*   `Ordering mode strict`: Whether AXI ordering rules enforced
*   `Ordering mode posted writes`: Whether writes acknowledged immediately or after NoC completion
*   `Linked`: Whether multiple host transactions form single NoC multi-request transaction
*   `Static`: Whether virtual channel assignment is static or dynamic

**→ For iATU/oATU register details, TLB programming, and ordering semantics, see [PCIe and ARC Tiles](https://deepwiki.com/tenstorrent/tt-isa-documentation/9.2-edge-tiles:-pcie-arc-and-l2cpu)**

**Sources:**[Diagrams/Src/EdgeTile.lua 132-290](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/EdgeTile.lua#L132-L290)[WormholeB0/NoC/Ordering.md 34-46](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Ordering.md?plain=1#L34-L46)

## DRAM Tiles

DRAM tiles provide access to off-chip GDDR6 memory. Each Wormhole ASIC contains **18 DRAM tiles**, collectively exposing **12 GiB**.

**DRAM Tile Block Diagram**

**Key Attributes:**

*   **No RISC-V cores**: Hardware-only memory controller, no software execution
*   **Pure responder**: Cannot initiate NoC requests; only responds to incoming NoC read/write transactions
*   **Dual-channel GDDR6**: Each tile controls 2× 32-bit channels
*   **6 groups of 3 tiles**: Each 2 GiB GDDR6 bank is accessible via 3 DRAM tiles (one tile per NoC, plus one spare)
*   **Total capacity**: 18 tiles × 512 MiB ≈ 9-12 GiB depending on SKU

**Address Space:** DRAM tiles expose the full 2 GiB address range per group. Software accesses DRAM by sending NoC read/write requests to DRAM tile (x, y) coordinates with addresses in the DRAM address range.

**→ For GDDR6 timing parameters, interleaving schemes, and L2CPU tile (Blackhole), see [DRAM Tiles](https://deepwiki.com/tenstorrent/tt-isa-documentation/9.3-dram-tiles)**

**Sources:**[WormholeB0/README.md 5](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1#L5-L5)[WormholeB0/NoC/README.md 15-16](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L15-L16)

## ARC Tile

The ARC tile provides chip-level management. Each Wormhole ASIC contains **1 ARC tile** with ARCv2 processor cores.

**ARC Tile Block Diagram**

**Key Functions:**

*   **Chip initialization**: ARCv2 cores execute boot firmware that configures all tiles
*   **Reset management**: Register `RISCV_DEBUG_REG_SOFT_RESET_0` allows per-tile reset control
*   **TLB programming**: Configures PCIe tile's address translation tables
*   **Board management**: Access to SPI (for flash), I2C (for sensors), eFuses (for chip ID/trim)
*   **Cycle counter**: Provides reference timestamp for debug and profiling

**Software Access:** ARC firmware is Tenstorrent-proprietary. Customer software cannot directly execute on ARC cores, but can trigger ARC operations via mailbox/interrupt mechanisms.

**→ For ARC register map, reset sequences, and PCIe TLB configuration, see [PCIe and ARC Tiles](https://deepwiki.com/tenstorrent/tt-isa-documentation/9.2-edge-tiles:-pcie-arc-and-l2cpu)**

**Sources:**[Diagrams/Src/EdgeTile.lua 292-436](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/EdgeTile.lua#L292-L436)[WormholeB0/README.md 8](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1#L8-L8)

## L2CPU Tile (Blackhole Only)

Blackhole ASICs add **4 L2CPU tiles** (at coordinates corresponding to "CPU 0-3", "CPU 4-7", "CPU 8-11", "CPU 12-15" in the NoC grid) providing general-purpose RISC-V compute with cache hierarchy. These tiles do not exist in Wormhole.

**L2CPU Tile Block Diagram (Blackhole)**

**Key Attributes:**

*   **280 RISC-V harts per tile**: RV64GC ISA (64-bit, general-purpose, compressed)
*   **Cache hierarchy**: L1I/L1D (per-hart) → L2 (per-group) → L3 (shared) → NoC
*   **General-purpose workloads**: C/C++ code, unlike Tensix's specialized AI kernels
*   **4 tiles total**: 4 × 280 = 1120 harts across chip

**→ For L2CPU ISA details, cache coherency protocols, and DRAM interleaving, see [DRAM Tiles](https://deepwiki.com/tenstorrent/tt-isa-documentation/9.3-dram-tiles)**

**Sources:**[Diagrams/Src/NoC.lua 32-43](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/NoC.lua#L32-L43)

## Common Characteristics

All specialized tiles share certain architectural features:

### NoC Integration

All specialized tiles integrate fully with the dual NoC fabric. Each tile has **2 NIUs** (Network Interface Units), one for each NoC.

**NoC Integration Pattern**

**Sources:**[WormholeB0/NoC/MemoryMap.md 3-9](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L3-L9)[WormholeB0/NoC/README.md 13-22](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L13-L22)

### NIU Memory Map

All tiles with NIUs expose standard memory-mapped registers for NoC access:

**NIU Base Addresses**

| Tile Type | NIU #0 Base Address | NIU #1 Base Address | Notes |
| --- | --- | --- | --- |
| Tensix, Ethernet | `0x0_FFB2_0000` | `0x0_FFB3_0000` | Local access from tile RISC-V |
| PCIe (via NoC) | `0xF_FFB2_0000` | `0xF_FFB3_0000` | Remote access from other tiles |
| ARC, DRAM | `0xF_FFB2_0000` | `0xF_FFB3_0000` | Remote access only |

**Key NIU Registers (at `NIU_BASE + offset`):**

| Offset | Register Name | Purpose |
| --- | --- | --- |
| `0x000` | `NOC_TARG_ADDR_LO` | Target address low 32 bits |
| `0x004` | `NOC_TARG_ADDR_MID` | Target address high bits + X/Y coords |
| `0x00C` | `NOC_RET_ADDR_LO` | Return address low 32 bits |
| `0x010` | `NOC_RET_ADDR_MID` | Return address high bits + X/Y coords |
| `0x018` | `NOC_PACKET_TAG` | Transaction ID and flags |
| `0x01C` | `NOC_CTRL` | Request type and control flags |
| `0x020` | `NOC_AT_LEN_BE` | Length/byte-enable or atomic opcode |
| `0x028` | `NOC_CMD_CTRL` | Initiate request (write 1 to bit 0) |
| `0x02C` | `NOC_NODE_ID` | Tile X/Y coordinates (read-only) |
| `0x030` | `NOC_ENDPOINT_ID` | Tile type and index (read-only) |

Writing `1` to `NOC_CMD_CTRL[0]` triggers hardware to:

1.   Increment counters at `NIU_BASE + 0x200`
2.   Apply coordinate translation (if enabled)
3.   Assign virtual channel number
4.   Initiate NoC transaction
5.   Clear `NOC_CMD_CTRL[0]` when VC assigned

**Sources:**[WormholeB0/NoC/MemoryMap.md 3-47](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L3-L47)[WormholeB0/NoC/MemoryMap.md 148-156](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L148-L156)

### Request Initiation Capabilities

Not all tiles can initiate NoC requests. The ability to initiate depends on whether the tile contains a processor or DMA engine:

**NoC Request Initiation Capability by Tile Type**

**Initiation Methods:**

*   **Tensix/Ethernet RISC-V cores**: Write to `NOC_CMD_CTRL` register at `NIU_BASE + 0x28` after configuring `NOC_TARG_ADDR`, `NOC_RET_ADDR`, etc.
*   **ARC cores**: Issue read/write via ARC XBAR, which translates to NoC transaction via TLBs
*   **L2CPU harts**: Cache miss triggers NoC read; writeback triggers NoC write
*   **PCIe tile**: Host issues PCIe TLP → PCIe controller translates to AXI → iATU translates to NoC
*   **DRAM tiles**: No initiation capability; purely reactive

**Warning:** Software executing on RISC-V cores can only use `NOC_CMD_CTRL` registers in Tensix and Ethernet tiles. The ARC and L2CPU tiles use different mechanisms. PCIe and DRAM tiles have no software execution capability.

**Sources:**[WormholeB0/NoC/MemoryMap.md 46-47](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L46-L47)[WormholeB0/NoC/MemoryMap.md 148-156](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L148-L156)

### No Tensix Coprocessor

With the exception of the L2CPU tile (which has a different cache-based architecture), specialized tiles do **not** contain the Tensix coprocessor. This means:

*   **No Matrix Unit (FPU)**: No low-precision matrix operations
*   **No Vector Unit (SFPU)**: No 32-lane SIMD operations
*   **No Unpackers/Packers**: No format conversion and compression engines
*   **No MOP expander**: No macro-operation instruction expansion (except for Ethernet/Tensix which have limited MOP support for their RISC-V cores)

This architectural simplification reduces die area and power consumption for tiles focused on I/O, storage, or management rather than compute.

**Sources:**[WormholeB0/TensixTile/README.md 8-13](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L8-L13)[WormholeB0/EthernetTile/BabyRISCV/README.md 8](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/EthernetTile/BabyRISCV/README.md?plain=1#L8-L8)

## Data Flow Patterns

### Multi-Tier Memory Hierarchy

Data flows through a hierarchy from host memory to on-chip compute:

**Memory Hierarchy and Data Movement**

**Sources:**[WormholeB0/README.md 3-9](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1#L3-L9)[WormholeB0/TensixTile/README.md 4-6](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L4-L6)

### Chip-to-Chip Communication via Ethernet

Ethernet tiles enable multi-chip scaling with 100 GbE links:

**Ethernet Inter-Chip Data Flow**

**Sources:**[WormholeB0/README.md 6](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1#L6-L6)[WormholeB0/EthernetTile/BabyRISCV/README.md 8-13](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/EthernetTile/BabyRISCV/README.md?plain=1#L8-L13)

### Host-Device Communication via PCIe

The PCIe tile translates host transactions to NoC operations:

**PCIe Transaction Translation Flow**

**Sources:**[WormholeB0/NoC/Ordering.md 34-46](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Ordering.md?plain=1#L34-L46)[Diagrams/Src/EdgeTile.lua 132-290](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/EdgeTile.lua#L132-L290)

## Memory Capacity by Tile Type

Specialized tiles have varying memory capacities optimized for their functions:

| Tile Type | On-Tile Memory | Total Across Chip | Purpose |
| --- | --- | --- | --- |
| Tensix | 1464 KiB L1 per tile | ~117 MiB (80 tiles) | Compute scratch space |
| Ethernet | 256 KiB L1 per tile | 4 MiB (16 tiles) | Packet buffers, staging |
| DRAM | None (external GDDR6) | 12 GiB external | Long-term storage |
| ARC | 512 KiB CSM | 512 KiB (1 tile) | Management firmware |
| PCIe | None | N/A | Pure translation bridge |
| L2CPU | L1/L2/L3 caches | Unknown | Cache hierarchy |

**Sources:**[WormholeB0/TensixTile/README.md 4](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L4-L4)[WormholeB0/EthernetTile/BabyRISCV/README.md 7](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/EthernetTile/BabyRISCV/README.md?plain=1#L7-L7)[Diagrams/Src/EdgeTile.lua 1-490](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Src/EdgeTile.lua#L1-L490)

## Software Execution Models

Different tiles have different software execution models:

| Tile Type | Software Model | Programmable By |
| --- | --- | --- |
| Tensix | Fully customer-controlled | Customer |
| Ethernet | Cooperative execution | Tenstorrent firmware + customer callbacks |
| DRAM | No software | N/A (hardware only) |
| ARC | Tenstorrent firmware only | Tenstorrent |
| PCIe | No software | N/A (hardware only) |
| L2CPU | Unknown | Likely customer or Tenstorrent |

**Ethernet Cooperative Model:** Ethernet tiles run Tenstorrent firmware that:

*   Performs initial link configuration and training
*   Handles link drops and retraining
*   Provides baseline data movement service to host
*   Calls into customer code at designated entry points

**Sources:**[WormholeB0/EthernetTile/BabyRISCV/README.md 9-13](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/EthernetTile/BabyRISCV/README.md?plain=1#L9-L13)

## Address Space Organization

Each specialized tile exposes a different memory/register map:

**Memory Map Overview by Tile Type**

**Address Ranges:**

| Tile Type | Address Range | Contents |
| --- | --- | --- |
| Tensix | `0x0` - `0x16E_FFF` | L1 RAM (1464 KiB) |
| Ethernet | `0x0` - `0x3_FFFF` | L1 RAM (256 KiB) |
| Ethernet | `0xFFB9_0000` - `0xFFB9_1FFF` | TX queue 0-7 registers (`ETH_TXQ0_REGS_START`) |
| Ethernet | `0xFFB9_2000` - `0xFFB9_3FFF` | RX queue 0-7 registers (`ETH_RXQ0_REGS_START`) |
| Ethernet | `0xFFBA_0000` - `0xFFBA_FFFF` | Ethernet MAC registers |
| Ethernet | `0xFFBB_0000` - `0xFFBB_FFFF` | Ethernet PCS registers |
| All (local) | `0xFFB2_0000` - `0xFFB2_FFFF` | NIU #0 registers |
| All (local) | `0xFFB3_0000` - `0xFFB3_FFFF` | NIU #1 registers |
| DRAM | `0x0` - `0x7FFF_FFFF` | GDDR6 memory (2 GiB per group) |

**Sources:**[WormholeB0/NoC/MemoryMap.md 3-9](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L3-L9)[WormholeB0/EthernetTile/BabyRISCV/README.md 65-81](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/EthernetTile/BabyRISCV/README.md?plain=1#L65-L81)

## Performance Characteristics

### Bandwidth Summary

| Interface | Bandwidth | Tile Count | Aggregate |
| --- | --- | --- | --- |
| Ethernet link | 12.5 GB/s per tile | 16 | 200 GB/s |
| PCIe Gen4 x16 | ~32 GB/s bidirectional | 1 | 32 GB/s |
| GDDR6 channel | ~32 GB/s per tile (2 channels) | 18 | ~576 GB/s |
| NoC #0 or #1 | 256 bits/cycle @ 1 GHz = 32 GB/s per hop | N/A | Depends on routing |

### Latency Characteristics

| Access Type | Latency (cycles) | Notes |
| --- | --- | --- |
| Tensix RISC-V → Tensix L1 | ≥ 8 | Bank conflicts add latency |
| Ethernet RISC-V → Ethernet L1 | ≥ 7 | Slightly lower than Tensix |
| Any RISC-V → DRAM | ≥ hundreds | NoC + GDDR6 latency |
| Host → Tensix L1 (via PCIe + NoC) | ≥ microseconds | PCIe + NoC + L1 access |

**Sources:**[WormholeB0/README.md 7](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1#L7-L7)[WormholeB0/EthernetTile/BabyRISCV/README.md 47-55](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/EthernetTile/BabyRISCV/README.md?plain=1#L47-L55)[WormholeB0/NoC/README.md 59-68](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L59-L68)[WormholeB0/TensixTile/BabyRISCV/README.md 59-65](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L59-L65)

## Relationship to Other Tiles

**Key Relationships:**

*   **I/O tiles complement Tensix tiles**: Tensix performs compute, I/O tiles handle external communication
*   **I/O tiles access DRAM**: Can read/write GDDR6 via NoC (same as Tensix)
*   **I/O tiles are NoC peers**: Participate in same dual NoC fabric as all other tiles
*   **No direct I/O tile communication**: Ethernet and PCIe tiles communicate via NoC, not directly

**Sources:**[WormholeB0/README.md 3-14](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1#L3-L14)[WormholeB0/NoC/README.md 5-22](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L5-L22)

Dismiss
Refresh this wiki

Enter email to refresh