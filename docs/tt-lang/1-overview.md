---
project: tt-lang
github: https://github.com/tenstorrent/tt-lang
deepwiki: https://deepwiki.com/tenstorrent/tt-lang/1-overview
---

# Overview

Relevant source files
*   [.editorconfig](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.editorconfig)
*   [.github/ISSUE_TEMPLATE/config.yml](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/ISSUE_TEMPLATE/config.yml)
*   [.github/ISSUE_TEMPLATE/feature_request.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/ISSUE_TEMPLATE/feature_request.md?plain=1)
*   [.github/scripts/probe-docker-image.sh](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/scripts/probe-docker-image.sh)
*   [.github/scripts/tests/test_probe_docker_image.bats](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/scripts/tests/test_probe_docker_image.bats)
*   [.github/workflows/publish-s3-pypi.yml](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/workflows/publish-s3-pypi.yml)
*   [.gitignore](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.gitignore)
*   [CHANGELOG.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CHANGELOG.md?plain=1)
*   [CITATION.cff](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CITATION.cff)
*   [LICENSE](https://github.com/tenstorrent/tt-lang/blob/d76e6233/LICENSE)
*   [LICENSE_understanding.txt](https://github.com/tenstorrent/tt-lang/blob/d76e6233/LICENSE_understanding.txt)
*   [NOTICE](https://github.com/tenstorrent/tt-lang/blob/d76e6233/NOTICE)
*   [README.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1)
*   [docs/sphinx/getting-started.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/getting-started.md?plain=1)
*   [docs/sphinx/specs/TTLangSpecification.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1)
*   [examples/README.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/README.md?plain=1)
*   [examples/elementwise-tutorial/step_0_ttnn_base.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_0_ttnn_base.py)
*   [examples/elementwise-tutorial/step_1_single_node_single_tile_block.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_1_single_node_single_tile_block.py)
*   [examples/elementwise-tutorial/step_2_single_node_multitile_block.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_2_single_node_multitile_block.py)
*   [examples/elementwise-tutorial/step_3_multinode.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py)
*   [include/ttlang/Dialect/TTL/Transforms/DFBMaterialization.h](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Transforms/DFBMaterialization.h)
*   [lib/Dialect/TTL/Transforms/DFBMaterialization.cpp](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/DFBMaterialization.cpp)
*   [lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp)
*   [python/CMakeLists.txt](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/CMakeLists.txt)
*   [python/pykernel/_src/kernel_ast.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/pykernel/_src/kernel_ast.py)
*   [test/python/invalid/invalid_reduce_scalar_undefined.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/invalid/invalid_reduce_scalar_undefined.py)
*   [test/python/simple_reduce_scalar.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/simple_reduce_scalar.py)

tt-lang is a Python-based Domain-Specific Language (DSL) designed for authoring high-performance custom kernels on Tenstorrent hardware [README.md 20-21](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L20-L21) It provides an expressive yet ergonomic middle ground between high-level **TT-NN** (straightforward operations but limited expressivity) and low-level **TT-Metalium** (full hardware control but high management overhead) [README.md 27-29](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L27-L29)

The primary use case is **kernel fusion** for model deployment. Engineers porting models through TT-NN can take sequences of operations, express them as fused tt-lang kernels with explicit control over intermediate results and memory layout, validate correctness through simulation, and integrate the result as a drop-in replacement in their TT-NN graph [README.md 39-40](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L39-L40)

This page provides a high-level system overview. For detailed information, see: [What is tt-lang](https://deepwiki.com/tenstorrent/tt-lang/1.1-what-is-tt-lang), [Installation and Setup](https://deepwiki.com/tenstorrent/tt-lang/1.2-installation-and-setup), and [Quick Start Guide](https://deepwiki.com/tenstorrent/tt-lang/1.3-quick-start-guide).

## Purpose and Position in the Ecosystem

```mermaid
graph TB
    subgraph "Natural Language Space"
        ["High-Level Ops"]
        ["Custom Fused Kernels"]
        ["Hardware Primitives"]
    end

    subgraph "Code Entity Space"
        ["TT-NN"]
        ["tt-lang"]
        ["TT-Metalium"]
    end
    
    ["High-Level Ops"] --- ["TT-NN"]
    ["Custom Fused Kernels"] --- ["tt-lang"]
    ["Hardware Primitives"] --- ["TT-Metalium"]

    ["TT-NN"] -->|"Fusion / Custom Patterns"| ["tt-lang"]
    ["tt-lang"] -->|"Lowering / CodeGen"| ["TT-Metalium"]
    ["tt-lang"] -->|"Functional Validation"| ["ttlang-sim"]
```


tt-lang joins the Tenstorrent software ecosystem to provide a unified entrypoint with integrated simulation, performance analysis, and AI-assisted development [README.md 29-30](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L29-L30)

*   **TT-NN Layer**: Provides high-level operations that are straightforward to use but lack the expressivity needed for custom kernels [README.md 37](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L37-L37)
*   **TT-Lang Layer**: Expressive DSL where the compiler handles resource management (DST register allocation, NOC addressing, subblocking) while maintaining high expressivity for application-level concerns [README.md 37-39](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L37-L39)
*   **TT-Metalium Layer**: Provides direct access to hardware primitives without abstraction overhead for developers who need maximum control [README.md 37](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L37-L37)

**Diagram: Software Stack Positioning**

**Sources:**[README.md 27-40](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L27-L40)[docs/sphinx/specs/TTLangSpecification.md 53-58](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L53-L58)

## System Architecture

```mermaid
graph TB
    subgraph "User Interface Layer"
        ["Python_DSL"]
        ["ttl.operation"]
        ["ttl.compute"]
        ["ttl.datamovement"]
    end
    
    subgraph "Compilation Infrastructure"
        ["TTL_Dialect"]
        ["TTKernel_Dialect"]
        ["MLIR_Pass_Pipeline"]
        ["EmitC_Lowering"]
    end
    
    subgraph "Execution Backends"
        ["Hardware_Tensix_Cores"]
        ["ttlang-sim_Simulator"]
    end
    
    ["Python_DSL"] -->|"AST Parsing"| ["TTL_Dialect"]
    ["TTL_Dialect"] -->|"Transforms"| ["MLIR_Pass_Pipeline"]
    ["MLIR_Pass_Pipeline"] -->|"Lowering"| ["TTKernel_Dialect"]
    ["TTKernel_Dialect"] -->|"CodeGen"| ["EmitC_Lowering"]
    
    ["EmitC_Lowering"] -->|"Binary Execution"| ["Hardware_Tensix_Cores"]
    ["Python_DSL"] -->|"Direct Execution"| ["ttlang-sim_Simulator"]
```

The compilation infrastructure transforms Python AST to initial MLIR via `TTCompilerBase` [python/pykernel/_src/kernel_ast.py:61-62](), applies optimization passes such as `TTLInsertIntermediateDFBsPass` [lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp:39-41](), and generates C++ code via `EmitC` [python/CMakeLists.txt:11-12](). Execution backends support both functional simulation for rapid development and hardware compilation for deployment [README.md:76-78]().
```


tt-lang consists of a Python-based frontend, an MLIR-based compilation pipeline, and dual execution backends (Hardware and Simulator).

**Diagram: System Layers and Components**

The compilation infrastructure transforms Python AST to initial MLIR via `TTCompilerBase`[python/pykernel/_src/kernel_ast.py 61-62](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/pykernel/_src/kernel_ast.py#L61-L62) applies optimization passes such as `TTLInsertIntermediateDFBsPass`[lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp 39-41](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp#L39-L41) and generates C++ code via `EmitC`[python/CMakeLists.txt 11-12](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/CMakeLists.txt#L11-L12) Execution backends support both functional simulation for rapid development and hardware compilation for deployment [README.md 76-78](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L76-L78)

**Sources:**[README.md 27-40](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L27-L40)[python/pykernel/_src/kernel_ast.py 61-157](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/pykernel/_src/kernel_ast.py#L61-L157)[lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp 32-114](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp#L32-L114)

## Key Components and Code Entities

```mermaid
graph TD
    subgraph "Operation: @ttl.operation"
        subgraph "Thread 1: @ttl.compute"
            ["Math_Ops_FPU_SFPU"]
        end
        subgraph "Thread 2: @ttl.datamovement"
            ["NCRISC_NOC_Read"]
        end
        subgraph "Thread 3: @ttl.datamovement"
            ["BRISC_NOC_Write"]
        end
    end
    
    ["NCRISC_NOC_Read"] -->|"Sync via DataflowBuffer"| ["Math_Ops_FPU_SFPU"]
    ["Math_Ops_FPU_SFPU"] -->|"Sync via DataflowBuffer"| ["BRISC_NOC_Write"]
```


The tt-lang API centers on decorators and specialized functions that define the kernel structure and data movement:

| API Entity | Location | Purpose |
| --- | --- | --- |
| `@ttl.operation()` | [docs/sphinx/specs/TTLangSpecification.md 67](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L67-L67) | Defines the operation entry point taking TT-NN tensors. |
| `@ttl.compute()` | [docs/sphinx/specs/TTLangSpecification.md 74](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L74-L74) | Marks a thread function for Tensix FPU/SFPU math operations. |
| `@ttl.datamovement()` | [docs/sphinx/specs/TTLangSpecification.md 78](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L78-L78) | Marks thread functions for data movement (BRISC/NCRISC). |
| `ttl.grid_size()` | [docs/sphinx/specs/TTLangSpecification.md 107](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L107-L107) | Returns the size of the execution grid for multi-core work. |
| `ttl.node()` | [docs/sphinx/specs/TTLangSpecification.md 144](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L144-L144) | Returns the coordinate of the current core within the grid. |
| `tt-lang-sim` | [README.md 78](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L78-L78) | CLI tool for running kernels in the functional simulator. |

**Diagram: Kernel Structure and Threads**

**Sources:**[docs/sphinx/specs/TTLangSpecification.md 60-84](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L60-L84)[examples/elementwise-tutorial/step_3_multinode.py 44-165](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py#L44-L165)[README.md 76-78](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L76-L78)

## Compilation Pipeline

The compiler transforms Python code through multiple MLIR dialects before generating the final C++ source for the hardware.

1.   **Initial IR**: Python AST is visited by `TTCompilerBase` to generate the `ttl` dialect [python/pykernel/_src/kernel_ast.py 61-127](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/pykernel/_src/kernel_ast.py#L61-L127)
2.   **Frontend Passes**: Handles transformations like inserting intermediate Dataflow Buffers (DFBs) [lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp 9-13](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp#L9-L13)
3.   **Dialect Management**: Uses `TTL` and `TTKernel` dialects for hardware mapping [python/CMakeLists.txt 40-110](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/CMakeLists.txt#L40-L110)
4.   **Backend Lowering**: Generates hardware-ready C++ code using `EmitC`[python/CMakeLists.txt 11-12](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/CMakeLists.txt#L11-L12)

**Sources:**[python/pykernel/_src/kernel_ast.py 61-127](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/pykernel/_src/kernel_ast.py#L61-L127)[lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp 9-114](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp#L9-L114)[python/CMakeLists.txt 1-135](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/CMakeLists.txt#L1-L135)

## Execution Modes

tt-lang supports two primary execution paths:

### Functional Simulator

The fastest way to iterate. It runs kernels as pure Python without requiring Tenstorrent hardware or the full compiler stack [README.md 35-36](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L35-L36)

*   **Command**: `tt-lang-sim <kernel>.py`[README.md 78](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L78-L78)
*   **Scheduling**: Uses greenlets to enable deterministic scheduling [CHANGELOG.md 95](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CHANGELOG.md?plain=1#L95-L95)
*   **Setup**: Can be built separately with `-DTTLANG_SIM_ONLY=ON`[docs/sphinx/getting-started.md 140](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/getting-started.md?plain=1#L140-L140)

### Hardware Execution

Compiles the kernel into C++ binaries that run on Tensix cores.

*   **Setup**: Requires the full toolchain and `ttnn`[docs/sphinx/getting-started.md 9-12](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/getting-started.md?plain=1#L9-L12)
*   **Integration**: Works with `ttnn.Tensor` and requires hardware device access [docs/sphinx/getting-started.md 29-34](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/getting-started.md?plain=1#L29-L34)

**Sources:**[README.md 43-64](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L43-L64)[docs/sphinx/getting-started.md 128-130](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/getting-started.md?plain=1#L128-L130)[CHANGELOG.md 93-106](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CHANGELOG.md?plain=1#L93-L106)

## Getting Started

To begin using tt-lang:

1.   **Installation**: Install via PyPI (`pip install tt-lang`) or use the provided Docker images [README.md 56-92](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L56-L92)
2.   **First Kernel**: Run the elementwise tutorial examples to understand the programming model [examples/elementwise-tutorial/step_1_single_node_single_tile_block.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_1_single_node_single_tile_block.py)
3.   **Simulation**: Use `tt-lang-sim` to validate logic and debug before moving to hardware [README.md 78](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L78-L78)
4.   **Testing**: Verify your setup by running the test suite [examples/README.md 83-93](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/README.md?plain=1#L83-L93)

**Sources:**[README.md 41-82](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L41-L82)[docs/sphinx/getting-started.md 7-42](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/getting-started.md?plain=1#L7-L42)[examples/README.md 1-28](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/README.md?plain=1#L1-L28)

Dismiss
Refresh this wiki

Enter email to refresh