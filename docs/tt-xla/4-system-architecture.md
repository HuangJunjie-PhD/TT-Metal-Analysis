---
project: tt-xla
github: https://github.com/tenstorrent/tt-xla
deepwiki: https://deepwiki.com/tenstorrent/tt-xla/4-system-architecture
---

# System Architecture

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.gitignore)
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt)
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
*   [third_party/CMakeLists.txt](https://github.com/tenstorrent/tt-xla/blob/c77995f6/third_party/CMakeLists.txt)

## Purpose and Scope

This document provides a high-level overview of the TT-XLA system architecture, describing how the various components interact to enable running JAX, PyTorch, and vLLM models on Tenstorrent hardware. It covers the major architectural layers, data flow through the compilation pipeline, and the relationships between core components.

For detailed information about specific subsystems, see:

*   **Compilation Pipeline**: [4.1](https://deepwiki.com/tenstorrent/tt-xla/4.1-compilation-pipeline) - Detailed flow from framework code to device execution
*   **PJRT Plugin Implementation**: [4.2](https://deepwiki.com/tenstorrent/tt-xla/4.2-pjrt-plugin-system) - Technical details of the PJRT plugin interface
*   **Framework Integration**: [5](https://deepwiki.com/tenstorrent/tt-xla/5-framework-integration) - How JAX, PyTorch, and vLLM integrate with TT-XLA
*   **Build System**: [3](https://deepwiki.com/tenstorrent/tt-xla/3-build-system) - CMake configuration and dependency management

## Architectural Overview

TT-XLA serves as an integration layer between machine learning frameworks and Tenstorrent hardware, using the PJRT (Portable JAX Runtime) interface as the primary integration mechanism. The system transforms framework-specific computational graphs into optimized device code through a multi-stage compilation pipeline.

### High-Level Architecture
```mermaid
graph TB
    subgraph "Test Discovery Layer"
        DynamicLoader["DynamicLoader (base)"]
        TorchLoader["TorchDynamicLoader"]
        JaxLoader["JaxDynamicLoader"]
        ForgeModels["third_party/tt_forge_models<br/>loader.py files"]
    end
    
    subgraph "Test Entry Points"
        TestModels["tests/runner/test_models.py"]
        TestAllTorch["test_all_models_torch()"]
        TestAllJax["test_all_models_jax()"]
        TestLLMs["test_llms_torch()"]
        TestOpByOp["test_all_models_op_by_op()"]
    end
    
    subgraph "Test Execution Layer"
        ModelTester["ModelTester (base)"]
        TorchTester["TorchModelTester"]
        JaxTester["JaxModelTester"]
        DynamicTorchTester["DynamicTorchModelTester"]
        DynamicJaxTester["DynamicJaxModelTester"]
    end
    
    subgraph "Configuration & Fixtures"
        PytestIni["pytest.ini"]
        Conftest["conftest.py"]
        TestConfig["test_config_*.yaml"]
        ModelTestConfig["ModelTestConfig"]
        ComparisonConfig["ComparisonConfig"]
    end
    
    subgraph "Validation & Analysis"
        Evaluator["Evaluator"]
        ComparisonResult["ComparisonResult"]
        FailingReasonsFinder["FailingReasonsFinder"]
        FailingReasons["FailingReasons (50+ patterns)"]
    end
    
    DynamicLoader --> TorchLoader
    DynamicLoader --> JaxLoader
    TorchLoader --> ForgeModels
    JaxLoader --> ForgeModels
    
    ForgeModels --> TestModels
    TestModels --> TestAllTorch
    TestModels --> TestAllJax
    TestModels --> TestLLMs
    TestModels --> TestOpByOp
    
    TestAllTorch --> DynamicTorchTester
    TestAllJax --> DynamicJaxTester
    TestLLMs --> DynamicTorchTester
    
    ModelTester --> TorchTester
    ModelTester --> JaxTester
    TorchTester --> DynamicTorchTester
    JaxTester --> DynamicJaxTester
    
    PytestIni --> TestModels
    Conftest --> TestModels
    TestConfig --> ModelTestConfig
    ModelTestConfig --> DynamicTorchTester
    ModelTestConfig --> DynamicJaxTester
    ComparisonConfig --> DynamicTorchTester
    ComparisonConfig --> DynamicJaxTester
    
    DynamicTorchTester --> Evaluator
    DynamicJaxTester --> Evaluator
    Evaluator --> ComparisonResult
    DynamicTorchTester -.exception.-> FailingReasonsFinder
    DynamicJaxTester -.exception.-> FailingReasonsFinder
    FailingReasonsFinder --> FailingReasons
```

**Architecture Overview**: The test framework follows a layered architecture. At the top, `DynamicLoader` classes discover models from the `tt_forge_models` repository. Test entry points in `test_models.py` parametrize these models with run modes (inference/training) and parallelism modes (single-device/data-parallel/tensor-parallel). The execution layer uses framework-specific `ModelTester` subclasses to compile and run models on both CPU and TT hardware. Results flow through the validation layer (`Evaluator` and `ComparisonResult`) which applies PCC and ATOL thresholds. On failure, the `FailingReasonsFinder` classifies exceptions using 50+ predefined patterns.
```


```mermaid
graph TB
    subgraph "Framework Layer"
        JAX["JAX Models<br/>@jit decorated functions"]
        PyTorch["PyTorch Models<br/>torch.compile"]
        vLLM["vLLM Engine<br/>LLM Inference"]
    end
    
    subgraph "TT-XLA Frontend"
        JAXPlugin["jax_plugin_tt<br/>python_package/jax_plugin_tt/"]
        TorchPlugin["torch_plugin_tt<br/>python_package/torch_plugin_tt/"]
        vLLMPlugin["vllm_tt plugin<br/>integrations/vllm_plugin/"]
        PJRT["PJRT Plugin<br/>pjrt_plugin_tt.so<br/>pjrt_implementation/src/"]
    end
    
    subgraph "Compilation Layer"
        StableHLO["StableHLO/SHLO IR<br/>Intermediate Representation"]
        TTMLIR["TT-MLIR Compiler<br/>third_party/tt-mlir/<br/>TTIR → TTNN Dialects"]
    end
    
    subgraph "Runtime Layer"
        TTMetal["TT-Metal Runtime<br/>Device Kernels<br/>tt-metal libraries"]
    end
    
    subgraph "Hardware Layer"
        TTDevice["Tenstorrent Devices<br/>n150, p150, n300, llmbox"]
    end
    
    JAX --> JAXPlugin
    PyTorch --> TorchPlugin
    vLLM --> vLLMPlugin
    
    JAXPlugin --> PJRT
    TorchPlugin --> PJRT
    vLLMPlugin --> PJRT
    
    PJRT --> StableHLO
    StableHLO --> TTMLIR
    TTMLIR --> TTMetal
    TTMetal --> TTDevice
```


**Sources:**[README.md 19](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L19-L19)[README.md 26-33](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L26-L33)[CMakeLists.txt 10-23](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L10-L23)

## Core Components

### Framework Integration Layer

TT-XLA provides thin wrapper packages for each supported framework that set up the PJRT plugin and enable framework-specific tracing and compilation:

| Component | Location | Purpose |
| --- | --- | --- |
| `jax_plugin_tt` | [python_package/jax_plugin_tt/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/python_package/jax_plugin_tt/) | Imports and configures PJRT plugin for JAX |
| `torch_plugin_tt` | [python_package/torch_plugin_tt/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/python_package/torch_plugin_tt/) | Registers PJRT plugin as PyTorch XLA backend |
| `vllm_tt` | [integrations/vllm_plugin/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/integrations/vllm_plugin/) | Integrates with vLLM's platform interface for LLM serving |

These plugins are packaged together in the Python wheel distribution alongside the core PJRT plugin binary.

**Sources:**[docs/src/getting_started_build_from_source.md 174-185](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L174-L185)

### PJRT Plugin (`pjrt_plugin_tt.so`)

```mermaid
graph TB
    subgraph "PJRT Plugin Structure"
        PJRTDylib["pjrt_plugin_tt.so<br/>Main Plugin Binary"]
        
        subgraph "Implementation Layers"
            Bindings["TTPJRTBindings<br/>pjrt_implementation/src/"]
            API["TTPJRTApi<br/>pjrt_implementation/src/api/"]
            Utils["TTPJRTUtils<br/>pjrt_implementation/src/utils/"]
        end
        
        subgraph "External Dependencies"
            TTMLIRCompiler["TTMLIRCompiler<br/>libTTMLIRCompiler.so"]
            TTMLIRRuntime["TTMLIRRuntime<br/>libTTMLIRRuntime.so"]
            Loguru["loguru<br/>Logging Library"]
        end
    end
    
    PJRTDylib --> Bindings
    Bindings --> API
    API --> TTMLIRCompiler
    API --> TTMLIRRuntime
    API --> Utils
    Utils --> Loguru
```

**Key Responsibilities:**
- **Device Management**: Enumerates and manages Tenstorrent devices
- **Graph Compilation**: Accepts StableHLO graphs and invokes TT-MLIR compiler
- **Execution**: Schedules compiled programs on devices and manages buffers
- **Memory Management**: Allocates and tracks device memory
```


The PJRT plugin is the central integration point with an importance score of 462.77, making it the most critical component in the system. It implements the standard PJRT C API, providing a uniform device interface that frameworks can target without TT-specific modifications.

**Key Responsibilities:**

*   **Device Management**: Enumerates and manages Tenstorrent devices
*   **Graph Compilation**: Accepts StableHLO graphs and invokes TT-MLIR compiler
*   **Execution**: Schedules compiled programs on devices and manages buffers
*   **Memory Management**: Allocates and tracks device memory

**Sources:**[CMakeLists.txt 10-23](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L10-L23)[pjrt_implementation/src/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/src/)

### Intermediate Representation Layer

TT-XLA uses StableHLO (Stable HLO) as the intermediate representation format between frameworks and the TT-MLIR compiler. StableHLO provides a stable, portable representation of ML operations that can be generated from multiple frameworks.

**Why StableHLO:**

*   Framework-agnostic representation
*   Well-defined semantics for ML operations
*   Versioned format with backward compatibility guarantees
*   Standard format supported by multiple compiler toolchains

**Sources:**[README.md 19](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L19-L19)[README.md 32](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L32-L32)

### TT-MLIR Compiler

TT-MLIR is an external dependency (built via CMake ExternalProject) that performs the core compilation from StableHLO to device-executable code. The compiler implements multiple dialect levels:

1.   **TTIR (Tenstorrent IR)**: High-level operations specific to TT hardware (e.g., `ttir.rms_norm`)
2.   **TTNN (Tenstorrent Neural Network)**: Lower-level operations mapping to device primitives (e.g., `ttnn.add`)
3.   **Code Generation**: Produces device kernels for TT-Metal runtime

**Configuration:**

The TT-MLIR dependency is configured in [third_party/CMakeLists.txt 47-84](https://github.com/tenstorrent/tt-xla/blob/c77995f6/third_party/CMakeLists.txt#L47-L84) with specific build options:

The version is pinned to a specific commit SHA to ensure reproducible builds: [third_party/CMakeLists.txt 8](https://github.com/tenstorrent/tt-xla/blob/c77995f6/third_party/CMakeLists.txt#L8-L8)

**Sources:**[third_party/CMakeLists.txt 33-96](https://github.com/tenstorrent/tt-xla/blob/c77995f6/third_party/CMakeLists.txt#L33-L96)[tests/filecheck/rms_norm.ttir.mlir 1-2](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/filecheck/rms_norm.ttir.mlir#L1-L2)[tests/filecheck/add.ttnn.mlir 1-2](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/filecheck/add.ttnn.mlir#L1-L2)

### TT-Metal Runtime

TT-Metal provides the low-level runtime services for executing compiled code on Tenstorrent devices. It is included as a transitive dependency through TT-MLIR and packaged in the final distribution.

**Runtime Services:**

*   Device initialization and configuration
*   Kernel loading and dispatch
*   Inter-chip communication (for multi-chip configurations)
*   Performance tracing and profiling

**Sources:**[third_party/CMakeLists.txt 38-39](https://github.com/tenstorrent/tt-xla/blob/c77995f6/third_party/CMakeLists.txt#L38-L39)

## Component Dependency Graph

```mermaid
graph TD
    subgraph "Build Outputs"
        Wheel["pjrt_plugin_tt-*.whl<br/>Python Distribution"]
        PJRTPlugin["pjrt_plugin_tt.so<br/>PJRT Plugin Binary"]
    end
    
    subgraph "Source Components"
        SrcCommon["pjrt_implementation/src/common/<br/>Core PJRT Implementation"]
        SrcTT["pjrt_implementation/src/tt/<br/>TT-specific Code"]
        Integrations["integrations/<br/>vllm_plugin/"]
        PythonPkg["python_package/<br/>jax/torch/vllm wrappers"]
    end
    
    subgraph "External Dependencies"
        TTMLIR["tt-mlir<br/>ExternalProject<br/>Pinned to SHA"]
        TTMetal["tt-metal<br/>Transitive Dependency"]
        Loguru["loguru<br/>Logging Library"]
        PJRTCAPI["third_party/pjrt_c_api/<br/>Standard PJRT Headers"]
    end
    
    SrcCommon --> PJRTPlugin
    SrcTT --> PJRTPlugin
    PJRTCAPI --> PJRTPlugin
    
    TTMLIR --> PJRTPlugin
    TTMetal --> TTMLIR
    Loguru --> PJRTPlugin
    
    PJRTPlugin --> Wheel
    TTMLIR --> Wheel
    TTMetal --> Wheel
    Loguru --> Wheel
    Integrations --> Wheel
    PythonPkg --> Wheel
```


The following diagram shows the build-time dependencies between major components:

**Sources:**[CMakeLists.txt 97-106](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L97-L106)[third_party/CMakeLists.txt 29-132](https://github.com/tenstorrent/tt-xla/blob/c77995f6/third_party/CMakeLists.txt#L29-L132)[docs/src/getting_started_build_from_source.md 174-185](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L174-L185)

## System Boundaries and Interfaces

### PJRT C API Boundary

The PJRT C API ([third_party/pjrt_c_api/](https://github.com/tenstorrent/tt-xla/blob/c77995f6/third_party/pjrt_c_api/)) defines the primary interface boundary between framework-specific code and the TT-XLA plugin. This is a standardized interface that allows:

*   **Framework Independence**: Frameworks interact only with standard PJRT API, not TT-specific code
*   **Binary Compatibility**: Plugin can be loaded dynamically without recompiling frameworks
*   **Uniform Semantics**: Consistent behavior across different accelerator backends

**Key API Functions:**

*   `PJRT_Client_Create`: Initialize connection to TT devices
*   `PJRT_Compile`: Accept StableHLO and produce executable
*   `PJRT_Executable_Execute`: Run compiled program on device
*   `PJRT_Buffer_*`: Memory management operations

**Sources:**[third_party/CMakeLists.txt 31](https://github.com/tenstorrent/tt-xla/blob/c77995f6/third_party/CMakeLists.txt#L31-L31)

### Compiler Interface

The interface between PJRT plugin and TT-MLIR compiler consists of:

1.   **Input**: StableHLO MLIR module (serialized or in-memory)
2.   **Output**: Compiled executable with device kernels and metadata
3.   **Configuration**: Compilation options (optimization level, debugging, etc.)

The TT-MLIR libraries ([third_party/CMakeLists.txt 98-112](https://github.com/tenstorrent/tt-xla/blob/c77995f6/third_party/CMakeLists.txt#L98-L112)) expose APIs for:

*   `TTMLIRCompiler`: Compilation services
*   `TTMLIRRuntime`: Execution and memory management

**Sources:**[third_party/CMakeLists.txt 98-112](https://github.com/tenstorrent/tt-xla/blob/c77995f6/third_party/CMakeLists.txt#L98-L112)

### Runtime Interface

The TT-Metal runtime provides device services through its API, including:

*   Device enumeration and initialization via `tt-smi`-compatible interfaces
*   Kernel execution scheduling
*   Memory allocation and transfer
*   Multi-chip coordination (for n300, llmbox configurations)

**Sources:**[docs/src/getting_started.md 42-49](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1#L42-L49)

## Installation Structure

The final wheel package assembles all components into a cohesive installation:

```
pjrt_plugin_tt/                    # Main package
├── __init__.py
├── pjrt_plugin_tt.so              # Core PJRT plugin
├── lib/                           # Shared libraries
│   ├── libTTMLIRCompiler.so       # Compiler library
│   ├── libTTMLIRRuntime.so        # Runtime library
│   └── libloguru.so               # Logging library
└── tt-metal/                      # Runtime dependencies
    ├── kernels/                   # Device kernels
    └── runtime/                   # Metal runtime files

jax_plugin_tt/                     # JAX integration
└── __init__.py                    # Imports and configures PJRT

torch_plugin_tt/                   # PyTorch integration
└── __init__.py                    # Registers backend

vllm_tt/                           # vLLM integration (optional)
└── ...                            # Platform implementation
```

This structure allows the package to be pip-installed and used across different frameworks without conflicts.

**Sources:**[docs/src/getting_started_build_from_source.md 174-187](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L174-L187)

## Hardware Configuration

TT-XLA supports multiple Tenstorrent device configurations:

| Device | Description | Multi-Chip Support |
| --- | --- | --- |
| n150 | Single Wormhole chip | No |
| p150 | Single Wormhole chip | No |
| n300 | Dual Wormhole chips | Yes (tensor parallel) |
| llmbox | Multi-chip system | Yes (tensor/data parallel) |
| galaxy | Large-scale deployment | Yes (distributed) |

Device detection and initialization occurs through:

1.   Hardware configuration via TT-Installer
2.   Hugepages setup for efficient memory allocation
3.   Device enumeration through `tt-smi` utility

**Sources:**[docs/src/getting_started.md 25-49](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1#L25-L49)

## Configuration and Environment

The system behavior can be configured through environment variables:

| Variable | Purpose | Default |
| --- | --- | --- |
| `TTMLIR_TOOLCHAIN_DIR` | Path to TT-MLIR toolchain | (required) |
| `TTXLA_LOGGER_LEVEL` | Logging verbosity | INFO |
| `TT_METAL_RUNTIME_ROOT` | Metal runtime location | (auto-detected) |

Build-time configuration options in [CMakeLists.txt 46-63](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L46-L63):

*   `TTMLIR_ENABLE_PERF_TRACE`: Enable performance tracing
*   `TTXLA_ENABLE_EXPLORER`: Enable TT-MLIR Explorer tool
*   `TTXLA_ENABLE_PJRT_TESTS`: Build PJRT unit tests
*   `TTXLA_TRACY_ZONES`: Enable Tracy profiling zones

**Sources:**[CMakeLists.txt 46-63](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L46-L63)[docs/src/getting_started_build_from_source.md 123](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L123-L123)

## Summary

The TT-XLA system architecture is organized in clean layers with well-defined interfaces:

1.   **Framework Layer**: JAX, PyTorch, vLLM provide models
2.   **Plugin Layer**: Thin wrappers configure PJRT plugin for each framework
3.   **PJRT Layer**: Standard interface implements compilation and execution
4.   **Compiler Layer**: TT-MLIR transforms StableHLO to device code
5.   **Runtime Layer**: TT-Metal executes kernels on hardware
6.   **Hardware Layer**: Tenstorrent accelerators perform computation

The PJRT plugin serves as the central integration point, decoupling framework-specific concerns from device-specific compilation and execution. This architecture enables adding new frameworks or new Tenstorrent hardware generations without requiring changes throughout the stack.

**Sources:**[README.md 18-33](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L18-L33)[CMakeLists.txt 10-23](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L10-L23)

Dismiss
Refresh this wiki

Enter email to refresh

## Additional Diagrams


### Build Options and Configuration Variables


```mermaid
graph LR
    subgraph "Primary Build Options"
        PerfTrace["TTMLIR_ENABLE_PERF_TRACE<br/>(default: ON)"]
        PyBindings["TTMLIR_ENABLE_BINDINGS_PYTHON<br/>(default: OFF)"]
        Explorer["TTXLA_ENABLE_EXPLORER<br/>(default: OFF)"]
        PJRTTests["TTXLA_ENABLE_PJRT_TESTS<br/>(default: OFF)"]
        Tracy["TTXLA_TRACY_ZONES<br/>(default: OFF)"]
        Coverage["CODE_COVERAGE<br/>(default: OFF)"]
    end
    
    subgraph "Cache Variables"
        BuildType["TTMLIR_BUILD_TYPE<br/>(default: Release)"]
    end
    
    subgraph "Derived Settings"
        RTDebug["TT_RUNTIME_DEBUG"]
    end
    
    Explorer -->|"implies"| PyBindings
    Explorer -->|"enables"| RTDebug
    BuildType -.->|"passed to tt-mlir"| TTMLIR["tt-mlir build"]
    PerfTrace -.->|"passed to tt-mlir"| TTMLIR
    PyBindings -.->|"passed to tt-mlir"| TTMLIR
    RTDebug -.->|"passed to tt-mlir"| TTMLIR
```

**Diagram: Build Configuration Options and Dependencies**
```


#### Purpose and Mechanism


```mermaid
graph LR
    subgraph "Without Composite"
        Input1["torch.nn.functional.gelu"]
        Decomp1["Multiple StableHLO ops<br/>mul, add, tanh, etc."]
        TTIR1["Generic TTIR lowering"]
    end
    
    subgraph "With Composite"
        Input2["torch.nn.functional.gelu"]
        Composite["stablehlo.composite<br/>name='tenstorrent.gelu'"]
        TTIR2["ttir.gelu<br/>(native implementation)"]
    end
    
    Input1 --> Decomp1 --> TTIR1
    Input2 --> Composite --> TTIR2
```

**Diagram: Composite operations enable native TT-MLIR implementations**

Sources: [python_package/tt_torch/composite_ops.py:1-25]()
```


### Code Entity Map


```mermaid
graph TD
    subgraph "vllm_tt/input_batch.py"
        IB["InputBatch\
add_request(), remove_request()\
temperature_cpu_tensor\
top_k_cpu_tensor\
bad_words_token_ids\
logit_bias"]
    end

    subgraph "vllm_tt/metadata.py"
        XLASM["XLASupportedSamplingMetadata\
from_input_batch()\
_compute_token_counts()\
_compute_prompt_mask()\
_compute_bad_words_mask()"]
    end

    subgraph "vllm_tt/sampler.py"
        SAM["Sampler\
forward()\
sample()\
apply_bad_words()\
apply_logit_bias()\
apply_penalties()\
apply_temperature()\
apply_min_p()\
greedy_sample()\
random_sample()"]
        TOPKP["apply_top_k_top_p()\
(module-level function)"]
    end

    subgraph "vllm_tt/model_runner.py"
        MR["TTModelRunner\
sample_from_logits_func\
torch.compile(backend=tt)"]
    end

    IB -->|"from_input_batch()"| XLASM
    XLASM -->|"passed to"| SAM
    SAM --> TOPKP
    MR -->|"compiles"| SAM
```

Sources: [integrations/vllm_plugin/vllm_tt/input_batch.py:24-210](), [integrations/vllm_plugin/vllm_tt/metadata.py:24-328](), [integrations/vllm_plugin/vllm_tt/sampler.py:14-244](), [integrations/vllm_plugin/vllm_tt/model_runner.py:438-446]()
2b:T870b,
```


#### Exception-Based Detection


```mermaid
graph TB
    ExceptionData["ExceptionData"]
    
    subgraph "Exception Properties"
        ClassName["class_name<br/>(e.g., 'RuntimeError')"]
        Message["message<br/>(exception.__str__())"]
        ErrorLog["error_log<br/>(pytest traceback)"]
        Stdout["stdout<br/>(captured output)"]
        Stderr["stderr<br/>(captured errors)"]
    end
    
    subgraph "ExceptionCheck Matchers"
        ClassMatch["class_name match"]
        MsgCheckers["message checkers<br/>(contains, starts_with, regex, etc.)"]
        LogCheckers["error_log checkers<br/>(last_line, any, neg, etc.)"]
        OutCheckers["stdout/stderr checkers"]
        ComponentMatch["component match<br/>(ComponentChecker)"]
    end
    
    ExceptionData -->|provides| ClassName
    ExceptionData -->|provides| Message
    ExceptionData -->|provides| ErrorLog
    ExceptionData -->|provides| Stdout
    ExceptionData -->|provides| Stderr
    
    ClassName --> ClassMatch
    Message --> MsgCheckers
    ErrorLog --> LogCheckers
    Stdout --> OutCheckers
    Stderr --> OutCheckers
    ErrorLog --> ComponentMatch
    
    ClassMatch -->|all must pass| Result["ExceptionCheck.check()<br/>returns True/False"]
    MsgCheckers -->|all must pass| Result
    LogCheckers -->|all must pass| Result
    OutCheckers -->|all must pass| Result
    ComponentMatch -->|must pass| Result
```


#### Debugging a Compilation Issue


```mermaid
graph TB
    Issue["Compilation Error<br/>or Wrong Output"]
    
    Issue --> EnableExport["Set export_path + export_model_name"]
    EnableExport --> RunModel["Run model to trigger compilation"]
    RunModel --> ExamineVHLO["Examine vhlo_*.mlir<br/>Input from framework"]
    
    ExamineVHLO --> CheckSHLO{"SHLO stage<br/>looks correct?"}
    CheckSHLO -->|No| FrameworkIssue["Framework serialization issue<br/>Check JAX/PyTorch version"]
    CheckSHLO -->|Yes| CheckTTIR{"TTIR stage<br/>looks correct?"}
    
    CheckTTIR -->|No| SHLOIssue["SHLO→TTIR lowering issue<br/>Check tt-mlir pipeline"]
    CheckTTIR -->|Yes| CheckTTNN{"TTNN stage<br/>looks correct?"}
    
    CheckTTNN -->|No| TTNNIssue["TTIR→TTNN lowering issue<br/>Check optimization passes"]
    CheckTTNN -->|Yes| RuntimeIssue["Runtime execution issue<br/>Check device logs"]
    
    style Issue fill:#fee
```

**Workflow Example**:

1. **Enable IR Export**:
```python
torch_xla.set_custom_compile_options({
    "export_path": "./debug",
    "export_model_name": "problematic_model",
})
```

2. **Trigger Compilation**:
```python
output = model(input)
torch_xla.sync()
```

3. **Examine Generated IRs**:
```bash
ls debug/irs/
```

