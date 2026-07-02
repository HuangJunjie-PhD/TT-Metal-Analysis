---
project: tt-npe
github: https://github.com/tenstorrent/tt-npe
deepwiki: https://deepwiki.com/tenstorrent/tt-npe/3-workload-system
---

# Workload System

Relevant source files
*   [tt_npe/cpp/include/npeUtil.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeUtil.hpp)
*   [tt_npe/cpp/include/npeWorkload.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeWorkload.hpp)
*   [tt_npe/cpp/src/npeWorkloadIngest.cpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeWorkloadIngest.cpp)
*   [tt_npe/cpp/test/data/multichip-trace-example.json](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/test/data/multichip-trace-example.json)
*   [tt_npe/cpp/test/test_npe_workload.cpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/test/test_npe_workload.cpp)
*   [tt_npe/py/util/fabric_post_process.py](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/fabric_post_process.py)

The Workload System provides an abstract representation of Network-on-Chip (NoC) traffic patterns for performance estimation. It defines a hierarchical data model (`npeWorkload`, `npeWorkloadPhase`, `npeWorkloadTransfer`) that captures the essential characteristics of data movement operations, including source/destination coordinates, packet sizes, timing constraints, and multi-chip routing information. This abstraction enables both analysis of real hardware traces and exploration of synthetic traffic patterns.

For information about creating workloads programmatically or from JSON files, see [Creating Workloads from JSON](https://deepwiki.com/tenstorrent/tt-npe/3.2-creating-workloads-from-json) and [Programmatic Workload Construction](https://deepwiki.com/tenstorrent/tt-npe/3.3-programmatic-workload-construction). For details on trace ingestion and fabric routing, see [Converting NoC Traces to Workloads](https://deepwiki.com/tenstorrent/tt-npe/3.4-converting-noc-traces-to-workloads) and [Trace Preprocessing and Fabric Routing](https://deepwiki.com/tenstorrent/tt-npe/2.2-trace-preprocessing-and-fabric-routing).

* * *

## Workload Data Model Overview

The workload representation follows a three-level hierarchy that organizes NoC transfers by execution phase, allowing the simulator to model temporal ordering and dependencies accurately.

**Sources:**

*   [tt_npe/cpp/include/npeWorkload.hpp 16-150](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeWorkload.hpp#L16-L150)
*   [tt_npe/cpp/include/npeUtil.hpp 162-303](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeUtil.hpp#L162-L303)

* * *

## Workload Ingestion Architecture

The system supports two primary workload input methods: custom JSON workload files and tt-metal NoC trace files. Both paths converge to the unified `npeWorkload` representation through distinct ingestion pipelines.

**Sources:**

*   [tt_npe/cpp/src/npeWorkloadIngest.cpp 31-219](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeWorkloadIngest.cpp#L31-L219)
*   [tt_npe/cpp/src/npeWorkloadIngest.cpp 303-660](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeWorkloadIngest.cpp#L303-L660)
*   [tt_npe/cpp/src/npeWorkloadIngest.cpp 662-681](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeWorkloadIngest.cpp#L662-L681)
*   [tt_npe/py/util/fabric_post_process.py 506-602](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/fabric_post_process.py#L506-L602)

* * *

## Transfer Types and Destination Encoding

Transfers support both unicast and multicast operations through the `NocDestination` variant type. Multicast destinations are represented as one or more rectangular grids of coordinates, enabling efficient iteration during simulation.

| Transfer Type | Destination Type | Description | Example |
| --- | --- | --- | --- |
| **Unicast** | `Coord` | Single destination coordinate | `Coord{device_id=0, row=5, col=3}` |
| **Multicast** | `MulticastCoordSet` | Rectangular grid(s) of destinations | `MulticastCoordSet({start=(1,1), end=(3,5)})` |
| **Multi-Grid Multicast** | `MulticastCoordSet` | Multiple non-contiguous grids | Multiple `CoordGrid` entries in `coord_grids` |

### Coordinate System

All coordinates use the format `Coord{device_id, row, col}` where:

*   `device_id` identifies the physical chip (0 for single-chip, 0-N for multi-chip)
*   `row` is the Y position in the NoC grid
*   `col` is the X position in the NoC grid

**Sources:**

*   [tt_npe/cpp/include/npeUtil.hpp 162-170](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeUtil.hpp#L162-L170)
*   [tt_npe/cpp/include/npeUtil.hpp 172-300](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeUtil.hpp#L172-L300)
*   [tt_npe/cpp/include/npeUtil.hpp 303](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeUtil.hpp#L303-L303)

* * *

## Transfer Groups for Multi-Chip Routing

Transfer groups enable modeling of complex multi-hop fabric routes where a single logical transfer is decomposed into multiple physical transfers across device boundaries. Each transfer in a group has a unique index and optionally references a parent transfer, forming a dependency tree.

### Transfer Group Fields

*   **`transfer_group_id`**: Unique identifier shared by all transfers in the same logical operation (assigned via `registerTransferGroupID()`)
*   **`transfer_group_index`**: Sequential index within the group (0, 1, 2, ...)
*   **`transfer_group_parent`**: Index of the parent transfer that must complete before this transfer activates (-1 for root)

The dependency tracker uses these fields to enforce correct ordering during simulation, ensuring that downstream transfers only activate after upstream transfers complete.

**Sources:**

*   [tt_npe/cpp/include/npeWorkload.hpp 25-83](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeWorkload.hpp#L25-L83)
*   [tt_npe/cpp/src/npeWorkloadIngest.cpp 520-638](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeWorkloadIngest.cpp#L520-L638)
*   [tt_npe/py/util/fabric_post_process.py 176-483](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/fabric_post_process.py#L176-L483)

* * *

## Zone Tracking for Profiling Context

Zones provide hierarchical profiling context extracted from tt-metal trace files. Each zone has a name, timestamp, and phase (start/end), allowing transfers to be annotated with their enclosing profiling scopes.

The `ZoneIterator` maintains a stack of currently-active zones as it traverses the timeline, allowing each transfer to capture its full hierarchical context at the moment it was issued.

**Sources:**

*   [tt_npe/cpp/include/npeUtil.hpp 313-373](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeUtil.hpp#L313-L373)
*   [tt_npe/cpp/src/npeWorkloadIngest.cpp 271-301](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeWorkloadIngest.cpp#L271-L301)
*   [tt_npe/cpp/src/npeWorkloadIngest.cpp 384-400](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeWorkloadIngest.cpp#L384-L400)

* * *

## Workload Validation and Preprocessing

Before simulation, workloads undergo validation and preprocessing to ensure correctness and infer missing parameters.

### Validation Pipeline

### Injection Rate Inference

When `injection_rate` is not specified (value is 0.0), the system automatically infers it based on the source core type:

`// npeWorkload::inferInjectionRates() implementationvoid npeWorkload::inferInjectionRates(const npeDeviceModel &device_model) {    for (auto &phase : phases) {        for (auto &transfer : phase.transfers) {            if (transfer.injection_rate == 0.0) {                transfer.injection_rate = device_model.getSrcInjectionRate(transfer.src);            }        }    }}`
The device model maps core types (DRAM, Tensix, Ethernet, PCIe) to their characteristic bandwidth capabilities.

**Sources:**

*   [tt_npe/cpp/include/npeWorkload.hpp 75-125](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeWorkload.hpp#L75-L125)
*   [tt_npe/cpp/test/test_npe_workload.cpp 25-134](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/test/test_npe_workload.cpp#L25-L134)

* * *

## Golden Cycle Tracking

Workloads can store golden cycle counts derived from real hardware execution, enabling comparison between simulated and actual performance. The system tracks per-device cycle ranges and computes mesh-wide aggregates.

| Field | Type | Description |
| --- | --- | --- |
| `golden_cycles` | `map<DeviceID, pair<CycleCount, CycleCount>>` | Per-device (start, end) cycle counts |
| `MESH_DEVICE` | Special device ID (-2) | Aggregate cycles across all devices |

The `computeGoldenCyclesAndT0()` function extracts these values from trace timestamps, computing the earliest start and latest end across all cores per device.

**Sources:**

*   [tt_npe/cpp/include/npeWorkload.hpp 111-125](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeWorkload.hpp#L111-L125)
*   [tt_npe/cpp/src/npeWorkloadIngest.cpp 221-269](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeWorkloadIngest.cpp#L221-L269)

* * *

## Schedule Scaling

The `scaleWorkloadSchedule()` method enables exploration of "what-if" scenarios by uniformly compressing or expanding the temporal schedule:

`// Scale all phase_cycle_offsets by scale_factor// Example: scale_factor=0.5 compresses schedule to half durationworkload.scaleWorkloadSchedule(0.5);`
This is useful for analyzing congestion effects under different injection rate assumptions or simulating faster/slower kernel execution.

**Sources:**

*   [tt_npe/cpp/include/npeWorkload.hpp 109](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeWorkload.hpp#L109-L109)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-npe/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh