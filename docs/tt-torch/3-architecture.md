---
project: tt-torch
github: https://github.com/tenstorrent/tt-torch
deepwiki: https://deepwiki.com/tenstorrent/tt-torch/3-architecture
---

# Architecture

Relevant source files
*   [tests/utils.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/utils.py)
*   [third_party/CMakeLists.txt](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/third_party/CMakeLists.txt)
*   [tt_torch/csrc/bindings.cpp](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/bindings.cpp)
*   [tt_torch/csrc/tt-mlir-interface.cpp](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/tt-mlir-interface.cpp)
*   [tt_torch/csrc/tt-mlir-interface.hpp](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/tt-mlir-interface.hpp)
*   [tt_torch/dynamo/backend.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/backend.py)
*   [tt_torch/dynamo/executor.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/executor.py)
*   [tt_torch/dynamo/passes.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/passes.py)
*   [tt_torch/dynamo/shlo_backend.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/shlo_backend.py)
*   [tt_torch/dynamo/torch_backend.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/torch_backend.py)
*   [tt_torch/tools/device_manager.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/device_manager.py)
*   [tt_torch/tools/utils.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/utils.py)
*   [tt_torch/tools/verify.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/verify.py)

This document provides a high-level overview of tt-torch's internal architecture, covering the major components and their interactions. For detailed information about specific subsystems, see the child pages:

*   [Compilation Pipeline](https://deepwiki.com/tenstorrent/tt-torch/3.1-compilation-pipeline) - Multi-stage IR lowering process
*   [Backend Integration](https://deepwiki.com/tenstorrent/tt-torch/3.2-backend-integration) - Dynamo integration and backend implementations
*   [Executor Framework](https://deepwiki.com/tenstorrent/tt-torch/3.3-executor-framework) - Execution engine classes
*   [Runtime and Device Bindings](https://deepwiki.com/tenstorrent/tt-torch/3.4-runtime-and-device-bindings) - Python-C++ interface layer
*   [Device Management and Parallelism](https://deepwiki.com/tenstorrent/tt-torch/3.5-device-management-and-parallelism) - Multi-device execution
*   [ONNX Support](https://deepwiki.com/tenstorrent/tt-torch/3.6-onnx-support) - ONNX model compilation path

## System Overview

tt-torch implements a compiler stack that transforms PyTorch and ONNX models into optimized executables for Tenstorrent hardware. The architecture is organized into four primary layers: frontend, compilation, execution, and runtime.

The system accepts models through two entry points: PyTorch's `torch.compile()` API with the `tt-legacy` or `tt-experimental` backends, and direct ONNX model compilation via `compile_onnx()`. Both paths converge at the StableHLO IR level, where they follow a unified lowering pipeline through TTIR and TTNN dialects before generating flatbuffer binaries.

### High-Level Architecture Layers

**Sources**: [tt_torch/dynamo/backend.py 1-370](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/backend.py#L1-L370)[tt_torch/dynamo/executor.py 1-142](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/executor.py#L1-L142)[tt_torch/csrc/bindings.cpp 1-608](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/bindings.cpp#L1-L608)

## Core Components

### Backend Entry Point

The `backend()` function in `tt_torch.dynamo.backend` serves as the main entry point registered with PyTorch's Dynamo compiler. It receives a `torch.fx.GraphModule` and `BackendOptions` containing a `CompilerConfig` instance that controls compilation behavior.

**Sources**: [tt_torch/dynamo/backend.py 312-370](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/backend.py#L312-L370)[tt_torch/tools/utils.py 300-340](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/utils.py#L300-L340)

### Configuration System

The `CompilerConfig` class controls all aspects of compilation and execution:

| Configuration | Purpose | Default |
| --- | --- | --- |
| `compile_depth` | Determines compilation stage: `TORCH_FX`, `STABLEHLO`, `TTNN_IR`, `EXECUTE`, or op-by-op modes | `EXECUTE` |
| `profile_ops` | Enables operation-level profiling and tracking | `True` |
| `enable_consteval` | Enables constant evaluation pass | `False` |
| `enable_optimizer` | Enables TTNN optimizer pass | `False` |
| `verify_op_by_op` | Verifies each operation against golden reference | `False` |
| `enable_intermediate_verification` | Enables runtime intermediate result verification | `False` |
| `mesh_shape` | Mesh shape for multi-device execution | `[1, 1]` |
| `device_map` | Maps model layers to specific devices | `{}` |

**Sources**: [tt_torch/tools/utils.py 300-572](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/utils.py#L300-L572)

### Multi-Stage IR Lowering

The compilation pipeline performs progressive lowering through multiple intermediate representations. Each stage has specific responsibilities:

**Sources**: [tt_torch/dynamo/backend.py 197-310](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/backend.py#L197-L310)[tt_torch/dynamo/passes.py 1-800](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/passes.py#L1-L800)[tt_torch/csrc/tt-mlir-interface.cpp 134-291](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/tt-mlir-interface.cpp#L134-L291)

## Executor Architecture

The executor framework provides three main execution modes controlled by `CompileDepth`:

### Executor Class Hierarchy

**Sources**: [tt_torch/dynamo/executor.py 141-800](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/executor.py#L141-L800)[tt_torch/dynamo/torch_backend.py 185-600](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/torch_backend.py#L185-L600)[tt_torch/dynamo/shlo_backend.py 279-650](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/shlo_backend.py#L279-L650)

### Execution Modes

| Mode | Class | Compilation Level | Use Case |
| --- | --- | --- | --- |
| Full Execution | `Executor` | Complete binary | Production inference |
| Op-by-Op (Torch) | `TorchExecutor` | Per FX node | Frontend debugging |
| Op-by-Op (StableHLO) | `StablehloExecutor` | Per MLIR op | Backend debugging |

**Sources**: [tt_torch/dynamo/executor.py 141-175](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/executor.py#L141-L175)[tt_torch/tools/utils.py 48-55](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/utils.py#L48-L55)

## Runtime Interface

The Python-C++ interface layer provides bidirectional communication between Python and the tt-mlir runtime through the `tt_mlir` module built from `bindings.cpp`.

### Key Runtime Functions

**Sources**: [tt_torch/csrc/bindings.cpp 436-608](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/bindings.cpp#L436-L608)[tt_torch/csrc/tt-mlir-interface.cpp 1-294](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/tt-mlir-interface.cpp#L1-L294)

### Tensor Conversion Pipeline

Tensor conversion between PyTorch and tt-metal runtime involves multiple steps to handle layout transformations required by Tenstorrent hardware:

**Sources**: [tt_torch/csrc/bindings.cpp 95-159](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/bindings.cpp#L95-L159)[tt_torch/csrc/bindings.cpp 226-392](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/bindings.cpp#L226-L392)

## Multi-Device Execution

tt-torch supports multiple multi-device execution strategies through the `MultiChipGraph` abstraction and `DeviceManager`.

### Multi-Device Graph Splitting

When `device_map` is configured in `CompilerConfig`, the graph is split across multiple devices:

**Sources**: [tt_torch/dynamo/passes.py 312-800](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/passes.py#L312-L800)[tt_torch/tools/utils.py 78-114](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/utils.py#L78-L114)

### DeviceManager

The `DeviceManager` class provides a centralized interface for managing parent mesh devices and sub-mesh devices:

**Sources**: [tt_torch/tools/device_manager.py 1-219](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/device_manager.py#L1-L219)

## Build System Integration

The build system coordinates multiple external dependencies through CMake and Python packaging:

### External Dependencies

**Sources**: [third_party/CMakeLists.txt 1-191](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/third_party/CMakeLists.txt#L1-L191)[tt_torch/csrc/CMakeLists.txt](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/CMakeLists.txt)

### Built Artifacts

| Artifact | Source | Purpose |
| --- | --- | --- |
| `tt_mlir.so` | tt-torch C++ bindings | Python extension for runtime interface |
| `libTTMLIRCompiler.so` | tt-mlir (via tt-xla) | MLIR compilation pipeline |
| `libTTMLIRRuntime.so` | tt-mlir (via tt-xla) | Hardware runtime execution |
| `pjrt_plugin_tt.so` | tt-xla | XLA PJRT plugin for experimental backend |
| `torch_mlir` packages | torch-mlir | PyTorch to MLIR import functionality |

**Sources**: [third_party/CMakeLists.txt 87-177](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/third_party/CMakeLists.txt#L87-L177)

## Key Design Patterns

### Caching Strategy

The executor maintains three-level caches for runtime tensors to avoid redundant preprocessing:

**Sources**: [tt_torch/dynamo/backend.py 21-54](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/backend.py#L21-L54)[tt_torch/dynamo/executor.py 176-203](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/executor.py#L176-L203)[tt_torch/dynamo/executor.py 325-456](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/executor.py#L325-L456)

### Op-by-Op Compilation Workflow

When `compile_depth` is set to `COMPILE_OP_BY_OP` or `EXECUTE_OP_BY_OP`, the system compiles each operation individually:

**Sources**: [tt_torch/dynamo/torch_backend.py 334-600](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/torch_backend.py#L334-L600)[tt_torch/tools/utils.py 141-298](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/utils.py#L141-L298)

### Intermediate Verification

When `enable_intermediate_verification` is enabled (requires `TT_RUNTIME_DEBUG=ON`), the system registers callbacks to capture intermediate results:

**Sources**: [tt_torch/dynamo/backend.py 77-124](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/backend.py#L77-L124)[tt_torch/tools/utils.py 898-934](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/utils.py#L898-L934)[tt_torch/csrc/bindings.cpp 413-434](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/bindings.cpp#L413-L434)

## Summary

The tt-torch architecture implements a layered design separating concerns:

1.   **Frontend Layer**: Provides PyTorch and ONNX entry points with unified backend registration
2.   **Compilation Layer**: Progressive IR lowering through multiple MLIR dialects
3.   **Execution Layer**: Flexible executor framework supporting full and op-by-op execution modes
4.   **Runtime Layer**: C++ bindings providing tensor management and hardware interaction

This architecture enables debugging at multiple abstraction levels while supporting production-optimized full compilation. The multi-device support and caching strategies provide scalability and performance optimization.

**Sources**: All files listed in previous sections.

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-torch/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh