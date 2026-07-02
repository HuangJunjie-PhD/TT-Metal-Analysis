---
project: tt-topology
github: https://github.com/tenstorrent/tt-topology
deepwiki: https://deepwiki.com/tenstorrent/tt-topology/3-cli-reference
---

# CLI Reference

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1)
*   [tt_topology/tt_topology.py](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py)

This document provides comprehensive reference documentation for the `tt-topology` command-line interface. It covers all available command-line options, execution modes, and usage patterns for configuring Tenstorrent hardware topologies.

For information about specific topology algorithms and coordinate generation, see [Topology Algorithms](https://deepwiki.com/tenstorrent/tt-topology/4.3-topology-algorithms). For hardware-specific configuration details, see [Hardware Support](https://deepwiki.com/tenstorrent/tt-topology/5-hardware-support).

## Command Structure

The `tt-topology` CLI follows a standard argument-based structure with several mutually exclusive operational modes:

**CLI Argument Structure and Execution Modes**

Sources: [tt_topology/tt_topology.py 36-116](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L36-L116)

## Command Options

### Core Options

| Option | Short | Type | Default | Description |
| --- | --- | --- | --- | --- |
| `--version` | `-v` | flag | - | Display version information and exit |
| `--layout` | `-l` | choice | `linear` | Select topology layout: `linear`, `torus`, `mesh`, `isolated` |
| `--octopus` | `-o` | flag | `false` | Enable Galaxy/Octopus configuration mode |
| `--list` | `-ls` | flag | `false` | List current topology and board information |
| `--generate_reset_json` | `-g` | flag | `false` | Generate default reset configuration file |

### File Output Options

| Option | Short | Type | Default | Description |
| --- | --- | --- | --- | --- |
| `--log` | - | optional string | `~/tt_topology_logs/<timestamp>_log.json` | Topology flash log filename |
| `--plot_filename` | `-p` | optional string | `chip_layout.png` | Graph visualization output file |
| `--filename` | `-f` | optional string | `~/tt_smi/<timestamp>_snapshot.json` | Test log filename |

### Configuration Options

| Option | Short | Type | Default | Description |
| --- | --- | --- | --- | --- |
| `--reset` | `-r` | list | `None` | Reset JSON configuration file(s) for Octopus mode |

Sources: [tt_topology/tt_topology.py 40-114](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L40-L114)

## Execution Flow

The CLI dispatches to different execution paths based on the provided arguments:

**CLI Execution Flow and Backend Dispatch**

Sources: [tt_topology/tt_topology.py 404-538](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L404-L538)[tt_topology/tt_topology.py 428-475](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L428-L475)

## Operational Modes

### Standard Topology Configuration

The standard mode configures layouts for N150/N300 boards with ethernet connections:

**Standard Topology Mode Execution Path**

Sources: [tt_topology/tt_topology.py 515-519](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L515-L519)[tt_topology/tt_topology.py 232-244](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L232-L244)

### Galaxy/Octopus Configuration Mode

Octopus mode handles Galaxy system configuration with specialized reset procedures:

**Galaxy/Octopus Configuration Flow**

Sources: [tt_topology/tt_topology.py 288-402](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L288-L402)[tt_topology/tt_topology.py 491-513](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L491-L513)

## Usage Examples

### Basic Topology Configuration

`# Configure linear topology (default)tt-topology # Configure mesh topology with custom plot filenamett-topology -l mesh -p mesh_visualization.png # Configure torus topology with custom log filett-topology -l torus --log custom_torus.json # Set boards to isolated statett-topology -l isolated`
### Information and Discovery

`# List current topology and board informationtt-topology --list # Display version informationtt-topology --version # Generate reset configuration templatett-topology --generate_reset_json`
### Galaxy/Octopus Configuration

`# Configure Galaxy system with reset JSONtt-topology --octopus --reset ~/.config/tenstorrent/reset_config.json # Generate reset configuration for Galaxy setuptt-topology -g`
Sources: [README.md 72-94](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L72-L94)[README.md 115-148](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L115-L148)[README.md 163-218](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L163-L218)

## Device Detection and Filtering

The CLI automatically detects and filters supported hardware:

**Device Detection and Hardware Filtering**

Sources: [tt_topology/tt_topology.py 452-470](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L452-L470)[tt_topology/tt_topology.py 428-436](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L428-L436)

## Error Handling

The CLI implements comprehensive error handling for common failure scenarios:

| Error Condition | Check Location | Action |
| --- | --- | --- |
| No driver detected | `get_driver_version()` | Exit with driver installation message |
| No arguments provided | `len(sys.argv) > 1` | Print usage and exit |
| No devices detected | `detect_chips_with_callback()` | Exit with hardware check message |
| Unsupported boards | Board type filtering | Warning message, continue with supported |
| Missing reset JSON (Octopus) | Reset input validation | Exit with error message |
| Invalid reset JSON format | `ResetType.CONFIG_JSON` | Exit with format error |
| Device count mismatch | Post-reset device verification | Exit with detection error |

Sources: [tt_topology/tt_topology.py 411-418](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L411-L418)[tt_topology/tt_topology.py 420-426](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L420-L426)[tt_topology/tt_topology.py 437-450](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L437-L450)[tt_topology/tt_topology.py 494-509](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L494-L509)

## Integration with Backend Systems

The CLI serves as the entry point that instantiates and configures backend processing systems:

**CLI to Backend System Integration**

Sources: [tt_topology/tt_topology.py 516](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L516-L516)[tt_topology/tt_topology.py 511](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L511-L511)

Dismiss
Refresh this wiki

Enter email to refresh