---
project: tt-umd
github: https://github.com/tenstorrent/tt-umd
deepwiki: https://deepwiki.com/tenstorrent/tt-umd/5-coordinate-systems-and-soc-descriptors
---

# Coordinate Systems & SOC Descriptors

Relevant source files
*   [device/api/umd/device/coordinates/blackhole_coordinate_manager.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/coordinates/blackhole_coordinate_manager.hpp)
*   [device/api/umd/device/coordinates/coordinate_manager.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/coordinates/coordinate_manager.hpp)
*   [device/api/umd/device/coordinates/wormhole_coordinate_manager.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/coordinates/wormhole_coordinate_manager.hpp)
*   [device/api/umd/device/soc_descriptor.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/soc_descriptor.hpp)
*   [device/api/umd/device/types/core_coordinates.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/types/core_coordinates.hpp)
*   [device/coordinates/blackhole_coordinate_manager.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/coordinates/blackhole_coordinate_manager.cpp)
*   [device/coordinates/coordinate_manager.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/coordinates/coordinate_manager.cpp)
*   [device/coordinates/wormhole_coordinate_manager.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/coordinates/wormhole_coordinate_manager.cpp)
*   [device/soc_descriptor.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/soc_descriptor.cpp)
*   [docs/yaml_schemas/soc_descriptor.yaml](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/docs/yaml_schemas/soc_descriptor.yaml)
*   [nanobind/py_api_soc_descriptor.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_soc_descriptor.cpp)
*   [tests/api/test_noc.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/api/test_noc.cpp)
*   [tests/baremetal/test_core_coord_translation_bh.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/baremetal/test_core_coord_translation_bh.cpp)
*   [tests/baremetal/test_core_coord_translation_wh.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/baremetal/test_core_coord_translation_wh.cpp)
*   [tests/baremetal/test_soc_descriptor.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/baremetal/test_soc_descriptor.cpp)

## Purpose and Scope

This document describes the coordinate addressing systems used to identify cores on Tenstorrent chips and the SOC descriptor structures that define chip topology. Understanding these systems is essential for any operations that target specific cores, translate between coordinate spaces, or query chip layout information.

The UMD uses a multi-layered coordinate approach to abstract physical hardware defects (harvesting) and provide a consistent interface for application developers.

For details on the implementation of these systems, see the following child pages:

*   [Coordinate Translations](https://deepwiki.com/tenstorrent/tt-umd/5.1-coordinate-translations) — Detailed documentation on `LOGICAL`, `TRANSLATED`, and `NOC0` systems, and the `CoordinateManager` hierarchy.
*   [SOC Descriptor](https://deepwiki.com/tenstorrent/tt-umd/5.2-soc-descriptor) — Documentation on the `SocDescriptor` class, YAML schema, and how chip topology is represented.

## Coordinate Systems Overview

The UMD supports multiple coordinate systems to address cores. Different systems serve different purposes in the hardware abstraction:

**Diagram: Coordinate System Hierarchy**

Sources: [device/api/umd/device/types/core_coordinates.hpp 47-54](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/types/core_coordinates.hpp#L47-L54)[device/api/umd/device/soc_descriptor.hpp 49-58](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/soc_descriptor.hpp#L49-L58)

### Coordinate System Types

The codebase defines coordinate systems through the `CoordSystem` enumeration:

| Coordinate System | Purpose | Usage Context |
| --- | --- | --- |
| **LOGICAL** | User-facing virtual coordinate space | Application-level addressing, ignores harvested cores |
| **TRANSLATED** | Post-harvesting physical grid | Used for device access when NOC translation is enabled |
| **NOC0** | Network-on-Chip 0 routing | Raw physical hardware coordinates |
| **NOC1** | Network-on-Chip 1 routing | Alternative hardware routing (often a rotation/flip of NOC0) |

Sources: [device/api/umd/device/types/core_coordinates.hpp 47-54](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/types/core_coordinates.hpp#L47-L54)[device/coordinates/coordinate_manager.cpp 145-179](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/coordinates/coordinate_manager.cpp#L145-L179)

## CoreCoord Structure

The `CoreCoord` struct (defined in `tt::umd` namespace) represents a core location with associated metadata:

**Diagram: CoreCoord and Associated Enums**

Sources: [device/api/umd/device/types/core_coordinates.hpp 140-190](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/types/core_coordinates.hpp#L140-L190)[nanobind/py_api_soc_descriptor.cpp 29-78](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_soc_descriptor.cpp#L29-L78)

## SOC Descriptor Structure

The `SocDescriptor` class is the central data structure defining chip topology. It is initialized from a static `SocArchDescriptor` (loaded from YAML) and runtime `ChipInfo` (containing harvesting masks).

**Diagram: SocDescriptor Initialization Flow**

Sources: [device/soc_descriptor.cpp 183-189](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/soc_descriptor.cpp#L183-L189)[device/soc_descriptor.cpp 106-126](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/soc_descriptor.cpp#L106-L126)[device/api/umd/device/soc_descriptor.hpp 37-39](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/soc_descriptor.hpp#L37-L39)

### Key Responsibilities

*   **Core Enumeration**: Provides lists of cores filtered by type (e.g., `get_cores(CoreType::TENSIX)`).
*   **Harvesting Management**: Tracks which cores are disabled via `HarvestingMasks`.
*   **Coordinate Translation**: Proxies translation requests to the internal `CoordinateManager`.
*   **Serialization**: Can export the current chip state (including harvesting) back to a YAML format.

Sources: [device/api/umd/device/soc_descriptor.hpp 70-78](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/soc_descriptor.hpp#L70-L78)[device/soc_descriptor.cpp 59-66](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/soc_descriptor.cpp#L59-L66)[device/soc_descriptor.cpp 128-181](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/soc_descriptor.cpp#L128-L181)

## Harvesting and Remapping

Harvesting is the process of disabling defective cores (Tensix rows/columns, DRAM channels, or Ethernet links). The `CoordinateManager` handles this by shifting `LOGICAL` coordinates to skip harvested `NOC0` locations.

### Impact on Coordinates

1.   **NOC0**: Remains constant (physical location on silicon).
2.   **LOGICAL**: Shrinks. If a chip has 10 rows and 1 is harvested, the LOGICAL grid becomes 9 rows high.
3.   **TRANSLATED**: In architectures like Blackhole, the hardware NOC translation logic remaps coordinates so that software can address a contiguous block, even if physical cores are missing.

Sources: [device/coordinates/blackhole_coordinate_manager.cpp 75-101](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/coordinates/blackhole_coordinate_manager.cpp#L75-L101)[device/coordinates/wormhole_coordinate_manager.cpp 56-87](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/coordinates/wormhole_coordinate_manager.cpp#L56-L87)[device/api/umd/device/soc_descriptor.hpp 123-131](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/soc_descriptor.hpp#L123-L131)

## Coordinate Usage in APIs

When using the `Cluster` or `TTDevice` APIs, coordinates are typically passed as `CoreCoord` objects. The driver uses the `SocDescriptor` to ensure the correct physical address is targeted.

`// Example: Translating a logical coordinate to NOC0 for low-level register accessCoreCoord logical_core(0, 0, CoreType::TENSIX, CoordSystem::LOGICAL);CoreCoord noc0_core = soc_desc.translate_coord_to(logical_core, CoordSystem::NOC0);`
Sources: [tests/api/test_noc.cpp 68-69](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/api/test_noc.cpp#L68-L69)[tests/baremetal/test_soc_descriptor.cpp 114-115](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/baremetal/test_soc_descriptor.cpp#L114-L115)[device/api/umd/device/soc_descriptor.hpp 49-58](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/soc_descriptor.hpp#L49-L58)

For more details, see [Coordinate Translations](https://deepwiki.com/tenstorrent/tt-umd/5.1-coordinate-translations) and [SOC Descriptor](https://deepwiki.com/tenstorrent/tt-umd/5.2-soc-descriptor).

Dismiss
Refresh this wiki

Enter email to refresh