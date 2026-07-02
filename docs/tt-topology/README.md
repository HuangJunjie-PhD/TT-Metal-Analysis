---
project: tt-topology
github: https://github.com/tenstorrent/tt-topology
deepwiki: https://deepwiki.com/tenstorrent/tt-topology
---

# Overview

Relevant source files
*   [CHANGELOG.md](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/CHANGELOG.md?plain=1)
*   [README.md](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1)
*   [pyproject.toml](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml)

The `tt-topology` repository is a specialized command-line utility designed to configure ethernet routing topologies for Tenstorrent hardware systems. This tool manages the coordination and configuration of multiple NB (neural board) cards connected through ethernet links, enabling them to operate as cohesive computing clusters in mesh, linear, torus, or isolated layouts.

This document covers the fundamental architecture, capabilities, and design principles of the tt-topology system. For detailed installation instructions, see [Installation](https://deepwiki.com/tenstorrent/tt-topology/2.1-installation). For complete command-line usage, see [CLI Reference](https://deepwiki.com/tenstorrent/tt-topology/3-cli-reference). For hardware-specific configuration details, see [Hardware Support](https://deepwiki.com/tenstorrent/tt-topology/5-hardware-support).

## System Architecture

The tt-topology system follows a layered architecture that bridges high-level user commands with low-level hardware programming:

Sources: [pyproject.toml 43-44](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml#L43-L44)[pyproject.toml 26-36](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml#L26-L36)

## Core Capabilities

tt-topology provides four primary operational modes, each targeting different use cases and hardware configurations:

| Mode | Purpose | Algorithm | Hardware Support |
| --- | --- | --- | --- |
| `mesh` | Trivalent graph topology | BFS coordinate assignment | N150, N300 |
| `linear` | Single-line connectivity | DFS longest path detection | N150, N300 |
| `torus` | Cyclic graph topology | Cycle detection and ordering | N150, N300 |
| `isolated` | Disabled ethernet state | Default coordinate reset | N150, N300 |
| `octopus` | Galaxy cluster configuration | Specialized Galaxy programming | N150 + Galaxy |

The tool operates through a consistent five-step process: default flash, board reset, connection mapping, coordinate generation, and final flash with reset.

Sources: [README.md 109-156](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L109-L156)[README.md 158-232](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L158-L232)[README.md 95-105](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L95-L105)

## Hardware Configuration Workflow

The system follows a standardized workflow for configuring hardware topologies:

Sources: [README.md 95-105](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L95-L105)[README.md 220-232](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L220-L232)

## Memory Interface Architecture

The system interacts with Tenstorrent hardware through a well-defined memory map and register interface:

Sources: [pyproject.toml 30-31](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml#L30-L31)

## Development Stack Integration

tt-topology integrates with the broader Tenstorrent development ecosystem through carefully managed dependencies:

Sources: [pyproject.toml 26-36](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml#L26-L36)[CHANGELOG.md 20](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/CHANGELOG.md?plain=1#L20-L20)[CHANGELOG.md 47-48](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/CHANGELOG.md?plain=1#L47-L48)

## Supported Hardware Matrix

The system supports specific Tenstorrent hardware configurations with defined limitations:

| Hardware Type | Support Status | Topology Modes | Special Notes |
| --- | --- | --- | --- |
| N150 PCIe cards | ✅ Supported | mesh, linear, torus, isolated | Standard operation |
| N300 PCIe cards | ✅ Supported | mesh, linear, torus, isolated | Dual-chip boards |
| Galaxy systems | ✅ Supported | octopus only | Requires reset config |
| BH PCIe cards | ❌ Not supported | none | Tool will error |
| WH 6U Galaxy | ❌ Not supported | none | Tool will error |
| BH 6U Galaxy | ❌ Not supported | none | Tool will error |

The tool includes built-in validation to prevent usage with unsupported hardware configurations, ensuring safe operation across the supported hardware matrix.

Sources: [README.md 12-18](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L12-L18)[README.md 158-161](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L158-L161)

## Operational Outputs

tt-topology generates structured outputs for both operational use and debugging purposes:

*   **JSON logs**: Structured logs stored in `~/tt_topology_logs/` with pre/post flash register states, connection maps, and coordinate assignments
*   **PNG visualizations**: Graph plots showing physical chip layout and connections, customizable filename via `-p` flag
*   **Elasticsearch integration**: Compatible JSON format for enterprise log aggregation and analysis
*   **Console output**: Real-time progress and status information during operation

For detailed logging configuration, see [Logging and Diagnostics](https://deepwiki.com/tenstorrent/tt-topology/6-logging-and-diagnostics). For troubleshooting common issues, see [Troubleshooting](https://deepwiki.com/tenstorrent/tt-topology/8-troubleshooting).

Sources: [README.md 233-240](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L233-L240)[pyproject.toml 28](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml#L28-L28)

Dismiss
Refresh this wiki

Enter email to refresh