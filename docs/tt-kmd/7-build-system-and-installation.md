---
project: tt-kmd
github: https://github.com/tenstorrent/tt-kmd
deepwiki: https://deepwiki.com/tenstorrent/tt-kmd/7-build-system-and-installation
---

# Build System and Installation

Relevant source files
*   [.github/workflows/mass-build-test.yml](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/.github/workflows/mass-build-test.yml)
*   [AKMBUILD](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/AKMBUILD)
*   [Makefile](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile)
*   [README.md](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1)
*   [VERSION_UPDATE.md](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/VERSION_UPDATE.md?plain=1)
*   [module.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h)

This document describes how to build, install, and configure the Tenstorrent kernel driver module (`tt-kmd`). It covers the supported build systems (DKMS, AKMS, and manual make), the compilation process that produces `tenstorrent.ko`, installation methods for different Linux distributions, and the module parameters that control driver behavior at load time.

For information about loading the module and initial device setup, see [Getting Started](https://deepwiki.com/tenstorrent/tt-kmd/2-getting-started). For details about the module's initialization sequence after loading, see [Module Initialization and Lifecycle](https://deepwiki.com/tenstorrent/tt-kmd/3.1-module-initialization-and-lifecycle).

## Build Architecture

The Tenstorrent kernel driver is built as a single loadable kernel module (`tenstorrent.ko`) that links together multiple object files. The build process integrates with the Linux kernel build system (kbuild) and supports multiple packaging mechanisms for automated kernel version tracking.

Titled: Driver Compilation and Linking

**Build Process Components**

The `Makefile` defines the module composition at [Makefile 4-5](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L4-L5):

`obj-m += tenstorrent.otenstorrent-y := module.o chardev.o enumerate.o interrupt.o wormhole.o blackhole.o msgqueue.o pcie.o sg_helpers.o memory.o tlb.o telemetry.o`
The kernel build system is invoked via `KMAKE` at [Makefile 10-11](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L10-L11)

**Sources:**[Makefile 4-11](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L4-L11)[module.h 19-22](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h#L19-L22)

## Version Management

The driver version is managed across multiple files. `dkms.conf` (referenced in [VERSION_UPDATE.md 6](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/VERSION_UPDATE.md?plain=1#L6-L6)) serves as the primary source of truth, while `module.h` provides defines for the kernel module itself.

Titled: Version Synchronization Workflow

**Version Maintenance**

When updating the version, the following files require manual changes as documented in [VERSION_UPDATE.md 1-17](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/VERSION_UPDATE.md?plain=1#L1-L17):

| File | Update Required |
| --- | --- |
| `dkms.conf` | `PACKAGE_VERSION="X.Y.Z"` |
| `AKMBUILD` | `modver=X.Y.Z` at [AKMBUILD 2](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/AKMBUILD#L2-L2) |
| `module.h` | `MAJOR`, `MINOR`, `PATCH` at [module.h 19-21](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h#L19-L21) |
| `ioctl.h` | `TENSTORRENT_DRIVER_VERSION` (Major version) |

**Sources:**[module.h 19-22](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h#L19-L22)[AKMBUILD 1-4](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/AKMBUILD#L1-L4)[VERSION_UPDATE.md 1-17](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/VERSION_UPDATE.md?plain=1#L1-L17)[Makefile 13-15](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L13-L15)

## Build System Options

The driver supports multiple build and packaging systems to accommodate different Linux distributions.

### DKMS (Dynamic Kernel Module Support)

Used for Debian, Ubuntu, Fedora, and RHEL. It automatically rebuilds the driver upon kernel updates.

*   **Install**: `make dkms`[Makefile 35-38](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L35-L38)
*   **Remove**: `make dkms-remove`[Makefile 40-48](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L40-L48)
*   For details, see [DKMS Configuration](https://deepwiki.com/tenstorrent/tt-kmd/7.1-dkms-configuration).

### AKMS (Alpine Kernel Module Support)

Used specifically for Alpine Linux.

*   **Install**: `make akms`[Makefile 49-51](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L49-L51)
*   **Remove**: `make akms-remove`[Makefile 53-56](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L53-L56)
*   For details, see [AKMS Configuration](https://deepwiki.com/tenstorrent/tt-kmd/7.2-akms-configuration).

### Manual Build with Make

Standard build process using the kernel headers directly.

*   **Command**: `make modules`[Makefile 20-21](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L20-L21)
*   **Install**: `make modules_install`[Makefile 23-24](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L23-L24)
*   For details, see [Manual Build with Make](https://deepwiki.com/tenstorrent/tt-kmd/7.3-manual-build-with-make).

### NixOS Support

The repository includes Nix flake support for declarative installation on NixOS.

*   **Usage**: Add `tt-kmd.overlays.default` and `boot.extraModulePackages` as described in [README.md 38-57](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L38-L57)

**Sources:**[Makefile 35-56](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/Makefile#L35-L56)[README.md 17-66](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L17-L66)[AKMBUILD 1-4](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/AKMBUILD#L1-L4)

## Module Parameters

The driver behavior can be tuned at load time using module parameters. These are defined as `extern` in [module.h 25-29](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h#L25-L29) and implemented in `module.c`.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `max_devices` | `uint` | 32 | Maximum number of Tenstorrent devices to support. |
| `dma_address_bits` | `uint` | 0 | Force DMA address width (0 = auto). |
| `reset_limit` | `uint` | 10 | Max number of consecutive reset attempts. |
| `auto_reset_timeout` | `uchar` | 10 | Timeout in seconds for M3/ARC reset. |
| `power_policy` | `bool` | true | Enable/disable driver-managed power states. |
| `idle_power_down_grace_ms` | `uint` | - | Delay before powering down idle hardware. |

For details on configuration and use cases, see [Module Parameters](https://deepwiki.com/tenstorrent/tt-kmd/7.4-module-parameters).

**Sources:**[module.h 25-29](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h#L25-L29)

## CI and Compatibility

The driver is build-tested against mainline kernel versions from **5.4 through 6.18**[README.md 15](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L15-L15) A automated mass-build pipeline exists in `.github/workflows/mass-build-test.yml` to ensure compatibility across this range [github/workflows/mass-build-test.yml 1-18](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/github/workflows/mass-build-test.yml#L1-L18)

The driver also handles distribution-specific backports, such as RHEL 9.4 API changes, via version macros in [module.h 11-17](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h#L11-L17)

**Sources:**[README.md 13-15](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/README.md?plain=1#L13-L15)[.github/workflows/mass-build-test.yml 1-18](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/.github/workflows/mass-build-test.yml#L1-L18)[module.h 11-17](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.h#L11-L17)

Dismiss
Refresh this wiki

Enter email to refresh