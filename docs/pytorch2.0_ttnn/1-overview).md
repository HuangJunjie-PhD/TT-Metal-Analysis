---
project: pytorch2.0_ttnn
github: https://github.com/tenstorrent/pytorch2.0_ttnn
deepwiki: https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/1-overview)
---

# Overview

Relevant source files
*   [.github/actions/common_repo_setup/action.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_repo_setup/action.yaml)
*   [.github/actions/common_repo_setup/dependencies.json](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_repo_setup/dependencies.json)
*   [.github/workflows/update-docker-container.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-docker-container.yaml)
*   [README.md](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/README.md?plain=1)
*   [dockerfile/Dockerfile](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/dockerfile/Dockerfile)
*   [docs/AddNewOperationLowering.md](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/AddNewOperationLowering.md?plain=1)
*   [docs/README.md.in](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/README.md.in)
*   [requirements.txt](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/requirements.txt)
*   [tools/collect_metrics.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py)
*   [torch_ttnn/backend.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py)
*   [torch_ttnn/metrics.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/metrics.py)
*   [torch_ttnn/passes/analysis/multi_device_shard_analysis_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/analysis/multi_device_shard_analysis_pass.py)
*   [torch_ttnn/passes/lowering/add_data_move_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py)
*   [torch_ttnn/passes/lowering/target_wrappers.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py)
*   [torch_ttnn/passes/lowering/to_tt_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py)

## Purpose and Scope

The **pytorch2.0_ttnn** repository implements a compilation backend for PyTorch 2.0 that enables execution of PyTorch models on Tenstorrent AI accelerators. The system transforms PyTorch operations into TT-NN (Tenstorrent Neural Network) operations through a multi-pass compilation pipeline integrated with PyTorch's `torch.compile` infrastructure.

This page provides an architectural overview of the compilation system, its major components, and how they interact. For installation instructions, see [Installation and Requirements](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/1.1-installation-and-requirements). For usage examples, see [Quick Start Guide](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/1.2-quick-start-guide). For detailed compilation pipeline information, see [Architecture Overview](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/1.3-architecture-overview).

**Note**: This project is no longer actively maintained. Users are encouraged to migrate to [TT-Forge](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/TT-Forge) for continued development and support.

Sources: [README.md 1-116](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/README.md?plain=1#L1-L116)[docs/README.md.in 1-116](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/README.md.in#L1-L116)

* * *

## System Architecture

**System Architecture Overview**

The compilation system follows a standard compiler architecture with frontend integration, multiple transformation passes, and backend code generation. The `torch.compile` mechanism serves as the integration point, invoking `ttnn_backend` which wraps the core `aten_backend` function.

The pipeline consists of three major phases:

1.   **Analysis Phase**: Examines the FX graph to collect metadata about graph structure, input types, and multi-device sharding requirements
2.   **Transformation Phase**: Converts PyTorch ATen operations to TT-NN operations, with the guard system preventing compilation of known problematic patterns
3.   **Optimization Phase**: Applies performance optimizations including operation fusion, memory management, and common subexpression elimination

Sources: [torch_ttnn/backend.py 1-324](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L1-L324)[torch_ttnn/passes/lowering/to_tt_pass.py 1-50](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L1-L50)

* * *

## Key Components and Data Flow

**Compilation Data Flow**

The compilation process transforms a PyTorch FX graph through several key stages:

1.   **Operation Lowering (`ToTtPass`)**: The `ReplaceMoreTt` class walks the FX graph, replacing PyTorch ATen operations with TT-NN equivalents. Simple one-to-one mappings use dictionaries like `TTNN_POINTWISE_UNARY_OPS`, while complex conversions use `ReplaceMoreTtManually`. The `target_wrappers` module provides custom wrapper functions for operations requiring preprocessing.

2.   **Data Movement (`AddDataMovePass`)**: The `NodeInputAligner` analyzes tensor data flow and inserts necessary conversion operations. When transitioning from PyTorch to TT-NN, it inserts `ttnn.from_torch` calls. When returning to PyTorch, it inserts `ttnn.to_torch` calls. Layout conversions (`ttnn.to_layout`) ensure tensors have correct formats (ROW_MAJOR vs TILE).

3.   **Multi-Device Handling**: When `TorchTtnnOption.data_parallel` is enabled, the system inserts mesh mapping operations to distribute tensors across multiple devices. Parameters are replicated using `ReplicateTensorToMesh`, while input arguments are sharded using `ShardTensorToMesh` along dimension 0.

Sources: [torch_ttnn/backend.py 98-260](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L98-L260)[torch_ttnn/passes/lowering/to_tt_pass.py 163-371](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L163-L371)[torch_ttnn/passes/lowering/add_data_move_pass.py 278-612](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L278-L612)

* * *

## Backend Configuration and Options

**Configuration Options**

The `TorchTtnnOption` class configures compilation behavior:

| Field | Type | Purpose |
| --- | --- | --- |
| `device` | `ttnn.Device` or `ttnn.MeshDevice` | Target hardware for execution |
| `data_parallel` | `bool` | Enable data parallel execution across multiple devices |
| `gen_graphviz` | `bool` | Generate visualization of graph transformations |
| `run_mem_analysis` | `bool` | Enable memory usage analysis |
| `metrics_path` | `str` | Directory for metrics collection |
| `bypass_compile` | `bool` | Skip compilation (for debugging) |
| `use_less_ttnn_op_types` | `bool` | Use reduced operation set |
| `export_code` | `str` | Path for standalone script generation |
| `load_params_once` | `bool` | Preprocess model parameters once |

**Device Configuration Patterns**

For single-device execution:

For multi-device data parallel execution:

Sources: [torch_ttnn/backend.py 21-77](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L21-L77)[README.md 27-54](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/README.md?plain=1#L27-L54)[docs/README.md.in 27-52](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/README.md.in#L27-L52)

* * *

## Operation Lowering Strategy

**Operation Lowering Categories**

The system categorizes operation lowering into three types:

1.   **Simple (Dictionary-based)**: One-to-one mappings stored in dictionaries like `TTNN_POINTWISE_UNARY_OPS`. These operations require no special handling and map directly from ATen to TT-NN.

Example: `torch.ops.aten.sqrt.default` → `ttnn.sqrt`

2.   **Transformer-based**: Handled by `ReplaceMoreTt.call_function`, these operations require inspection of arguments or kwargs but still map to a single TT-NN operation with transformations.

Example: `torch.ops.aten.addmm.default` → `ttnn.matmul` + `ttnn.add`

3.   **Manual (Complex)**: Handled by `ReplaceMoreTtManually`, these operations require graph inspection, metadata checks, or multiple TT-NN operations to replace a single PyTorch operation.

Example: `torch.ops.aten.native_layer_norm.default` → `target_wrappers.native_layer_norm` (which internally decides between `ttnn.layer_norm` or `ttnn.operations.moreh.layer_norm`)

**Guard System Integration**

Before lowering, each operation is checked by `can_lowering_to_ttnn()` which consults blocklists in:

*   `to_tt_guard.py`: Manually curated blocklists for known problematic patterns
*   `to_tt_guard_autogen.py`: Automatically generated from failed tests

Sources: [torch_ttnn/passes/lowering/to_tt_pass.py 116-371](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L116-L371)[torch_ttnn/passes/lowering/to_tt_pass.py 440-870](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L440-L870)[torch_ttnn/passes/lowering/target_wrappers.py 1-414](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/target_wrappers.py#L1-L414)

* * *

## Data Movement and Tensor Alignment

**Tensor State Transitions**

The `AddDataMovePass` manages tensor state transitions through the `NodeInputAligner` class. Three alignment specifications handle different transition scenarios:

1.   **AlignSpecFromTorch**: Converts PyTorch tensors to TT-NN format

    *   Inserts `ttnn.from_torch(tensor, device=..., layout=..., dtype=...)`
    *   For multi-device: wraps with `ShardTensorToMesh` or `ReplicateTensorToMesh`
    *   Default configuration: `device=TtnnDevice`, `layout=TtnnTileLayout`, `dtype=TtnnBfloat16`

2.   **AlignSpecToTorch**: Converts TT-NN tensors back to PyTorch

    *   Inserts `ttnn.to_torch(tensor, dtype=...)`
    *   For multi-device: wraps with `ConcatMeshToTensor`

3.   **AlignSpecInTtnn**: Handles TT-NN to TT-NN transitions

    *   Inserts layout conversions: `ttnn.to_layout(tensor, layout)`
    *   Inserts device transfers: `ttnn.to_device(tensor)` or `ttnn.from_device(tensor)`

**Special Operation Requirements**

Certain operations require specific tensor configurations:

| Operation | Requirement | Reason |
| --- | --- | --- |
| `ttnn.embedding` | `dtype=TtnnUint32`, `layout=ROW_MAJOR_LAYOUT` | First input must be integer indices |
| `ttnn.slice` | `layout=ROW_MAJOR_LAYOUT` | Cannot unpad tilized tensors with partial tiles |
| `ttnn.split` | `layout=ROW_MAJOR_LAYOUT` | Runtime error with tilized inputs |
| `ttnn.argmax` | `dtype=TtnnBfloat16`, `layout=ROW_MAJOR_LAYOUT` | Documentation requirement |
| `target_wrappers.conv` | Weight on host with `ROW_MAJOR_LAYOUT` | Current limitation (tt-metal#15893) |

Sources: [torch_ttnn/passes/lowering/add_data_move_pass.py 278-612](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L278-L612)[torch_ttnn/passes/lowering/add_data_move_pass.py 296-385](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L296-L385)

* * *

## Testing and Metrics Infrastructure

**Testing Framework Components**

The testing infrastructure consists of:

1.   **pytest Configuration (`conftest.py`)**: Provides fixtures including:

    *   `device`: Manages TT-NN device lifecycle
    *   `reset_torch_dynamo`: Clears compilation cache between tests
    *   `compile_and_run`: Standard compilation and execution wrapper
    *   Command-line options: `--export_code`, `--metrics`, `--accuracy`

2.   **ModelTester Pattern**: Base class for standardized model testing

    *   Subclasses implement `_load_model()` and `_load_inputs()`
    *   Automatic compilation and accuracy comparison
    *   Example: `tests/models/mnist/test_mnist.py`

3.   **Metrics Collection**: Three types of pickled data:

    *   Runtime metrics: execution time, throughput
    *   Schema lists: input variations per operation
    *   Autogenerated op metrics: isolated operation test results

**Conversion Status Tracking**

The `InputVarPerOp` class tracks operation conversion status:

**Automated Guard Generation**

Failed tests trigger automatic guard generation:

1.   `collect_metrics.py` identifies failed input variations
2.   `generate_guard_function_from_input_var_metric.py` creates blocklist entries
3.   `to_tt_guard_autogen.py` prevents future compilation of problematic patterns

Sources: [tools/collect_metrics.py 1-438](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L1-L438)[torch_ttnn/metrics.py 1-206](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/metrics.py#L1-L206)

* * *

## Multi-Device Architecture

**Multi-Device Execution Strategy**

The system implements data parallel execution through tensor sharding:

1.   **Input Classification**: `InputAnalysisPass` tags placeholders as:

    *   `PARAMETER`: Model weights (replicated across devices)
    *   `BUFFER`: Model buffers like running statistics (replicated)
    *   `ARGUMENT`: Batch inputs (sharded along dimension 0)

2.   **Sharding Metadata Propagation**: `MultiDeviceShardAnalysisPass` walks the graph from sharded inputs, marking nodes with:

    *   `is_sharded=True`: Indicates tensor is distributed
    *   `shard_dim=N`: Which dimension is sharded
    *   `concat_size=M`: Original size after concatenation

3.   **Wrapper Insertion**: `MultiDevicePass` inserts placeholder operations:

    *   `target_wrappers.shard_tensor(tensor, dim, num_devices)`
    *   `target_wrappers.replicate_tensor(tensor)`
    *   `target_wrappers.concat_tensor(tensor, dim, num_devices)`

4.   **Mesh Mapper Substitution**: `AddDataMovePass` replaces wrappers with actual mesh operations:

    *   `ttnn.ShardTensorToMesh(device, dim=...)` for arguments
    *   `ttnn.ReplicateTensorToMesh(device)` for parameters/buffers
    *   `ttnn.ConcatMeshToTensor(device, dim=...)` for outputs

**Limitations**

Current implementation requires:

*   Sharded dimension size must be divisible by number of devices
*   Only dimension 0 sharding is supported
*   All arguments are sharded; selective sharding not supported

Sources: [torch_ttnn/passes/analysis/multi_device_shard_analysis_pass.py 1-66](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/analysis/multi_device_shard_analysis_pass.py#L1-L66)[torch_ttnn/passes/lowering/add_data_move_pass.py 443-486](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/add_data_move_pass.py#L443-L486)

* * *

## Dependencies and Environment

**Core Dependencies**

| Package | Version | Purpose |
| --- | --- | --- |
| `torch` | 2.7.1+cpu | PyTorch framework with CPU backend |
| `torchvision` | 0.22.1+cpu | Computer vision utilities |
| `ttnn` | 0.62.0rc36.dev247 | Tenstorrent neural network library |
| `tabulate` | 0.9.0 | Metrics table formatting |
| `networkx` | 3.1 | Graph analysis utilities |

**Installation Methods**

From repository:

Editable development install:

**Development Dependencies**

The `requirements-dev.txt` includes additional packages for:

*   Testing: `pytest`, `pytest-github-report`
*   Model libraries: `transformers`, `timm`, `accelerate`
*   Utilities: `pandas`, `matplotlib`, `graphviz`

**Container Environment**

CI/CD uses Docker containers based on:

*   Base image: `ghcr.io/tenstorrent/tt-metal/tt-metalium/ubuntu-22.04-dev-amd64`
*   System packages: `libgl1-mesa-glx`, `git-lfs`, `libsndfile1`
*   Container tags generated from SHA1 hash of dependencies

Sources: [requirements.txt 1-10](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/requirements.txt#L1-L10)[dockerfile/Dockerfile 1-15](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/dockerfile/Dockerfile#L1-L15)[.github/workflows/update-docker-container.yaml 1-132](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-docker-container.yaml#L1-L132)

* * *

## Repository Structure

```
pytorch2.0_ttnn/
├── torch_ttnn/                    # Main package
│   ├── backend.py                 # Entry point: ttnn_backend, aten_backend
│   ├── metrics.py                 # Metrics collection classes
│   ├── utils.py                   # Graph utilities, custom objects
│   ├── passes/                    # Compilation passes
│   │   ├── analysis/              # Graph analysis passes
│   │   │   ├── graph_module_analysis_pass.py
│   │   │   ├── input_analysis_pass.py
│   │   │   └── multi_device_shard_analysis_pass.py
│   │   ├── lowering/              # Operation lowering passes
│   │   │   ├── to_tt_pass.py      # Core operation conversion
│   │   │   ├── add_data_move_pass.py  # Data movement insertion
│   │   │   ├── target_wrappers.py     # Custom operation wrappers
│   │   │   ├── to_tt_guard.py         # Manual blocklists
│   │   │   └── to_tt_guard_autogen.py # Auto-generated blocklists
│   │   ├── constant_folding_pass.py
│   │   ├── fusion_pass.py
│   │   ├── deallocation_pass.py
│   │   └── multi_device_pass.py
│   └── preprocessing/             # Input preprocessing
│       ├── handle_input_aliasing.py
│       └── handle_tangents.py
├── tests/                         # Test suite
│   ├── models/                    # End-to-end model tests
│   │   ├── mnist/
│   │   ├── resnet/
│   │   └── ...
│   ├── lowering/                  # Operation-level tests
│   │   ├── pointwise/
│   │   ├── eltwise/
│   │   └── ...
│   └── utils.py                   # Test utilities (comp_pcc, assert_with_pcc)
├── tools/                         # Development tools
│   ├── collect_metrics.py         # Metrics aggregation and README generation
│   ├── generate_input_variation_test.py  # Auto-test generation
│   └── export_code.py             # Standalone script generation
├── .github/                       # CI/CD workflows
│   ├── workflows/
│   │   ├── run-tests.yaml
│   │   ├── update-docker-container.yaml
│   │   └── before_merge.yaml
│   └── actions/
│       └── common_repo_setup/
├── docs/                          # Documentation
│   ├── README.md.in               # README template
│   ├── OperationsReport.md        # Generated operations status
│   ├── AddNewOperationLowering.md # Developer guide
│   └── ProblemSolving.md          # Debugging guide
├── dockerfile/                    # Container definitions
├── operations/                    # Per-operation status pages
├── requirements.txt               # Production dependencies
├── requirements-dev.txt           # Development dependencies
└── README.md                      # Generated from README.md.in
```

**Key File Purposes**

*   **torch_ttnn/backend.py**: Defines `TorchTtnnOption`, `aten_backend`, and `ttnn_backend` - the main entry points for compilation
*   **torch_ttnn/passes/lowering/to_tt_pass.py**: Implements `ToTtPass`, `ReplaceMoreTt`, and `ReplaceMoreTtManually` - core operation lowering logic
*   **torch_ttnn/passes/lowering/add_data_move_pass.py**: Implements `AddDataMovePass` and `NodeInputAligner` - tensor format alignment
*   **tools/collect_metrics.py**: Aggregates test metrics and generates documentation (README.md, OperationsReport.md)

Sources: [torch_ttnn/backend.py 1-324](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/backend.py#L1-L324)[tools/collect_metrics.py 1-438](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L1-L438)[docs/AddNewOperationLowering.md 1-71](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/AddNewOperationLowering.md?plain=1#L1-L71)

Dismiss
Refresh this wiki

Enter email to refresh