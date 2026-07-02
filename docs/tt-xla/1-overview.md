---
project: tt-xla
github: https://github.com/tenstorrent/tt-xla
deepwiki: https://deepwiki.com/tenstorrent/tt-xla/1-overview
---

# Overview

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.gitignore)
*   [README.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1)
*   [docs/src/getting_started.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1)
*   [docs/src/getting_started_build_from_source.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1)
*   [docs/src/getting_started_docker.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_docker.md?plain=1)
*   [docs/src/imgs/test_infra.png](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/imgs/test_infra.png)
*   [docs/src/imgs/tt_smi.png](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/imgs/tt_smi.png)
*   [docs/src/imgs/tt_xla_logo.png](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/imgs/tt_xla_logo.png)
*   [docs/src/test_infra.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/test_infra.md?plain=1)
*   [tests/filecheck/add.ttnn.mlir](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/filecheck/add.ttnn.mlir)
*   [tests/filecheck/rms_norm.ttir.mlir](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/filecheck/rms_norm.ttir.mlir)

This page describes the purpose, architecture, and major subsystems of the `tt-xla` repository. It is the entry point for understanding how the codebase fits together. For setup instructions, see [Getting Started](https://deepwiki.com/tenstorrent/tt-xla/2-getting-started). For detailed treatment of individual subsystems, follow the links to the relevant child pages throughout this document.

* * *

## What is TT-XLA?

TT-XLA is a PJRT (Portable JAX Runtime) plugin that enables running JAX, PyTorch, and vLLM models on Tenstorrent AI accelerator hardware. It acts as the integration layer between ML frameworks and the Tenstorrent compiler (TT-MLIR) and runtime (TT-Metal) stack.

The core artifact is `pjrt_plugin_tt.so`, a shared library implementing the PJRT C API. This plugin compiles StableHLO graphs from JAX and PyTorch into Tenstorrent-executable binaries and manages execution on devices including n150, p150, n300, and llmbox configurations.

**Key components by architectural importance:**

| Component | Importance | Description |
| --- | --- | --- |
| **PJRT Plugin** | Core (462.77) | Implements PJRT C API in `pjrt_plugin_tt.so`, manages compilation and execution |
| **Test Configuration** | Critical (322.28) | YAML-based model test definitions tracking EXPECTED_PASSING/KNOWN_FAILURE_XFAIL/NOT_SUPPORTED_SKIP status |
| **CI/CD Pipeline** | Infrastructure (166.78) | GitHub Actions workflows for building, testing, and releasing across hardware targets |
| **vLLM Integration** | Specialized (78.13) | `TTPlatform`, `TTWorker`, `TTModelRunner` for LLM serving |
| **PyTorch Backend** | Frontend (44.45) | `torch_plugin_tt` with FX graph transformation pipeline |

Sources: [README.md 19-28](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L19-L28)[README.md 26-42](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L26-L42)

* * *

## Position in the Tenstorrent Software Stack

**Diagram: Overall System Architecture**

Sources: [README.md 19-28](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L19-L28)[README.md 44-51](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L44-L51)[docs/src/getting_started.md 1-3](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1#L1-L3)

* * *

## Major Subsystems

### 1. PJRT Plugin (`pjrt_plugin_tt.so`)

The PJRT plugin is the central hub (importance: 462.77) connecting ML frameworks to Tenstorrent hardware. Built via CMake in [CMakeLists.txt](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt) it implements the PJRT C API specification and manages the complete compilation and execution lifecycle.

**Diagram: PJRT Plugin Architecture**

The plugin is distributed as part of the `pjrt_plugin_tt` Python wheel, which includes the `.so` binary, TT-MLIR compiler libraries in `lib/`, and TT-Metal runtime assets in `tt-metal/`. See [PJRT Plugin System](https://deepwiki.com/tenstorrent/tt-xla/4.2-pjrt-plugin-system) for implementation details.

Sources: [README.md 19-28](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L19-L28)[docs/src/getting_started_build_from_source.md 174-187](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L174-L187)

* * *

### 2. Compilation Pipeline

**Diagram: Data Flow Through Compilation**

The compilation pipeline transforms user code through multiple stages:

1.   **Framework tracing**: JAX's `jit` or PyTorch's `torch.compile` produces a computation graph
2.   **Graph passes**: Apply optimization passes including fusion, decompositions, and composite op formation
3.   **StableHLO**: Emit StableHLO representation with metadata
4.   **TT-MLIR lowering**: Progressive lowering through TTIR → TTNN dialects (external dependency: [tt-mlir](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tt-mlir))
5.   **Code generation**: Produce Flatbuffer binary or compiled `.so` consumed by TT-Metal

Compilation behavior is controlled by `CompileOptions` including `optimization_level`, `math_fidelity`, `enable_bfp8_conversion`, and `backend` selection. For detailed treatment, see [Compilation Pipeline](https://deepwiki.com/tenstorrent/tt-xla/4.1-compilation-pipeline).

Sources: [README.md 19-28](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L19-L28)[tests/filecheck/rms_norm.ttir.mlir 1-2](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/filecheck/rms_norm.ttir.mlir#L1-L2)[tests/filecheck/add.ttnn.mlir 1-2](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/filecheck/add.ttnn.mlir#L1-L2)

* * *

### 3. Framework Integrations

TT-XLA provides three framework integration points, each with distinct architecture:

**JAX Integration** (via `jax_plugin_tt`):

*   Thin Python wrapper in [python_package/jax_plugin_tt/__init__.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/python_package/jax_plugin_tt/__init__.py)
*   Registered via setuptools `entry_points` under the `jax_plugins` group
*   JAX's `jit` decorator automatically routes to `pjrt_plugin_tt.so` when device is `'tt'`
*   Direct PJRT C API invocation with minimal graph transformation

**PyTorch Integration** (via `torch_plugin_tt`, importance: 44.45):

*   `torch.compile(backend='tt')` entry point in [python_package/torch_plugin_tt/__init__.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/python_package/torch_plugin_tt/__init__.py)
*   `TTPlugin` implements PyTorch's `DevicePlugin` interface
*   `torch_pass_pipeline()` applies FX graph transformations: 
    *   Fusion passes (attention, linear layers)
    *   Composite op formation (RMS norm, layer norm)
    *   Decompositions for unsupported ops
    *   Metadata propagation

*   `XLAExecutor` compiles transformed graph via PJRT

**vLLM Integration** (importance: 78.13):

*   Specialized LLM serving infrastructure in [integrations/vllm_tt/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/integrations/vllm_tt/)
*   `TTPlatform` implements vLLM's platform interface
*   `TTWorker` manages device allocation and distributed execution
*   `TTModelRunner` handles generation tasks with optimization strategies: 
    *   `dummy_run()` pre-compilation to fixed shapes
    *   Token/request padding to prevent expensive recompilation
    *   SPMD setup for tensor parallelism

*   `TTPoolingRunner` for embedding tasks
*   Custom layers: `XlaMergedColumnParallelLinear`, `XlaQKVParallelLinear`

For detailed framework integration documentation, see [Framework Integration](https://deepwiki.com/tenstorrent/tt-xla/5-framework-integration).

Sources: [README.md 19-28](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L19-L28)[docs/src/getting_started_build_from_source.md 174-187](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L174-L187)

* * *

### 4. Build System

The build system (importance: 462.77) uses CMake with external dependency management and Python wheel packaging.

**Diagram: Build Dependency Chain**

**Build Process:**

1.   **CMake configuration** in [CMakeLists.txt](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt) defines library targets:

    *   `TTPJRTUtils` (utilities and logging)
    *   `TTPJRTApi` (PJRT C API implementation)
    *   `TTPJRTBindings` (C++ bindings)
    *   `TTPJRTTTDylib` (final `pjrt_plugin_tt.so`)

2.   **External dependencies**:

    *   `tt-mlir` fetched as `ExternalProject` at version pinned by `TT_MLIR_VERSION` SHA
    *   `loguru` for logging infrastructure
    *   `tt-metal` runtime automatically included via tt-mlir

3.   **Python wheel** built via [python_package/setup.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/python_package/setup.py):

```
pjrt_plugin_tt/
    pjrt_plugin_tt.so        # PJRT plugin binary
    tt-metal/                # TT Metal runtime assets
    lib/                     # tt-mlir and tt-metal .so files
jax_plugin_tt/
    __init__.py              # JAX integration
torch_plugin_tt/
    __init__.py              # PyTorch integration
vllm_tt/
    platform.py              # vLLM integration
```
4.   **CI/CD** (importance: 166.78):

    *   Docker images: base, CI, IRD variants in [.github/workflows/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.github/workflows/)
    *   Manylinux wheel builds for portability
    *   Artifact caching by SHA for efficiency
    *   Multi-stage builds for different configurations (Release/Explorer/Debug)

For complete build instructions, see [Build System](https://deepwiki.com/tenstorrent/tt-xla/3-build-system).

Sources: [docs/src/getting_started_build_from_source.md 114-188](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L114-L188)[.gitignore 1-46](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.gitignore#L1-L46)

* * *

### 5. Testing Infrastructure

The testing system (importance: 322.28 for test configs, 71.91 for test runner, 93.48 for CI orchestration) validates model correctness and tracks bringup status across multiple hardware configurations.

**Diagram: Testing Ecosystem**

**Test Organization:**

| Category | Location | Primary Classes |
| --- | --- | --- |
| JAX ops/graphs | [tests/jax/single_chip/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/jax/single_chip/) | `OpTester`, `GraphTester` |
| JAX models | [tests/jax/models/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/jax/models/) | `JaxModelTester`, `DynamicJaxModelTester` |
| PyTorch ops | [tests/torch/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/torch/) | `TorchWorkload`, `TorchDeviceRunner` |
| Multi-chip | [tests/jax/multi_chip/n300/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/jax/multi_chip/n300/)[tests/jax/multi_chip/llmbox/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/jax/multi_chip/llmbox/) | `JaxMultichipWorkload` |
| IR validation | [tests/filecheck/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/filecheck/) | `pytest.mark.filecheck` decorator |
| vLLM sampling | [tests/vllm/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/vllm/) | Sampling correctness tests (importance: 35.42) |

Tests compare device outputs against CPU reference using PCC (Pearson Correlation Coefficient) and ATOL thresholds defined in YAML configs. The test matrix JSON files drive CI execution across n150, p150, n300, and llmbox hardware targets.

For the complete testing framework, see [Testing Infrastructure](https://deepwiki.com/tenstorrent/tt-xla/6-testing-infrastructure).

Sources: [docs/src/getting_started_build_from_source.md 188-194](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L188-L194)[docs/src/test_infra.md 1-155](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/test_infra.md?plain=1#L1-L155)

* * *

## Related Tenstorrent Projects

TT-XLA is part of a broader Tenstorrent software ecosystem:

| Project | Role | Use Case |
| --- | --- | --- |
| **TT-MLIR** | Compiler (external dependency) | Lowers StableHLO → TTIR → TTNN, produces Flatbuffer/`.so` binaries |
| **TT-Metal** | Runtime (external dependency) | Low-level kernel execution, memory management, device orchestration |
| **TT-Forge-FE** | Alternative frontend | ONNX/PaddlePaddle support (single-chip only, TVM-based) |
| **TT-TVM** | Compiler infrastructure | TVM fork for TT-Forge-FE graph compilation |
| **TT-Torch** | Deprecated | Former PyTorch frontend; **users should migrate to TT-XLA** |

**Architectural Positioning:**

*   **For PyTorch, JAX, vLLM**: Use **TT-XLA** (this repository) - supports both single and multi-chip
*   **For ONNX, PaddlePaddle**: Use **TT-Forge-FE** - single-chip only
*   **All frontends** compile through **TT-MLIR** and execute on **TT-Metal**

Sources: [README.md 30-51](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L30-L51)

* * *

## Quick Start

To verify a working installation after installing the wheel:

For full setup instructions, see [Getting Started](https://deepwiki.com/tenstorrent/tt-xla/2-getting-started).

Sources: [docs/src/getting_started.md 51-70](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1#L51-L70)[docs/src/getting_started_build_from_source.md 152-159](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L152-L159)

Dismiss
Refresh this wiki

Enter email to refresh