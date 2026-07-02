---
project: pytorch2.0_ttnn
github: https://github.com/tenstorrent/pytorch2.0_ttnn
deepwiki: https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/8-development-tools-and-utilities)
---

# Development Tools and Utilities

Relevant source files
*   [.github/actions/common_model_tests/action.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_model_tests/action.yaml)
*   [.github/actions/common_multi_device_tests/action.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_multi_device_tests/action.yaml)
*   [docs/ProblemSolving.md](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/ProblemSolving.md?plain=1)
*   [tests/inputs/bert/input_data.json](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/inputs/bert/input_data.json)
*   [tests/lowering/eltwise/unary/test_aten_log_softmax.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/eltwise/unary/test_aten_log_softmax.py)
*   [tests/lowering/embedding/test_embedding.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/embedding/test_embedding.py)
*   [tests/models/squeeze_bert/test_squeeze_bert.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/squeeze_bert/test_squeeze_bert.py)
*   [tests/multi_device/test_multi_bert.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/multi_device/test_multi_bert.py)
*   [tests/multi_device/test_multi_mnist.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/multi_device/test_multi_mnist.py)
*   [tools/export_code.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py)
*   [torch_ttnn/passes/deallocation_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/deallocation_pass.py)
*   [torch_ttnn/preprocessing/handle_input_aliasing.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/preprocessing/handle_input_aliasing.py)
*   [torch_ttnn/preprocessing/handle_tangents.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/preprocessing/handle_tangents.py)
*   [torch_ttnn/utils.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py)

This document covers developer-facing utilities and debugging tools that support the pytorch2.0_ttnn compilation pipeline. These tools assist with graph manipulation, code generation for debugging, preprocessing operations, and troubleshooting compilation and accuracy issues. For information about the core compilation pipeline, see [Core Compilation System](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/2-core-compilation-system). For testing infrastructure, see [Testing Framework and Infrastructure](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4-testing-framework-and-infrastructure).

* * *

## Overview of Development Utilities

The development toolkit consists of four main components:

| Component | Primary Files | Purpose |
| --- | --- | --- |
| **Code Export System** | `tools/export_code.py` | Generate standalone scripts for accuracy debugging and profiling |
| **Graph Utilities** | `torch_ttnn/utils.py` | FX graph manipulation, shape extraction, custom object registration |
| **Preprocessing Utilities** | `torch_ttnn/preprocessing/handle_input_aliasing.py` `torch_ttnn/preprocessing/handle_tangents.py` | Handle input aliasing and mark tangent outputs for gradient flow |
| **Debugging Guidance** | `docs/ProblemSolving.md` | Structured debugging methodology and common error patterns |

Sources: [torch_ttnn/utils.py 1-263](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py#L1-L263)[tools/export_code.py 1-932](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L1-L932)[torch_ttnn/preprocessing/handle_input_aliasing.py 1-126](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/preprocessing/handle_input_aliasing.py#L1-L126)[torch_ttnn/preprocessing/handle_tangents.py 1-62](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/preprocessing/handle_tangents.py#L1-L62)[docs/ProblemSolving.md 1-203](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/ProblemSolving.md?plain=1#L1-L203)

* * *

## Code Export and Standalone Script Generation

### Purpose and Architecture

The code export system generates self-contained Python scripts from compiled models for offline debugging. When invoked with `--export_code=accuracy` or `--export_code=profiling`, the system captures both the computation graph and all input data (parameters, buffers, dynamic inputs), then generates executable scripts that can run independently.

**Export Code Flow Diagram**

Sources: [tools/export_code.py 889-932](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L889-L932)[tools/export_code.py 519-716](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L519-L716)[tools/export_code.py 718-803](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L718-L803)

### Key Functions and Entry Points

#### `export_code(torch_ttnn_option)`

Main entry point invoked by the compilation backend. The function checks if `torch_ttnn_option.export_code` is set to a valid option (`"accuracy"` or `"profiling"`), then orchestrates code generation.

**Key attributes from TorchTtnnOption:**

*   `metrics_path`: Used as the model name for output files
*   `_aten_fx_graphs`: List of lists of unmodified ATen FX graphs
*   `_ttnn_fx_graphs`: List of lists of TTNN-lowered FX graphs
*   `_all_inputs`: List of lists of input tensors (includes parameters, buffers, and dynamic inputs)

Sources: [tools/export_code.py 889-932](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L889-L932)

#### `generate_flat_args(gm, example_inputs)`

Combines parameters, buffers, and dynamic inputs into a single flattened list. This function is called during lowering when the GraphModule has been lowered to ATen operations. The logic mirrors PyTorch's `aot_module_simplified` to ensure all inputs are captured.

Sources: [tools/export_code.py 493-516](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L493-L516)

### Accuracy Mode: Operation-by-Operation Comparison

When `--export_code=accuracy` is specified, the generated script performs operation-by-operation accuracy comparisons between ATen and TTNN implementations.

**Accuracy Export Structure Diagram**

Sources: [tools/export_code.py 362-469](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L362-L469)[tools/export_code.py 612-631](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L612-L631)

**Operation Mapping Process:**

The system maps ATen operations to TTNN operations using metadata:

1.   `_compute_key(node)`: Creates a unique key from `seq_nr`, `original_aten` name, and tensor metadata
2.   `_map_aten_to_ttnn_ops()`: Builds a dictionary mapping each ATen node to a list of TTNN nodes
3.   `_process_ttnn_ops()`: Inserts accuracy check tuples after the final TTNN node for each ATen operation

Sources: [tools/export_code.py 200-215](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L200-L215)[tools/export_code.py 217-249](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L217-L249)[tools/export_code.py 252-279](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L252-L279)

### Profiling Mode: Tracy-Compatible Output

When `--export_code=profiling` is specified, the generated script includes Tracy profiling instrumentation for performance analysis.

**Key differences in profiling mode:**

*   Wraps execution in `Profiler()` context
*   Inserts `signpost()` markers at iteration boundaries
*   Calls `ttnn.DumpDeviceProfiler(device)` every 500 operations and before return
*   Omits ATen operations and accuracy checks
*   Runs multiple iterations (configurable via `total_num_iterations`)

Sources: [tools/export_code.py 642-654](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L642-L654)[tools/export_code.py 451-453](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L451-L453)[tools/export_code.py 799](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L799-L799)

### Handling Graph Breakages and Tangents

Models with graph breakages (multiple forward functions) require special handling:

**Tangent and Output Renaming:**

1.   `_collect_tangents()`: Identifies outputs marked as tangents (gradients used in backward pass)
2.   `_rename_outputs()`: Renames outputs with chunk and graph indices to avoid name collisions
3.   `_rename_arguments_and_tangents()`: Matches tangent inputs to their source outputs across graph boundaries

**Clone Node Handling:**

For input aliasing (see [Input Aliasing and Tangent Handling](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/Input%20Aliasing%20and%20Tangent%20Handling)), clone nodes are matched with primal outputs:

*   `_get_first_clone_node()`: Finds the first clone placeholder in argument list
*   `_collect_matching_primals_from_clones()`: Maps clone nodes to their corresponding primal outputs

Sources: [tools/export_code.py 812-850](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L812-L850)[tools/export_code.py 164-198](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L164-L198)[tools/export_code.py 853-886](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/export_code.py#L853-L886)

### Usage Examples

**Generate accuracy debugging script:**

**Generate profiling script:**

The accuracy script will report the first operation pair with PCC < 0.90, making it easy to isolate accuracy issues.

Sources: [docs/ProblemSolving.md 179-203](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/ProblemSolving.md?plain=1#L179-L203)[.github/actions/common_model_tests/action.yaml 26](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_model_tests/action.yaml#L26-L26)[.github/actions/common_multi_device_tests/action.yaml 20](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_multi_device_tests/action.yaml#L20-L20)

* * *

## Graph Utilities and FX Helpers

The `torch_ttnn/utils.py` module provides utilities for manipulating FX graphs, extracting metadata, and registering custom objects.

### Core Graph Manipulation

#### `GraphCleanup(gm)`

Standard cleanup sequence for FX GraphModules after modifications:

1.   Eliminates dead code nodes
2.   Validates graph structure (lint)
3.   Recompiles the GraphModule

This function is called after most graph transformation passes to ensure the graph remains in a valid state.

Sources: [torch_ttnn/utils.py 8-13](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py#L8-L13)

### Shape and Metadata Extraction

**Shape Extraction Utilities Diagram**

Sources: [torch_ttnn/utils.py 16-47](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py#L16-L47)

#### `get_shape(gm, node_or_shape)`

Unified interface for extracting tensor shapes from various node types:

*   Handles `torch.fx.Node`, `torch.fx.Proxy`, `torch.Size`, lists, tuples
*   For `Node` types, checks `node.meta["val"]` first
*   For `get_attr` nodes, retrieves the actual attribute from the GraphModule
*   Returns `torch.Size()` for scalar values (int, float)

Sources: [torch_ttnn/utils.py 16-47](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py#L16-L47)

#### `get_dtype(node)`, `get_opname(node)`, `get_arg(node, index, name, default)`

**Helper functions for node introspection:**

| Function | Purpose | Key Logic |
| --- | --- | --- |
| `get_dtype(node)` | Extract tensor dtype | Checks `node.meta["val"].dtype` |
| `get_opname(node)` | Get operation name | Handles `aten.*` targets, `__name__` attribute, or string target |
| `get_arg(node, index, name, default)` | Retrieve argument by position or name | Checks `node.args[index]` then `node.kwargs[name]` |

Sources: [torch_ttnn/utils.py 58-74](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py#L58-L74)

### Node Classification and Filtering

#### `is_operation(node)`, `users_have_getitem(node)`

**Utility functions for node classification:**

*   `is_operation(node)`: Returns True if node is neither placeholder nor output
*   `users_have_getitem(node)`: Returns the first user node that is a getitem operation, or None

These functions are used extensively in graph traversal and transformation passes.

Sources: [torch_ttnn/utils.py 76-84](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py#L76-L84)

#### `get_output_nodes(graph)`, `get_input_nodes(graph)`

**Graph boundary accessors:**

*   `get_output_nodes(graph)`: Returns the list of output node arguments (the actual outputs, not the output node itself)
*   `get_input_nodes(graph)`: Returns list of all placeholder nodes

Sources: [torch_ttnn/utils.py 114-124](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py#L114-L124)

### Custom Object Registration System

The custom object registration system allows TTNN-specific objects (devices, layouts, dtypes, memory configs) to be serialized in FX graphs using unique string representations.

**Custom Object Registration Flow**

Sources: [torch_ttnn/utils.py 137-175](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py#L137-L175)[torch_ttnn/utils.py 177-262](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py#L177-L262)

#### `get_add_custom_object_in_graph(key, obj)`

Registers a custom object with a unique string key:

1.   Creates a `WrapperObj` class with `__repr__()` returning the key
2.   Registers the key as a custom builtin in `torch.fx.graph`
3.   Stores both wrapper and original object in `__custom_objects_registry`
4.   Returns the wrapper object for use in graph construction

**Example usage in code:** When the compilation system needs to pass TTNN-specific objects (like `ttnn.TILE_LAYOUT` or device references) through the FX graph, it uses this registration system to ensure they serialize correctly.

Sources: [torch_ttnn/utils.py 182-210](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py#L182-L210)

#### `get_emplace_custom_object_in_graph(object_type, *args, **kwargs)`

Higher-level interface that instantiates an object and registers it:

1.   Builds a unique key from `object_type.__name__` and sanitized arguments
2.   Instantiates the object: `object_type(*args, **kwargs)`
3.   Calls `get_add_custom_object_in_graph()` with the key and instance

**Example from documentation:**

Sources: [torch_ttnn/utils.py 213-262](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py#L213-L262)

### Shape Validation

#### `HasValidPageSize(shape, strict=False)`

Validates tensor shapes for TTNN page size requirements:

*   TTNN requires the innermost dimension (`shape[-1]`) to be divisible by 32, or less than 32 if `strict=False`
*   This prevents `valid_page_size` runtime errors from TTNN

**Validation logic:**

*   Returns True if `shape[-1] % 32 == 0`
*   If `strict=False`, also returns True if `shape[-1] < 32`
*   Used in operation guards and data movement passes

Sources: [torch_ttnn/utils.py 127-133](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/utils.py#L127-L133)

* * *

## Input Aliasing and Tangent Handling

These preprocessing utilities handle special cases in AOT Autograd compilation that can cause issues when transferring data between host and device.

### Input Aliasing Problem and Solution

**Problem Context:**

AOT Autograd optimizes memory by detecting when an output tensor shares storage with an input tensor (aliasing). However, this optimization breaks when tensors move between host and device memory, as TTNN operations create new device allocations.

**Issue reference:**[PyTorch Issue #108079](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/PyTorch%20Issue%20#108079)

**Input Aliasing Workaround Flow**

Sources: [torch_ttnn/preprocessing/handle_input_aliasing.py 8-27](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/preprocessing/handle_input_aliasing.py#L8-L27)[torch_ttnn/preprocessing/handle_input_aliasing.py 31-98](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/preprocessing/handle_input_aliasing.py#L31-L98)

### `insert_clones_for_input_aliasing(gm)`

Inserts clone operations after input placeholder nodes before AOT Autograd processing:

**Algorithm:**

1.   Identify placeholder nodes that represent tensor inputs
2.   Skip nodes that are mutated in-place (e.g., used as first argument to `copy_` or `slice_scatter`)
3.   Insert `torch.ops.aten.clone.default(node)` after all placeholder nodes
4.   Replace all uses of the placeholder with the clone node

**Important note:** The function does NOT call `eliminate_dead_code()` at this stage because the graph has not been functionalized yet. Premature dead code elimination can remove necessary mutation operations.

**Example scenario from comments:**

If `eliminate_dead_code()` runs before functionalization, it would incorrectly remove the `masked_fill_` line since `masked_fill` appears unused.

Sources: [torch_ttnn/preprocessing/handle_input_aliasing.py 31-98](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/preprocessing/handle_input_aliasing.py#L31-L98)

### `remove_clones_for_input_aliasing(gm)`

Removes the clone operations after AOT Autograd has determined aliasing metadata:

**Pattern matched:**

```
placeholder -> clone.default -> operation
```

**Algorithm:**

1.   Find placeholder nodes with exactly one user
2.   Check if that user is `torch.ops.aten.clone.default`
3.   Replace all uses of the clone with the original placeholder
4.   Erase the clone node
5.   Call `GraphCleanup()` to finalize

Sources: [torch_ttnn/preprocessing/handle_input_aliasing.py 101-126](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/preprocessing/handle_input_aliasing.py#L101-L126)

### Tangent Marking for Gradient Flow

#### `mark_output_as_tangents(graph)`

Marks output tensors that represent gradients (tangents) for training graphs. This metadata is used by multi-device passes to handle gradient distribution correctly.

**Tangent Identification Process:**

1.   Find the output node (always the last node in the graph)
2.   Extract output arguments
3.   Find the first output starting with "primals" (activations before this are tangents)
4.   Query PyTorch's tracing context for `fw_metadata.output_info`
5.   Build `output_grad_mask` based on: 
    *   `OutputType` (non_alias, unsafe_view_alias, custom_function_view)
    *   Tensor type (not SymInt)
    *   `requires_grad` flag

6.   Mark tangent nodes with `node.meta["tangents"] = mask_value`

**Tangent Mask Logic:**

Sources: [torch_ttnn/preprocessing/handle_tangents.py 9-62](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/preprocessing/handle_tangents.py#L9-L62)

**Note:** Multi-device tangent handling for training is currently limited. See [Multi-Device Support and Sharding](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/3.4-multi-device-support-and-sharding) for details.

* * *

## Debugging and Problem Solving

The `docs/ProblemSolving.md` document provides structured debugging methodology for common compilation and runtime errors.

### Common Error Patterns and Solutions

#### Incompatible Function Arguments

**Error signature:**

```
TypeError: __call__(): incompatible function arguments. The following argument types are supported:
```

**Debugging Process Diagram**

**Example from documentation:**

Error shows `ttnn.clone` called with positional arguments:

Python binding in tt-metal shows `py::kw_only()` marker, requiring keyword arguments for `memory_config` and `dtype`. Solution: modify lowering to use kwargs.

Sources: [docs/ProblemSolving.md 15-91](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/ProblemSolving.md?plain=1#L15-L91)

#### Backend Compiler Failure

**Error signature:**

```
torch._dynamo.exc.BackendCompilerFailed: backend='ttnn_backend' raised:
Exception: An error occurred when running the 'ToTtPass' pass
```

**Debugging steps:**

1.   **Run with trace flag:**

 
2.   **Set breakpoint at failure location:**

 
3.   **Run until breakpoint and enter interactive mode:**

 

This allows inspection of the exception traceback to identify the root cause.

Sources: [docs/ProblemSolving.md 92-145](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/ProblemSolving.md?plain=1#L92-L145)

#### Torch vs TTNN Tensor Type Mismatch

**Error signature:**

```
TypeError: conv2d(): incompatible function arguments. The following argument types are supported:
    1. (..., input_tensor: tt_lib.tensor.Tensor, ...)
Invoked with: input_tensor=tensor(..., dtype=torch.bfloat16)
```

**Root cause:** A `torch.Tensor` is passed where `ttnn.Tensor` is expected.

**Solution:** The `AddDataMovePass` should insert `ttnn.from_torch()` for this argument but failed to do so. Review the data movement logic in the pass.

Sources: [docs/ProblemSolving.md 146-176](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/ProblemSolving.md?plain=1#L146-L176)

### Accuracy Debugging Workflow

**Accuracy Debugging with Export Code**

**Example output from documentation:**

```
Traceback (most recent call last):
  File "MobileNetSSD_code.py", line 5650, in <module>
    forward(*inputs)
  File "MobileNetSSD_code.py", line 1347, in forward
    test_accuracy(mean, ttnn_mean)
  File "MobileNetSSD_code.py", line 225, in test_accuracy
    assert_with_pcc(expected, actual, pcc = 0.90)
AssertionError: 0.7265316050734197
```

This pinpoints line 1347 where `ttnn.mean` has accuracy issues (PCC 0.72).

Sources: [docs/ProblemSolving.md 179-203](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/ProblemSolving.md?plain=1#L179-L203)

### Key Debugging Commands

| Command | Purpose | Example |
| --- | --- | --- |
| `--trace` | Enable pdb debugger in pytest | `pytest test.py --trace` |
| `--export_code=accuracy` | Generate accuracy debugging script | `pytest test.py --export_code=accuracy` |
| `--export_code=profiling` | Generate Tracy profiling script | `pytest test.py --export_code=profiling` |
| `-s` | Disable output capture (see prints) | `pytest test.py -s` |
| `--gen_graphviz` | Generate FX graph visualizations | Set `option.gen_graphviz = True` |

Sources: [docs/ProblemSolving.md 1-203](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/ProblemSolving.md?plain=1#L1-L203)[.github/actions/common_model_tests/action.yaml 26](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_model_tests/action.yaml#L26-L26)

### Recommended Resources

**Code search across repositories:**

*   TT-Metal TTNN operations: `github.com/tenstorrent/tt-metal/tree/main/ttnn/cpp/ttnn/operations`
*   TT-Metal test examples: `github.com/tenstorrent/tt-metal/` (search for operation usage)
*   pytorch2.0_ttnn repository: `github.com/tenstorrent/pytorch2.0_ttnn`

Sources: [docs/ProblemSolving.md 4-13](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/ProblemSolving.md?plain=1#L4-L13)

Dismiss
Refresh this wiki

Enter email to refresh