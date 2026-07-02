---
project: riscv-ocelot
github: https://github.com/tenstorrent/riscv-ocelot
deepwiki: https://deepwiki.com/tenstorrent/riscv-ocelot/3-memory-system
---

# Memory System

Relevant source files
*   [docs/sections/decode-stage.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/decode-stage.rst)
*   [docs/sections/execution-stages.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/execution-stages.rst)
*   [docs/sections/future-work.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/future-work.rst)
*   [docs/sections/issue-units.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/issue-units.rst)
*   [docs/sections/load-store-unit.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/load-store-unit.rst)
*   [docs/sections/memory-system.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/memory-system.rst)
*   [docs/sections/physical-realization.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/physical-realization.rst)
*   [docs/sections/reg-file-bypass-network.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/reg-file-bypass-network.rst)
*   [docs/sections/rename-stage.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/rename-stage.rst)
*   [docs/sections/reorder-buffer.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/reorder-buffer.rst)
*   [src/main/scala/common/tile.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/common/tile.scala)
*   [src/main/scala/ifu/icache.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/icache.scala)
*   [src/main/scala/lsu/dcache.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/dcache.scala)
*   [src/main/scala/lsu/mshrs.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/mshrs.scala)
*   [src/main/scala/lsu/prefetcher.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/prefetcher.scala)
*   [src/main/scala/lsu/tlb.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/tlb.scala)

This document describes the memory system in the RISCV-V Ocelot processor, including the cache hierarchy, address translation, and memory interfaces. The memory system is responsible for efficiently moving data between the processor core and off-chip memory while maintaining the illusion of a simple linear memory space.

## Memory Hierarchy Overview

The RISC-V Ocelot implements a hierarchical memory system to bridge the performance gap between the processor and main memory. The memory hierarchy consists of separate L1 instruction and data caches connected to the memory system through the TileLink interface.

Sources: [src/main/scala/common/tile.scala 129-146](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/common/tile.scala#L129-L146)[src/main/scala/lsu/dcache.scala 374-404](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/dcache.scala#L374-L404)

## Data Cache Architecture

The Ocelot uses a non-blocking data cache (BoomNonBlockingDCache) that can handle multiple outstanding memory requests. This allows the processor to continue executing instructions while waiting for memory operations to complete, which is essential for out-of-order execution.

### BoomNonBlockingDCache

The BoomNonBlockingDCache is the primary data cache implementation used in Ocelot. It's a sophisticated component that supports multiple outstanding memory operations, cache coherence, and speculative execution.

Sources: [src/main/scala/lsu/dcache.scala 414-473](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/dcache.scala#L414-L473)[src/main/scala/lsu/dcache.scala 807-822](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/dcache.scala#L807-L822)

### Cache Organization

The data cache is parameterizable and can be configured in two main ways:

1.   **Duplicated Data Array**: Each way has a complete data array copy
2.   **Banked Data Array**: Data is distributed across multiple banks to increase bandwidth

The organization of the data array is configured by the `numDCacheBanks` parameter in BoomCoreParams.

Sources: [src/main/scala/lsu/dcache.scala 281-373](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/dcache.scala#L281-L373)[src/main/scala/lsu/dcache.scala 461-462](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/dcache.scala#L461-L462)

## Miss Status Handling Register (MSHR)

The MSHR is a critical component for handling cache misses in a non-blocking cache. It tracks the status of outstanding cache misses and manages the process of fetching data from memory and updating the cache.

Sources: [src/main/scala/lsu/mshrs.scala 106-388](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/mshrs.scala#L106-L388)

## Cache Request Flow

The data cache processes requests in multiple stages. This diagram shows the flow of a memory request through the data cache.

Sources: [src/main/scala/lsu/dcache.scala 580-750](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/dcache.scala#L580-L750)

## Store Pipeline and Write-back

The data cache handles stores differently from loads. Stores must be committed in the ROB before they can update memory. The write-back unit is responsible for writing dirty cache lines back to memory.

Sources: [src/main/scala/lsu/dcache.scala 24-143](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/dcache.scala#L24-L143)

## Probe Unit and Cache Coherence

The probe unit handles coherence traffic from the memory system. It processes TileLink B-channel requests (probes) and updates the cache state accordingly.

Sources: [src/main/scala/lsu/dcache.scala 145-257](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/dcache.scala#L145-L257)

## Translation Lookaside Buffer (TLB)

The TLB caches virtual-to-physical address translations to accelerate memory accesses. The Ocelot uses a non-blocking data TLB (NBDTLB) that can handle multiple outstanding translation requests.

Sources: [src/main/scala/lsu/tlb.scala 17-357](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/tlb.scala#L17-L357)

## Prefetching

The memory system includes a prefetcher interface to improve performance by fetching memory lines before they are explicitly requested. The default implementation is a next-line prefetcher (NLPrefetcher) that fetches the next cache line after a miss.

Sources: [src/main/scala/lsu/prefetcher.scala 24-72](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/prefetcher.scala#L24-L72)

## Memory Consistency Model

The Ocelot implements the RISC-V Weak Memory Ordering (RVWMO) memory consistency model. The memory system maintains several key properties:

1.   Write → Read ordering is relaxed (loads can bypass earlier stores to different addresses)
2.   Read → Read ordering is maintained (loads to the same address appear in order)
3.   A thread can read its own writes early (store-to-load forwarding)

The Load/Store Unit (LSU) maintains these properties by:

*   Checking for memory ordering violations during execution
*   Maintaining a store queue for pending stores
*   Supporting store-to-load forwarding

Sources: [docs/sections/load-store-unit.rst 76-101](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/load-store-unit.rst#L76-L101)

## Integration with the Processor Core

The memory system integrates with the rest of the processor through the LSU, which manages the interface between the core's execution units and the memory hierarchy.

Sources: [docs/sections/load-store-unit.rst 1-127](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/load-store-unit.rst#L1-L127)[src/main/scala/common/tile.scala 130-146](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/common/tile.scala#L130-L146)

## TileLink Interface

The memory system communicates with the rest of the system using the TileLink protocol. TileLink is a scalable, cache-coherent interconnect protocol designed for RISC-V systems.

Sources: [src/main/scala/lsu/dcache.scala 776-822](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/dcache.scala#L776-L822)[src/main/scala/common/tile.scala 127-146](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/common/tile.scala#L127-L146)

## Memory System Parameters

The memory system is highly configurable through various parameters, which can be set in the configuration:

| Parameter | Description | Default Value |
| --- | --- | --- |
| nSets | Number of cache sets | Configurable |
| nWays | Number of cache ways | Configurable |
| blockBytes | Size of a cache block in bytes | Configurable |
| nMSHRs | Number of MSHRs | Configurable |
| nSDQ | Size of store data queue | Configurable |
| nRPQ | Size of replay queue | Configurable |
| nMMIOs | Number of MMIO ports | Configurable |
| numDCacheBanks | Number of data cache banks | Configurable |
| enablePrefetching | Whether prefetching is enabled | Configurable |

Sources: [src/main/scala/lsu/dcache.scala 383-405](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/dcache.scala#L383-L405)[src/main/scala/lsu/mshrs.scala 545-546](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/mshrs.scala#L545-L546)

Dismiss
Refresh this wiki

Enter email to refresh