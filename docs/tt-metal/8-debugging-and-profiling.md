---
project: tt-metal
github: https://github.com/tenstorrent/tt-metal
deepwiki: https://deepwiki.com/tenstorrent/tt-metal/8-debugging-and-profiling
---

# Debugging and Profiling

Relevant source files
*   [conftest.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/conftest.py)
*   [models/demos/t3000/falcon40b/tests/unit_tests/layernorm/test_falcon_layernorm.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/t3000/falcon40b/tests/unit_tests/layernorm/test_falcon_layernorm.py)
*   [models/demos/t3000/falcon40b/tests/unit_tests/layernorm/test_fused_falcon_layernorm.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/t3000/falcon40b/tests/unit_tests/layernorm/test_fused_falcon_layernorm.py)
*   [models/demos/t3000/falcon40b/tests/unit_tests/test_falcon_create_qkv_heads.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/t3000/falcon40b/tests/unit_tests/test_falcon_create_qkv_heads.py)
*   [models/demos/t3000/falcon40b/tests/unit_tests/test_falcon_softmax.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/t3000/falcon40b/tests/unit_tests/test_falcon_softmax.py)
*   [models/demos/ttnn_falcon7b/tests/multi_chip/test_falcon_causallm.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/ttnn_falcon7b/tests/multi_chip/test_falcon_causallm.py)
*   [tests/scripts/common.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/common.py)
*   [tests/tt_metal/tools/profiler/test_device_profiler.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tools/profiler/test_device_profiler.py)
*   [tests/tt_metal/tt_metal/debug_tools/debug_tools_fixture.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_metal/debug_tools/debug_tools_fixture.hpp)
*   [tests/tt_metal/tt_metal/debug_tools/device_print/test_dram_print_output.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_metal/debug_tools/device_print/test_dram_print_output.cpp)
*   [tests/tt_metal/tt_metal/debug_tools/watcher/test_assert.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_metal/debug_tools/watcher/test_assert.cpp)
*   [tests/tt_metal/tt_metal/debug_tools/watcher/test_sanitize.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_metal/debug_tools/watcher/test_sanitize.cpp)
*   [tests/tt_metal/tt_metal/debug_tools/watcher/test_stack_usage.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_metal/debug_tools/watcher/test_stack_usage.cpp)
*   [tests/tt_metal/tt_metal/test_kernels/compute/simple_tls_check.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_metal/test_kernels/compute/simple_tls_check.cpp)
*   [tests/tt_metal/tt_metal/test_kernels/dataflow/dram_copy_to_noc_coord.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_metal/test_kernels/dataflow/dram_copy_to_noc_coord.cpp)
*   [tests/tt_metal/tt_metal/test_kernels/dataflow/dram_copy_to_noc_coord_2_0.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_metal/test_kernels/dataflow/dram_copy_to_noc_coord_2_0.cpp)
*   [tests/tt_metal/tt_metal/test_kernels/dataflow/kernel_thread_barrier.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_metal/test_kernels/dataflow/kernel_thread_barrier.cpp)
*   [tests/tt_metal/tt_metal/test_kernels/misc/watcher_asserts.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_metal/test_kernels/misc/watcher_asserts.cpp)
*   [tests/tt_metal/tt_metal/test_kernels/misc/watcher_ringbuf.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_metal/test_kernels/misc/watcher_ringbuf.cpp)
*   [tests/tt_metal/tt_metal/test_kernels/misc/watcher_stack.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_metal/test_kernels/misc/watcher_stack.cpp)
*   [tests/ttnn/tracy/test_dispatch_profiler.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/tracy/test_dispatch_profiler.py)
*   [tests/ttnn/tracy/test_profiler_sync.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/tracy/test_profiler_sync.py)
*   [tests/ttnn/tracy/test_trace_runs.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/tracy/test_trace_runs.py)
*   [tt_metal/api/tt-metalium/profiler_types.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/api/tt-metalium/profiler_types.hpp)
*   [tt_metal/core_descriptors/blackhole_140_arch_fabric_mux.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/core_descriptors/blackhole_140_arch_fabric_mux.yaml)
*   [tt_metal/hw/firmware/src/tt-2xx/trisck.cc](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/firmware/src/tt-2xx/trisck.cc)
*   [tt_metal/hw/inc/api/debug/assert.h](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/inc/api/debug/assert.h)
*   [tt_metal/hw/inc/api/kernel_thread_globals.h](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/inc/api/kernel_thread_globals.h)
*   [tt_metal/hw/inc/internal/debug/sanitize.h](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/inc/internal/debug/sanitize.h)
*   [tt_metal/hw/inc/internal/debug/stack_usage.h](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/inc/internal/debug/stack_usage.h)
*   [tt_metal/hw/inc/internal/tt-2xx/risc_common.h](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/inc/internal/tt-2xx/risc_common.h)
*   [tt_metal/impl/debug/debug_helpers.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/debug_helpers.hpp)
*   [tt_metal/impl/debug/dprint_server.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/dprint_server.cpp)
*   [tt_metal/impl/debug/noc_logging.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/noc_logging.cpp)
*   [tt_metal/impl/debug/watcher_device_reader.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/watcher_device_reader.cpp)
*   [tt_metal/impl/debug/watcher_device_reader.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/watcher_device_reader.hpp)
*   [tt_metal/impl/debug/watcher_server.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/watcher_server.cpp)
*   [tt_metal/impl/profiler/profiler.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/profiler/profiler.cpp)
*   [tt_metal/impl/profiler/profiler.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/profiler/profiler.hpp)
*   [tt_metal/impl/profiler/profiler_state_manager.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/profiler/profiler_state_manager.hpp)
*   [tt_metal/impl/profiler/tt_metal_profiler.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/profiler/tt_metal_profiler.cpp)
*   [tt_metal/tools/profiler/event_metadata.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/tools/profiler/event_metadata.hpp)
*   [tt_metal/tools/profiler/kernel_profiler.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/tools/profiler/kernel_profiler.hpp)
*   [tt_metal/tools/profiler/noc_event_profiler.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/tools/profiler/noc_event_profiler.hpp)
*   [tt_metal/tools/profiler/noc_event_profiler_utils.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/tools/profiler/noc_event_profiler_utils.hpp)

This page provides a high-level overview of the debugging and profiling tools and infrastructure available in the `tt-metal` codebase. These tools enable comprehensive runtime visibility, error detection, and performance analysis across the full stack of Tenstorrent hardware and software layers. The purpose is to outline how these subsystems interrelate and to guide users to more detailed child pages for each topic.

* * *

## Debugging Infrastructure Overview

The debugging infrastructure is tightly integrated with the core runtime system and configured via environment variables parsed into the `RunTimeOptions` interface [tt_metal/llrt/rtoptions.hpp 43](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/rtoptions.hpp#L43-L43) The centerpiece is the `MetalContext` singleton which coordinates debugging state across devices [tt_metal/impl/context/metal_context.cpp 35-46](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/context/metal_context.cpp#L35-L46)

Debugging targets can be specified granularly via the `TargetSelection` mechanism to enable debugging features on select chips, cores, or RISC-V processors. This enables focused debugging minimizing overhead and data noise.

The main debugging subsystems include:

*   **Watcher Server**: Monitors kernel progress, detects hangs, asserts, NoC access errors.
*   **DPRINT Server**: Captures device-kernel `printf`-style debug output via L1 ring buffers.
*   **Profiler State Manager**: Collects timing and performance data, interacts with Tracy.

The following diagram maps key debugging system components to their code entities:

**Sources:**

*   [tt_metal/impl/context/metal_context.hpp 35-46](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/context/metal_context.hpp#L35-L46)
*   [tt_metal/llrt/rtoptions.hpp 43](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/rtoptions.hpp#L43-L43)
*   [tt_metal/impl/debug/watcher_server.hpp 10-15](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/watcher_server.hpp#L10-L15)

* * *

## Watcher Debug System

Watcher is a firmware-assisted monitoring framework that captures device runtime state, kernel waypoint progress, NoC transaction validation, and kernel assert conditions. It helps identify hangs, illegal accesses, and data races at runtime.

*   The host-side `WatcherServer` runs a polling thread that periodically reads device mailboxes and parses debug data via `WatcherDeviceReader`[tt_metal/impl/debug/watcher_server.cpp 78-91](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/watcher_server.cpp#L78-L91)
*   Kernels are instrumented to emit waypoints marking progress; watcher tracks these to identify hangs per RISC processor like `BRISC`, `NCRISC`, and `TRISC`[tt_metal/impl/debug/watcher_device_reader.cpp 64-75](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/watcher_device_reader.cpp#L64-L75)
*   Intensive NoC address sanitization identifies illegal memory transactions and misrouted packets [tt_metal/impl/debug/watcher_device_reader.cpp 154-162](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/watcher_device_reader.cpp#L154-L162)
*   Asserts triggered by device kernels surface detailed information including core ID and line number [tt_metal/hw/inc/api/debug/assert.h 5-15](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/inc/api/debug/assert.h#L5-L15)
*   Stack pointer scanning detects potential overflows or unusual stack behavior [tt_metal/impl/debug/watcher_device_reader.cpp 108-132](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/watcher_device_reader.cpp#L108-L132)

Watcher is fundamental for live debugging and provides human and machine-readable diagnostics in `watcher.log`.

For implementation and usage details, see the child page: [Watcher Debug System](https://deepwiki.com/tenstorrent/tt-metal/8.2-watcher-debug-system).

**Sources:**

*   [tt_metal/impl/debug/watcher_device_reader.cpp 64-162](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/watcher_device_reader.cpp#L64-L162)
*   [tt_metal/impl/debug/watcher_server.cpp 78-91](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/watcher_server.cpp#L78-L91)
*   [tt_metal/hw/inc/api/debug/assert.h 5-15](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/inc/api/debug/assert.h#L5-L15)

* * *

## Kernel Debugging Tools (DPRINT)

The DPRINT subsystem enables in-kernel `printf` debugging by streaming formatted strings and data from device RISC cores to the host in real-time.

*   Device kernels write debug logs to per-RISC ring buffers in L1 memory.
*   The host-side `DPrintServer` reads and parses these buffers, decoding messages with associated kernel ELF symbol data [tt_metal/impl/debug/dprint_server.cpp 63-196](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/dprint_server.cpp#L63-L196)
*   Initialization synchronization uses a magic value in buffers to detect kernel startup and avoid race conditions during data streaming [tt_metal/impl/debug/dprint_server.cpp 139-163](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/dprint_server.cpp#L139-L163)
*   Debug output can be selectively muted or split by chip/core/RISC using `RiscKey` structures and comparators to track streams.
*   Configured via runtime environment variables and options to flexibly control debugging output.

Together, these capabilities facilitate detailed device kernel tracing without intrusive instrumentation.

For more comprehensive info, see the child page: [Kernel Debugging Tools](https://deepwiki.com/tenstorrent/tt-metal/8.3-kernel-debugging-tools).

**Sources:**

*   [tt_metal/impl/debug/dprint_server.cpp 63-196](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/dprint_server.cpp#L63-L196)

* * *

## Profiling and Performance Analysis

The profiling subsystem provides detailed performance metrics and timing information, integrating the Tracy profiler for rich host-side visualization alongside dedicated device-side data collection.

### Profiler Components and Data Flow

### Key Features

*   **Host-Device Synchronization:** A dedicated sync kernel (`sync_kernel.cpp`) ensures host CPU and device RISC-V timers are aligned for accurate time correlation [tt_metal/impl/profiler/tt_metal_profiler.cpp 102-140](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/profiler/tt_metal_profiler.cpp#L102-L140)
*   **NoC Event Profiling:** Detailed capture and analysis of NoC read, write, and barrier transactions are enabled through `NOCDebugEvent` structures [tt_metal/impl/profiler/profiler.cpp 114-168](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/profiler/profiler.cpp#L114-L168)
*   **L1 and DRAM Buffering:** Runtime profiling data is collected initially in fast L1 buffers (`profiler_data_buffer`) and then optionally flushed to larger DRAM buffers for long traces [tt_metal/tools/profiler/kernel_profiler.hpp 82-101](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/tools/profiler/kernel_profiler.hpp#L82-L101)
*   **RISC Control Buffers:** Per-RISC control buffers manage start, stop, and state of profiling on the `BRISC`, `NCRISC`, `ERISC`, and `TRISC` processors [tt_metal/impl/profiler/profiler.cpp 127-153](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/profiler/profiler.cpp#L127-L153)
*   **Fast Dispatch Integration:** Profiling can be triggered during fast dispatch mode, capturing detailed kernel execution timing and hardware events.

This integration allows developers to identify performance bottlenecks and optimize kernel and program execution efficiently.

For full technical details, refer to the child page: [Profiling and Performance Analysis](https://deepwiki.com/tenstorrent/tt-metal/8.4-profiling-and-performance-analysis).

**Sources:**

*   [tt_metal/impl/profiler/profiler.cpp 66-153](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/profiler/profiler.cpp#L66-L153)
*   [tt_metal/impl/profiler/tt_metal_profiler.cpp 102-140](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/profiler/tt_metal_profiler.cpp#L102-L140)
*   [tt_metal/tools/profiler/kernel_profiler.hpp 79-101](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/tools/profiler/kernel_profiler.hpp#L79-L101)

* * *

## Triage Tools and Post-Mortem Analysis

The codebase provides several capabilities for post-mortem debugging and root cause analysis in the event of hangs, timeouts, or hardware faults.

*   The `triage.py` tool performs detailed health checks on ARC processors, NoC connectivity, and RISC-V core states to help isolate causes of system hangs [tools/triage/triage.py 31-42](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tools/triage/triage.py#L31-L42)
*   Fast Dispatch state and prefetcher core inspection are automated using profiler instrumentation enabled by environment variables such as `TT_METAL_DEVICE_PROFILER_DISPATCH`[tests/tt_metal/tools/profiler/test_device_profiler.py 62-83](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tools/profiler/test_device_profiler.py#L62-L83)
*   CI automation triggers auto-triage runs on dispatch timeouts, producing JSON reports consumable by LLMs or automation systems for faster debugging [.github/actions/setup-job/action.yml 145-160](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/actions/setup-job/action.yml#L145-L160)
*   Mid-run dumps and C++ post-processing tools enable capturing hardware state snapshots prior to fatal faults.

These facilities significantly reduce time-to-resolution by automating data capture and analysis, supporting both engineers and CI pipelines.

For more information, see the dedicated child page: [Triage Tools and Post-Mortem Analysis](https://deepwiki.com/tenstorrent/tt-metal/8.6-triage-tools-and-post-mortem-analysis).

**Sources:**

*   [tools/triage/triage.py 31-42](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tools/triage/triage.py#L31-L42)
*   [tests/tt_metal/tools/profiler/test_device_profiler.py 59-83](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tools/profiler/test_device_profiler.py#L59-L83)
*   [.github/actions/setup-job/action.yml 145-160](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/actions/setup-job/action.yml#L145-L160)

* * *

## Debug Environment Variables

The runtime behavior of the debugging infrastructure is controlled extensively through a set of `TT_METAL_*` environment variables. These allow selective enabling/disabling of subsystems and configuration of debugging modes.

| Environment Variable | Purpose | Notes/Reference |
| --- | --- | --- |
| `TT_METAL_WATCHER` | Enables the Watcher server; `1` = basic, `2` = extended modes | [tt_metal/impl/debug/watcher_server.cpp 121](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/debug/watcher_server.cpp#L121-L121) |
| `TT_METAL_DEVICE_PROFILER` | Enables hardware-level device profiling | [tt_metal/impl/profiler/profiler.cpp 127-135](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/profiler/profiler.cpp#L127-L135) |
| `TT_METAL_SLOW_DISPATCH_MODE` | Runs dispatch in slow mode (without HW command queue) for easier debugging | [tests/tt_metal/tools/profiler/test_device_profiler.py 63](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tools/profiler/test_device_profiler.py#L63-L63) |
| `TT_METAL_PROFILER_SYNC` | Enables host-device time synchronization kernel | [tt_metal/impl/profiler/tt_metal_profiler.cpp 105](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/profiler/tt_metal_profiler.cpp#L105-L105) |
| `TT_METAL_DEVICE_PROFILER_NOC_EVENTS` | Enables detailed NoC event tracking in the profiler | [tests/tt_metal/tools/profiler/test_device_profiler.py 64](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tools/profiler/test_device_profiler.py#L64-L64) |
| `TT_METAL_OPERATION_TIMEOUT_SECONDS` | Sets hang detection timeout threshold for auto-triage | [.github/actions/setup-job/action.yml 158](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/actions/setup-job/action.yml#L158-L158) |
| `TT_METAL_PROFILER_MID_RUN_DUMP` | Enables mid-run dump of profiling data | [tests/tt_metal/tools/profiler/test_device_profiler.py 66-67](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tools/profiler/test_device_profiler.py#L66-L67) |
| `TT_METAL_PROFILER_CPP_POST_PROCESS` | Controls C++ post-processing of profiling data | [tests/tt_metal/tools/profiler/test_device_profiler.py 67](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tools/profiler/test_device_profiler.py#L67-L67) |

For the full list and detailed explanations, see the child page: [Debug Environment Variables](https://deepwiki.com/tenstorrent/tt-metal/8.5-debug-environment-variables).

**Sources:**

*   [tests/tt_metal/tools/profiler/test_device_profiler.py 59-83](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tools/profiler/test_device_profiler.py#L59-L83)
*   [tt_metal/impl/profiler/tt_metal_profiler.cpp 102-110](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/profiler/tt_metal_profiler.cpp#L102-L110)
*   [.github/actions/setup-job/action.yml 158](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/actions/setup-job/action.yml#L158-L158)

* * *

This overview page serves as the entry point for understanding the comprehensive debugging and profiling capabilities in the `tt-metal` repository. For full technical depth and implementation details on each subsystem, please consult the following dedicated child pages:

*   **[Debugging Tools Overview](https://deepwiki.com/tenstorrent/tt-metal/8.1-debugging-tools-overview)** — Introduction and summary of all debugging capabilities, including watcher, kernel debugging, and environment variables.
*   **[Watcher Debug System](https://deepwiki.com/tenstorrent/tt-metal/8.2-watcher-debug-system)** — In-depth explanation of the watcher system for monitoring device state, kernel execution progress, asserts, and errors.
*   **[Kernel Debugging Tools](https://deepwiki.com/tenstorrent/tt-metal/8.3-kernel-debugging-tools)** — Documentation of DPRINT buffers, on-device debug print streaming, and host-side capture/parsing.
*   **[Profiling and Performance Analysis](https://deepwiki.com/tenstorrent/tt-metal/8.4-profiling-and-performance-analysis)** — Full details on Tracy integration, device profiling buffers, NoC event tracking, and performance metrics.
*   **[Debug Environment Variables](https://deepwiki.com/tenstorrent/tt-metal/8.5-debug-environment-variables)** — Exhaustive list and description of environment variables controlling debugging behavior.
*   **[Triage Tools and Post-Mortem Analysis](https://deepwiki.com/tenstorrent/tt-metal/8.6-triage-tools-and-post-mortem-analysis)** — Usage of post-mortem triage tools, auto-triage CI integration, callstack dumping, NOC diagnostic checks, and hardware hang analysis.

This modular breakdown ensures that developers and engineers can rapidly access precise guidance for their debugging or profiling needs.

* * *

**Summary Diagram: Debugging and Profiling Subsystems and Data Flow**

* * *

# End of Section 8: Debugging and Profiling

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-metal/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh