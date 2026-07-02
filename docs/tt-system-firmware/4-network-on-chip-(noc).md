---
project: tt-system-firmware
github: https://github.com/tenstorrent/tt-system-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-system-firmware/4-network-on-chip-(noc)
---

# Network-on-Chip (NOC)

Relevant source files
*   [include/tenstorrent/sys_init_defines.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h)
*   [lib/tenstorrent/bh_arc/CMakeLists.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/CMakeLists.txt)
*   [lib/tenstorrent/bh_arc/clock_wave.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/clock_wave.c)
*   [lib/tenstorrent/bh_arc/clock_wave.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/clock_wave.h)
*   [lib/tenstorrent/bh_arc/eth.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c)
*   [lib/tenstorrent/bh_arc/gddr.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c)
*   [lib/tenstorrent/bh_arc/gddr.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.h)
*   [lib/tenstorrent/bh_arc/noc2axi.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc2axi.c)
*   [lib/tenstorrent/bh_arc/noc2axi.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc2axi.h)
*   [lib/tenstorrent/bh_arc/noc_init.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c)
*   [lib/tenstorrent/bh_arc/noc_init.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.h)
*   [lib/tenstorrent/bh_arc/pcie.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c)
*   [lib/tenstorrent/bh_arc/pcie.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.h)
*   [lib/tenstorrent/bh_arc/pciesd.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pciesd.h)
*   [lib/tenstorrent/bh_arc/tensix_init.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c)
*   [lib/tenstorrent/bh_arc/tensix_init.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.h)
*   [lib/tenstorrent/bh_arc/tt_shell.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tt_shell.c)
*   [tests/lib/tenstorrent/bh_arc/src/msgqueue.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/tests/lib/tenstorrent/bh_arc/src/msgqueue.c)

## Overview

The Network-on-Chip (NOC) is the on-chip interconnect fabric that connects all hardware subsystems in the Blackhole ASIC. The NOC provides address translation, routing, and communication between the ARC processor, Tensix compute cores, GDDR memory controllers, Ethernet controllers, and PCIe subsystems. This page documents the NOC architecture, coordinate systems, TLB mechanisms, and translation capabilities.

For detailed information about how individual subsystems initialize and use the NOC, see:

*   Tensix initialization: [Tensix Compute Cores](https://deepwiki.com/tenstorrent/tt-system-firmware/3.3-tensix-compute-cores)
*   GDDR access: [GDDR Memory Controllers](https://deepwiki.com/tenstorrent/tt-system-firmware/3.4-gddr-memory-controllers)
*   Ethernet access: [Ethernet Subsystem](https://deepwiki.com/tenstorrent/tt-system-firmware/3.5-ethernet-subsystem)
*   PCIe access: [PCIe Subsystem](https://deepwiki.com/tenstorrent/tt-system-firmware/3.2-pcie-subsystem)

For NOC translation and harvesting support, see [NOC Translation and Harvesting](https://deepwiki.com/tenstorrent/tt-system-firmware/4.1-noc-translation-and-harvesting).

* * *

## NOC Physical Architecture

The Blackhole NOC is organized as a 17×12 grid with dual independent routing rings (NOC0 and NOC1). Each grid position corresponds to a physical hardware tile, and the two NOC rings provide redundant paths for fault tolerance and increased bandwidth.

**NOC Grid Layout**

**Sources:**[lib/tenstorrent/bh_arc/noc_init.c 333-583](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L333-L583)

### Dual NOC Rings

The system provides two independent NOC rings with mirrored coordinate systems:

| Feature | NOC0 | NOC1 |
| --- | --- | --- |
| **Base Address** | `0xC0000000` | `0xE0000000` |
| **X Coordinate Mapping** | `x` | `16 - x` |
| **Y Coordinate Mapping** | `y` | `11 - y` |
| **Use Case** | Primary communication path | Redundant path, increased bandwidth |

The coordinate mirroring means that a tile at NOC0 coordinates (x, y) appears at NOC1 coordinates (16-x, 11-y).

**Sources:**[lib/tenstorrent/bh_arc/noc2axi.h 13-14](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc2axi.h#L13-L14)[lib/tenstorrent/bh_arc/noc_init.c 312-327](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L312-L327)

* * *

## NOC Coordinate Systems

The NOC uses multiple coordinate systems to enable flexible routing and fault tolerance:

### Physical vs. Logical Coordinates

**Sources:**[lib/tenstorrent/bh_arc/noc_init.c 275-310](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L275-L310)

### Coordinate Translation Tables

The NOC translation system uses several data structures to map logical coordinates to physical tiles:

| Structure | Size | Purpose |
| --- | --- | --- |
| `translate_table_x[32]` | 32 bytes | Maps pre-translation X → post-translation X |
| `translate_table_y[32]` | 32 bytes | Maps pre-translation Y → post-translation Y |
| `logical_coords[17][12]` | 204 entries | Reverse mapping for overlay routing |
| `translate_col_mask[1]` | 32 bits | Bitmask for columns with translation |
| `translate_row_mask[1]` | 32 bits | Bitmask for rows with translation |

**Sources:**[lib/tenstorrent/bh_arc/noc_init.c 275-286](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L275-L286)

### Tensix Column Mapping

The physical Tensix columns map to NOC0 X coordinates using a specific pattern to optimize routing:

This interleaving pattern places alternating columns on opposite sides of the grid for better routing balance.

**Sources:**[lib/tenstorrent/bh_arc/noc_init.c 334](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L334-L334)

* * *

## NOC-to-AXI TLB System

The ARC processor accesses NOC-connected tiles through Translation Lookaside Buffers (TLBs) that map 16MB address windows to specific (x,y) coordinates and 64-bit base addresses.

### TLB Window Mechanism

**Sources:**[lib/tenstorrent/bh_arc/noc2axi.h 13-42](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc2axi.h#L13-L42)

### TLB Register Structure

Each NOC ring (0 and 1) has 16 independent TLBs, configured through four 32-bit registers per TLB:

**Sources:**[lib/tenstorrent/bh_arc/noc2axi.c 11-59](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc2axi.c#L11-L59)

### TLB Setup Functions

The firmware provides several functions for configuring TLBs:

| Function | Purpose | Usage |
| --- | --- | --- |
| `NOC2AXITlbSetup()` | Unicast to single tile | Accessing specific GDDR/Tensix/Eth tile |
| `NOC2AXIMulticastTlbSetup()` | Multicast to rectangular region | Parallel writes to multiple tiles |
| `NOC2AXITensixBroadcastTlbSetup()` | Broadcast to all Tensix cores | Global Tensix configuration |

**Example: Setting up TLB for MRISC L1 access**

**Sources:**[lib/tenstorrent/bh_arc/gddr.c 72-79](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L72-L79)[lib/tenstorrent/bh_arc/noc2axi.c 93-112](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc2axi.c#L93-L112)

* * *

## Transaction Ordering Modes

The NOC supports four ordering modes to balance performance and consistency:

| Mode | Ordering Guarantee | Use Case |
| --- | --- | --- |
| **Relaxed** | None - max throughput | Bulk data transfers where order doesn't matter |
| **Strict** | All transactions ordered | Configuration registers, status reads |
| **Posted** | Writes don't wait for completion | Fire-and-forget writes |
| **Posted Strict** | Posted writes in order | Ordered configuration sequences |

**Sources:**[lib/tenstorrent/bh_arc/noc2axi.h 19-24](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc2axi.h#L19-L24)

* * *

## Broadcast and Multicast Operations

### Broadcast Exclusion

The NOC broadcast system uses exclusion masks to prevent sending broadcasts to specific rows or columns. This is critical for avoiding broadcast reception by non-compute tiles:

**Sources:**[lib/tenstorrent/bh_arc/noc_init.c 114-155](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L114-L155)

### Tensix Broadcast

The `NOC2AXITensixBroadcastTlbSetup()` function configures a TLB for broadcasting to all unharvested Tensix cores while skipping the ARC's own row:

**Sources:**[lib/tenstorrent/bh_arc/noc2axi.c 140-177](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc2axi.c#L140-L177)

* * *

## NOC Initialization Sequence

The NOC is initialized early in the boot sequence through `NocInit()`, which runs at `SYS_INIT` priority 99:

**Key Configuration Steps:**

1.   **NIU (Network Interface Unit) Configuration** - Each NOC node has a NIU with registers controlling clock gating, ordering, and translation
2.   **Tile Clock Disable** - Disabled tiles have clocks gated to save power
3.   **Overlay Clock Gating** - Stream overlay gets clock gating enabled for efficiency
4.   **Broadcast Exclusion** - Configure which rows/columns don't receive broadcasts

**Sources:**[lib/tenstorrent/bh_arc/noc_init.c 221-273](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L221-L273)

* * *

## Common NOC Usage Patterns

### Pattern 1: Reading/Writing Single Tile Register

**Sources:**[lib/tenstorrent/bh_arc/gddr.c 108-115](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L108-L115)

### Pattern 2: Bulk Transfer to Tile L1

**Sources:**[lib/tenstorrent/bh_arc/eth.c 228-248](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L228-L248)

### Pattern 3: Broadcast Configuration Write

**Sources:**[lib/tenstorrent/bh_arc/tensix_init.c 84-108](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L84-L108)

### Pattern 4: Multicast to Tile Array

**Sources:**[lib/tenstorrent/bh_arc/tensix_init.c 227-231](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L227-L231)

* * *

## NOC Register Access Helpers

The firmware provides inline helper functions for common NOC operations:

| Function | Purpose | Example |
| --- | --- | --- |
| `GetTlbWindowAddr(noc_id, tlb, addr)` | Get pointer to TLB-mapped address | Accessing L1 memory |
| `NOC2AXIWrite32(noc_id, tlb, addr, data)` | Write 32-bit value through TLB | Register writes |
| `NOC2AXIWrite8(noc_id, tlb, addr, data)` | Write 8-bit value through TLB | Byte-level access |
| `NOC2AXIRead32(noc_id, tlb, addr)` | Read 32-bit value through TLB | Status register reads |

**Implementation Detail:**

**Sources:**[lib/tenstorrent/bh_arc/noc2axi.h 34-61](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc2axi.h#L34-L61)

* * *

## Performance Considerations

### Clock Gating

The NOC supports extensive clock gating to reduce power consumption:

*   **NIU Clock Gating** - Enabled via `NIU_CFG_0` bit 0
*   **Router Clock Gating** - Enabled via `ROUTER_CFG_0` bit 0
*   **Tile Clock Disable** - Completely gates clocks to harvested/disabled tiles
*   **Overlay Clock Gating** - Stream overlay clock gating via `STREAM_PERF_CONFIG`

All clock gating is controlled by the `cg_en` feature flag in the firmware table.

**Sources:**[lib/tenstorrent/bh_arc/noc_init.c 235-265](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L235-L265)

### Router Backoff

The NOC router uses exponential backoff to handle congestion:

This sets the maximum backoff exponent to 15, allowing routers to wait up to 2^15 cycles when encountering congestion.

**Sources:**[lib/tenstorrent/bh_arc/noc_init.c 233](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L233-L233)

### Ordering Mode Selection

Choose ordering mode based on use case:

*   **Configuration sequences**: Use `kNoc2AxiOrderingStrict` to ensure registers are written in order
*   **Bulk data transfers**: Use `kNoc2AxiOrderingRelaxed` for maximum throughput
*   **Fire-and-forget writes**: Use `kNoc2AxiOrderingPosted` when completion doesn't matter
*   **Ordered init sequences**: Use `kNoc2AxiOrderingPostedStrict` for ordered non-blocking writes

**Sources:**[lib/tenstorrent/bh_arc/noc2axi.h 19-24](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc2axi.h#L19-L24)

* * *

## Coordinate Helper Functions

The `noc.c` and `noc_init.c` files provide coordinate translation helpers:

These functions abstract the complexity of coordinate mapping, especially when NOC translation is enabled.

**Sources:**[lib/tenstorrent/bh_arc/noc_init.c 742-755](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L742-L755)

* * *

## NIU Configuration Registers

Each NOC node has a Network Interface Unit (NIU) with configuration registers accessed via an offset from the node's base address:

| Register | Index | Purpose |
| --- | --- | --- |
| `NIU_CFG_0` | 0x0 | Clock gating, tile enable, translation enable, AXI enable |
| `ROUTER_CFG(n)` | 1-5 | Router configuration (backoff, broadcast exclusion) |
| `NOC_X_ID_TRANSLATE_TABLE(n)` | 6-11 | X coordinate translation entries |
| `NOC_Y_ID_TRANSLATE_TABLE(n)` | 12-17 | Y coordinate translation entries |
| `NOC_ID_LOGICAL` | 18 | Logical (x,y) coordinate for this node |
| `NOC_ID_TRANSLATE_COL_MASK` | 20 | Bitmask of columns with translation enabled |
| `NOC_ID_TRANSLATE_ROW_MASK` | 21 | Bitmask of rows with translation enabled |

**Key NIU_CFG_0 Bits:**

*   Bit 12: `TILE_CLK_OFF` - Disable clock to this tile
*   Bit 13: `TILE_HEADER_STORE_OFF` - Disable header double-write (NOC2AXI only)
*   Bit 14: `NOC_ID_TRANSLATE_EN` - Enable coordinate translation
*   Bit 15: `AXI_SLAVE_ENABLE` - Enable AXI slave interface

**Sources:**[lib/tenstorrent/bh_arc/noc_init.c 29-47](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L29-L47)

* * *

## Debug and Testing

### Debug NOC Translation Message Handler

The firmware provides a debug message handler for testing NOC translation configurations:

This allows host software to dynamically reconfigure NOC translation for testing harvesting scenarios.

**Sources:**[lib/tenstorrent/bh_arc/noc_init.c 705-740](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L705-L740)

### Tensix Enable Control

The `set_tensix_enable()` function allows runtime enable/disable of all Tensix cores:

**Sources:**[lib/tenstorrent/bh_arc/noc_init.c 193-219](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc_init.c#L193-L219)

Dismiss
Refresh this wiki

Enter email to refresh