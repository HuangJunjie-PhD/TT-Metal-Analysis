---
project: tt-tutorial
github: https://github.com/tenstorrent/tt-tutorial
deepwiki: https://deepwiki.com/tenstorrent/tt-tutorial/7-performance-and-optimization)
---

# Performance and Optimization

Relevant source files
*   [operations/dispatch_overhead.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch_overhead.md?plain=1)

This document covers advanced performance optimization techniques for Tenstorrent hardware, with particular focus on dispatch system optimization, kernel execution timing, and resource allocation strategies. The material addresses the tradeoffs between dispatch overhead, kernel initialization latency, and runtime performance across different hardware platforms.

For model-level optimization and bring-up techniques, see [Model Tutorials and Bring-up](https://deepwiki.com/tenstorrent/tt-tutorial/5.1-model-tutorials-and-bring-up). For dispatch system implementation details, see [Dispatch System and Optimization](https://deepwiki.com/tenstorrent/tt-tutorial/7.1-dispatch-system-and-optimization).

## Dispatch Performance Overview

The dispatch system is the critical path for kernel execution on Tenstorrent hardware. Performance optimization requires understanding the interaction between dispatch timing, worker core initialization, and resource contention. There is no single optimization strategy that benefits all workloads - finding optimal performance requires experimentation and careful analysis of timing characteristics.

### Core Dispatch Components

The dispatch system operates through a coordinated interaction between several key components:

_Sources: [operations/dispatch\_overhead.md 10-58](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L10-L58)_

## Fast Dispatch Mechanisms

Fast dispatch operates through a two-message protocol that enables asynchronous kernel preparation and synchronous execution triggers.

### Launch and Go Message Protocol

The `Launch Message` contains kernel metadata including:

*   Kernel ID (`k_id`)
*   Number of circular buffers
*   Number of runtime arguments
*   Configuration details for execution setup

The `Go Message` provides the final execution trigger after the previous kernel completes. Optimal timing achieves approximately 700 nanoseconds between the done signal and go message.

_Sources: [operations/dispatch\_overhead.md 13-58](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L13-L58)_

### Dispatch Timing Scenarios

Performance varies significantly based on timing alignment between dispatcher and worker cores:

The ring buffer constraint represents a significant bottleneck when kernels are too large to coexist. Dispatch times of 7-15 microseconds typically indicate this scenario.

_Sources: [operations/dispatch\_overhead.md 64-113](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L64-L113)_

## Resource Contention and Timing

### Asynchronous Dispatch Limitations

The dispatch system supports asynchronous operation with kernels dispatched ahead of current execution, but faces constraints:

*   Maximum lookahead: 6 kernels (`m > 6` causes stalls)
*   Ring buffer capacity: 70KB (statically configured)
*   Resource sharing: DRAM bandwidth, NoC bandwidth, and dispatch operations compete

Compute-bound kernels experience minimal dispatch interference, while DRAM-bound and NoC-bound workloads suffer performance degradation from dispatch resource contention.

_Sources: [operations/dispatch\_overhead.md 129-145](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L129-L145)_

## Worker Initialization Optimization

### NCRISC Kernel Binary Management (Wormhole)

On Wormhole platforms, `NCRISC` workers execute from IRAM, requiring binary copy from L1 memory:

| Scenario | Binary Size | Copy Time | Performance Impact |
| --- | --- | --- | --- |
| Best case | ~2KB | ~200ns | Minimal |
| Common case | ~4KB | ~400ns | Acceptable |
| Worst case | ~16KB | ~1600ns | Significant |

Copy performance operates at approximately 10 bytes per cycle. Binary size optimization directly impacts initialization latency.

_Sources: [operations/dispatch\_overhead.md 158-176](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L158-L176)_

### BRISC vs NCRISC Role Assignment

Optimal performance requires strategic assignment of Reader/Compute/Writer roles:

**Recommended Configuration:**

*   Reader kernels → `NCRISC` (smaller binaries, faster IRAM copy)
*   Writer kernels → `BRISC` (no IRAM copy overhead)

This arrangement enables `NCRISC` to begin binary copy for Kernel N+1 while `BRISC` completes the Writer portion of Kernel N.

**Mitigation for Suboptimal Conditions:** When dispatcher lag or ring buffer constraints prevent early copy initiation, reverse the assignment to minimize pipeline stalls.

_Sources: [operations/dispatch\_overhead.md 177-217](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L177-L217)_

### Circular Buffer Configuration

CB initialization time scales with configuration complexity:

| Platform | Time per CB | Worst Case (32 CBs) |
| --- | --- | --- |
| Wormhole | ~25 cycles | ~800ns |
| Blackhole | ~20 cycles | ~640ns |

Current implementation initializes CBs after binary copy. Upcoming optimization will parallelize these operations, bounding total initialization time by binary copy duration.

_Sources: [operations/dispatch\_overhead.md 218-240](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L218-L240)_

## Kernel Group Configuration

### Group Definition and Impact

A kernel group consists of cores receiving identical kernel binaries. Group proliferation directly impacts dispatch performance:

*   Compile-time argument variations create separate binaries
*   Each group requires individual multicast dispatch
*   Binary sizes up to 8KB cannot overlap with prior execution

_Sources: [operations/dispatch\_overhead.md 241-262](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L241-L262)_

### Physical Layout Constraints

Platform multicast capabilities affect kernel group efficiency:

**Wormhole:** Rectangular multicast blocks only

*   L-shaped layouts split into multiple groups
*   Example: Single L-shape becomes 2-3 rectangular groups

**Blackhole:** Flexible multicast support

*   L-shaped layouts supported as single group
*   Reduced dispatch overhead

_Sources: [operations/dispatch\_overhead.md 263-284](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L263-L284)_

## NoC Performance and Bandwidth

### Flow Control Units (Flits)

NoC data transmission operates via flits with platform-specific characteristics:

| Platform | Flit Size | Bandwidth | Header Overhead |
| --- | --- | --- | --- |
| Wormhole | 32 bytes | 32 GB/s | 1 flit per transaction |
| Blackhole | 64 bytes | 64 GB/s | 1 flit per transaction |

Minimum transaction size: 2 flits (1 header + 1 data)

_Sources: [operations/dispatch\_overhead.md 285-303](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L285-L303)_

## Runtime Arguments vs Kernel Groups

### RTA Performance Characteristics

Runtime Arguments (RTAs) provide dynamic kernel configuration but introduce dispatch costs:

*   **First RTA:** High cost (entire 32B flit)
*   **Subsequent RTAs:** Effectively free until flit boundary
*   **Scaling impact:** 256 RTAs × 4B × 3 kernels × 64 cores = 196KB dispatch overhead

### Optimization Guidelines

**Use RTAs when:**

*   Small binary size increase (e.g., 2KB → 2.1KB)
*   Significant code reuse across cores
*   Dynamic behavior configuration needed

**Use separate kernel groups when:**

*   Large monolithic kernels (8KB+)
*   Execution path optimization critical
*   Minimal code sharing between variants

_Sources: [operations/dispatch\_overhead.md 328-348](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L328-L348)_

## Platform-Specific Considerations

### T3K Ethernet Dispatch

T3K platforms use Ethernet cores for dispatch with inherent limitations:

| Component | Tensix Dispatch | Ethernet Dispatch |
| --- | --- | --- |
| L1 Cache | 1.5MB | 256KB |
| RISC-V Cores | 5 | 1 |
| Buffering | Extensive | Minimal |
| Performance | High throughput | Limited throughput |

Ethernet dispatch cannot match Tensix performance due to resource constraints. Design dispatch strategies accordingly when targeting T3K platforms.

_Sources: [operations/dispatch\_overhead.md 391-416](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L391-L416)_

### Global Variable Initialization

Static initialization outperforms runtime assignment:

**Inefficient:**

**Optimized:**

_Sources: [operations/dispatch\_overhead.md 349-390](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L349-L390)_

## Conclusion

Performance optimization requires balancing dispatch overhead, initialization latency, and resource contention. Key optimization strategies include:

*   Minimizing uncovered latency through proper timing alignment
*   Strategic RTA usage to reduce kernel binary proliferation
*   Optimized kernel group layout and role assignment
*   Platform-aware dispatch configuration

Continuous profiling and workload-specific tuning unlock optimal performance on Tenstorrent architectures.

_Sources: [operations/dispatch\_overhead.md 417-428](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/dispatch\_overhead.md?plain=1#L417-L428)_

Dismiss
Refresh this wiki

Enter email to refresh