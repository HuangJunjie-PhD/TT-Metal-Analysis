---
project: tt-low-level-documentation
github: https://github.com/tenstorrent/tt-low-level-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-low-level-documentation/3-api-usage-guide
---

# API Usage Guide

Relevant source files
*   [data_movement_doc/general/dm_best_practices.md](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1)
*   [data_movement_doc/general/intro_to_dm.md](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/intro_to_dm.md?plain=1)

## Purpose and Scope

This guide provides comprehensive documentation for using TT-Metal's NOC (Network-on-Chip) data movement APIs correctly and efficiently. It is targeted at operation and model writers who need to implement high-performance data transfer kernels on Tenstorrent hardware.

**This guide covers:**

*   API design philosophy and fundamental concepts
*   Synchronization requirements and barrier placement
*   Performance optimization patterns and anti-patterns
*   API selection criteria for different scenarios
*   Common mistakes and how to avoid them

**Related documentation:**

*   For NOC architecture fundamentals, see [NOC Architecture](https://deepwiki.com/tenstorrent/tt-low-level-documentation/2.2-noc-architecture)
*   For platform-specific differences, see [Hardware Platforms](https://deepwiki.com/tenstorrent/tt-low-level-documentation/2.3-hardware-platforms)
*   For detailed performance analysis, see [Performance Analysis](https://deepwiki.com/tenstorrent/tt-low-level-documentation/4-performance-analysis)
*   For specific topics, see subsections: [Core API Concepts](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.1-core-api-concepts), [Asynchronous Operations](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.2-asynchronous-operations-and-batching), [Circular Buffers](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.3-circular-buffers), [Virtual Channels](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.4-virtual-channels-usage), [Stateful APIs](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.5-stateful-vs-stateless-apis), [Posted Writes](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.6-posted-writes-optimization)

* * *

## API Design Philosophy

The NOC APIs are designed around three core principles that maximize hardware utilization:

**1. Asynchronous by Default:** All data movement operations (`noc_async_read`, `noc_async_write`) are non-blocking. They enqueue commands to the NOC hardware and return immediately, allowing the kernel to issue multiple transactions in flight simultaneously.

**2. Explicit Synchronization:** The kernel writer must explicitly insert barriers (`noc_async_read_barrier`, `noc_async_write_barrier`) at points where data must be available. This gives fine-grained control over when to wait versus when to overlap communication with computation.

**3. State Preservation for Efficiency:** The NOC hardware maintains configuration registers across operations. Stateful APIs (`noc_async_read_with_state`, `noc_async_write_one_packet_with_state`) leverage this to reduce register programming overhead when performing repeated operations with similar parameters.

Sources: [data_movement_doc/general/dm_best_practices.md 1-10](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L1-L10)[data_movement_doc/general/intro_to_dm.md 4-6](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/intro_to_dm.md?plain=1#L4-L6)

* * *

## API Taxonomy

The following diagram categorizes the NOC data movement APIs by their operational characteristics:

**API Family Classification**

Sources: [data_movement_doc/general/dm_best_practices.md 236-258](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L236-L258)

* * *

## API Function Reference Table

The following table provides a quick reference for all core NOC APIs:

| API Function | Category | Purpose | When to Use |
| --- | --- | --- | --- |
| `noc_async_read()` | Read | General-purpose asynchronous read from NOC address | Single or infrequent reads |
| `noc_async_read_set_state()` | Read | Configure NOC read state registers | Before repeated reads with same source |
| `noc_async_read_with_state()` | Read | Execute read using pre-configured state | Repeated reads, reuses register state |
| `noc_async_read_barrier()` | Synchronization | Block until all pending reads complete | Before consuming read data |
| `noc_async_write()` | Write | General-purpose asynchronous write to NOC address | Single or infrequent writes |
| `noc_async_write_multicast()` | Write | Broadcast write to multiple destinations | One-to-many data distribution |
| `noc_async_write_one_packet_set_state()` | Write | Configure NOC write state for small transfers | Before repeated small writes (<8KB WH, <16KB BH) |
| `noc_async_write_one_packet_with_state()` | Write | Execute small write using pre-configured state | Repeated small writes, reuses state |
| `noc_async_write_barrier()` | Synchronization | Block until all pending writes complete and ack | Before kernel exit or state change |
| `noc_async_writes_flushed()` | Synchronization | Ensure writes sent (not necessarily completed) | When send completion is sufficient |
| `get_noc_addr()` | Address | Convert (x, y, local_addr) to NOC address | Core-to-core addressing |
| `get_noc_addr_from_bank_id()` | Address | Convert (bank_id, local_addr) to NOC address | DRAM addressing |
| `get_noc_multicast_addr()` | Address | Generate multicast address for rectangular region | Multicast write setup |
| `cb_reserve_back()` | Circular Buffer | Reserve space for producer to write | Before writing to CB |
| `cb_push_back()` | Circular Buffer | Make reserved space available to consumer | After writing to CB |
| `cb_wait_front()` | Circular Buffer | Wait for data to be available | Before reading from CB |
| `cb_pop_front()` | Circular Buffer | Release consumed data | After reading from CB |
| `get_write_ptr()` | Circular Buffer | Get pointer to CB write location | Access CB memory for writing |
| `get_read_ptr()` | Circular Buffer | Get pointer to CB read location | Access CB memory for reading |

Sources: [data_movement_doc/general/dm_best_practices.md 236-258](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L236-L258)

* * *

## Critical Synchronization Patterns

Understanding when and where to place barriers is fundamental to correct and efficient NOC usage. The following diagram illustrates the four critical synchronization points:

**Synchronization Decision Tree**

Sources: [data_movement_doc/general/dm_best_practices.md 7-62](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L7-L62)

### Synchronization Point Details

**1. Before Data Use (Read Operations)**

After issuing `noc_async_read()` operations, you must insert `noc_async_read_barrier()` before accessing the destination memory. The read is asynchronous, and data is not guaranteed to arrive until the barrier completes.

**2. Buffer Full Conditions**

When the NOC command buffer reaches capacity, it cannot accept new operations. The code must wait until space becomes available. Use `noc_async_writes_flushed()` to drain the buffer without waiting for operation completion (faster than full barrier).

**3. Context Switch or State Change**

Before changing NOC configuration state or switching between different operation patterns, ensure all pending operations complete with `noc_async_write_barrier()`. This prevents state corruption.

**4. Kernel Termination**

Before returning from a kernel, all outstanding write operations must complete. Otherwise, writes may be lost or partially executed. Use `noc_async_write_barrier()` before the final `return` statement.

Sources: [data_movement_doc/general/dm_best_practices.md 52-62](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L52-L62)

* * *

## Common Anti-Patterns and Solutions

The following diagram contrasts inefficient patterns with their optimized alternatives:

**Performance Anti-Patterns vs Best Practices**

Sources: [data_movement_doc/general/dm_best_practices.md 7-150](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L7-L150)

* * *

## API Selection Guide

Use this decision tree to select the appropriate API for your use case:

**API Selection Decision Tree**

Sources: [data_movement_doc/general/dm_best_practices.md 89-234](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L89-L234)

* * *

## Virtual Channel Assignment Strategy

Virtual channels (VCs) separate traffic by **bandwidth characteristics**, not to parallelize identical traffic. The following table shows the correct assignment strategy:

| Virtual Channel | Traffic Class | Rationale |
| --- | --- | --- |
| `VC 0` | DRAM operations | DRAM has different (lower) bandwidth characteristics |
| `VC 1` | L1 and Ethernet operations | Both have same bandwidth characteristics |
| `VC 2` | Flow control / credit messages | Separate control plane from data plane |
| `VC 3` | Priority or alternative traffic | Additional separation if needed |

**Important:** Software-controllable VCs for unicast operations are limited to values 0, 1, 2, 3. Multicast operations must use VC 4. Other VCs are reserved for system use.

**Anti-Pattern:** Do NOT use multiple VCs to split same-class traffic to the same destination. Due to VC arbitration occurring before port arbitration, this actually degrades performance. See [Virtual Channels Usage](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.4-virtual-channels-usage) for detailed explanation.

Example from [data_movement_doc/general/dm_best_practices.md 167-181](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L167-L181):

Sources: [data_movement_doc/general/dm_best_practices.md 152-181](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L152-L181)[data_movement_doc/general/dm_best_practices.md 214-234](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L214-L234)

* * *

## Circular Buffer Operation Pairing

Circular buffers require strict pairing of reserve/push and wait/pop operations. The following diagram shows the correct producer-consumer pattern:

**Circular Buffer Lifecycle**

**Critical Rule:** The number of pages reserved in `cb_reserve_back(cb_id, n)` must exactly match the total pushed via `cb_push_back()` calls. Similarly, `cb_wait_front(cb_id, n)` must match `cb_pop_front()` totals.

Example from [data_movement_doc/general/dm_best_practices.md 68-80](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L68-L80):

Sources: [data_movement_doc/general/dm_best_practices.md 63-88](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L63-L88)

* * *

## Address Generation Patterns

The NOC uses a 64-bit addressing scheme that encodes physical coordinates and local addresses. Always use the provided helper functions:

| Helper Function | Use Case | Address Format |
| --- | --- | --- |
| `get_noc_addr(x, y, local_addr)` | Core-to-core transfers | Physical coordinates to NOC address |
| `get_noc_addr_from_bank_id(bank_id, local_addr)` | DRAM access | Bank ID to DRAM NOC address |
| `get_noc_multicast_addr(x_start, y_start, x_end, y_end, local_addr)` | Rectangular multicast | Region coordinates to multicast address |

**Anti-Pattern from [data_movement_doc/general/dm_best_practices.md 126-134](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L126-L134):**

Sources: [data_movement_doc/general/dm_best_practices.md 122-134](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L122-L134)

* * *

## Platform-Specific Considerations

The APIs are largely platform-independent, but there are critical threshold differences:

| Consideration | Wormhole B0 | Blackhole P100 |
| --- | --- | --- |
| **Max packet size** | 8 KB | 16 KB |
| **Interface width** | 256 bits/cycle | 512 bits/cycle |
| **One-packet threshold** | Use `*_one_packet` APIs for transfers < 8KB | Use `*_one_packet` APIs for transfers < 16KB |
| **Register state** | Some registers non-stateful | More registers stateful |

For detailed platform differences, see [Hardware Platforms](https://deepwiki.com/tenstorrent/tt-low-level-documentation/2.3-hardware-platforms) and [Platform Comparison](https://deepwiki.com/tenstorrent/tt-low-level-documentation/5.2-platform-comparison).

Sources: [data_movement_doc/general/intro_to_dm.md 22-24](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/intro_to_dm.md?plain=1#L22-L24)[data_movement_doc/general/dm_best_practices.md 209-213](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L209-L213)

* * *

## Performance Optimization Checklist

When writing data movement kernels, verify the following for optimal performance:

*   - [x] **Batching:** Issue multiple operations before barriers (target: 4-16 ops per batch)
*   - [x] **Stateful APIs:** Use `*_set_state()` / `*_with_state()` for repeated operations
*   - [x] **Multicast:** Use `noc_async_write_multicast()` for one-to-many patterns
*   - [x] **Virtual Channels:** Assign VCs by bandwidth class, not to parallelize same-class traffic
*   - [x] **Circular Buffers:** Ensure reserve/push and wait/pop are perfectly paired
*   - [x] **Address Generation:** Use `get_noc_addr()` helpers, never manual bit manipulation
*   - [x] **Barrier Placement:** Barriers only at synchronization points, not per-operation
*   - [x] **Packet Size:** Use `*_one_packet` APIs for small transfers (< 8KB WH, < 16KB BH)
*   - [x] **Posted Writes:** Consider posted writes for NOC-saturating workloads (see [Posted Writes](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.6-posted-writes-optimization))
*   - [x] **Kernel Termination:** Always `noc_async_write_barrier()` before `return`

Sources: [data_movement_doc/general/dm_best_practices.md 1-258](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L1-L258)

* * *

## Additional Resources

For detailed API documentation and function signatures, refer to the [TT-Metal Kernel API Documentation](https://docs.tenstorrent.com/tt-metal/latest/tt-metalium/tt_metal/apis/kernel_apis.html#data-movement).

For subsystem-specific guides, see:

*   [Core API Concepts](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.1-core-api-concepts) - Fundamentals and common pitfalls
*   [Asynchronous Operations and Batching](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.2-asynchronous-operations-and-batching) - Maximizing NOC throughput
*   [Circular Buffers](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.3-circular-buffers) - Producer-consumer synchronization
*   [Virtual Channels Usage](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.4-virtual-channels-usage) - Traffic separation strategies
*   [Stateful vs Stateless APIs](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.5-stateful-vs-stateless-apis) - Register state management
*   [Posted Writes Optimization](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.6-posted-writes-optimization) - Advanced performance techniques

Sources: [data_movement_doc/general/dm_best_practices.md 236](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/dm_best_practices.md?plain=1#L236-L236)

Dismiss
Refresh this wiki

Enter email to refresh