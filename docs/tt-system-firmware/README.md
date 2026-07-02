---
project: tt-system-firmware
github: https://github.com/tenstorrent/tt-system-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-system-firmware
---

# Overview

 Relevant source files

* [.github/workflows/docs.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/docs.yml)
* [README.md](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1)
* [VERSION](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/VERSION)
* [app/dmc/CMakeLists.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/CMakeLists.txt)
* [app/dmc/VERSION](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/VERSION)
* [app/dmc/boards/qemu\_riscv32.conf](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/boards/qemu_riscv32.conf)
* [app/dmc/boards/tt\_blackhole\_tt\_blackhole\_dmc.overlay](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/boards/tt_blackhole_tt_blackhole_dmc.overlay)
* [app/smc/VERSION](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/VERSION)
* [doc/Doxyfile](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/Doxyfile)
* [doc/Makefile](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/Makefile)
* [doc/\_doxygen/doxygen-awesome-tt.css](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/_doxygen/doxygen-awesome-tt.css)
* [doc/\_doxygen/doxygen-awesome-tt.js](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/_doxygen/doxygen-awesome-tt.js)
* [doc/\_doxygen/main.md](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/_doxygen/main.md?plain=1)
* [doc/api.rst](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/api.rst)
* [doc/conf.py](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/conf.py)
* [doc/contribute/coding\_guidelines/index.rst](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/contribute/coding_guidelines/index.rst)
* [doc/contribute/index.rst](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/contribute/index.rst)
* [doc/images/tt\_logo\_white.png](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/images/tt_logo_white.png)
* [doc/index.rst](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/index.rst)
* [doc/requirements.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/requirements.txt)
* [doc/zephyr.rst](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/zephyr.rst)
* [scripts/get\_ttzp\_version.py](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/get_ttzp_version.py)
* [zephyr/blobs/fw\_pack-grayskull.tar.gz](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/fw_pack-grayskull.tar.gz)
* [zephyr/blobs/fw\_pack-wormhole.tar.gz](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/fw_pack-wormhole.tar.gz)
* [zephyr/blobs/tt\_blackhole\_erisc.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_erisc.bin)
* [zephyr/blobs/tt\_blackhole\_erisc\_params.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_erisc_params.bin)
* [zephyr/blobs/tt\_blackhole\_gddr\_params\_GALAXY\_REVC.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_gddr_params_GALAXY_REVC.bin)
* [zephyr/blobs/tt\_blackhole\_libpciesd.a](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_libpciesd.a)
* [zephyr/blobs/tt\_blackhole\_serdes\_eth\_fw.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_serdes_eth_fw.bin)
* [zephyr/blobs/tt\_blackhole\_serdes\_eth\_fwreg.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_serdes_eth_fwreg.bin)
* [zephyr/module.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml)

**tt-system-firmware** is the Zephyr-based firmware repository for Tenstorrent AI accelerators. It provides the complete system management and hardware initialization stack for Blackhole, Wormhole, and Grayskull AI accelerator boards. The firmware manages hardware subsystems (Tensix compute cores, GDDR memory, Ethernet interconnects, PCIe interfaces), implements dynamic voltage/frequency scaling, provides telemetry and monitoring capabilities, and exposes management interfaces to host software.

This page provides a high-level introduction to the firmware architecture, components, and organization. For detailed information about specific subsystems:

* System architecture details: see [System Architecture](/tenstorrent/tt-system-firmware/1.1-system-architecture)
* Version numbering scheme: see [Versioning and Release Strategy](/tenstorrent/tt-system-firmware/1.2-versioning-and-release-strategy)
* Board-specific configurations: see [Supported Hardware Platforms](/tenstorrent/tt-system-firmware/1.3-supported-hardware-platforms)
* SMC firmware details: see [System Management Controller (SMC)](/tenstorrent/tt-system-firmware/2.1-system-management-controller-(smc))
* DMC firmware details: see [Device Management Controller (DMC)](/tenstorrent/tt-system-firmware/2.2-device-management-controller-(dmc))
* Build system details: see [Build System](/tenstorrent/tt-system-firmware/6-build-system)

## Firmware Architecture

The firmware employs a three-layer hierarchical architecture where each layer has distinct responsibilities and execution environments:

**Sources:** [README.md1-49](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1#L1-L49) [VERSION1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/VERSION#L1-L6) [app/smc/VERSION1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/VERSION#L1-L6) [app/dmc/VERSION1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/VERSION#L1-L6) [zephyr/module.yml1-235](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L1-L235)

### Layer Responsibilities

**System Management Controller (SMC)** runs on a dedicated ARC processor on the board and serves as the primary management interface to the host. It processes 256 different message types via a request/response queue system, manages board-level peripherals (fans, voltage regulators, sensors), implements DVFS (Dynamic Voltage and Frequency Scaling) with thermal/power throttling, collects telemetry every 100ms, and coordinates with the DMC for device-level operations.

**Device Management Controller (DMC)** runs on a dedicated RISC-V processor on the board and provides hardware-level control capabilities. It implements JTAG-based chip reset and recovery mechanisms, loads bootrom and firmware from SPI flash, communicates with CMFW via the CM2DM protocol over SMBus, and manages low-level hardware initialization sequences.

**Chip Management Firmware (CMFW)** executes on ARC cores within the Blackhole ASIC and manages all on-chip subsystems. It initializes and controls the Tensix grid (14×10 compute cores), GDDR memory controllers (8 instances), Ethernet subsystem (14 channels), and PCIe interfaces (2 instances). It implements NOC (Network-on-Chip) translation for fault tolerance, provides harvesting support to route around defective tiles, and loads specialized firmware to MRISC, ERISC, and SerDes controllers.

**Sources:** [README.md15-16](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1#L15-L16) Diagram 1 from high-level architecture

## Repository Structure

The repository is organized into applications, libraries, and binary firmware components:

**Sources:** [VERSION1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/VERSION#L1-L6) [app/smc/VERSION1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/VERSION#L1-L6) [app/dmc/VERSION1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/VERSION#L1-L6) [zephyr/module.yml1-235](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L1-L235)

### Version Hierarchy

The firmware uses a hierarchical versioning strategy where the product version (19.7.99.0) contains independently versioned sub-applications. Each component maintains its own VERSION file:

| Component | Version File | Current Version | Format |
| --- | --- | --- | --- |
| Product | `VERSION` | 19.7.99.0 | MAJOR.MINOR.PATCHLEVEL.TWEAK |
| SMC | `app/smc/VERSION` | 0.29.99.0 | MAJOR.MINOR.PATCHLEVEL.TWEAK |
| DMC | `app/dmc/VERSION` | 0.23.99.0 | MAJOR.MINOR.PATCHLEVEL.TWEAK |

The version parsing is implemented in [scripts/get\_ttzp\_version.py1-59](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/get_ttzp_version.py#L1-L59) which provides both string and packed u32 representations. The u32 format packs version components as `(major << 24) | (minor << 16) | (patchlevel << 8) | tweak`.

**Sources:** [VERSION1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/VERSION#L1-L6) [app/smc/VERSION1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/VERSION#L1-L6) [app/dmc/VERSION1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/VERSION#L1-L6) [scripts/get\_ttzp\_version.py11-54](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/get_ttzp_version.py#L11-L54)

### Binary Firmware Components

The repository includes pre-compiled firmware binaries for hardware subsystems that cannot be built from source. These are declared in the Zephyr module definition and verified via SHA256 checksums:

| Firmware Blob | Purpose | Version | Declaration |
| --- | --- | --- | --- |
| `tt_blackhole_gddr_init.bin` | MRISC firmware for GDDR memory training | 2.13 | [zephyr/module.yml82-90](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L82-L90) |
| `tt_blackhole_gddr_params_*.bin` | Board-specific GDDR parameters (13 variants) | 2.13 | [zephyr/module.yml91-198](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L91-L198) |
| `tt_blackhole_erisc.bin` | ERISC firmware for Ethernet protocol | 1.9.0 | [zephyr/module.yml37-44](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L37-L44) |
| `tt_blackhole_erisc_params.bin` | Ethernet firmware parameters | 1.9.0 | [zephyr/module.yml46-54](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L46-L54) |
| `tt_blackhole_serdes_eth_fw*.bin` | Ethernet SerDes firmware and registers | 0.9.16 | [zephyr/module.yml64-81](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L64-L81) |
| `tt_blackhole_libpciesd.a` | PCIe SerDes static library | 0.2 | [zephyr/module.yml55-63](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L55-L63) |
| `tt_blackhole_nano_bootcode.bin` | Modified bootrom for Blackhole | 0.1 | [zephyr/module.yml199-207](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L199-L207) |
| `tt_blackhole_trisc_dest_wipe.bin` | TRISC 0 DEST wipe firmware | 0.2 | [zephyr/module.yml226-234](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L226-L234) |

**Sources:** [zephyr/module.yml36-234](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L36-L234)

## Hardware Support

The firmware supports multiple Tenstorrent accelerator architectures and board variants:

### Blackhole Architecture

* **P100A**: Single-chip PCIe boards with GDDR parameters [zephyr/module.yml100-108](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L100-L108)
* **P100B**: Variant with different GDDR configuration [zephyr/module.yml109-117](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L109-L117)
* **P150A/B/C**: Mid-range boards with revision-specific parameters [zephyr/module.yml127-153](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L127-L153)
* **P300A/B/C**: High-end boards with multiple configuration revisions [zephyr/module.yml154-180](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L154-L180)
* **Galaxy**: Multi-chip systems (32-chip configurations) [zephyr/module.yml181-198](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L181-L198)
* **ORION**: Development platform variant [zephyr/module.yml91-99](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L91-L99)

### Legacy Architectures

* **Wormhole**: Previous generation (fw\_pack-wormhole.tar.gz, version 19.7.0-rc1) [zephyr/module.yml208-216](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L208-L216)
* **Grayskull**: First generation (fw\_pack-grayskull.tar.gz, version 80.18.0.0) [zephyr/module.yml217-225](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L217-L225)

Board definitions are located in the Zephyr board structure under `boards/tenstorrent/` as specified in [zephyr/module.yml11](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L11-L11)

**Sources:** [zephyr/module.yml91-225](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L91-L225)

## Build System Integration

The firmware integrates with Zephyr as a module, using `west` as the meta-build tool:

**Sources:** [zephyr/module.yml1-35](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L1-L35) [README.md19-32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1#L19-L32)

### Build Process

The build system uses CMake with Zephyr's `sysbuild` support to build multiple applications. Firmware images are signed using `imgtool` for MCUBoot integration, which provides signature verification and secure boot capabilities. The signing process is documented in the firmware signing section (see [Firmware Signing and MCUBoot](/tenstorrent/tt-system-firmware/6.3-firmware-signing-and-mcuboot)).

Custom runners enable firmware flashing via `tt-flash` tool and hardware debugging via `pyluwen` library. These runners are declared in [zephyr/module.yml26-29](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L26-L29)

**Sources:** [zephyr/module.yml1-35](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L1-L35) [README.md19-32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1#L19-L32)

## Documentation Organization

This documentation is organized hierarchically to guide you from high-level concepts to implementation details:

### Architecture Documentation

* **[System Architecture](/tenstorrent/tt-system-firmware/1.1-system-architecture)**: Three-layer firmware architecture (SMC/DMC/CMFW), communication protocols, hardware abstraction
* **[Versioning and Release Strategy](/tenstorrent/tt-system-firmware/1.2-versioning-and-release-strategy)**: Hierarchical versioning scheme, VERSION file structure, release process
* **[Supported Hardware Platforms](/tenstorrent/tt-system-firmware/1.3-supported-hardware-platforms)**: Board variants, hardware configurations, platform-specific parameters

### Firmware Applications

* **[Firmware Applications](/tenstorrent/tt-system-firmware/2-firmware-applications)**: Overview of SMC and DMC applications
  + **[System Management Controller (SMC)](/tenstorrent/tt-system-firmware/2.1-system-management-controller-(smc))**: Message handling, telemetry, DVFS implementation
  + **[Device Management Controller (DMC)](/tenstorrent/tt-system-firmware/2.2-device-management-controller-(dmc))**: Event processing, CM2DM protocol, JTAG control

### Hardware Subsystems

* **[Hardware Subsystems](/tenstorrent/tt-system-firmware/3-hardware-subsystems)**: Tensix, GDDR, Ethernet, PCIe initialization and management
* **[Network-on-Chip (NOC)](/tenstorrent/tt-system-firmware/4-network-on-chip-(noc))**: NOC architecture, TLB system, harvesting support
* **[Binary Firmware Components](/tenstorrent/tt-system-firmware/5-binary-firmware-components)**: MRISC, ERISC, SerDes firmware details

### Development

* **[Build System](/tenstorrent/tt-system-firmware/6-build-system)**: Zephyr integration, build pipeline, firmware packaging, signing
* **[Testing and Validation](/tenstorrent/tt-system-firmware/7-testing-and-validation)**: Unit tests, hardware smoke tests, stress tests, Metal integration
* **[CI/CD Infrastructure](/tenstorrent/tt-system-firmware/8-cicd-infrastructure)**: GitHub Actions workflows, hardware test infrastructure, board configuration matrix
* **[Development Guide](/tenstorrent/tt-system-firmware/9-development-guide)**: Setup instructions, documentation system, coding guidelines

**API Reference**: Complete C API documentation generated from source code using Doxygen is available at `/tt-system-firmware/doxygen/index.html`. See [API Reference](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/API Reference) for details.

**Sources:** [doc/index.rst1-34](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/index.rst#L1-L34) [doc/api.rst1-13](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/api.rst#L1-L13) [doc/\_doxygen/main.md1-10](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/_doxygen/main.md?plain=1#L1-L10)

## Quick Links

* **GitHub Repository**: <https://github.com/tenstorrent/tt-system-firmware>
* **Getting Started**: [Getting Started Guide](/tenstorrent/tt-system-firmware/9.1-getting-started)
* **API Documentation**: [API Reference](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/API Reference)
* **Contributing**: [Contributing Guidelines](/tenstorrent/tt-system-firmware/9.3-coding-guidelines-and-standards)
* **CI Status**: Build badges and test results available in [README.md3-11](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1#L3-L11)

**Sources:** [README.md1-49](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1#L1-L49) [doc/conf.py1-77](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/conf.py#L1-L77)

Refresh this wiki