---
project: tt-npe
github: https://github.com/tenstorrent/tt-npe
deepwiki: https://deepwiki.com/tenstorrent/tt-npe/5-device-models
---

# Device Models

Relevant source files
*   [tt_npe/cpp/include/device_models/blackhole.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/blackhole.hpp)
*   [tt_npe/cpp/include/device_models/wormhole_b0.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_b0.hpp)
*   [tt_npe/cpp/include/device_models/wormhole_multichip.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_multichip.hpp)
*   [tt_npe/cpp/include/npeDeviceModelIface.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeDeviceModelIface.hpp)
*   [tt_npe/cpp/include/npeDeviceModelUtils.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeDeviceModelUtils.hpp)
*   [tt_npe/cpp/include/npeStats.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeStats.hpp)

## Overview

Device models are the abstraction layer that encapsulates hardware-specific characteristics of Tenstorrent accelerators. Each supported hardware architecture (WormholeB0, Blackhole) has a concrete device model implementation that adheres to the `npeDeviceModel` interface, enabling the simulation engine to work polymorphically with any device architecture.

Device models provide:

*   **Topology information**: Grid dimensions, core type layouts, device IDs
*   **Routing logic**: NoC path calculation for unicast and multicast transfers
*   **Bandwidth parameters**: Link bandwidth, injection rates, absorption rates
*   **Congestion modeling**: Bandwidth derating based on resource contention
*   **Resource identification**: Bidirectional lookup between link/NIU IDs and attributes

This page provides an architectural overview of the device model system. For implementation details, see:

*   **[Page 5.1: Device Model Interface](https://deepwiki.com/tenstorrent/tt-npe/5.1-device-model-interface)** - Pure virtual interface specification and contract
*   **[Page 5.2: Wormhole B0 Implementation](https://deepwiki.com/tenstorrent/tt-npe/5.2-wormhole-b0-implementation)** - 12x10 grid architecture details
*   **[Page 5.3: Blackhole Implementation](https://deepwiki.com/tenstorrent/tt-npe/5.3-blackhole-implementation)** - 12x17 grid architecture details
*   **[Page 5.4: Multi-chip Device Models](https://deepwiki.com/tenstorrent/tt-npe/5.4-multi-chip-device-models)** - Multi-device fabric configurations
*   **[Page 5.5: Routing Algorithms and Link Lookup](https://deepwiki.com/tenstorrent/tt-npe/5.5-routing-algorithms-and-link-lookup)** - Dimension-ordered routing and resource lookups

**Sources:**[tt_npe/cpp/include/npeDeviceModelIface.hpp 1-73](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeDeviceModelIface.hpp#L1-L73)[tt_npe/cpp/include/device_models/wormhole_b0.hpp 1-543](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_b0.hpp#L1-L543)[tt_npe/cpp/include/device_models/blackhole.hpp 1-604](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/blackhole.hpp#L1-L604)[tt_npe/cpp/include/device_models/wormhole_multichip.hpp 1-334](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_multichip.hpp#L1-L334)

## System Integration

Device models integrate with the simulation engine and other tt-npe components to provide hardware-specific behavior. The following diagram shows how device models fit into the overall architecture:

**Device Model Position in tt-npe Architecture**

The `npeEngine` receives workload descriptions (`npeWorkload`) and uses the device model to route transfers, compute bandwidth, and model congestion. The device model operates on `npeDeviceState` to track resource utilization across timesteps.

**Sources:**[tt_npe/cpp/include/npeDeviceModelIface.hpp 1-73](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeDeviceModelIface.hpp#L1-L73)[tt_npe/cpp/include/npeEngine.hpp 1-50](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeEngine.hpp#L1-L50)

## Class Hierarchy

Device models use an interface-based architecture where all concrete implementations inherit from the abstract `npeDeviceModel` class. This enables polymorphic usage by the simulation engine.

**Device Model Class Hierarchy**

Key design points:

*   `npeDeviceModel` is a pure virtual interface (all methods are `= 0`)
*   `WormholeB0DeviceModel` and `BlackholeDeviceModel` are standalone single-chip implementations
*   `WormholeMultichipDeviceModel` uses composition, internally containing a `WormholeB0DeviceModel` and extending it with multi-chip routing and device ID management
*   The simulation engine (`npeEngine`) holds a pointer to `npeDeviceModel`, enabling runtime polymorphism

**Sources:**[tt_npe/cpp/include/npeDeviceModelIface.hpp 23-72](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeDeviceModelIface.hpp#L23-L72)[tt_npe/cpp/include/device_models/wormhole_b0.hpp 16-26](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_b0.hpp#L16-L26)[tt_npe/cpp/include/device_models/blackhole.hpp 16-37](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/blackhole.hpp#L16-L37)[tt_npe/cpp/include/device_models/wormhole_multichip.hpp 17-28](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_multichip.hpp#L17-L28)

## Core Responsibilities

Device models provide five categories of functionality to the simulation engine:

| Responsibility | Description | Key Interface Methods | When Used |
| --- | --- | --- | --- |
| **Topology** | Grid dimensions, device IDs, core type mapping | `getRows()`, `getCols()`, `getNumChips()`, `getDeviceIDs()`, `getCoreType(Coord)` | Initialization, validation, statistics |
| **Routing** | Calculate link-by-link NoC paths from source to destination | `route(nocType, Coord, NocDestination)` | Transfer activation |
| **Bandwidth Parameters** | Injection/absorption rates, link bandwidth | `getSrcInjectionRate(Coord)`, `getSinkAbsorptionRate(Coord)`, `getLinkBandwidth(nocLinkID)` | Bandwidth calculation |
| **Resource Lookup** | Bidirectional ID ↔ attribute translation for links and NIUs | `getLinkID(nocLinkAttr)`, `getLinkAttributes(nocLinkID)`, `getNIUID(nocNIUAttr)`, `getNIUAttributes(nocNIUID)` | Routing, statistics |
| **State Management** | Initialize device state, compute transfer rates with congestion | `initDeviceState()`, `computeCurrentTransferRate(...)` | Each timestep |

The `computeCurrentTransferRate()` method coordinates the bandwidth calculation pipeline:

1.   Call `updateTransferBandwidth()` (from `npeDeviceModelUtils.hpp`) to compute packet-size-based bandwidth using the transfer bandwidth table
2.   Optionally call `modelCongestion()` to derate bandwidth based on link/NIU demand
3.   Update `PETransferState::curr_bandwidth` for each active transfer

See [Page 5.1](https://deepwiki.com/tenstorrent/tt-npe/5.1-device-model-interface) for detailed interface specifications.

**Sources:**[tt_npe/cpp/include/npeDeviceModelIface.hpp 23-70](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeDeviceModelIface.hpp#L23-L70)[tt_npe/cpp/include/npeDeviceModelUtils.hpp 51-65](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeDeviceModelUtils.hpp#L51-L65)

## Supported Architectures

tt-npe supports three device configurations through concrete device model implementations:

| Device Model | Architecture | Grid Size | Link BW | Worker Injection | DRAM Injection | DRAM Banks | File |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `WormholeB0DeviceModel` | Wormhole B0 | 12×10 | 30 GB/s | 28.1 GB/s | 23.2 GB/s | 12 | [wormhole_b0.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/wormhole_b0.hpp) |
| `BlackholeDeviceModel` | Blackhole P100/P150 | 12×17 | 60.9 GB/s | 60.9 GB/s | 40.0 GB/s | 7 (P100) / 8 (P150) | [blackhole.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/blackhole.hpp) |
| `WormholeMultichipDeviceModel` | Multi-chip Wormhole | (12×10)×N | 30 GB/s | 28.1 GB/s | 23.2 GB/s | 12×N | [wormhole_multichip.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/wormhole_multichip.hpp) |

### Core Type Layout

All architectures use four core types defined in the `CoreType` enumeration:

*   **WORKER**: Compute cores with full NoC bandwidth
*   **DRAM**: Memory interface cores with architecture-specific bandwidth
*   **ETH**: Ethernet cores for inter-chip communication (fabric links)
*   **UNDEF**: Undefined/reserved/inactive core locations

Core type layout is stored in `coord_to_core_type` (a `Grid2D<CoreType>`) and initialized from `coord_to_core_type_map` during construction. Core type determines:

*   Injection rate via `getSrcInjectionRate(Coord)` → `core_type_to_inj_rate[getCoreType(coord)]`
*   Absorption rate via `getSinkAbsorptionRate(Coord)` → `core_type_to_abs_rate[getCoreType(coord)]`
*   Whether multicast sink NIUs accumulate demand (only WORKER cores participate)

### NoC Configuration

Both architectures use two independent NoCs with dimension-ordered routing:

*   **NOC0**: Routes East first (X dimension), then South (Y dimension)
*   **NOC1**: Routes North first (Y dimension), then West (X dimension)

The toroidal mesh topology wraps around at grid boundaries via `wrapToRange()` helper function.

### Transfer Bandwidth Tables

Each device model contains a `TransferBandwidthTable` (`tbt`) mapping packet size to achieved bandwidth. Examples:

**Wormhole B0**[wormhole_b0.hpp 453-454](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/wormhole_b0.hpp#L453-L454):

```
{0, 0}, {128, 5.5}, {256, 10.1}, {512, 18.0}, {1024, 27.4}, {2048, 30.0}, {8192, 30.0}
```

**Blackhole**[blackhole.hpp 466-467](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/blackhole.hpp#L466-L467):

```
{0, 0}, {128, 6.0}, {256, 12.1}, {512, 24.2}, {1024, 48.0}, {2048, 57.7}, {4096, 58.7}, {8192, 60.4}, {16384, 60.9}
```

The `interpolateBW()` function in [npeDeviceModelUtils.hpp 16-50](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/npeDeviceModelUtils.hpp#L16-L50) performs piecewise linear interpolation between table entries to compute bandwidth for arbitrary packet sizes.

For detailed architecture specifications, see:

*   [Page 5.2: Wormhole B0 Implementation](https://deepwiki.com/tenstorrent/tt-npe/5.2-wormhole-b0-implementation)
*   [Page 5.3: Blackhole Implementation](https://deepwiki.com/tenstorrent/tt-npe/5.3-blackhole-implementation)
*   [Page 5.4: Multi-chip Device Models](https://deepwiki.com/tenstorrent/tt-npe/5.4-multi-chip-device-models)

**Sources:**[tt_npe/cpp/include/device_models/wormhole_b0.hpp 434-466](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_b0.hpp#L434-L466)[tt_npe/cpp/include/device_models/blackhole.hpp 446-478](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/blackhole.hpp#L446-L478)[tt_npe/cpp/include/device_models/wormhole_multichip.hpp 17-28](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_multichip.hpp#L17-L28)[tt_npe/cpp/include/npeDeviceModelUtils.hpp 16-50](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeDeviceModelUtils.hpp#L16-L50)

## Usage in Simulation

The device model is invoked by `npeEngine` at multiple stages during simulation execution:

**Device Model Invocation Sequence**

### Key Integration Points

**1. Initialization** - `npeEngine::runSinglePerfSim()` calls `device_model->initDeviceState()` to allocate demand grids sized for the device's link and NIU counts. For multi-chip models, this allocates grids spanning all devices.

**2. Transfer Activation** - When a transfer becomes active, the engine calls `route()` to compute the link-by-link path. The returned `nocRoute` is stored in `PETransferState::route` for bandwidth calculation and statistics.

**3. Timestep Processing** - At each timestep, `computeCurrentTransferRate()` computes bandwidth for all live transfers:

*   `updateTransferBandwidth()` from [npeDeviceModelUtils.hpp 51-65](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/npeDeviceModelUtils.hpp#L51-L65) interpolates the transfer bandwidth table
*   If congestion modeling is enabled, `modelCongestion()` accumulates demand on demand grids and derates bandwidth
*   `PETransferState::curr_bandwidth` is updated in-place

**4. Resource Lookups** - Statistics collection uses `getLinkAttributes()` and `getNIUAttributes()` to translate numeric IDs back to coordinates and types for per-device aggregation.

See [Page 4.1: Performance Estimation Algorithm](https://deepwiki.com/tenstorrent/tt-npe/4.1-performance-estimation-algorithm) for detailed simulation loop description.

**Sources:**[tt_npe/cpp/include/npeDeviceModelIface.hpp 28-42](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeDeviceModelIface.hpp#L28-L42)[tt_npe/cpp/include/device_models/wormhole_b0.hpp 191-222](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_b0.hpp#L191-L222)[tt_npe/cpp/include/npeDeviceModelUtils.hpp 51-65](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeDeviceModelUtils.hpp#L51-L65)[tt_npe/cpp/include/npeDeviceModelUtils.hpp 67-140](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeDeviceModelUtils.hpp#L67-L140)

## Instantiation and Selection

Device models are instantiated based on workload metadata or configuration parameters. The system supports three instantiation patterns:

| Pattern | Constructor | Use Case | Chips |
| --- | --- | --- | --- |
| **Single-chip WormholeB0** | `WormholeB0DeviceModel()` | Single Wormhole device simulation | 1 |
| **Single-chip Blackhole** | `BlackholeDeviceModel(Model::p100)` or `BlackholeDeviceModel(Model::p150)` | Single Blackhole device with P100/P150 variant | 1 |
| **Multi-chip Wormhole** | `WormholeMultichipDeviceModel(num_chips)` | TG, TGG, Galaxy, N150 fabric configurations | N |

The `DeviceArch` enumeration [npeDeviceModelIface.hpp 18-21](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/npeDeviceModelIface.hpp#L18-L21) identifies the architecture type:

Each device model implements `getArch()` to return its architecture type, enabling runtime architecture detection.

### Multi-chip Architecture

`WormholeMultichipDeviceModel` uses composition rather than inheritance, internally containing a `WormholeB0DeviceModel` instance. It extends single-chip functionality by:

*   Managing device IDs: `_device_ids` contains `{0, 1, ..., num_chips-1}`
*   Scaling lookup tables: Link and NIU lookups span all devices (size = single_chip_size × num_chips)
*   Delegating per-chip operations: Routing, bandwidth lookups, and core info delegate to the internal `_wormhole_b0_model`
*   Cross-chip routing: `changeRouteDeviceID()` modifies routes to substitute correct device IDs

See [Page 5.4: Multi-chip Device Models](https://deepwiki.com/tenstorrent/tt-npe/5.4-multi-chip-device-models) for implementation details.

**Sources:**[tt_npe/cpp/include/npeDeviceModelIface.hpp 18-21](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeDeviceModelIface.hpp#L18-L21)[tt_npe/cpp/include/device_models/blackhole.hpp 18-37](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/blackhole.hpp#L18-L37)[tt_npe/cpp/include/device_models/wormhole_multichip.hpp 17-28](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_multichip.hpp#L17-L28)[tt_npe/cpp/include/device_models/wormhole_multichip.hpp 57-81](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_multichip.hpp#L57-L81)

## Resource Identification System

Device models implement bidirectional lookup between numeric resource IDs and their physical attributes. This system enables efficient indexing (IDs are array indices) while maintaining semantic information (attributes include coordinates and types).

### Link and NIU Types

**Link Resources** - Each grid location has 4 directional links (2 per NoC):

*   `nocLinkID`: Integer index for fast array access
*   `nocLinkAttr`: `{Coord{device_id, row, col}, nocLinkType}` where `nocLinkType` ∈ {NOC0_EAST, NOC0_SOUTH, NOC1_NORTH, NOC1_WEST}

**NIU Resources** - Each grid location has 4 Network Interface Units (2 per NoC × src/sink):

*   `nocNIUID`: Integer index for fast array access
*   `nocNIUAttr`: `{Coord{device_id, row, col}, nocNIUType}` where `nocNIUType` ∈ {NOC0_SRC, NOC0_SINK, NOC1_SRC, NOC1_SINK}

### Lookup Table Construction

Device model constructors call `populateNoCLinkLookups()` and `populateNoCNIULookups()` to build bidirectional mappings:

Enumeration order: For single-chip models, IDs increase as: row (outer), col (middle), type (inner). For multi-chip models, device_id is the outermost loop.

### Demand Grid Indexing

The `npeDeviceState` contains `LinkDemandGrid` and `NIUDemandGrid` vectors indexed by `nocLinkID` and `nocNIUID`. Congestion modeling accumulates bandwidth demand at these indices, and statistics collection translates IDs back to attributes for per-device aggregation.

See [Page 5.5: Routing Algorithms and Link Lookup](https://deepwiki.com/tenstorrent/tt-npe/5.5-routing-algorithms-and-link-lookup) for routing implementation details.

**Sources:**[tt_npe/cpp/include/device_models/wormhole_b0.hpp 27-49](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_b0.hpp#L27-L49)[tt_npe/cpp/include/device_models/wormhole_b0.hpp 243-275](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_b0.hpp#L243-L275)[tt_npe/cpp/include/device_models/wormhole_multichip.hpp 29-55](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_multichip.hpp#L29-L55)

## Congestion Modeling

Device models implement congestion modeling through the `modelCongestion()` method, which calculates bandwidth derating factors based on resource contention. The algorithm operates in two phases:

### Demand Calculation Phase

For each active transfer, the method accumulates effective demand (bandwidth × utilization factor) on:

*   Source NIU (injection point)
*   Sink NIU(s) (absorption point, potentially multiple for multicast)
*   All links in the transfer's route

Multicast writes accumulate demand on worker sink NIUs only, as other core types ignore multicast traffic.

### Bandwidth Derating Phase

For each transfer, the method computes three derating factors:

1.   **Link bottleneck**: Ratio of link bandwidth to maximum link demand on the route
2.   **Source NIU bottleneck**: Ratio of injection rate to source NIU demand
3.   **Sink NIU bottleneck**: Ratio of absorption rate to sink NIU demand (minimum across all sinks for multicast)

The transfer's bandwidth is multiplied by the minimum derating factor across these three resources, modeling the bottleneck's limiting effect.

The current implementation uses a single-iteration first-order congestion model (NUM_ITERS=1, grad_fac=1.0). Historical code comments indicate gradient descent was explored but found unnecessary.

**Sources:**[tt_npe/cpp/include/device_models/wormhole_b0.hpp 55-189](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_b0.hpp#L55-L189)[tt_npe/cpp/include/device_models/blackhole.hpp 67-201](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/blackhole.hpp#L67-L201)[tt_npe/cpp/include/device_models/wormhole_multichip.hpp 90-216](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/device_models/wormhole_multichip.hpp#L90-L216)

Dismiss
Refresh this wiki

Enter email to refresh