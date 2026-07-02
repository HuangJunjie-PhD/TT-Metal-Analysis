---
project: tt-isa-documentation
github: https://github.com/tenstorrent/tt-isa-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-isa-documentation/2-network-on-chip-(noc))
---

# Network-on-Chip (NoC)

Relevant source files
*   [WormholeB0/NoC/Alignment.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Alignment.md?plain=1)
*   [WormholeB0/NoC/Counters.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Counters.md?plain=1)
*   [WormholeB0/NoC/MemoryMap.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1)
*   [WormholeB0/NoC/Ordering.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Ordering.md?plain=1)
*   [WormholeB0/NoC/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1)
*   [WormholeB0/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1)

## Purpose and Scope

The Network-on-Chip (NoC) is the primary communication backbone in Wormhole and Blackhole ASICs, enabling data movement between all tile types: Tensix compute tiles, DRAM tiles, Ethernet tiles, PCIe tile, and ARC tile. Each ASIC contains two independent NoCs (NoC #0 and NoC #1) arranged as 2D torus networks, providing redundancy, bandwidth, and load balancing for distributed compute workloads.

This page introduces the NoC architecture, topology, packet structure, transaction types, and virtual channel mechanism. For detailed information about specific NoC subsystems, see:

*   NIU memory-mapped registers and request initiation: [NoC Memory Map and NIU Interface](https://deepwiki.com/tenstorrent/tt-isa-documentation/2.1-noc-memory-map-and-niu-interface)
*   Transaction routing and parameters: [Transaction Types and Routing](https://deepwiki.com/tenstorrent/tt-isa-documentation/2.2-transaction-types-and-routing)
*   Virtual channel details and ordering semantics: [Virtual Channels and Ordering Guarantees](https://deepwiki.com/tenstorrent/tt-isa-documentation/2.3-virtual-channels-and-ordering-guarantees)
*   Counter-based monitoring and alignment rules: [NoC Counters and Alignment Requirements](https://deepwiki.com/tenstorrent/tt-isa-documentation/2.4-noc-counters-and-alignment-requirements)
*   Stream-based data delivery: [NoC Overlay Streams](https://deepwiki.com/tenstorrent/tt-isa-documentation/2-network-on-chip-(noc))#2.5)

For chip-level architecture context, see [Chip Architecture and Tile Types](https://deepwiki.com/tenstorrent/tt-isa-documentation/1.2-chip-architecture-and-tile-types).

**Sources:**[WormholeB0/NoC/README.md 1-69](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L1-L69)

## Dual NoC Architecture

Each ASIC contains two physically independent NoCs that connect the same set of tiles but flow in opposite directions. This dual-NoC design provides:

*   **Bandwidth doubling**: Two independent data paths between any tile pair
*   **Load balancing**: Software can distribute traffic across NoCs to avoid congestion
*   **Redundancy**: Critical transactions can use both NoCs for reliability
*   **Routing diversity**: NoC #0 uses X-first routing (X then Y), while NoC #1 uses Y-first routing (Y then X)

**Sources:**[WormholeB0/NoC/README.md 1-11](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L1-L11)[WormholeB0/README.md 9-10](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/README.md?plain=1#L9-L10)

## 2D Torus Topology

Each NoC is organized as a 10×12 grid of NoC Interface Units (NIUs) and routers, forming a 2D torus where left/right edges wrap around and top/bottom edges wrap around. Each intersection in the grid contains a router that connects to four neighboring routers (±X, ±Y) and one local NIU. The NIU provides the interface between the NoC router and the tile's local resources (memory, registers, compute units).

### NoC #0 Topology (X-first routing)

The grid uses NoC coordinates (X, Y) where:

*   X ranges from 0 to 9 (10 columns)
*   Y ranges from 0 to 11 (12 rows)

Each tile type occupies specific coordinates:

*   **Tensix tiles**: Most of (X, Y) where 0 ≤ X ≤ 9 and 1 ≤ Y ≤ 10
*   **DRAM tiles**: Distributed around the perimeter (Y=0, Y=11, X=0, X=9)
*   **Ethernet tiles**: Concentrated at Y=0 and Y=11
*   **PCIe tile**: Fixed at (8, 0)
*   **ARC tile**: Fixed at (2, 11)
*   **Empty tiles**: Locations without active hardware, but containing routers

**Sources:**[WormholeB0/NoC/README.md 5-11](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L5-L11)[WormholeB0/NoC/MemoryMap.md 183-186](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L183-L186)

## NoC Interface Unit (NIU) Architecture

Each tile contains two NIUs (one for NoC #0, one for NoC #1). The NIU serves as the interface between the tile's local address space and the NoC router, handling request initiation, packet assembly/disassembly, and transaction monitoring.

### NIU Request Initiators

Each NIU provides four independent request initiators, allowing up to four concurrent NoC transactions to be configured. Software initiates a transaction by:

1.   Writing transaction parameters to the initiator's registers (`NOC_TARG_ADDR`, `NOC_RET_ADDR`, `NOC_CTRL`, `NOC_AT_LEN_BE`, etc.)
2.   Writing `1` to bit 0 of `NOC_CMD_CTRL`
3.   Hardware assigns a virtual channel number and queues the request
4.   Hardware clears bit 0 of `NOC_CMD_CTRL` when the initiator is available for reuse

**Sources:**[WormholeB0/NoC/MemoryMap.md 1-45](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L1-L45)[WormholeB0/NoC/MemoryMap.md 148-157](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L148-L157)

## Packet Structure and Flits

All NoC communication occurs via packets. Each packet consists of one or more **flits** (flow control units):

*   **Header flit**: Contains routing information, transaction type, addresses, and control flags (256 bits)
*   **Data flits**: Contains payload data (0 to 256 flits × 256 bits each = 0 to 8192 bytes maximum)

### Flit Transmission

*   **Flit size**: 256 bits (32 bytes) transmitted atomically
*   **Maximum data payload**: 256 data flits = 8192 bytes per packet
*   **Throughput**: 1 flit per cycle per hop (at NoC clock frequency)
*   **Pipeline stages**: ~5 cycles NIU-to-router, 9 cycles router-to-router, ~5 cycles router-to-NIU

**Sources:**[WormholeB0/NoC/README.md 1-4](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L1-L4)[WormholeB0/NoC/README.md 59-69](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L59-L69)

## Transaction Types

The NoC supports three primary transaction types, each with various options:

### Read Requests (`NOC_CMD_RD`)

*   **Purpose**: Read contiguous bytes from remote tile's address space
*   **Request packet**: 1 header flit (no data)
*   **Response packet**: 1 header flit + data flits containing requested data
*   **Maximum length**: 8192 bytes per request (limited by 256 data flits)
*   **Key registers**: 
    *   `NOC_TARG_ADDR`: Source location (where to read from)
    *   `NOC_RET_ADDR`: Destination location (where to write response data)
    *   `NOC_AT_LEN_BE`: Number of bytes to read

*   **Always generates response**: Cannot be posted

**Sources:**[WormholeB0/NoC/README.md 24-25](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L24-L25)[WormholeB0/NoC/MemoryMap.md 73-91](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L73-L91)

### Write Requests (`NOC_CMD_WR`)

*   **Purpose**: Write data to remote tile's address space
*   **Data source options**: 
    *   **Inline** (`NOC_CMD_WR_INLINE=true`): Data from 32-bit `NOC_AT_DATA` register (≤16 bytes)
    *   **Non-inline** (`NOC_CMD_WR_INLINE=false`): Data read from local memory at `NOC_TARG_ADDR`

*   **Posted vs. Non-posted**: 
    *   **Posted** (`NOC_CMD_RESP_MARKED=false`): Fire-and-forget, no acknowledgement
    *   **Non-posted** (`NOC_CMD_RESP_MARKED=true`): Acknowledgement returned when write completes

*   **Byte-enable mode** (`NOC_CMD_WR_BE=true`): Write arbitrary subset of ≤32 bytes using mask in `NOC_AT_LEN_BE`
*   **Broadcast support**: Can write to rectangle of tiles using `NOC_CMD_BRCST_PACKET`
*   **Maximum length**: 8192 bytes per request

**Sources:**[WormholeB0/NoC/README.md 25-34](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L25-L34)[WormholeB0/NoC/MemoryMap.md 49-72](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L49-L72)[WormholeB0/NoC/MemoryMap.md 116-128](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L116-L128)

### Atomic Requests (`NOC_CMD_AT`)

*   **Purpose**: Perform atomic read-modify-write operation on remote L1 memory
*   **Restrictions**: 
    *   Target must be L1 address (not MMIO)
    *   Only available at Tensix and Ethernet tiles
    *   Operates on aligned 16-byte region

*   **Operation types**: Compare-and-swap, increment, decrement, bitwise operations (see [Atomics documentation](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Atomics%20documentation) for details)
*   **Operands**: 32-bit immediate in `NOC_AT_DATA` or operands encoded in `NOC_AT_LEN_BE`
*   **Optional response**: When `NOC_CMD_RESP_MARKED=true`, returns old value to `NOC_RET_ADDR`
*   **Broadcast support**: Can perform atomic operation at multiple tiles

**Sources:**[WormholeB0/NoC/README.md 35-37](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L35-L37)[WormholeB0/NoC/MemoryMap.md 55](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L55-L55)[WormholeB0/NoC/MemoryMap.md 79-80](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L79-L80)

## Virtual Channels and Deadlock Prevention

Each packet is assigned a 4-bit **virtual channel (VC) number** as it traverses each hop. The VC number determines which buffer resources at the router/NIU the packet can use. Once a packet fully traverses a hop, its VC number is released for reuse by other packets.

### Virtual Channel Number Composition

### Virtual Channel Bit Functions

| Bit | Name | Function | Control |
| --- | --- | --- | --- |
| 3 | Dateline | Flips at torus wraparound to prevent cyclic dependencies | Hardware-controlled, flips at statically determined locations based on route |
| 2:1 | Class | Separates request/response traffic classes | Hardware-enforced based on transaction type |
| 0 | Buddy | Enables adaptive routing around congestion | Software can force static via `NOC_CMD_VC_STATIC`, otherwise hardware chooses dynamically |

### Dateline Mechanism

The 2D torus topology creates the potential for circular routing dependencies. The dateline bit breaks these cycles:

*   Packets start with dateline bit set to initial value
*   When packet crosses certain grid boundaries (wraparound edges), dateline bit flips
*   Routers use VC number (including dateline bit) to arbitrate for output ports
*   By ensuring dateline flips occur at consistent locations, circular dependencies are prevented

The locations where dateline flips occur are determined by the router hardware and can be queried from `NOC_NODE_ID` register bits 26-27.

**Sources:**[WormholeB0/NoC/README.md 44-52](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L44-L52)[WormholeB0/NoC/MemoryMap.md 180-191](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L180-L191)[WormholeB0/NoC/Ordering.md 9-18](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Ordering.md?plain=1#L9-L18)

## NoC Addressing and Coordinates

NoC addresses combine memory addresses with tile coordinates. The addressing scheme depends on whether coordinate translation is enabled.

### Address Format (Unicast)

| Bits | Field | Description |
| --- | --- | --- |
| 0:35 | Memory Address | 36-bit address within target tile (high 4 bits usually zero) |
| 36:41 | X Coordinate | Target tile X coordinate (0-9 in NoC space) |
| 42:47 | Y Coordinate | Target tile Y coordinate (0-11 in NoC space) |
| 48:59 | Reserved | Ignored by hardware |
| 60:63 | Reserved | Ignored by hardware |

For broadcast packets, the format encodes a rectangle:

| Bits | Field | Description |
| --- | --- | --- |
| 0:35 | Memory Address | Same address at all target tiles |
| 36:41 | EndX | Rectangle boundary (X dimension) |
| 42:47 | EndY | Rectangle boundary (Y dimension) |
| 48:53 | StartX | Rectangle boundary (X dimension) |
| 54:59 | StartY | Rectangle boundary (Y dimension) |
| 60:63 | Reserved | Ignored by hardware |

The broadcast rectangle includes all tiles (x, y) where:

*   `StartX ≤ x ≤ EndX` (or `x ≤ EndX || StartX ≤ x` if `StartX > EndX`, to support wraparound)
*   `StartY ≤ y ≤ EndY` (or `y ≤ EndY || StartY ≤ y` if `StartY > EndY`, to support wraparound)

### Coordinate Translation

When bit 14 of `NIU_CFG_0` is set, coordinate translation is enabled. Software specifies coordinates in "logical space" (typically 16-25 for X, 16-27 for Y), and hardware translates to NoC coordinates (0-9 for X, 0-11 for Y) using lookup tables:

*   `NOC_X_ID_TRANSLATE_TABLE_0`: 128-bit table mapping logical X to NoC X (32 entries × 4 bits)
*   `NOC_Y_ID_TRANSLATE_TABLE_0`: 128-bit table mapping logical Y to NoC Y (32 entries × 4 bits)

Firmware configures these tables to hide fused-off tiles and present a contiguous logical address space to software.

**Sources:**[WormholeB0/NoC/MemoryMap.md 93-115](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L93-L115)[WormholeB0/NoC/MemoryMap.md 217-268](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L217-L268)

## Performance Characteristics

### Throughput

| Hop Type | Bandwidth per NoC | Notes |
| --- | --- | --- |
| NIU → Router | 256 bits/cycle | 1 flit per cycle |
| Router → Router | 256 bits/cycle per axis | Independent X and Y channels |
| Router → NIU | 256 bits/cycle | 1 flit per cycle |

**Effective throughput** depends on the ratio of header to data:

*   Minimum: 4 bytes data per packet (1 header + 0 data flits) = 4 bytes / (1 flit) = 12.5% efficiency
*   Maximum: 8192 bytes data per packet (1 header + 256 data flits) = 8192 bytes / (257 flits) ≈ 99.6% efficiency

### Latency

| Hop Type | Latency |
| --- | --- |
| NIU → Router | ~5 cycles |
| Router → Router | 9 cycles |
| Router → NIU | ~5 cycles |

**Round-trip latency** for minimal packet (request + response):

*   Adjacent tiles: ~(5 + 9 + 5) + (5 + 9 + 5) ≈ 38 cycles
*   Diagonal corners (worst case): Multiple router hops, ~100+ cycles

**Factors affecting latency**:

*   **Congestion**: Multiple packets contending for same virtual channel
*   **Ordering constraints**: `NOC_CMD_VC_LINKED` or `NOC_CMD_VC_STATIC` can force packets to wait
*   **Path reservation**: `NOC_CMD_PATH_RESERVE` on broadcasts adds reservation phase
*   **Arbitration priority**: `NOC_CMD_ARB_PRIORITY` can prioritize certain traffic

**Sources:**[WormholeB0/NoC/README.md 59-69](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L59-L69)

## Ordering and Synchronization

NoC transactions are **weakly ordered** by default. Software can achieve stronger ordering through several mechanisms:

### Ordering Control Flags

| Flag | Purpose | Effect on Ordering |
| --- | --- | --- |
| `NOC_CMD_VC_STATIC` | Force static VC number | Requests with same VC on same route cannot reorder |
| `NOC_CMD_VC_LINKED` | Multi-request transaction | All requests in transaction use same VC per hop, stronger ordering |
| `NOC_CMD_RESP_MARKED` | Request acknowledgement | Can poll for completion via counters |

### Counter-Based Synchronization

Software monitors transaction progress using NIU counters:

*   **`NIU_MST_REQS_OUTSTANDING_ID(i)`**: Incremented when request initiated, decremented when response/ack received
*   **`NIU_MST_WRITE_REQS_OUTGOING_ID(i)`**: Tracks write requests where data is still being read from local memory
*   **`NIU_MST_CMD_ACCEPTED`**: Incremented when request assigned a VC number
*   **Various sent/received counters**: Track requests at different pipeline stages

The `NOC_PACKET_TRANSACTION_ID` field (4 bits) selects which counter set (0-15) a transaction affects, allowing software to track multiple independent streams.

For detailed ordering rules and guarantees, see [Virtual Channels and Ordering Guarantees](https://deepwiki.com/tenstorrent/tt-isa-documentation/2.3-virtual-channels-and-ordering-guarantees) and [NoC Counters and Alignment Requirements](https://deepwiki.com/tenstorrent/tt-isa-documentation/2.4-noc-counters-and-alignment-requirements).

**Sources:**[WormholeB0/NoC/README.md 54-57](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/README.md?plain=1#L54-L57)[WormholeB0/NoC/Counters.md 1-44](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Counters.md?plain=1#L1-L44)[WormholeB0/NoC/Ordering.md 1-32](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Ordering.md?plain=1#L1-L32)

## Address Alignment Requirements

NoC transactions have strict alignment requirements that vary by transaction type, memory region, and direction. Violations result in undefined behavior.

### Key Alignment Rules

| Transaction Type | Source/Destination | Requirement |
| --- | --- | --- |
| L1 ↔ L1 read/write | Both addresses | Congruent mod 16 (addresses must have same offset within 16-byte boundary) |
| L1 ↔ L1 read/write | Both addresses | Congruent mod 32 for "other" addresses |
| MMIO read/write | Address | 4-byte aligned, length must be 4 bytes |
| Inline write to L1 | Destination | 16-byte aligned (rounded down if not) |
| Inline write to MMIO | Destination | 4-byte aligned |
| Byte-enable write to L1 | Destination | 32-byte aligned |
| Atomic operation | L1 target | Within aligned 16-byte region |

For complete alignment tables and requirements, see [NoC Counters and Alignment Requirements](https://deepwiki.com/tenstorrent/tt-isa-documentation/2.4-noc-counters-and-alignment-requirements).

**Sources:**[WormholeB0/NoC/Alignment.md 1-66](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Alignment.md?plain=1#L1-L66)

## NIU Register Interface

Each NIU exposes a memory-mapped register interface at base addresses:

*   **NoC #0 NIU**: `0xFFB2_0000` (Tensix/Ethernet tiles)
*   **NoC #1 NIU**: `0xFFB3_0000` (Tensix/Ethernet tiles)

### Register Map Summary

| Offset | Contents | Access |
| --- | --- | --- |
| `+0x000` | Request Initiator #0 (`NOC_TARG_ADDR`, `NOC_RET_ADDR`, `NOC_CTRL`, `NOC_CMD_CTRL`, etc.) | R/W |
| `+0x02C` | NIU Identification (`NOC_NODE_ID`, `NOC_ENDPOINT_ID`) | R |
| `+0x050` | Clear Transaction ID Counters | W |
| `+0x054` | Combined Request Initiator Status | R |
| `+0x100` | NIU and Router Configuration (`NIU_CFG_0`, `ROUTER_CFG_*`) | R/W |
| `+0x200` | NIU Counters (`NIU_MST_*`, `NIU_SLV_*`) | R |
| `+0x300` | Router Debug Information | R |
| `+0x380` | NIU Debug Information | R |
| `+0x400` | Request Initiator #1 | R/W |
| `+0x800` | Request Initiator #2 | R/W |
| `+0xC00` | Request Initiator #3 | R/W |

For detailed register field definitions and usage, see [NoC Memory Map and NIU Interface](https://deepwiki.com/tenstorrent/tt-isa-documentation/2.1-noc-memory-map-and-niu-interface).

**Sources:**[WormholeB0/NoC/MemoryMap.md 11-29](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L11-L29)

## Example: NoC Read Transaction Flow

**Sources:**[WormholeB0/NoC/MemoryMap.md 148-157](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/MemoryMap.md?plain=1#L148-L157)[WormholeB0/NoC/Counters.md 71-96](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/NoC/Counters.md?plain=1#L71-L96)

Dismiss
Refresh this wiki

Enter email to refresh