---
project: tt-burnin
github: https://github.com/tenstorrent/tt-burnin
deepwiki: https://deepwiki.com/tenstorrent/tt-burnin/4-hardware-support
---

# Hardware Support

Relevant source files
*   [CHANGELOG.md](https://github.com/tenstorrent/tt-burnin/blob/809f293d/CHANGELOG.md?plain=1)
*   [tt_burnin/chip.py](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/chip.py)

This document provides comprehensive coverage of the hardware platforms supported by tt-burnin and their specific implementations. It covers the hardware abstraction layer, chip-specific implementations, detection mechanisms, and low-level hardware operations available for Tenstorrent AI accelerator chips.

For information about TTX workload files specific to each hardware platform, see [TTX Workload Files](https://deepwiki.com/tenstorrent/tt-burnin/4.2-ttx-workload-files). For details about the main application logic that orchestrates hardware operations, see [Main Application Logic](https://deepwiki.com/tenstorrent/tt-burnin/2.1-main-application-logic).

## Supported Hardware Architectures

The tt-burnin utility supports three generations of Tenstorrent AI accelerator architectures through a unified hardware abstraction layer. Each architecture has distinct characteristics and capabilities that are abstracted through common interfaces.

### Hardware Support Matrix

| Architecture | Class | Grid Size | Tensix Grid | Remote Support | First Added |
| --- | --- | --- | --- | --- | --- |
| Grayskull | `GsChip` | 13×12 | 12×10 | No | v0.1.0 |
| Wormhole | `WhChip` | 10×12 | 8×10 | Yes | v0.1.0 |
| Blackhole | `BhChip` | 17×12 | 14×10 | No | v0.2.0 |

### Architecture Evolution and Features

**Sources:**[tt_burnin/chip.py 173-364](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/chip.py#L173-L364)[CHANGELOG.md 8-40](https://github.com/tenstorrent/tt-burnin/blob/809f293d/CHANGELOG.md?plain=1#L8-L40)

## Hardware Abstraction Layer

The hardware abstraction is built around the `TTChip` base class, which provides a uniform interface for all Tenstorrent chip architectures while allowing architecture-specific implementations to override behavior as needed.

### Core Abstraction Design

### Hardware Interface Operations

The `TTChip` base class provides standardized methods for low-level hardware operations across all architectures:

| Operation Category | Methods | Purpose |
| --- | --- | --- |
| NOC Operations | `noc_read32`, `noc_write32`, `noc_broadcast32` | Network-on-chip communication |
| AXI Operations | `axi_read32`, `axi_write32` | AXI bus transactions |
| SPI Operations | `spi_read`, `spi_write` | SPI interface access |
| ARC Messaging | `arc_msg` | ARM processor communication |
| Telemetry | `get_telemetry`, `get_telemetry_unchanged` | Hardware monitoring |

**Sources:**[tt_burnin/chip.py 17-164](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/chip.py#L17-L164)

## Architecture-Specific Implementations

### Grayskull Implementation

The `GsChip` class implements support for the first-generation Grayskull architecture with a 13×12 grid containing 12×10 Tensix cores.

Key characteristics:

*   Grid dimensions: 13×12 with NOC coordinate mapping via `PHYS_X_TO_NOC_0_X` and `PHYS_Y_TO_NOC_0_Y` arrays
*   Harvesting support through row-level disabling based on `get_harvest_bits()`
*   No multi-node coordination support (`coord()` returns `"N/A"`)

**Sources:**[tt_burnin/chip.py 317-364](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/chip.py#L317-L364)

### Wormhole Implementation

The `WhChip` class supports the second-generation Wormhole architecture with enhanced multi-node capabilities and remote chip support.

Features:

*   Dual NOC coordinate systems with separate mapping arrays for NOC 0 and NOC 1
*   Row-based harvesting using physical-to-NOC coordinate translation
*   Multi-node coordinate reporting via `coord()` returning shelf/rack coordinates
*   Remote chip variant (`RemoteWhChip`) with custom broadcast implementations

**Sources:**[tt_burnin/chip.py 253-315](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/chip.py#L253-L315)

### Blackhole Implementation

The `BhChip` class represents the latest generation with advanced features including NOC translation and column-based harvesting.

Advanced capabilities:

*   Largest grid at 17×12 with support for 14×10 Tensix cores
*   Column-based harvesting controlled by `enabled_tensix_columns` bitmask
*   Optional NOC translation mode that affects core mapping
*   Enhanced telemetry including signed temperature reporting

**Sources:**[tt_burnin/chip.py 173-251](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/chip.py#L173-L251)

## Hardware Detection and Initialization

The detection system provides both robust and fallible detection modes for different use cases in the burn-in utility.

### Detection Flow

### Detection Functions

| Function | Purpose | Use Case |
| --- | --- | --- |
| `detect_local_chips()` | Fallible detection with communication verification | Production burn-in |
| `detect_chips()` | Standard detection with remote support | General usage |

The detection process includes real-time progress reporting with terminal-aware output formatting for interactive use.

**Sources:**[tt_burnin/chip.py 366-446](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/chip.py#L366-L446)

## Hardware Operations Interface

### Low-Level Hardware Access

The hardware abstraction provides direct access to chip subsystems through standardized interfaces:

### Coordinate Systems and Grid Management

Each architecture implements coordinate transformation between physical and NOC address spaces:

*   **Grayskull**: Single NOC with static mapping arrays
*   **Wormhole**: Dual NOC systems with bidirectional mapping
*   **Blackhole**: Dynamic coordinate systems with optional translation

The `get_tensix_locations()` method provides architecture-aware core enumeration that accounts for harvesting and coordinate transformations.

**Sources:**[tt_burnin/chip.py 116-164](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/chip.py#L116-L164)[tt_burnin/chip.py 270-289](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/chip.py#L270-L289)[tt_burnin/chip.py 208-240](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/chip.py#L208-L240)

Dismiss
Refresh this wiki

Enter email to refresh