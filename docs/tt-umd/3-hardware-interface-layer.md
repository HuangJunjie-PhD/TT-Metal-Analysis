---
project: tt-umd
github: https://github.com/tenstorrent/tt-umd
deepwiki: https://deepwiki.com/tenstorrent/tt-umd/3-hardware-interface-layer
---

# Hardware Interface Layer

Relevant source files
*   [device/api/umd/device/pcie/pci_device.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/pcie/pci_device.hpp)
*   [device/api/umd/device/pcie/pci_ids.h](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/pcie/pci_ids.h)
*   [device/api/umd/device/tt_kmd_lib/tt_kmd_lib.h](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_kmd_lib/tt_kmd_lib.h)
*   [device/api/umd/device/warm_reset.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/warm_reset.hpp)
*   [device/pcie/ioctl.h](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/pcie/ioctl.h)
*   [device/pcie/pci_device.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/pcie/pci_device.cpp)
*   [device/tt_kmd_lib/tt_kmd_lib.c](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_kmd_lib/tt_kmd_lib.c)
*   [device/warm_reset.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/warm_reset.cpp)
*   [nanobind/py_api_warm_reset.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_warm_reset.cpp)

## Purpose and Scope

The Hardware Interface Layer provides low-level hardware communication mechanisms for Tenstorrent devices. This layer abstracts the details of PCIe communication protocols, manages memory access through Translation Lookaside Buffers (TLBs), coordinates DMA operations, and handles topology discovery. It sits between the [Device Management System](https://deepwiki.com/tenstorrent/tt-umd/2-device-management-system) above and the physical hardware below, providing optimized data transfer paths and hardware initialization.

For details on PCIe operations and the kernel driver interface, see [PCIDevice & Hardware Communication](https://deepwiki.com/tenstorrent/tt-umd/3.1-pcidevice-and-hardware-communication). For memory access strategies, see [Memory Management Systems](https://deepwiki.com/tenstorrent/tt-umd/3.2-memory-management-systems). For hardware enumeration, see [Topology Discovery](https://deepwiki.com/tenstorrent/tt-umd/3.3-topology-discovery).

## Hardware Communication Architecture

The system primarily communicates with Tenstorrent hardware via the PCIe bus using a custom kernel mode driver (KMD). The `PCIDevice` class acts as the primary interface to this driver, utilizing `ioctl` calls and memory-mapped I/O (MMIO).

### Code Entity Space: PCIe Interface

**Sources:**[device/api/umd/device/pcie/pci_device.hpp 121-132](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/pcie/pci_device.hpp#L121-L132)[device/pcie/pci_device.cpp 173-198](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/pcie/pci_device.cpp#L173-L198)[device/pcie/ioctl.h 18-33](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/pcie/ioctl.h#L18-L33)[device/tt_kmd_lib/tt_kmd_lib.c 64-81](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_kmd_lib/tt_kmd_lib.c#L64-L81)

## Key Hardware Components

### PCIDevice: The PCIe Interface

The `PCIDevice` class manages the lifecycle of a single Tenstorrent PCIe device. It handles device enumeration, character device file descriptor management, and BAR (Base Address Register) mappings.

*   **Enumeration:** Discovers devices via `/dev/tenstorrent/` and sysfs [device/pcie/pci_device.cpp 201-212](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/pcie/pci_device.cpp#L201-L212)
*   **Information Retrieval:** Reads vendor IDs, device IDs, and BDF (Bus:Device:Function) strings [device/api/umd/device/pcie/pci_device.hpp 33-52](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/pcie/pci_device.hpp#L33-L52)
*   **Architecture Support:** Identifies if a device is `WORMHOLE_B0` (0x401e) or `BLACKHOLE` (0xb140) [device/api/umd/device/pcie/pci_ids.h 13-14](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/pcie/pci_ids.h#L13-L14)

### Memory Management and TLBs

Tenstorrent hardware uses Translation Lookaside Buffers (TLBs) to map host PCIe address space to internal Network-on-Chip (NoC) addresses.

*   **TLB Allocation:** UMD requests TLB windows from the KMD via `TENSTORRENT_IOCTL_ALLOCATE_TLB`[device/pcie/ioctl.h 29](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/pcie/ioctl.h#L29-L29)
*   **Sizes:** Supported sizes include 1MB, 2MB, 16MB, and 4GB, depending on the architecture [device/api/umd/device/tt_kmd_lib/tt_kmd_lib.h 109-112](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_kmd_lib/tt_kmd_lib.h#L109-L112)
*   **DMA Buffers:** Manages physically contiguous memory for high-speed transfers [device/api/umd/device/pcie/pci_device.hpp 54-61](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/pcie/pci_device.hpp#L54-L61)

For details, see [Memory Management Systems](https://deepwiki.com/tenstorrent/tt-umd/3.2-memory-management-systems).

### Warm Reset and Lifecycle

The `WarmReset` system allows for resetting devices without a full host reboot. It supports architecture-specific reset sequences and utilizes a multi-process synchronization mechanism.

**Sources:**[device/warm_reset.cpp 50-98](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/warm_reset.cpp#L50-L98)[device/api/umd/device/warm_reset.hpp 77-100](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/warm_reset.hpp#L77-L100)[device/pcie/ioctl.h 150-153](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/pcie/ioctl.h#L150-L153)

## Integration with Higher Layers

The Hardware Interface Layer is primarily consumed by the `LocalChip` class in the [Device Management System](https://deepwiki.com/tenstorrent/tt-umd/2-device-management-system). While `LocalChip` handles the logical "Chip" abstraction, it delegates all physical I/O to `PCIDevice` and associated memory managers.

### Key Interactions:

1.   **Device Initialization:**`PCIDevice` is instantiated with a device number (e.g., 0 for `/dev/tenstorrent/0`) [device/api/umd/device/pcie/pci_device.hpp 166](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/pcie/pci_device.hpp#L166-L166)
2.   **Memory Access:**`tt_kmd_lib` provides C-style wrappers for `tt_noc_read32` and `tt_noc_write32` which use TLBs to access device registers [device/api/umd/device/tt_kmd_lib/tt_kmd_lib.h 151-178](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_kmd_lib/tt_kmd_lib.h#L151-L178)
3.   **Reset Coordination:** The `WarmResetCommunication` class ensures that if one process resets a chip, other processes using the same cluster are notified to clean up their state [device/api/umd/device/warm_reset.hpp 63-75](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/warm_reset.hpp#L63-L75)

## Related Child Pages

*   [PCIDevice & Hardware Communication](https://deepwiki.com/tenstorrent/tt-umd/3.1-pcidevice-and-hardware-communication) — Details on ioctl interfaces, BAR mappings, and IOMMU handling.
*   [Memory Management Systems](https://deepwiki.com/tenstorrent/tt-umd/3.2-memory-management-systems) — Details on TLBManager, TlbWindow, and SysmemManager.
*   [Topology Discovery](https://deepwiki.com/tenstorrent/tt-umd/3.3-topology-discovery) — Details on the discovery pipeline and Ethernet link detection.

**Sources:**

*   [device/pcie/pci_device.cpp 173-212](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/pcie/pci_device.cpp#L173-L212)
*   [device/api/umd/device/pcie/pci_device.hpp 33-166](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/pcie/pci_device.hpp#L33-L166)
*   [device/warm_reset.cpp 50-128](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/warm_reset.cpp#L50-L128)
*   [device/api/umd/device/warm_reset.hpp 18-100](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/warm_reset.hpp#L18-L100)
*   [device/api/umd/device/tt_kmd_lib/tt_kmd_lib.h 58-112](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_kmd_lib/tt_kmd_lib.h#L58-L112)
*   [device/pcie/ioctl.h 18-153](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/pcie/ioctl.h#L18-L153)

Dismiss
Refresh this wiki

Enter email to refresh