---
project: vllm
github: https://github.com/tenstorrent/vllm
deepwiki: https://deepwiki.com/tenstorrent/vllm/7-development-guide
---

# Development Guide

Relevant source files
*   [.buildkite/release-pipeline.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/.buildkite/release-pipeline.yaml)
*   [.buildkite/scripts/annotate-release.sh](https://github.com/tenstorrent/vllm/blob/cd9e5b83/.buildkite/scripts/annotate-release.sh)
*   [.buildkite/scripts/cleanup-nightly-builds.sh](https://github.com/tenstorrent/vllm/blob/cd9e5b83/.buildkite/scripts/cleanup-nightly-builds.sh)
*   [.buildkite/scripts/upload-wheels.sh](https://github.com/tenstorrent/vllm/blob/cd9e5b83/.buildkite/scripts/upload-wheels.sh)
*   [CMakeLists.txt](https://github.com/tenstorrent/vllm/blob/cd9e5b83/CMakeLists.txt)
*   [cmake/utils.cmake](https://github.com/tenstorrent/vllm/blob/cd9e5b83/cmake/utils.cmake)
*   [csrc/ops.h](https://github.com/tenstorrent/vllm/blob/cd9e5b83/csrc/ops.h)
*   [csrc/quantization/cutlass_w8a8/c3x/scaled_mm_blockwise_sm100_fp8_dispatch.cuh](https://github.com/tenstorrent/vllm/blob/cd9e5b83/csrc/quantization/cutlass_w8a8/c3x/scaled_mm_blockwise_sm100_fp8_dispatch.cuh)
*   [csrc/quantization/cutlass_w8a8/c3x/scaled_mm_sm100_fp8_dispatch.cuh](https://github.com/tenstorrent/vllm/blob/cd9e5b83/csrc/quantization/cutlass_w8a8/c3x/scaled_mm_sm100_fp8_dispatch.cuh)
*   [csrc/quantization/cutlass_w8a8/scaled_mm_entry.cu](https://github.com/tenstorrent/vllm/blob/cd9e5b83/csrc/quantization/cutlass_w8a8/scaled_mm_entry.cu)
*   [csrc/torch_bindings.cpp](https://github.com/tenstorrent/vllm/blob/cd9e5b83/csrc/torch_bindings.cpp)
*   [docker/Dockerfile](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docker/Dockerfile)
*   [pyproject.toml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/pyproject.toml)
*   [setup.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/setup.py)
*   [tools/install_deepgemm.sh](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tools/install_deepgemm.sh)
*   [vllm/_custom_ops.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/_custom_ops.py)

This guide is for developers who want to contribute to or extend the functionality of vLLM, a high-performance inference and serving engine for LLMs. It covers the build system, C++/CUDA extensions, model loading, and extension points to help you navigate and modify the codebase effectively.

For information about installation as an end user, see [Installation Guide](https://deepwiki.com/tenstorrent/vllm/1.1-installation-guide). For details about supported models, see [Supported Models](https://deepwiki.com/tenstorrent/vllm/1.2-supported-models).

## Setting Up a Development Environment

### Prerequisites

*   Python 3.9+ (up to 3.12)
*   CUDA Toolkit (11.8, 12.1, or 12.4 recommended)
*   CMake 3.26+
*   Ninja build system (recommended)
*   PyTorch 2.6.0

### Python-only Development

If you only need to modify Python code (not C++/CUDA extensions), you can use pre-compiled binaries:

This approach downloads pre-built extensions from a matching commit in the main branch and creates an editable installation where changes to Python code take effect immediately.

Sources: [docs/source/getting_started/installation/gpu/cuda.inc.md 80-93](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docs/source/getting_started/installation/gpu/cuda.inc.md?plain=1#L80-L93)

### Full Development Build

For development that includes C++/CUDA extensions:

This builds both Python and C++/CUDA components. The first build can take significant time due to CUDA compilation.

Sources: [setup.py 267-282](https://github.com/tenstorrent/vllm/blob/cd9e5b83/setup.py#L267-L282)

## Build System Architecture

vLLM uses a sophisticated build system to handle its mix of Python, C++, and CUDA/HIP code.

Sources: [setup.py 84-221](https://github.com/tenstorrent/vllm/blob/cd9e5b83/setup.py#L84-L221)[CMakeLists.txt 14-151](https://github.com/tenstorrent/vllm/blob/cd9e5b83/CMakeLists.txt#L14-L151)

### Key Components of the Build System

1.   **setup.py**: The main entry point that:

    *   Detects the target device (CUDA, ROCm, CPU, TPU, etc.)
    *   Configures build options and dependencies
    *   Defines extension modules
    *   Manages Python package metadata

2.   **CMakeLists.txt**: The CMake configuration file that:

    *   Sets up compiler flags for different platforms
    *   Configures CUDA/HIP compilation options
    *   Defines build targets for extension modules
    *   Handles conditional compilation based on device capabilities

3.   **Extension Modules**:

    *   `_C`: Main C++ extension with attention, activation, and quantization kernels
    *   `_moe_C`: Extension for Mixture of Experts operations
    *   `vllm_flash_attn`: Flash Attention integration
    *   `cumem_allocator`: Custom memory allocator for CUDA

Sources: [setup.py 635-675](https://github.com/tenstorrent/vllm/blob/cd9e5b83/setup.py#L635-L675)[CMakeLists.txt 229-577](https://github.com/tenstorrent/vllm/blob/cd9e5b83/CMakeLists.txt#L229-L577)

### Build Options

vLLM supports several build configurations through environment variables:

| Variable | Description | Default |
| --- | --- | --- |
| `VLLM_TARGET_DEVICE` | Target device (cuda, rocm, cpu, tpu, etc.) | `cuda` |
| `VLLM_USE_PRECOMPILED` | Use pre-compiled binaries | `False` |
| `MAX_JOBS` | Maximum number of parallel build jobs | CPU count |
| `NVCC_THREADS` | Number of threads for NVCC compilation | `1` |
| `CMAKE_BUILD_TYPE` | Build type (Debug, RelWithDebInfo) | `RelWithDebInfo` for production |

Sources: [setup.py 36-50](https://github.com/tenstorrent/vllm/blob/cd9e5b83/setup.py#L36-L50)[setup.py 98-129](https://github.com/tenstorrent/vllm/blob/cd9e5b83/setup.py#L98-L129)

### Platform Support

vLLM supports multiple hardware platforms:

| Platform | Environment Variable | Files Built |
| --- | --- | --- |
| CUDA | `VLLM_TARGET_DEVICE=cuda` | All extensions + CUDA-specific kernels |
| ROCm | `VLLM_TARGET_DEVICE=rocm` | `_C`, `_moe_C`, `_rocm_C` |
| CPU | `VLLM_TARGET_DEVICE=cpu` | CPU-optimized kernels |
| TPU | `VLLM_TARGET_DEVICE=tpu` | TPU-specific implementation |
| HPU (Gaudi) | `VLLM_TARGET_DEVICE=hpu` | HPU-specific implementation |

Sources: [setup.py 405-457](https://github.com/tenstorrent/vllm/blob/cd9e5b83/setup.py#L405-L457)

## C++/CUDA Extensions

vLLM uses custom C++/CUDA operations for performance-critical components. These are implemented in the `csrc/` directory and exposed to Python through PyTorch's C++ extension mechanism.

Sources: [vllm/_custom_ops.py 16-127](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/_custom_ops.py#L16-L127)[csrc/ops.h 31-298](https://github.com/tenstorrent/vllm/blob/cd9e5b83/csrc/ops.h#L31-L298)[csrc/torch_bindings.cpp 19-278](https://github.com/tenstorrent/vllm/blob/cd9e5b83/csrc/torch_bindings.cpp#L19-L278)[CMakeLists.txt 229-245](https://github.com/tenstorrent/vllm/blob/cd9e5b83/CMakeLists.txt#L229-L245)

### Key CUDA Operations

1.   **Paged Attention**: Core attention implementation with PagedAttention mechanism

    *   `paged_attention_v1` and `paged_attention_v2`: Efficient attention computation with block-based KV cache

2.   **Position Encoding**:

    *   `rotary_embedding`: Applies rotary position embeddings to query and key tensors
    *   `batched_rotary_embedding`: Batched version supporting multiple LoRA adapters

3.   **Layer Normalization**:

    *   `rms_norm`: Optimized RMS normalization
    *   `fused_add_rms_norm`: Fused addition and normalization for residual connections

4.   **Activation Functions**:

    *   `silu_and_mul`, `gelu_and_mul`: Fused activation and multiplication operations
    *   Various GELU variants (fast, new, quick)

5.   **Quantization Kernels**:

    *   Optimized kernels for various quantization methods (GPTQ, AWQ, Marlin, etc.)
    *   Specialized matrix multiplication routines for different quantization formats

Sources: [vllm/_custom_ops.py 40-478](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/_custom_ops.py#L40-L478)[csrc/torch_bindings.cpp 41-266](https://github.com/tenstorrent/vllm/blob/cd9e5b83/csrc/torch_bindings.cpp#L41-L266)

### Adding a New CUDA Kernel

To add a new CUDA kernel to vLLM:

1.   Implement the kernel in a `.cu` file in the appropriate subdirectory of `csrc/`
2.   Add the function declaration to `csrc/ops.h`
3.   Register the operation in `csrc/torch_bindings.cpp` using `ops.def` and `ops.impl`
4.   Add Python bindings in `vllm/_custom_ops.py`
5.   Add the source file to the appropriate extension in `CMakeLists.txt`

Example flow for registering a new operation:

Sources: [csrc/torch_bindings.cpp 31-66](https://github.com/tenstorrent/vllm/blob/cd9e5b83/csrc/torch_bindings.cpp#L31-L66)[vllm/_custom_ops.py 40-92](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/_custom_ops.py#L40-L92)

## Model Loading System

vLLM's model loading system is designed to support various model formats, storage backends, and quantization methods.

Sources: [vllm/model_executor/model_loader/loader.py 193-473](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/model_loader/loader.py#L193-L473)

### Model Loader Components

vLLM implements several model loaders:

1.   **BaseModelLoader**: Abstract base class defining the loader interface
2.   **DefaultModelLoader**: Standard loader for various file formats from HuggingFace Hub or local files
3.   **DummyModelLoader**: Sets model weights to random values (for testing/benchmarking)
4.   **TensorizerLoader**: Uses CoreWeave's tensorizer library for efficient loading
5.   **ShardedStateLoader**: Efficiently loads sharded models for tensor parallelism

Sources: [vllm/model_executor/model_loader/loader.py 210-600](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/model_loader/loader.py#L210-L600)

### Model Loading Process

The model loading process involves:

1.   **Weight Download**: If needed, download model weights from remote sources
2.   **Format Parsing**: Handle different formats (safetensors, PyTorch, GGUF)
3.   **Weight Processing**: Apply any necessary transformations (reshaping, data type conversion)
4.   **Tensor Parallelism**: For distributed execution, load only the required shard
5.   **Quantization**: Apply quantization methods if specified

Sources: [vllm/model_executor/model_loader/loader.py 354-456](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/model_loader/loader.py#L354-L456)[vllm/model_executor/model_loader/weight_utils.py 228-351](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/model_loader/weight_utils.py#L228-L351)

### Weight Formats Support

vLLM supports multiple weight file formats:

| Format | Description | Environment Variable |
| --- | --- | --- |
| Safetensors | Safe format avoiding Python deserialization | `VLLM_LOAD_FORMAT=safetensors` |
| FastSafetensors | Optimized safetensors loading | `VLLM_LOAD_FORMAT=fastsafetensors` |
| PyTorch | Standard PyTorch checkpoint | `VLLM_LOAD_FORMAT=pt` |
| GGUF | GGUF format (commonly used for quantized models) | Automatic detection |
| NPCache | NumPy-based cache format | `VLLM_LOAD_FORMAT=npcache` |

Sources: [vllm/model_executor/model_loader/loader.py 285-303](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/model_loader/loader.py#L285-L303)

## Quantization System

vLLM supports numerous quantization methods through a flexible registration system.

Sources: [vllm/model_executor/layers/quantization/__init__.py 8-138](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/layers/quantization/__init__.py#L8-L138)[vllm/model_executor/layers/linear.py 136-158](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/layers/linear.py#L136-L158)

### Supported Quantization Methods

vLLM supports a wide range of quantization methods:

| Method | Description |
| --- | --- |
| GPTQ | Generative Pre-trained Transformer Quantization |
| AWQ | Activation-aware Weight Quantization |
| Marlin | Optimized quantization with custom CUDA kernels |
| FP8 | 8-bit floating point quantization |
| Compressed Tensors | Various compression formats (W8A8, W4A16, etc.) |
| GGUF | GGUF format quantization |
| NVFp4 | NVIDIA 4-bit floating point |

Sources: [vllm/model_executor/layers/quantization/__init__.py 8-36](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/layers/quantization/__init__.py#L8-L36)

### Adding a New Quantization Method

To add a custom quantization method:

1.   Create a new class that extends `QuantizationConfig`
2.   Register it using the `register_quantization_config` decorator
3.   Implement the necessary methods and kernels

Example:

Sources: [vllm/model_executor/layers/quantization/__init__.py 42-75](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/layers/quantization/__init__.py#L42-L75)

## Linear Layer System

vLLM implements a flexible system for linear layers that supports various parallel execution strategies and quantization methods.

Sources: [vllm/model_executor/layers/linear.py 194-1204](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/layers/linear.py#L194-L1204)

### Linear Layer Components

vLLM implements several linear layer types:

1.   **LinearBase**: Abstract base class for all linear layers
2.   **ReplicatedLinear**: Standard linear layer without parallelism
3.   **ColumnParallelLinear**: Linear layer with column parallelism (output dimension sharded)
4.   **RowParallelLinear**: Linear layer with row parallelism (input dimension sharded)
5.   **MergedColumnParallelLinear**: Packed linear layers with column parallelism
6.   **QKVParallelLinear**: Specialized layer for query-key-value projections

Sources: [vllm/model_executor/layers/linear.py 194-332](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/layers/linear.py#L194-L332)[vllm/model_executor/layers/linear.py 334-491](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/layers/linear.py#L334-L491)[vllm/model_executor/layers/linear.py 494-672](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/layers/linear.py#L494-L672)

### Weight Loading

Linear layers in vLLM implement specialized weight loading mechanisms:

1.   **Standard loading**: Direct copy to parameter tensor
2.   **Sharded loading**: Load only the required shard for tensor parallelism
3.   **Quantized loading**: Handle specialized formats for quantized weights

Sources: [vllm/model_executor/layers/linear.py 419-465](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/layers/linear.py#L419-L465)

## Contributing Guidelines

When contributing to vLLM:

### Code Style

vLLM follows specific code style conventions:

*   Python: Enforced by ruff and mypy
*   C++/CUDA: Follow existing code style
*   Line length limit: 80 characters

Sources: [pyproject.toml 57-106](https://github.com/tenstorrent/vllm/blob/cd9e5b83/pyproject.toml#L57-L106)[pyproject.toml 120-146](https://github.com/tenstorrent/vllm/blob/cd9e5b83/pyproject.toml#L120-L146)

### Testing

*   Add unit tests for new functionality
*   For quantization methods, add entries to `tests/weight_loading/models.txt`
*   Run tests with `pytest` before submitting PRs

Sources: [tests/weight_loading/models.txt 1-34](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/weight_loading/models.txt#L1-L34)

### Documentation

*   Add docstrings to new classes and functions
*   Update relevant documentation if changing user-facing features
*   Use sphinx-compatible docstring format

Sources: [docs/source/conf.py 1-266](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docs/source/conf.py#L1-L266)

### Build Process

*   Test your changes with both CUDA and CPU builds
*   For C++/CUDA changes, test with multiple CUDA versions if possible
*   Use `bash tools/check_repo.sh` to verify code quality

Sources: [.github/workflows/scripts/build.sh 1-24](https://github.com/tenstorrent/vllm/blob/cd9e5b83/.github/workflows/scripts/build.sh#L1-L24)

## Debugging Tips

### Common Issues

*   **Import errors for C++ extensions**: Check that you're using a compatible CUDA/PyTorch version
*   **Build failures**: Look at detailed CMake logs in the build directory
*   **Weight loading issues**: Enable more verbose logging with `VLLM_LOG_LEVEL=debug`

### Profiling

*   For CUDA kernel performance, use NVIDIA's Nsight Compute or Systems
*   For Python code, use standard Python profiling tools

### Developing C++ Extensions

When modifying C++ extensions:

1.   Build with `CMAKE_BUILD_TYPE=Debug` for better debugging information
2.   Consider using `NVCC_THREADS` to speed up compilation
3.   Use `cmake --build build --target _C` to build only specific targets after changes

Sources: [setup.py 143-144](https://github.com/tenstorrent/vllm/blob/cd9e5b83/setup.py#L143-L144)

## Summary

This development guide covers the main components of vLLM that you'll need to understand to contribute effectively. The build system, custom CUDA operations, model loading system, and quantization framework are the core elements that enable vLLM's high-performance LLM inference.

When extending vLLM, focus on maintaining its performance characteristics while adding your new functionality, and follow the existing patterns for extension points to ensure compatibility with the rest of the system.

Sources: [setup.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/setup.py)[CMakeLists.txt](https://github.com/tenstorrent/vllm/blob/cd9e5b83/CMakeLists.txt)[vllm/model_executor/model_loader/loader.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/model_loader/loader.py)[vllm/model_executor/layers/linear.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/layers/linear.py)[vllm/_custom_ops.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/_custom_ops.py)[vllm/model_executor/layers/quantization/__init__.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/layers/quantization/__init__.py)

Dismiss
Refresh this wiki

Enter email to refresh