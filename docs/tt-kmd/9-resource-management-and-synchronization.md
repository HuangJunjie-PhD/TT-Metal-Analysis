---
project: tt-kmd
github: https://github.com/tenstorrent/tt-kmd
deepwiki: https://deepwiki.com/tenstorrent/tt-kmd/9-resource-management-and-synchronization
---

# Resource Management and Synchronization

Relevant source files
*   [chardev.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c)
*   [device.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h)

## Purpose and Scope

This page documents the driver's resource management and synchronization mechanisms. It covers the synchronization primitives used to coordinate concurrent access, the user-space resource locking facility, power state aggregation across multiple clients, and the reset generation tracking system that ensures safe device recovery.

For information about specific resource types (DMA buffers, page pinnings, TLBs), see [Memory Management](https://deepwiki.com/tenstorrent/tt-kmd/6-memory-management). For device lifecycle state transitions, see [PCI Device Discovery and Management](https://deepwiki.com/tenstorrent/tt-kmd/3.3-pci-device-discovery-and-management). For IOCTL command details, see [IOCTL Command Reference](https://deepwiki.com/tenstorrent/tt-kmd/5.2-ioctl-command-reference).

## Architecture Overview

The driver employs a multi-layered synchronization strategy to handle concurrent operations from multiple user-space clients, device resets, and hardware removal. The architecture consists of:

1.   **Kernel-level synchronization primitives** that protect shared device state.
2.   **User-space resource locks** that enable inter-process coordination.
3.   **Power state aggregation** that combines requirements from all active clients.
4.   **Generation-based invalidation** that prevents stale file descriptor access after reset.

### Synchronization Architecture

Sources: [chardev.c 40-46](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L40-L46)[device.h 25-77](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L25-L77)

## Synchronization Primitives

The driver uses several primary synchronization primitives to coordinate access to shared state. The most critical is the `reset_rwsem`, which ensures that no hardware access occurs while a device reset is in progress.

*   **`reset_rwsem`**: A read-write semaphore where normal operations (IOCTLs, mmap) take the read lock, and reset operations take the write lock to drain all in-flight activity.
*   **`chardev_mutex`**: Protects the `open_fds_list` and manages the reference counting for first-open/last-close hardware initialization and teardown.
*   **`iatu_mutex`**: Serializes access to the outbound address translation units (iATU) during configuration.
*   **`kernel_tlb_mutex`**: Manages allocation of kernel-reserved TLB windows.

For details, see [Synchronization Primitives](https://deepwiki.com/tenstorrent/tt-kmd/9.1-synchronization-primitives).

Sources: [device.h 35](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L35-L35)[device.h 43](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L43-L43)[device.h 66](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L66-L66)[chardev.c 472-475](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L472-L475)

## User-Space Resource Locks

The driver provides a bitmask-based resource locking mechanism via the `LOCK_CTL` ioctl. This allows user-space processes to coordinate access to specific hardware blocks (e.g., specific engines or memory regions) without kernel-space overhead.

*   **Operations**: Supports `ACQUIRE`, `ACQUIRE_BLOCKING`, `RELEASE`, and `TEST`.
*   **Safety**: Locks are automatically released if the process crashes or closes its file descriptor.
*   **Implementation**: Uses a 16-bit `resource_lock` bitmap in `tenstorrent_device` and a `resource_lock_waitqueue` for blocking acquires.

For details, see [Resource Locks](https://deepwiki.com/tenstorrent/tt-kmd/9.2-resource-locks).

Sources: [device.h 48-49](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L48-L49)[chardev.c 270-329](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L270-L329)

## Power Management

The driver implements an aggregated power policy. Instead of a single "on/off" switch, the driver tracks the power requirements of every open file descriptor and applies the union of all requested states to the hardware.

*   **Aggregation**: Uses a "MAX of settings" and "OR of flags" strategy. If one client requires high performance and another requires low power, high performance is maintained.
*   **Idle Power-Down**: Supports a deferred idle power-down grace period via `power_down_work`. If the last file descriptor is closed, the driver waits for a configurable period before powering down the ASIC.
*   **Power-Aware Clients**: Clients can opt-in to explicit power management by opening the device with `O_APPEND`.

For details, see [Power Management](https://deepwiki.com/tenstorrent/tt-kmd/9.3-power-management).

Sources: [device.h 61](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L61-L61)[chardev.c 377-437](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L377-L437)[chardev.c 633-636](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L633-L636)

## Reset and Recovery

Device resets are managed as a global state transition. When a reset is triggered (either via ASIC-specific registers, PCIe hot reset, or DMC reset), the driver increments a `reset_gen` counter.

*   **Generation Tracking**: Every file descriptor stores the `reset_gen` at the time it was opened. If the device's `reset_gen` increments, all existing file descriptors become stale and must be reopened.
*   **VMA Zapping**: During reset, `tenstorrent_vma_zap` is called to invalidate all existing user-space memory mappings to the device, preventing access to unstable hardware.
*   **Exclusive Access**: The `reset_rwsem` is held for write during the entire reset procedure to ensure no other threads are accessing the device BARs or registers.

### Reset State Machine

For details, see [Reset and Recovery](https://deepwiki.com/tenstorrent/tt-kmd/9.4-reset-and-recovery).

Sources: [device.h 34](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L34-L34)[chardev.c 191-266](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L191-L266)[chardev.c 484-487](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L484-L487)

Dismiss
Refresh this wiki

Enter email to refresh