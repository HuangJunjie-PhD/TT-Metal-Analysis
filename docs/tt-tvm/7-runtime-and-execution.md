---
project: tt-tvm
github: https://github.com/tenstorrent/tt-tvm
deepwiki: https://deepwiki.com/tenstorrent/tt-tvm/7-runtime-and-execution
---

# Runtime and Execution

Relevant source files
*   [CONTRIBUTORS.md](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CONTRIBUTORS.md?plain=1)
*   [NEWS.md](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/NEWS.md?plain=1)
*   [gallery/how_to/deploy_models/deploy_model_on_android.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/deploy_models/deploy_model_on_android.py)
*   [gallery/how_to/tune_with_autoscheduler/tune_network_arm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py)
*   [gallery/how_to/tune_with_autoscheduler/tune_network_mali.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_mali.py)
*   [include/tvm/runtime/crt/crt.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/crt/crt.h)
*   [include/tvm/runtime/profiling.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/profiling.h)
*   [include/tvm/runtime/vm/executable.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/vm/executable.h)
*   [include/tvm/runtime/vm/vm.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/vm/vm.h)
*   [jvm/README.md](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/jvm/README.md?plain=1)
*   [python/tvm/contrib/debugger/debug_executor.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/contrib/debugger/debug_executor.py)
*   [python/tvm/relay/backend/vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/backend/vm.py)
*   [python/tvm/runtime/profiler_vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/profiler_vm.py)
*   [python/tvm/runtime/profiling/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/profiling/__init__.py)
*   [python/tvm/runtime/vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/vm.py)
*   [src/relay/backend/vm/compiler.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc)
*   [src/relay/backend/vm/compiler.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.h)
*   [src/runtime/graph_executor/debug/graph_executor_debug.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/graph_executor/debug/graph_executor_debug.cc)
*   [src/runtime/profiling.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/profiling.cc)
*   [src/runtime/vm/executable.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/executable.cc)
*   [src/runtime/vm/profiler/vm.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/profiler/vm.cc)
*   [src/runtime/vm/profiler/vm.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/profiler/vm.h)
*   [src/runtime/vm/vm.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc)
*   [tests/python/relay/test_vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_vm.py)
*   [tests/python/relay/test_vm_serialization.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_vm_serialization.py)
*   [tests/python/unittest/test_runtime_profiling.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_runtime_profiling.py)
*   [tests/python/unittest/test_runtime_vm_profiler.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_runtime_vm_profiler.py)

## Purpose and Scope

This section provides an overview of TVM's runtime systems for executing compiled models on target devices. It covers the three main execution models (Graph Executor, Virtual Machine, and AOT Executor), their architecture, and common runtime components. For details on specific runtime components:

*   Graph Executor implementation: see [Graph Executor](https://deepwiki.com/tenstorrent/tt-tvm/7.1-graph-executor)
*   Virtual Machine implementation: see [Virtual Machine Executor](https://deepwiki.com/tenstorrent/tt-tvm/7.2-virtual-machine-executor)
*   AOT (Ahead-of-Time) compilation: see [AOT Executor](https://deepwiki.com/tenstorrent/tt-tvm/7.3-aot-executor)
*   Minimal C Runtime for embedded systems: see [C Runtime (CRT)](https://deepwiki.com/tenstorrent/tt-tvm/7.4-c-runtime-(crt))
*   Performance measurement infrastructure: see [Profiling System](https://deepwiki.com/tenstorrent/tt-tvm/7.5-profiling-system)

For the compilation pipeline that produces executables, see [Code Generation Backends](https://deepwiki.com/tenstorrent/tt-tvm/6-code-generation-backends).

* * *

## Overview

The TVM runtime layer is responsible for loading compiled model artifacts and executing them efficiently on target hardware. After the compilation pipeline transforms a high-level model into low-level code (see [Architecture and Compilation Pipeline](https://deepwiki.com/tenstorrent/tt-tvm/2-architecture-and-compilation-pipeline)), the runtime system manages:

1.   **Loading executables** - deserializing compiled artifacts into memory
2.   **Memory management** - allocating and managing tensor storage
3.   **Device orchestration** - coordinating execution across CPUs, GPUs, and accelerators
4.   **Function dispatch** - invoking compiled kernels and operators
5.   **Data movement** - copying tensors between devices when needed

TVM provides three primary execution models, each optimized for different deployment scenarios:

| Execution Model | Use Case | Key Characteristics |
| --- | --- | --- |
| **Graph Executor** | Static models, production deployment | Minimal overhead, static memory planning, fast startup |
| **Virtual Machine** | Dynamic models, control flow | Supports dynamic shapes, if/while loops, closures |
| **AOT Executor** | Embedded systems, microcontrollers | No dynamic memory, static compilation, minimal runtime |

* * *

## Runtime Architecture

The following diagram illustrates the high-level architecture of TVM's runtime systems and how they interact with compiled artifacts and target devices:

**Sources:**

*   [src/runtime/vm/vm.cc 1-826](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L1-L826)
*   [src/runtime/graph_executor/debug/graph_executor_debug.cc 1-300](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/graph_executor/debug/graph_executor_debug.cc#L1-L300)
*   [python/tvm/runtime/vm.py 1-230](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/vm.py#L1-L230)

* * *

## Execution Flow

The typical execution flow from a compiled model to producing results follows these stages:

**Sources:**

*   [src/runtime/vm/vm.cc 429-442](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L429-L442) - `VirtualMachine::Invoke`
*   [src/runtime/vm/vm.cc 497-522](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L497-L522) - `VirtualMachine::LoadExecutable`
*   [src/runtime/vm/vm.cc 524-550](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L524-L550) - `VirtualMachine::Init`

* * *

## Key Runtime Components

### Executable Representation

Each execution model uses a different executable format to represent the compiled model:

**Graph Executor:** Uses a JSON-based graph representation where nodes represent operations and edges represent data dependencies. The graph is static and optimized for minimal overhead.

**Virtual Machine:** Uses bytecode instructions (similar to Python bytecode) stored in a `VMFunction`. Each function contains:

*   Parameter names (`params`)
*   Register file size (`register_file_size`)
*   Instruction sequence (`instructions`)
*   Device placement information (`param_device_indexes`)

**AOT Executor:** Generates standalone C code that can be compiled directly into the application, eliminating the need for a separate runtime library.

**Sources:**

*   [src/runtime/vm/executable.cc 126-141](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/executable.cc#L126-L141) - `Executable::GetVMFunctionWithName`
*   [include/tvm/runtime/vm/vm.h 82-92](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/vm/vm.h#L82-L92) - `VMFunction` structure

* * *

### Virtual Machine Internals

The Virtual Machine is TVM's most flexible runtime, supporting dynamic shapes, control flow, and closures. Its architecture consists of:

**Sources:**

*   [src/runtime/vm/vm.cc 599-856](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L599-L856) - VM instruction execution loop
*   [src/runtime/vm/vm.cc 399-413](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L399-L413) - Frame management
*   [include/tvm/runtime/vm/vm.h 125-236](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/vm/vm.h#L125-L236) - VirtualMachine class

* * *

### Instruction Set

The VM supports approximately 15 instruction types for different operations:

| Instruction | Purpose | Example Usage |
| --- | --- | --- |
| `LoadConst` | Load constant from constant pool | Loading model weights |
| `Invoke` | Call a global VM function | Calling sub-functions |
| `InvokePacked` | Call a compiled operator | Executing conv2d, matmul |
| `InvokeClosure` | Call a closure | Higher-order functions |
| `AllocTensor` | Allocate tensor with static shape | Output buffers |
| `AllocTensorReg` | Allocate tensor with dynamic shape | Dynamic shapes |
| `AllocStorage` | Allocate raw memory buffer | Storage for tensors |
| `AllocADT` | Allocate algebraic data type | Tuples, lists |
| `If` | Conditional branch | Control flow |
| `GetField` | Extract tuple element | Tuple unpacking |
| `DeviceCopy` | Copy tensor between devices | CPU ↔ GPU transfer |
| `ReshapeTensor` | Reshape without copying | View operations |
| `ShapeOf` | Get tensor shape | Dynamic shape queries |

**Sources:**

*   [include/tvm/runtime/vm/bytecode.h 40-60](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/vm/bytecode.h#L40-L60)
*   [src/runtime/vm/vm.cc 304-333](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L304-L333) - Instruction emission

* * *

## Memory Management

All runtime executors share a common memory management infrastructure:

The VM's memory allocation follows a two-level strategy:

1.   **Storage Allocation** (`AllocStorage` instruction): Allocates raw memory buffers with device placement and alignment requirements
2.   **Tensor Allocation** (`AllocTensor`/`AllocTensorReg` instructions): Creates typed tensor views over storage buffers with shape and dtype information

**Sources:**

*   [src/runtime/vm/vm.cc 524-550](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L524-L550) - Allocator initialization
*   [src/runtime/vm/vm.cc 619-624](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L619-L624) - Storage allocation in VM

* * *

## Device Management

The runtime maintains a mapping between virtual devices (used in the IR) and physical devices (actual hardware):

The VM's `Init()` method establishes this mapping:

1.   Receives a list of physical devices from the user
2.   Matches each virtual device (from the executable) to a physical device by type
3.   Creates appropriate memory allocators for each physical device
4.   Stores the mapping in `devices_` and `allocators_` vectors

**Sources:**

*   [src/runtime/vm/vm.cc 524-550](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L524-L550) - Device initialization
*   [src/runtime/vm/vm.cc 340-364](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L340-L364) - `GetDeviceIndex` mapping
*   [src/runtime/vm/compiler.cc 340-364](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/compiler.cc#L340-L364) - Device index assignment during compilation

* * *

## Packed Functions

The `PackedFunc` interface provides a uniform way to call compiled operators across different backends:

During VM execution, when an `InvokePacked` instruction is encountered:

1.   Arguments are extracted from registers and flattened (ADT objects are unpacked)
2.   A `TVMArgs` object is constructed with value pointers and type codes
3.   The `PackedFunc` is invoked via `CallPacked()`
4.   Results are written back to output registers

**Sources:**

*   [src/runtime/vm/vm.cc 452-495](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L452-L495) - `VirtualMachine::InvokePacked`
*   [src/runtime/vm/compiler.cc 508-527](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/compiler.cc#L508-L527) - Packed function emission

* * *

## Profiling Integration

All executors integrate with TVM's profiling infrastructure to collect performance metrics:

The profiling-enabled VM (`VirtualMachineDebug`) wraps each operation:

1.   **Before instruction**: Calls `OpStartHook()` to start profiling with operation name and device
2.   **Execute instruction**: Runs the actual operation
3.   **After instruction**: Calls `OpStopHook()` to collect metrics
4.   **Report generation**: Aggregates all collected data into a structured `Report`

**Sources:**

*   [src/runtime/vm/profiler/vm.cc 48-94](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/profiler/vm.cc#L48-L94) - VM profiler implementation
*   [src/runtime/vm/profiler/vm.cc 103-163](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/profiler/vm.cc#L103-L163) - Instruction profiling hooks
*   [src/runtime/profiling.cc 122-183](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/profiling.cc#L122-L183) - Profiler core implementation

* * *

## Python Interface

The Python API provides a high-level interface for loading and executing models:

Key Python classes:

*   **`Executable`** (`python/tvm/runtime/vm.py`): Wrapper for VM bytecode and metadata
*   **`VirtualMachine`** (`python/tvm/runtime/vm.py`): Main runtime interface for execution
*   **`VirtualMachineProfiler`** (`python/tvm/runtime/profiler_vm.py`): Profiling-enabled VM

**Sources:**

*   [python/tvm/runtime/vm.py 78-332](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/vm.py#L78-L332) - Executable and VirtualMachine classes
*   [python/tvm/runtime/profiler_vm.py 1-100](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/profiler_vm.py#L1-L100) - Profiling VM
*   [python/tvm/relay/backend/vm.py 37-73](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/backend/vm.py#L37-L73) - Compilation helper

* * *

## Summary

The TVM runtime layer provides multiple execution models optimized for different deployment scenarios:

*   **Graph Executor**: Static execution with minimal overhead for production
*   **Virtual Machine**: Flexible execution supporting dynamic shapes and control flow
*   **AOT Executor**: Minimal runtime for embedded and microcontroller deployments

All executors share common infrastructure for memory management, device orchestration, and operator invocation through `PackedFunc`. The profiling system integrates seamlessly to provide detailed performance insights.

For detailed information on each executor implementation, refer to the respective child pages.

Dismiss
Refresh this wiki

Enter email to refresh