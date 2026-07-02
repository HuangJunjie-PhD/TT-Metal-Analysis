---
project: tt-smi
github: https://github.com/tenstorrent/tt-smi
deepwiki: https://deepwiki.com/tenstorrent/tt-smi/5-reset-operations
---

# Reset Operations

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1)
*   [tt_smi/tt_smi.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py)
*   [tt_smi/tt_smi_backend.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py)

## Purpose and Scope

This page provides comprehensive documentation on device reset functionality in TT-SMI. TT-SMI supports two primary reset mechanisms: standard PCI board resets for individual devices, and Galaxy-specific IPMI-based resets for Galaxy 6U systems. Reset operations are critical for recovering devices from error states and ensuring system reliability.

For detailed information on specific reset types, see:

*   [Reset Architecture and Types](https://deepwiki.com/tenstorrent/tt-smi/5.1-reset-architecture-and-types) - Platform-specific reset mechanisms
*   [Standard PCI Device Resets](https://deepwiki.com/tenstorrent/tt-smi/5.2-standard-pci-device-resets) - Individual board reset operations
*   [Galaxy System Resets](https://deepwiki.com/tenstorrent/tt-smi/5.3-galaxy-system-resets) - Galaxy 6U tray and full system resets
*   [Reset Testing Framework](https://deepwiki.com/tenstorrent/tt-smi/5.4-reset-testing-framework) - Automated reset validation

Sources: [README.md 145-178](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L145-L178)[tt_smi/tt_smi.py 11](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L11-L11)[tt_smi/tt_smi_backend.py 733-928](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L733-L928)

## Overview of Reset Capabilities

TT-SMI provides two distinct reset mechanisms designed for different system architectures:

### Reset Mechanism Summary

| Reset Type | Platforms | CLI Command | Implementation | Scope |
| --- | --- | --- | --- | --- |
| **PCI Board Reset** | Wormhole, Blackhole | `tt-smi -r [indices]` | `pci_board_reset()` | Individual boards or all PCI devices |
| **Galaxy System Reset** | Galaxy 6U (WH/BH) | `tt-smi -glx_reset` | `glx_6u_trays_reset()` | Full system (32 devices) or individual trays |

### Reset Implementation Architecture

**Diagram: Reset Entry Points and Dispatch Logic**

Sources: [tt_smi/tt_smi.py 789-871](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L789-L871)[tt_smi/tt_smi_backend.py 733-807](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L733-L807)[tt_smi/tt_smi_backend.py 860-928](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L860-L928)

### Platform-Specific Reset Methods

**Diagram: Platform-Specific Reset Handlers**

Sources: [tt_smi/tt_smi_backend.py 733-787](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L733-L787)[tt_smi/tt_smi_backend.py 860-928](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L860-L928)[tt_smi/tt_smi_backend.py 819-858](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L819-L858)

## Command Line Interface

### Reset Command Reference

TT-SMI provides multiple CLI options for different reset scenarios:

| Command | Description | Default Behavior |
| --- | --- | --- |
| `tt-smi -r` | Reset all PCI devices | Resets all detected devices with re-init |
| `tt-smi -r 0,1` | Reset specific PCI indices | Resets devices at indices 0 and 1 |
| `tt-smi -glx_reset` | Full Galaxy 6U reset | Resets all 4 trays (32 devices) |
| `tt-smi -glx_reset_auto` | Galaxy reset with retry | Auto-retries up to 3 times on failure |
| `tt-smi -glx_reset_tray N` | Reset single Galaxy tray | Resets tray N (1-4) |
| `tt-smi --no_reinit` | Skip device re-detection | Works with any reset command |
| `tt-smi -ls` | List resettable devices | Shows PCI indices and board types |
| `tt-smi -glx_list_tray_to_device` | Show tray-to-device mapping | Galaxy systems only |

Sources: [tt_smi/tt_smi.py 625-680](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L625-L680)[README.md 115-126](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L115-L126)

### Reset Command Flow

**Diagram: CLI Argument Processing and Reset Dispatch**

Sources: [tt_smi/tt_smi.py 789-871](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L789-L871)[tt_smi/tt_smi_backend.py 733-807](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L733-L807)[tt_smi/tt_smi_backend.py 860-928](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L860-L928)

## Reset Types Overview

TT-SMI implements two primary reset mechanisms with platform-specific handlers:

### Standard PCI Device Resets

**Function:**`pci_board_reset()`[tt_smi/tt_smi_backend.py 733-807](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L733-L807)

Standard PCI resets target individual boards via their PCI interface. The function:

1.   Identifies chip types (Wormhole or Blackhole) from PCI indices
2.   Groups devices by type into separate lists
3.   Dispatches to platform-specific reset handlers: 
    *   `WHChipReset.full_lds_reset()` for Wormhole boards
    *   `BHChipReset.full_lds_reset()` for Blackhole boards

4.   Optionally re-initializes devices via `detect_chips_with_callback()`

**Reset Characteristics:**

*   **Wormhole:** Board-level reset with power cycle and Ethernet retraining
*   **Blackhole:** ASIC-level reset
*   **Galaxy 6U Detection:** Disables secondary bus reset if Galaxy board types detected (0x35, 0x47)

See [Standard PCI Device Resets](https://deepwiki.com/tenstorrent/tt-smi/5.2-standard-pci-device-resets) for detailed implementation.

Sources: [tt_smi/tt_smi_backend.py 733-807](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L733-L807)[README.md 156-163](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L156-L163)

### Galaxy System Resets

**Function:**`glx_6u_trays_reset()`[tt_smi/tt_smi_backend.py 860-928](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L860-L928)

Galaxy resets use IPMI commands to reset Galaxy 6U trays (4 trays, 32 devices). The function:

1.   Executes `run_wh_ubb_ipmi_reset()` with configurable parameters
2.   Waits 30 seconds for hardware reset completion
3.   Calls `run_ubb_wait_for_driver_load()` to wait for driver
4.   Performs `check_wh_galaxy_eth_link_status()` to validate Ethernet links
5.   Re-initializes all chips via `detect_chips_with_callback()`

**IPMI Parameters:**

*   `ubb_num`: Tray bitmask (0xF = all 4 trays)
*   `dev_num`: Device bitmask (0xFF = all devices)
*   `op_mode`: Operation mode (0x0 = assert/deassert with period)
*   `reset_time`: Reset duration in 10ms units (0xF = 150ms)

See [Galaxy System Resets](https://deepwiki.com/tenstorrent/tt-smi/5.3-galaxy-system-resets) for detailed implementation.

Sources: [tt_smi/tt_smi_backend.py 860-928](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L860-L928)[README.md 206-232](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L206-L232)

## Common Reset Options

### Device Re-initialization

By default, reset operations re-initialize devices to verify successful reset. This is controlled by the `--no_reinit` flag:

**Re-initialization Process:**

1.   Calls `detect_chips_with_callback()` to scan for devices
2.   Reports number of successfully detected devices
3.   Exits with error if device count doesn't match expectations

**Function Signature:**

Sources: [tt_smi/tt_smi.py 676-680](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L676-L680)[tt_smi/tt_smi.py 796-806](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L796-L806)[tt_smi/tt_smi_backend.py 789-806](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L789-L806)

### Identifying Devices for Reset

Use `tt-smi -ls` to list all devices with their PCI indices:

Output includes:

*   **All available boards:** All detected devices (including remote N300 ASICs)
*   **Boards that can be reset:** Only locally-connected devices that support reset

For Galaxy systems, use `tt-smi -glx_list_tray_to_device` to view tray-to-device mapping:

Shows which PCI device IDs correspond to which Galaxy trays (1-4).

Sources: [tt_smi/tt_smi.py 700-712](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L700-L712)[tt_smi/tt_smi_backend.py 154-239](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L154-L239)[README.md 184-248](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L184-L248)

## Usage Examples

### Resetting by PCI Indices

Resets the devices at PCI indices 0 and 1. TT-SMI will automatically determine the device types and perform the appropriate reset operations.

### Resetting All Devices

With no specific indices, TT-SMI resets all PCI devices found on the system.

### Resetting with Configuration File

Resets devices according to the specifications in the JSON configuration file.

### Finding Available Devices for Reset

Lists all Tenstorrent devices on the system, including their PCI indices, which can be used for targeted reset operations.

Sources: [README.md 146-181](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L146-L181)[tt_smi/tt_smi.py 676-691](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L676-L691)

## Security and Special Considerations

### ARM Systems Warning

PCIe configuration is set up differently on ARM systems, which may cause current reset implementations to not work as expected. For ARM systems, a full system reboot is recommended instead of using TT-SMI reset functionality.

### Reset Configuration Options

Users can disable software version reporting and serial number reporting in the reset configuration file:

When `disable_sw_version_report` is set to true, all software versions in the "Latest SW Versions" block are reported as "N/A".

Sources: [README.md 123-125](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L123-L125)[README.md 208-215](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L208-L215)

## Implementation Architecture

The following diagram shows the core components involved in the reset implementation:

Sources: [tt_smi/tt_smi.py 800-881](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L800-L881)[tt_smi/tt_smi.py 693-702](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L693-L702)[tt_smi/tt_smi_backend.py 603-709](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L603-L709)[tt_smi/tt_smi_backend.py 721-791](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L721-L791)

Dismiss
Refresh this wiki

Enter email to refresh