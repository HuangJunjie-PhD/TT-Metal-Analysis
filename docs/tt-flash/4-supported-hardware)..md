---
project: tt-flash
github: https://github.com/tenstorrent/tt-flash
deepwiki: https://deepwiki.com/tenstorrent/tt-flash/4-supported-hardware).
---

# Supported Hardware

Relevant source files
*   [CHANGELOG.md](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1)
*   [tt_flash/chip.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py)
*   [tt_flash/utility.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py)

This document provides an overview of the hardware platforms supported by tt-flash, including Wormhole and Blackhole chip architectures and their board variants. It describes the chip abstraction layer used to interface with different hardware types and the board identification system.

For detailed information on specific platforms, see [Wormhole Platform](https://deepwiki.com/tenstorrent/tt-flash/4.3-wormhole-platform) and [Blackhole Platform](https://deepwiki.com/tenstorrent/tt-flash/4.4-blackhole-platform). For the board identification mechanism, see [Board Identification](https://deepwiki.com/tenstorrent/tt-flash/4.2-board-identification). For version compatibility details, see [Version Checking and Compatibility](https://deepwiki.com/tenstorrent/tt-flash/5.3-version-checking-and-compatibility).

* * *

## Overview

tt-flash supports two current-generation Tenstorrent AI accelerator architectures:

*   **Wormhole (WH)**: Implemented via the `WhChip` class, supporting Galaxy systems and E-series boards
*   **Blackhole (BH)**: Implemented via the `BhChip` class, supporting GALAXY, NEBULA, and P-series boards

Grayskull (GS) support was removed in version 3.4.7. The architecture uses an abstract base class pattern where `TTChip` defines the common interface and chip-specific subclasses implement platform-specific behaviors such as firmware version retrieval, reset procedures, and hardware features.

**Sources**: [CHANGELOG.md 1-96](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1#L1-L96)[tt_flash/chip.py 113-240](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L113-L240)

* * *

## Chip Architecture Classes

The hardware abstraction layer uses an inheritance hierarchy to manage different chip architectures:

### Class Hierarchy

**Sources**: [tt_flash/chip.py 113-306](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L113-L306)

### TTChip Abstract Base Class

The `TTChip` class [tt_flash/chip.py 113-240](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L113-L240) provides the common interface for all chip types:

*   **Hardware Communication**: Wraps `pyluwen.PciChip` for low-level operations
*   **AXI Interface**: `axi_write32()`, `axi_read32()`, `axi_write()`, `axi_read()` for bus access
*   **SPI Interface**: `spi_write()`, `spi_read()` for flash operations
*   **ARC Messaging**: `arc_msg()` for processor communication
*   **Telemetry**: `get_telemetry()`, `get_telemetry_unchanged()` for chip status
*   **Reinitialization**: `reinit()` to re-enumerate chip after reset

Abstract methods that must be implemented by subclasses:

*   `min_fw_version()`: Minimum firmware version required for this chip type
*   `get_bundle_version()`: Retrieve running and SPI firmware bundle versions

**Sources**: [tt_flash/chip.py 113-240](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L113-L240)

### WhChip Implementation

The `WhChip` class [tt_flash/chip.py 297-306](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L297-L306) implements Wormhole-specific behavior:

*   **Minimum FW Version**: Returns `0x2170000` (version 2.23.0.0)
*   **Version Retrieval**: Uses `get_bundle_version_v1()`[tt_flash/chip.py 29-92](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L29-L92) which queries firmware via ARC messages with `MSG_TYPE_FW_VERSION`
*   **Representation**: String format `"Wormhole[{interface_id}]"`

**Sources**: [tt_flash/chip.py 297-306](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L297-L306)[tt_flash/chip.py 29-92](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L29-L92)

### BhChip Implementation

The `BhChip` class [tt_flash/chip.py 241-295](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L241-L295) implements Blackhole-specific behavior:

*   **Minimum FW Version**: Returns `0x0` (no minimum requirement)
*   **Version Retrieval**: Reads from telemetry (`fw_bundle_version`) and boot filesystem (`decode_boot_fs_table("cmfwcfg")`)
*   **ASIC Location**: `get_asic_location()` returns 0 (left) or 1 (right) for P300 dual-ASIC boards, reading from telemetry or GPIO strap register `0x80030D20` as fallback
*   **Representation**: String format `"Blackhole[{interface_id}]"`

**Sources**: [tt_flash/chip.py 241-295](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L241-L295)

* * *

## Board Type Detection

Board identification uses a 64-bit Board ID value read from `chip.board_type()`. The detection system is implemented in [tt_flash/utility.py 39-131](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L39-L131):

**Key Functions**:

*   `get_board_type(board_id, from_type)`[tt_flash/utility.py 39-106](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L39-L106): Maps UPI value to internal codename
*   `change_to_public_name(codename)`[tt_flash/utility.py 108-131](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L108-L131): Converts codename to user-facing name

For detailed board identification logic, see [Board Identification](https://deepwiki.com/tenstorrent/tt-flash/4.2-board-identification).

**Sources**: [tt_flash/utility.py 39-131](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L39-L131)[tt_flash/chip.py 202-204](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L202-L204)

* * *

## Platform Support Matrix

### Wormhole Platforms

| Board Codename | UPI Value | Revision | Public Name | Support Status |
| --- | --- | --- | --- | --- |
| WH_UBB | 0x35 | N/A | Galaxy Wormhole | Supported |
| E300_R2 | 0x1 | 0x2 | (E300_R2) | Supported |
| E300_R3 | 0x1 | 0x3, 0x4 | (E300_R3) | Supported |
| E300_105 | 0x3 | N/A | e150 | Supported |
| E300_X2 | 0xA | N/A | e300 | Supported |
| E75 | 0x7 | N/A | e75 | Supported |

**Sources**: [tt_flash/utility.py 60-73](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L60-L73)[tt_flash/utility.py 109-115](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L109-L115)

### Blackhole Platforms

| Board Codename | UPI Value | Public Name | Added Version | Notes |
| --- | --- | --- | --- | --- |
| GALAXY-1 | 0x47 | Galaxy Blackhole | (base) |  |
| GALAXY | 0xB | (GALAXY) | (base) |  |
| NEBULA_CB | 0x8 | (NEBULA_CB) | (base) |  |
| NEBULA_X1 | 0x18 | n150 | (base) |  |
| NEBULA_X2 | 0x14 | n300 | (base) |  |
| P100-1 | 0x36 | p100 | v3.0.0 |  |
| P100A-1 | 0x43 | (P100A-1) | v3.1.2 |  |
| P150A-1 | 0x40 | p150a | v3.1.2 |  |
| P150B-1 | 0x41 | p150b | v3.1.2 |  |
| P150C-1 | 0x42 | p150c | v3.1.2 |  |
| P300A-1 | 0x45 | p300 | (base) | P-series variants |
| P300B-1 | 0x44 | p300 | (base) | P-series variants |
| P300C-1 | 0x46 | p300 | (base) | P-series variants |

**Sources**: [tt_flash/utility.py 74-105](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L74-L105)[tt_flash/utility.py 116-124](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L116-L124)[CHANGELOG.md 1-96](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1#L1-L96)

### Legacy Platforms

| Architecture | Status | Removal Version | Notes |
| --- | --- | --- | --- |
| Grayskull | Removed | v3.4.7 | No longer supported |

**Sources**: [CHANGELOG.md 1-96](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1#L1-L96)

* * *

## Chip Detection

The detection system identifies chips on the local PCI bus and wraps them in architecture-specific objects:

### Detection Functions

**Functions**:

*   `detect_local_chips(ignore_ethernet)`[tt_flash/chip.py 308-374](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L308-L374): Enumerates local chips with detailed progress callbacks, validates communication, and raises exceptions on failure
*   `detect_chips(local_only)`[tt_flash/chip.py 377-387](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L377-L387): Simple enumeration without callbacks, optionally includes remote chips

Both functions use `pyluwen` to enumerate PCI devices, then wrap them in `WhChip` or `BhChip` objects based on the device type returned by `device.as_wh()` or `device.as_bh()`.

**Sources**: [tt_flash/chip.py 308-387](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L308-L387)

* * *

## Hardware Communication Interfaces

All hardware communication flows through the `TTChip` interface methods, which delegate to the underlying `pyluwen.PciChip`:

| Interface | Methods | Purpose |
| --- | --- | --- |
| **AXI Bus** | `axi_write32()`, `axi_read32()`, `axi_write()`, `axi_read()` | Direct memory-mapped I/O |
| **SPI Flash** | `spi_write()`, `spi_read()` | Read/write SPI flash contents |
| **ARC Messages** | `arc_msg()` | Send messages to ARC processor |
| **Telemetry** | `get_telemetry()`, `get_telemetry_unchanged()` | Read chip status and firmware versions |

The `fw_defines` dictionary [tt_flash/chip.py 118](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L118-L118) is loaded from `data/{arch}/fw_defines.yaml` and contains message type constants used for ARC communication.

**Sources**: [tt_flash/chip.py 205-230](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L205-L230)[tt_flash/chip.py 94-111](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L94-L111)

* * *

## Version History

Key platform support milestones:

| Version | Date | Changes |
| --- | --- | --- |
| 3.4.0 | 2025-07-30 | No longer require `--force` for BH flashing |
| 3.2.0 | 2025-03-12 | Luwen version bump for stability with tt-smi |
| 3.1.2 | 2025-02-28 | Added P100A, P150A/C support |
| 3.1.0 | 2024-10-29 | Added BH boot-fs format support |
| 3.0.0 | 2024-08-23 | Added P100 support |
| 2.0.0 | (historical) | Wormhole flash release |
| 1.0.0 | (historical) | Grayskull flash release (now removed) |

**Sources**: [CHANGELOG.md 1-96](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1#L1-L96)

Dismiss
Refresh this wiki

Enter email to refresh