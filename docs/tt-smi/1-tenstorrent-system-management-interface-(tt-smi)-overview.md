---
project: tt-smi
github: https://github.com/tenstorrent/tt-smi
deepwiki: https://deepwiki.com/tenstorrent/tt-smi/1-tenstorrent-system-management-interface-(tt-smi)-overview
---

# Tenstorrent System Management Interface (TT-SMI) Overview

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1)
*   [pyproject.toml](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml)
*   [tests/test_reset.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tests/test_reset.py)
*   [tt_smi/tt_smi.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py)

## Purpose and Scope

This document provides a comprehensive overview of the Tenstorrent System Management Interface (TT-SMI), a command-line utility for monitoring and managing Tenstorrent hardware accelerators. It covers the system architecture, core components, supported platforms, and key features at a high level.

For specific topics, refer to:

*   Installation procedures: [Installation and Setup](https://deepwiki.com/tenstorrent/tt-smi/1.1-installation-and-setup)
*   Command-line interface details: [Command-Line Interface](https://deepwiki.com/tenstorrent/tt-smi/2-command-line-interface)
*   Interactive TUI documentation: [Interactive Text User Interface (TUI)](https://deepwiki.com/tenstorrent/tt-smi/3-interactive-text-user-interface-(tui))
*   Reset operations: [Reset Operations](https://deepwiki.com/tenstorrent/tt-smi/5-reset-operations)
*   Development environment setup: [Development Guide](https://deepwiki.com/tenstorrent/tt-smi/8-development-guide)

## What is TT-SMI?

TT-SMI is a Python-based system management interface that provides real-time monitoring and control capabilities for Tenstorrent AI accelerators. It serves three primary functions:

1.   **Hardware Monitoring**: Display device information, telemetry (voltage, current, power, temperature), and firmware versions
2.   **Device Management**: Perform board-level, ASIC-level, and system-level resets
3.   **Data Export**: Generate JSON snapshots of system state for logging and analysis

The tool operates in multiple modes:

*   **Interactive TUI**: Full-featured textual user interface with real-time telemetry updates (default mode)
*   **CLI Mode**: Command-line operations for scripting and automation
*   **Snapshot Mode**: JSON output for integration with monitoring systems

**Sources:**[tt_smi/tt_smi.py 1-12](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L1-L12)[README.md 1-10](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L1-L10)[pyproject.toml 1-5](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L1-L5)

## System Requirements

| Component | Requirement |
| --- | --- |
| Python Version | ≥ 3.10 |
| Driver Version | ≥ 2.0.0 (tt-kmd) |
| Rust | Required for building dependencies |
| OS Support | Linux (Ubuntu/Debian, Fedora/EL9) |
| Architecture | x86_64 (ARM systems have limited reset support) |

**Deprecated Support:**

*   Grayskull devices are no longer supported as of version 3.0.35

**Sources:**[pyproject.toml 6](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L6-L6)[README.md 12-18](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L12-L18)

## High-Level System Architecture

### Component Overview

**Sources:**[tt_smi/tt_smi.py 1-898](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L1-L898)[pyproject.toml 26-36](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L26-L36)

### Data Flow Architecture

**Sources:**[tt_smi/tt_smi.py 67-575](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L67-L575)[tt_smi/tt_smi.py 114-147](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L114-L147)

## Core Components

### Main Entry Point: `main()`

The `main()` function at [tt_smi/tt_smi.py 768-897](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L768-L897) serves as the primary entry point:

1.   **Driver Validation**: Checks for Tenstorrent driver presence (tt-kmd ≥ 2.0.0)
2.   **Argument Parsing**: Processes CLI arguments via `parse_args()` at [tt_smi/tt_smi.py 576-682](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L576-L682)
3.   **Reset Handling**: Executes reset operations before backend initialization if requested
4.   **Device Detection**: Calls `detect_chips_with_callback()` from `tt_tools_common`
5.   **Backend Instantiation**: Creates `TTSMIBackend` instance with detected devices
6.   **Frontend Dispatch**: Invokes appropriate frontend based on mode (GUI/CLI/snapshot)

**Sources:**[tt_smi/tt_smi.py 768-897](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L768-L897)

### Application Class: `TTSMI`

The `TTSMI` class at [tt_smi/tt_smi.py 67-575](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L67-L575) is a Textual-based application:

**Key Bindings** defined at [tt_smi/tt_smi.py 70-78](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L70-L78):

| Key | Action | Description |
| --- | --- | --- |
| `q, Q` | `action_quit()` | Exit application |
| `h, H` | `action_help()` | Show help menu |
| `d, D` | `app.toggle_dark` | Toggle dark mode |
| `c, C` | `action_toggle_compact()` | Toggle sidebar visibility |
| `1` | `action_tab_one()` | Switch to Device Info tab |
| `2` | `action_tab_two()` | Switch to Telemetry tab |
| `3` | `action_tab_three()` | Switch to Firmware tab |

**Sources:**[tt_smi/tt_smi.py 67-575](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L67-L575)

### Backend System: `TTSMIBackend`

The `TTSMIBackend` class manages device state and data collection. It maintains separate dictionaries for different data types:

| Data Store | Type | Purpose |
| --- | --- | --- |
| `device_infos` | `Dict[int, Dict]` | PCI info, board type, DRAM status |
| `device_telemetrys` | `Dict[int, Dict]` | Voltage, current, power, temperature |
| `firmware_infos` | `Dict[int, Dict]` | ARC, ETH, M3, Flash versions |
| `chip_limits` | `Dict[int, Dict]` | VDD max, TDP, TDC, thermal limits |
| `pci_properties` | `Dict[int, Dict]` | PCIe speed and width |

**Sources:**[tt_smi/tt_smi_backend.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py)

### Configuration System: `constants.py`

The constants module defines:

*   **Table Headers**: `INFO_TABLE_HEADER`, `TELEMETRY_TABLE_HEADER`, `FIRMWARES_TABLE_HEADER`
*   **Telemetry Lists**: `SMBUS_TELEMETRY_LIST` (54 metrics for Wormhole), `BH_TELEMETRY_LIST` (33 TAG metrics)
*   **Device Info Lists**: `DEV_INFO_LIST`, `FW_LIST`
*   **Galaxy Constants**: `WH_UBB_BUS_IDS`, `BH_UBB_BUS_IDS` (tray-to-bus mappings)
*   **Limits**: Default VDD, TDP, TDC, thermal limits per platform
*   **GUI Settings**: `GUI_INTERVAL_TIME = 0.1` (telemetry update interval)
*   **Help Menu**: `HELP_MENU_MARKDOWN` (keyboard shortcuts)

**Sources:**[tt_smi/constants.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/constants.py)

## Supported Hardware Platforms

TT-SMI supports multiple Tenstorrent hardware generations with platform-specific capabilities:

| Platform | Board Types | Reset Type | Telemetry System | Status |
| --- | --- | --- | --- | --- |
| **Wormhole** | n150, n300, n300 L/R | Board-level (power cycle + ETH retrain) | SMBus (54 metrics) | Active |
| **Blackhole** | p100, p150, p300 | ASIC-level | TAG-based (33 metrics) | Active |
| **Galaxy WH** | tt-galaxy-wh | IPMI-based (tray-level) | SMBus | Active |
| **Galaxy BH** | tt-galaxy-bh | IPMI-based (tray-level) | TAG-based | Active |
| **Grayskull** | e75, e150, e300 | Tensix-level | Legacy | **Deprecated v3.0.35** |

### Galaxy System Architecture

Galaxy 6U systems have unique tray-based architecture:

**Sources:**[tt_smi/constants.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/constants.py)[README.md 205-248](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L205-L248)

## Key Feature Categories

### 1. Device Monitoring

*   **Real-time Telemetry**: Updates every 100ms in GUI mode (configurable via `GUI_INTERVAL_TIME`)
*   **Device Information**: PCI bus ID, board type, coordinates, DRAM status, PCIe properties
*   **Firmware Versions**: ARC, Ethernet, M3 (bootloader + app), TT Flash
*   **Limit Monitoring**: Compares current metrics against VDD max, TDP, TDC, thermal limits
*   **Remote Device Detection**: Identifies and marks remote devices on multi-chip boards (n300)

**Sources:**[tt_smi/tt_smi.py 166-302](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L166-L302)[tt_smi/tt_smi.py 318-507](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L318-L507)

### 2. Reset Operations

Three reset strategies depending on hardware:

1.   **Standard PCI Reset** (`pci_board_reset()`): For individual Wormhole/Blackhole devices
2.   **Galaxy IPMI Reset** (`glx_6u_trays_reset()`): For Galaxy 6U tray systems
3.   **Auto-retry Reset**: Galaxy-specific with up to 3 retry attempts

**CLI Flags**:

*   `-r [indices]` / `--reset`: Reset specified PCI devices or all if none specified
*   `-glx_reset`: Full Galaxy system reset
*   `-glx_reset_auto`: Galaxy reset with automatic retry on failure
*   `-glx_reset_tray N`: Reset specific Galaxy tray (1-4)
*   `--no_reinit`: Skip device re-detection after reset

**Sources:**[tt_smi/tt_smi.py 625-680](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L625-L680)[README.md 145-248](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L145-L248)

### 3. Data Export and Logging

*   **Snapshot Mode** (`-s`): JSON output to stdout for piping to other tools
*   **File Export** (`-f [filename]`): Save snapshot to file (default: `~/tt_smi/<timestamp>_snapshot.json`)
*   **Elasticsearch Integration**: Optional telemetry logging to Elasticsearch (requires elasticsearch ≥ 8.11.0)
*   **Pydantic Models**: Type-safe log structures defined in `log.py`

**Sources:**[tt_smi/tt_smi.py 594-615](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L594-L615)[pyproject.toml 28](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L28-L28)[README.md 250-274](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L250-L274)

## Dependency Architecture

TT-SMI relies on two critical Tenstorrent libraries:

### Critical Dependencies

| Dependency | Version | Purpose |
| --- | --- | --- |
| `pyluwen` | ~0.8.0 | Low-level hardware interface: `pci_scan()`, `get_telemetry()`, chip detection |
| `tt_tools_common` | ~1.6.0 | Shared utilities: `detect_chips_with_callback()`, `WHChipReset`, `BHChipReset`, Galaxy reset functions |
| `textual` | ≥0.59.0 | TUI framework: `App`, `DataTable`, `TabbedContent`, widget system |
| `rich` | ≥13.7.0 | Text rendering and formatting |
| `pydantic` | ≥1.2 | Data validation and log models |

**Version Pinning Strategy**:

*   **Tenstorrent Libraries** (`pyluwen`, `tt_tools_common`): Use compatible version specifier (`~=`) to ensure hardware compatibility
*   **Common Libraries** (`textual`, `rich`, etc.): Use minimum version specifier (`>=`) for flexibility

**Sources:**[pyproject.toml 26-36](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L26-L36)

## Build and Distribution

### Package Configuration

The project is configured as a standard Python package via `pyproject.toml` at [pyproject.toml 1-87](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L1-L87):

*   **Package Name**: `tt-smi`
*   **Current Version**: 3.1.1
*   **Entry Point**: `tt-smi = "tt_smi:main"` at [pyproject.toml 53-54](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L53-L54)
*   **Build System**: setuptools ≥ 43.0.0 with wheel support
*   **Python Requirement**: ≥ 3.10

### Distribution Channels

**Installation Methods**:

1.   **PyPI**: `pip install tt-smi` (recommended for users)
2.   **From Source**: `pip install .` or `pip install --editable .` (for developers)
3.   **Debian Package**: Via Tenstorrent Debian repository

**Sources:**[pyproject.toml 1-87](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L1-L87)[README.md 47-84](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L47-L84)

## Version History

Current stable version: **3.1.1**

**Major Milestones**:

*   **v3.0.35**: Removed Grayskull support
*   **v3.x**: Added Galaxy 6U support, IPMI-based resets
*   **v2.x**: Introduced Textual-based TUI, Blackhole support
*   **v1.x**: Initial ncurses-based implementation

For detailed version history, see [Version History and Changes](https://deepwiki.com/tenstorrent/tt-smi/9-version-history-and-changes).

**Sources:**[pyproject.toml 3](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L3-L3)[README.md 15](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L15-L15)

## CLI Command Reference

Quick reference of main CLI options:

| Option | Description | Example |
| --- | --- | --- |
| (no args) | Launch interactive TUI | `tt-smi` |
| `-v, --version` | Display version | `tt-smi -v` |
| `-ls, --list` | List all devices | `tt-smi -ls` |
| `-s, --snapshot` | Print snapshot to stdout | `tt-smi -s` |
| `-f [file]` | Save snapshot to file | `tt-smi -f output.json` |
| `-c, --compact` | Hide sidebar in GUI | `tt-smi -c` |
| `-l, --local` | Local chips only (WH) | `tt-smi -l` |
| `-r [indices]` | Reset devices | `tt-smi -r 0,1` |
| `-glx_reset` | Reset Galaxy system | `tt-smi -glx_reset` |
| `-glx_reset_tray N` | Reset Galaxy tray | `tt-smi -glx_reset_tray 2` |
| `--no_reinit` | Skip re-detection after reset | `tt-smi -r --no_reinit` |

For comprehensive CLI documentation, see [Command-Line Interface](https://deepwiki.com/tenstorrent/tt-smi/2-command-line-interface).

**Sources:**[tt_smi/tt_smi.py 576-682](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L576-L682)[README.md 86-127](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L86-L127)

## Testing Framework

TT-SMI includes a pytest-based testing suite at [tests/test_reset.py 1-104](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tests/test_reset.py#L1-L104):

**Test Fixtures**:

*   `devices`: Returns list of PCI indices via `pci_scan()`
*   `is_galaxy_6u`: Detects if system is Galaxy 6U (32 devices, all Galaxy board types)

**Test Cases**:

*   `test_pci_reset_all_devices`: Single reset of all PCI devices
*   `test_pci_reset_all_devices_stress`: 10 consecutive resets (stress test)
*   `test_glx_reset`: Single Galaxy system reset
*   `test_glx_reset_stress`: 10 consecutive Galaxy resets

Tests automatically skip based on hardware configuration (e.g., skip PCI tests on Galaxy, skip Galaxy tests on non-Galaxy).

**Sources:**[tests/test_reset.py 1-104](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tests/test_reset.py#L1-L104)

Dismiss
Refresh this wiki

Enter email to refresh