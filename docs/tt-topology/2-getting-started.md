---
project: tt-topology
github: https://github.com/tenstorrent/tt-topology
deepwiki: https://deepwiki.com/tenstorrent/tt-topology/2-getting-started
---

# Getting Started

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1)
*   [pyproject.toml](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml)

This page provides an introduction to tt-topology and guides you through the basic concepts needed to start configuring Tenstorrent hardware topologies. For detailed installation instructions, see [Installation](https://deepwiki.com/tenstorrent/tt-topology/2.1-installation). For hardware compatibility information, see [Hardware Requirements](https://deepwiki.com/tenstorrent/tt-topology/2.2-hardware-requirements). For complete command-line documentation, see [CLI Reference](https://deepwiki.com/tenstorrent/tt-topology/3-cli-reference).

## Purpose and Scope

TT-Topology is a command-line utility that configures ethernet routing topologies for Tenstorrent hardware by flashing coordinate information to NB (Nebula) cards. The tool supports multiple topology layouts including mesh, linear, torus, and isolated configurations, as well as specialized Galaxy/Octopus system programming.

This page covers the essential concepts and workflow for using tt-topology. It does not cover advanced topics like custom topology algorithms or hardware debugging, which are documented in [Core Architecture](https://deepwiki.com/tenstorrent/tt-topology/4-core-architecture) and [Troubleshooting](https://deepwiki.com/tenstorrent/tt-topology/8-troubleshooting) respectively.

## What is tt-topology?

TT-Topology bridges high-level topology concepts with low-level hardware configuration by:

*   **Detecting** connected Tenstorrent chips and their interconnections
*   **Computing** coordinate assignments based on the selected topology layout
*   **Flashing** these coordinates to SPI registers on the hardware
*   **Validating** the configuration through board resets and connectivity checks

**Workflow Overview**

Sources: [pyproject.toml 44](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml#L44-L44)[README.md 95-105](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L95-L105)

## Core System Components

The following diagram shows how the main code entities work together to transform user commands into hardware configuration:

Sources: [pyproject.toml 26-36](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml#L26-L36)[pyproject.toml 44](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml#L44-L44)[README.md 158-232](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L158-L232)

## Basic Usage Patterns

TT-Topology operates through command-line arguments that specify the desired topology layout and configuration options:

### Standard Topology Configuration

`# Configure mesh topologytt-topology -l mesh -p mesh_layout.png # Configure linear topology  tt-topology -l linear -p linear_layout.png # Configure torus topologytt-topology -l torus -p torus_layout.png`
### System Information and Diagnostics

`# List current hardware topologytt-topology -ls # Generate reset configuration for Galaxy systemstt-topology -g`
### Galaxy/Octopus Mode

`# Configure Galaxy systems with reset configurationtt-topology -o -r ~/.config/tenstorrent/reset_config.json`
Sources: [README.md 72-94](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L72-L94)[README.md 115-117](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L115-L117)[README.md 162-218](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L162-L218)

## Command Flow and File Interactions

This diagram shows how user commands flow through the codebase and what files are involved:

Sources: [README.md 95-105](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L95-L105)[pyproject.toml 26-36](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml#L26-L36)[README.md 234-240](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L234-L240)

## Prerequisites and Dependencies

Before using tt-topology, ensure your system meets these requirements:

| Component | Requirement | Purpose |
| --- | --- | --- |
| Python | >= 3.7 | Runtime environment |
| Rust | Latest stable | Required for pyluwen library |
| Tenstorrent Hardware | N150, N300 boards | Target hardware for configuration |
| Operating System | Linux | Hardware interface compatibility |

**Key Dependencies:**

*   `pyluwen` - Hardware interface library requiring Rust
*   `tt_tools_common` - Tenstorrent hardware utilities
*   `networkx` - Graph algorithms for topology computation
*   `elasticsearch` - Structured logging backend
*   `matplotlib` - Visualization output

Sources: [pyproject.toml 6](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml#L6-L6)[pyproject.toml 26-36](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml#L26-L36)[README.md 24-28](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L24-L28)

## Hardware Compatibility

TT-Topology supports specific Tenstorrent hardware configurations:

**Supported Hardware:**

*   N150 boards (Nebula cards)
*   N300 boards (dual-chip Nebula cards)
*   Galaxy systems (via Octopus mode)

**Unsupported Hardware:**

*   BH PCIe cards
*   WH 6U Galaxy systems
*   BH 6U Galaxy systems

The tool will detect and error out when used with unsupported hardware configurations.

Sources: [README.md 12-18](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L12-L18)

## Next Steps

To begin using tt-topology:

1.   **Install the tool** - Follow the detailed instructions in [Installation](https://deepwiki.com/tenstorrent/tt-topology/2.1-installation)
2.   **Verify hardware** - Check compatibility requirements in [Hardware Requirements](https://deepwiki.com/tenstorrent/tt-topology/2.2-hardware-requirements)
3.   **Learn the commands** - Review all available options in [CLI Reference](https://deepwiki.com/tenstorrent/tt-topology/3-cli-reference)
4.   **Understand layouts** - Study the topology types in [Layout Types](https://deepwiki.com/tenstorrent/tt-topology/3.2-layout-types)
5.   **Configure logging** - Set up diagnostics as described in [Logging and Diagnostics](https://deepwiki.com/tenstorrent/tt-topology/6-logging-and-diagnostics)

For advanced usage including custom topologies and system integration, see [Core Architecture](https://deepwiki.com/tenstorrent/tt-topology/4-core-architecture) and [Development](https://deepwiki.com/tenstorrent/tt-topology/7-development).

Dismiss
Refresh this wiki

Enter email to refresh