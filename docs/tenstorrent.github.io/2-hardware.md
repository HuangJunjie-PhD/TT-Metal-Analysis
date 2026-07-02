---
project: tenstorrent.github.io
github: https://github.com/tenstorrent/tenstorrent.github.io
deepwiki: https://deepwiki.com/tenstorrent/tenstorrent.github.io/2-hardware
---

# Hardware

Relevant source files
*   [core/README.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/README.md?plain=1)
*   [core/aibs/README.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/README.md?plain=1)
*   [core/aibs/grayskull/specifications.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/grayskull/specifications.md?plain=1)
*   [core/aibs/index.rst](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/index.rst)
*   [core/aibs/wormhole/specifications.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1)
*   [core/systems/index.rst](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/index.rst)
*   [core/systems/t3000/index.rst](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/t3000/index.rst)

## Purpose and Scope

This document provides an overview of Tenstorrent's hardware portfolio, including both complete systems and add-in boards (AIBs). The hardware described in this document forms the foundation for running Tenstorrent's software stack and machine learning workloads. For detailed software information, see [Software](https://deepwiki.com/tenstorrent/tenstorrent.github.io/3-software).

Sources: [core/aibs/index.rst 1-9](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/index.rst#L1-L9)[core/systems/index.rst 1-21](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/index.rst#L1-L21)

## Hardware Portfolio Overview

Tenstorrent's hardware portfolio consists of two primary categories:

1.   **Complete Systems** - Fully integrated workstations and servers pre-configured with Tensix processors.
2.   **Add-In Boards (AIBs)** - Individual Tensix processor cards and accessories that can be installed in compatible systems.

### Hardware to Code Entity Mapping

The following diagram bridges physical hardware products to their identifiers and supporting files within the documentation and system management structure.

Sources: [core/aibs/wormhole/specifications.md 15-35](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L15-L35)[core/aibs/grayskull/specifications.md 21-38](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/grayskull/specifications.md?plain=1#L21-L38)[core/aibs/README.md 14-33](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/README.md?plain=1#L14-L33)

## Complete Systems

Tenstorrent offers a range of complete systems designed for different performance levels and use cases, from single-processor workstations to multi-processor servers.

### System Comparison

| System | Form Factor | Tensix Processors | Key Features |
| --- | --- | --- | --- |
| **TT-QuietBox** | Desktop | 4x Wormhole or Blackhole | Liquid-cooled, silent operation |
| **TT-LoudBox (T3000)** | 4U Rack/Workstation | 4x Wormhole (n150s/n300s) | High-performance air-cooled [core/systems/t3000/index.rst 1-20](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/t3000/index.rst#L1-L20) |
| **T7000** | 4U Rack | 8x Wormhole n150s | High-density 8-card mesh [core/README.md 28](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/README.md?plain=1#L28-L28) |
| **T1000** | Desktop | 1x Wormhole n150s | Entry-level single-node [core/README.md 24](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/README.md?plain=1#L24-L24) |
| **Galaxy** | 4U Server | 32x Wormhole | Large-scale cluster node [core/README.md 32](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/README.md?plain=1#L32-L32) |

For details on workstations and server form factors, see [Complete Systems](https://deepwiki.com/tenstorrent/tenstorrent.github.io/2.1-complete-systems).

Sources: [core/systems/index.rst 4-21](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/index.rst#L4-L21)[core/README.md 18-34](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/README.md?plain=1#L18-L34)

## Add-In Boards (AIBs)

Tenstorrent's Add-In Boards include Tensix processors and accessories that facilitate multi-processor configurations.

### Tensix Processors

Tensix processors are Tenstorrent's AI accelerator cards. There are three generations of Tensix processors:

#### Blackhole Series

The latest generation featuring PCIe 5.0 and 800G networking. For detailed specifications and installation, see [Blackhole Processors](https://deepwiki.com/tenstorrent/tenstorrent.github.io/2.2.1-blackhole-processors).

#### Wormhole Series

The current mainstream generation. Models include the single-ASIC **n150** and dual-ASIC **n300**.

*   **n150d/n300d**: Active cooling (axial fans) for desktop use [core/aibs/wormhole/specifications.md 33](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L33-L33)
*   **n150s/n300s**: Passive cooling for server airflow [core/aibs/wormhole/specifications.md 13](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L13-L13)

For detailed specifications, see [Wormhole Processors](https://deepwiki.com/tenstorrent/tenstorrent.github.io/2.2.2-wormhole-processors).

#### Grayskull Series (Deprecated)

The first-generation processor. Software support is discontinued after `TT-KMD``ttkmd_1.31` and `TT-Metalium``v0.55`[core/aibs/grayskull/specifications.md 15-19](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/grayskull/specifications.md?plain=1#L15-L19) For historical reference, see [Grayskull Processors](https://deepwiki.com/tenstorrent/tenstorrent.github.io/2.2.3-grayskull-processors).

### Processor Comparison Table

| Specification | n150s (Wormhole) | n300s (Wormhole) | e150 (Grayskull) |
| --- | --- | --- | --- |
| **ASICs** | 1 | 2 | 1 |
| **Tensix Cores** | 72 | 128 | 120 |
| **SRAM** | 108 MB | 192 MB | 120 MB |
| **Memory** | 12 GB GDDR6 | 24 GB GDDR6 | 8 GB LPDDR4 |
| **TBP** | 160W | 300W | 200W |
| **Interface** | PCIe 4.0 x16 | PCIe 4.0 x16 | PCIe 4.0 x16 |

Sources: [core/aibs/wormhole/specifications.md 15-35](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L15-L35)[core/aibs/grayskull/specifications.md 21-38](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/grayskull/specifications.md?plain=1#L21-L38)[core/aibs/README.md 14-33](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/README.md?plain=1#L14-L33)

### Accessories

*   **Warp 100 Bridge**: Enables high-speed internal chip-to-chip communication between Wormhole cards [core/aibs/wormhole/specifications.md 49-51](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L49-L51)
*   **Active Cooling Kit (ACK)**: Required for installing passive "s" model cards in desktop environments without high-velocity server airflow [core/aibs/wormhole/specifications.md 13](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L13-L13)

For detailed information, see [Accessories](https://deepwiki.com/tenstorrent/tenstorrent.github.io/2.2.4-accessories).

## Hardware Topologies and Connectivity

Tenstorrent hardware utilizes high-speed Ethernet-based scaling. Wormhole cards feature two **QSFP-DD** ports supporting 200G connectivity for inter-system or inter-card mesh networking [core/aibs/wormhole/specifications.md 51](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L51-L51)

### Card-to-Card Communication Path

This diagram illustrates the relationship between the physical ports and the internal `Tensix Core` architecture.

Sources: [core/aibs/wormhole/specifications.md 30-31](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L30-L31)[core/aibs/wormhole/specifications.md 43-52](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L43-L52)

## Regulatory Compliance

Tenstorrent hardware complies with international standards including FCC, CE, and RoHS. For full compliance statements, see [Regulatory Compliance](https://deepwiki.com/tenstorrent/tenstorrent.github.io/2.3-regulatory-compliance).

## Related Documentation

*   [Complete Systems](https://deepwiki.com/tenstorrent/tenstorrent.github.io/2.1-complete-systems) — Detailed specifications for QuietBox, LoudBox, T7000, and T1000.
*   [Add-In Boards (AIBs)](https://deepwiki.com/tenstorrent/tenstorrent.github.io/2.2-add-in-boards-(aibs)) — Deep dive into Blackhole, Wormhole, and Grayskull cards.
*   [Software](https://deepwiki.com/tenstorrent/tenstorrent.github.io/3-software) — Documentation for the software stack and drivers (`TT-KMD`, `TT-Metalium`).

Dismiss
Refresh this wiki

Enter email to refresh