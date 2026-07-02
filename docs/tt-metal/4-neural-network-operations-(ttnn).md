---
project: tt-metal
github: https://github.com/tenstorrent/tt-metal
deepwiki: https://deepwiki.com/tenstorrent/tt-metal/4-neural-network-operations-(ttnn)
---

# Neural Network Operations (TTNN)

Relevant source files
*   [cmake/helper_functions.cmake](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/cmake/helper_functions.cmake)
*   [docs/source/tt-metalium/index.rst](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/docs/source/tt-metalium/index.rst)
*   [docs/source/tt-metalium/tt_metal/environment_variables/index.rst](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/docs/source/tt-metalium/tt_metal/environment_variables/index.rst)
*   [docs/source/tt-metalium/tt_metal/labs/index.rst](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/docs/source/tt-metalium/tt_metal/labs/index.rst)
*   [docs/source/tt-metalium/tt_metal/labs/matmul/lab1/lab1.rst](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/docs/source/tt-metalium/tt_metal/labs/matmul/lab1/lab1.rst)
*   [docs/source/tt-metalium/tt_metal/labs/matmul/lab2/lab2.rst](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/docs/source/tt-metalium/tt_metal/labs/matmul/lab2/lab2.rst)
*   [docs/source/tt-metalium/tt_metal/labs/matmul/lab3/lab3.rst](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/docs/source/tt-metalium/tt_metal/labs/matmul/lab3/lab3.rst)
*   [tests/tt_eager/python_api_testing/unit_testing/misc/test_num_cores_to_corerangeset_in_subcoregrids.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_eager/python_api_testing/unit_testing/misc/test_num_cores_to_corerangeset_in_subcoregrids.py)
*   [tests/tt_metal/distributed/test_dispatch_context.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/distributed/test_dispatch_context.cpp)
*   [tests/ttnn/benchmark/cpp/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/benchmark/cpp/CMakeLists.txt)
*   [tests/ttnn/lab_examples/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/lab_examples/CMakeLists.txt)
*   [tests/ttnn/lab_examples/sources.cmake](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/lab_examples/sources.cmake)
*   [tests/ttnn/tracy/cpp/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/tracy/cpp/CMakeLists.txt)
*   [tests/ttnn/unit_tests/base_functionality/test_device.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/unit_tests/base_functionality/test_device.py)
*   [tests/ttnn/unit_tests/base_functionality/test_grid_to_cores.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/unit_tests/base_functionality/test_grid_to_cores.py)
*   [tests/ttnn/unit_tests/gtests/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/unit_tests/gtests/CMakeLists.txt)
*   [tests/ttnn/unit_tests/gtests/multiprocess/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/unit_tests/gtests/multiprocess/CMakeLists.txt)
*   [tests/ttnn/unit_tests/gtests/test_graph_capture_arguments_morehdot.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/unit_tests/gtests/test_graph_capture_arguments_morehdot.cpp)
*   [tests/ttnn/unit_tests/gtests/test_graph_capture_arguments_transpose.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/unit_tests/gtests/test_graph_capture_arguments_transpose.cpp)
*   [tests/ttnn/unit_tests/gtests/ttnn_test_fixtures.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/unit_tests/gtests/ttnn_test_fixtures.hpp)
*   [tests/ttnn/unit_tests/gtests/udm/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/unit_tests/gtests/udm/CMakeLists.txt)
*   [tests/ttnn/unit_tests/operations/experimental/test_multi_scale_deformable_attn.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/unit_tests/operations/experimental/test_multi_scale_deformable_attn.py)
*   [tests/ttnn/unit_tests/tensor/test_tensor_utilities.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/unit_tests/tensor/test_tensor_utilities.py)
*   [tt_metal/api/tt-metalium/experimental/dispatch_context.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/api/tt-metalium/experimental/dispatch_context.hpp)
*   [tt_metal/api/tt-metalium/work_split.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/api/tt-metalium/work_split.hpp)
*   [tt_metal/common/work_split.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/common/work_split.cpp)
*   [tt_metal/distributed/dispatch_context.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/distributed/dispatch_context.cpp)
*   [tt_metal/distributed/mesh_device_impl.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/distributed/mesh_device_impl.hpp)
*   [tt_metal/llrt/hal/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/hal/CMakeLists.txt)
*   [tt_metal/llrt/hal/codegen/codegen.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/hal/codegen/codegen.sh)
*   [tt_metal/tools/lightmetal_runner/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/tools/lightmetal_runner/CMakeLists.txt)
*   [tt_metal/tools/watcher_dump/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/tools/watcher_dump/CMakeLists.txt)
*   [ttnn/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/CMakeLists.txt)
*   [ttnn/api/ttnn/device.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/api/ttnn/device.hpp)
*   [ttnn/core/device.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/core/device.cpp)
*   [ttnn/cpp/ttnn-nanobind/device.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn-nanobind/device.cpp)
*   [ttnn/cpp/ttnn-nanobind/tensor.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn-nanobind/tensor.cpp)
*   [ttnn/cpp/ttnn/operations/data_movement/moe_expert_token_remap/device/kernels/dataflow/writer_moe_expert_token_remap.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/data_movement/moe_expert_token_remap/device/kernels/dataflow/writer_moe_expert_token_remap.cpp)
*   [ttnn/cpp/ttnn/operations/data_movement/moe_expert_token_remap/device/moe_expert_token_remap_device_operation.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/data_movement/moe_expert_token_remap/device/moe_expert_token_remap_device_operation.cpp)
*   [ttnn/cpp/ttnn/operations/data_movement/moe_expert_token_remap/device/moe_expert_token_remap_device_operation.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/data_movement/moe_expert_token_remap/device/moe_expert_token_remap_device_operation.hpp)
*   [ttnn/cpp/ttnn/operations/data_movement/moe_expert_token_remap/device/moe_expert_token_remap_program_factory.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/data_movement/moe_expert_token_remap/device/moe_expert_token_remap_program_factory.cpp)
*   [ttnn/cpp/ttnn/operations/data_movement/moe_expert_token_remap/moe_expert_token_remap.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/data_movement/moe_expert_token_remap/moe_expert_token_remap.cpp)
*   [ttnn/cpp/ttnn/operations/data_movement/moe_expert_token_remap/moe_expert_token_remap.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/data_movement/moe_expert_token_remap/moe_expert_token_remap.hpp)
*   [ttnn/cpp/ttnn/operations/data_movement/moe_routing_remap/device/kernels/dataflow/writer_moe_routing_remap.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/data_movement/moe_routing_remap/device/kernels/dataflow/writer_moe_routing_remap.cpp)
*   [ttnn/cpp/ttnn/operations/experimental/experimental_nanobind.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/experimental_nanobind.cpp)
*   [ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/CMakeLists.txt)
*   [ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/kernels/compute/msda_compute.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/kernels/compute/msda_compute.cpp)
*   [ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/kernels/dataflow/reader_msda.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/kernels/dataflow/reader_msda.cpp)
*   [ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/kernels/dataflow/writer_msda.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/kernels/dataflow/writer_msda.cpp)
*   [ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/kernels/msda_tile_layout.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/kernels/msda_tile_layout.hpp)
*   [ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/multi_scale_deformable_attn_device_operation.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/multi_scale_deformable_attn_device_operation.cpp)
*   [ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/multi_scale_deformable_attn_device_operation.hpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/multi_scale_deformable_attn_device_operation.hpp)
*   [ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/multi_scale_deformable_attn_program_factory.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/multi_scale_deformable_attn/device/multi_scale_deformable_attn_program_factory.cpp)
*   [ttnn/examples/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/examples/CMakeLists.txt)
*   [ttnn/examples/add/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/examples/add/CMakeLists.txt)
*   [ttnn/examples/lab_eltwise_binary/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/examples/lab_eltwise_binary/CMakeLists.txt)
*   [ttnn/examples/lab_eltwise_binary/kernels/compute/tiles_add.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/examples/lab_eltwise_binary/kernels/compute/tiles_add.cpp)
*   [ttnn/examples/lab_eltwise_binary/kernels/dataflow/read_tiles.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/examples/lab_eltwise_binary/kernels/dataflow/read_tiles.cpp)
*   [ttnn/sources.cmake](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/sources.cmake)
*   [ttnn/test/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/test/CMakeLists.txt)
*   [ttnn/ttnn/__init__.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py)
*   [ttnn/ttnn/core.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/core.py)
*   [ttnn/ttnn/device.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/device.py)
*   [ttnn/ttnn/operations/conv2d.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/operations/conv2d.py)
*   [ttnn/ttnn/types.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py)

## Purpose and Scope

This page provides a high-level overview of the TTNN (Tenstorrent Neural Network) library, the neural network operation layer built on top of the TT-Metalium runtime. TTNN offers a comprehensive set of tensor operations and neural network primitives designed to efficiently utilize Tenstorrent hardware platforms.

As a parent page, it outlines the key components, operation categories, and design patterns of TTNN. Deep technical details, API references, and implementation specifics are deferred to dedicated child pages:

 — Explain the Python API surface, operation registration, and configuration systems.
 — Document the C++ operation registration system, template dispatch, and device operation interface.
 — Explain TTNN tensor abstraction, memory configuration, layout management, and sharding strategies.
 — Document backward operations for gradient computation, golden functions, and autograd integration.
 — Explain conv2d/conv_transpose2d implementation, sharding strategies, and performance optimizations.
 — Document matmul operations, linear layers, and GEMM optimization strategies.
 — Explain unary/binary/ternary operations, program factories, and SFPU integration.
 — Document scaled dot-product attention, paged attention, Flash Attention, and multi-latent attention implementations.
) — Explain all-gather, reduce-scatter, all-reduce, and other CCL primitives for multi-device execution.
 — Document program configuration, optimization modes (performance/accuracy), and precision control.

* * *

## Architecture Overview

TTNN forms the high-level neural network operation layer in the Tenstorrent software stack. User-facing Python code invokes TTNN operations through the `ttnn` package, which internally calls into a nanobind C++ extension (`ttnn._ttnn`) backed by TTNN core libraries. These libraries manage tensor abstractions, operation execution via the `device_operation` framework, and invoke the TT-Metalium low-level runtime for device dispatch.

### System Stack Mapping

The following diagram bridges the natural language concept of the software stack to the specific code entities and files that implement them.

Title: TTNN Software Stack Mapping

Sources: [ttnn/ttnn/__init__.py 17-21](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py#L17-L21)[ttnn/ttnn/types.py 49-50](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py#L49-L50)[ttnn/sources.cmake 28](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/sources.cmake#L28-L28)

* * *

## TTNN Library Components

The TTNN system is structured into several core modules that handle tensor state, device execution, and multi-device coordination.

| Component | Description |
| --- | --- |
| **ttnn::Tensor** | The central tensor abstraction. Manages attributes, storage, and layouts. [ttnn/ttnn/types.py 49-50](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py#L49-L50) |
| **ttnn::MeshDevice** | Handles multi-device orchestration and command queue management. [ttnn/ttnn/device.py 14](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/device.py#L14-L14) |
| **ttnn_core** | C++ library containing core logic for async runtime, cluster management, and tensor ops. [ttnn/CMakeLists.txt 92-93](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/CMakeLists.txt#L92-L93) |
| **ttnncpp** | Shared library providing the C++ backend for operations and device management. [ttnn/CMakeLists.txt 182-184](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/CMakeLists.txt#L182-L184) |
| **FlatBuffer Schemas** | Used for tensor serialization and specification. [ttnn/CMakeLists.txt 101-107](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/CMakeLists.txt#L101-L107) |

Sources: [ttnn/CMakeLists.txt 92-184](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/CMakeLists.txt#L92-L184)[ttnn/ttnn/types.py 10-122](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py#L10-L122)[ttnn/ttnn/device.py 14-17](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/device.py#L14-L17)

* * *

## Operation Categories and Modules

TTNN organizes operations into distinct categories, often grouped by their functional purpose or experimental status.

| Category | Python/C++ Context | Example Operations |
| --- | --- | --- |
| Matrix Multiplication | `ttnn.matmul` | `matmul`, `minimal_matmul`[ttnn/cpp/ttnn/operations/experimental/experimental_nanobind.cpp 62](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/experimental_nanobind.cpp#L62-L62) |
| Convolution | `ttnn.conv2d` | `conv2d`, `prepare_conv_weights`[ttnn/ttnn/operations/conv2d.py 105-121](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/operations/conv2d.py#L105-L121) |
| Distributed/CCL | `ttnn.multi_device` | `allgather_int`, `distribute_tensor`[ttnn/ttnn/__init__.py 105-141](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py#L105-L141) |
| Activation/Unary | `ttnn.activation` | `UnaryWithParam`, `UnaryOpType`[ttnn/ttnn/types.py 90-91](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py#L90-L91) |
| Experimental | `ttnn.experimental` | `adaptive_pool`, `deepseek_moe`, `paged_cache`[ttnn/cpp/ttnn/operations/experimental/experimental_nanobind.cpp 9-35](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/experimental_nanobind.cpp#L9-L35) |

Sources: [ttnn/ttnn/operations/conv2d.py 13-121](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/operations/conv2d.py#L13-L121)[ttnn/ttnn/__init__.py 105-141](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py#L105-L141)[ttnn/ttnn/types.py 90-95](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py#L90-L95)[ttnn/cpp/ttnn/operations/experimental/experimental_nanobind.cpp 9-85](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/experimental_nanobind.cpp#L9-L85)

* * *

## Dispatch and Execution Context

TTNN operations utilize a `DispatchContext` to manage the transition between slow and fast dispatch modes, particularly in multi-device (Mesh) environments.

### Dispatch Mode Transition

This diagram associates logic steps in the TTNN backend with the code entities that execute them.

Title: TTNN Dispatch Context Management

Sources: [tt_metal/distributed/dispatch_context.cpp 25-47](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/distributed/dispatch_context.cpp#L25-L47)[tt_metal/distributed/dispatch_context.cpp 88-107](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/distributed/dispatch_context.cpp#L88-L107)

The `DispatchContext` allows for manual initialization and termination of Fast Dispatch on supported architectures like Galaxy and Blackhole. It manages the lifecycle of command queues, including stashing Slow Dispatch queues during FD sessions. [tt_metal/distributed/dispatch_context.cpp 47-110](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/distributed/dispatch_context.cpp#L47-L110)

* * *

## Python API and Configuration

The `ttnn` Python package (rooted in `ttnn/ttnn/__init__.py`) sets up the environment for operation execution:

*   **Global `CONFIG`**: Managed via `ttnn._ttnn.CONFIG`, controlling runtime parameters like logging and configuration paths. [ttnn/ttnn/__init__.py 20-24](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py#L20-L24)
*   **Config Management**: Functions like `load_config_from_dictionary` and the `manage_config` context manager allow dynamic adjustment of operation behavior. [ttnn/ttnn/__init__.py 29-103](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py#L29-L103)
*   **Device Management**: Utility functions like `open_device`, `close_device`, and `synchronize_device` are exposed for hardware interaction. [ttnn/ttnn/device.py 30-62](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/device.py#L30-L62)
*   **Dispatch Configuration**: `DispatchCoreConfig` allows users to specify `DispatchCoreType` (WORKER/ETH) and `DispatchCoreAxis` (ROW/COL). [ttnn/ttnn/device.py 110-155](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/device.py#L110-L155)

Sources: [ttnn/ttnn/__init__.py 20-141](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py#L20-L141)[ttnn/ttnn/device.py 30-155](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/device.py#L30-L155)

* * *

## Tensor Abstraction and Memory Support

The `ttnn.Tensor` abstraction supports various storage types and layouts, providing the flexibility needed for diverse neural network architectures.

*   **Storage Types**: Tensors reside in `DEVICE_STORAGE_TYPE` (on-chip) or `StorageType.HOST`. [ttnn/ttnn/types.py 39-41](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py#L39-L41)
*   **Layouts**: Supports `ROW_MAJOR_LAYOUT` and `TILE_LAYOUT`. [ttnn/ttnn/types.py 35-37](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py#L35-L37)
*   **Memory Configuration**: Controlled via `MemoryConfig`, specifying `BufferType` (DRAM/L1) and `TensorMemoryLayout` (Interleaved/Sharded). [ttnn/ttnn/types.py 22-33](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py#L22-L33)
*   **Sharding**: Supports various strategies including `ShardStrategy` (HEIGHT, WIDTH, BLOCK) and `ShardSpec`. [ttnn/ttnn/types.py 69-81](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py#L69-L81)
*   **Data Types**: Comprehensive support for `bfloat16`, `float32`, `uint32`, `bfloat8_b`, and `fp8_e4m3`. [ttnn/ttnn/types.py 10-19](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py#L10-L19)

Multi-device support is handled through `MeshDevice` and `MeshTensor`, allowing data to be replicated or sharded across a cluster of chips. [ttnn/ttnn/__init__.py 105-141](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py#L105-L141)[ttnn/ttnn/device.py 14](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/device.py#L14-L14)

Sources: [ttnn/ttnn/types.py 10-81](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py#L10-L81)[ttnn/ttnn/__init__.py 105-141](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py#L105-L141)[ttnn/ttnn/device.py 14-17](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/device.py#L14-L17)

* * *

# Summary

The TTNN library provides a structured, high-level interface for tensor operations and neural network primitives optimized for Tenstorrent accelerators. It combines:

*   Python APIs backed by a nanobinded C++ core.
*   Modular operation registration and implementation patterns.
*   Rich tensor abstractions including sharding, layout control, and multi-device `Mesh` support.
*   Comprehensive configuration management for performance and accuracy tuning.

For in-depth details on usage, internals, and specific operation categories, consult the respective child pages.

* * *

# References

*   [ttnn/ttnn/__init__.py 1-247](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py#L1-L247)
*   [ttnn/CMakeLists.txt 1-200](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/CMakeLists.txt#L1-L200)
*   [ttnn/ttnn/types.py 1-122](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/types.py#L1-L122)
*   [ttnn/ttnn/device.py 1-166](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/device.py#L1-L166)
*   [ttnn/ttnn/operations/conv2d.py 1-121](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/operations/conv2d.py#L1-L121)
*   [tt_metal/distributed/dispatch_context.cpp 1-170](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/distributed/dispatch_context.cpp#L1-L170)
*   [ttnn/cpp/ttnn/operations/experimental/experimental_nanobind.cpp 1-85](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/cpp/ttnn/operations/experimental/experimental_nanobind.cpp#L1-L85)

Dismiss
Refresh this wiki

Enter email to refresh