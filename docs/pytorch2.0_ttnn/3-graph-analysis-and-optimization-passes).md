---
project: pytorch2.0_ttnn
github: https://github.com/tenstorrent/pytorch2.0_ttnn
deepwiki: https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/3-graph-analysis-and-optimization-passes)
---

# Graph Analysis and Optimization Passes

Relevant source files
*   [.github/actions/common_model_tests/action.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_model_tests/action.yaml)
*   [.github/actions/common_multi_device_tests/action.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_multi_device_tests/action.yaml)
*   [docs/ProblemSolving.md](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/ProblemSolving.md?plain=1)
*   [requirements-dev.txt](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/requirements-dev.txt)
*   [tests/inputs/bert/input_data.json](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/inputs/bert/input_data.json)
*   [tests/lowering/creation/test_arange.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/creation/test_arange.py)
*   [tests/lowering/creation/test_full.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/creation/test_full.py)
*   [tests/lowering/creation/test_full_like.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/creation/test_full_like.py)
*   [tests/lowering/creation/test_masked_fill.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/creation/test_masked_fill.py)
*   [tests/lowering/creation/test_ones.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/creation/test_ones.py)
*   [tests/lowering/creation/test_to_copy.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/creation/test_to_copy.py)
*   [tests/lowering/misc/test_scaled_dot_product_attention.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/misc/test_scaled_dot_product_attention.py)
*   [tests/lowering/tensor_manipulation/test_reshape.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/tensor_manipulation/test_reshape.py)
*   [tests/lowering/tensor_manipulation/test_slice.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/tensor_manipulation/test_slice.py)
*   [tests/lowering/tensor_manipulation/test_view.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/tensor_manipulation/test_view.py)
*   [tests/models/beit/test_beit_image_classification.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/beit/test_beit_image_classification.py)
*   [tests/models/deit/test_deit.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/deit/test_deit.py)
*   [tests/models/detr/test_detr.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/detr/test_detr.py)
*   [tests/models/speecht5_tts/test_speecht5_tts.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/speecht5_tts/test_speecht5_tts.py)
*   [tests/models/torchvision/test_torchvision_image_classification.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/torchvision/test_torchvision_image_classification.py)
*   [tests/models/vilt/test_vilt.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/vilt/test_vilt.py)
*   [tests/models/xglm/test_xglm.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/xglm/test_xglm.py)
*   [tests/models/yolov5/test_yolov5.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/yolov5/test_yolov5.py)
*   [tests/multi_device/test_multi_bert.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/multi_device/test_multi_bert.py)
*   [tests/multi_device/test_multi_folded_lift_fresh_copy.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/multi_device/test_multi_folded_lift_fresh_copy.py)
*   [tests/multi_device/test_multi_mnist.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/multi_device/test_multi_mnist.py)
*   [tests/pattern/test_beit_pattern.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/pattern/test_beit_pattern.py)
*   [torch_ttnn/passes/analysis/graph_module_analysis_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/analysis/graph_module_analysis_pass.py)
*   [torch_ttnn/passes/analysis/input_analysis_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/analysis/input_analysis_pass.py)
*   [torch_ttnn/passes/constant_folding_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/constant_folding_pass.py)
*   [torch_ttnn/passes/deallocation_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/deallocation_pass.py)
*   [torch_ttnn/passes/lowering/eliminate_coreops_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/eliminate_coreops_pass.py)
*   [torch_ttnn/passes/lowering/load_once_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/load_once_pass.py)
*   [torch_ttnn/passes/lowering/to_tt_guard.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_guard.py)
*   [torch_ttnn/passes/lowering/to_tt_guard_autogen.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_guard_autogen.py)
*   [torch_ttnn/passes/multi_device_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/multi_device_pass.py)

## Purpose and Scope

This document covers the analysis and optimization passes that transform and improve the FX graph during PyTorch to TTNN compilation. These passes operate on `torch.fx.GraphModule` objects between operation lowering and final code generation. They are responsible for:

*   Analyzing graph structure and metadata
*   Classifying inputs and model types
*   Folding constant expressions at compile time
*   Managing memory and deallocating tensors
*   Enabling multi-device execution
*   Eliminating redundant operations

For information about the lowering process that converts PyTorch operations to TTNN operations, see [Operation Lowering (ToTtPass)](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/2.2-operation-lowering-(tottpass)). For information about data movement and tensor format alignment, see [Data Movement and Alignment (AddDataMovePass)](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/2.3-data-movement-and-alignment-(adddatamovepass)).

## Pass Pipeline Overview

The compilation pipeline executes passes in a specific order managed by `torch.fx.passes.infra.PassManager`. Analysis passes run first to gather metadata, followed by optimization passes that transform the graph based on this metadata.

**Sources:**[torch_ttnn/backend.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py)[torch_ttnn/passes/](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/)

## Analysis Passes

Analysis passes traverse the graph to collect metadata without modifying its structure. This metadata is stored in `node.meta` dictionaries and `gm.meta` dictionaries for later use by transformation passes.

### GraphModuleAnalysisPass

The `GraphModuleAnalysisPass` classifies the entire graph as one of three model types:

| Model Type | Description | Detection Method |
| --- | --- | --- |
| `ModelType.INFERENCE` | Forward pass for inference | Default case when neither training indicator is present |
| `ModelType.TRAIN_FORWARD` | Forward pass during training | Returns placeholder nodes unchanged in output |
| `ModelType.TRAIN_BACKWARD` | Backward pass for gradient computation | Contains function calls with "backward" in target name |

The classification is stored in `gm.meta["graph_type"]` and used by passes like `LoadOncePass` to enable optimizations only for inference.

**Sources:**[torch_ttnn/passes/analysis/graph_module_analysis_pass.py 1-68](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/analysis/graph_module_analysis_pass.py#L1-L68)

### InputAnalysisPass

The `InputAnalysisPass` classifies each placeholder node (graph input) using the `PrimalTag` enumeration:

The `PrimalTag` classification determines data distribution strategies in multi-device execution:

*   **PARAMETER** and **BUFFER**: Replicated across all devices (consistent state)
*   **ARGUMENT**: Sharded across devices (data parallelism)

**Sources:**[torch_ttnn/passes/analysis/input_analysis_pass.py 1-111](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/analysis/input_analysis_pass.py#L1-L111)

### MultiDeviceShardAnalysisPass

The `MultiDeviceShardAnalysisPass` propagates sharding metadata through the graph when multi-device execution is enabled. It marks nodes with:

*   `is_sharded`: Whether tensor is distributed across devices
*   `shard_dim`: Which dimension is sharded
*   `concat_size`: Total size before sharding

This metadata guides `MultiDevicePass` in inserting shard/replicate/concat operations.

**Sources:** Diagram 5 in system architecture

## Constant Folding Pass

The `ConstantFoldingPass` evaluates constant expressions at compile time, replacing them with `get_attr` nodes that reference pre-computed tensors. This reduces runtime overhead for operations with static inputs.

### Foldable Operations

The pass maintains a whitelist of operations eligible for folding:

### Folding Process

### Integration with Guard System

The constant folding pass checks `can_lowering_to_ttnn(node)` before folding to ensure operations blocked by the guard system are not folded. This prevents folding operations that would fail during lowering.

**Sources:**[torch_ttnn/passes/constant_folding_pass.py 1-138](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/constant_folding_pass.py#L1-L138)

## Multi-Device Transformation

The `MultiDevicePass` transforms single-device graphs for execution across multiple Tenstorrent devices in a mesh configuration. It only activates when `device.get_num_devices() >= 2`.

### Tensor Distribution Strategy

### Graph Rewriting for Sharding

For operations like `view`, `expand`, and `_unsafe_view`, the pass rewrites constant shape arguments to account for sharding:

After transformation, the pass runs `FakeTensorProp` and `ShapeProp` to propagate updated tensor shapes through the graph.

**Sources:**[torch_ttnn/passes/multi_device_pass.py 1-123](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/multi_device_pass.py#L1-L123)

## Memory Management Passes

### LoadOncePass

The `LoadOncePass` moves iteration-invariant operations (those with constant inputs like parameters) into a `run_once` wrapper function that caches results between model runs. This is only enabled for end-to-end inference models.

#### Invariant Detection

#### Convolution Preprocessing

For `conv` operations, the pass preprocesses weights and biases:

**Sources:**[torch_ttnn/passes/lowering/load_once_pass.py 1-210](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/load_once_pass.py#L1-L210)

### EliminateCoreopsPass

The `EliminateCoreopsPass` removes redundant data movement operation pairs that cancel each other out:

| Pattern | Elimination |
| --- | --- |
| `from_device` → `to_device` | Remove both, connect predecessor to successor |
| `to_device` → `from_device` | Remove both, connect predecessor to successor |
| `from_torch` → `to_torch` | Remove both, connect predecessor to successor |
| `to_torch` → `from_torch` | Remove both, connect predecessor to successor |

After eliminating pairs, the pass runs dead code elimination to remove unreachable nodes.

**Sources:**[torch_ttnn/passes/lowering/eliminate_coreops_pass.py 1-47](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/eliminate_coreops_pass.py#L1-L47)

### DeallocationPass

The `DeallocationPass` inserts explicit `deallocate()` calls to free TTNN tensor memory when tensors are no longer needed. This is critical due to limited L1 memory on Tenstorrent devices.

#### Last Use Tracking

The pass uses `TrackUnusedValues` to identify the last use of each node:

#### Deallocation Insertion

The pass skips deallocation for:

*   Reference nodes (`pack_to_tuple`, `getitem`)
*   Cached nodes (`node.meta["is_cached"] == True`)

**Sources:**[torch_ttnn/passes/deallocation_pass.py 1-113](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/deallocation_pass.py#L1-L113)

## Other Optimization Passes

While not implemented in the current codebase, the system architecture mentions additional passes:

| Pass Name | Purpose |
| --- | --- |
| `FusionPass` | Combines multiple operations into single fused kernels |
| `CSEPass` | Common subexpression elimination to deduplicate computations |

These would follow similar patterns of traversing the graph and applying transformations.

## Pass Execution Order

The passes execute in a carefully orchestrated sequence:

The order ensures that:

1.   Metadata is available before transformations
2.   Constants are folded before lowering
3.   Multi-device ops are inserted after shape propagation
4.   Memory is managed after all transformations

**Sources:**[torch_ttnn/backend.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#LNaN-LNaN) Diagram 2 in system architecture

## Pass Base Class

All passes inherit from `torch.fx.passes.infra.pass_base.PassBase` and implement:

The `PassManager` uses the `modified` flag to determine whether to run subsequent passes that depend on changes.

**Sources:**[torch_ttnn/passes/*/pass*.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/*/pass*.py)

Dismiss
Refresh this wiki

Enter email to refresh