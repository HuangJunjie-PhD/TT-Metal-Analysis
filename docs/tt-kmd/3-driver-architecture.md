---
project: tt-kmd
github: https://github.com/tenstorrent/tt-kmd
deepwiki: https://deepwiki.com/tenstorrent/tt-kmd/3-driver-architecture
---

# Driver Architecture

Relevant source files
*   [AKMBUILD](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/AKMBUILD)
*   [device.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h)
*   [dkms.conf](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/dkms.conf)
*   [enumerate.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c)
*   [module.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c)
*   [module.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h)

This page provides an overview of the tt-kmd driver's internal architecture, explaining the major components, their relationships, and key design patterns. The driver is structured as a Linux kernel module that provides a character device interface for user-space applications to interact with Tenstorrent hardware.

For detailed information about specific subsystems:

*   Module loading and initialization sequence: see [Module Initialization and Lifecycle](https://deepwiki.com/tenstorrent/tt-kmd/3.1-module-initialization-and-lifecycle)
*   Device abstraction and polymorphism implementation: see [Device Abstraction Layer](https://deepwiki.com/tenstorrent/tt-kmd/3.2-device-abstraction-layer)
*   PCI device discovery and management: see [PCI Device Discovery and Management](https://deepwiki.com/tenstorrent/tt-kmd/3.3-pci-device-discovery-and-management)
*   User-space API: see [User-Space Interface](https://deepwiki.com/tenstorrent/tt-kmd/5-user-space-interface)
*   Memory operations: see [Memory Management](https://deepwiki.com/tenstorrent/tt-kmd/6-memory-management)

## Core Components

The driver is organized into several major components that work together to provide device functionality:

| Component | Primary Files | Purpose |
| --- | --- | --- |
| Module Core | `module.c` | Module initialization, parameter handling, global resource management |
| Device Enumeration | `enumerate.c` | PCI device discovery, probe/remove lifecycle, device registry |
| Character Device | `chardev.c` | User-space interface via `/dev/tenstorrent/*` nodes |
| Device Abstraction | `device.h` | Core device structures and device class vtable pattern |
| Memory Management | `memory.c` | DMA buffers, page pinning, TLB management, IATU configuration |
| Hardware Drivers | `wormhole.c`, `blackhole.c` | Device-specific implementations |

**Module Structure**

**Module Initialization Flow**

The driver follows a standard initialization sequence in `ttdriver_init()`:

1.   Creates global debugfs directory (`tt_debugfs_root`) [module.c 82](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L82-L82)
2.   Creates global procfs directory (`tt_procfs_root`) [module.c 84](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L84-L84)
3.   Initializes character device subsystem via `init_char_driver(max_devices)`[module.c 90](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L90-L90)
4.   Registers PCI driver via `tenstorrent_pci_register_driver()`[module.c 94](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L94-L94)

Sources: [module.c 76-108](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L76-L108)

## Device Structures

The driver uses two core structures to represent devices:

**struct tenstorrent_device**

The base device structure contains all common state shared across device types:

Sources: [device.h 25-77](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L25-L77)

**struct tenstorrent_device_class**

The device class acts as a virtual function table, enabling polymorphic behavior for different hardware types:

| Function Pointer | Purpose |
| --- | --- |
| `init_device` | Initialize device structures, map BARs |
| `init_hardware` | Configure PCIe, start firmware |
| `cleanup_device` | Unmap BARs, free device resources |
| `cleanup_hardware` | Put device into low-power state |
| `reset` | Perform device reset |
| `configure_tlb` | Configure TLB entry with NOC parameters |
| `describe_tlb` | Query TLB configuration |
| `configure_outbound_atu` | Program outbound IATU region |
| `noc_write32` | Write to NOC address space |
| `save_reset_state` / `restore_reset_state` | Save/restore reset-critical state |

Sources: [device.h 81-119](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L81-L119)

## Device Class Polymorphism Pattern

The driver implements polymorphism in C using function pointer tables. This allows the generic driver code to operate on any device type without conditional logic.

**Device Class Registration**

The PCI device ID table in `module.c` associates each PCI device ID with its corresponding device class via the `driver_data` field [module.c 64-72](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L64-L72) During `tenstorrent_pci_probe()`, the driver retrieves the device class and uses it to dispatch device-specific operations.

Sources: [module.c 64-72](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L64-L72)[device.h 81-119](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L81-L119)

**Polymorphic Call Pattern**

Generic code calls through the `dev_class` pointer without knowing the concrete device type [device.h 31](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L31-L31) The vtable dispatches to the appropriate implementation.

Sources: [device.h 31](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L31-L31)[device.h 81-119](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L81-L119)

## Component Interaction Architecture

**High-Level Component Flow**

Sources: [module.c 76-108](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L76-L108)[enumerate.c 33](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L33-L33)[device.h 25-77](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L25-L77)

## Device Lifecycle Management

**Probe and Remove Sequence**

The driver manages devices from PCI discovery through removal. For Galaxy systems, the driver includes specialized logic in `galaxy_bdf_to_ordinal()` to assign ordinals based on the physical board and chip location [enumerate.c 48-76](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L48-L76)

Key steps in Probe:

1.   Allocate `tenstorrent_device` using device class's `instance_size`[device.h 83](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L83-L83)
2.   Assign unique ordinal via XArray allocator `tenstorrent_dev_xa`[enumerate.c 33](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L33-L33)
3.   Initialize reference count (`kref`) [device.h 26](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L26-L26)
4.   Call device class `init_device()` to map BARs [device.h 91](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L91-L91)
5.   Call device class `init_hardware()` to configure hardware [device.h 92](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L92-L92)
6.   Register character device node.

Sources: [enumerate.c 33-76](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L33-L76)[device.h 81-119](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L81-L119)

## Reference Counting and Lifetime Management

The driver uses reference counting (`kref`) to manage device lifetime [device.h 26](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L26-L26) The device structure remains valid as long as there are active references. References are released via `tenstorrent_device_put()`[device.h 121](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L121-L121)

## Per-FD Resource Tracking

Each open file descriptor gets a `struct chardev_private` that tracks all resources allocated through that FD. This enables automatic cleanup when the FD is closed, even if the process crashes.

**Resource Tracking in Diagnostics**

The `mappings_seq_show` function in `enumerate.c` demonstrates the depth of tracking, including:

*   Pinned pages and associated iATU entries [enumerate.c 124-155](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L124-L155)
*   Coherent DMA buffers and their IOMMU/Physical addresses [enumerate.c 158-175](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L158-L175)
*   Allocated TLB windows [enumerate.c 178-180](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L178-L180)
*   BAR and TLB VMA mappings [enumerate.c 183-207](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L183-L207)

Sources: [enumerate.c 84-212](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L84-L212)

## Module Parameters

The driver exposes runtime configuration parameters in `module.c`:

| Parameter | Type | Default | Purpose |
| --- | --- | --- | --- |
| `max_devices` | uint | 32 | Maximum supported device count [module.c 36](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L36-L36) |
| `dma_address_bits` | uint | 0 (auto) | DMA address width for allocations [module.c 40](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L40-L40) |
| `reset_limit` | uint | 10 | Max reset attempts during boot [module.c 44](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L44-L44) |
| `auto_reset_timeout` | byte | 10 | Seconds for M3 auto-reset [module.c 48](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L48-L48) |
| `power_policy` | bool | true | Enable low power at probe [module.c 52](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L52-L52) |
| `idle_power_down_grace_ms` | uint | 5000 | Delay for idle power-down [module.c 56](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L56-L56) |

Sources: [module.c 36-62](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L36-L62)

## Global Resources

The driver maintains several global resources:

| Resource | Type | Purpose |
| --- | --- | --- |
| `tenstorrent_dev_xa` | XArray | Device registry [enumerate.c 33](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L33-L33) |
| `tt_debugfs_root` | dentry | Root debugfs directory [module.c 33](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L33-L33) |
| `tt_procfs_root` | proc_dir_entry | Root procfs directory [module.c 34](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L34-L34) |

Sources: [enumerate.c 33](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L33-L33)[module.c 33-34](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L33-L34)

## Diagnostic Interfaces

The driver exposes diagnostic information through debugfs and procfs:

**debugfs mappings File**

The `mappings_seq_show` function provides a snapshot of all active memory mappings for a device, including PIDs, virtual addresses, physical addresses (PA), and IOVAs [enumerate.c 84-212](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L84-L212)

**procfs driver/tenstorrent**

The driver creates a root entry at `/proc/driver/tenstorrent` to house device-specific process information [module.c 84](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L84-L84)

Sources: [enumerate.c 84-212](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L84-L212)[module.c 84](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L84-L84)

Dismiss
Refresh this wiki

Enter email to refresh