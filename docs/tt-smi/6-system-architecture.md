---
project: tt-smi
github: https://github.com/tenstorrent/tt-smi
deepwiki: https://deepwiki.com/tenstorrent/tt-smi/6-system-architecture
---

# System Architecture

Relevant source files
*   [tt_smi/tt_smi.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py)
*   [tt_smi/tt_smi_backend.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py)

This document provides a detailed technical overview of the Tenstorrent System Management Interface (TT-SMI) internal architecture, component structure, and data flow mechanisms. It covers the core system design, how different components interact, and the internal organization of the codebase. For user interfaces, see [User Interfaces](https://deepwiki.com/tenstorrent/tt-smi/2-command-line-interface); for hardware support details, see [Hardware Support](https://deepwiki.com/tenstorrent/tt-smi/3-interactive-text-user-interface-(tui)); and for reset functionality internals, see [Reset Functionality](https://deepwiki.com/tenstorrent/tt-smi/4-hardware-platform-support).

## 1. Core Architecture Overview

TT-SMI follows a layered architecture that separates user interfaces from hardware interaction logic. The system is designed with a clear separation of concerns, allowing for extensibility across different hardware generations while maintaining a consistent interface.

Sources:

*   [tt_smi/tt_smi.py 33-40](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L33-L40)
*   [tt_smi/tt_smi_backend.py 48-99](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L48-L99)
*   [pyproject.toml 26-37](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L26-L37)

## 2. Key Components and Responsibilities

The TT-SMI architecture consists of the following major components, each with distinct responsibilities:

### 2.1 Main Application Entry Point

The `tt_smi.py` module serves as the entry point, handling command-line arguments, setting up the backend, and launching the appropriate interface (CLI or TUI).

Sources:

*   [tt_smi/tt_smi.py 785-907](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L785-L907)
*   [tt_smi/tt_smi.py 719-757](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L719-L757)

### 2.2 Backend System

The `TTSMIBackend` class encapsulates hardware interaction, data collection, and formatting logic. It's responsible for:

1.   Managing device objects
2.   Collecting device information and telemetry
3.   Handling reset operations
4.   Generating log data

Sources:

*   [tt_smi/tt_smi_backend.py 49-155](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L49-L155)
*   [tt_smi/tt_smi_backend.py 334-367](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L334-L367)
*   [tt_smi/tt_smi_backend.py 436-442](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L436-L442)

### 2.3 User Interface Components

TT-SMI provides both a CLI and a TUI built with the Textual library. The `TTSMI` class defines the TUI, which includes multiple tabs for different information categories.

Sources:

*   [tt_smi/tt_smi.py 68-625](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L68-L625)
*   [tt_smi/tt_smi.py 126-146](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L126-L146)

## 3. Data Flow Architecture

The data flow in TT-SMI follows a well-defined pattern from hardware to user interface:

Sources:

*   [tt_smi/tt_smi.py 596-614](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L596-L614)
*   [tt_smi/tt_smi_backend.py 224-228](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L224-L228)
*   [tt_smi/tt_smi_backend.py 436-442](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L436-L442)

## 4. Device Information and Telemetry Collection

TT-SMI collects several categories of information from hardware devices:

### 4.1 Device Information Collection

The system gathers static device information including:

| Information Type | Description | Source Method |
| --- | --- | --- |
| Board ID | Unique identifier for the board | `get_board_id()` |
| Board Type | Hardware model (e.g., n300, grayskull) | `get_board_type()` |
| Bus ID | PCI bus identifier | `get_pci_bdf()` |
| PCIe Speed | Current PCIe link speed | `get_pci_properties()` |
| PCIe Width | Current PCIe link width | `get_pci_properties()` |
| DRAM Status | DRAM training status | `get_dram_training_status()` |
| DRAM Speed | Memory speed rating | `get_dram_speed()` |
| Coordinates | For clustered systems, spatial coordinates | `get_local_coord()` |

Sources:

*   [tt_smi/tt_smi_backend.py 334-367](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L334-L367)
*   [tt_smi/tt_smi_backend.py 268-305](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L268-L305)
*   [tt_smi/tt_smi_backend.py 230-266](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L230-L266)

### 4.2 Telemetry Collection

Real-time telemetry data is collected periodically from devices, including:

| Telemetry Type | Description | Source |
| --- | --- | --- |
| Voltage | Core voltage in volts | SMBUS_TELEMETRY.VCORE |
| Current | Current draw in amps | SMBUS_TELEMETRY.TDC |
| Power | Power consumption in watts | SMBUS_TELEMETRY.TDP |
| AICLK | AI clock frequency in MHz | SMBUS_TELEMETRY.AICLK |
| ASIC Temperature | Die temperature in °C | SMBUS_TELEMETRY.ASIC_TEMPERATURE |

Sources:

*   [tt_smi/tt_smi_backend.py 373-442](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L373-L442)
*   [tt_smi/tt_smi.py 166-171](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L166-L171)

### 4.3 Firmware Version Collection

The system collects firmware versions from different components:

| Firmware Component | Description |
| --- | --- |
| CM Firmware | Control Manager firmware version |
| CM Firmware Date | Build date of CM firmware |
| Ethernet Firmware | Ethernet controller firmware version |
| BM Bootloader | Board Manager bootloader version |
| BM Application | Board Manager application version |
| TT-Flash Version | Version of flash tool used |
| Firmware Bundle | Overall firmware bundle version |

Sources:

*   [tt_smi/tt_smi_backend.py 501-562](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L501-L562)

## 5. Reset Operations Architecture

TT-SMI implements different reset mechanisms depending on the device type:

Sources:

*   [tt_smi/tt_smi_backend.py 647-709](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L647-L709)
*   [tt_smi/tt_smi_backend.py 564-579](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L564-L579)
*   [tt_smi/tt_smi.py 793-854](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L793-L854)

## 6. System Initialization Flow

The startup sequence of TT-SMI follows these steps:

Sources:

*   [tt_smi/tt_smi.py 785-907](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L785-L907)
*   [tt_smi/tt_smi.py 628-716](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L628-L716)
*   [tt_smi/tt_smi_backend.py 55-99](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L55-L99)

## 7. Logging Architecture

TT-SMI includes a structured logging system for capturing system state:

Sources:

*   [tt_smi/tt_smi_backend.py 63-76](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L63-L76)
*   [tt_smi/tt_smi_backend.py 141-156](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L141-L156)
*   [tt_smi/tt_smi_backend.py 111-140](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L111-L140)

## 8. Component Dependencies

TT-SMI depends on several internal and external components:

### 8.1 Internal Dependencies

### 8.2 External Dependencies

| Dependency | Purpose | Version |
| --- | --- | --- |
| pyluwen | Low-level hardware access | git+[https://github.com/tenstorrent/luwen.git@v0.6.4](https://github.com/tenstorrent/luwen.git@v0.6.4) |
| tt_tools_common | Common utilities for Tenstorrent tools | git+[https://github.com/tenstorrent/tt-tools-common.git@v1.4.14](https://github.com/tenstorrent/tt-tools-common.git@v1.4.14) |
| textual | TUI framework | 0.59.0 |
| rich | Terminal formatting | 13.7.0 |
| elasticsearch | Optional log storage | 8.11.0 |
| pydantic | Data validation | >=1.2 |

Sources:

*   [pyproject.toml 26-37](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L26-L37)
*   [tt_smi/tt_smi.py 13-28](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L13-L28)
*   [tt_smi/tt_smi_backend.py 10-44](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_backend.py#L10-L44)

Dismiss
Refresh this wiki

Enter email to refresh