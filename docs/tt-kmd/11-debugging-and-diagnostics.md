---
project: tt-kmd
github: https://github.com/tenstorrent/tt-kmd
deepwiki: https://deepwiki.com/tenstorrent/tt-kmd/11-debugging-and-diagnostics
---

# Debugging and Diagnostics

Relevant source files
*   [enumerate.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c)
*   [tools/fix-tt-hotplug-bars](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/fix-tt-hotplug-bars)
*   [tools/make-installer](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/make-installer)
*   [tools/make-source-release](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/make-source-release)
*   [tools/power.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/power.c)
*   [tools/reset.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/reset.c)

## Purpose and Scope

This page documents the debugging and diagnostic facilities provided by the Tenstorrent kernel driver (`tt-kmd`). These tools enable developers to troubleshoot hardware issues, identify resource leaks, validate driver state, and monitor device access patterns. The driver provides structured diagnostic interfaces through `debugfs` and `procfs`, along with standalone developer tools and a comprehensive test suite.

For information about hardware telemetry and monitoring data (temperature, voltage, clocks), see [Hardware Monitoring and Telemetry](https://deepwiki.com/tenstorrent/tt-kmd/8-hardware-monitoring-and-telemetry). For general testing workflows and the CI/CD pipeline, see [Testing and Development](https://deepwiki.com/tenstorrent/tt-kmd/10-testing-and-development).

## Diagnostic Interface Overview

The driver exposes diagnostic information through multiple kernel interfaces and user-space tools:

| Interface | Location | Purpose | Permissions |
| --- | --- | --- | --- |
| **debugfs mappings** | `/sys/kernel/debug/tenstorrent/{ordinal}/mappings` | Resource allocation visibility | Root or `CAP_SYS_ADMIN` for addresses |
| **procfs pids** | `/proc/tenstorrent/{ordinal}/pids` | Active process tracking | World-readable |
| **Reset Tool** | `tools/reset` | Targeted device reset (ASIC/DMC) | Root / Device access |
| **Power Tool** | `tools/power` | Manual power state manipulation | Root / Device access |
| **Test suite** | `test/ttkmd_test` | Automated validation | Executable |

Sources: [enumerate.c 84-111](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L84-L111)[enumerate.c 1161-1172](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L1161-L1172)[tools/reset.c 160-210](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/reset.c#L160-L210)[tools/power.c 154-175](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/power.c#L154-L175)

## debugfs Mappings File

### File Location and Purpose

The `debugfs` mappings file provides real-time visibility into all resource allocations for a device. It is created during device registration and shows which processes hold which resources.

**Diagram: debugfs Mappings File Creation and Access Flow**

Sources: [chardev.c 104-129](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L104-L129)[enumerate.c 1277](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L1277-L1277)[enumerate.c 1154-1159](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L1154-L1159)

### File Format and Structure

The file begins with a warning header indicating format instability [enumerate.c 93-95](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L93-L95) The output is structured as a table with the following columns:

| Column | Description |
| --- | --- |
| **PID** | Process ID of the file descriptor owner [enumerate.c 107](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L107-L107) |
| **Comm** | Command name of the process [enumerate.c 110](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L110-L110) |
| **Type** | Resource type (OPEN_FD, PIN_PAGES, DMA_BUF, BAR, TLB) [enumerate.c 110-202](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L110-L202) |
| **Mapping Details** | Type-specific allocation details (Addresses, IDs, Sizes) |

### Address Visibility and Permissions

Physical addresses and IOVAs are only displayed to users with `CAP_SYS_ADMIN` capability [enumerate.c 91](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L91-L91) Non-privileged users see zero addresses. This prevents information leakage while still allowing resource tracking.

**Diagram: Address Visibility Control**

Sources: [enumerate.c 91](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L91-L91)[enumerate.c 133-140](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L133-L140)[enumerate.c 159-160](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L159-L160)

## Resource Types in Mappings File

### Open File Descriptors (OPEN_FD)

Every open file descriptor to `/dev/tenstorrent/{ordinal}` is tracked. The driver maintains a per-device list of `chardev_private` structures in `open_fds_list`[enumerate.c 101](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L101-L101)

### Pinned Pages (PIN_PAGES / PIN_PAGES+IATU)

User-space pages pinned via `TENSTORRENT_IOCTL_PIN_PAGES` are tracked per file descriptor. The output shows:

*   **VA**: Virtual address of the pinned region [enumerate.c 125](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L125-L125)
*   **PA/IOVA**: Physical address or IOVA (based on IOMMU state) [enumerate.c 132-137](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L132-L137)
*   **NOC**: NOC address if an outbound iATU region is associated [enumerate.c 150](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L150-L150)
*   **Size**: Size in bytes [enumerate.c 126](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L126-L126)

### DMA Buffers (DMA_BUF / DMA_BUF+IATU)

Driver-allocated coherent DMA buffers are identified by their `buf_index`. The output includes:

*   **ID**: The `dmabuf->index`[enumerate.c 169](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L169-L169)
*   **PA/IOVA**: Physical address or IOVA [enumerate.c 161](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L161-L161)
*   **NOC**: NOC base address if an iATU region is configured [enumerate.c 170](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L170-L170)

### BAR and TLB Mappings

The file tracks active memory mappings created via `mmap()`:

*   **BAR**: Shows BAR index, cache mode (WC/UC), offset, and size [enumerate.c 189-194](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L189-L194)
*   **TLB**: Shows TLB ID and the corresponding BAR + Offset it maps to [enumerate.c 196-202](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L196-L202)

## procfs PID Tracking

The driver creates a `procfs` directory for each device at `/proc/tenstorrent/{ordinal}/` containing a `pids` file [chardev.c 106-108](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L106-L108) This file lists all PIDs with open file descriptors, one per line.

**Diagram: procfs PID File Structure**

Sources: [chardev.c 106-108](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L106-L108)[enumerate.c 1161-1172](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L1161-L1172)

## Standalone Developer Tools

The repository includes several standalone C and Bash tools for low-level diagnostics and system fixing.

### Reset Tool (`tools/reset.c`)

A utility to trigger device resets via `TENSTORRENT_IOCTL_RESET_DEVICE`[tools/reset.c 40](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/reset.c#L40-L40)

*   **ASIC Reset**: Triggers a standard ASIC reset [tools/reset.c 43](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/reset.c#L43-L43)
*   **DMC Reset**: Triggers an ASIC + DMC (Dynamic Memory Controller) reset [tools/reset.c 44](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/reset.c#L44-L44)
*   **Functionality**: It identifies the device type (Wormhole/Blackhole), retrieves the BDF, triggers the reset IOCTL, and waits for the device to reappear in sysfs [tools/reset.c 185-230](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/reset.c#L185-L230)

### Power Tool (`tools/power.c`)

Allows manual manipulation of power states via `TENSTORRENT_IOCTL_SET_POWER_STATE`[tools/power.c 35](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/power.c#L35-L35)

*   **Flags**: Control AI Clock, PHY Wakeup, Tensix Enable, and L2CPU Enable [tools/power.c 46-49](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/power.c#L46-L49)
*   **Settings**: Set up to 14 numeric power parameters [tools/power.c 50](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/power.c#L50-L50)
*   **Aggregation**: The driver aggregates these settings across all active clients [tools/power.c 171-173](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/power.c#L171-L173)

### Hotplug BAR Fixer (`tools/fix-tt-hotplug-bars`)

A Bash script to resolve BAR allocation failures on Thunderbolt-connected devices. It identifies Tenstorrent devices with `<unassigned>` BARs, locates the parent Thunderbolt bridge, removes it, and triggers a PCI rescan to force the kernel to reallocate bridge windows [tools/fix-tt-hotplug-bars 28-105](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/fix-tt-hotplug-bars#L28-L105)

## Troubleshooting Guide

### Common Issues and Resolutions

| Symptom | Potential Cause | Diagnostic Step |
| --- | --- | --- |
| **Device not found** | Driver not loaded or PCI probe failed | Check `lsmod` and `dmesg | grep tenstorrent`. |
| **Unassigned BARs** | BIOS/Kernel failed to allocate BAR space (common on Thunderbolt) | Run `tools/fix-tt-hotplug-bars`. |
| **IOCTL -ENODEV** | Device is in reset or detached | Check `needs_hw_init` or `detached` flags in driver state. |
| **Resource Leaks** | Application failed to close FDs | Inspect `/sys/kernel/debug/tenstorrent/{n}/mappings`. |
| **Mutex Timeouts** | Long-running resets or hung ARC firmware | Look for `"...locked, skipping details..."` in mappings file [enumerate.c 113](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L113-L113) |

### Manual Reset Recovery

If a device becomes unresponsive, use the reset tool:

Sources: [tools/reset.c 161-164](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/reset.c#L161-L164)[tools/reset.c 199-201](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/tools/reset.c#L199-L201)

### Diagnostic File Lock Contention

The `mappings_seq_show` function uses `mutex_trylock` to avoid blocking the diagnostic read if the driver is busy [enumerate.c 112](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L112-L112)[enumerate.c 117](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L117-L117) If the output shows `"...locked..."`, it indicates high contention on the `chardev_private` or `iatu_mutex`.

Sources: [enumerate.c 112-121](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L112-L121)

Dismiss
Refresh this wiki

Enter email to refresh