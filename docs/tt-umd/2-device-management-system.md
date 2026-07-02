---
project: tt-umd
github: https://github.com/tenstorrent/tt-umd
deepwiki: https://deepwiki.com/tenstorrent/tt-umd/2-device-management-system
---

# Device Management System

Relevant source files
*   [device/api/umd/device/chip/chip.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/chip.hpp)
*   [device/api/umd/device/chip/local_chip.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/local_chip.hpp)
*   [device/api/umd/device/chip/mock_chip.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/mock_chip.hpp)
*   [device/api/umd/device/chip/remote_chip.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/remote_chip.hpp)
*   [device/api/umd/device/cluster.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/cluster.hpp)
*   [device/api/umd/device/simulation/simulation_chip.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/simulation/simulation_chip.hpp)
*   [device/chip/chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp)
*   [device/chip/local_chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp)
*   [device/chip/mock_chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/mock_chip.cpp)
*   [device/chip/remote_chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp)
*   [device/cluster.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp)
*   [device/simulation/simulation_chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/simulation/simulation_chip.cpp)

The Device Management System provides a layered architecture for interacting with Tenstorrent hardware devices. The system abstracts hardware complexity through well-defined layers, enabling applications to work with single or multi-chip configurations without dealing with low-level hardware details.

This page provides an overview of the device management architecture. For detailed information on specific components, see:

*   [Cluster Management](https://deepwiki.com/tenstorrent/tt-umd/2.1-cluster-management) - High-level API for multi-chip orchestration
*   [Cluster Descriptor & Topology](https://deepwiki.com/tenstorrent/tt-umd/2.2-cluster-descriptor-and-topology) - Topology representation and discovery
*   [Chip Abstractions](https://deepwiki.com/tenstorrent/tt-umd/2.3-chip-abstractions) - Chip types and their implementations
*   [TTDevice Interface](https://deepwiki.com/tenstorrent/tt-umd/2.4-ttdevice-interface) - Architecture-specific device operations

## Architecture Layers

The device management system is organized into distinct layers, each with specific responsibilities:

**Layer 1: Application Interface** - The `Cluster` class provides the primary API for applications to interact with single or multiple Tenstorrent devices.

**Layer 2: Device Abstraction** - The `Chip` hierarchy (`LocalChip`, `RemoteChip`, `MockChip`, `SimulationChip`) abstracts different chip connectivity types and provides uniform access patterns.

**Layer 3: Hardware Operations** - The `TTDevice` class implements architecture-specific low-level operations for different chip generations (Wormhole, Blackhole).

**Layer 4: Hardware Communication** - `PCIDevice` handles direct PCIe MMIO and DMA operations, while `RemoteCommunication` manages Ethernet fabric transfers.

**Layer 5: Memory Management** - `TLBManager` and `SysmemManager` optimize memory access patterns and manage address translation.

### Layered Architecture Diagram

Sources:

*   [device/cluster.cpp 5-63](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L5-L63)
*   [device/chip/chip.cpp 5-30](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp#L5-L30)
*   [device/chip/local_chip.cpp 5-39](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L5-L39)
*   [device/chip/remote_chip.cpp 5-26](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L5-L26)
*   [device/api/umd/device/cluster.hpp 129-182](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/cluster.hpp#L129-L182)

## Component Overview

### Cluster

The `Cluster` class ([device/api/umd/device/cluster.hpp 129-182](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/cluster.hpp#L129-L182)) is the primary API for applications. It manages a heterogeneous collection of chips and provides unified operations that work across both local and remote devices.

Key responsibilities:

*   Device discovery and initialization via `create_cluster_descriptor` ([device/api/umd/device/cluster.hpp 163-166](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/cluster.hpp#L163-L166)).
*   Topology management via `ClusterDescriptor`.
*   Routing operations to appropriate chip instances.
*   Multi-chip broadcast operations like `broadcast_write_to_cluster`.

The `Cluster` constructor accepts `ClusterOptions` ([device/api/umd/device/cluster.hpp 76-123](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/cluster.hpp#L76-L123)) which specify the `ChipType` (SILICON, SIMULATION, MOCK, SWEMULE), the target devices, and topology discovery options.

For detailed API documentation, see [Cluster Management](https://deepwiki.com/tenstorrent/tt-umd/2.1-cluster-management).

### ClusterDescriptor

The `ClusterDescriptor` class describes the physical topology of the multi-chip system. It contains information about Ethernet connectivity, physical chip locations, and MMIO capability mapping.

Key methods in `Cluster` like `get_cluster_description()` ([device/api/umd/device/cluster.hpp 175](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/cluster.hpp#L175-L175)) provide access to this topology, which is essential for determining how to route data between chips.

For detailed information, see [Cluster Descriptor & Topology](https://deepwiki.com/tenstorrent/tt-umd/2.2-cluster-descriptor-and-topology).

### Chip Hierarchy

The `Chip` class ([device/api/umd/device/chip/chip.hpp 34-158](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/chip.hpp#L34-L158)) defines the abstract interface for all chip types. It provides common operations like `write_to_device()`, `read_from_device()`, and power state management.

#### Chip Type Overview

| Chip Type | MMIO Capable | Primary Use Case | Communication Path |
| --- | --- | --- | --- |
| **LocalChip** | Yes | PCIe-connected devices | Direct via `TTDevice`&`PCIDevice` |
| **RemoteChip** | No | Ethernet-connected devices | Routed through `RemoteCommunication` |
| **MockChip** | No | Testing without hardware | No-op implementations |
| **SimulationChip** | No | RTL or Functional simulation | `TTDevice` to simulator backend |

#### LocalChip

`LocalChip` ([device/api/umd/device/chip/local_chip.hpp 31-114](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/local_chip.hpp#L31-L114)) represents a chip with direct PCIe access. It owns a `TTDevice` for hardware operations, a `TLBManager` for memory address translation, and a `SysmemManager` for host memory allocation. It also manages inter-process synchronization via `RobustMutex` and `LockManager` ([device/chip/local_chip.cpp 91-109](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L91-L109)).

#### RemoteChip

`RemoteChip` ([device/api/umd/device/chip/remote_chip.hpp 32-90](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/remote_chip.hpp#L32-L90)) represents a chip accessible via Ethernet. It uses `RemoteCommunication` ([device/chip/remote_chip.cpp 38-41](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L38-L41)) to perform I/O. Remote chips do not have direct MMIO capability ([device/chip/remote_chip.cpp 60](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L60-L60)).

For detailed information on chip implementations, see [Chip Abstractions](https://deepwiki.com/tenstorrent/tt-umd/2.3-chip-abstractions).

Sources:

*   [device/api/umd/device/chip/chip.hpp 34-158](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/chip.hpp#L34-L158)
*   [device/api/umd/device/chip/local_chip.hpp 31-114](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/local_chip.hpp#L31-L114)
*   [device/api/umd/device/chip/remote_chip.hpp 32-90](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/remote_chip.hpp#L32-L90)
*   [device/api/umd/device/chip/mock_chip.hpp 21-75](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/mock_chip.hpp#L21-L75)
*   [device/api/umd/device/simulation/simulation_chip.hpp 38-124](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/simulation/simulation_chip.hpp#L38-L124)

### TTDevice

The `TTDevice` class provides architecture-specific hardware operations. It is the bridge between the high-level `Chip` abstraction and the low-level `PCIDevice` or `RemoteCommunication` protocols.

`TTDevice` instances are created via factory methods and held by `Chip` instances ([device/chip/local_chip.cpp 60-61](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L60-L61)). They handle architecture-specific details like coordinate translation, NoC access, and ARC messenger interactions.

For detailed information, see [TTDevice Interface](https://deepwiki.com/tenstorrent/tt-umd/2.4-ttdevice-interface).

Sources:

*   [device/chip/local_chip.cpp 44-62](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L44-L62)
*   [device/chip/remote_chip.cpp 31-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L31-L50)

## Communication Flow Diagram

The following diagram illustrates how a write request from the application travels through the system layers to the hardware.

Sources:

*   [device/api/umd/device/chip/local_chip.hpp 55](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/local_chip.hpp#L55-L55)
*   [device/chip/local_chip.cpp 131-135](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L131-L135)

## Initialization Flow

The system initializes in two main phases:

1.   **Cluster Construction**: The `Cluster` constructor ([device/cluster.cpp 342-434](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L342-L434)) performs topology discovery and instantiates the required `Chip` objects based on the discovered architecture and user options.
2.   **Device Startup**: The `start_device()` call ([device/chip/local_chip.cpp 139-155](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L139-L155)) performs hardware-level setup, including pinning system memory, initializing PCIe iATUs, and setting up memory barriers.

Sources:

*   [device/cluster.cpp 342-434](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L342-L434)
*   [device/chip/local_chip.cpp 139-155](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L139-L155)
*   [device/chip/chip.cpp 63-67](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp#L63-L67)

Dismiss
Refresh this wiki

Enter email to refresh