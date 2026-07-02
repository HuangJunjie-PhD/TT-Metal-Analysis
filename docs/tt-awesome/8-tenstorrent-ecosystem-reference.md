---
project: tt-awesome
github: https://github.com/tenstorrent/tt-awesome
deepwiki: https://deepwiki.com/tenstorrent/tt-awesome/8-tenstorrent-ecosystem-reference
---

# Tenstorrent Ecosystem Reference

Relevant source files
*   [entries/riscv-arch/ttsim.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/riscv-arch/ttsim.json)
*   [src/_data/github_meta.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/github_meta.json)
*   [src/_data/planet_feeds.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planet_feeds.json)

The Tenstorrent ecosystem encompasses a diverse range of hardware architectures and a layered software stack designed for AI and general-purpose compute. This page provides a high-level overview of these components as documented in the `tt-awesome` directory.

## Hardware Generations & Architecture

Tenstorrent hardware is built around the **Tensix** core, a proprietary compute engine optimized for tensor operations. The ecosystem spans multiple generations of silicon and system-level configurations, ranging from single-chip PCIe cards to multi-chip scale-out solutions.

### Key Architecture Components

*   **Tensix Cores**: The primary compute units containing SIMD engines and dense math units.
*   **NoC (Network on Chip)**: The internal interconnect allowing cores to communicate via a 2D-mesh topology.
*   **ERISC / ARC**: Specialized RISC-V processors for Ethernet management and system control.
*   **ttsim**: A bit-exact simulator that allows developers to run `TT-Metalium` workloads on x86 systems without physical silicon [entries/riscv-arch/ttsim.json 4](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/riscv-arch/ttsim.json#L4-L4)

### Hardware Roadmap

*   **Grayskull**: The first-generation architecture.
*   **Wormhole**: Second-generation chips featuring integrated Ethernet for scaling (e.g., **n150** and **n300** cards).
*   **Blackhole**: The latest generation (e.g., **p150** and **p300**), designed for switch-free scaling and high-bandwidth memory [src/_data/planet_feeds.json 78](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planet_feeds.json#L78-L78)
*   **Galaxy**: A 32-chip multi-card configuration for large-scale deployments [src/_data/planet_feeds.json 64](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planet_feeds.json#L64-L64)

For details on core counts, memory bandwidth, and specific board configurations, see [Hardware Generations & Architecture](https://deepwiki.com/tenstorrent/tt-awesome/8.1-hardware-generations-and-architecture).

## Software Stack Overview

The software stack is organized into layers, from low-level kernel drivers to high-level MLIR-based compilers and inference servers.

### Stack Layers

| Layer | Component | Description |
| --- | --- | --- |
| **Driver / Firmware** | `tt-kmd`, `tt-umd`, `tt-system-firmware` | Kernel and user-mode drivers; board management firmware [src/_data/planet_feeds.json 76-84](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planet_feeds.json#L76-L84) |
| **Programming Model** | `TT-Metalium` (`tt-metal`) | The low-level programming model for direct Tensix core control. |
| **Libraries** | `TT-NN` | High-level tensor library for neural network operations. |
| **Compilers** | `TT-Forge`, `tt-buda` | MLIR-based and legacy compiler stacks for model deployment. |
| **Tools & Servers** | `tt-smi`, `tt-inference-server`, `tt-exalens` | System monitoring, model serving, and debugging tools [src/_data/planet_feeds.json 48-56](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planet_feeds.json#L48-L56) |

For details on the API surface and integration paths, see [Software Stack Overview](https://deepwiki.com/tenstorrent/tt-awesome/8.2-software-stack-overview).

## Ecosystem Entity Mapping

The following diagram bridges the high-level system names to the specific code entities and repositories tracked within the `tt-awesome` data layer.

### Code Entity Relationship

Sources: [src/_data/planet_feeds.json 6-15](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planet_feeds.json#L6-L15)[src/_data/planet_feeds.json 48-57](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planet_feeds.json#L48-L57)[entries/riscv-arch/ttsim.json 2-16](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/riscv-arch/ttsim.json#L2-L16)

### Data Flow: From Silicon to Service

This diagram illustrates how system telemetry and releases flow through the ecosystem as captured by the `tt-awesome` automation scripts.

Sources: [src/_data/github_meta.json 76-116](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/github_meta.json#L76-L116)[src/_data/planet_feeds.json 48-57](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planet_feeds.json#L48-L57)[src/_data/planet_feeds.json 76-85](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planet_feeds.json#L76-L85)

Dismiss
Refresh this wiki

Enter email to refresh