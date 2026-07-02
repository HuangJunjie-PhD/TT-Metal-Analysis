---
project: tt-umd
github: https://github.com/tenstorrent/tt-umd
deepwiki: https://deepwiki.com/tenstorrent/tt-umd/1-overview
---

# Overview

Relevant source files
*   [.github/manylinux-aarch64.Dockerfile](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.github/manylinux-aarch64.Dockerfile)
*   [.github/ubuntu-24.04-arm.Dockerfile](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.github/ubuntu-24.04-arm.Dockerfile)
*   [.github/workflows/build-device.yml](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.github/workflows/build-device.yml)
*   [.github/workflows/docker-image.yml](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.github/workflows/docker-image.yml)
*   [.github/workflows/release.yml](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.github/workflows/release.yml)
*   [CHANGELOG](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CHANGELOG)
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt)
*   [README.emu.md](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/README.emu.md?plain=1)
*   [README.md](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/README.md?plain=1)
*   [VERSION](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/VERSION)
*   [cmake/example_client.cmake](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/cmake/example_client.cmake)
*   [device/CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt)
*   [device/api/umd/device/types/arch.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/types/arch.hpp)
*   [device/chip_helpers/silicon_sysmem_manager.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip_helpers/silicon_sysmem_manager.cpp)
*   [device/hugepage.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/hugepage.cpp)
*   [device/hugepage.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/hugepage.hpp)
*   [docs/COORDINATE_SYSTEMS.md](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/docs/COORDINATE_SYSTEMS.md?plain=1)
*   [emulation_rocky8.def](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/emulation_rocky8.def)
*   [tests/CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/CMakeLists.txt)
*   [third_party/CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/third_party/CMakeLists.txt)

## Purpose and Scope

**tt-umd** (Tenstorrent User Mode Driver) is the foundational software hardware abstraction layer (HAL) for interacting with Tenstorrent hardware accelerators. It provides a unified C++ API for managing single-chip and multi-chip systems, abstracting hardware complexity behind a Cluster-centric interface. The driver handles device discovery, memory management, inter-chip communication, and low-level hardware access across **Wormhole**, **Blackhole**, and **Grayskull** architectures.

This document provides an architectural overview of tt-umd, introducing its layered design, key abstractions, and supported hardware configurations. For detailed information on specific subsystems, see:

*   Device management and Cluster API: [Device Management System](https://deepwiki.com/tenstorrent/tt-umd/2-device-management-system)
*   Topology discovery and multi-chip systems: [Cluster Descriptor & Topology](https://deepwiki.com/tenstorrent/tt-umd/2.2-cluster-descriptor-and-topology)
*   Hardware communication mechanisms: [Hardware Interface Layer](https://deepwiki.com/tenstorrent/tt-umd/3-hardware-interface-layer)
*   Architecture-specific features: [Architecture-Specific Features](https://deepwiki.com/tenstorrent/tt-umd/6-architecture-specific-features)
*   Build system and development: [Build System & Development](https://deepwiki.com/tenstorrent/tt-umd/7-build-system-and-development)
*   Simulation capabilities: [Device Simulation](https://deepwiki.com/tenstorrent/tt-umd/8-device-simulation)

Sources: [device/cluster.cpp 1-69](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L1-L69)[CMakeLists.txt 23-31](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L23-L31)[device/tt_device/tt_device.hpp 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/tt_device.hpp#L1-L50)

* * *

## Architecture Philosophy

tt-umd employs a **Cluster-centric design** where the `tt::umd::Cluster` class serves as the single entry point for all device operations. Applications interact with chips through this unified API regardless of whether chips are locally attached via PCIe, remotely connected via Ethernet, or accessed through JTAG for debugging.

The core abstraction separates `LocalChip` (PCIe/JTAG-attached) from `RemoteChip` (Ethernet-routed through a gateway) implementations, allowing the `Cluster` to transparently route operations based on topology. This design enables treating heterogeneous multi-chip systems—from single development boards (N150, P150) to 36-chip Galaxy systems—as a single logical device.

**Key Design Principles:**

*   **Unified API:** Same interface for local, remote, and simulated chips.
*   **Topology-aware routing:** Automatic path selection based on chip location and Ethernet connectivity.
*   **Architecture abstraction:** Common interface across Wormhole and Blackhole via `TTDevice` implementations.
*   **Discovery-driven initialization:** Hardware topology is detected at runtime using `TopologyDiscovery`.

Sources: [device/cluster.cpp 364-456](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L364-L456)[device/chip/local_chip.cpp 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L1-L50)[device/chip/remote_chip.cpp 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L1-L50)[device/topology/topology_discovery.cpp 1-100](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery.cpp#L1-L100)

* * *

## System Layering

tt-umd implements a layered architecture, with each layer providing progressively lower-level access to hardware:

### Layered Architecture Diagram

Title: tt-umd Architectural Layers

Sources: [device/CMakeLists.txt 64-138](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L64-L138)[device/cluster.cpp 232-275](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L232-L275)[device/tt_device/tt_device.hpp 1-100](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/tt_device.hpp#L1-L100)

* * *

## Core Components

### Cluster Management

The `tt::umd::Cluster` class is the primary interface for all device operations. It manages device lifecycle, routes memory operations, and provides synchronization primitives.

**Operational Data Flow:** Title: Cluster Operation Routing

Sources: [device/cluster.cpp 82-138](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L82-L138)[device/cluster.cpp 557-580](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L557-L580)

### ClusterDescriptor

The `ClusterDescriptor` holds authoritative topology information discovered during initialization or loaded from a YAML file. It maps logical chip IDs to physical locations and Ethernet connections.

**Key Metadata:**

*   `chip_unique_ids`: Maps `ChipId` to ASIC unique identifiers.
*   `ethernet_connections`: Bidirectional Ethernet link graph.
*   `chips_with_mmio`: Maps logical chip IDs to PCIe device indices.
*   `harvesting_masks`: Core harvesting status per chip.

Sources: [device/cluster_descriptor.cpp 1-100](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster_descriptor.cpp#L1-L100)[device/soc_descriptor.cpp 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/soc_descriptor.cpp#L1-L50)

### Memory Management

tt-umd utilizes a sophisticated memory management system to optimize data transfers:

*   **TLBManager:** Manages Translation Lookaside Buffer windows in PCIe BAR space.
*   **SysmemManager:** Handles host memory allocation, supporting both **Hugepages** (1GB/2MB) and IOMMU-mapped memory.
*   **DMA:** Supports high-speed bulk transfers (specifically on Wormhole architecture).

Sources: [device/chip_helpers/sysmem_manager.cpp 1-100](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip_helpers/sysmem_manager.cpp#L1-L100)[device/chip_helpers/tlb_manager.cpp 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip_helpers/tlb_manager.cpp#L1-L50)[device/hugepage.cpp 31-48](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/hugepage.cpp#L31-L48)

* * *

## Supported Hardware and Topologies

| Architecture | Identifier | Board Types | Max ETH Links |
| --- | --- | --- | --- |
| **Wormhole** | `tt::ARCH::WORMHOLE_B0` | N150, N300, T3000, Galaxy 4U | 16 |
| **Blackhole** | `tt::ARCH::BLACKHOLE` | P100, P150, P300, Galaxy 6U | 14 |
| **Grayskull** | `tt::ARCH::GRAYSKULL` | E150, E300 | 0 |

Sources: [device/api/umd/device/types/arch.hpp 1-30](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/types/arch.hpp#L1-L30)[CHANGELOG 3-15](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CHANGELOG#L3-L15)

* * *

## Build System and Components

The repository uses a CMake-based build system with modular feature flags to control binary size and dependencies.

| Feature Flag | Description |
| --- | --- |
| `TT_UMD_BUILD_PYTHON` | Generates `tt_umd` Python package using nanobind. |
| `TT_UMD_BUILD_SIMULATION` | Enables RTL simulation support via FlatBuffers and NNG. |
| `TT_UMD_BUILD_TOOLS` | Builds diagnostic tools like `telemetry` and `topology`. |
| `TT_UMD_BUILD_TESTS` | Builds GTest-based unit and integration tests. |

Sources: [CMakeLists.txt 55-63](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L55-L63)[device/CMakeLists.txt 1-40](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L1-L40)

* * *

## Summary

tt-umd provides the critical bridge between high-level Tenstorrent software stacks and physical silicon. By abstracting the complexities of PCIe, Ethernet fabric, and architecture-specific register maps, it allows for scalable software development across diverse Tenstorrent hardware configurations.

Sources: [README.md 1-100](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/README.md?plain=1#L1-L100)[CHANGELOG 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CHANGELOG#L1-L50)

Dismiss
Refresh this wiki

Enter email to refresh