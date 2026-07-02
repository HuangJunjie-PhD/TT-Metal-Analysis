---
project: tt-umd
github: https://github.com/tenstorrent/tt-umd
deepwiki: https://deepwiki.com/tenstorrent/tt-umd/4-multi-device-communication
---

# Multi-Device Communication

Relevant source files
*   [device/chip/chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp)
*   [device/chip/local_chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp)
*   [device/chip/remote_chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp)
*   [device/cluster.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp)
*   [device/tt_device/remote_communication.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/remote_communication.cpp)

## Overview

Multi-device communication in `tt-umd` enables coordination between multiple Tenstorrent chips in a cluster. The system provides a unified `Cluster` API that abstracts the physical topology, allowing applications to interact with both PCIe-connected chips and Ethernet-connected chips through the same interface.

The communication architecture is built on two fundamental chip types:

*   **LocalChip**: Directly accessible via PCIe, providing full MMIO, DMA, and TLB management. [device/chip/local_chip.cpp 5-62](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L5-L62)
*   **RemoteChip**: Accessed indirectly through Ethernet links via a gateway LocalChip. [device/chip/remote_chip.cpp 5-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L5-L50)

This page provides an overview of the multi-device communication architecture. For implementation details:

*   See [Remote Communication & Ethernet](https://deepwiki.com/tenstorrent/tt-umd/4.1-remote-communication-and-ethernet) for ethernet protocol specifics and `RemoteCommunication` implementation.
*   See [Ethernet Interface Protocol](https://deepwiki.com/tenstorrent/tt-umd/4.2-ethernet-interface-protocol) for low-level command structures and firmware interaction.

* * *

## Chip Abstraction: LocalChip vs RemoteChip

The UMD implements a chip abstraction layer that distinguishes between directly accessible chips and those requiring ethernet routing:

| Chip Type | Implementation | Communication Path | Key Capabilities |
| --- | --- | --- | --- |
| **LocalChip** | [device/chip/local_chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp) | Host → PCIe → Device | `write_to_device()`, `dma_write_to_device()`, `TLBManager`, `SysmemManager` |
| **RemoteChip** | [device/chip/remote_chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp) | Host → Gateway LocalChip → Ethernet → Device | `write_to_device()`, `read_from_device()`, `wait_for_non_mmio_flush()` |

Both chip types implement the `Chip` base interface, allowing the `Cluster` class to treat them uniformly. However, their underlying implementations differ significantly based on their physical connectivity. [device/chip/chip.cpp 33-40](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp#L33-L40)

Sources: [device/chip/local_chip.cpp 44-62](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L44-L62)[device/chip/remote_chip.cpp 31-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L31-L50)[device/chip/chip.cpp 33-40](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp#L33-L40)

* * *

## Gateway Pattern for Remote Access

Remote chips lack direct PCIe connectivity to the host. Instead, all communication is routed through a **gateway LocalChip** that serves as an ethernet proxy. The `Cluster` class manages this relationship during construction.

**Diagram: Gateway Pattern Architecture**

The gateway pattern is established during cluster construction. When `Cluster::construct_chip_from_cluster()` creates a `RemoteChip`, it identifies the gateway and passes the gateway's `LocalChip*` pointer to the `RemoteChip` creator. [device/chip/remote_chip.cpp 31-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L31-L50)

### Chip Classification During Cluster Construction

During cluster initialization, `Cluster` classifies chips based on MMIO capability:

**Diagram: Chip Classification Logic**

The classification drives routing decisions throughout the cluster's lifetime. For instance, `use_ethernet_broadcast` is enabled for Wormhole systems when remote chips are present and host memory is available. [device/cluster.cpp 182-184](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L182-L184)

Sources: [device/cluster.cpp 149-184](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L149-L184)[device/chip/remote_chip.cpp 31-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L31-L50)

* * *

## Communication Pathways

The `Cluster` API provides a uniform interface for accessing both local and remote chips, routing operations through different pathways based on chip type.

### LocalChip: Direct Hardware Access

`LocalChip::write_to_device()` (inherited or implemented via `TTDevice`) uses PCIe memory-mapped I/O with TLB management. It initializes mutexes for `REMOTE_ARC_MSG` and `MEM_BARRIER` to ensure thread safety during multi-chip operations. [device/chip/local_chip.cpp 91-109](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L91-L109)

### RemoteChip: Ethernet Routing

`RemoteChip::write_to_device()` delegates to its internal `TTDevice`, which uses a `RemoteCommunication` instance. [device/chip/remote_chip.cpp 76-78](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L76-L78)

*   **Legacy Support**: On Wormhole, this typically uses `RemoteCommunicationLegacyFirmware`. [device/tt_device/remote_communication.cpp 28-30](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/remote_communication.cpp#L28-L30)
*   **Load Balancing**: UMD can cycle through multiple ethernet cores for host-to-cluster transfers via `update_active_eth_core_idx()`. [device/tt_device/remote_communication.cpp 63-69](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/remote_communication.cpp#L63-L69)

Sources: [device/chip/local_chip.cpp 91-109](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L91-L109)[device/chip/remote_chip.cpp 76-78](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L76-L78)[device/tt_device/remote_communication.cpp 26-37](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/remote_communication.cpp#L26-L37)

* * *

## Write Ordering and Synchronization

Remote chip operations are often asynchronous or buffered. To ensure write completion, applications must explicitly flush.

### Flush and Memory Barriers

For `RemoteChip`, `wait_for_non_mmio_flush()` is essential to ensure that buffered ethernet commands have reached their destination. [device/chip/remote_chip.cpp 104](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L104-L104)

*   **L1/DRAM Barriers**: Remote chips implement `l1_membar` and `dram_membar` by calling `wait_for_non_mmio_flush()`. [device/chip/remote_chip.cpp 106-112](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L106-L112)
*   **Local Synchronization**: `LocalChip` uses physical memory barriers and resets `MemBarFlag` on Tensix/Eth/DRAM cores during initialization. [device/chip/local_chip.cpp 111-129](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L111-L129)

**Diagram: Remote Write Synchronization**

Sources: [device/chip/remote_chip.cpp 104-112](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp#L104-L112)[device/chip/local_chip.cpp 111-129](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp#L111-L129)[device/tt_device/remote_communication.cpp 21-24](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/remote_communication.cpp#L21-L24)

* * *

## Multi-Chip Coordination Features

### Ethernet Core Management

UMD allows configuring specific ethernet cores for host-to-cluster transfers. This is handled via `set_remote_transfer_ethernet_cores()`, which informs the driver which active links can be used for non-MMIO transfers. [device/tt_device/remote_communication.cpp 39-46](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/remote_communication.cpp#L39-L46)

### Training and Readiness

Multi-chip systems require synchronized readiness. The `Chip::wait_chip_to_be_ready()` method ensures both Ethernet and DRAM cores have finished training before allowing communication. [device/chip/chip.cpp 63-67](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp#L63-L67)

*   **Eth Training**: `wait_eth_cores_training()` polls the training status of all Ethernet cores in the SoC. [device/chip/chip.cpp 69-78](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp#L69-L78)
*   **DRAM Training**: `wait_dram_cores_training()` checks status for all non-harvested DRAM channels. [device/chip/chip.cpp 80-94](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp#L80-L94)

Sources: [device/tt_device/remote_communication.cpp 39-46](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/remote_communication.cpp#L39-L46)[device/chip/chip.cpp 63-94](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp#L63-L94)

* * *

## Child Pages

For deep dives into specific communication mechanisms, refer to the following:

*   **[Remote Communication & Ethernet](https://deepwiki.com/tenstorrent/tt-umd/4.1-remote-communication-and-ethernet)**: Details on the `RemoteCommunication` class hierarchy, legacy firmware support, and specific `RemoteChip` implementation details.
*   **[Ethernet Interface Protocol](https://deepwiki.com/tenstorrent/tt-umd/4.2-ethernet-interface-protocol)**: Details on the low-level command structures (`routing_cmd_t`), ethernet core memory maps, and the handshake protocol with `erisc` firmware. [device/cluster.cpp 68-79](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L68-L79)

Sources: [device/cluster.cpp 68-79](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L68-L79)[device/tt_device/remote_communication.cpp 26-37](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/remote_communication.cpp#L26-L37)

Dismiss
Refresh this wiki

Enter email to refresh