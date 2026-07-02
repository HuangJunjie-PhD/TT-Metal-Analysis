---
project: tt-topology
github: https://github.com/tenstorrent/tt-topology
deepwiki: https://deepwiki.com/tenstorrent/tt-topology/5-hardware-support
---

# Hardware Support

Relevant source files
*   [CHANGELOG.md](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/CHANGELOG.md?plain=1)
*   [README.md](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1)
*   [tt_topology/tt_topology.py](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py)

This document provides comprehensive information about the hardware platforms supported by tt-topology, including board types, capabilities, and configuration requirements. For information about specific memory layouts and register definitions, see [Memory Layout](https://deepwiki.com/tenstorrent/tt-topology/5.2-memory-layout). For details about supported board features and specifications, see [Supported Boards](https://deepwiki.com/tenstorrent/tt-topology/5.1-supported-boards).

## Overview

The tt-topology tool supports a specific subset of Tenstorrent hardware platforms designed for multi-chip topology configurations. The tool validates hardware compatibility during device detection and filters out unsupported platforms to ensure reliable operation.

### Hardware Support Matrix

**Sources:**[tt_topology/tt_topology.py 457](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L457-L457)[README.md 13-17](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L13-L17)

## Hardware Detection and Validation

The tt-topology system implements a multi-stage hardware detection and validation process to ensure compatibility and prevent operation on unsupported platforms.

### Detection Flow

**Sources:**[tt_topology/tt_topology.py 452-470](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L452-L470)[tt_topology/tt_topology.py 20-22](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L20-L22)

### Hardware Validation Logic

The system performs strict validation to ensure only compatible hardware is processed:

| Validation Stage | Function | Purpose |
| --- | --- | --- |
| Device Detection | `detect_chips_with_callback()` | Discovers all connected Tenstorrent devices |
| Board ID Extraction | `dev.board_id()` | Gets hardware-specific board identifier |
| Type Resolution | `get_board_type(hex_id)` | Maps board ID to human-readable type |
| Support Filtering | `board_type in supported_boards` | Filters to only supported platforms |

**Sources:**[tt_topology/tt_topology.py 455-461](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L455-L461)

## Board-Specific Capabilities

Different board types support different topology configurations and have distinct hardware characteristics that affect their use in tt-topology.

### Capability Matrix

**Sources:**[README.md 158-161](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L158-L161)[tt_topology/tt_topology.py 515-516](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L515-L516)

### N300 Board Characteristics

N300 boards contain two Wormhole chips and require special handling during coordinate assignment:

*   **Default State**: Local chip at (0,0), remote chip at (1,0)
*   **Reset Behavior**: Both chips require coordinated resets
*   **Device Count Validation**: Expected device count is `num_local_chips * 2`

**Sources:**[README.md 98](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L98-L98)[tt_topology/tt_topology.py 182](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L182-L182)

## Hardware Interface Architecture

The tt-topology system interfaces with hardware through multiple abstraction layers, each providing specific capabilities for different board types.

### Interface Stack

**Sources:**[tt_topology/tt_topology.py 14-27](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L14-L27)[tt_topology/tt_topology.py 154-166](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L154-L166)

### Device Detection Modes

The system supports different detection modes based on the intended operation:

| Mode | Function Call | Ethernet Access | Use Case |
| --- | --- | --- | --- |
| List Mode | `detect_chips_with_callback(local_only=False, ignore_ethernet=False)` | Required | Topology discovery |
| Octopus Mode | `detect_chips_with_callback(local_only=False, ignore_ethernet=False)` | Required | Galaxy configuration |
| Standard Flash | `detect_chips_with_callback(local_only=True, ignore_ethernet=True)` | Ignored | PCIe-only flashing |

**Sources:**[tt_topology/tt_topology.py 428-436](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L428-L436)

## Octopus Configuration Requirements

Octopus mode provides specialized support for Galaxy systems connected to N150 boards, requiring specific hardware configurations and reset procedures.

### Supported Octopus Configurations

**Sources:**[README.md 159-161](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L159-L161)[README.md 169-232](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L169-L232)

### Octopus Hardware Validation

Octopus mode performs additional validation beyond standard board detection:

*   **Reset JSON Required**: Must provide valid reset configuration file
*   **Device Count Validation**: Local vs remote device count verification
*   **Connection Mapping**: QSFP link validation and shelf assignment

**Sources:**[tt_topology/tt_topology.py 491-513](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L491-L513)[tt_topology/tt_topology.py 377-393](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L377-L393)

## Hardware Constraints and Limitations

The tt-topology tool has specific constraints based on hardware capabilities and driver requirements.

### System Requirements

| Component | Requirement | Validation |
| --- | --- | --- |
| Driver | tt-kmd 2.0.0+ | `get_driver_version()` check |
| Architecture | Wormhole only | Board type filtering |
| Form Factor | 4U Galaxy only | Explicit unsupported list |
| Interface | PCIe connectivity | Device enumeration |

**Sources:**[tt_topology/tt_topology.py 411-418](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L411-L418)[README.md 13-17](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L13-L17)

### Unsupported Hardware

The following hardware is explicitly not supported and will cause the tool to filter devices or exit:

*   **Blackhole Architecture**: All BH-based cards and systems
*   **6U Galaxy Systems**: Both WH and BH variants
*   **Legacy Architectures**: Pre-Wormhole chip designs

**Sources:**[README.md 13-17](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L13-L17)[tt_topology/tt_topology.py 463-468](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L463-L468)

### Device Count Expectations

Different board configurations have specific device count requirements:

**Sources:**[tt_topology/tt_topology.py 182-188](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L182-L188)[tt_topology/tt_topology.py 385-393](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L385-L393)

Dismiss
Refresh this wiki

Enter email to refresh