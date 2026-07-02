---
project: tt-kmd
github: https://github.com/tenstorrent/tt-kmd
deepwiki: https://deepwiki.com/tenstorrent/tt-kmd/1-overview
---

# Overview

Relevant source files
*   [.github/workflows/mass-build-test.yml](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/.github/workflows/mass-build-test.yml)
*   [AKMBUILD](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/AKMBUILD)
*   [Makefile](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile)
*   [README.md](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1)
*   [SUMMARY.md](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/SUMMARY.md?plain=1)
*   [VERSION_UPDATE.md](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/VERSION_UPDATE.md?plain=1)
*   [dkms.conf](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/dkms.conf)
*   [module.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c)
*   [module.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h)

The Tenstorrent Kernel Mode Driver (`tt-kmd`) is a Linux kernel driver that enables user-space applications to interface with Tenstorrent AI accelerator hardware. The driver provides low-level hardware abstraction, memory management, device initialization, and hardware monitoring capabilities for **Wormhole** and **Blackhole** AI accelerator chips.

## What is tt-kmd?

`tt-kmd` is a PCI device driver implemented as the `tenstorrent.ko` kernel module. It provides:

*   **PCI Device Discovery**: Automatic detection and enumeration of Tenstorrent devices via the PCI subsystem.
*   **Character Device Interface**: User-space API through `/dev/tenstorrent/%d` device nodes with IOCTL magic number `0xFA`.
*   **Memory Management**: DMA buffer allocation, page pinning (GUP), TLB configuration, and outbound iATU management.
*   **Hardware Abstraction**: Polymorphic device support through `tenstorrent_device_class` implementations for different hardware generations.
*   **Hardware Monitoring**: Integration with the Linux `hwmon` subsystem for telemetry (temperature, power, etc.).
*   **ARC Firmware Communication**: Protocol for interacting with the on-chip ARC processor for power management and telemetry.

The driver creates a character device file at `/dev/tenstorrent/N` for each detected Tenstorrent device (where N is the device ordinal), which serves as the primary interface for user applications.

Sources: [README.md 1-11](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L1-L11)[module.c 4-31](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L4-L31)[module.c 64-74](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L64-L74)[module.c 80-90](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L80-L90)

## Supported Hardware

The `tt-kmd` driver currently supports two generations of Tenstorrent AI accelerator chips:

| Chip Name | PCI Vendor ID | PCI Device ID | Device Class | DMA Address Bits | TLB Types | Implementation |
| --- | --- | --- | --- | --- | --- | --- |
| **Wormhole** | `0x1E52` | `0x401E` | `wormhole_class` | 32-bit | 3 kinds (1M/2M/16M) | `wormhole.c` |
| **Blackhole** | `0x1E52` | `0xB140` | `blackhole_class` | 58-bit | 2 kinds (2M/4G) | `blackhole.c` |

**Note**: Grayskull (PCI Device ID `0xFACA`) is defined in the source but is deprecated and not actively supported in this driver version.

Each device class provides hardware-specific implementations through the `tenstorrent_device_class` function table, allowing the core driver to remain hardware-agnostic.

Sources: [README.md 7-9](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L7-L9)[module.c 64-72](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L64-L72)[module.h 19-33](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h#L19-L33)

## High-Level Architecture

### Component Architecture Diagram

This diagram bridges the "Natural Language Space" to "Code Entity Space" by showing how the module initialization leads to device discovery and character device creation.

Sources: [module.c 64-98](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L64-L98)[module.h 25-33](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h#L25-L33)[Makefile 5](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L5-L5)

### Module Initialization Sequence

The following diagram illustrates the boot-time sequence of the driver, from module loading to device-specific initialization.

Sources: [module.c 76-108](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L76-L108)[module.c 110-121](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L110-L121)[Makefile 5](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L5-L5)

## Key Features

### 1. Memory Management

The driver manages several complex memory mapping scenarios:

*   **DMA Buffers**: Allocation of coherent memory via `dma_alloc_coherent`.
*   **Page Pinning**: Pinning user-space buffers for device access using `pin_user_pages`.
*   **TLB Windows**: Mapping specific NoC (Network on Chip) address ranges into PCIe BAR space.
*   **Outbound iATU**: Configuring the Address Translation Unit to allow the device to access host memory.

### 2. User-Space Interface

The driver exposes a robust IOCTL interface (Magic `0xFA`) for:

*   Device information and telemetry.
*   Mapping and unmapping DMA buffers and TLBs.
*   Device reset and recovery.
*   Resource locking for multi-process synchronization.

### 3. Hardware Monitoring

Integration with the `hwmon` subsystem allows standard Linux tools (like `sensors`) to read device metrics:

*   **Telemetry**: Real-time monitoring of voltage, current, and temperature.
*   **ARC Integration**: Communication with ARC firmware to fetch telemetry tags.

Sources: [module.c 33-34](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L33-L34)[module.c 52-62](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L52-L62)[module.h 25-29](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h#L25-L29)

## Module Parameters

Configuration can be adjusted at load time via `modprobe` or `/etc/modprobe.d/`:

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `max_devices` | uint | `32` | Maximum number of Tenstorrent chips to support. |
| `dma_address_bits` | uint | `0` | DMA address bits (0 for automatic). |
| `reset_limit` | uint | `10` | Max resets during boot before giving up. |
| `auto_reset_timeout` | byte | `10` | Seconds for M3 auto reset to occur. |
| `power_policy` | bool | `true` | Enable power policy (low power at probe). |
| `idle_power_down_grace_ms` | uint | `5000` | Delay before sending idle power-down message. |

Sources: [module.c 36-62](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L36-L62)[module.h 25-29](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h#L25-L29)

## Build and Versioning

The driver uses a unified versioning scheme defined in `dkms.conf` and `module.h`.

*   **Current Version**: `2.9.1-pre`
*   **Minimum Kernel**: Linux `5.4`
*   **Build Systems**: Supports `DKMS` (Debian/Ubuntu/Fedora), `AKMS` (Alpine), and NixOS Flakes.

Sources: [dkms.conf 1-2](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/dkms.conf#L1-L2)[module.h 19-22](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h#L19-L22)[module.c 19-21](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L19-L21)[AKMBUILD 1-4](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/AKMBUILD#L1-L4)[VERSION_UPDATE.md 1-16](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/VERSION_UPDATE.md?plain=1#L1-L16)

Dismiss
Refresh this wiki

Enter email to refresh