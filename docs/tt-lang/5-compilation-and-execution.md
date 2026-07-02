---
project: tt-lang
github: https://github.com/tenstorrent/tt-lang
deepwiki: https://deepwiki.com/tenstorrent/tt-lang/5-compilation-and-execution
---

# TTNN Integration

Relevant source files
*   [.github/scripts/probe-docker-image.sh](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/scripts/probe-docker-image.sh)
*   [.github/scripts/tests/test_probe_docker_image.bats](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/scripts/tests/test_probe_docker_image.bats)
*   [.github/workflows/publish-s3-pypi.yml](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/workflows/publish-s3-pypi.yml)
*   [.gitignore](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.gitignore)
*   [README.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1)
*   [docs/sphinx/getting-started.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/getting-started.md?plain=1)
*   [include/ttlang/Dialect/TTL/Passes.td](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td)
*   [lib/Dialect/TTL/Pipelines/TTLPipelines.cpp](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Pipelines/TTLPipelines.cpp)
*   [lib/Dialect/TTL/Transforms/CMakeLists.txt](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/CMakeLists.txt)
*   [python/CMakeLists.txt](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/CMakeLists.txt)
*   [python/ttl/_src/ttl_ast.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/ttl_ast.py)
*   [python/ttl/ttl_api.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py)
*   [test/me2e/builder/pipeline.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/me2e/builder/pipeline.py)

## Purpose and Scope

This page describes how `tt-lang` kernels integrate with the **TTNN** library for tensor management and execution on Tenstorrent hardware. It provides a high-level overview of the relationship between the DSL and the runtime, covering tensor creation, memory configuration, and the execution of compiled kernels. `tt-lang` is designed as a middle ground between the high-level TTNN and low-level TT-Metalium [README.md 27-30](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L27-L30)

For deep technical details on specific integration topics, refer to the following child pages:

*   **[TTNN Interoperability Overview](https://deepwiki.com/tenstorrent/tt-lang/5.1-ttnn-interoperability-overview)**: Explains the relationship between `tt-lang` and TTNN, and when to choose each.
*   **[Tensor Creation and Memory Configuration](https://deepwiki.com/tenstorrent/tt-lang/5.2-tensor-creation-and-memory-configuration)**: Covers `ttnn.from_torch`, memory configurations (DRAM/L1), and `TILE_LAYOUT`.
*   **[Executing Compiled Kernels](https://deepwiki.com/tenstorrent/tt-lang/5.3-executing-compiled-kernels)**: Details calling kernels with TTNN tensors and extracting results via `to_torch`.

* * *

## Integration Architecture

`tt-lang` serves as an ergonomic middle ground between high-level **TTNN** operations and low-level **TT-Metalium**[README.md 27-30](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L27-L30) It allows developers to take a sequence of TTNN operations, express a fused equivalent in the Python DSL, and integrate the result as a drop-in replacement in a TTNN graph [README.md 39-40](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L39-L40)

### TTNN Execution Flow

```mermaid
graph TB
    subgraph "Host_Space_(Python/PyTorch)"
        [torch_Tensor] -- "ttnn.from_torch" --> [ttnn_Tensor]
    end
    
    subgraph "TT-Lang_Compilation_Space"
        [ttnn_Tensor] -- "TTLGenericCompiler" --> [CompiledTTNNKernel]
    end
    
    subgraph "TTNN_Runtime_Space"
        [CompiledTTNNKernel] -- "run_kernel_on_device" --> [ttnn_generic_op]
    end
    
    subgraph "Hardware_Space_(Device)"
        [ttnn_generic_op] -- "Metal_Runtime" --> [BRISC_NCRISC_TRISC]
    end
```


The following diagram maps the flow from host PyTorch tensors to device execution using `tt-lang` and TTNN entities.

**Diagram: Host-to-Device Execution Flow**

**Sources:**[python/ttl/ttl_api.py 88-91](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L88-L91)[python/ttl/_src/ttl_ast.py 128-135](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/ttl_ast.py#L128-L135)[python/ttl/dtype_utils.py 84-87](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/dtype_utils.py#L84-L87)[README.md 39-40](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L39-L40)

* * *

## Tensor Interoperability

`tt-lang` kernels operate on `ttnn.Tensor` objects. The integration relies on lazy loading of the `ttnn` module via `_ensure_ttnn()` to avoid triggering heavy dependencies during initial module load [python/ttl/ttl_api.py 20-36](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L20-L36)

### Conversion and Layout

Tensors are typically converted from PyTorch using `ttnn.from_torch`.

*   **Layout**: Kernels primarily support `ttnn.TILE_LAYOUT`. The compiler enforces that tensors must be tiled and have at least 2 dimensions [python/ttl/_src/ttl_ast.py 71-83](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/ttl_ast.py#L71-L83)
*   **DType Mapping**: The system provides utilities to map between Torch and TTNN data types [python/ttl/dtype_utils.py 86-87](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/dtype_utils.py#L86-L87)
*   **Memory Config**: Tensors can be placed in different memory spaces (L1 or DRAM), and the cache key includes `buffer_type`[python/ttl/ttl_api.py 123-130](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L123-L130)[python/ttl/_src/ttl_ast.py 73-74](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/ttl_ast.py#L73-L74)
*   **Sharding**: The system supports various sharding strategies; the cache key specifically tracks mesh shapes for multi-device compilations [python/ttl/ttl_api.py 141-150](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L141-L150)

For details on memory setups, see **[Tensor Creation and Memory Configuration](https://deepwiki.com/tenstorrent/tt-lang/5.2-tensor-creation-and-memory-configuration)**.

* * *

## Kernel Execution Model

When a kernel decorated with `@ttl.kernel` is called with `ttnn.Tensor` arguments, the `tt-lang` infrastructure manages the transition from Python DSL to hardware execution.

### Compilation and Caching

```mermaid
graph LR
    [ttl_kernel_decorator] -- "check_cache" --> [make_cache_key]
    [make_cache_key] -- "cache_miss" --> [TTLGenericCompiler]
    [TTLGenericCompiler] -- "returns" --> [CompiledTTNNKernel]
    [CompiledTTNNKernel] -- "dispatch" --> [run_kernel_on_device]
    [run_kernel_on_device] -- "post_execution" --> [run_profiling_pipeline]
```


The system uses a caching mechanism to avoid redundant compilations. The `_make_cache_key` function generates a key from tensor properties (shape, dtype, memory space, layout), mesh configuration, and runtime compute config parameters like `fp32_dest_acc_en`[python/ttl/ttl_api.py 133-158](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L133-L158)

**Diagram: Kernel Invocation and Dispatch**

**Sources:**[python/ttl/ttl_api.py 133-158](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L133-L158)[python/ttl/ttl_api.py 90-95](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L90-L95)[python/ttl/ttl_api.py 166-180](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L166-L180)

### Execution Paths

*   **Hardware**: If actual `ttnn.Tensor` objects are passed, the kernel is compiled (if not cached) and dispatched to the device using `run_kernel_on_device`[python/ttl/ttl_api.py 90-95](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L90-L95)
*   **Simulation**: If simulated tensors are used (e.g., via `tt-lang-sim`), the kernel runs via the functional simulator framework without requiring a full compilation stack [README.md 78-79](https://github.com/tenstorrent/tt-lang/blob/d76e6233/README.md?plain=1#L78-L79)

For details on the execution flow, see **[Executing Compiled Kernels](https://deepwiki.com/tenstorrent/tt-lang/5.3-executing-compiled-kernels)**.

* * *

## Profiling and Debugging

Integration with TTNN includes support for the **TT-Metal Device Profiler**. When auto-profiling is enabled, the system automatically reads device profiler data after kernel execution.

*   **Profiler Integration**: The system calls `ttnn.ReadDeviceProfiler(device)` to retrieve timing data from the hardware [python/ttl/ttl_api.py 205](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L205-L205)
*   **Source Mapping**: The `_run_profiling_pipeline` maps device cycles back to the original Python source lines using a `line_mapper`[python/ttl/ttl_api.py 228](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L228-L228)
*   **DMA Attribution**: The system loads a CB flow graph to attribute DMA cycles to specific wait/push operations [python/ttl/ttl_api.py 231-232](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L231-L232)

**Sources:**[python/ttl/ttl_api.py 166-180](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L166-L180)[python/ttl/ttl_api.py 205](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L205-L205)[python/ttl/ttl_api.py 228-232](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L228-L232)

Dismiss
Refresh this wiki

Enter email to refresh