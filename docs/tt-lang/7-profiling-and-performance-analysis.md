---
project: tt-lang
github: https://github.com/tenstorrent/tt-lang
deepwiki: https://deepwiki.com/tenstorrent/tt-lang/7-profiling-and-performance-analysis
---

# Profiling and Performance Analysis

Relevant source files
*   [include/ttlang/Dialect/TTL/Passes.td](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td)
*   [lib/Dialect/TTL/Pipelines/TTLPipelines.cpp](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Pipelines/TTLPipelines.cpp)
*   [lib/Dialect/TTL/Transforms/CMakeLists.txt](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/CMakeLists.txt)
*   [lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp)
*   [python/ttl/_src/auto_profile.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py)
*   [python/ttl/_src/signpost_profile.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/signpost_profile.py)
*   [python/ttl/_src/ttl_ast.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/ttl_ast.py)
*   [python/ttl/ttl_api.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py)
*   [test/me2e/builder/pipeline.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/me2e/builder/pipeline.py)
*   [test/python/auto_profile_signposts.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/auto_profile_signposts.py)

**Purpose**: This page describes the profiling tools and performance analysis capabilities in tt-lang for measuring kernel execution on Tenstorrent hardware. It covers auto-profiling for per-line cycle counts, user-defined signposts, trace visualization with Perfetto, and NOC/DMA traffic analysis.

**Scope**: This page focuses on profiling kernel performance on real hardware. For information about the functional simulator for correctness testing without hardware, see [Simulation Framework](https://deepwiki.com/tenstorrent/tt-lang/6-simulation-framework). For general kernel execution concepts, see [TTNN Integration](https://deepwiki.com/tenstorrent/tt-lang/5-ttnn-integration).

* * *

## Profiling Modes Overview

tt-lang provides several complementary profiling modes, each enabled by environment variables:

| Mode | Environment Variable | Output | Use Case |
| --- | --- | --- | --- |
| **Auto-Profiling** | `TTLANG_AUTO_PROFILE=1` | Per-line cycle counts with source code | Identify hotspots in kernel code |
| **Signpost Profiling** | `TTLANG_SIGNPOST_PROFILE=1` | User-defined zone timing | Measure specific code regions |
| **Trace Visualization** | `TTLANG_PERF_SERV=1` | Perfetto timeline view | Visualize thread concurrency |

All profiling modes require hardware execution and the TT-Metal device profiler:

`export TT_METAL_DEVICE_PROFILER=1export TT_METAL_PROFILER_MID_RUN_DUMP=1  # For accurate dumps`
**Sources**: [python/ttl/_src/auto_profile.py 8-11](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L8-L11)[python/ttl/_src/signpost_profile.py 8-11](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/signpost_profile.py#L8-L11)[test/python/auto_profile_signposts.py 20-21](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/auto_profile_signposts.py#L20-L21)

* * *

## Profiling System Architecture

The profiling system spans the compilation pipeline, runtime execution, and post-execution analysis:

**Diagram: Profiling System Data Flow**

The system operates in four phases:

1.   **Compilation**: The `TTLGenericCompiler`[python/ttl/_src/ttl_ast.py 128](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/ttl_ast.py#L128-L128) emits signpost operations and tracks source line mappings via `SourceLineMapper`[python/ttl/_src/auto_profile.py 57-63](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L57-L63)
2.   **MLIR Transformation**: `SignpostOp`[lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp 149](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp#L149-L149) operations are lowered to C++ profiler instrumentation via the `TTLLowerSignpostToEmitCPass`[lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp 142-143](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp#L142-L143)
3.   **Runtime**: Device profiler captures zone timestamps during kernel execution, dumping to `profile_log_device.csv`[python/ttl/_src/auto_profile.py 126-129](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L126-L129)
4.   **Analysis**: Post-execution tools like `parse_device_profile_csv()`[python/ttl/_src/auto_profile.py 126-129](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L126-L129) and `parse_signpost_zones()`[python/ttl/_src/signpost_profile.py 28-30](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/signpost_profile.py#L28-L30) parse profiler data and generate reports.

**Sources**: [python/ttl/_src/ttl_ast.py 128](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/ttl_ast.py#L128-L128)[lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp 142-143](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp#L142-L143)[python/ttl/_src/auto_profile.py 57-63](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L57-L63)[python/ttl/_src/auto_profile.py 126-129](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L126-L129)[python/ttl/_src/signpost_profile.py 28-30](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/signpost_profile.py#L28-L30)

* * *



```mermaid
graph TB
    subgraph "Compilation Phase"
        [TTLGenericCompiler] -->|"line-based signposts"| [is_auto_profile_enabled]
        [is_auto_profile_enabled] -->|"track mapping"| [SourceLineMapper]
    end
    
    subgraph "MLIR Transformation"
        [SignpostOp] -->|"pass"| [TTLLowerSignpostToEmitCPass]
        [TTLLowerSignpostToEmitCPass] -->|"generate"| [DeviceZoneScopedN]
    end
    
    subgraph "Runtime Execution"
        [DeviceZoneScopedN] -->|"execute on"| [DeviceProfiler]
        [DeviceProfiler] -->|"dump"| [profile_log_device_csv]
    end
    
    subgraph "Analysis & Visualization"
        [profile_log_device_csv] -->|"read"| [parse_device_profile_csv]
        [profile_log_device_csv] -->|"read"| [parse_signpost_zones]
        [parse_device_profile_csv] -->|"generate"| [print_profile_report]
        [SourceLineMapper] -->|"source mapping"| [print_profile_report]
    end
```

**Diagram: Profiling System Data Flow**

The system operates in four phases:
1. **Compilation**: The `TTLGenericCompiler` [python/ttl/_src/ttl_ast.py:128]() emits signpost operations and tracks source line mappings via `SourceLineMapper` [python/ttl/_src/auto_profile.py:57-63]().
2. **MLIR Transformation**: `SignpostOp` [lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp:149]() operations are lowered to C++ profiler instrumentation via the `TTLLowerSignpostToEmitCPass` [lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp:142-143]().
3. **Runtime**: Device profiler captures zone timestamps during kernel execution, dumping to `profile_log_device.csv` [python/ttl/_src/auto_profile.py:126-129]().
4. **Analysis**: Post-execution tools like `parse_device_profile_csv()` [python/ttl/_src/auto_profile.py:126-129]() and `parse_signpost_zones()` [python/ttl/_src/signpost_profile.py:28-30]() parse profiler data and generate reports.
```
## Auto-Profiling System

Auto-profiling automatically instruments every source line and operation with profiler zones, providing detailed cycle counts without manual instrumentation. For details, see [Auto-Profiling System](https://deepwiki.com/tenstorrent/tt-lang/7.1-auto-profiling-system).

### Enabling Auto-Profiling

`export TTLANG_AUTO_PROFILE=1export TT_METAL_DEVICE_PROFILER=1 python my_kernel.py`
The profiler report is printed after kernel execution via `print_profile_report()`[python/ttl/_src/auto_profile.py 185-193](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L185-L193)

**Sources**: [python/ttl/_src/auto_profile.py 52-54](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L52-L54)[python/ttl/_src/auto_profile.py 185-193](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L185-L193)

### Signpost Generation Strategy

Auto-profiling uses a naming convention to correlate hardware timestamps with source lines. Each source line gets a base signpost, and operations within that line (CB operations, DMAs) get their own nested signposts with the operation name appended [python/ttl/_src/auto_profile.py 78-99](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L78-L99)

**Diagram: Auto-Profiling Signpost Hierarchy**

**Sources**: [python/ttl/_src/auto_profile.py 78-99](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L78-L99)[python/ttl/_src/auto_profile.py 116-123](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L116-L123)[test/python/auto_profile_signposts.py 67-81](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/auto_profile_signposts.py#L67-L81)

* * *



```mermaid
graph LR
    subgraph "Source Code Line 42"
        [LineSignpost] -->|"contains"| [CBWait]
        [LineSignpost] -->|"contains"| [CBPop]
    end
    
    [CBWait] -->|"emit"| [ZONE_START]
    [CBWait] -->|"emit"| [ZONE_END]
    [ZONE_START] --> [Duration]
    [ZONE_END] --> [Duration]
```

**Diagram: Auto-Profiling Signpost Hierarchy**
```
## Performance Trace Visualization

The trace visualization system converts device profiler data to Chrome Trace Event format for visualization in tools like Perfetto UI. For details, see [Performance Trace Visualization](https://deepwiki.com/tenstorrent/tt-lang/7.2-performance-trace-visualization).

### Trace Server Integration

The system includes a trace server capable of serving visualization data [python/ttl/ttl_api.py 62](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L62-L62) During execution, `_run_profiling_pipeline` handles reading the device profiler data from the hardware [python/ttl/ttl_api.py 166-208](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L166-L208)

**Sources**: [python/ttl/ttl_api.py 62](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L62-L62)[python/ttl/ttl_api.py 166-208](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L166-L208)

* * *

## Signpost Profiling

Signpost profiling allows users to define custom profiling zones around specific code regions using `ttl.signpost()`. For details, see [Signpost Profiling](https://deepwiki.com/tenstorrent/tt-lang/7.3-signpost-profiling).

### Signpost Lowering to EmitC

The `SignpostLowering` pattern converts `ttl.signpost` operations to `DeviceZoneScopedN()` C++ instrumentation [lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp 124-126](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp#L124-L126) The pass ensures that the region contains "interesting" operations (i.e., `ttkernel` dialect ops) before emitting instrumentation [lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp 117-128](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp#L117-L128) It also checks for escaping values via `hasEscapingValues` that might break the scope block [lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp 63-76](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp#L63-L76)

**Sources**: [lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp 63-140](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp#L63-L140)[python/ttl/_src/signpost_profile.py 8-11](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/signpost_profile.py#L8-L11)

* * *

## NOC and DMA Performance Analysis

Analysis of NOC events and DMA transfers identifies memory bottlenecks. For details, see [NOC and DMA Performance Analysis](https://deepwiki.com/tenstorrent/tt-lang/7.4-noc-and-dma-performance-analysis).

The profiling system can correlate `cb_wait()` calls with their corresponding DMA producers to identify pipeline stalls via `cb_wait_to_dma` mapping [python/ttl/_src/auto_profile.py 209-212](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L209-L212) Statistics can be derived from JSON Lines trace files.

**Sources**: [python/ttl/_src/auto_profile.py 126-182](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L126-L182)[python/ttl/_src/auto_profile.py 209-212](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L209-L212)

* * *

## Optimization Workflow

Kernel optimization is a systematic process of identifying bottlenecks using the tools described above. For details, see [Optimization Workflow](https://deepwiki.com/tenstorrent/tt-lang/7.5-optimization-workflow).

A key optimization target is maximizing core utilization and reducing DRAM traffic. The `TTLLowerSignpostToEmitCPass` specifically skips coordinate lookups (like `ttkernel.my_x` or `ttkernel.my_y`) to avoid cluttering the profile with trivial setup code [lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp 29-33](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp#L29-L33)

**Sources**: [lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp 29-33](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/LowerSignpostToEmitC.cpp#L29-L33)

* * *

## Environment Variable Reference

| Variable | Value | Effect |
| --- | --- | --- |
| `TT_METAL_DEVICE_PROFILER` | `1` | Enable hardware profiler |
| `TTLANG_AUTO_PROFILE` | `1` | Enable line-level auto-profiling [python/ttl/_src/auto_profile.py 52-54](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L52-L54) |
| `TTLANG_SIGNPOST_PROFILE` | `1` | Enable user-defined signpost profiling [python/ttl/_src/signpost_profile.py 24-25](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/signpost_profile.py#L24-L25) |
| `TTLANG_COMPILE_ONLY` | `1` | Skip execution, only compile [test/python/auto_profile_signposts.py 20](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/auto_profile_signposts.py#L20-L20) |

**Sources**: [python/ttl/_src/auto_profile.py 52-54](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/auto_profile.py#L52-L54)[python/ttl/_src/signpost_profile.py 24-25](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/signpost_profile.py#L24-L25)[test/python/auto_profile_signposts.py 20](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/auto_profile_signposts.py#L20-L20)

Dismiss
Refresh this wiki

Enter email to refresh
