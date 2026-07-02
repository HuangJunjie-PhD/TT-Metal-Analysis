---
project: tt-kmd
github: https://github.com/tenstorrent/tt-kmd
deepwiki: https://deepwiki.com/tenstorrent/tt-kmd/6-memory-management
---

# Memory Management

Relevant source files
*   [memory.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c)
*   [memory.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h)
*   [sg_helpers.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/sg_helpers.c)
*   [sg_helpers.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/sg_helpers.h)
*   [tlb.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tlb.c)
*   [tlb.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tlb.h)

## Purpose and Scope

This page provides an overview of the memory management subsystems in the Tenstorrent kernel driver. Memory management encompasses DMA buffer allocation, user-space page pinning, address translation units (iATU and TLB), and memory mapping operations that enable efficient data transfer between host memory, device memory, and across multiple devices.

For detailed information on specific subsystems:

*   DMA buffer allocation: see [DMA Buffer Allocation](https://deepwiki.com/tenstorrent/tt-kmd/6.1-dma-buffer-allocation)
*   User-space page pinning: see [Page Pinning](https://deepwiki.com/tenstorrent/tt-kmd/6.2-page-pinning)
*   TLB window management: see [TLB Management](https://deepwiki.com/tenstorrent/tt-kmd/6.3-tlb-management)
*   Address translation architecture: see [IATU and Address Translation](https://deepwiki.com/tenstorrent/tt-kmd/6.4-iatu-and-address-translation)

The character device interface for memory operations is documented in [IOCTL Command Reference](https://deepwiki.com/tenstorrent/tt-kmd/5.2-ioctl-command-reference), and the memory mapping interface is covered in [Memory Mapping Interface](https://deepwiki.com/tenstorrent/tt-kmd/5.3-memory-mapping-interface).

## Architecture Overview

The memory management subsystem provides three primary mechanisms for data transfer:

1.   **DMA Buffers**: Coherent memory allocated in kernel space via `dma_alloc_coherent`, accessible from both CPU and device.
2.   **Pinned Pages**: User-space memory locked into physical memory using `pin_user_pages_fast` for zero-copy DMA.
3.   **Peer Mappings**: Direct device-to-device memory access for P2P communication via `ioctl_map_peer_bar`.

All memory operations are tracked per file descriptor through `struct chardev_private`, ensuring automatic cleanup when applications exit.

### Memory Management Components

**Diagram: Memory Management Component Architecture**

Sources: [memory.c 362-1227](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L362-L1227)[chardev_private.h 20-61](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev_private.h#L20-L61)[memory.h 20-62](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h#L20-L62)

### Data Structure Relationships

The memory management subsystem uses several key data structures to track resources:

| Structure | Purpose | Storage | Lifetime |
| --- | --- | --- | --- |
| `struct dmabuf` | DMA buffer metadata | Hash table keyed by `buf_index` | Until fd close or explicit free |
| `struct pinned_page_range` | Pinned user memory | Linked list | Until `UNPIN_PAGES` or fd close |
| `struct peer_resource_mapping` | P2P mappings | Linked list | Until fd close |
| `struct bar_mapping` | mmap tracking | Linked list with refcount | Until all VMAs closed |
| `struct tenstorrent_outbound_iatu_region` | iATU state | Array[16] in `tenstorrent_device` | Per allocation |

Sources: [chardev_private.h 29-38](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev_private.h#L29-L38)[memory.h 20-30](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h#L20-L30)[memory.h 57-62](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h#L57-L62)

## Address Translation Architecture

The driver implements a multi-layer address translation architecture to enable flexible access patterns between host and device:

**Diagram: Multi-Layer Address Translation Flow**

Sources: [memory.c 122-186](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L122-L186)[memory.c 556-572](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L556-L572)[tlb.c 9-84](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tlb.c#L9-L84)

### Address Translation Characteristics

**Outbound iATU (Host → Device)**:

*   16 programmable regions per device tracked in `tenstorrent_outbound_iatu_region`[memory.h 60-66](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h#L60-L66)
*   Dynamically allocated top-down [memory.c 54-86](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L54-L86) or bottom-up [memory.c 88-120](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L88-L120)
*   Maps host physical addresses to NOC addresses.
*   Required for NOC DMA operations when the `NOC_DMA` flag is used.
*   Address space limited by `noc_dma_limit` in `tenstorrent_device_class`[memory.c 162](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L162-L162)

**Inbound TLB (Device → Host)**:

*   Hardware-specific window counts and sizes managed by a bitmap allocator in `tlb.c`[tlb.c 9-53](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tlb.c#L9-L53)
*   Wormhole: 156×1MB, 10×2MB, 20×16MB windows in BAR0.
*   Blackhole: 202×2MB windows in BAR0, up to 8×4GB windows in BAR4.
*   Configured via `tenstorrent_device_configure_tlb`[tlb.c 77-84](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tlb.c#L77-L84)

Sources: [memory.c 158-186](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L158-L186)[tlb.c 1-84](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tlb.c#L1-L84)

## IOMMU Handling

The driver adapts to the presence or absence of an IOMMU using `is_iommu_translated`[memory.h 58](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h#L58-L58)

**Diagram: IOMMU Detection and Handling**

When IOMMU is enabled, the driver:

1.   Creates scatter-gather tables using `alloc_chained_sgt_for_pages()`[sg_helpers.c 17-72](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/sg_helpers.c#L17-L72) to avoid contiguous memory allocation.
2.   Maps using `dma_map_sgtable()`[sg_helpers.h 17-25](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/sg_helpers.h#L17-L25) to obtain IOVA addresses.
3.   Verifies the IOMMU produces contiguous mappings.
4.   Uses IOVA addresses for iATU programming.

Sources: [memory.c 556-560](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L556-L560)[sg_helpers.c 17-72](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/sg_helpers.c#L17-L72)[sg_helpers.h 17-25](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/sg_helpers.h#L17-L25)

## Resource Tracking and Cleanup

All memory resources are tracked in per-FD structures and automatically cleaned up via `tenstorrent_memory_cleanup`[memory.h 56](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h#L56-L56) on file descriptor close:

**Diagram: Resource Cleanup Flow**

The cleanup sequence ensures no memory leaks when processes exit and proper teardown of iATU regions.

Sources: [memory.c 1228-1289](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L1228-L1289)[tlb.c 55-75](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tlb.c#L55-L75)

## Memory Mapping Multiplexing

The driver uses an mmap offset encoding scheme to multiplex different resource types. The top 4 bits of the mmap offset encode the resource type, while lower bits encode the offset within that resource.

| Offset Range | Resource Type | Cache Mode |
| --- | --- | --- |
| `0x0_00000000` | BAR0 (Resource 0) | Uncacheable |
| `0x1_00000000` | BAR0 (Resource 0) | Write-Combining |
| `0x6_00000000` | TLB Windows | Uncacheable |
| `0x7_00000000` | TLB Windows | Write-Combining |
| High Offsets | DMA Buffers (0-254) | Coherent |

DMA buffers use high offsets near the 44-bit limit (defined by `MAX_DMA_BUF_SIZE_LOG2`[memory.h 10](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h#L10-L10)) to avoid conflicts.

Sources: [memory.c 286-308](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L286-L308)[memory.c 452-454](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L452-L454)[memory.c 1060-1227](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L1060-L1227)

## Key Functions Reference

| Function | Purpose | Location |
| --- | --- | --- |
| `ioctl_query_mappings()` | Enumerate available mmap regions | [memory.c 362-440](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L362-L440) |
| `ioctl_allocate_dma_buf()` | Allocate coherent DMA buffer | [memory.c 456-543](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L456-L543) |
| `ioctl_pin_pages()` | Pin user pages for zero-copy DMA | [memory.c 574-754](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L574-L754) |
| `ioctl_unpin_pages()` | Release pinned pages | [memory.c 756-790](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L756-L790) |
| `ioctl_map_peer_bar()` | Map peer device BAR for P2P | [memory.c 792-897](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L792-L897) |
| `tenstorrent_device_allocate_tlb()` | Allocate TLB window from bitmap | [tlb.c 9-53](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tlb.c#L9-L53) |
| `tenstorrent_device_free_tlb()` | Release TLB window | [tlb.c 55-75](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tlb.c#L55-L75) |
| `tenstorrent_device_configure_tlb()` | Program TLB registers | [tlb.c 77-84](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tlb.c#L77-L84) |
| `tenstorrent_mmap()` | Map memory to user space | [memory.c 1060-1227](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L1060-L1227) |
| `setup_noc_dma()` | Configure iATU for NOC DMA | [memory.c 158-186](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L158-L186) |
| `configure_outbound_iatu()` | Program iATU hardware | [memory.c 122-155](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L122-L155) |
| `tenstorrent_memory_cleanup()` | Release all resources on fd close | [memory.c 1228-1289](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L1228-L1289) |

Sources: [memory.c 1-1289](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L1-L1289)[tlb.c 1-84](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tlb.c#L1-L84)[memory.h 36-56](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h#L36-L56)

Dismiss
Refresh this wiki

Enter email to refresh