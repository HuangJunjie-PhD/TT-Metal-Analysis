---
project: tt-low-level-documentation
github: https://github.com/tenstorrent/tt-low-level-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-low-level-documentation/2-data-movement-system
---

# Data Movement System

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/README.md?plain=1)
*   [data_movement_doc/general/intro_to_dm.md](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/intro_to_dm.md?plain=1)

## Purpose and Scope

This section provides foundational understanding of the data movement architecture in TT-Metal. It covers the Network-on-Chip (NOC) infrastructure, dual NOC instances, RISC-V processor control, and hardware platform characteristics for Wormhole B0 and Blackhole P100 devices.

Data movement encompasses all mechanisms for transferring data between Tensix cores, DRAM, and other subsystems. The NOC serves as the high-bandwidth interconnect fabric that enables this communication.

**Related Pages:**

*   For detailed NOC architectural specifications, see [NOC Architecture](https://deepwiki.com/tenstorrent/tt-low-level-documentation/2.2-noc-architecture)
*   For platform-specific comparisons and evolution, see [Hardware Platforms](https://deepwiki.com/tenstorrent/tt-low-level-documentation/2.3-hardware-platforms)
*   For test coverage and methodology, see [Introduction to Data Movement](https://deepwiki.com/tenstorrent/tt-low-level-documentation/2.1-introduction-to-data-movement)
*   For API usage patterns and optimization, see [API Usage Guide](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3-api-usage-guide)

* * *

## System Overview

The data movement system is built around the Network-on-Chip (NOC), a packet-switched interconnect that connects all processing elements and memory subsystems. The NOC provides the physical communication substrate for all data transfers in TT-Metal applications.

**Key Characteristics:**

*   **Dual NOC instances** (NOC0 and NOC1) provide independent communication paths with 32 total virtual channels
*   **Dedicated RISC-V processors** (BRISC and NCRISC) control each NOC instance
*   **2D torus topology** ensures uniform latency and high bisection bandwidth
*   **Packet-based transfers** with platform-specific maximum packet sizes
*   **Asynchronous operations** allow overlap of computation and communication

Sources: [README.md 1-10](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/README.md?plain=1#L1-L10)[data_movement_doc/general/intro_to_dm.md 1-34](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/intro_to_dm.md?plain=1#L1-L34)

* * *

## NOC Infrastructure

**Diagram: NOC Infrastructure and Processor Control**

The NOC infrastructure consists of two independent NOC instances, each controlled by a dedicated RISC-V processor:

| Component | NOC Instance | Controller | Purpose |
| --- | --- | --- | --- |
| NOC0 | First instance | BRISC | Primary data movement, column-emphasis routing |
| NOC1 | Second instance | NCRISC | Secondary data movement, row-emphasis routing |

**Virtual Channels:** Each NOC instance provides 16 virtual channels for traffic management:

*   8 non-dateline virtual channels
*   8 dateline virtual channels (for deadlock prevention in torus topology)
*   4 unicast virtual channels under software control

The dual NOC design provides:

*   **Bandwidth doubling** for concurrent transfers
*   **Traffic separation** to avoid interference between different data movement patterns
*   **Redundancy** for different routing strategies (row vs column emphasis)

Sources: [data_movement_doc/general/intro_to_dm.md 11-30](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/intro_to_dm.md?plain=1#L11-L30)

* * *

## Hardware Platform Characteristics

The NOC architecture is implemented on two hardware platforms with different performance characteristics:

| Specification | Wormhole B0 | Blackhole P100 |
| --- | --- | --- |
| **Tensix Cores** | 64 cores | 110 cores |
| **NOC Interface Width** | 256 bits/cycle (32 bytes) | 512 bits/cycle (64 bytes) |
| **Maximum Packet Size** | 8 KB | 16 KB |
| **Address Width** | 48-bit | 64-bit |
| **Virtual Channels per NOC** | 16 | 16 |
| **NOC Instances** | 2 (NOC0, NOC1) | 2 (NOC0, NOC1) |
| **Topology** | 2D Torus | 2D Torus |

**Platform Evolution:** Blackhole represents an evolution from Wormhole with:

*   **2× interface bandwidth** (512 vs 256 bits/cycle)
*   **2× maximum packet size** (16KB vs 8KB)
*   **72% more cores** (110 vs 64)
*   **Extended addressing** (64-bit vs 48-bit)

The core NOC architecture remains consistent between platforms, ensuring API compatibility while enabling performance scaling.

Sources: [data_movement_doc/general/intro_to_dm.md 22-33](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/intro_to_dm.md?plain=1#L22-L33)

* * *

## Data Movement Operations

**Diagram: Data Movement Operations and Test Coverage**

The NOC supports three fundamental operation types:

**1. Read Operations**

*   Asynchronous reads from DRAM or remote L1 memory
*   Requires `noc_async_read_barrier()` before consuming data
*   Test coverage: DRAM-to-L1 transfers, core-to-core reads

**2. Write Operations**

*   Unicast: single destination
*   Multicast: multiple destinations in rectangular grid
*   Posted writes: no acknowledgment (higher performance)
*   Non-posted writes: acknowledgment required (guaranteed completion)
*   Test coverage: L1-to-DRAM transfers, core-to-core writes, multicast patterns

**3. Atomic Operations**

*   Semaphore increment/decrement
*   Atomic read-modify-write
*   Used for synchronization between cores
*   Test coverage: producer-consumer patterns, multi-core synchronization

Sources: [data_movement_doc/general/intro_to_dm.md 26-27](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/intro_to_dm.md?plain=1#L26-L27)

* * *

## Testing and Validation Framework

The data movement system includes comprehensive test suites located in the tt-metal repository:

**Diagram: Test Framework Structure and Metrics Collection**

**Test Organization:** The test framework is organized in the tt-metal repository under `tests/tt_metal/tt_metal/data_movement/`:

*   Each test scenario has a dedicated subdirectory with README documentation
*   Tests measure latency, bandwidth, and throughput for various patterns
*   Results are platform-specific (separate for Wormhole B0 and Blackhole)

**Metrics Collection:** The `test_data_movement.py` script:

*   Executes tests across different configurations
*   Collects performance counters and timing measurements
*   Generates visualizations for analysis
*   Validates results against expected performance targets

**Regression Detection:**

*   Assertions verify correctness of data transfers
*   Performance baselines detect regressions
*   Logging captures detailed execution traces

Sources: [data_movement_doc/general/intro_to_dm.md 41-77](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/intro_to_dm.md?plain=1#L41-L77)[data_movement_doc/general/intro_to_dm.md 99-102](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/intro_to_dm.md?plain=1#L99-L102)

* * *

## Documentation Organization

This section is organized into the following child pages:

| Page | Title | Purpose |
| --- | --- | --- |
| [2.1](https://deepwiki.com/tenstorrent/tt-low-level-documentation/2.1-introduction-to-data-movement) | Introduction to Data Movement | Overview of data movement concepts, NOC features, test coverage, and getting started guide |
| [2.2](https://deepwiki.com/tenstorrent/tt-low-level-documentation/2.2-noc-architecture) | NOC Architecture | Detailed specification of 2D torus topology, packet switching, virtual channels, and routing |
| [2.3](https://deepwiki.com/tenstorrent/tt-low-level-documentation/2.3-hardware-platforms) | Hardware Platforms | Comprehensive comparison of Wormhole B0 and Blackhole P100 platforms |

**Related Documentation Sections:**

*   [API Usage Guide](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3-api-usage-guide) - Practical guidance for using NOC APIs effectively
*   [Performance Analysis](https://deepwiki.com/tenstorrent/tt-low-level-documentation/4-performance-analysis) - Detailed performance studies including multicast schemes and virtual channel evaluations
*   [Technical Reference](https://deepwiki.com/tenstorrent/tt-low-level-documentation/5-technical-reference) - Platform-specific specifications and register documentation

Sources: [README.md 1-10](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/README.md?plain=1#L1-L10)[data_movement_doc/general/intro_to_dm.md 80-95](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/intro_to_dm.md?plain=1#L80-L95)

* * *

## Key Concepts Summary

**NOC Instances:**

*   NOC0: Controlled by BRISC processor, column-emphasis routing
*   NOC1: Controlled by NCRISC processor, row-emphasis routing

**Platform Support:**

*   Wormhole B0: 64 cores, 256 bits/cycle, 8KB packets
*   Blackhole P100: 110 cores, 512 bits/cycle, 16KB packets

**Operation Types:**

*   Read: Asynchronous reads with explicit barriers
*   Write: Unicast/multicast, posted/non-posted variants
*   Atomic: Semaphores and read-modify-write operations

**Testing Coverage:**

*   DRAM ↔ Core transfers
*   Core ↔ Core point-to-point and multicast
*   Loopback and self-communication patterns
*   Performance profiling and regression validation

Sources: [data_movement_doc/general/intro_to_dm.md 1-107](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/intro_to_dm.md?plain=1#L1-L107)

Dismiss
Refresh this wiki

Enter email to refresh