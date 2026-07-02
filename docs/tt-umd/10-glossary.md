---
project: tt-umd
github: https://github.com/tenstorrent/tt-umd
deepwiki: https://deepwiki.com/tenstorrent/tt-umd/10-glossary
---

# Glossary

Relevant source files
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt)
*   [README.emu.md](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/README.emu.md?plain=1)
*   [cmake/example_client.cmake](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/cmake/example_client.cmake)
*   [device/CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt)
*   [device/api/umd/device/firmware/erisc_firmware.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/firmware/erisc_firmware.hpp)
*   [device/api/umd/device/firmware/firmware_utils.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/firmware/firmware_utils.hpp)
*   [device/api/umd/device/soc_descriptor.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/soc_descriptor.hpp)
*   [device/api/umd/device/topology/topology_discovery.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/topology/topology_discovery.hpp)
*   [device/api/umd/device/topology/topology_discovery_blackhole.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/topology/topology_discovery_blackhole.hpp)
*   [device/api/umd/device/topology/topology_discovery_options.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/topology/topology_discovery_options.hpp)
*   [device/api/umd/device/topology/topology_discovery_wormhole.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/topology/topology_discovery_wormhole.hpp)
*   [device/api/umd/device/tt_device/blackhole_tt_device.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/blackhole_tt_device.hpp)
*   [device/api/umd/device/tt_device/tt_device.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/tt_device.hpp)
*   [device/api/umd/device/tt_device/wormhole_tt_device.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/wormhole_tt_device.hpp)
*   [device/api/umd/device/types/arch.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/types/arch.hpp)
*   [device/api/umd/device/types/cluster_descriptor_types.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/types/cluster_descriptor_types.hpp)
*   [device/api/umd/device/types/cluster_types.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/types/cluster_types.hpp)
*   [device/api/umd/device/types/core_coordinates.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/types/core_coordinates.hpp)
*   [device/api/umd/device/utils/semver.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/utils/semver.hpp)
*   [device/chip/chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp)
*   [device/chip/local_chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/local_chip.cpp)
*   [device/chip/remote_chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/remote_chip.cpp)
*   [device/cluster.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp)
*   [device/coordinates/coordinate_manager.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/coordinates/coordinate_manager.cpp)
*   [device/firmware/firmware_utils.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/firmware/firmware_utils.cpp)
*   [device/soc_descriptor.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/soc_descriptor.cpp)
*   [device/topology/topology_discovery.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery.cpp)
*   [device/topology/topology_discovery_blackhole.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery_blackhole.cpp)
*   [device/topology/topology_discovery_wormhole.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery_wormhole.cpp)
*   [device/tt_device/blackhole_tt_device.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/blackhole_tt_device.cpp)
*   [device/tt_device/tt_device.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/tt_device.cpp)
*   [device/tt_device/wormhole_tt_device.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/wormhole_tt_device.cpp)
*   [docs/COORDINATE_SYSTEMS.md](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/docs/COORDINATE_SYSTEMS.md?plain=1)
*   [docs/yaml_schemas/cluster_descriptor.yaml](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/docs/yaml_schemas/cluster_descriptor.yaml)
*   [emulation_rocky8.def](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/emulation_rocky8.def)
*   [nanobind/py_api_soc_descriptor.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_soc_descriptor.cpp)
*   [nanobind/py_api_topology_discovery.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_topology_discovery.cpp)
*   [tests/CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/CMakeLists.txt)
*   [tests/api/test_noc.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/api/test_noc.cpp)
*   [tests/baremetal/test_soc_descriptor.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/baremetal/test_soc_descriptor.cpp)
*   [tests/cluster_descriptor_examples/quasar_Q1.yaml](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/cluster_descriptor_examples/quasar_Q1.yaml)
*   [tests/misc/test_semver.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/misc/test_semver.cpp)
*   [third_party/CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/third_party/CMakeLists.txt)

This glossary defines technical terms, hardware components, and software abstractions used within the `tt-umd` (User Mode Driver) codebase. It serves as a reference for engineers onboarding to the repository to understand the relationship between physical hardware entities and their representation in code.

## Hardware Components

| Term | Definition |
| --- | --- |
| **ARC** | Argonaut RISC Core. A management processor on the chip responsible for power management, thermal monitoring, and initial boot sequences. Communication with ARC is handled via `ArcMessenger`[device/api/umd/device/tt_device/tt_device.hpp 19](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/tt_device.hpp#L19-L19) |
| **NOC** | Network on Chip. The high-bandwidth interconnect fabric that allows cores (Tensix, DRAM, ETH) to communicate. Chips typically have two NOCs: `NOC0` and `NOC1`[device/api/umd/device/tt_device/tt_device.hpp 37](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/tt_device.hpp#L37-L37) |
| **TLB** | Translation Lookaside Buffer. In the context of UMD, these are hardware registers in the PCIe interface used to map Host MMIO addresses to specific NOC coordinates and addresses on the chip [device/api/umd/device/chip/local_chip.cpp 26](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/local_chip.cpp#L26-L26) |
| **BAR** | Base Address Register. PCIe configuration space registers that define the address ranges the chip responds to on the PCIe bus. UMD uses `BAR0` for register access and `BAR2` for large memory apertures (Blackhole) [device/tt_device/blackhole_tt_device.cpp 63-69](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/blackhole_tt_device.cpp#L63-L69) |
| **iATU** | internal Address Translation Unit. A hardware component in the PCIe controller used to map outbound PCIe transactions to internal chip addresses or to map inbound NOC transactions to Host memory [device/tt_device/wormhole_tt_device.cpp 115-135](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/wormhole_tt_device.cpp#L115-L135) |
| **Tensix** | The primary computational cores on Tenstorrent AI processors, consisting of a vector engine, scalar engine, and local L1 memory [device/chip/chip.cpp 132](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp#L132-L132) |
| **ETH** | Ethernet Cores. Specialized cores used for chip-to-chip communication over an Ethernet fabric. Managed via `erisc` (Ethernet RISC) firmware [device/topology/topology_discovery.cpp 26](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery.cpp#L26-L26) |

**Sources:**

*   [device/api/umd/device/tt_device/tt_device.hpp 19-37](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/tt_device.hpp#L19-L37)
*   [device/tt_device/blackhole_tt_device.cpp 63-69](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/blackhole_tt_device.cpp#L63-L69)
*   [device/tt_device/wormhole_tt_device.cpp 115-135](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/wormhole_tt_device.cpp#L115-L135)
*   [device/chip/chip.cpp 132](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp#L132-L132)

* * *

## Software Abstractions

### System Entities

*   **Cluster**: The top-level object representing a collection of interconnected Tenstorrent chips. It manages the lifecycle of all chips and provides the primary API for I/O [device/cluster.cpp 49-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L49-L50)
*   **ChipId**: A logical identifier (usually 0 to N-1) assigned to each chip in a cluster [device/cluster.cpp 86](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L86-L86)
*   **TTDevice**: The hardware abstraction layer for a single physical chip. It handles the specific communication protocol (PCIe, JTAG, or Remote) [device/api/umd/device/tt_device/tt_device.hpp 68-76](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/tt_device.hpp#L68-L76)
*   **SocDescriptor**: A structure defining the static layout of a specific chip architecture, including core counts, core types, and their physical coordinates [device/api/umd/device/soc_descriptor.hpp 27](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/soc_descriptor.hpp#L27-L27)

### Coordinate Systems

Coordinates are represented by the `CoreCoord` struct [device/api/umd/device/types/core_coordinates.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/types/core_coordinates.hpp)

*   **LOGICAL**: A simplified, zero-indexed coordinate system used by software that ignores hardware "harvesting" (disabled cores) [device/topology/topology_discovery_blackhole.cpp 164-165](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery_blackhole.cpp#L164-L165)
*   **NOC0 / NOC1**: The physical hardware coordinates used to route packets on the respective Network-on-Chip [device/topology/topology_discovery_blackhole.cpp 162-163](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery_blackhole.cpp#L162-L163)
*   **TRANSLATED**: Coordinates that have been adjusted for hardware offsets and harvesting to ensure software-to-hardware mapping is valid [device/chip/chip.cpp 75](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp#L75-L75)

### Diagram: Entity Mapping

This diagram shows how natural language hardware concepts map to specific C++ classes and file structures.

**Hardware to Code Mapping**

**Sources:**

*   [device/api/umd/device/tt_device/tt_device.hpp 68-82](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/tt_device.hpp#L68-L82)
*   [device/api/umd/device/soc_descriptor.hpp 27](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/soc_descriptor.hpp#L27-L27)
*   [device/cluster_descriptor.cpp 92](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster_descriptor.cpp#L92-L92)

* * *

## Architectures and Boards

### Architectures

*   **Wormhole (WH)**: Second-generation architecture featuring GDDR6 and integrated 100Gbps Ethernet for scaling. Represented by `WormholeTTDevice`[device/tt_device/wormhole_tt_device.cpp 43-44](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/wormhole_tt_device.cpp#L43-L44)
*   **Blackhole (BH)**: Third-generation architecture with LPDDR5, improved NOC, and enhanced management features. Represented by `BlackholeTTDevice`[device/tt_device/blackhole_tt_device.cpp 41-42](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/blackhole_tt_device.cpp#L41-L42)
*   **Quasar / Grendel**: Future architectures or simulation targets often found in testing and descriptor files [device/CMakeLists.txt 87](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L87-L87)

### Board Types

Board types are used during `TopologyDiscovery` to determine how chips are connected [device/topology/topology_discovery_wormhole.cpp 165](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery_wormhole.cpp#L165-L165)

*   **N150**: A single-Wormhole PCIe card.
*   **N300**: A dual-Wormhole PCIe card (two chips connected via local Ethernet).
*   **P150**: A Blackhole-based PCIe card [device/topology/topology_discovery_blackhole.cpp 137](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery_blackhole.cpp#L137-L137)
*   **UBB (Universal Baseboard)**: A board containing multiple chips, typically found in Galaxy systems [device/topology/topology_discovery_wormhole.cpp 165](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery_wormhole.cpp#L165-L165)
*   **Galaxy**: A large-scale system consisting of multiple UBBs interconnected via Ethernet backplanes.

**Sources:**

*   [device/tt_device/wormhole_tt_device.cpp 43-44](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/wormhole_tt_device.cpp#L43-L44)
*   [device/tt_device/blackhole_tt_device.cpp 41-42](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/blackhole_tt_device.cpp#L41-L42)
*   [device/topology/topology_discovery_wormhole.cpp 165](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery_wormhole.cpp#L165-L165)
*   [device/topology/topology_discovery_blackhole.cpp 137](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery_blackhole.cpp#L137-L137)

* * *

## Operational Concepts

| Concept | Definition |
| --- | --- |
| **Harvesting** | The process of disabling faulty cores (Tensix, DRAM, or ETH) during manufacturing. UMD must read "harvesting masks" from ARC to know which cores are available [device/tt_device/wormhole_tt_device.cpp 94-95](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/wormhole_tt_device.cpp#L94-L95) |
| **Hugepages** | Large memory pages (typically 1GB or 2MB) allocated by the Linux kernel. UMD uses these for DMA buffers to ensure contiguous physical memory and reduce TLB misses [device/cluster.cpp 30](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L30-L30) |
| **Warm Reset** | A procedure to reset the chip's internal state without power-cycling the host. Managed via the `WarmReset` utility [device/CMakeLists.txt 123](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L123-L123) |
| **AICLK** | AI Clock. The frequency at which the Tensix cores are operating. UMD can query this via ARC messages [device/tt_device/wormhole_tt_device.cpp 100-111](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/wormhole_tt_device.cpp#L100-L111) |
| **Eth Training** | The process where Ethernet firmware (`erisc`) establishes a stable link with a neighboring chip. UMD must wait for this to complete before remote I/O [device/chip/chip.cpp 69-78](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/chip.cpp#L69-L78) |

### Diagram: Initialization Data Flow

This diagram traces the flow of data from physical discovery to the software objects.

**Discovery to Cluster Flow**

**Sources:**

*   [device/topology/topology_discovery.cpp 117-136](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery.cpp#L117-L136)
*   [device/topology/topology_discovery.cpp 159-160](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery.cpp#L159-L160)
*   [device/cluster.cpp 149-155](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L149-L155)
*   [device/cluster.cpp 186-193](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/cluster.cpp#L186-L193)

Dismiss
Refresh this wiki

Enter email to refresh