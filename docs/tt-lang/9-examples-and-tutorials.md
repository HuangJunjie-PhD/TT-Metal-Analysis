---
project: tt-lang
github: https://github.com/tenstorrent/tt-lang
deepwiki: https://deepwiki.com/tenstorrent/tt-lang/9-examples-and-tutorials
---

# Examples and Tutorials

Relevant source files
*   [.editorconfig](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.editorconfig)
*   [.github/ISSUE_TEMPLATE/config.yml](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/ISSUE_TEMPLATE/config.yml)
*   [.github/ISSUE_TEMPLATE/feature_request.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/ISSUE_TEMPLATE/feature_request.md?plain=1)
*   [CHANGELOG.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CHANGELOG.md?plain=1)
*   [CITATION.cff](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CITATION.cff)
*   [LICENSE](https://github.com/tenstorrent/tt-lang/blob/d76e6233/LICENSE)
*   [LICENSE_understanding.txt](https://github.com/tenstorrent/tt-lang/blob/d76e6233/LICENSE_understanding.txt)
*   [NOTICE](https://github.com/tenstorrent/tt-lang/blob/d76e6233/NOTICE)
*   [docs/sphinx/specs/TTLangSpecification.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1)
*   [examples/README.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/README.md?plain=1)
*   [examples/elementwise-tutorial/step_0_ttnn_base.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_0_ttnn_base.py)
*   [examples/elementwise-tutorial/step_1_single_node_single_tile_block.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_1_single_node_single_tile_block.py)
*   [examples/elementwise-tutorial/step_2_single_node_multitile_block.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_2_single_node_multitile_block.py)
*   [examples/elementwise-tutorial/step_3_multinode.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py)
*   [include/ttlang/Dialect/TTL/Transforms/DFBMaterialization.h](https://github.com/tenstorrent/tt-lang/blob/d76e6233/include/ttlang/Dialect/TTL/Transforms/DFBMaterialization.h)
*   [lib/Dialect/TTL/Transforms/DFBMaterialization.cpp](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/DFBMaterialization.cpp)
*   [lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp](https://github.com/tenstorrent/tt-lang/blob/d76e6233/lib/Dialect/TTL/Transforms/TTLInsertIntermediateDFBs.cpp)
*   [python/pykernel/_src/kernel_ast.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/pykernel/_src/kernel_ast.py)
*   [test/python/invalid/invalid_reduce_scalar_undefined.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/invalid/invalid_reduce_scalar_undefined.py)
*   [test/python/simple_reduce_scalar.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/simple_reduce_scalar.py)

This page provides practical examples demonstrating common programming patterns in tt-lang. The examples progress from simple single-core operations to advanced multi-core kernels with broadcasting and inter-core communication using pipes. All examples execute on Tenstorrent hardware via TTNN integration or via the functional simulator.

For details on basic operations, see [Simple Element-wise Operations](https://deepwiki.com/tenstorrent/tt-lang/9.1-simple-element-wise-operations). For advanced communication, see [Multi-core Communication with Pipes](https://deepwiki.com/tenstorrent/tt-lang/9.4-multi-core-communication-with-pipes).

## Tutorial Progression

The `examples/` directory contains a curated progression of examples that build on each other, including comprehensive multi-step tutorials for element-wise and matrix multiplication operations:

| Example / Tutorial | Demonstrates | Key Concepts |
| --- | --- | --- |
| `eltwise_add.py` | Multi-core element-wise add | `with` statement pattern, 2D grid work distribution |
| [Simple Element-wise Operations](https://deepwiki.com/tenstorrent/tt-lang/9.1-simple-element-wise-operations) | Fused `(a * b + c) * d` | Step-by-step from TTNN baseline to multi-node multi-tile blocks |
| [Multi-tile Processing](https://deepwiki.com/tenstorrent/tt-lang/9.2-multi-tile-processing) | Fused `relu(a @ b + c)` | K-reduction, double-buffered accumulators, multi-device sharding |
| `eltwise_pipe.py` | Inter-core multicast | `ttl.Pipe`, `ttl.PipeNet`, `if_src`/`if_dst` |
| `broadcast_demo.py` | Scalar broadcasting | `ttl.block.broadcast` with `ttl.math.reduce_sum` |
| [Matmul Benchmarks and Advanced Examples](https://deepwiki.com/tenstorrent/tt-lang/9.7-matmul-benchmarks-and-advanced-examples) | High-performance matmul | SUMMA, K-split, multicast patterns, and performance sweeps |

**Sources:**[examples/README.md 30-82](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/README.md?plain=1#L30-L82)[examples/elementwise-tutorial/step_3_multinode.py 6-18](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py#L6-L18)[CHANGELOG.md 33](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CHANGELOG.md?plain=1#L33-L33)

## Running Examples

Examples execute on hardware with TTNN tensors. Most examples include a `main()` function that handles device initialization and tensor setup. Alternatively, they can be run on the simulator using `tt-lang-sim` without hardware access.

`def main() -> None:    device = ttnn.open_device(device_id=0) # <FileRef file-url="https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py#L175-L175" min=175  file-path="examples/elementwise-tutorial/step_3_multinode.py">Hii</FileRef>    # ... tensor setup ...    y = tutorial_operation(a, b, c) # Call the @ttl.operation <FileRef file-url="https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py#L192-L192" min=192  file-path="examples/elementwise-tutorial/step_3_multinode.py">Hii</FileRef>    # ... validation ...    ttnn.close_device(device) # <FileRef file-url="https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py#L201-L201" min=201  file-path="examples/elementwise-tutorial/step_3_multinode.py">Hii</FileRef>`
To run via the simulator:

`tt-lang-sim examples/eltwise_add.py # <FileRef file-url="https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/README.md?plain=1#L16-L16" min=16  file-path="examples/README.md">Hii</FileRef>`
**Sources:**[examples/elementwise-tutorial/step_3_multinode.py 175-201](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py#L175-L201)[examples/README.md 5-17](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/README.md?plain=1#L5-L17)

## Anatomy of a tt-lang Operation

**Diagram: Operation Structure with Code Entity Mapping**

This diagram shows the standard structure of a tt-lang operation as seen in the element-wise tutorial. The `@ttl.operation` decorator defines the grid layout. `ttl.make_dataflow_buffer_like()` creates buffers for inter-thread communication. The `with` statement automatically manages DFB lifecycle (`reserve`/`push` and `wait`/`pop`).

**Sources:**[examples/elementwise-tutorial/step_3_multinode.py 44-165](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py#L44-L165)[docs/sphinx/specs/TTLangSpecification.md 60-97](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L60-L97)

## Multi-Core Work Distribution Pattern

**Diagram: Multi-Core Work Distribution with ttl.node()**

**Code Pattern from `step_3_multinode.py`:**

`@ttl.operation(grid=(4, 4))def __tutorial_operation(a, b, c, y):    grid_cols, grid_rows = ttl.grid_size(dims=2) # <FileRef file-url="https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py#L54-L54" min=54  file-path="examples/elementwise-tutorial/step_3_multinode.py">Hii</FileRef>        @ttl.datamovement()    def tutorial_read():        node_col, node_row = ttl.node(dims=2) # <FileRef file-url="https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py#L96-L96" min=96  file-path="examples/elementwise-tutorial/step_3_multinode.py">Hii</FileRef>        for local_row in range(rows_per_node):            row = node_row * rows_per_node + local_row # <FileRef file-url="https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py#L102-L102" min=102  file-path="examples/elementwise-tutorial/step_3_multinode.py">Hii</FileRef>            # ... calculate tensor slice indices ...`
**Sources:**[examples/elementwise-tutorial/step_3_multinode.py 44-110](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py#L44-L110)[docs/sphinx/specs/TTLangSpecification.md 100-115](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L100-L115)

## Inter-Core Communication with Pipes

The `eltwise_pipe.py` example demonstrates how to use `ttl.Pipe` and `ttl.PipeNet` to multicast data from a source core to multiple destination cores.

**Diagram: Pipe Multicast Pattern**

**Key Code Entities:**

*   `ttl.Pipe((0, 0), (0, slice(1, 4)))`: Defines a pipe from node (0,0) to nodes (0,1), (0,2), and (0,3).
*   `pipe_net.is_src()`: Predicate used to identify if the current node is the source in the `PipeNet`. [CHANGELOG.md 18](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CHANGELOG.md?plain=1#L18-L18)
*   `pipe_net.is_dst()`: Predicate used to identify if the current node is a destination. [CHANGELOG.md 18](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CHANGELOG.md?plain=1#L18-L18)
*   `ttl.copy(block, pipe)`: Moves data from a local buffer into the pipe for transmission.

**Sources:**[docs/sphinx/specs/TTLangSpecification.md 12-13](https://github.com/tenstorrent/tt-lang/blob/d76e6233/docs/sphinx/specs/TTLangSpecification.md?plain=1#L12-L13)[CHANGELOG.md 18-24](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CHANGELOG.md?plain=1#L18-L24)[examples/README.md 35-36](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/README.md?plain=1#L35-L36)

## Summary of Child Pages

### [Simple Element-wise Operations](https://deepwiki.com/tenstorrent/tt-lang/9.1-simple-element-wise-operations)

Covers the basics of writing kernels for operations like `add` or `multiply`. Focuses on the `with`-statement pattern for managing `DataflowBuffer` lifecycle. For details, see [Simple Element-wise Operations](https://deepwiki.com/tenstorrent/tt-lang/9.1-simple-element-wise-operations).

### [Multi-tile Processing](https://deepwiki.com/tenstorrent/tt-lang/9.2-multi-tile-processing)

Demonstrates how to process tile blocks larger than 1x1 (e.g., `GRANULARITY = 4` in `step_3_multinode.py`) to amortize DMA and control flow overhead. For details, see [Multi-tile Processing](https://deepwiki.com/tenstorrent/tt-lang/9.2-multi-tile-processing).

### [Broadcasting Patterns](https://deepwiki.com/tenstorrent/tt-lang/9.3-broadcasting-patterns)

Explains how to use `ttl.block.broadcast` to expand tensors across dimensions (rows, columns, or scalars) within a compute kernel. Includes examples of reducing and then re-broadcasting. For details, see [Broadcasting Patterns](https://deepwiki.com/tenstorrent/tt-lang/9.3-broadcasting-patterns).

### [Multi-core Communication with Pipes](https://deepwiki.com/tenstorrent/tt-lang/9.4-multi-core-communication-with-pipes)

Deep dive into `Pipe` and `PipeNet` for inter-core data movement. Covers unicast, multicast, and the `is_src`/`is_dst` predicate model. For details, see [Multi-core Communication with Pipes](https://deepwiki.com/tenstorrent/tt-lang/9.4-multi-core-communication-with-pipes).

### [Large Tensor Streaming](https://deepwiki.com/tenstorrent/tt-lang/9.5-large-tensor-streaming)

Patterns for processing tensors that are much larger than the available L1 memory by streaming tiles through small, double-buffered circular buffers (`block_count=2`). For details, see [Large Tensor Streaming](https://deepwiki.com/tenstorrent/tt-lang/9.5-large-tensor-streaming).

### [Fused Operations and Control Flow](https://deepwiki.com/tenstorrent/tt-lang/9.6-fused-operations-and-control-flow)

Shows how to chain multiple operations (e.g., `a_blk * b_blk + c_blk`) within a single compute kernel to minimize memory round-trips and utilize the SFPU/FPU efficiently. For details, see [Fused Operations and Control Flow](https://deepwiki.com/tenstorrent/tt-lang/9.6-fused-operations-and-control-flow).

### [Matmul Benchmarks and Advanced Examples](https://deepwiki.com/tenstorrent/tt-lang/9.7-matmul-benchmarks-and-advanced-examples)

Explains the matmul benchmark suite, including K-split and SUMMA algorithms. Covers performance sweep methodology and multicast patterns for weight/activation reuse. For details, see [Matmul Benchmarks and Advanced Examples](https://deepwiki.com/tenstorrent/tt-lang/9.7-matmul-benchmarks-and-advanced-examples).

**Sources:**[examples/elementwise-tutorial/step_3_multinode.py 37-73](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/elementwise-tutorial/step_3_multinode.py#L37-L73)[examples/README.md 30-82](https://github.com/tenstorrent/tt-lang/blob/d76e6233/examples/README.md?plain=1#L30-L82)[CHANGELOG.md 33](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CHANGELOG.md?plain=1#L33-L33)[test/python/simple_reduce_scalar.py 25-47](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/python/simple_reduce_scalar.py#L25-L47)

Dismiss
Refresh this wiki

Enter email to refresh
