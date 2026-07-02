---
project: tt-torch
github: https://github.com/tenstorrent/tt-torch
deepwiki: https://deepwiki.com/tenstorrent/tt-torch/1-overview
---

# Overview

Relevant source files
*   [.github/Dockerfile.base](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/Dockerfile.base)
*   [.github/Dockerfile.ci](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/Dockerfile.ci)
*   [README.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/README.md?plain=1)
*   [docs/src/adding_models.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/adding_models.md?plain=1)
*   [docs/src/controlling.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/controlling.md?plain=1)
*   [docs/src/overview.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/overview.md?plain=1)
*   [docs/src/pre_commit.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/pre_commit.md?plain=1)
*   [docs/src/test.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/test.md?plain=1)
*   [env/activate](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/env/activate)
*   [requirements.txt](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt)
*   [setup.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py)
*   [third_party/CMakeLists.txt](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/third_party/CMakeLists.txt)
*   [tt_torch/__init__.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/__init__.py)
*   [tt_torch/csrc/CMakeLists.txt](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/CMakeLists.txt)
*   [tt_torch/csrc/bindings.cpp](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/bindings.cpp)
*   [tt_torch/csrc/tt-mlir-interface.cpp](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/tt-mlir-interface.cpp)
*   [tt_torch/csrc/tt-mlir-interface.hpp](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/tt-mlir-interface.hpp)
*   [tt_torch/tools/profile_util.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/profile_util.py)

This document provides a high-level overview of the tt-torch system architecture, its compilation pipeline, and core components. tt-torch is a PyTorch-based compiler frontend that enables running PyTorch models on Tenstorrent AI accelerators.

> **DEPRECATION NOTICE**: tt-torch is deprecated. For current PyTorch support on Tenstorrent hardware, see [TT-XLA](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/TT-XLA) This documentation is maintained for reference and existing deployments.

For detailed information on specific subsystems:

*   Installation and environment setup: see [Installation and Setup](https://deepwiki.com/tenstorrent/tt-torch/2.1-installation-and-setup)
*   Compilation pipeline details: see [Compilation Pipeline](https://deepwiki.com/tenstorrent/tt-torch/3.1-compilation-pipeline)
*   Building from source: see [Build System](https://deepwiki.com/tenstorrent/tt-torch/4.1-build-system)
*   Testing infrastructure: see [Testing Framework](https://deepwiki.com/tenstorrent/tt-torch/4.2-testing-framework)
*   Supported models: see [Model Support](https://deepwiki.com/tenstorrent/tt-torch/5-model-support)

* * *

## Purpose and Scope

tt-torch serves as a bridge between PyTorch models and Tenstorrent hardware by providing:

1.   **Graph Capture**: Integration with PyTorch 2.0's `torch.compile` API via Dynamo backends
2.   **IR Lowering**: Multi-stage lowering through torch-mlir, StableHLO, TTIR, and TTNN dialects
3.   **Hardware Execution**: Runtime bindings to execute compiled binaries on Tenstorrent devices
4.   **Debugging Tools**: Op-by-op compilation and execution modes for model bring-up
5.   **Testing Framework**: Comprehensive verification system for correctness and performance

The system handles PyTorch `nn.Module` instances and ONNX models, compiling them to hardware-executable flatbuffer binaries.

**Sources**: [README.md 20-34](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/README.md?plain=1#L20-L34)[docs/src/overview.md 1-29](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/overview.md?plain=1#L1-L29)

* * *

## System Architecture

tt-torch is structured as a multi-layered compilation and execution system:

**Architecture Diagram: tt-torch System Layers**

The system operates through distinct layers:

| Layer | Components | Purpose |
| --- | --- | --- |
| **User Layer** | `torch.compile`, `import tt_torch` | API entry points for model compilation |
| **Frontend Layer** | `DynamoBackend`, `CompilerConfig`, FX passes | Graph capture, optimization, and configuration |
| **MLIR Layer** | torch-mlir, StableHLO, tt-mlir | Progressive IR lowering to hardware abstractions |
| **Execution Layer** | `Executor` hierarchy, Python-C++ bindings | Model execution and device management |
| **Hardware Layer** | tt-metal, Tenstorrent devices | Low-level hardware operations |

**Sources**: [tt_torch/__init__.py 1-75](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/__init__.py#L1-L75)[tt_torch/dynamo/backend.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/backend.py)[tt_torch/csrc/bindings.cpp 1-607](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/bindings.cpp#L1-L607)[tt_torch/csrc/tt-mlir-interface.cpp 1-294](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/tt-mlir-interface.cpp#L1-L294)

* * *

## Compilation Pipeline

The compilation pipeline transforms PyTorch models through multiple intermediate representations:

**Compilation Pipeline Diagram: Model to Execution**

### Stage Descriptions

**Stage 1: Graph Capture** ([tt_torch/dynamo/backend.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/backend.py))

*   Invoked via `torch.compile(backend="tt-legacy")`
*   Captures execution as FX graph representation
*   Applies decomposition, constant folding, shape propagation
*   Configurable via `CompilerConfig.compile_depth`

**Stage 2: torch-mlir Lowering** ([third_party/torch-mlir](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/third_party/torch-mlir))

*   Converts FX graph to Torch dialect MLIR
*   Lowers through Torch backend to StableHLO
*   Provides stable interface between PyTorch and tt-mlir

**Stage 3: tt-mlir Compilation** ([tt_torch/csrc/tt-mlir-interface.cpp 134-291](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/tt-mlir-interface.cpp#L134-L291))

*   `compileStableHLOToTTIR()`: Converts StableHLO to TTIR
*   `compileTTIRToTTNN()`: Lowers TTIR to TTNN with hardware-specific optimizations
*   Generates flatbuffer binary for runtime execution
*   Functions exposed via C++ interface at [tt_torch/csrc/tt-mlir-interface.hpp](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/tt-mlir-interface.hpp)

**Stage 4: Runtime Execution** ([tt_torch/csrc/bindings.cpp 216-410](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/bindings.cpp#L216-L410))

*   `create_binary_from_bytestream()`: Instantiates runtime binary from flatbuffer
*   `preprocess_inputs()`: Tilizes inputs and allocates device memory
*   `run()` or `run_async()`: Submits work to hardware via `tt::runtime::submit`
*   Returns PyTorch tensors to host

**Sources**: [docs/src/overview.md 16-24](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/overview.md?plain=1#L16-L24)[tt_torch/csrc/tt-mlir-interface.cpp 134-291](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/tt-mlir-interface.cpp#L134-L291)[tt_torch/csrc/bindings.cpp 216-410](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/bindings.cpp#L216-L410)

* * *

## Core Components

### Backend Registration

tt-torch registers two Dynamo backends on import:

| Backend Name | Implementation | Purpose |
| --- | --- | --- |
| `"tt-legacy"` | `tt_torch.dynamo.backend.backend()` | Main Dynamo-based compilation (recommended) |
| `"tt-experimental"` | `tt_torch.dynamo.experimental.xla_backend` | XLA/PJRT-based execution (experimental) |

Registration occurs at [tt_torch/__init__.py 26-28](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/__init__.py#L26-L28) when `import tt_torch` executes.

**Sources**: [tt_torch/__init__.py 1-75](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/__init__.py#L1-L75)[setup.py 226-228](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L226-L228)

### Executor Hierarchy

The executor system provides multiple execution strategies:

**Executor Class Hierarchy**

*   `Executor`: Base class providing binary execution, device management, type conversion ([tt_torch/dynamo/executor.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/executor.py))
*   `OpByOpExecutor`: Compiles and executes operations individually for debugging
*   `TorchExecutor`: Processes FX graph nodes one-by-one ([tt_torch/dynamo/torch_backend.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/torch_backend.py))
*   `StablehloExecutor`: Executes StableHLO operations individually ([tt_torch/dynamo/shlo_backend.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/shlo_backend.py))
*   `OnnxExecutor`: Handles ONNX model compilation and execution

**Sources**: [tt_torch/dynamo/executor.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/executor.py)[docs/src/adding_models.md 68-143](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/adding_models.md?plain=1#L68-L143)

### Compiler Configuration

The `CompilerConfig` class controls compilation behavior:

`# Key configuration optionsclass CompilerConfig:    compile_depth: CompileDepth       # How far to compile (TORCH_FX, STABLEHLO, TTNN_IR, EXECUTE, etc.)    op_by_op_backend: OpByOpBackend   # Which backend for op-by-op (TORCH or STABLEHLO)    enable_consteval: bool             # Enable constant folding    consteval_parameters: bool         # Fold model parameters as constants    inline_parameters: bool            # Inline parameters in binary`
Configuration can be set via:

1.   **Environment variables**: `TT_TORCH_COMPILE_DEPTH`, `TT_TORCH_CONSTEVAL`, etc.
2.   **Programmatically**: Pass `CompilerConfig` instance to `BackendOptions`

**Sources**: [tt_torch/tools/utils.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/utils.py)[docs/src/controlling.md 1-48](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/controlling.md?plain=1#L1-L48)

* * *

## Execution Modes

tt-torch supports multiple execution strategies configurable via `CompilerConfig.compile_depth`:

| Mode | `CompileDepth` | Behavior |
| --- | --- | --- |
| **FX Graph Only** | `TORCH_FX` | Return FX graph without compilation |
| **StableHLO IR** | `STABLEHLO` | Compile to StableHLO, return IR string |
| **TTNN IR** | `TTNN_IR` | Compile to TTNN, return IR string |
| **Op-by-Op Compile** | `COMPILE_OP_BY_OP` | Compile each unique op, no execution |
| **Op-by-Op Execute** | `EXECUTE_OP_BY_OP` | Compile and execute each unique op individually |
| **Full Execution** | `EXECUTE` | Full compilation to binary and hardware execution |

### Op-by-Op Flow

The op-by-op execution mode enables debugging by:

1.   Extracting individual operations from the FX graph
2.   Compiling each unique operation (based on op type and input shapes) independently
3.   Executing on hardware and comparing with golden PyTorch outputs
4.   Recording results to JSON for analysis

This mode is engaged with `compile_depth = CompileDepth.EXECUTE_OP_BY_OP` and allows testing models with unsupported operations, as compilation continues past failures.

**Sources**: [docs/src/adding_models.md 36-65](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/adding_models.md?plain=1#L36-L65)[docs/src/overview.md 24-28](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/overview.md?plain=1#L24-L28)

* * *

## External Dependencies

tt-torch integrates multiple external projects via CMake:

### Core Dependencies

**Dependency Graph**

### Version Management

Versions are pinned in [third_party/CMakeLists.txt](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/third_party/CMakeLists.txt):

| Component | Version/Commit | Purpose |
| --- | --- | --- |
| tt-xla | `cb4cc36` | Provides PJRT plugin, tt-mlir integration |
| torch-mlir | `64a937e` | PyTorch to MLIR conversion |
| tt-forge-models | `c8a3cb8` | Reference model implementations |
| tt-mlir | Via tt-xla | Core compiler, TTIR/TTNN dialects |
| tt-metal | Via tt-mlir | Hardware runtime API |

### Build Artifacts

The build system produces several shared libraries:

| Artifact | Source | Purpose |
| --- | --- | --- |
| `tt_mlir.so` | [tt_torch/csrc/bindings.cpp](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/bindings.cpp) | Python extension for runtime bindings |
| `libTTMLIRCompiler.so` | tt-xla/tt-mlir | MLIR compilation passes |
| `libTTMLIRRuntime.so` | tt-xla/tt-mlir | Runtime execution engine |
| `pjrt_plugin_tt.so` | tt-xla | XLA PJRT plugin for experimental backend |
| `ttmlir-opt` | tt-mlir | Standalone compiler tool |

**Sources**: [third_party/CMakeLists.txt 1-191](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/third_party/CMakeLists.txt#L1-L191)[setup.py 132-146](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L132-L146)[requirements.txt 1-61](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt#L1-L61)

* * *

## Environment Setup

The runtime requires specific environment variables configured via [env/activate](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/env/activate):

| Variable | Purpose | Default |
| --- | --- | --- |
| `TTMLIR_TOOLCHAIN_DIR` | LLVM/MLIR toolchain location | `/opt/ttmlir-toolchain/` |
| `TT_TORCH_HOME` | Repository root | Current directory |
| `TT_METAL_HOME` | tt-metal installation | `third_party/tt-mlir/.../tt-metal` |
| `LD_LIBRARY_PATH` | Shared library paths | PyTorch, MLIR, tt-metal libs |
| `PYTHONPATH` | Python module paths | tt-torch, tt-metal, MLIR packages |
| `ARCH_NAME` | Target architecture | `wormhole_b0` |

The activation script also creates a Python 3.11 virtual environment and installs dependencies from [requirements.txt](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt)

**Sources**: [env/activate 1-52](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/env/activate#L1-L52)[setup.py 150-161](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L150-L161)

* * *

## Testing Infrastructure

tt-torch provides a comprehensive testing framework:

### Test Categories

| Category | Location | Purpose |
| --- | --- | --- |
| **Unit Tests** | `tests/torch/`, `tests/onnx/` | Basic operation verification |
| **Model Tests** | `tests/models/` | End-to-end model execution |
| **CI Workflows** | `.github/workflows/` | Automated testing on multiple architectures |

### ModelTester Framework

Tests inherit from `ModelTester` or `OnnxModelTester` base classes ([tests/utils.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/utils.py)):

`class ModelTester:    def _load_model(self):      # Abstract: load the model    def _load_inputs(self):     # Abstract: prepare inputs    def test_model(self):       # Execute and verify`
The framework provides:

*   Automatic verification against PyTorch golden outputs
*   PCC (Pearson Correlation Coefficient) and ATOL checking
*   JSON result logging for op-by-op analysis
*   Performance profiling integration

**Sources**: [tests/utils.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/utils.py)[docs/src/adding_models.md 12-26](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/adding_models.md?plain=1#L12-L26)[docs/src/test.md 1-174](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/test.md?plain=1#L1-L174)

* * *

## Getting Started

For new users:

1.   **Installation**: See [Installation and Setup](https://deepwiki.com/tenstorrent/tt-torch/2.1-installation-and-setup) for building from source or installing from wheel
2.   **Basic Usage**: See [Basic Usage Examples](https://deepwiki.com/tenstorrent/tt-torch/2.2-basic-usage-examples) for simple model compilation examples
3.   **Testing**: Run `pytest tests/torch/test_basic.py` to verify the installation
4.   **Demos**: Try `demos/resnet/resnet50_demo.py` for a complete example

For developers adding models:

1.   Consult [Adding New Models](https://deepwiki.com/tenstorrent/tt-torch/6.2-adding-new-models) for step-by-step instructions
2.   Review existing tests in `tests/models/` for patterns
3.   Use op-by-op mode initially for debugging: `CompileDepth.EXECUTE_OP_BY_OP`
4.   Progress to full execution once operations are verified: `CompileDepth.EXECUTE`

**Sources**: [README.md 26-30](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/README.md?plain=1#L26-L30)[docs/src/test.md 22-49](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/test.md?plain=1#L22-L49)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-torch/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh