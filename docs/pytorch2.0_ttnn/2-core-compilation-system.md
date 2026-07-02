---
project: pytorch2.0_ttnn
github: https://github.com/tenstorrent/pytorch2.0_ttnn
deepwiki: https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/2-core-compilation-system
---

# Core Compilation System

Relevant source files
*   [torch_ttnn/backend.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py)
*   [torch_ttnn/passes/lowering/add_data_move_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py)
*   [torch_ttnn/passes/lowering/target_wrappers.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py)
*   [torch_ttnn/passes/lowering/to_tt_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py)

## Purpose and Scope

The Core Compilation System is the central pipeline that transforms PyTorch FX graphs into TTNN (Tenstorrent Neural Network) operations executable on Tenstorrent AI accelerators. This system implements the `torch.compile` backend, orchestrating a series of graph transformation passes that progressively lower high-level PyTorch operations into device-specific TTNN operations while inserting necessary data movement instructions.

This page covers the main compilation pipeline architecture, backend entry points, and the critical lowering passes. For detailed testing infrastructure, see [Testing Framework and Infrastructure](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4-testing-framework-and-infrastructure). For metrics collection and reporting, see [Metrics, Reporting, and Feedback Loops](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/7-metrics-reporting-and-feedback-loops). For specific model test coverage, see [Model Test Coverage](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/5-model-test-coverage) and [Operation Lowering Test Coverage](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/6-operation-lowering-test-coverage).

## Compilation Pipeline Architecture

The compilation system follows a multi-stage architecture where PyTorch models are progressively transformed through a series of analysis and transformation passes:

**Sources:**[torch_ttnn/backend.py 101-259](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L101-L259)

The pipeline accepts a PyTorch `GraphModule` from the `aot_autograd` decomposition frontend and returns a callable function that executes the lowered graph. The transformation is orchestrated by PyTorch's `PassManager` which executes passes sequentially, with each pass potentially modifying the graph structure.

## Backend Entry Points

The compilation backend provides two primary entry points that integrate with PyTorch's compilation infrastructure:

### Backend Functions

| Function | Purpose | Wrapping Layer |
| --- | --- | --- |
| `ttnn_backend` | Top-level entry point registered with `torch.compile` | Wraps `aten_backend` with `aot_autograd` |
| `aten_backend` | Core compilation logic executing the pass pipeline | Directly processes decomposed ATen graphs |

**Sources:**[torch_ttnn/backend.py 271-323](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L271-L323)[torch_ttnn/backend.py 101-259](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L101-L259)

The `ttnn_backend` function [torch_ttnn/backend.py 271-323](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L271-L323) performs preliminary analysis of input types (parameters, buffers, arguments) and wraps the `aten_backend` with `aot_autograd`, which decomposes high-level PyTorch operations into primitive ATen operations. The `aten_backend` function [torch_ttnn/backend.py 101-259](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L101-L259) is the core compilation entry point that:

1.   Removes clones inserted for input aliasing workarounds [torch_ttnn/backend.py 117](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L117-L117)
2.   Collects input variation metrics if enabled [torch_ttnn/backend.py 129-136](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L129-L136)
3.   Registers TTNN objects in the graph's global namespace [torch_ttnn/backend.py 144](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L144-L144)
4.   Constructs and executes the pass pipeline [torch_ttnn/backend.py 166-201](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L166-L201)
5.   Returns a boxed function for execution [torch_ttnn/backend.py 259](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L259-L259)

### Configuration Object

The `TorchTtnnOption` class [torch_ttnn/backend.py 22-77](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L22-L77) encapsulates all compilation configuration:

| Option | Type | Purpose |
| --- | --- | --- |
| `device` | `ttnn.Device` | Target device or mesh device for execution |
| `gen_graphviz` | `bool` | Generate GraphViz visualizations after each pass |
| `metrics_path` | `str` | Path for metrics collection output |
| `use_less_ttnn_op_types` | `bool` | Prefer generic ops over specialized variants |
| `data_parallel` | `bool` | Enable multi-device data parallelism |
| `load_params_once` | `bool` | Cache parameter conversions across iterations |
| `bypass_compile` | `bool` | Skip compilation for debugging |
| `export_code` | `str` | Path to export standalone Python code |

**Sources:**[torch_ttnn/backend.py 22-77](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L22-L77)

## Pass Pipeline Execution

The `PassManager` executes a carefully ordered sequence of passes, with each pass implementing the `PassBase` interface and returning a `PassResult` indicating whether the graph was modified:

**Sources:**[torch_ttnn/backend.py 166-180](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L166-L180)

### Pass Categories

**Analysis Passes**[torch_ttnn/backend.py 167-169](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L167-L169) attach metadata to graph nodes without transforming operations:

*   `GraphModuleAnalysisPass`: Determines execution mode (INFERENCE, TRAIN_FORWARD, TRAIN_BACKWARD)
*   `InputAnalysisPass`: Tags placeholder nodes as PARAMETER, BUFFER, or ARGUMENT
*   `MultiDeviceShardAnalysisPass`: Identifies tensor sharding requirements for data parallelism

**Core Lowering Passes**[torch_ttnn/backend.py 170-178](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L170-L178) perform the actual operation transformation:

*   `ConstantFoldingPass`: Evaluates operations with constant inputs at compile time
*   `MultiDevicePass`: Inserts shard/replicate/concat markers for multi-device execution
*   `ToTtPass`: Primary lowering pass converting ATen operations to TTNN equivalents
*   `FusionPass`: Applies pattern-based optimizations to reduce operation count
*   `AddDataMovePass`: Inserts `ttnn.from_torch`, `ttnn.to_torch`, and layout conversions

**Optimization Passes**[torch_ttnn/backend.py 175-179](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L175-L179) clean up and optimize the lowered graph:

*   `LoadOncePass`: Caches parameter conversions to avoid redundant host-device transfers
*   `EliminateCoreopsPass`: Removes unnecessary operations that don't contribute to outputs
*   `CSEPass`: Eliminates common subexpressions
*   `PermuteReshapeTuple`: Optimizes permute/reshape operations involving tuples
*   `DeallocationPass`: Inserts explicit tensor deallocation for memory management

**Sources:**[torch_ttnn/backend.py 166-180](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L166-L180)

## Operation Lowering System

The `ToTtPass` is the centerpiece of the compilation pipeline, responsible for converting PyTorch ATen operations to TTNN operations. It employs a two-stage approach:

### Transformation Architecture

**Sources:**[torch_ttnn/passes/lowering/to_tt_pass.py 163-371](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L163-L371)[torch_ttnn/passes/lowering/to_tt_pass.py 440-1523](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L440-L1523)

### Stage 1: Automatic Transformation

The `ReplaceMoreTt` transformer [torch_ttnn/passes/lowering/to_tt_pass.py 163-371](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L163-L371) extends `torch.fx.Transformer` to automatically convert operations during graph traversal. For each `call_function` node, it:

1.   Checks the guard system to verify the operation can be lowered [torch_ttnn/passes/lowering/to_tt_pass.py 217-218](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L217-L218)
2.   Attempts to map ATen operations to TTNN equivalents using dictionaries like `TTNN_POINTWISE_UNARY_OPS`[torch_ttnn/passes/lowering/to_tt_pass.py 116-160](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L116-L160)
3.   Handles special cases requiring argument reordering or additional logic [torch_ttnn/passes/lowering/to_tt_pass.py 243-371](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L243-L371)

Example operation mappings:

| ATen Operation | TTNN Operation | Notes |
| --- | --- | --- |
| `torch.ops.aten.mm.default` | `ttnn.matmul` | Direct mapping [torch_ttnn/passes/lowering/to_tt_pass.py 237-238](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L237-L238) |
| `torch.ops.aten.addmm.default` | `ttnn.matmul` + `ttnn.add` | Decomposed [torch_ttnn/passes/lowering/to_tt_pass.py 226-229](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L226-L229) |
| `torch.ops.aten._softmax.default` | `ttnn.softmax` | Added `numeric_stable=True`[torch_ttnn/passes/lowering/to_tt_pass.py 249-254](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L249-L254) |
| `torch.ops.aten.hardtanh.default` | `ttnn.hardtanh` | Converted positional to keyword args [torch_ttnn/passes/lowering/to_tt_pass.py 243-247](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L243-L247) |

**Sources:**[torch_ttnn/passes/lowering/to_tt_pass.py 163-371](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L163-L371)

### Stage 2: Manual Insertion

The `ReplaceMoreTtManually` function [torch_ttnn/passes/lowering/to_tt_pass.py 440-1523](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L440-L1523) handles operations requiring complex graph manipulation that cannot be expressed through simple transformation. It uses the `GraphWrapper` helper [torch_ttnn/passes/lowering/to_tt_pass.py 387-437](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L387-L437) to insert new nodes with proper metadata preservation.

Notable manual transformations include:

**Binary Element-wise Operations**[torch_ttnn/passes/lowering/to_tt_pass.py 473-525](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L473-L525): The `lower_binary_eltwise` helper handles broadcasting constraints, dtype casting (int32/int64 to bfloat16), and scalar operand positioning workarounds for TTNN limitations.

**Batch Normalization**[torch_ttnn/passes/lowering/to_tt_pass.py 460-471](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L460-L471): Inference-mode batch norm is decomposed into reshape, rsqrt, sub, and mul operations since TTNN lacks a dedicated batch norm primitive.

**Expand Operation**[torch_ttnn/passes/lowering/to_tt_pass.py 687-726](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L687-L726): Converts to either `ttnn.expand`, `ttnn.repeat`, or `ttnn.experimental.view` depending on the specific expansion pattern and alignment requirements.

**View/Reshape Operations**[torch_ttnn/passes/lowering/to_tt_pass.py 879-907](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L879-L907): Chooses between `ttnn.experimental.view` (preserves memory layout) and `ttnn.reshape` (may copy) based on dimension constraints and tile alignment.

**Sources:**[torch_ttnn/passes/lowering/to_tt_pass.py 440-1523](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L440-L1523)

## Data Movement and Layout Alignment

The `AddDataMovePass`[torch_ttnn/passes/lowering/add_data_move_pass.py 567-610](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L567-L610) ensures tensors have correct device placement, memory layout, and data type for each operation. This pass runs after `ToTtPass` and is critical for correctness since TTNN operations have strict requirements.

### Alignment Specifications

The pass uses dataclass specifications to describe required transformations:

**Sources:**[torch_ttnn/passes/lowering/add_data_move_pass.py 296-312](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L296-L312)

### Operation Requirements

The pass classifies TTNN operations into categories to determine alignment needs:

| Category | Operations | Default Requirements |
| --- | --- | --- |
| `TTNN_POINTWISE_UNARY_OPS` | `ttnn.relu`, `ttnn.sigmoid`, `ttnn.exp`, etc. | Device, TILE layout, BFLOAT16 |
| `TTNN_POINTWISE_BINARY_OPS` | `ttnn.add`, `ttnn.mul`, `ttnn.sub`, etc. | Device, TILE layout, BFLOAT16 |
| `TTNN_MATRIX_MULTIPLICATION_OPS` | `ttnn.matmul`, `ttnn.linear` | Device, TILE layout, BFLOAT16 |
| `TTNN_ROW_LAYOUT_OPS` | `ttnn.slice`, `target_wrappers.roll`, `ttnn.argmax` | Device, ROW_MAJOR layout |
| `TTNN_DATAMOVE_OPS` | `ttnn.concat`, `ttnn.permute`, `ttnn.reshape` | Varies by operation |

**Sources:**[torch_ttnn/passes/lowering/add_data_move_pass.py 34-178](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L34-L178)

Special cases override default requirements [torch_ttnn/passes/lowering/add_data_move_pass.py 344-384](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L344-L384):

*   `ttnn.embedding` first input requires `UINT32` dtype and `ROW_MAJOR` layout [torch_ttnn/passes/lowering/add_data_move_pass.py 346-350](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L346-L350)
*   `ttnn.slice` and `target_wrappers.roll` require `ROW_MAJOR` layout to handle partial tiles [torch_ttnn/passes/lowering/add_data_move_pass.py 351-356](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L351-L356)
*   `ttnn.argmax` requires `BFLOAT16` dtype and `ROW_MAJOR` layout per documentation [torch_ttnn/passes/lowering/add_data_move_pass.py 360-363](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L360-L363)
*   `target_wrappers.conv` weights must remain on host with `ROW_MAJOR` layout [torch_ttnn/passes/lowering/add_data_move_pass.py 372-375](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L372-L375)

### Multi-Device Handling

For multi-device execution, the pass inserts mesh mapper and composer operations [torch_ttnn/passes/lowering/add_data_move_pass.py 442-484](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L442-L484):

**Sources:**[torch_ttnn/passes/lowering/add_data_move_pass.py 442-463](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L442-L463)

The `NodeInputAligner` class [torch_ttnn/passes/lowering/add_data_move_pass.py 277-564](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L277-L564) manages the insertion of alignment operations, maintaining a cache of aligned nodes [torch_ttnn/passes/lowering/add_data_move_pass.py 281-282](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L281-L282) to avoid duplicate conversions when multiple operations share the same input.

## Guard System and Blocklists

The guard system prevents lowering of operations that are unsupported or known to produce incorrect results. The `can_lowering_to_ttnn` function is called during both `ToTtPass` stages to validate each operation before transformation.

### Guard Architecture

**Sources:**[torch_ttnn/passes/lowering/to_tt_guard.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_guard.py)[torch_ttnn/passes/lowering/to_tt_pass.py 217-218](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L217-L218)

The guard system uses multiple levels of checks:

1.   **Operation-level blocklists**: Complete operations that cannot be lowered, stored in dictionaries like `ops_unsupported` and sets per model
2.   **Schema-specific blocklists**: Specific operation signatures with particular input types/shapes that fail
3.   **Conditional guards**: Runtime checks on tensor shapes, dtypes, or other properties

For model-specific guards, test files may define custom guard functions that are registered and called during compilation. The auto-generation system (see [Automated Test Generation](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4.4-automated-test-generation)) discovers failures during model test runs and automatically adds entries to `to_tt_guard_autogen.py`.

**Sources:**[torch_ttnn/passes/lowering/to_tt_guard.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_guard.py)

## Target Wrappers and Custom Operations

The `target_wrappers` module [torch_ttnn/passes/lowering/target_wrappers.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py) provides wrapper functions that bridge gaps between PyTorch and TTNN operation semantics or implement operations not directly available in TTNN.

### Wrapper Categories

**Sources:**[torch_ttnn/passes/lowering/target_wrappers.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py)

### Key Wrapper Implementations

**`run_once` Execution Control**[torch_ttnn/passes/lowering/target_wrappers.py 42-165](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py#L42-L165): This wrapper enables parameter preprocessing by executing a sequence of operations once during the first model iteration and caching results. It's used by `LoadOncePass` to preprocess model parameters (weights, biases) into device-native formats without redundant conversions on subsequent iterations. The function:

1.   Deserializes pickled function specifications from arguments
2.   Executes each function, tracking dependencies between operations
3.   Manages memory by deallocating intermediate results
4.   Returns cached results on subsequent calls [torch_ttnn/passes/lowering/target_wrappers.py 163-165](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py#L163-L165)

**`conv` Unified Convolution**[torch_ttnn/passes/lowering/target_wrappers.py 196-273](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py#L196-L273): Provides a single interface for `ttnn.conv1d`, `ttnn.conv2d`, and `ttnn.conv_transpose2d`, dispatching based on input spatial dimensionality and the `transposed` flag. This simplifies `ToTtPass` lowering logic for all convolution variants.

**`native_layer_norm` Adaptive Implementation**[torch_ttnn/passes/lowering/target_wrappers.py 372-413](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py#L372-L413): Chooses between `ttnn.layer_norm` (faster when only normalized output needed) and `ttnn.operations.moreh.layer_norm` (required when mean/rstd outputs are used) based on which outputs are consumed by downstream operations [torch_ttnn/passes/lowering/target_wrappers.py 566-580](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py#L566-L580)

**Multi-Device Markers**[torch_ttnn/passes/lowering/target_wrappers.py 350-366](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py#L350-L366): `replicate_tensor`, `shard_tensor`, and `concat_tensor` are placeholder wrappers that propagate shape information during graph construction. They're substituted with actual mesh operations by `AddDataMovePass` when multi-device execution is detected.

**Sources:**[torch_ttnn/passes/lowering/target_wrappers.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py)

### Decorator Pattern

All wrappers use the `@target_wrapper` decorator [torch_ttnn/passes/lowering/target_wrappers.py 24-26](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py#L24-L26) which prefixes function names with `"target_wrapper_"`. This ensures unique naming in the FX graph, preventing conflicts when exporting code where both the wrapper call and the actual function name appear as strings.

**Sources:**[torch_ttnn/passes/lowering/target_wrappers.py 24-26](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py#L24-L26)

## Graph Metadata Preservation

Throughout the compilation pipeline, metadata preservation is critical for correctness and debugging. The system tracks multiple metadata attributes on each FX node:

| Metadata Key | Purpose | Example Value |
| --- | --- | --- |
| `val` | FakeTensor representing output shape/dtype | `FakeTensor(torch.Size([1, 768]), dtype=torch.float32)` |
| `original_input_variations` | ATen operation schema before lowering | `InputVariation(...)` (see [Metrics Collection](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/7.1-metrics-collection-and-schema-tracking)) |
| `stack_trace` | Source code location | Python traceback string |
| `nn_module_stack` | Module hierarchy | Dict of module names |
| `model_type` | Execution mode | `ModelType.INFERENCE`, `ModelType.TRAIN_FORWARD` |
| `primal_tag` | Input classification | `PrimalTag.PARAMETER`, `PrimalTag.BUFFER`, `PrimalTag.ARGUMENT` |

The `ReplaceMoreTt` transformer explicitly copies metadata from original nodes to transformed nodes [torch_ttnn/passes/lowering/to_tt_pass.py 194-200](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L194-L200) and the `GraphWrapper` helper used in manual transformations also preserves metadata [torch_ttnn/passes/lowering/to_tt_pass.py 394-399](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L394-L399)

**Sources:**[torch_ttnn/passes/lowering/to_tt_pass.py 192-202](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L192-L202)[torch_ttnn/passes/lowering/to_tt_pass.py 387-437](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L387-L437)

## Error Handling and Validation

The compilation pipeline includes multiple layers of validation:

1.   **Guard checks during lowering**[torch_ttnn/passes/lowering/to_tt_pass.py 217-218](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L217-L218): Operations failing guards remain as ATen ops
2.   **Grayskull architecture checks**[torch_ttnn/passes/lowering/to_tt_pass.py 52-62](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L52-L62): Certain ops (ceil, floor, round, trunc) are incompatible with Grayskull devices
3.   **Integer output detection**[torch_ttnn/passes/lowering/to_tt_pass.py 44-49](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L44-L49): Operations producing integer outputs (argmax, argmin) are skipped if used as graph outputs
4.   **Shape and dtype validation**: Many operations validate input shapes/dtypes before lowering
5.   **Exception propagation**[torch_ttnn/backend.py 197-201](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L197-L201): Compilation exceptions are logged with context and re-raised

When compilation fails, the system provides detailed error messages including the failing pass, node information, and stack traces. If `gen_graphviz=True`, intermediate graph visualizations help debug transformation issues.

**Sources:**[torch_ttnn/passes/lowering/to_tt_pass.py 44-70](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L44-L70)[torch_ttnn/backend.py 197-201](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L197-L201)

Dismiss
Refresh this wiki

Enter email to refresh