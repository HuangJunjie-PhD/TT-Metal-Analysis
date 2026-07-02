---
project: tt-tvm
github: https://github.com/tenstorrent/tt-tvm
deepwiki: https://deepwiki.com/tenstorrent/tt-tvm/8-auto-tuning-system
---

# Auto-Tuning System

Relevant source files
*   [CONTRIBUTORS.md](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CONTRIBUTORS.md?plain=1)
*   [NEWS.md](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/NEWS.md?plain=1)
*   [gallery/how_to/deploy_models/deploy_model_on_android.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/deploy_models/deploy_model_on_android.py)
*   [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py)
*   [gallery/how_to/tune_with_autoscheduler/tune_network_mali.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_mali.py)
*   [include/tvm/runtime/crt/crt.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/crt/crt.h)
*   [jvm/README.md](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/jvm/README.md?plain=1)

## Purpose and Scope

The auto-tuning system is TVM's automated performance optimization infrastructure that searches for optimal low-level schedules for tensor computations on specific hardware targets. This document provides an overview of the three generations of auto-tuning systems in TVM: AutoTVM, AutoScheduler (Ansor), and Meta Schedule.

For details on specific systems, see:

*   [AutoTVM](https://deepwiki.com/tenstorrent/tt-tvm/8.1-autotvm) - Template-based tuning requiring manual schedule templates
*   [AutoScheduler](https://deepwiki.com/tenstorrent/tt-tvm/8.2-autoscheduler) - Template-free tuning with automatic search space generation
*   [Meta Schedule](https://deepwiki.com/tenstorrent/tt-tvm/8.3-meta-schedule) - Advanced scheduling with extensible design
*   [RPC Infrastructure](https://deepwiki.com/tenstorrent/tt-tvm/8.4-rpc-infrastructure) - Distributed measurement across devices
*   [Tuning Workflows](https://deepwiki.com/tenstorrent/tt-tvm/8.5-tuning-workflows) - End-to-end tuning procedures

For information about TIR scheduling primitives used by these systems, see [Schedule API](https://deepwiki.com/tenstorrent/tt-tvm/5.3-schedule-api).

* * *

## Overview

Auto-tuning addresses the challenge that optimal implementations of tensor computations vary dramatically across hardware platforms (CPUs, GPUs, DSPs) and workload shapes. Manually optimizing schedules for every operator on every target is impractical. The auto-tuning system automates this by:

1.   **Task Extraction**: Partitioning a neural network into small subgraphs (tasks)
2.   **Search Space Construction**: Generating candidate schedules using scheduling primitives
3.   **Performance Measurement**: Compiling and executing candidates on target hardware
4.   **Best Schedule Selection**: Recording the fastest implementation for each task

The system uses a **task scheduler** that allocates measurement trials across tasks based on their predicted impact on end-to-end execution time, approximated as `sum(latency[task] * weight[task])`.

Sources: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 20-45](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L20-L45)

* * *

## Three Generations of Auto-Tuning

| System | Approach | Search Space | Key Feature |
| --- | --- | --- | --- |
| **AutoTVM** | Template-based | Manual schedule templates with tunable knobs | Requires operator-specific templates |
| **AutoScheduler** | Template-free | Automatic from compute DAG | No templates needed, uses TOPI declarations |
| **Meta Schedule** | Advanced | Programmable with schedule rules | Extensible framework, better performance |

### Evolution Timeline

**Sources**: [NEWS.md 26-27](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/NEWS.md?plain=1#L26-L27)[NEWS.md 172-180](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/NEWS.md?plain=1#L172-L180)[gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 42-45](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L42-L45)

* * *

## Architecture: Auto-Tuning in the Compilation Pipeline

**Key Classes and Functions**:

| Component | Code Entity | Purpose |
| --- | --- | --- |
| Task Extraction | `auto_scheduler.extract_tasks()` | Partition network into tunable subgraphs |
| Task Scheduler | `auto_scheduler.TaskScheduler` | Allocate measurement trials across tasks |
| Builder | `auto_scheduler.LocalBuilder` | Compile candidate schedules |
| Runner | `auto_scheduler.RPCRunner` | Execute and measure on target device |
| Log Management | `auto_scheduler.RecordToFile` | Save measurement records |
| Apply Results | `auto_scheduler.ApplyHistoryBest` | Load best schedules during compilation |

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 258-279](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L258-L279)[gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 303-340](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L303-L340)

* * *

## Core Concepts

### Search Tasks and Workloads

A **search task** represents a tunable computational subgraph extracted from a network. Each task has:

*   **Workload Key**: Unique identifier for the computation pattern
*   **Compute DAG**: Tensor expression defining the computation
*   **Weight**: Number of times this subgraph appears in the network

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 258-279](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L258-L279)

### Measurement Infrastructure

**Builder Types**:

*   `LocalBuilder`: Compiles schedules on the local machine
*   `LocalBuilder(build_func="ndk")`: Cross-compilation using Android NDK

**Runner Types**:

*   `LocalRunner`: Measures on local device
*   `RPCRunner`: Measures on remote device via RPC

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 306-319](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L306-L319)[gallery/how_to/tune_with_autoscheduler/tune_network_mali.py 228-238](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_mali.py#L228-L238)

### Tuning Options

The `TuningOptions` class configures the tuning process:

| Option | Purpose | Typical Value |
| --- | --- | --- |
| `num_measure_trials` | Total measurements to perform | `800 * len(tasks)` |
| `builder` | How to compile schedules | `LocalBuilder()` or with `"ndk"` |
| `runner` | How to measure performance | `RPCRunner()` for remote devices |
| `measure_callbacks` | Actions after measurement | `[RecordToFile(log_file)]` |

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 306-319](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L306-L319)

### Task Scheduler Output

During tuning, the task scheduler prints a table showing progress:

```
----------------------------------------------------------------------
------------------------------  [ Task Scheduler ]
----------------------------------------------------------------------
|  ID  | Latency (ms) | Speed (GFLOPS) | Trials |
-------------------------------------------------
|    0 |        0.013 |           0.31 |     64 |
|    1 |        0.845 |           2.43 |    448 |
|    2 |        0.046 |          -0.00 |     64 |
...
-------------------------------------------------
 Estimated total latency: 38.347 ms      Trials: 19992   Used time : 19260 s     Next ID: 3
```

The last line shows:

*   **Estimated total latency**: Weighted sum approximating end-to-end execution time
*   **Trials**: Total measurements performed
*   **Used time**: Wall-clock time spent tuning
*   **Next ID**: Task to tune next

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 367-416](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L367-L416)

* * *

## Common Workflow

### Step 1: Define Target and Network

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 232-256](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L232-L256)

### Step 2: Extract Search Tasks

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 268-279](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L268-L279)

### Step 3: Configure Tuning

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 303-320](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L303-L320)

### Step 4: Run Tuning

This launches the tuning process which:

1.   Generates candidate schedules for each task
2.   Compiles candidates using the builder
3.   Measures performance using the runner
4.   Records results to the log file
5.   Prioritizes tasks based on impact on total latency

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 321](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L321-L321)

### Step 5: Compile with Best Schedules

The `ApplyHistoryBest` context manager:

*   Reads the tuning log file
*   Loads the best measured schedule for each workload
*   Injects these schedules during compilation

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 323-329](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L323-L329)

### Step 6: Deploy and Execute

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 343-356](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L343-L356)

* * *

## Tuning Log Format

Tuning logs are JSON files containing measurement records. Each record includes:

Log files support:

*   **Distillation**: `python3 -m tvm.auto_scheduler.measure_record --mode distill -i log.json` removes non-optimal records
*   **Resume**: Pass `load_log_file=log_file` to `TaskScheduler` to continue from previous tuning
*   **Analysis**: Query history to understand performance characteristics

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 428-443](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L428-L443)

* * *

## RPC Infrastructure for Distributed Tuning

### RPC Setup Commands

**Start RPC Tracker** (on host machine):

**Register Device** (on each device):

**Query Registered Devices**:

Example output:

```
Queue Status
----------------------------------
key          total  free  pending
----------------------------------
rasp4b-64    11     11    0
android      2      2     0
rk3399       2      2     0
----------------------------------
```

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 154-221](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L154-L221)[gallery/how_to/tune_with_autoscheduler/tune_network_mali.py 151-161](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_mali.py#L151-L161)

* * *

## Hardware Parameters

For GPU tuning, hardware parameters inform the search space:

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_mali.py 182-206](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_mali.py#L182-L206)

* * *

## Integration with Compilation

The auto-tuning system integrates with TVM's compilation pipeline through:

### 1. Pass Context Configuration

The `relay.backend.use_auto_scheduler` config flag enables AutoScheduler dispatch during Relay compilation.

### 2. TECompiler Integration

The `TECompiler` (Tensor Expression Compiler) queries tuning logs when lowering Relay operators to TIR:

*   Checks if a tuned schedule exists for the workload
*   If found, applies the recorded schedule
*   If not found, falls back to default TOPI schedules

### 3. Build Function Selection

Builders can be customized for cross-compilation:

*   `build_func="default"`: Standard compilation
*   `build_func="ndk"`: Android NDK cross-compilation
*   Custom build functions for specialized targets

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 323-329](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L323-L329)[gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 308](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L308-L308)

* * *

## Best Practices

### Tuning Budget

Set `num_measure_trials` based on:

*   **Quick test**: `200` trials for demonstration
*   **Production**: `800 * len(tasks)` for convergence
*   Example: ResNet-50 has ~29 tasks → 20,000 trials recommended

### CPU-Intensive Operations

Auto-tuning is CPU-intensive because it:

*   Compiles many candidate programs
*   Extracts features from compute DAGs
*   Requires high-performance CPU with many cores for faster search

### Log Management

*   **Distill logs**: Remove suboptimal records to reduce file size
*   **Resume tuning**: Continue from previous logs with `load_log_file`
*   **Version control**: Keep logs for reproducibility

### Parallel Measurement

Use multiple devices registered to RPC Tracker to parallelize measurements:

*   Register all available devices to the tracker
*   RPC runner automatically distributes work
*   Significantly accelerates tuning time

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 286-291](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L286-L291)[gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 428-443](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L428-L443)

* * *

## Error Handling

During tuning, you may see:

*   **"dmlc::Error" / "tvm::Error"**: Auto-scheduler tries invalid schedules; safe to ignore if tuning continues
*   **Timeout errors**: Increase `timeout` parameter in `RPCRunner`
*   **Compilation failures**: Some candidates fail to compile; isolated from main process

### Early Termination

You can terminate tuning early (Ctrl+C). As long as at least one valid schedule exists per task in the log file, compilation will succeed.

**Sources**: [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py 413-425](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py#L413-L425)

* * *

## Relationship to Other Systems

The auto-tuning system:

*   **Consumes**: Relay IR from [Frontend Converters](https://deepwiki.com/tenstorrent/tt-tvm/3-frontend-converters)
*   **Operates on**: TensorIR using [Schedule API](https://deepwiki.com/tenstorrent/tt-tvm/5.3-schedule-api) primitives
*   **Outputs**: Optimized schedules for [Code Generation Backends](https://deepwiki.com/tenstorrent/tt-tvm/6-code-generation-backends)
*   **Integrates with**: [Runtime and Execution](https://deepwiki.com/tenstorrent/tt-tvm/7-runtime-and-execution) via compiled libraries

**Sources**: High-level architecture Diagram 1, Diagram 5

Dismiss
Refresh this wiki

Enter email to refresh