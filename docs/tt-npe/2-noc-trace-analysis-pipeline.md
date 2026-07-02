---
project: tt-npe
github: https://github.com/tenstorrent/tt-npe
deepwiki: https://deepwiki.com/tenstorrent/tt-npe/2-noc-trace-analysis-pipeline
---

# NoC Trace Analysis Pipeline

Relevant source files
*   [tt_npe/.gitignore](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/.gitignore)
*   [tt_npe/cpp/include/npeEngine.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeEngine.hpp)
*   [tt_npe/cpp/src/npeEngine.cpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp)
*   [tt_npe/cpp/src/npeStats.cpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp)
*   [tt_npe/py/util/npe_analyze_noc_trace_dir.py](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py)

## Purpose and Scope

This document provides an overview of the complete pipeline for analyzing real hardware NoC traces collected from Tenstorrent devices. The pipeline transforms raw profiling data from tt-metal executions into performance insights and visualizations. It covers the data flow from trace collection through preprocessing, batch simulation, and output generation.

For specific implementation details, see:

*   Trace collection procedures: [Trace Collection with tt-metal Profiler](https://deepwiki.com/tenstorrent/tt-npe/2.1-trace-collection-with-tt-metal-profiler)
*   Preprocessing and fabric routing: [Trace Preprocessing and Fabric Routing](https://deepwiki.com/tenstorrent/tt-npe/2.2-trace-preprocessing-and-fabric-routing)
*   Batch processing implementation: [Batch Trace Analysis](https://deepwiki.com/tenstorrent/tt-npe/2.3-batch-trace-analysis)
*   Output format specifications: [Statistics and Visualization Output](https://deepwiki.com/tenstorrent/tt-npe/2.4-statistics-and-visualization-output)

## Pipeline Overview

The NoC trace analysis pipeline consists of four major stages that convert hardware profiling data into actionable performance metrics:

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 1-457](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L1-L457)[tt_npe/cpp/src/npeEngine.cpp 172-200](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L172-L200)[tt_npe/cpp/src/npeStats.cpp 799-876](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L799-L876)

## Data Flow and Transformation

### Trace Collection to Workload Format

The pipeline begins with raw NoC event traces captured during actual hardware execution. Each device produces its own trace file containing timestamped NoC transfer events:

Each merged trace file represents a single operation execution across all devices in the system. The format includes source/destination coordinates, byte counts, NoC types, and timing information that can be directly ingested by the NPE simulator.

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 402-414](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L402-L414)

### Batch Analysis Workflow

The `npe_analyze_noc_trace_dir.py` script orchestrates parallel analysis of multiple operations:

The script uses Python's `multiprocessing.Pool` to parallelize NPE simulations across all available CPU cores. Each worker processes one operation's merged trace independently.

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 140-151](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L140-L151)[tt_npe/py/util/npe_analyze_noc_trace_dir.py 205-231](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L205-L231)[tt_npe/py/util/npe_analyze_noc_trace_dir.py 420-435](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L420-L435)

### Simulation and Statistics Generation

For each operation, the NPE engine runs a cycle-accurate simulation:

The simulation tracks each transfer's bandwidth, route congestion, and completion time. Statistics are accumulated both per-timestep and as aggregated summaries.

**Sources:**[tt_npe/cpp/src/npeEngine.cpp 202-361](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L202-L361)[tt_npe/cpp/src/npeStats.cpp 42-85](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L42-L85)

## Key Components and Their Roles

### Trace Grouping and Operation Identification

The pipeline supports two grouping strategies based on the trace source:

| Grouping Mode | Use Case | ID Extraction | Function |
| --- | --- | --- | --- |
| **TTNN Mode** | Standard TTNN operations | `ttnn_op_id` from filename | `group_traces_ttnn()` |
| **Metal Mode** | Raw tt-metal programs | `program_runtime_id` | `group_traces_metal()` |

Both modes extract operation identifiers from trace filenames with pattern:

```
noc_trace_dev{device_id}_{op_name}_ID{id}_traceID{trace_id}.json
```

The `OpUID` class encapsulates both the TTNN operation ID and optional trace replay session ID for uniquely identifying operations across multiple trace collection runs.

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 126-139](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L126-L139)[tt_npe/py/util/npe_analyze_noc_trace_dir.py 277-319](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L277-L319)[tt_npe/py/util/npe_analyze_noc_trace_dir.py 333-362](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L333-L362)

### NPE Configuration for Trace Analysis

The `run_npe()` function configures the NPE simulator specifically for trace analysis:

`cfg = npe.Config()cfg.device_name = device_name                    # from topologycfg.workload_json_filepath = workload_file       # merged tracecfg.workload_is_noc_trace = True                # trace format flagcfg.cycles_per_timestep = 32                     # simulation granularitycfg.emit_timeline_file = True                    # for visualizationcfg.compress_timeline_output_file = compress     # optional compressioncfg.topology_json = topology_json_file          # for multi-chip`
The critical setting is `workload_is_noc_trace = True`, which directs the workload parser to interpret the JSON as trace events rather than synthetic transfer specifications.

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 205-220](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L205-L220)

### Statistics Accumulation

The `Stats` class maintains performance metrics across all analyzed operations:

| Metric | Calculation | Purpose |
| --- | --- | --- |
| `getCycles()` | Sum of golden cycles | Total execution time |
| `getWeightedAvgLinkUtil()` | Σ(util × cycles) / total_cycles | Time-weighted NoC utilization |
| `getWeightedAvgDramBWUtil()` | Σ(dram_util × cycles) / total_cycles | Time-weighted DRAM usage |
| `getWeightedAvgEthBWUtil()` | Σ(eth_util × cycles) / total_cycles | Time-weighted fabric usage |
| `getErrorPercentiles()` | Sorted cycle prediction errors | Accuracy distribution |

Each operation contributes a `Datapoint` containing its `npeStats::deviceStats` result, indexed by operation UID and device ID.

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 42-124](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L42-L124)

## Output Formats

### Operations Performance Table

The batch analyzer generates a summary table displaying per-operation metrics:

```
---------------------------------------------------------------------------------------------------------
Opname                                    Op ID  Metal Trace ID    NoC Util  McastWr NoC Util  DRAM BW Util  ...
---------------------------------------------------------------------------------------------------------
ttnn_all_gather                              42               -        45.2%              12.3%        67.8%  ...
ttnn_matmul                                  45               -        78.9%              34.1%        92.1%  ...
...
```

The table includes weighted averages across all operations and accuracy statistics when `--show_accuracy_stats` is enabled.

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 232-262](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L232-L262)

### Timeline Visualization Files

For each operation, the pipeline generates `.npeviz` files containing:

*   Transfer definitions with routes and timing
*   Per-timestep link demand and utilization
*   Zone annotations for hierarchical grouping
*   Chip topology for multi-device workloads

The timeline format supports both legacy (v0) and current (v1) schemas. Files can optionally be compressed with zstd (`.npeviz.zst`) and split into smaller chunks for large workloads.

**Sources:**[tt_npe/cpp/src/npeStats.cpp 799-876](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L799-L876)

### Manifest File

The `manifest.json` file maps operation identifiers to their timeline files:

`[  {    "global_call_count": 42,    "file": "ttnn_all_gather_ID42.npeviz"  },  {    "global_call_count": 45,    "file": "ttnn_matmul_ID45.npeviz.zst"  }]`
This enables the ttnn-visualizer to locate and load the correct timeline for each operation in chronological order.

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 437-442](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L437-L442)

## Integration Points

### tt-metal Profiler Integration

Traces are collected by setting environment variables before running tt-metal programs:

`TT_METAL_DEVICE_PROFILER=1           # Enable profilerTT_METAL_PROFILER_SYNC=1             # Synchronize devices (critical for multi-chip)`
The profiler writes per-device trace files to a directory that serves as input to `npe_analyze_noc_trace_dir.py`.

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 377-381](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L377-L381)

### ttnn-visualizer Integration

The pipeline's output directory structure is designed for direct consumption by ttnn-visualizer:

```
npe_viz/
├── manifest.json                    # operation index
├── ttnn_all_gather_ID42.npeviz     # timeline for op 42
├── ttnn_matmul_ID45.npeviz.zst     # compressed timeline
└── ...
```

The visualizer reads the manifest to populate its operation list and loads timeline files on-demand for interactive exploration.

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 383-387](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L383-L387)[tt_npe/py/util/npe_analyze_noc_trace_dir.py 445-446](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L445-L446)

### Multi-chip Topology Handling

For multi-chip systems, the `topology.json` file describes fabric connectivity and is used in two places:

1.   `fabric_post_process.py` - elaborates cross-chip transfer routes
2.   Timeline serialization - embeds chip coordinates for 3D visualization

The topology determines the `device_name` (e.g., "T3000", "N150") passed to NPE's device model factory.

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 403-405](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L403-L405)[tt_npe/cpp/src/npeStats.cpp 396-448](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L396-L448)

## Execution Flow Summary

The complete pipeline execution follows this sequence:

1.   **Discovery Phase**: Scan directory for `noc_trace*.json` files, extract metadata from filenames, group by operation UID
2.   **Preprocessing Phase**: For each operation group, invoke `fabric_post_process.py` to merge per-device traces with topology elaboration
3.   **Simulation Phase**: Spawn parallel worker processes, each running `npeEngine::runPerfEstimation()` on a merged trace
4.   **Collection Phase**: Aggregate `npeStats` results into a `Stats` object tracking all operations
5.   **Output Phase**: Generate summary table, write timeline files, create manifest for visualization

The pipeline is designed for throughput - analyzing hundreds of operations in minutes by leveraging parallel processing and efficient C++ simulation.

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 364-448](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L364-L448)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-npe/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh