---
project: tt-low-level-documentation
github: https://github.com/tenstorrent/tt-low-level-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-low-level-documentation/5-technical-reference
---

# Technical Reference

Relevant source files
*   [data_movement_doc/general/ideal_performance.md](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1)
*   [data_movement_doc/general/state_preservation.md](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/state_preservation.md?plain=1)

## Purpose and Scope

This section provides detailed technical specifications, reference data, and platform-specific implementation details for the TT-Metal data movement system. It serves as a comprehensive reference for understanding the underlying hardware architecture, register-level operations, and performance characteristics.

The technical reference covers:

*   **Performance baselines** for all data movement primitives across platforms
*   **Register state preservation** and stateful vs stateless API behavior (see [Register State Preservation](https://deepwiki.com/tenstorrent/tt-low-level-documentation/5.1-register-state-preservation))
*   **Platform-specific differences** between Wormhole B0 and Blackhole P100 architectures (see [Platform Comparison](https://deepwiki.com/tenstorrent/tt-low-level-documentation/5.2-platform-comparison))
*   **Licensing information** for this documentation (see [License](https://deepwiki.com/tenstorrent/tt-low-level-documentation/5.3-license))

For API usage patterns and best practices, see [API Usage Guide](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3-api-usage-guide). For performance analysis and optimization studies, see [Performance Analysis](https://deepwiki.com/tenstorrent/tt-low-level-documentation/4-performance-analysis).

* * *

## Performance Baselines

The following table documents ideal performance characteristics for fundamental data movement primitives, measured in **bytes per cycle**. These values represent theoretical maximum throughput under optimal conditions (no congestion, optimal NOC routing, proper alignment).

| Primitive | Wormhole B0 | Blackhole P100 |
| --- | --- | --- |
| DRAM Read | 22 | 33 |
| DRAM Write | 21 | 34 |
| One To One | 29 | 60 |
| One From One | 28 | 60 |
| One To All (Unicast) | 31 | 62 |
| One To All (Multicast) | 15 | 24 |
| One To All (Multicast + Linked) | 22 | 41 |
| One From All | 30 | 60 |

**Notes:**

*   "All" corresponds to 64 Tensix cores on Wormhole B0 and 110 Tensix cores on Blackhole P100
*   Blackhole P100 achieves approximately 2× throughput for core-to-core operations due to 512-bit interface width (vs 256-bit on Wormhole)
*   DRAM operations show more modest improvements (~1.5×) due to memory subsystem constraints
*   Multicast operations achieve lower throughput than unicast due to packet replication overhead

**Sources:**[data_movement_doc/general/ideal_performance.md 1-18](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1#L1-L18)

* * *

## Technical Architecture Overview

**Diagram: Technical Architecture Layers**

This diagram illustrates the relationship between hardware platforms, register architecture, API design, and performance characteristics. Stateful registers enable efficient batched operations by preserving address and control information across multiple API calls, while control registers must be explicitly set for each transaction.

**Sources:**[data_movement_doc/general/state_preservation.md 1-33](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/state_preservation.md?plain=1#L1-L33)[data_movement_doc/general/ideal_performance.md 1-18](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1#L1-L18)

* * *

## Register-to-API Mapping

**Diagram: Register Programming in NOC APIs**

This diagram maps NOC hardware registers to their corresponding API functions. Stateful APIs (`noc_async_write`, `noc_async_read`, etc.) rely on address and control registers preserving their values across multiple calls, reducing register programming overhead. The `NOC_CMD_CTRL` register acts as the transaction trigger and must be set for each operation.

**Sources:**[data_movement_doc/general/state_preservation.md 1-33](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/state_preservation.md?plain=1#L1-L33)

* * *

## Register State Preservation

NOC operations are implemented by programming hardware registers in command buffers. Understanding which registers preserve their state across API calls is critical for performance optimization and correct API usage.

### Stateful Registers (Value Preserved)

The following registers maintain their programmed values across multiple NOC API calls unless explicitly modified:

**Address Registers:**

*   `NOC_TARG_ADDR_LO` - Target address low 32 bits (both platforms)
*   `NOC_TARG_ADDR_MID` - Target address middle 32 bits (both platforms)
*   `NOC_TARG_ADDR_HI` - Target address high bits (Blackhole only, unused on Wormhole B0)
*   `NOC_RET_ADDR_LO` - Return address low 32 bits (both platforms)
*   `NOC_RET_ADDR_MID` - Return address middle 32 bits (both platforms)
*   `NOC_RET_ADDR_HI` - Return address high bits (Blackhole only, unused on Wormhole B0)

**Control Registers:**

*   `NOC_PACKET_TAG` - Packet routing and identification
*   `NOC_CTRL` - Control flags for transaction behavior
*   `NOC_AT_LEN_BE` - Address translation length and byte enable
*   `NOC_AT_DATA` - Address translation data

**Blackhole-Specific Stateful Registers:**

*   `NOC_AT_LEN_BE_1` - Extended address translation (Blackhole only)
*   `NOC_BRCST_EXCLUDE` - Multicast exclusion mask (Blackhole only)
*   `NOC_L1_ACC_AT_INSTRN` - L1 access address translation instruction (Blackhole only)
*   `NOC_SEC_CTRL` - Security control (Blackhole only)

### Non-Stateful Registers (Control Flags)

These registers must be explicitly set for each operation:

*   `NOC_CMD_CTRL` - Command control (triggers transactions)
*   `NOC_NODE_ID` - Node identifier for routing
*   `NOC_ENDPOINT_ID` - Endpoint identifier
*   `ECC_CTRL` - Error correction control
*   `NOC_CLEAR_OUTSTANDING_REQ_CNT` - Clear outstanding request counter

### Performance Implications

Stateful register behavior enables efficient batched operations:

1.   **Configure once**: Set target addresses, control flags
2.   **Issue multiple operations**: Only update data pointer and length
3.   **Reduced overhead**: Avoid redundant register programming

For detailed usage patterns, see [Stateful vs Stateless APIs](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.5-stateful-vs-stateless-apis).

**Sources:**[data_movement_doc/general/state_preservation.md 1-33](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/state_preservation.md?plain=1#L1-L33)

* * *

## Platform Architecture Comparison

**Diagram: Platform Evolution from Wormhole B0 to Blackhole P100**

Blackhole P100 represents an architectural evolution with wider NOC interfaces, larger packet sizes, extended addressing, and additional control registers. The 2× interface width improvement translates to 2× throughput for core-to-core operations, while DRAM bandwidth shows more modest gains due to memory subsystem constraints.

**Sources:**[data_movement_doc/general/ideal_performance.md 1-18](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1#L1-L18)[data_movement_doc/general/state_preservation.md 1-33](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/state_preservation.md?plain=1#L1-L33)

* * *

## Key Technical Specifications

| Specification | Wormhole B0 | Blackhole P100 |
| --- | --- | --- |
| **Tensix Cores** | 64 | 110 |
| **Grid Dimensions** | 8×8 (max tested 7×7) | ~10×11 (max tested 9×9) |
| **NOC Interface Width** | 256 bits/cycle | 512 bits/cycle |
| **Maximum Packet Size** | 8 KB | 16 KB |
| **Address Space** | 48-bit | 64-bit |
| **NOC Instances** | 2 (NOC0, NOC1) | 2 (NOC0, NOC1) |
| **Virtual Channels per NOC** | 16 | 16 |
| **RISC-V Processors per Core** | BRISC, NCRISC | BRISC, NCRISC |
| **Stateful Address Registers** | 4 (LO/MID only) | 6 (LO/MID/HI) |
| **Platform-Specific Registers** | 0 | 4 (listed above) |

For detailed platform comparison including register availability and performance characteristics, see [Platform Comparison](https://deepwiki.com/tenstorrent/tt-low-level-documentation/5.2-platform-comparison).

**Sources:**[data_movement_doc/general/ideal_performance.md 1-18](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1#L1-L18)[data_movement_doc/general/state_preservation.md 1-33](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/state_preservation.md?plain=1#L1-L33)

* * *

## Using This Reference

This technical reference is organized to support three primary use cases:

1.   **Performance Planning**: Use baseline metrics to estimate achievable throughput and identify bottlenecks in data movement patterns.

2.   **API Implementation**: Understand register state preservation to write efficient, stateful NOC operations that minimize overhead.

3.   **Cross-Platform Development**: Compare Wormhole B0 and Blackhole P100 specifications to write portable code or optimize for specific platforms.

For practical guidance on using these specifications, refer to:

*   [API Usage Guide](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3-api-usage-guide) for programming patterns
*   [Performance Analysis](https://deepwiki.com/tenstorrent/tt-low-level-documentation/4-performance-analysis) for optimization studies
*   [NOC Architecture](https://deepwiki.com/tenstorrent/tt-low-level-documentation/2.2-noc-architecture) for underlying hardware behavior

**Sources:**[data_movement_doc/general/ideal_performance.md 1-18](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1#L1-L18)[data_movement_doc/general/state_preservation.md 1-33](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/state_preservation.md?plain=1#L1-L33)

Dismiss
Refresh this wiki

Enter email to refresh