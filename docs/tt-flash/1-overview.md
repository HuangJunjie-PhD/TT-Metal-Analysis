---
project: tt-flash
github: https://github.com/tenstorrent/tt-flash
deepwiki: https://deepwiki.com/tenstorrent/tt-flash/1-overview
---

# Overview

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1)
*   [pyproject.toml](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml)
*   [tt_flash/main.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py)

**tt-flash** is a command-line utility for flashing firmware to Tenstorrent AI accelerator chips. It provides a unified interface for updating firmware on Wormhole and Blackhole hardware platforms, handling chip detection, firmware validation, SPI flash programming, and device reset procedures. The tool abstracts hardware-specific complexities through a layered architecture, enabling users to flash multiple devices with a single command while ensuring version compatibility and data integrity.

## Purpose and Scope

This document provides a high-level overview of tt-flash's architecture, components, and operational workflow. It introduces the major system layers and their interactions, serving as an entry point for understanding the codebase structure.

For specific topics, refer to:

*   Installation and basic usage patterns: see [Getting Started](https://deepwiki.com/tenstorrent/tt-flash/2-getting-started)
*   Detailed command syntax and options: see [Command Reference](https://deepwiki.com/tenstorrent/tt-flash/3-command-reference)
*   Hardware platform specifications: see [Supported Hardware](https://deepwiki.com/tenstorrent/tt-flash/4-supported-hardware)
*   Internal flashing mechanics and stages: see [Flashing Operations](https://deepwiki.com/tenstorrent/tt-flash/5-flashing-operations)
*   Hardware abstraction implementation: see [Hardware Abstraction Layer](https://deepwiki.com/tenstorrent/tt-flash/6-hardware-abstraction-layer)

**Sources:**[README.md 1-13](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L1-L13)[pyproject.toml 1-10](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L1-L10)

* * *

## System Architecture

tt-flash implements a three-layer architecture that separates user interaction, application logic, and hardware communication:

**Sources:**[tt_flash/main.py 1-243](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L1-L243)[tt_flash/flash.py 1-100](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L1-L100)[tt_flash/chip.py 1-50](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L1-L50)[pyproject.toml 23-35](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L23-L35)

* * *

## Component Layers

### User Interface Layer

The CLI entry point is `main.py::main()`[tt_flash/main.py 204-242](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L204-L242) It implements a custom argument parser (`NoExitArgumentParser`) that provides backward compatibility by defaulting to the `flash` subcommand when no subcommand is explicitly specified [tt_flash/main.py 49-58](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L49-L58) The parser supports two primary commands:

| Command | Handler | Purpose |
| --- | --- | --- |
| `flash` | `flash_chips()` | Write firmware to devices |
| `verify` | N/A (future) | Validate flash contents |

Version information is loaded from `.ignored/version.txt` or falls back to `tt_flash.__version__`[tt_flash/main.py 25-37](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L25-L37)

**Sources:**[tt_flash/main.py 60-178](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L60-L178)[tt_flash/main.py 204-242](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L204-L242)

### Core Application Layer

The core logic resides in `flash.py::flash_chips()`, which orchestrates the entire flashing workflow. This layer:

*   **Loads firmware bundles:**`main.py::load_manifest()` extracts `manifest.json` from `.fwbundle` tarfiles and validates version format [tt_flash/main.py 179-201](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L179-L201)
*   **Identifies board types:**`utility.py::get_board_type()` parses 64-bit Board IDs to extract UPI and revision codes
*   **Maps to public names:**`utility.py::change_to_public_name()` translates internal codenames (e.g., "E300_R2", "NEBULA_X1") to user-facing names (e.g., "e150", "n150")
*   **Manages version comparison:** Compares SPI flash versions against bundle versions to determine update necessity

**Sources:**[tt_flash/flash.py 1-100](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L1-L100)[tt_flash/utility.py 1-200](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L1-L200)[tt_flash/main.py 179-201](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L179-L201)

### Hardware Abstraction Layer

The abstraction layer is defined in `chip.py` with the following hierarchy:

`detect_local_chips()` enumerates PCI devices via pyluwen's detection mechanisms and wraps them in architecture-specific chip objects [tt_flash/chip.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#LNaN-LNaN) Each concrete implementation provides chip-specific features:

*   **WhChip:** Wormhole firmware version retrieval, standard reset procedures
*   **BhChip:** Blackhole-specific `boot_fs_write()` for boot filesystem updates, cryptographic post-flash validation (firmware v19+)

**Sources:**[tt_flash/chip.py 1-300](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L1-L300)

### Hardware Communication Layer

All low-level hardware I/O flows through the **pyluwen** library (~0.8.0) [pyproject.toml 26](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L26-L26) The `TTChip` base class delegates to `pyluwen.PciChip` methods:

| Operation | Method | Purpose |
| --- | --- | --- |
| SPI Read | `chip.spi_read(offset, length)` | Read from SPI flash |
| SPI Write | `chip.spi_write(offset, data)` | Write to SPI flash |
| AXI Read | `chip.axi_read(addr, length)` | Read AXI bus registers |
| AXI Write | `chip.axi_write(addr, data)` | Write AXI bus registers |
| ARC Message | `chip.arc_msg(msg_code, ...)` | Send messages to ARC processor |

**Sources:**[pyproject.toml 23-35](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L23-L35)[tt_flash/chip.py 50-150](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L50-L150)

* * *

## Operational Workflow

The flashing process follows a four-stage pipeline:

The stages are printed to stdout [tt_flash/main.py 213-224](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L213-L224):

1.   **SETUP:** Load and validate firmware bundle [tt_flash/main.py 213-219](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L213-L219)
2.   **DETECT:** Enumerate PCI devices and create chip objects [tt_flash/main.py 221-222](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L221-L222)
3.   **FLASH:** Write firmware with verification [tt_flash/main.py 224-234](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L224-L234)
4.   **RESET:** Platform-specific device reset procedures

For detailed stage implementations, see [Flashing Operations](https://deepwiki.com/tenstorrent/tt-flash/5-flashing-operations).

**Sources:**[tt_flash/main.py 204-242](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L204-L242)[tt_flash/flash.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#LNaN-LNaN)

* * *

## Supported Platforms

tt-flash currently supports two Tenstorrent architectures:

| Architecture | Implementation | Board Variants |
| --- | --- | --- |
| Wormhole | `chip.py::WhChip` | WH_UBB (Galaxy), E300 (R2/R3/105), E75 |
| Blackhole | `chip.py::BhChip` | GALAXY-1, NEBULA (X1/X2/CB), P100/P150/P300 series |

Grayskull support was removed in version 3.4.7 [README.md 152](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L152-L152) Each platform has specialized firmware retrieval methods, reset procedures, and hardware-specific features.

For comprehensive platform details, see [Supported Hardware](https://deepwiki.com/tenstorrent/tt-flash/4-supported-hardware).

**Sources:**[README.md 150-153](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L150-L153)[tt_flash/utility.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#LNaN-LNaN)[tt_flash/chip.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#LNaN-LNaN)

* * *

## Distribution and Dependencies

tt-flash is distributed through two channels:

1.   **PyPI:** Python wheels installable via `pip install tt-flash`[README.md 29-33](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L29-L33)
2.   **Debian packages:** Multi-version support for Ubuntu 22.04/24.04 [debian/changelog 1-50](https://github.com/tenstorrent/tt-flash/blob/60c06032/debian/changelog#L1-L50)

### Core Dependencies

| Dependency | Version Constraint | Purpose |
| --- | --- | --- |
| `pyluwen` | ~0.8.0 | Hardware I/O layer |
| `tt_tools_common` | ~1.5 | Shared Tenstorrent utilities |
| `pyyaml` | ==6.0.2 | Firmware metadata parsing |
| `tabulate` | ==0.9.0 | CLI table formatting |
| `tomli` | ==2.0.1 (Python <3.11) | TOML configuration parsing |

The project uses PEP 517/518 build system with `pyproject.toml` as the authoritative configuration [pyproject.toml 1-84](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L1-L84) A compatibility `setup.py` exists for Ubuntu 22.04 packaging tools.

For dependency details and integration points, see [Dependencies](https://deepwiki.com/tenstorrent/tt-flash/7-dependencies).

**Sources:**[pyproject.toml 23-35](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L23-L35)[README.md 28-63](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L28-L63)

* * *

## Version and License

Current version: **3.5.0**[pyproject.toml 3](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L3-L3)

tt-flash is licensed under Apache 2.0 [pyproject.toml 7](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L7-L7) The software is a firmware flashing utility only; firmware images are licensed and distributed separately at [https](https://github.com/tenstorrent/tt-flash/blob/60c06032/https#LNaN-LNaN)[README.md 112](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L112-L112)

For release history and changelog, see [Release History](https://deepwiki.com/tenstorrent/tt-flash/1.2-release-history).

**Sources:**[pyproject.toml 1-10](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L1-L10)[README.md 154-157](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L154-L157)[README.md 111-113](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L111-L113)

Dismiss
Refresh this wiki

Enter email to refresh