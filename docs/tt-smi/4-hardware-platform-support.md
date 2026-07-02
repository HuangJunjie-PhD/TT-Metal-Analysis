---
project: tt-smi
github: https://github.com/tenstorrent/tt-smi
deepwiki: https://deepwiki.com/tenstorrent/tt-smi/4-hardware-platform-support
---

# Hardware Platform Support

Relevant source files
*   [CHANGELOG.md](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1)
*   [README.md](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1)
*   [tt_smi/constants.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/constants.py)
*   [tt_smi/tt_smi_backend.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py)

This document describes the hardware platforms supported by the Tenstorrent System Management Interface (TT-SMI) and details the capabilities available for each platform. For information about interacting with these devices through the command-line interface, see page 2.

> **Note**: As of TT-SMI version 3.0.35, Grayskull devices are no longer supported. Users with Grayskull hardware should use TT-SMI version 3.0.34 or earlier.

## Supported Hardware Overview

TT-SMI currently supports the following Tenstorrent hardware platforms:

*   **Wormhole (WH)** - Standard PCIe boards (n-series, nb-series)
*   **Blackhole (BH)** - Standard PCIe boards (p-series)
*   **Galaxy Wormhole** - Multi-tray 6U systems (tt-galaxy-wh)
*   **Galaxy Blackhole** - Multi-tray 6U systems (tt-galaxy-bh)

### Platform Architecture Overview

Sources: [README.md 14-15](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L14-L15)[tt_smi/tt_smi_backend.py 98-105](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L98-L105)[tt_smi/tt_smi_backend.py 652-711](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L652-L711)

### Deprecated Hardware

**Grayskull (GS)** devices (e150, e300, e75) were supported through TT-SMI v3.0.34 but are no longer maintained. These first-generation devices supported Tensix-level reset functionality which has been removed from the codebase.

Sources: [README.md 14-15](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L14-L15)[CHANGELOG.md 1-7](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L1-L7)

## Hardware Detection and Identification

TT-SMI automatically detects all Tenstorrent devices present on the host system using the `detect_chips_with_callback()` function from `tt_tools_common`. Each device is identified by its PCI interface ID (for local devices) and classified by hardware type using the `get_device_name()` method.

### Device Detection Flow

### Board Type Identification

The `get_board_type()` function identifies specific board models by parsing the Board ID's Unique Part Identifier (UPI):

Sources: [tt_smi/tt_smi_backend.py 53-96](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L53-L96)[tt_smi/tt_smi_backend.py 98-105](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L98-L105)[tt_smi/tt_smi_backend.py 652-711](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L652-L711)

## Platform Capabilities Matrix

TT-SMI provides different capabilities depending on the hardware platform. The following table summarizes the feature availability across platforms:

| Capability | Wormhole | Blackhole | Galaxy WH | Galaxy BH |
| --- | --- | --- | --- | --- |
| Device Information | ✓ | ✓ | ✓ | ✓ |
| Real-time Telemetry | ✓ | ✓ | ✓ | ✓ |
| Firmware Versions | ✓ | ✓ | ✓ | ✓ |
| Board-level Reset | ✓ | ✓ | ✓* | ✓* |
| IPMI-based Reset | ✗ | ✗ | ✓ | ✓ |
| Tray-specific Reset | ✗ | ✗ | ✓ | ✓ |
| Coordinate Reporting | ✓ | ✗ | ✓ | ✗ |
| Remote Device Detection | ✓ | ✗ | ✓ | ✗ |

*Galaxy systems support both standard PCI resets (requires CPLD FW v1.16+) and IPMI-based Galaxy resets.

### Device Information Collection

The `get_device_info()` method collects the following information for each device:

| Field | Description | Source Method | Notes |
| --- | --- | --- | --- |
| `bus_id` | PCI bus address (domain:bus:device.function) | `device.get_pci_bdf()` | N/A for remote devices |
| `board_type` | Board model identifier | `get_board_type(board_id)` | From UPI in board ID |
| `board_id` | 16-digit hex board identifier | `get_board_id()` | Read from BOARD_ID or BOARD_ID_HIGH/LOW |
| `coords` | Physical coordinates (shelf_x, shelf_y, rack_x, rack_y) | `device.as_wh().get_local_coord()` | Wormhole only |
| `dram_status` | Memory training pass/fail | `get_dram_training_status()` | Platform-specific validation |
| `dram_speed` | Memory frequency | `get_dram_speed()` | Format: "16G", "14G", etc. |
| `pcie_speed` | Current PCIe generation | `get_pci_properties()` | Gen 1-5 from sysfs |
| `pcie_width` | Current PCIe lane count | `get_pci_properties()` | From sysfs |

Sources: [tt_smi/tt_smi_backend.py 384-415](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L384-L415)[tt_smi/tt_smi_backend.py 310-347](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L310-L347)

### Galaxy System Architecture

Galaxy 6U systems have a unique multi-tray architecture with 32 devices distributed across 4 trays. Each tray is identified by a UBB (Universal Baseboard) bus ID:

| Platform | Tray 1 | Tray 2 | Tray 3 | Tray 4 |
| --- | --- | --- | --- | --- |
| Galaxy Wormhole | 0xC0 | 0x80 | 0x00 | 0x40 |
| Galaxy Blackhole | 0x00 | 0x40 | 0xC0 | 0x80 |

The `print_tray_and_device_mapping()` method provides a mapping between tray numbers and device indices based on these bus IDs.

Sources: [tt_smi/constants.py 154-155](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/constants.py#L154-L155)[tt_smi/tt_smi_backend.py 203-239](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L203-L239)

## Telemetry Collection

TT-SMI provides real-time monitoring of telemetry data through platform-specific collection methods. The telemetry data is read from hardware registers via the SMBus interface and presented in a unified format.

### Telemetry Data Flow

### Telemetry Metrics

All platforms provide the following telemetry metrics:

| Metric | Description | Unit | Wormhole Source | Blackhole Source |
| --- | --- | --- | --- | --- |
| `voltage` | Core voltage | V | VCORE / 1000 | VCORE / 1000 |
| `current` | Core current | A | TDC & 0xFFFF | TDC & 0xFFFF |
| `power` | Core power | W | TDP & 0xFFFF | TDP & 0xFFFF |
| `aiclk` | AI clock frequency | MHz | AICLK & 0xFFFF | AICLK & 0xFFFF |
| `asic_temperature` | ASIC die temperature | °C | (ASIC_TEMPERATURE & 0xFFFF) / 16 | convert_signed_16_16_to_float() |
| `fan_speed` | Fan speed percentage | % | FAN_SPEED | FAN_RPM / 50 (max 5000 RPM) |
| `heartbeat` | Firmware watchdog counter | count | ARC3_HEALTH / 5 (~2 Hz) | TIMER_HEARTBEAT / 6 (~2 Hz) |

### Platform-Specific Telemetry Lists

**Wormhole**: Uses `SMBUS_TELEMETRY_LIST` with 54 metrics including:

*   Board and device identification
*   Firmware versions (ARC0-3, ETH, M3 BL/APP, TT_FLASH, FW_BUNDLE)
*   DDR status and speed
*   Ethernet and PCIe status
*   Power and thermal data
*   Clock frequencies
*   Health and debug information

**Blackhole**: Uses `BH_TELEMETRY_LIST` with 33 TAG-prefixed metrics including:

*   TAG_BOARD_ID_HIGH/LOW
*   TAG_VCORE, TAG_TDP, TAG_TDC
*   TAG_ASIC_TEMPERATURE, TAG_VREG_TEMPERATURE, TAG_BOARD_TEMPERATURE
*   TAG_AICLK, TAG_AXICLK, TAG_ARCCLK
*   TAG_L2CPUCLK0-3 (4 L2 CPU cores)
*   TAG_FAN_RPM, TAG_TIMER_HEARTBEAT

Sources: [tt_smi/tt_smi_backend.py 241-262](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L241-L262)[tt_smi/tt_smi_backend.py 423-513](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L423-L513)[tt_smi/constants.py 8-100](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/constants.py#L8-L100)

## Firmware Version Reporting

TT-SMI reports firmware versions for various subsystems on the hardware. The `get_firmware_versions()` method reads raw hexadecimal values from telemetry registers and converts them to semantic version strings using utility functions from `tt_tools_common`.

### Firmware Components

| Component | Description | Wormhole | Blackhole | Conversion Function |
| --- | --- | --- | --- | --- |
| `fw_bundle_version` | Firmware bundle package version | ✓ | ✓ | `hex_to_semver_m3_fw()` |
| `tt_flash_version` | TT Flash utility version | ✓ | ✓ | `hex_to_semver_m3_fw()` |
| `cm_fw` | Core Manager (ARC0) firmware | ✓ | ✓ | `hex_to_semver_m3_fw()` |
| `cm_fw_date` | CM firmware build date | ✓ | ✓ | `hex_to_date()` |
| `eth_fw` | Ethernet firmware | ✓ | ✓ | `hex_to_semver_eth()` |
| `bm_bl_fw` | Board Manager bootloader | ✓ | ✓ | `hex_to_semver_m3_fw()` |
| `bm_app_fw` | Board Manager application | ✓ | ✓ | `hex_to_semver_m3_fw()` |

### Version Collection Process

### Galaxy System Firmware Quirks

Galaxy systems (board type `wh_4u` / `tt-galaxy-wh`) have a known firmware flashing issue where the `FW_BUNDLE_VERSION` field may contain `0xffffffff`. In this case, TT-SMI retrieves the bundle version from the `TT_FLASH_VERSION` field instead:

Additionally, Galaxy systems report `N/A` for `tt_flash_version` since this field is repurposed for the bundle version.

Sources: [tt_smi/tt_smi_backend.py 588-649](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L588-L649)[tt_smi/tt_smi_backend.py 629-648](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L629-L648)

## Reset Operations by Platform

TT-SMI supports different reset mechanisms depending on the hardware platform. The reset functionality is implemented in the `pci_board_reset()` and `glx_6u_trays_reset()` functions, which dispatch to platform-specific reset handlers from `tt_tools_common`.

### Standard Platform Resets

**Wormhole**: Board-level reset using `WHChipReset.full_lds_reset()`

*   Power cycle the board
*   Optionally perform secondary bus reset (disabled on Galaxy)
*   Retrain PCIe link
*   Retrain Ethernet connections

**Blackhole**: ASIC-level reset using `BHChipReset.full_lds_reset()`

*   Reset the ASIC
*   Optionally perform secondary bus reset (disabled on Galaxy)
*   Retrain PCIe link

### Galaxy System Resets

Galaxy 6U systems use IPMI-based resets via the `glx_6u_trays_reset()` function:

### Reset Parameters

The IPMI reset command accepts the following parameters:

| Parameter | Description | Values |
| --- | --- | --- |
| `ubb_num` | Tray bitmap to reset | `0x0-0xF` (bit 0=Tray 1, bit 1=Tray 2, etc.) |
| `dev_num` | Device bitmap per tray | `0x0-0xFF` (8 devices per tray) |
| `op_mode` | Operation mode | `0x0`=assert/deassert, `0x1`=assert only, `0x2`=deassert only |
| `reset_time` | Reset duration | `0xF` = 150ms (10ms resolution) |

### Post-Reset Validation

For full Galaxy WH resets, TT-SMI validates Ethernet link status by checking for `LINK_INACTIVE_FAIL_DUMMY_PACKET` (value 10) errors in the debug buffer at address `0x12c0` for all 16 Ethernet ports on all 32 devices.

Sources: [tt_smi/tt_smi_backend.py 733-807](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L733-L807)[tt_smi/tt_smi_backend.py 860-928](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L860-L928)[tt_smi/tt_smi_backend.py 819-858](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L819-L858)

## Multi-Board Systems

TT-SMI supports multi-board configurations, providing capabilities to manage and inspect both local and remote devices in Wormhole clusters:

For multi-board Galaxy systems, TT-SMI uses `GalaxyReset` to coordinate resets across interconnected devices.

Sources: [tt_smi/tt_smi_backend.py 170-173](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L170-L173)[tt_smi/tt_smi_backend.py 616-644](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L616-L644)

## Hardware Support Changelog

TT-SMI's hardware support has evolved over time:

| Version | Key Hardware Support Features Added |
| --- | --- |
| 3.0.0 | Added Blackhole (BH) support for telemetry reporting and reset |
| 2.0.0 | Added Wormhole (WH) support, coordinates reporting, and board level reset |
| 1.0.0 | Initial release with Grayskull (GS) support and Tensix reset |

Sources: [CHANGELOG.md 81-87](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L81-L87)[CHANGELOG.md 135-143](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L135-L143)[CHANGELOG.md 155-161](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L155-L161)

Dismiss
Refresh this wiki

Enter email to refresh