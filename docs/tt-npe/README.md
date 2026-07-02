---
project: tt-npe
github: https://github.com/tenstorrent/tt-npe
deepwiki: https://deepwiki.com/tenstorrent/tt-npe
---

# TT-NPE Overview

 Relevant source files

* [README.md](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1)
* [docs/src/api.md](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/api.md?plain=1)
* [docs/src/getting\_started.md](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/getting_started.md?plain=1)
* [img/tt-npe-logo.png](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/img/tt-npe-logo.png)

## Purpose and Scope

This document provides a high-level introduction to **tt-npe** (Tenstorrent Network-on-Chip Performance Estimator), explaining its purpose, architecture, and key concepts. It is intended to help new users and developers understand what tt-npe is, how it works at a conceptual level, and how to begin using it.

For detailed installation instructions, see [Installation and Setup](/tenstorrent/tt-npe/1.1-installation-and-setup). For a hands-on tutorial, see [Quick Start Guide](/tenstorrent/tt-npe/1.2-quick-start-guide). For architectural details, see [System Architecture](/tenstorrent/tt-npe/1.3-system-architecture) and [Core Components](/tenstorrent/tt-npe/2-noc-trace-analysis-pipeline).

**Sources:** [README.md1-33](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L1-L33) [docs/src/getting\_started.md1-25](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/getting_started.md?plain=1#L1-L25)

---

## What is TT-NPE?

**tt-npe** is a lightweight, cycle-accurate Network-on-Chip (NoC) performance estimator and profiler for Tenstorrent Tensix-based devices. It simulates the behavior of NoC workloads to predict performance metrics such as execution time, link utilization, congestion impact, and DRAM bandwidth utilization.

The tool serves two primary purposes:

1. **Performance Profiler**: Analyzes real NoC traces captured from tt-metal device execution to provide detailed performance insights, congestion analysis, and interactive timeline visualizations.
2. **Performance Simulator**: Estimates the performance of synthetic or custom workloads without requiring hardware execution, enabling early-stage performance analysis and optimization.

tt-npe models key aspects of the NoC including:

* Dimension-ordered routing (NOC0 and NOC1)
* Unicast and multicast transfers
* Link and NIU (Network Interface Unit) congestion
* Packet size effects on bandwidth
* Multi-chip fabric communication

**Sources:** [README.md19-35](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L19-L35) [docs/src/getting\_started.md33-36](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/getting_started.md?plain=1#L33-L36)

---

## Key Capabilities

### Trace Analysis and Profiling

tt-npe integrates with the **tt-metal device profiler** to capture and analyze real NoC event traces. When profiling is enabled, the metal runtime records every NoC read/write operation, which tt-npe then processes to:

* Compute actual vs. predicted cycle counts
* Calculate per-device NoC and DRAM bandwidth utilization
* Quantify congestion impact on performance
* Generate interactive timeline visualizations via **ttnn-visualizer**

This capability is referred to as **Profiler Mode** and is the primary use case for production workload analysis.

**Sources:** [README.md35-89](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L35-L89) [docs/src/getting\_started.md33-60](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/getting_started.md?plain=1#L33-L60)

### Synthetic Workload Simulation

tt-npe can simulate workloads defined in three ways:

1. **JSON Workload Files**: Custom workload definitions with explicit phases and transfers
2. **Programmatic API**: Python-based workload construction using `npe.Workload`, `npe.Phase`, and `npe.Transfer` classes
3. **NoC Trace JSON**: Captured from tt-metal profiler with automatic conversion to internal format

This flexibility enables what-if analysis, performance modeling during development, and validation of hardware behavior against simulation.

**Sources:** [README.md37-177](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L37-L177) [docs/src/api.md52-73](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/api.md?plain=1#L52-L73)

### Supported Devices

tt-npe currently supports:

* **Wormhole B0**: 12×10 grid, 30 GB/s link bandwidth
* **Blackhole**: 12×17 grid, 60.9 GB/s link bandwidth
* **Multi-chip configurations**: Coordinated simulation across N-chip systems with fabric routing

**Sources:** [README.md21](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L21-L21)

---

## System Architecture

The following diagram shows the high-level architecture and how major components interact:

**Core Component Hierarchy:**

| Layer | Component | Purpose |
| --- | --- | --- |
| User Interface | `tt_npe.py` | Command-line tool for running simulations |
| User Interface | Python API | Programmatic workload generation via `npe.*` classes |
| Python Layer | `tt_npe_pybind` | pybind11 bindings exposing C++ API to Python |
| Python Layer | `npe_analyze_noc_trace_dir.py` | Batch trace processing and analysis |
| Python Layer | `fabric_post_process.py` | Multi-hop route elaboration for fabric transfers |
| C++ Core | `npeAPI` | Main simulation orchestrator |
| C++ Core | `npeEngine` | Timestep-based performance estimation loop |
| C++ Core | `npeWorkloadIngest` | JSON parsing and workload construction |
| C++ Core | `npeDeviceModel` | Abstract device interface (Wormhole, Blackhole, etc.) |
| C++ Core | `npeStats` | Statistics collection and timeline serialization |

**Sources:** [README.md112-116](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L112-L116) [docs/src/api.md3-8](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/api.md?plain=1#L3-L8)

---

## Data Flow: From Trace to Results

The following diagram illustrates the end-to-end data flow for the primary use case: analyzing NoC traces captured from tt-metal:

**Key Processing Steps:**

1. **Trace Capture**: tt-metal profiler records NoC events to device DRAM during execution
2. **Extraction**: Profiler extracts events to per-device JSON files (`noc_trace_dev0.json`, etc.)
3. **Processing**: `npe_analyze_noc_trace_dir.py` merges traces, groups by operation; `fabric_post_process.py` elaborates multi-chip paths
4. **Simulation**: `npeEngine` executes timestep-based simulation using device model for routing and congestion
5. **Output**: `npeStats` generates timeline files for visualization and console reports with cycle counts and utilization metrics

**Sources:** [README.md77-107](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L77-L107) [docs/src/getting\_started.md38-86](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/getting_started.md?plain=1#L38-L86)

---

## Core Abstractions

### Workload Model: npeWorkload

The central data structure is `npeWorkload`, which represents a complete NoC communication pattern. The hierarchy is:

**Key Concepts:**

* **npeWorkloadTransfer**: Represents a series of back-to-back packets from one source to one or more destinations. Analogous to a single `noc_async_read` or `noc_async_write` call.
* **npeWorkloadPhase**: A collection of transfers that execute concurrently. Most workloads use a single phase containing all transfers.
* **Transfer Groups**: For multi-hop fabric paths, transfers are grouped with `transfer_group_id` and linked via `transfer_group_parent` to create dependency chains.
* **Coord**: Three-dimensional coordinate `{device_id, row, col}` identifying a specific core on a specific device.
* **NocDestination**: Variant type that can be either a single `Coord` (unicast) or `MulticastCoordSet` (multicast to multiple cores).

**Sources:** [README.md158-171](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L158-L171) [docs/src/api.md54-67](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/api.md?plain=1#L54-L67)

### Device Model: npeDeviceModel

The `npeDeviceModel` abstract interface defines the contract for hardware-specific behavior:

**Routing Algorithms:**

* **NOC0**: East-then-South dimension-ordered routing (X-then-Y)
* **NOC1**: North-then-West dimension-ordered routing (Y-then-X)
* **Multicast**: Constructs tree by generating unicast routes to each destination

**Congestion Modeling:**

Device models track per-link and per-NIU demand in `npeDeviceState` using grid-based structures. During each timestep, the `computeCurrentTransferRate` method:

1. Accumulates demand from all active transfers
2. Calculates derating factors based on congestion
3. Adjusts bandwidth via `TransferBandwidthTable` (accounts for packet size effects)

**Sources:** [README.md183-195](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L183-L195)

### Simulation Engine: npeEngine

The `npeEngine` class orchestrates the timestep-based simulation:

```
npeEngine::runSimulation(): 1. Initialize device state via device model 2. Queue all transfers sorted by start time 3. For each timestep: a. Activate transfers that should start b. Compute bandwidth for each active transfer (with congestion) c. Update bytes transferred and progress d. Mark completed transfers e. Update statistics 4. Serialize results to npeStats 
```

**Key Data Structures:**

* **PETransferState**: Per-transfer simulation state tracking bytes remaining, current bandwidth, completion status
* **npeTransferDependencyTracker**: Manages checkpoint-based dependencies between transfer groups
* **npeDeviceState**: Maintains link demand grids and NIU demand grids for congestion computation

For implementation details, see [Simulation Engine (npeEngine)](/tenstorrent/tt-npe/2.1-trace-collection-with-tt-metal-profiler).

**Sources:** [README.md33](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L33-L33)

---

## Usage Modes

### Profiler Mode (Trace Analysis)

**Purpose**: Analyze real workload performance from captured NoC traces.

**Typical Workflow**:

1. Run tt-metal workload with NoC tracing enabled:
2. Traces are automatically analyzed and results added to ops perf report CSV
3. Interactive timelines generated in `output_dir/reports//npe_viz/`
4. Manual batch analysis if needed:

**Integration**: Integrates with `profile_this.py`, automatically adding NoC utilization and DRAM bandwidth columns to performance reports.

**Sources:** [README.md77-107](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L77-L107) [docs/src/getting\_started.md38-86](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/getting_started.md?plain=1#L38-L86)

### Simulation Mode (Synthetic Workloads)

**Purpose**: Estimate performance for custom or synthetic workloads without hardware execution.

**Typical Workflow**:

1. **From JSON File**:
2. **Programmatic Construction** (Python):

**Configuration Options**:

* `--cycles-per-timestep`: Temporal resolution (default: 10 cycles)
* `--cong-model`: Congestion model (default: `lut`, can disable with `none`)
* `-e`: Enable timeline export for visualization
* `--compress-timeline`: Enable zstd compression for timeline files

For details, see [Command Line Interface](/tenstorrent/tt-npe/3.1-workload-data-model) and [Python API](/tenstorrent/tt-npe/3.2-creating-workloads-from-json).

**Sources:** [README.md126-177](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L126-L177) [docs/src/api.md19-73](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/api.md?plain=1#L19-L73)

---

## Getting Started

To begin using tt-npe:

1. **Installation**: Clone the repository and build using `./build-npe.sh`. See [Installation and Setup](/tenstorrent/tt-npe/1.1-installation-and-setup) for detailed instructions.
2. **Environment Setup**: Source the `ENV_SETUP` script to add tt-npe tools to your PATH:
3. **Quick Start**: Run the example workload to verify installation:
4. **Next Steps**:

   * For trace analysis workflow, see [NoC Trace Analysis Pipeline](/tenstorrent/tt-npe/3.4-converting-noc-traces-to-workloads)
   * For programmatic workload generation, see [Creating Workloads](/tenstorrent/tt-npe/3.3-programmatic-workload-construction)
   * For detailed simulation options, see [Command Line Interface](/tenstorrent/tt-npe/3.1-workload-data-model)

**Sources:** [README.md55-75](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L55-L75) [docs/src/getting\_started.md3-24](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/getting_started.md?plain=1#L3-L24)

---

## Related Projects

tt-npe integrates with the broader Tenstorrent software ecosystem:

* **[tt-metalium](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt-metalium)**: Runtime and kernel library; source of NoC traces via device profiler
* **[ttnn-visualizer](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/ttnn-visualizer)**: Interactive timeline visualization tool for tt-npe outputs
* **[tt-forge-fe](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt-forge-fe)**, **[tt-xla](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt-xla)**, **[tt-torch](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt-torch)**, **[tt-mlir](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt-mlir)**: Frontend frameworks that compile to tt-metal

**Sources:** [README.md44-51](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L44-L51)

Refresh this wiki