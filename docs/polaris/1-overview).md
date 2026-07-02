---
project: polaris
github: https://github.com/tenstorrent/polaris
deepwiki: https://deepwiki.com/tenstorrent/polaris/1-overview)
---

# Overview

Relevant source files
*   [.github/spdxchecker-ignore.yml](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/.github/spdxchecker-ignore.yml)
*   [.github/workflows/license_check.yml](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/.github/workflows/license_check.yml)
*   [README.md](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/README.md?plain=1)
*   [doc/polaris_logo.png](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/doc/polaris_logo.png)
*   [polaris.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py)
*   [ttsim/config/validators.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/validators.py)

This document provides a comprehensive overview of the Polaris repository, an AI workload performance simulation framework. This page covers the system's architecture, core components, and operational flow. For detailed information about specific subsystems, refer to the specialized wiki pages: configuration management ([2.3](https://deepwiki.com/tenstorrent/polaris/2.3-configuration-management)), performance analysis ([3](https://deepwiki.com/tenstorrent/polaris/3-performance-analysis-and-benchmarking)), workload models ([4](https://deepwiki.com/tenstorrent/polaris/4-workload-models-and-examples)), and development infrastructure ([5](https://deepwiki.com/tenstorrent/polaris/5-development-and-testing)).

## Purpose and Scope

Polaris is a high-level simulator designed for performance analysis of AI architectures running various workloads. The system takes three primary inputs: AI workload specifications, hardware architecture configurations, and workload-to-architecture mappings. It converts these inputs into an internal directed acyclic graph (DAG) representation where nodes represent computational or communication operators and edges represent data flow. The framework then executes various graph transformations and schedules the DAG on target hardware backends for comprehensive performance analysis.

The simulation produces detailed performance metrics including execution cycles, memory usage, resource bottlenecks, and throughput projections across different hardware configurations and workload scenarios.

## System Architecture

The Polaris framework consists of several interconnected subsystems that work together to provide comprehensive AI workload performance simulation capabilities.

### Core System Components

**Sources:**[polaris.py 1-631](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L1-L631)[README.md 37-38](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/README.md?plain=1#L37-L38)[ttsim/config/validators.py 265-280](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/validators.py#L265-L280)

### Data Flow Architecture

**Sources:**[polaris.py 322-361](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L322-L361)[polaris.py 559-612](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L559-L612)[polaris.py 405-556](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L405-L556)

## Key System Components

### Main Orchestrator

The `polaris.py` file serves as the primary entry point and orchestrator for the entire simulation framework. It implements the `main()` function that coordinates all subsystems and manages the simulation lifecycle.

| Component | Function | Description |
| --- | --- | --- |
| `setup_cmdline_args()` | Argument parsing | Processes command-line arguments and configuration options |
| `polaris()` | Main simulation | Orchestrates the complete simulation workflow |
| `execute_wl_on_dev()` | Execution engine | Runs workloads on device models and collects performance data |
| `RangeArgument` | Parameter sweeping | Handles frequency and batch size range specifications |
| `ReducedStats` | Performance analysis | Summarizes detailed performance statistics |

**Sources:**[polaris.py 259-306](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L259-L306)[polaris.py 559-612](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L559-L612)[polaris.py 405-556](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L405-L556)

### Configuration System

The framework uses a YAML-based configuration system with three primary specification types:

*   **Workload Specification (`--wlspec`)**: Defines AI models, batch sizes, and operator configurations
*   **Architecture Specification (`--archspec`)**: Specifies hardware configurations, memory hierarchy, and compute resources
*   **Workload Mapping Specification (`--wlmapspec`)**: Maps operators to data types and computational resources

The configuration parsers validate input using Pydantic models defined in `ttsim/config/validators.py`.

**Sources:**[polaris.py 577](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L577-L577)[README.md 108-130](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/README.md?plain=1#L108-L130)[ttsim/config/validators.py 21-26](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/validators.py#L21-L26)

### Performance Analysis Framework

The system provides comprehensive performance analysis capabilities through multiple specialized components:

| Component | Purpose | Key Functions |
| --- | --- | --- |
| `TTSimHLWlDevRunPerfStats` | Detailed operator statistics | Per-operator performance metrics |
| `TTSimHLRunSummary` | High-level run summary | Aggregate performance across workloads |
| `ReducedStats` | Summary statistics | Resource bottleneck analysis and projections |

**Sources:**[ttsim/config/validators.py 265-280](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/validators.py#L265-L280)[ttsim/config/validators.py 309-311](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/config/validators.py#L309-L311)[polaris.py 49-135](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L49-L135)

## Input and Output Flow

### Input Processing

The framework accepts three types of input specifications processed through dedicated parsers:

**Sources:**[polaris.py 337-361](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L337-L361)[polaris.py 322-335](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L322-L335)[polaris.py 577](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L577-L577)

### Output Generation

The framework supports multiple output formats and generates both detailed statistics and summary reports:

**Sources:**[polaris.py 392-404](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L392-L404)[polaris.py 568-596](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L568-L596)[README.md 148-157](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/README.md?plain=1#L148-L157)

## Core Simulation Process

The simulation executes through a well-defined pipeline that processes workloads against hardware architectures:

### Simulation Pipeline

**Sources:**[polaris.py 184-232](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L184-L232)[polaris.py 439-449](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L439-L449)[polaris.py 547-553](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/polaris.py#L547-L553)

This overview provides the foundation for understanding how Polaris orchestrates AI workload performance simulation across different hardware architectures. The detailed implementation of each subsystem is covered in the specialized wiki pages referenced at the beginning of this document.

Dismiss
Refresh this wiki

Enter email to refresh