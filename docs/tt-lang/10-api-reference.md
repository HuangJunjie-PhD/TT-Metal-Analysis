---
project: tt-lang
github: https://github.com/tenstorrent/tt-lang
deepwiki: https://deepwiki.com/tenstorrent/tt-lang/10-api-reference
---

# API Reference

Relevant source files
*   [docs/sphinx/reference/compiler-options.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/reference/compiler-options.md?plain=1)
*   [include/ttlang/Dialect/TTL/Passes.td](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td)
*   [include/ttlang/Dialect/TTL/Pipelines/TTLPipelines.h](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Pipelines/TTLPipelines.h)
*   [lib/Dialect/TTL/Pipelines/TTLPipelines.cpp](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Pipelines/TTLPipelines.cpp)
*   [lib/Dialect/TTL/Transforms/CMakeLists.txt](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/CMakeLists.txt)
*   [python/ttl/__init__.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/__init__.py)
*   [python/ttl/_src/ttl_ast.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/_src/ttl_ast.py)
*   [python/ttl/compiler_options.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/compiler_options.py)
*   [python/ttl/ttl_api.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py)
*   [test/me2e/builder/pipeline.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/me2e/builder/pipeline.py)
*   [test/python/simple_matmul_subblock.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/simple_matmul_subblock.py)
*   [test/python/test_compiler_options.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/test_compiler_options.py)

This page provides comprehensive documentation of the Python API exposed by `tt-lang`. It covers the decorators, data structures, memory operations, and execution control interfaces that developers use to write custom kernels.

The `tt-lang` language is a Python-based _domain specific language (DSL)_ designed to express low-level programs for Tenstorrent hardware, operating on chunks of tensors called _blocks_[docs/sphinx/specs/TTLangSpecification.md 52-56](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L52-L56)

* * *

## API Organization

The `tt-lang` Python API is organized into several functional modules, each providing a specific set of capabilities. It is tightly integrated with TT-NN to allow mixing built-in TT-NN operations and user-written kernels [docs/sphinx/specs/TTLangSpecification.md 54-56](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L54-L56)

### Natural Language to Code Mapping

The following diagram maps high-level programming concepts to the specific Python classes and functions implemented in the `tt-lang` codebase.

| Concept | Code Entity | File Source |
| --- | --- | --- |
| **Operation Entry** | `ttl.operation` | [python/ttl/ttl.py 30](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl.py#L30-L30) |
| **Compute Thread** | `ttl.compute` | [python/ttl/ttl.py 31](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl.py#L31-L31) |
| **Data Movement** | `ttl.datamovement` | [python/ttl/ttl.py 32](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl.py#L32-L32) |
| **Dataflow Buffer** | `DataflowBuffer` | [python/ttl/ttl_api.py 74](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L74-L74) |
| **L1 Buffer (MLIR)** | `CircularBufferType` | [lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp 54](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp#L54-L54) |
| **Data Transfer** | `ttl.copy()` | [python/ttl/ttl_api.py 96](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L96-L96) |
| **Inter-core Pipe** | `Pipe` / `PipeNet` | [python/ttl/ttl_api.py 77](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L77-L77) |

**Sources:**[python/ttl/ttl_api.py 5-96](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L5-L96)[docs/sphinx/specs/TTLangSpecification.md 1-56](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L1-L56)[python/ttl/dataflow_buffer.py 1-74](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/dataflow_buffer.py#L1-L74)

* * *

## Kernel Decorators and Context

Kernel programs are defined using specific decorators that delineate the hardware threads and the overall operation entry point.

### @ttl.operation

The `@ttl.operation` decorator marks a Python function as a TT-Lang operation. This function takes input and output TTNN tensors as arguments [docs/sphinx/specs/TTLangSpecification.md 59-62](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L59-L62) It accepts parameters for `grid`, `memory_space`, and `options`[docs/sphinx/reference/compiler-options.md 83-91](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/reference/compiler-options.md?plain=1#L83-L91)

### @ttl.compute

Annotates a thread function that executes on the Tensix compute (TRISC) processors. These threads perform arithmetic and SFPU operations [docs/sphinx/specs/TTLangSpecification.md 61-62](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L61-L62)

### @ttl.datamovement

Annotates a thread function that executes on the data movement (BRISC/NCRISC) processors. These threads manage DMA transfers between L1 and DRAM or other cores [docs/sphinx/specs/TTLangSpecification.md 61-62](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L61-L62)

For details, see [Kernel Decorators and Context](https://deepwiki.com/tenstorrent/tt-lang/10.1-kernel-decorators-and-context).

**Sources:**[docs/sphinx/specs/TTLangSpecification.md 59-63](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L59-L63)[python/ttl/ttl_api.py 98-116](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L98-L116)[docs/sphinx/reference/compiler-options.md 83-91](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/reference/compiler-options.md?plain=1#L83-L91)

* * *

## Dataflow Buffer API

A _dataflow buffer_ (DFB) is the primary abstraction for managing data in L1 memory and synchronizing between threads.

### Buffer Lifecycle and Synchronization

The programming model relies on explicit producer-consumer synchronization. The `TTLInsertCBSync` MLIR pass ensures these operations are correctly matched by inserting missing `cb_push`/`cb_pop` for unmatched `cb_reserve`/`cb_wait`[include/ttlang/Dialect/TTL/Passes.td 6-12](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td#L6-L12)

| Method | Context | Description |
| --- | --- | --- |
| `reserve()` | Producer | Reserves space in the DFB for writing [include/ttlang/Dialect/TTL/Passes.td 10](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td#L10-L10) |
| `push()` | Producer | Signals data is ready for the consumer [include/ttlang/Dialect/TTL/Passes.td 8](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td#L8-L8) |
| `wait()` | Consumer | Blocks until data is available [include/ttlang/Dialect/TTL/Passes.td 11](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td#L11-L11) |
| `pop()` | Consumer | Signals data has been consumed [include/ttlang/Dialect/TTL/Passes.td 8](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td#L8-L8) |

For details, see [Dataflow Buffer API](https://deepwiki.com/tenstorrent/tt-lang/10.2-dataflow-buffer-api).

**Sources:**[python/ttl/ttl_api.py 69-74](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L69-L74)[include/ttlang/Dialect/TTL/Passes.td 6-26](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td#L6-L26)[lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp 49-54](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp#L49-L54)

* * *

## Copy and Data Movement API

The `ttl.copy` operation handles data movement between DRAM/L1 tensors and `TensorBlock` objects.

### Copy and Synchronization

*   **`ttl.copy(src, dst)`**: Initiates an asynchronous data transfer. The `TTLInsertCopyWait` pass automatically inserts missing `ttl.wait` operations for these copies [include/ttlang/Dialect/TTL/Passes.td 108-115](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td#L108-L115)
*   **`CopyTransferHandler.wait()`**: Blocks the current thread until the associated asynchronous copy operation completes [python/ttl/ttl_api.py 96](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L96-L96)

For details, see [Copy and Data Movement API](https://deepwiki.com/tenstorrent/tt-lang/10.3-copy-and-data-movement-api).

**Sources:**[python/ttl/ttl_api.py 96](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L96-L96)[include/ttlang/Dialect/TTL/Passes.td 108-118](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td#L108-L118)

* * *

## Tile Math Operations

`tt-lang` provides a suite of math operations that operate on `TensorBlock` objects.

### Element-wise and Reductions

The `TTLConvertTTLToCompute` pass fuses elementwise operations into `ttl.compute` operations [include/ttlang/Dialect/TTL/Passes.td 202](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td#L202-L202)

| Operator | DSL Function | Hardware Mapping |
| --- | --- | --- |
| `+`, `*` | Python operators | FPU/SFPU Ops [python/pykernel/_src/kernel_ast.py 89-93](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/pykernel/_src/kernel_ast.py#L89-L93) |
| `reduce` | `ttl.math.reduce_sum` | Reduction hardware [test/python/simple_reduce_scalar.py 45-47](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/simple_reduce_scalar.py#L45-L47) |
| `abs`, `neg` | `ttl.math.abs` | SFPU Ops [docs/sphinx/specs/TTLangSpecification.md 46](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L46-L46) |
| `accumulate` | `store(..., accumulate=True)` | L1 Accumulation via `TTKernelInsertL1Accumulation`[include/ttlang/Dialect/TTL/Passes.td 143-163](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td#L143-L163) |

For details, see [Tile Math Operations](https://deepwiki.com/tenstorrent/tt-lang/10.4-tile-math-operations).

**Sources:**[include/ttlang/Dialect/TTL/Passes.td 143-182](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Passes.td#L143-L182)[docs/sphinx/specs/TTLangSpecification.md 46-49](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L46-L49)[test/python/simple_reduce_scalar.py 74-87](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/simple_reduce_scalar.py#L74-L87)

* * *

## Grid and Core Context API

The Grid API allows kernels to determine their location and the total size of the execution space.

### Grid Intrinsics

*   **`ttl.grid_size(dims)`**: Returns the dimensions of the node grid. If requested dimensions are smaller than grid dimensions, the highest rank dimension is flattened [docs/sphinx/specs/TTLangSpecification.md 104-113](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L104-L113)
*   **`ttl.node()`**: Returns the coordinates of the current Tensix Core (renamed from `ttl.core`) [docs/sphinx/specs/TTLangSpecification.md 43](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L43-L43)

### Mapping to Hardware

The grid defines a space of nodes where a node corresponds to a single Tensix Core capable of executing a program [docs/sphinx/specs/TTLangSpecification.md 99-101](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L99-L101)

For details, see [Grid and Core Context API](https://deepwiki.com/tenstorrent/tt-lang/10.5-grid-and-core-context-api).

**Sources:**[docs/sphinx/specs/TTLangSpecification.md 99-115](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L99-L115)[python/ttl/ttl_api.py 88-93](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L88-L93)

* * *

## Compiler Options

The behavior of the compilation pipeline can be tuned using `CompilerOptions`. These can be set via command-line flags, environment variables, or the `@ttl.operation` decorator [python/ttl/compiler_options.py 129-137](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/compiler_options.py#L129-L137)

| Flag | Default | Description |
| --- | --- | --- |
| `--ttl-maximize-dst` | True | Enables subblock compute and scheduling to maximize DST utilization [python/ttl/compiler_options.py 139](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/compiler_options.py#L139-L139) |
| `--ttl-fpu-binary-ops` | True | Uses FPU for binary operations like `add_tiles`[python/ttl/compiler_options.py 140](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/compiler_options.py#L140-L140) |
| `--ttl-compiler-dfbs` | True | Automatically inserts intermediate DFBs for fused computations [python/ttl/compiler_options.py 147](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/compiler_options.py#L147-L147) |
| `--ttl-subblock-sync` | False | Refines DFB synchronization to subblock granularity [python/ttl/compiler_options.py 142](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/compiler_options.py#L142-L142) |

**Sources:**[python/ttl/compiler_options.py 123-149](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/compiler_options.py#L123-L149)[docs/sphinx/reference/compiler-options.md 1-22](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/reference/compiler-options.md?plain=1#L1-L22)

* * *

## Profiling and Auto-Profiling

The API includes built-in support for performance analysis via automated instrumentation and manual signposts.

*   **`TTLANG_AUTO_PROFILE=1`**: Environment variable to enable automatic instrumentation of source lines with cycle counts derived from device profiler logs [python/ttl/ttl_api.py 182-183](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L182-L183)
*   **`ttl.signpost`**: Manual signpost insertion for custom performance markers [docs/sphinx/specs/TTLangSpecification.md 41](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L41-L41)
*   **`serve_trace`**: A utility to serve Chrome Trace Event format data for visualization [python/ttl/ttl_api.py 62](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L62-L62)

**Sources:**[python/ttl/ttl_api.py 51-63](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L51-L63)[python/ttl/ttl_api.py 166-231](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L166-L231)[lib/Dialect/TTL/Transforms/CMakeLists.txt 13](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/CMakeLists.txt#L13-L13)

Dismiss
Refresh this wiki

Enter email to refresh
