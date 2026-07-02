---
project: tt-umd
github: https://github.com/tenstorrent/tt-umd
deepwiki: https://deepwiki.com/tenstorrent/tt-umd/6-architecture-specific-features
---

# Architecture-Specific Features

Relevant source files
*   [device/api/umd/device/arch/architecture_implementation.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/architecture_implementation.hpp)
*   [device/api/umd/device/arch/blackhole_implementation.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/blackhole_implementation.hpp)
*   [device/api/umd/device/arch/grendel_implementation.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/grendel_implementation.hpp)
*   [device/api/umd/device/arch/wormhole_implementation.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/wormhole_implementation.hpp)
*   [device/arch/blackhole_implementation.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/arch/blackhole_implementation.cpp)
*   [device/arch/wormhole_implementation.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/arch/wormhole_implementation.cpp)

## Purpose and Scope

This page provides an overview of how `tt-umd` handles multiple Tenstorrent chip architectures through abstraction layers and architecture-specific implementations. The system currently supports **Wormhole B0** and **Blackhole** architectures, with each having distinct hardware capabilities, communication protocols, and firmware requirements.

For detailed information about specific architectures, see:

*   Wormhole-specific features: [Wormhole Architecture](https://deepwiki.com/tenstorrent/tt-umd/6.1-wormhole-architecture)
*   Blackhole-specific features: [Blackhole Architecture](https://deepwiki.com/tenstorrent/tt-umd/6.2-blackhole-architecture)
*   ARC firmware communication: [ARC Communication](https://deepwiki.com/tenstorrent/tt-umd/6.3-arc-communication)

This page focuses on:

1.   **Architecture detection** and device factory patterns.
2.   **`architecture_implementation`** abstraction layer.
3.   **Firmware version management** and validation.
4.   **Key differences** between architectures in capabilities and protocols.

* * *

## Architecture Detection and Device Creation

The system uses a **factory pattern** to create architecture-specific `TTDevice` instances based on runtime detection of the underlying hardware.

### Device Factory Pattern

**Sources:**[device/tt_device/tt_device.cpp 69-117](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/tt_device.cpp#L69-L117)

* * *

## Architecture Implementation Layer

Each architecture-specific `TTDevice` subclass owns an `architecture_implementation` instance that provides architecture-specific parameters and methods. This abstraction allows the base `TTDevice` to remain architecture-agnostic.

### Architecture Implementation Hierarchy

**Sources:**[device/api/umd/device/arch/architecture_implementation.hpp 31-118](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/architecture_implementation.hpp#L31-L118)[device/api/umd/device/arch/wormhole_implementation.hpp 25-220](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/wormhole_implementation.hpp#L25-L220)[device/api/umd/device/arch/blackhole_implementation.hpp 29-210](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/blackhole_implementation.hpp#L29-L210)

The `architecture_implementation` is instantiated via a static factory method `create(tt::ARCH architecture)`[device/api/umd/device/arch/architecture_implementation.hpp 102](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/architecture_implementation.hpp#L102-L102) It defines critical hardware constants such as grid sizes, core locations (Tensix, DRAM, ETH, ARC, PCIe), and TLB configurations.

### Comparison of Key Hardware Data

| Feature | Wormhole B0 | Blackhole |
| --- | --- | --- |
| **Full Grid Size** | 10x12 [device/api/umd/device/arch/wormhole_implementation.hpp 118](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/wormhole_implementation.hpp#L118-L118) | 17x12 [device/api/umd/device/arch/blackhole_implementation.hpp 73](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/blackhole_implementation.hpp#L73-L73) |
| **Tensix Grid** | 8x10 [device/api/umd/device/arch/wormhole_implementation.hpp 124](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/wormhole_implementation.hpp#L124-L124) | 14x10 [device/api/umd/device/arch/blackhole_implementation.hpp 79](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/blackhole_implementation.hpp#L79-L79) |
| **DRAM Banks** | 6 [device/api/umd/device/arch/wormhole_implementation.hpp 140](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/wormhole_implementation.hpp#L140-L140) | 8 [device/api/umd/device/arch/blackhole_implementation.hpp 95](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/blackhole_implementation.hpp#L95-L95) |
| **ETH Channels** | 16 [device/api/umd/device/arch/wormhole_implementation.hpp 156](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/wormhole_implementation.hpp#L156-L156) | 14 [device/api/umd/device/arch/blackhole_implementation.hpp 143](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/blackhole_implementation.hpp#L143-L143) |
| **ARC Location (NOC0)** | {0, 10} [device/api/umd/device/arch/wormhole_implementation.hpp 177](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/wormhole_implementation.hpp#L177-L177) | {8, 0} [device/api/umd/device/arch/blackhole_implementation.hpp 114](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/blackhole_implementation.hpp#L114-L114) |

* * *

## ARC Communication and Messaging

Architectures differ in how they communicate with the ARC (Argonaut RISC Core) for system management tasks. Both use `arc_message_type` enums to define commands, but the underlying values and supported operations vary.

**Wormhole** supports specialized ARC messages for SPI access and SMBus telemetry [device/api/umd/device/arch/wormhole_implementation.hpp 100-115](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/wormhole_implementation.hpp#L100-L115):

*   `GET_SPI_DUMP_ADDR`
*   `GET_SMBUS_TELEMETRY_ADDR`
*   `SPI_READ / SPI_WRITE`

**Blackhole** focuses on core management and training status [device/api/umd/device/arch/blackhole_implementation.hpp 59-70](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/blackhole_implementation.hpp#L59-L70):

*   `GET_AICLK`
*   `SET_ETH_DRAM_TRAINED_STATUS`
*   `SETUP_IATU_FOR_PEER_TO_PEER`

For details, see [ARC Communication](https://deepwiki.com/tenstorrent/tt-umd/6.3-arc-communication).

* * *

## TLB and Memory Mapping Differences

Memory mapping via Translation Lookaside Buffers (TLBs) is highly architecture-specific, defining how PCIe BAR addresses translate to NOC coordinates.

*   **Wormhole** defines three TLB sizes: 1MB, 2MB, and 16MB [device/api/umd/device/arch/wormhole_implementation.hpp 39-98](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/wormhole_implementation.hpp#L39-L98) It uses a complex mapping where TLB indices determine the size and configuration [device/arch/wormhole_implementation.cpp 36-68](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/arch/wormhole_implementation.cpp#L36-L68)
*   **Blackhole** simplifies this to 2MB and 4GB TLBs [device/api/umd/device/arch/blackhole_implementation.hpp 31-57](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/blackhole_implementation.hpp#L31-L57) The 4GB TLBs are specifically used for large memory regions [device/arch/blackhole_implementation.cpp 37-48](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/arch/blackhole_implementation.cpp#L37-L48)

**Sources:**[device/arch/wormhole_implementation.cpp 36-68](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/arch/wormhole_implementation.cpp#L36-L68)[device/arch/blackhole_implementation.cpp 36-59](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/arch/blackhole_implementation.cpp#L36-L59)

* * *

## Summary of Child Pages

### [Wormhole Architecture](https://deepwiki.com/tenstorrent/tt-umd/6.1-wormhole-architecture)

Covers Wormhole-specific implementations including `wormhole_implementation`, `WormholeTTDevice`, and topology discovery. Details include ETH training, ARC APB access via BAR0, and iATU configuration through ARC messaging.

### [Blackhole Architecture](https://deepwiki.com/tenstorrent/tt-umd/6.2-blackhole-architecture)

Covers Blackhole-specific implementations including `blackhole_implementation`, `BlackholeTTDevice`, and its unique topology discovery. Details include direct iATU configuration via BAR2 and NOC-based ARC access.

### [ARC Communication](https://deepwiki.com/tenstorrent/tt-umd/6.3-arc-communication)

Documents the ARC processor communication infrastructure, including `ArcMessenger`, `ArcTelemetryReader`, and firmware interaction logic across both architectures.

**Sources:**[device/api/umd/device/arch/wormhole_implementation.hpp 1-220](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/wormhole_implementation.hpp#L1-L220)[device/api/umd/device/arch/blackhole_implementation.hpp 1-210](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/blackhole_implementation.hpp#L1-L210)[device/api/umd/device/arch/architecture_implementation.hpp 1-121](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/arch/architecture_implementation.hpp#L1-L121)

Dismiss
Refresh this wiki

Enter email to refresh