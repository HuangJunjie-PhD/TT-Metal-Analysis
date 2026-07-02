---
project: tt-npe
github: https://github.com/tenstorrent/tt-npe
deepwiki: https://deepwiki.com/tenstorrent/tt-npe/4-simulation-engine
---

# Simulation Engine

Relevant source files
*   [tt_npe/cpp/src/npeEngine.cpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp)
*   [tt_npe/cpp/src/npeStats.cpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp)
*   [tt_npe/py/util/npe_analyze_noc_trace_dir.py](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py)

## Purpose and Scope

The **Simulation Engine** is the core component of tt-npe that executes discrete-timestep performance estimation of NoC transfers. This page documents the `npeEngine` class, its internal data structures, and the overall simulation execution flow. The engine processes workloads represented as phases and transfers, computes routing paths, models congestion effects, tracks dependencies, and produces detailed performance statistics.

For information about device-specific routing and bandwidth modeling, see [Device Models](https://deepwiki.com/tenstorrent/tt-npe/5-device-models). For statistics collection and output formats, see [Statistics and Visualization Output](https://deepwiki.com/tenstorrent/tt-npe/2.4-statistics-and-visualization-output). For workload representation details, see [Workload System](https://deepwiki.com/tenstorrent/tt-npe/3-workload-system).

* * *

## Architecture Overview

The simulation engine consists of several cooperating subsystems that manage the lifecycle of NoC transfers through discrete timesteps.

**Diagram: Simulation Engine Component Architecture**

Sources: [tt_npe/cpp/src/npeEngine.cpp 1-362](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L1-L362)

* * *

## Key Classes and Data Structures

### npeEngine

The primary simulation orchestrator. Constructed with a device name, it creates the appropriate device model and provides the main entry point for performance estimation.

| Method | Purpose |
| --- | --- |
| `npeEngine(const std::string &device_name)` | Constructor; initializes device model via factory |
| `runPerfEstimation(const npeWorkload &wl, const npeConfig &cfg)` | Main entry point; optionally runs congestion-free comparison |
| `runSinglePerfSim(const npeWorkload &wl, const npeConfig &cfg)` | Core simulation loop |
| `initTransferState(const npeWorkload &wl)` | Flattens workload into transfer state vector |
| `createTransferQueue(const std::vector<PETransferState> &)` | Creates sorted activation queue |
| `genDependencies(std::vector<PETransferState> &)` | Builds dependency graph |

Sources: [tt_npe/cpp/src/npeEngine.cpp 21-47](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L21-L47)[tt_npe/cpp/src/npeEngine.cpp 172-200](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L172-L200)

### PETransferState

Per-transfer simulation state that tracks progress through the NoC. Each transfer from the workload is converted into a `PETransferState` instance.

**Key Fields:**

*   `params`: Original `npeWorkloadTransfer` definition
*   `route`: Vector of link IDs computed by device model
*   `start_cycle`: Actual start cycle (may differ from `phase_cycle_offset` due to dependencies)
*   `end_cycle`: Cycle when transfer completes
*   `curr_bandwidth`: Current effective bandwidth (bytes/cycle) after congestion derating
*   `total_bytes_transferred`: Progress tracker
*   `depends_on`: `npeCheckpointID` this transfer waits for
*   `required_by`: Vector of checkpoints gated by this transfer's completion

Sources: [tt_npe/cpp/src/npeEngine.cpp 25-46](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L25-L46)

### TransferQueuePair

Simple queue entry structure used to track when transfers should be considered for activation.

`struct TransferQueuePair {    size_t start_cycle;  // Earliest possible start    PETransferID id;     // Index into transfer_state vector};`
The queue is sorted in descending order of `start_cycle` so that the simulation loop can efficiently pop ready transfers from the end.

Sources: [tt_npe/cpp/src/npeEngine.cpp 49-64](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L49-L64)

### npeTransferDependencyTracker

Manages checkpoint-based dependencies between transfers. Uses a checkpoint abstraction where each checkpoint has:

*   A required count (number of transfers that must complete)
*   A delay offset applied after completion
*   An end cycle tracking when all dependencies are satisfied

**Key Operations:**

*   `createCheckpoint(int count, CycleCount delay)`: Creates new dependency checkpoint
*   `updateCheckpoint(npeCheckpointID id, CycleCount cycle)`: Marks one dependency complete
*   `done(npeCheckpointID id, CycleCount curr_cycle)`: Checks if checkpoint satisfied
*   `end_cycle_plus_delay(npeCheckpointID id)`: Returns activation time after checkpoint

Sources: [tt_npe/cpp/src/npeEngine.cpp 67-170](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L67-L170)

* * *

## Simulation Execution Flow

**Diagram: High-Level Simulation Execution Flow**

Sources: [tt_npe/cpp/src/npeEngine.cpp 172-361](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L172-L361)

* * *

## Initialization Process

Before entering the main simulation loop, `runSinglePerfSim` performs several initialization steps to prepare data structures.

### Transfer State Initialization

The `initTransferState` method flattens the hierarchical workload (phases → transfers) into a single vector indexed by transfer ID.

**Process:**

1.   Count total transfers across all phases
2.   Resize `transfer_state` vector to this size
3.   For each transfer in each phase: 
    *   Create `PETransferState` with original parameters
    *   Compute route using `model->route(noc_type, src, dst)`
    *   Store at index `transfer.getID()`

Sources: [tt_npe/cpp/src/npeEngine.cpp 25-47](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L25-L47)

### Transfer Queue Creation

The `createTransferQueue` method builds a sorted dispatch queue.

**Process:**

1.   Extract `(phase_cycle_offset, transfer_id)` pairs from all transfers
2.   Sort in **descending** order by cycle offset (greatest first)
3.   Return vector where the **end** contains earliest transfers

This allows the simulation loop to efficiently pop ready transfers using `swap` and `resize` operations.

Sources: [tt_npe/cpp/src/npeEngine.cpp 49-65](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L49-L65)

### Dependency Generation

The `genDependencies` method establishes serialization constraints between transfers. It uses two mechanisms:

#### 1. NIU-Based Bucketing

Transfers sharing the same NoC Injection Unit (NIU) are serialized with a stride-2 dependency pattern to approximate 2-VC (virtual channel) effects.

**Bucketing Key:**`(nocType, src.row, src.col, link_type)`

**Dependency Logic:**

*   Sort transfers per bucket by start cycle
*   For index `i`, create dependency on transfer at index `i - 2`
*   This prevents unlimited parallelism at a single NIU

Sources: [tt_npe/cpp/src/npeEngine.cpp 67-110](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L67-L110)

#### 2. Transfer Group Dependencies

For multi-hop fabric transfers (identified by `transfer_group_id != -1`), establish parent-child dependencies:

**Process:**

1.   Build map: `(group_id, group_index) → transfer_id`
2.   For each transfer with a `transfer_group_parent`: 
    *   Find parent transfer ID
    *   Compute checkpoint delay (NOC latency + potential ethernet hop delay)
    *   Create checkpoint linking child to parent completion

**Ethernet Hop Delay:**

```
ETH_HOP_CYCLE_DELAY = 600 + 0.1055 * packet_size (cycles)
```

Applied only when `src.device_id != parent.src.device_id`.

Sources: [tt_npe/cpp/src/npeEngine.cpp 112-153](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L112-L153)

* * *

## Main Simulation Loop

The core of `runSinglePerfSim` is a timestep-driven loop that advances simulation time and processes active transfers.

**Diagram: Main Simulation Loop Internal Flow**

Sources: [tt_npe/cpp/src/npeEngine.cpp 220-352](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L220-L352)

### Timestep Processing Details

Each timestep iteration performs the following operations:

**1. Statistics Insertion** (line 232)

`stats.insertTimestep(start_of_timestep, curr_cycle, wl);`
**2. Transfer Activation** (lines 235-256)

*   Scan `transfer_queue` from end (earliest start cycles)
*   Check if `start_cycle <= curr_cycle` and dependency satisfied
*   Move to `live_transfer_ids`
*   If dependency defined, adjust `start_cycle` to `dep_tracker.end_cycle_plus_delay()`
*   Swap activated transfers to end and resize queue

**3. Bandwidth Computation** (lines 258-264)

`model->computeCurrentTransferRate(    start_of_timestep,    curr_cycle,    transfer_state,    live_transfer_ids,    *device_state,    enable_congestion_model);`
This device model method updates `curr_bandwidth` for all live transfers, applying congestion derating if enabled.

**4. Statistics Update** (lines 267-274) Aggregate demand grids into per-timestep statistics for visualization.

**5. Transfer State Update** (lines 277-319) For each live transfer:

*   Compute `cycles_active_in_curr_timestep` (handle mid-timestep activations)
*   Calculate `bytes_transferred = min(remaining, cycles_active * curr_bandwidth)`
*   Update `total_bytes_transferred`
*   If complete: 
    *   Compute exact `end_cycle`
    *   Update all checkpoints in `required_by` vector
    *   Update worst-case end cycle in stats

**6. Compaction** (lines 322-328) Remove completed transfers from `live_transfer_ids` using `std::remove_if`.

**7. Exit Conditions** (lines 333-347)

*   **Success:** Both queues empty and dependencies satisfied
*   **Failure:**`curr_cycle > MAX_CYCLE_LIMIT`

Sources: [tt_npe/cpp/src/npeEngine.cpp 220-352](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L220-L352)

* * *

## State Management

### Device State Tracking

The `device_state` object (type `std::unique_ptr<npeDeviceState>`) maintains per-link and per-NIU demand grids used for congestion modeling.

**Initialization:**

`auto device_state = model->initDeviceState();`
The device model creates grids dimensioned to its topology (rows × cols × links_per_core).

**Usage in Loop:**

*   Reset each timestep
*   Accumulate demand from all live transfers during bandwidth computation
*   Used by congestion model to compute derating factors
*   Copied to stats for visualization

Sources: [tt_npe/cpp/src/npeEngine.cpp 208-209](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L208-L209)

### Statistics Collection

The `npeStats` object accumulates data throughout simulation:

**Per-Timestep:** (via `insertTimestep`)

*   Cycle range
*   Active transfer IDs
*   Link/NIU demand grids
*   Utilization metrics

**Per-Device:** (via `updateWorstCaseTransferEndCycle`)

*   Latest completion cycle
*   Golden vs. estimated cycles
*   DRAM/ETH bandwidth utilization

**Finalization:** (via `finishSimulation`)

*   Compute cycle prediction error
*   Filter timesteps to relevant range
*   Calculate aggregate metrics

Sources: [tt_npe/cpp/src/npeStats.cpp 42-85](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L42-L85)

* * *

## Congestion Impact Estimation

When `cfg.estimate_cong_impact` is enabled, `runPerfEstimation` executes two simulations:

1.   **With Congestion:** Standard run with congestion model active
2.   **Without Congestion:**`congestion_model_name = "none"`, timeline emission disabled

The difference between estimated cycles provides the congestion impact percentage:

`congestion_impact = 100.0 * (cong_cycles - cong_free_cycles) / cong_cycles`
This metric quantifies how much network contention slows down the workload.

Sources: [tt_npe/cpp/src/npeEngine.cpp 172-200](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L172-L200)

* * *

## Error Handling

The simulation can return errors via `npeResult` variant type:

| Error Code | Condition | Location |
| --- | --- | --- |
| `EXCEEDED_SIM_CYCLE_LIMIT` | `curr_cycle > MAX_CYCLE_LIMIT` | Line 346 |
| `DEPENDENCY_GEN_FAILED` | Unsupported architecture in dependency generation | Line 141 |

Dependency sanity checks ensure:

*   All checkpoints are satisfied exactly once
*   No inconsistencies in dependency graph

Sources: [tt_npe/cpp/src/npeEngine.cpp 161-164](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L161-L164)[tt_npe/cpp/src/npeEngine.cpp 334-336](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L334-L336)[tt_npe/cpp/src/npeEngine.cpp 345-347](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L345-L347)

* * *

## Integration with Python API

The simulation engine is exposed to Python via pybind11 bindings. The typical usage pattern from Python:

`cfg = npe.Config()cfg.device_name = "wormhole_b0"cfg.cycles_per_timestep = 32 wl = npe.createWorkloadFromJSON(json_path, cfg.device_name)npe_api = npe.InitAPI(cfg)result = npe_api.runNPE(wl)  # Calls npeEngine::runPerfEstimation`
The `runNPE` method internally creates an `npeEngine` instance and invokes `runPerfEstimation`.

Sources: [tt_npe/py/util/npe_analyze_noc_trace_dir.py 205-230](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L205-L230)

* * *

## Performance Considerations

### Timestep Granularity

The `cycles_per_timestep` configuration parameter controls simulation fidelity vs. performance:

*   **Smaller values** (e.g., 8-16): More accurate transfer timing, slower execution
*   **Larger values** (e.g., 64-128): Faster simulation, potential loss of fine-grained detail
*   **Default:** 32 cycles (balance between accuracy and speed)

### Memory Usage

The engine maintains several large data structures:

*   `transfer_state`: O(num_transfers), each ~200 bytes
*   `device_state.demand_grids`: O(num_cores × links_per_core)
*   `stats.per_timestep_stats`: O(num_timesteps), grows throughout simulation

For large workloads (>100K transfers), consider:

*   Enabling timeline compression (`cfg.compress_timeline_output_file = true`)
*   Using split timeline files for visualization
*   Increasing `cycles_per_timestep` to reduce timestep count

Sources: [tt_npe/cpp/src/npeEngine.cpp 202-361](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L202-L361)[tt_npe/cpp/src/npeStats.cpp 828-876](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L828-L876)

* * *

## Related Components

*   **[Performance Estimation Algorithm](https://deepwiki.com/tenstorrent/tt-npe/4.1-performance-estimation-algorithm)**: Detailed breakdown of `runSinglePerfSim` loop mechanics
*   **[Transfer State Lifecycle](https://deepwiki.com/tenstorrent/tt-npe/4.2-transfer-state-lifecycle)**: In-depth `PETransferState` field documentation
*   **[Dependency Tracking System](https://deepwiki.com/tenstorrent/tt-npe/4.3-dependency-tracking-system)**: Complete `genDependencies` algorithm and checkpoint mechanism
*   **[Congestion Modeling](https://deepwiki.com/tenstorrent/tt-npe/4.4-congestion-modeling)**: Device state management and bandwidth derating computation
*   **[Device Models](https://deepwiki.com/tenstorrent/tt-npe/5-device-models)**: `npeDeviceModelIface` interface and concrete implementations

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-npe/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh