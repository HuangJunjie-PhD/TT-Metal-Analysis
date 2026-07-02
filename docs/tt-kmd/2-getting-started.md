---
project: tt-kmd
github: https://github.com/tenstorrent/tt-kmd
deepwiki: https://deepwiki.com/tenstorrent/tt-kmd/2-getting-started
---

# Getting Started

Relevant source files
*   [.github/workflows/mass-build-test.yml](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/.github/workflows/mass-build-test.yml)
*   [Makefile](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile)
*   [README.md](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1)
*   [VERSION_UPDATE.md](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/VERSION_UPDATE.md?plain=1)
*   [contrib/packaging/nix/overlay.nix](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/contrib/packaging/nix/overlay.nix)
*   [dkms-post-install](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/dkms-post-install)
*   [flake.lock](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/flake.lock)
*   [flake.nix](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/flake.nix)
*   [modprobe.d-tenstorrent.conf](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/modprobe.d-tenstorrent.conf)
*   [udev-50-tenstorrent.rules](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/udev-50-tenstorrent.rules)

This document provides a quick start guide for installing, loading, and using the Tenstorrent kernel driver. It covers installation methods for various Linux distributions, module configuration parameters, and basic verification steps.

* * *

## Prerequisites

Before installing the driver, ensure your system meets the following requirements:

**Required Software:**

*   Linux kernel 5.4 or later (The driver is build-tested from 5.4 through 6.18) [README.md 13-15](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L13-L15)
*   Build tools: `make`, `gcc`, kernel build utilities
*   For DKMS-based installation: DKMS package manager
*   For AKMS-based installation: AKMS package manager (Alpine Linux)
*   For manual builds: direct access to kernel build system

**Supported Hardware:**

*   Wormhole devices (PCI Device ID: `0x401E`) [README.md 8](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L8-L8)[udev-50-tenstorrent.rules 2](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/udev-50-tenstorrent.rules#L2-L2)
*   Blackhole devices (PCI Device ID: `0xB140`) [README.md 9](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L9-L9)[udev-50-tenstorrent.rules 3](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/udev-50-tenstorrent.rules#L3-L3)

**Distribution-Specific Package Installation:**

| Distribution | Command |
| --- | --- |
| Debian/Ubuntu | `apt install dkms linux-headers-$(uname -r) build-essential` |
| Fedora | `dnf install dkms kernel-devel-$(uname -r) gcc make` |
| RHEL/CentOS | `dnf install epel-release && dnf install dkms kernel-devel-$(uname -r) gcc make` |
| Alpine Linux | `apk add akms linux-lts-dev build-base` |

Sources: [README.md 13-29](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L13-L29)

* * *

## Installation Methods

### Installation Flow Overview

Sources: [README.md 17-56](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L17-L56)[Makefile 35-55](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L35-L55)

* * *

### Pre-built Packages

Pre-built `.deb` packages are available for Debian and Ubuntu systems from the GitHub releases page [README.md 19](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L19-L19)

**Installation:**

The package automatically installs via DKMS and loads the module. After installation, verify with:

Sources: [README.md 19](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L19-L19)

* * *

### DKMS Installation from Source

Dynamic Kernel Module Support (DKMS) automatically rebuilds the module when the kernel is updated.

**Installation:**

**What `make dkms` does:**

1.   Executes `sudo dkms add .`[Makefile 36](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L36-L36)
2.   Executes `sudo dkms install --force tenstorrent/$(VERSION)`[Makefile 37](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L37-L37)
3.   Executes `sudo modprobe tenstorrent`[Makefile 38](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L38-L38)

The Makefile extracts the version from `dkms.conf` using `tools/current-version`[Makefile 13-14](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L13-L14) A post-install script [dkms-post-install 1-5](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/dkms-post-install#L1-L5) is typically used to copy udev rules [udev-50-tenstorrent.rules 1-4](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/udev-50-tenstorrent.rules#L1-L4) to `/etc/udev/rules.d/`.

**Uninstallation:**

This removes all installed versions and unloads the module [Makefile 40-47](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L40-L47)

Sources: [Makefile 35-47](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L35-L47)[dkms-post-install 1-5](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/dkms-post-install#L1-L5)[VERSION_UPDATE.md 14-16](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/VERSION_UPDATE.md?plain=1#L14-L16)

* * *

### AKMS Installation (Alpine Linux)

Alpine Linux uses AKMS (Alpine Kernel Module Support).

**Installation:**

**What `make akms` does:**

1.   Executes `doas akms install .`[Makefile 50](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L50-L50)
2.   Executes `doas modprobe tenstorrent`[Makefile 51](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L51-L51)

**Uninstallation:**

This unloads the module and removes the AKMS package [Makefile 53-55](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L53-L55)

Sources: [README.md 33-36](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L33-L36)[Makefile 49-55](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L49-L55)

* * *

### NixOS Installation

NixOS uses a declarative configuration approach with Nix flakes [flake.nix 1-55](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/flake.nix#L1-L55)

**Configuration Steps:**

1.   **Add flake input** to your `flake.nix`[README.md 40-43](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L40-L43):

1.   **Add overlay**[README.md 45-48](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L45-L48):

1.   **Add to kernel modules and udev packages**[README.md 50-54](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L50-L54):

1.   **Apply configuration:**

Sources: [README.md 38-56](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L38-L56)[flake.nix 1-55](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/flake.nix#L1-L55)[contrib/packaging/nix/overlay.nix 1-33](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/contrib/packaging/nix/overlay.nix#L1-L33)

* * *

### Manual Build with Make

For distributions without DKMS/AKMS support or for development purposes.

**Build:**

The Makefile invokes the kernel build system [Makefile 10-11](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L10-L11) with the module directory set to the current directory [Makefile 8](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L8-L8)

**Installation:**

**Source Files Compiled:** The module links several object files into `tenstorrent.o`[Makefile 4-5](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L4-L5):

*   `module.o`, `chardev.o`, `enumerate.o`, `interrupt.o`, `wormhole.o`, `blackhole.o`, `msgqueue.o`, `pcie.o`, `sg_helpers.o`, `memory.o`, `tlb.o`, `telemetry.o`.

Sources: [Makefile 4-24](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L4-L24)

* * *

## Module Parameters

The driver supports configuration parameters via `modprobe`.

### Parameter Reference Table

| Parameter | Type | Description |
| --- | --- | --- |
| `max_devices` | uint | Maximum number of Tenstorrent devices (chips) to support (Default: 16 in sample conf) |
| `dma_address_bits` | uint | DMA address bits, 0 for automatic |
| `reset_limit` | uint | Maximum number of times to reset device during boot |
| `auto_reset_timeout` | byte | Timeout duration in seconds for M3 auto reset |

### Setting Parameters

**Via modprobe configuration file:** Create or edit `/etc/modprobe.d/tenstorrent.conf`[modprobe.d-tenstorrent.conf 1-14](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/modprobe.d-tenstorrent.conf#L1-L14):

Sources: [modprobe.d-tenstorrent.conf 1-14](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/modprobe.d-tenstorrent.conf#L1-L14)

* * *

## Verifying Installation

### Check Character Devices

The driver registers device files named `/dev/tenstorrent/%d`[README.md 11](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L11-L11)

### Udev Rules and Symlinks

The driver provides udev rules [udev-50-tenstorrent.rules 1-4](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/udev-50-tenstorrent.rules#L1-L4) that set permissions to `0666` and create persistent symlinks by ASIC ID:

Sources: [README.md 11](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L11-L11)[udev-50-tenstorrent.rules 1-4](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/udev-50-tenstorrent.rules#L1-L4)

* * *

## CI and Build Testing

The driver is continuously tested against multiple kernel versions using a mass-build test pipeline [github/workflows/mass-build-test.yml 1-18](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/github/workflows/mass-build-test.yml#L1-L18)

**Mass Build Test Script:** The script `test/mass-build-test` is used to verify compatibility from kernel `v5.4` through `v6.18`[github/workflows/mass-build-test.yml 17](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/github/workflows/mass-build-test.yml#L17-L17)

Sources: [.github/workflows/mass-build-test.yml 1-18](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/.github/workflows/mass-build-test.yml#L1-L18)

Dismiss
Refresh this wiki

Enter email to refresh