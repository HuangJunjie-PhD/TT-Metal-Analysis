---
project: tt-tvm
github: https://github.com/tenstorrent/tt-tvm
deepwiki: https://deepwiki.com/tenstorrent/tt-tvm
---

# Overview

 Relevant source files

* [CMakeLists.txt](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt)
* [CONTRIBUTORS.md](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CONTRIBUTORS.md?plain=1)
* [NEWS.md](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/NEWS.md?plain=1)
* [cmake/config.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake)
* [cmake/modules/CUDA.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/CUDA.cmake)
* [cmake/modules/Git.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/Git.cmake)
* [cmake/modules/LibInfo.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/LibInfo.cmake)
* [cmake/utils/FindCUDA.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/utils/FindCUDA.cmake)
* [enable.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/enable.sh)
* [gallery/how\_to/deploy\_models/deploy\_model\_on\_android.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/deploy_models/deploy_model_on_android.py)
* [gallery/how\_to/tune\_with\_autoscheduler/tune\_network\_arm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_arm.py)
* [gallery/how\_to/tune\_with\_autoscheduler/tune\_network\_mali.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/gallery/how_to/tune_with_autoscheduler/tune_network_mali.py)
* [include/tvm/runtime/crt/crt.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/crt/crt.h)
* [install.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/install.sh)
* [jvm/README.md](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/jvm/README.md?plain=1)
* [python/tvm/relay/frontend/common.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py)
* [python/tvm/relay/frontend/mxnet.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/mxnet.py)
* [python/tvm/relay/frontend/onnx.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py)
* [python/tvm/relay/frontend/pytorch.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py)
* [python/tvm/relay/frontend/pytorch\_utils.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch_utils.py)
* [python/tvm/relay/frontend/qnn\_torch.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/qnn_torch.py)
* [python/tvm/relay/frontend/tensorflow.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py)
* [python/tvm/relay/frontend/tensorflow2.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow2.py)
* [python/tvm/relay/frontend/tensorflow2\_ops.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow2_ops.py)
* [python/tvm/relay/frontend/tensorflow\_ops.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow_ops.py)
* [python/tvm/relay/frontend/tflite.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py)
* [python/tvm/relay/frontend/tflite\_flexbuffer.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite_flexbuffer.py)
* [python/tvm/relay/testing/tf.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/testing/tf.py)
* [python/tvm/support.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/support.py)
* [src/relay/printer/relay\_text\_printer.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/printer/relay_text_printer.cc)
* [src/relay/printer/text\_printer.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/printer/text_printer.h)
* [src/support/libinfo.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/support/libinfo.cc)
* [tests/lint/check\_cmake\_options.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/check_cmake_options.py)
* [tests/python/frontend/mxnet/test\_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/mxnet/test_forward.py)
* [tests/python/frontend/onnx/test\_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/onnx/test_forward.py)
* [tests/python/frontend/pytorch/qnn\_test.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/qnn_test.py)
* [tests/python/frontend/pytorch/test\_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/test_forward.py)
* [tests/python/frontend/pytorch/test\_fx\_quant.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/test_fx_quant.py)
* [tests/python/frontend/pytorch/test\_object\_detection.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/test_object_detection.py)
* [tests/python/frontend/pytorch/test\_rnns.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/test_rnns.py)
* [tests/python/frontend/tensorflow/test\_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tensorflow/test_forward.py)
* [tests/python/frontend/tensorflow2/common.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tensorflow2/common.py)
* [tests/python/frontend/tensorflow2/test\_functional\_models.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tensorflow2/test_functional_models.py)
* [tests/python/frontend/tensorflow2/test\_sequential\_models.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tensorflow2/test_sequential_models.py)
* [tests/python/frontend/tflite/test\_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tflite/test_forward.py)
* [tests/scripts/task\_config\_build\_arm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_arm.sh)
* [tests/scripts/task\_config\_build\_cortexm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_cortexm.sh)
* [tests/scripts/task\_config\_build\_cpu.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_cpu.sh)
* [tests/scripts/task\_config\_build\_gpu.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu.sh)
* [tests/scripts/task\_config\_build\_gpu\_other.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu_other.sh)
* [tests/scripts/task\_config\_build\_gpu\_vulkan.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu_vulkan.sh)
* [tests/scripts/task\_config\_build\_hexagon.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_hexagon.sh)
* [tests/scripts/task\_config\_build\_i386.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_i386.sh)
* [tests/scripts/task\_config\_build\_wasm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_wasm.sh)

## Purpose and Scope

This page provides a high-level overview of **tt-tvm**, a fork of Apache TVM adapted for Tenstorrent hardware. tt-tvm is a deep learning compiler that ingests models from various machine learning frameworks (PyTorch, TensorFlow, ONNX, etc.) and compiles them into optimized executable code for diverse hardware targets including CPUs, GPUs, and specialized accelerators.

This overview covers:

* The overall system architecture and compilation pipeline
* Major subsystems and their relationships
* Key code entities and entry points
* Build and deployment infrastructure

For detailed information about specific subsystems, see:

* Frontend model ingestion: [Frontend Converters](/tenstorrent/tt-tvm/3-frontend-converters)
* Graph-level optimization: [Relay IR System](/tenstorrent/tt-tvm/4-relay-ir-system)
* Kernel-level optimization: [TensorIR (TIR) System](/tenstorrent/tt-tvm/5-tensorir-(tir)-system)
* Code generation: [Code Generation Backends](/tenstorrent/tt-tvm/6-code-generation-backends)
* Execution: [Runtime and Execution](/tenstorrent/tt-tvm/7-runtime-and-execution)
* Performance tuning: [Auto-Tuning System](/tenstorrent/tt-tvm/8-auto-tuning-system)
* Build configuration: [Build System](/tenstorrent/tt-tvm/9-build-system)

---

## System Architecture

tt-tvm implements a multi-stage compilation pipeline that progressively lowers high-level neural network models into optimized machine code. The system follows a layered architecture where each layer performs specific transformations and optimizations.

### Complete Compilation Flow

**Sources:** [python/tvm/relay/frontend/pytorch.py1-60](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L1-L60) [python/tvm/relay/frontend/onnx.py1-67](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L1-L67) [python/tvm/relay/frontend/tensorflow.py1-50](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py#L1-L50) [python/tvm/relay/frontend/tflite.py1-46](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L1-L46) [python/tvm/relay/frontend/mxnet.py1-70](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/mxnet.py#L1-L70)

---

## Major System Components

### Frontend Converters

The frontend layer provides unified entry points for converting models from different ML frameworks into Relay IR. Each converter implements framework-specific logic while sharing common infrastructure.

#### Frontend Entry Points and Key Classes

**Key Code Entities:**

| Component | File Location | Primary Classes/Functions |
| --- | --- | --- |
| PyTorch Frontend | [python/tvm/relay/frontend/pytorch.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py) | `from_pytorch()` (line 59), `PyTorchOpConverter` (line 156) |
| ONNX Frontend | [python/tvm/relay/frontend/onnx.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py) | `from_onnx()` (line 67), `OnnxOpConverter` (line 562) |
| TensorFlow Frontend | [python/tvm/relay/frontend/tensorflow.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py) | `from_tensorflow()` (line 50), `_convert_map` |
| TFLite Frontend | [python/tvm/relay/frontend/tflite.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py) | `from_tflite()` (line 46), `OperatorConverter` (line 59) |
| MXNet Frontend | [python/tvm/relay/frontend/mxnet.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/mxnet.py) | `from_mxnet()` (line 70) |
| Common Utilities | [python/tvm/relay/frontend/common.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py) | `AttrCvt`, `infer_type`, `infer_shape`, `set_span` |

**Sources:** [python/tvm/relay/frontend/pytorch.py1-167](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L1-L167) [python/tvm/relay/frontend/onnx.py1-584](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L1-L584) [python/tvm/relay/frontend/tensorflow.py1-50](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py#L1-L50) [python/tvm/relay/frontend/tflite.py1-188](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L1-L188) [python/tvm/relay/frontend/mxnet.py1-70](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/mxnet.py#L1-L70) [python/tvm/relay/frontend/common.py1-100](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L1-L100)

### Relay IR: Graph-Level Representation

After frontend conversion, models are represented as Relay IR - a functional, graph-based intermediate representation. Relay provides a high-level abstraction for neural network operators and supports both static and dynamic shapes.

**Sources:** [python/tvm/relay/frontend/common.py41-53](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L41-L53)

### Build System Configuration

tt-tvm uses CMake for cross-platform compilation with extensive configuration options for different hardware targets and features.

#### Build Configuration Structure

**Key Build Options:**

| Category | Options | File Reference |
| --- | --- | --- |
| CUDA Support | `USE_CUDA`, `USE_CUDNN`, `USE_CUBLAS` | [CMakeLists.txt29](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L29-L29) [cmake/config.cmake49](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L49-L49) |
| LLVM/CPU | `USE_LLVM`, `USE_DNNL` | [CMakeLists.txt56](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L56-L56) [cmake/config.cmake149](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L149-L149) |
| ROCm/AMD | `USE_ROCM`, `USE_RCCL` | [CMakeLists.txt46-47](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L46-L47) [cmake/config.cmake63](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L63-L63) |
| Embedded | `USE_MICRO`, `USE_HEXAGON` | [CMakeLists.txt67](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L67-L67) [cmake/config.cmake117](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L117-L117) |
| Executors | `USE_GRAPH_EXECUTOR`, `USE_AOT_EXECUTOR` | [CMakeLists.txt59-61](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L59-L61) [cmake/config.cmake135](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L135-L135) |

**Sources:** [CMakeLists.txt1-135](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L1-L135) [cmake/config.cmake1-149](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L1-L149) [cmake/modules/LibInfo.cmake1-50](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/LibInfo.cmake#L1-L50) [src/support/libinfo.cc1-50](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/support/libinfo.cc#L1-L50)

---

## Installation and Setup

tt-tvm provides installation scripts that handle dependencies and build configuration.

### Installation Process

The [install.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/install.sh) script performs the following steps:

1. **LLVM Setup**: Downloads and extracts LLVM 13.0.0

   * Location: [install.sh14-21](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/install.sh#L14-L21)
   * Target directory: `third_party/llvm/`
2. **Submodule Initialization**: Initializes git submodules

   * Location: [install.sh23-24](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/install.sh#L23-L24)
3. **Build Configuration**: Configures CMake with LLVM path

   * Location: [install.sh26-32](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/install.sh#L26-L32)
   * Build type: Debug or Release based on `TVM_BUILD_CONFIG`
4. **Compilation**: Builds TVM with parallel make

   * Location: [install.sh34-42](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/install.sh#L34-L42)
5. **Python Package Installation**: Installs TVM Python bindings

   * Location: [install.sh47](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/install.sh#L47-L47)

### Environment Setup

The [enable.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/enable.sh) script sets up the environment:

**Sources:** [install.sh1-55](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/install.sh#L1-L55) [enable.sh1-10](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/enable.sh#L1-L10)

---

## Key Abstractions and Data Flow

### IR Module and Parameters

All frontend converters produce the same output format:

**Code Example Locations:**

* PyTorch: [python/tvm/relay/frontend/pytorch.py36](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L36-L36) - `IRModule` import
* ONNX: [python/tvm/relay/frontend/onnx.py31](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L31-L31) - `IRModule` import
* TensorFlow: [python/tvm/relay/frontend/tensorflow.py30](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py#L30-L30) - `IRModule` import
* Common type inference: [python/tvm/relay/frontend/common.py41-49](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L41-L49)

**Sources:** [python/tvm/relay/frontend/pytorch.py36](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L36-L36) [python/tvm/relay/frontend/onnx.py31](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L31-L31) [python/tvm/relay/frontend/tensorflow.py30](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py#L30-L30) [python/tvm/relay/frontend/common.py41-49](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L41-L49)

### Operator Conversion Pattern

Each frontend implements a similar pattern for operator conversion:

**Implementation Examples:**

| Frontend | Converter Class | Example Operator | Location |
| --- | --- | --- | --- |
| PyTorch | `PyTorchOpConverter` | `make_elemwise()` | [python/tvm/relay/frontend/pytorch.py156-310](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L156-L310) |
| ONNX | `OnnxOpConverter` | `get_converter()` | [python/tvm/relay/frontend/onnx.py562-584](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L562-L584) |
| TFLite | `OperatorConverter` | `convert_op_to_relay()` | [python/tvm/relay/frontend/tflite.py267-309](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L267-L309) |
| TensorFlow | `_convert_map` dict | TF operator mapping | [python/tvm/relay/frontend/tensorflow\_ops.py1-50](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow_ops.py#L1-L50) |

**Sources:** [python/tvm/relay/frontend/pytorch.py156-310](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L156-L310) [python/tvm/relay/frontend/onnx.py562-584](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L562-L584) [python/tvm/relay/frontend/tflite.py267-309](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L267-L309) [python/tvm/relay/frontend/tensorflow\_ops.py1-50](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow_ops.py#L1-L50)

---

## Testing Infrastructure

tt-tvm includes comprehensive test suites for each frontend converter to ensure correctness.

### Test Organization

**Test File Locations:**

| Framework | Test File | Key Functions |
| --- | --- | --- |
| PyTorch | [tests/python/frontend/pytorch/test\_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/test_forward.py) | `verify_model()` (line 133), `list_ops()` (line 50) |
| ONNX | [tests/python/frontend/onnx/test\_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/onnx/test_forward.py) | `verify_with_ort()` (line 250), `get_tvm_output()` (line 132) |
| TensorFlow | [tests/python/frontend/tensorflow/test\_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tensorflow/test_forward.py) | `compare_tf_with_tvm()` (line 243), `run_tvm_graph()` (line 129) |
| TFLite | [tests/python/frontend/tflite/test\_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tflite/test_forward.py) | `run_tvm_graph()` (line 190), `run_tflite_graph()` (line 271) |

**Sources:** [tests/python/frontend/pytorch/test\_forward.py50-277](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/test_forward.py#L50-L277) [tests/python/frontend/onnx/test\_forward.py132-278](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/onnx/test_forward.py#L132-L278) [tests/python/frontend/tensorflow/test\_forward.py129-243](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tensorflow/test_forward.py#L129-L243) [tests/python/frontend/tflite/test\_forward.py190-297](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tflite/test_forward.py#L190-L297)

---

## System Dependencies and Third-Party Integration

tt-tvm integrates with numerous third-party libraries and frameworks:

### Core Dependencies

| Dependency | Purpose | Configuration |
| --- | --- | --- |
| LLVM | CPU/GPU code generation | `USE_LLVM` in [cmake/config.cmake149](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L149-L149) |
| CUDA | NVIDIA GPU support | `USE_CUDA` in [cmake/config.cmake49](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L49-L49) |
| ROCm | AMD GPU support | `USE_ROCM` in [cmake/config.cmake63](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L63-L63) |
| DNNL | Intel CPU optimizations | `USE_DNNL` in [CMakeLists.txt98](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L98-L98) |
| DLPACK | Tensor interchange format | [CMakeLists.txt87](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L87-L87) |

### Frontend Framework Integration

The system supports models from:

* **PyTorch**: via `torch.jit.trace()` - [python/tvm/relay/frontend/pytorch.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py)
* **TensorFlow**: via GraphDef protocol buffers - [python/tvm/relay/frontend/tensorflow.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py)
* **ONNX**: via ONNX model format - [python/tvm/relay/frontend/onnx.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py)
* **TFLite**: via FlatBuffer format - [python/tvm/relay/frontend/tflite.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py)
* **MXNet**: via symbol/gluon APIs - [python/tvm/relay/frontend/mxnet.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/mxnet.py)

**Sources:** [CMakeLists.txt87-129](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L87-L129) [cmake/config.cmake49-149](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L49-L149)

---

## Summary

tt-tvm is a comprehensive deep learning compiler that:

1. **Ingests models** from multiple frameworks (PyTorch, TensorFlow, ONNX, TFLite, MXNet) through specialized frontend converters
2. **Represents models** using Relay IR, a functional graph-based intermediate representation
3. **Optimizes models** through graph-level and kernel-level transformations
4. **Generates code** for diverse hardware targets (CPUs, GPUs, accelerators)
5. **Executes models** using multiple runtime backends (graph executor, VM, AOT)
6. **Configures builds** through a flexible CMake system with 100+ options
7. **Validates correctness** through extensive test suites for each frontend

The system's modular architecture enables extensibility while maintaining a clean separation between frontend conversion, intermediate optimization, backend code generation, and runtime execution. This design allows tt-tvm to support new frameworks, operators, and hardware targets with minimal changes to existing components.

**Sources:** [CMakeLists.txt1-135](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L1-L135) [python/tvm/relay/frontend/pytorch.py1-167](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L1-L167) [python/tvm/relay/frontend/onnx.py1-584](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L1-L584) [python/tvm/relay/frontend/tensorflow.py1-50](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py#L1-L50) [python/tvm/relay/frontend/common.py1-100](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L1-L100) [install.sh1-55](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/install.sh#L1-L55)

Refresh this wiki