---
project: polaris
github: https://github.com/tenstorrent/polaris
deepwiki: https://deepwiki.com/tenstorrent/polaris/2-core-simulation-system)
---

# Core Simulation System

Relevant source files
*   [polaris.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py)
*   [ttsim/config/simconfig.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/simconfig.py)
*   [ttsim/config/validators.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/validators.py)
*   [ttsim/graph/wl_graph.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/graph/wl_graph.py)
*   [ttsim/ops/op.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/ops/op.py)

This document covers the heart of the Polaris simulation framework, including the main orchestrator (`polaris.py`) and the core TTSIM simulation infrastructure. The core simulation system is responsible for executing workloads on simulated hardware architectures and collecting performance statistics.

For information about specific workload models and neural network implementations, see [Workload Models and Examples](https://deepwiki.com/tenstorrent/polaris/4-workload-models-and-examples). For details on performance analysis and benchmarking tools, see [Performance Analysis and Benchmarking](https://deepwiki.com/tenstorrent/polaris/3-performance-analysis-and-benchmarking).

## Main Orchestrator

The `polaris.py` script serves as the primary entry point and orchestrates the entire simulation process. It coordinates workload execution across different hardware configurations and manages the complete simulation lifecycle.

### Core Orchestration Flow

**Sources:**[polaris.py 259-307](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L259-L307)[polaris.py 559-612](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L559-L612)[polaris.py 405-557](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L405-L557)

### Key Data Structures

The orchestrator manages several key data structures during execution:

| Structure | Type | Purpose |
| --- | --- | --- |
| `_WLG` | `dict[tuple, tuple]` | Workload graphs table mapping (wlg, wln, wli, wlb) to (wlobj, wlgraph) |
| `device_list` | `list[tuple]` | Device and frequency combinations |
| `workload_list` | `list[tuple]` | Workload configurations with batch sizes |
| `summary_stats` | `list[dict]` | Aggregated performance statistics |

**Sources:**[polaris.py 184-233](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L184-L233)[polaris.py 322-362](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L322-L362)

## TTSIM Framework

The TTSIM framework provides the core simulation infrastructure, including operations, computational graphs, and device modeling capabilities.

### Operation System Architecture

**Sources:**[ttsim/ops/op.py 205-308](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/ops/op.py#L205-L308)[ttsim/ops/op.py 310-341](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/ops/op.py#L310-L341)[ttsim/ops/op.py 342-388](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/ops/op.py#L342-L388)

### Graph Construction and Execution

The `WorkloadGraph` class manages the computational graph representation and execution:

**Sources:**[ttsim/graph/wl_graph.py 24-81](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/graph/wl_graph.py#L24-L81)[ttsim/graph/wl_graph.py 123-128](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/graph/wl_graph.py#L123-L128)[ttsim/graph/wl_graph.py 134-158](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/graph/wl_graph.py#L134-L158)

### Performance Calculation System

Each `SimOp` implements performance calculation through the `get_perf_counts()` method and device execution:

**Sources:**[ttsim/ops/op.py 259-277](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/ops/op.py#L259-L277)[ttsim/ops/op.py 288-307](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/ops/op.py#L288-L307)

## Configuration System

The configuration system provides structured validation and management of simulation parameters through Pydantic models.

### Configuration Model Hierarchy

**Sources:**[ttsim/config/validators.py 265-280](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/validators.py#L265-L280)[ttsim/config/validators.py 231-264](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/validators.py#L231-L264)[ttsim/config/validators.py 309-311](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/validators.py#L309-L311)

### Device and Hardware Models

The configuration system includes comprehensive device modeling:

**Sources:**[ttsim/config/simconfig.py 398-481](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/simconfig.py#L398-L481)[ttsim/config/simconfig.py 333-348](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/simconfig.py#L333-L348)[ttsim/config/simconfig.py 350-371](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/simconfig.py#L350-L371)

## Workload Processing Pipeline

The core simulation system processes workloads through a structured pipeline that handles both TTSIM and ONNX workloads:

**Sources:**[polaris.py 184-233](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L184-L233)[ttsim/graph/wl_graph.py 134-158](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/graph/wl_graph.py#L134-L158)[ttsim/graph/wl_graph.py 244-252](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/graph/wl_graph.py#L244-L252)

## Statistics Collection and Aggregation

The system collects detailed performance statistics at multiple levels:

### Per-Operation Statistics

Each operation tracks comprehensive performance metrics in its `perf_stats` dictionary:

| Metric | Type | Description |
| --- | --- | --- |
| `inElems` | `int` | Count of input elements |
| `outElems` | `int` | Count of output elements |
| `inBytes` | `int` | Input size in bytes |
| `outBytes` | `int` | Output size in bytes |
| `instrs` | `dict[str, int]` | Instruction type to count mapping |
| `compute_cycles` | `float` | Compute cycles required |
| `mem_rd_cycles` | `float` | Memory read cycles |
| `mem_wr_cycles` | `float` | Memory write cycles |
| `cycles` | `float` | Total execution cycles |
| `msecs` | `float` | Execution time in milliseconds |

**Sources:**[ttsim/ops/op.py 259-277](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/ops/op.py#L259-L277)[polaris.py 450-513](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L450-L513)

### Summary Statistics

The `ReducedStats` class aggregates operation-level statistics into workload-level summaries:

**Sources:**[polaris.py 49-136](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L49-L136)[polaris.py 547-556](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L547-L556)

Dismiss
Refresh this wiki

Enter email to refresh