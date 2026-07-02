---
project: tt-tvm
github: https://github.com/tenstorrent/tt-tvm
deepwiki: https://deepwiki.com/tenstorrent/tt-tvm/11-development-guide
---

# Development Guide

Relevant source files
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt)
*   [apps/microtvm/reference-vm/base-box/base_box_provision.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/apps/microtvm/reference-vm/base-box/base_box_provision.sh)
*   [apps/microtvm/reference-vm/rebuild_tvm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/apps/microtvm/reference-vm/rebuild_tvm.sh)
*   [ci/jenkins/generate.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py)
*   [cmake/config.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake)
*   [cmake/modules/CUDA.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/CUDA.cmake)
*   [cmake/modules/Git.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/Git.cmake)
*   [cmake/modules/LibInfo.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/LibInfo.cmake)
*   [cmake/utils/FindCUDA.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/utils/FindCUDA.cmake)
*   [docker/Dockerfile.ci_arm](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_arm)
*   [docker/Dockerfile.ci_cortexm](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cortexm)
*   [docker/Dockerfile.ci_cpu](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cpu)
*   [docker/Dockerfile.ci_gpu](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_gpu)
*   [docker/Dockerfile.ci_hexagon](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_hexagon)
*   [docker/Dockerfile.ci_i386](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_i386)
*   [docker/Dockerfile.ci_lint](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_lint)
*   [docker/Dockerfile.ci_minimal](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_minimal)
*   [docker/Dockerfile.ci_riscv](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_riscv)
*   [docker/Dockerfile.ci_wasm](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_wasm)
*   [docker/install/ubuntu2004_install_python.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu2004_install_python.sh)
*   [docker/install/ubuntu_init_zephyr_project.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_init_zephyr_project.sh)
*   [docker/install/ubuntu_install_androidsdk.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_androidsdk.sh)
*   [docker/install/ubuntu_install_arduino.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_arduino.sh)
*   [docker/install/ubuntu_install_boost.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_boost.sh)
*   [docker/install/ubuntu_install_caffe.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_caffe.sh)
*   [docker/install/ubuntu_install_cmake_source.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_cmake_source.sh)
*   [docker/install/ubuntu_install_core.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_core.sh)
*   [docker/install/ubuntu_install_darknet.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_darknet.sh)
*   [docker/install/ubuntu_install_emscripten.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_emscripten.sh)
*   [docker/install/ubuntu_install_gradle.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_gradle.sh)
*   [docker/install/ubuntu_install_llvm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_llvm.sh)
*   [docker/install/ubuntu_install_nnpack.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_nnpack.sh)
*   [docker/install/ubuntu_install_nodejs.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_nodejs.sh)
*   [docker/install/ubuntu_install_onnx.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_onnx.sh)
*   [docker/install/ubuntu_install_papi.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_papi.sh)
*   [docker/install/ubuntu_install_python.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python.sh)
*   [docker/install/ubuntu_install_python_package.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python_package.sh)
*   [docker/install/ubuntu_install_redis.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_redis.sh)
*   [docker/install/ubuntu_install_sbt.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_sbt.sh)
*   [docker/install/ubuntu_install_sphinx.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_sphinx.sh)
*   [docker/install/ubuntu_install_tensorflow.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_tensorflow.sh)
*   [docker/install/ubuntu_install_tensorflow_aarch64.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_tensorflow_aarch64.sh)
*   [docker/install/ubuntu_install_tflite.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_tflite.sh)
*   [docker/install/ubuntu_install_verilator.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_verilator.sh)
*   [docker/install/ubuntu_install_vulkan.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_vulkan.sh)
*   [docker/install/ubuntu_install_zephyr.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_zephyr.sh)
*   [docker/install/ubuntu_install_zephyr_sdk.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_zephyr_sdk.sh)
*   [docs/how_to/profile/papi.rst](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docs/how_to/profile/papi.rst)
*   [python/tvm/support.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/support.py)
*   [src/support/libinfo.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/support/libinfo.cc)
*   [tests/lint/check_cmake_options.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/check_cmake_options.py)
*   [tests/lint/flake8.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/flake8.sh)
*   [tests/scripts/setup-pytest-env.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh)
*   [tests/scripts/task_config_build_arm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_arm.sh)
*   [tests/scripts/task_config_build_cortexm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_cortexm.sh)
*   [tests/scripts/task_config_build_cpu.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_cpu.sh)
*   [tests/scripts/task_config_build_gpu.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu.sh)
*   [tests/scripts/task_config_build_gpu_other.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu_other.sh)
*   [tests/scripts/task_config_build_gpu_vulkan.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu_vulkan.sh)
*   [tests/scripts/task_config_build_hexagon.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_hexagon.sh)
*   [tests/scripts/task_config_build_i386.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_i386.sh)
*   [tests/scripts/task_config_build_minimal.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_minimal.sh)
*   [tests/scripts/task_config_build_wasm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_wasm.sh)

This guide provides practical information for developers contributing to tt-tvm. It covers setting up a development environment, building the project with various configurations, running tests, and understanding the development workflow. For information about the overall architecture and compilation pipeline, see [Architecture and Compilation Pipeline](https://deepwiki.com/tenstorrent/tt-tvm/2-architecture-and-compilation-pipeline). For specific component implementation details, refer to the subsections under this guide (11.1-11.5).

## Prerequisites and Dependencies

TVM requires several core dependencies regardless of the build configuration. The base requirements are documented in the CI Docker images and installation scripts.

### Core Build Dependencies

The CI environments use Ubuntu 22.04 as the base system. Core dependencies are installed via [docker/install/ubuntu_install_core.sh 26-46](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_core.sh#L26-L46) which includes:

*   `g++`, `git`, `make`, `cmake`, `ninja-build`
*   `libcurl4-openssl-dev`, `libssl-dev`, `libtinfo-dev`, `libz-dev`
*   `libopenblas-dev` for BLAS operations

Python dependencies are managed through a virtual environment at `/venv/apache-tvm-py3.8`, installed by [docker/install/ubuntu_install_python.sh 84-108](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python.sh#L84-L108) and [docker/install/ubuntu_install_python_package.sh 24-48](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python_package.sh#L24-L48)

**Sources:**[docker/install/ubuntu_install_core.sh 1-47](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_core.sh#L1-L47)[docker/install/ubuntu_install_python.sh 1-124](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python.sh#L1-L124)[docker/install/ubuntu_install_python_package.sh 1-49](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python_package.sh#L1-L49)

### Backend-Specific Dependencies

Different hardware backends require specific SDKs and libraries:

| Backend | Dependency | Installation Script |
| --- | --- | --- |
| CUDA | CUDA Toolkit, cuDNN, cuBLAS | [cmake/utils/FindCUDA.cmake 41-143](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/utils/FindCUDA.cmake#L41-L143) |
| ROCm | ROCm SDK, MIOpen, rocBLAS | [docker/install/ubuntu_install_rocm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_rocm.sh) |
| OpenCL | OpenCL headers and ICD loader | [docker/install/ubuntu_install_opencl.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_opencl.sh) |
| Vulkan | Vulkan SDK | [docker/install/ubuntu_install_vulkan.sh 23-26](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_vulkan.sh#L23-L26) |
| Hexagon DSP | Hexagon SDK, Android NDK | [docker/install/ubuntu_install_hexagon.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_hexagon.sh) |
| MicroTVM | Zephyr SDK, ARM toolchains | [docker/install/ubuntu_install_zephyr.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_zephyr.sh) |

**Sources:**[cmake/modules/CUDA.cmake 1-98](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/CUDA.cmake#L1-L98)[docker/Dockerfile.ci_gpu 1-172](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_gpu#L1-L172)[docker/Dockerfile.ci_hexagon 1-96](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_hexagon#L1-L96)

## Building TVM from Source

### CMake Configuration System

TVM uses CMake with a configuration file approach. The build is controlled by [CMakeLists.txt 1-898](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L1-L898) and customized through `config.cmake`.

The standard workflow is:

1.   Copy the template: `cp cmake/config.cmake build/config.cmake`
2.   Edit `build/config.cmake` with desired options
3.   Run CMake from the build directory: `cmake ..`
4.   Build with `make -j$(nproc)` or `ninja`

**Sources:**[CMakeLists.txt 17-28](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L17-L28)[cmake/config.cmake 18-37](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L18-L37)

### Common Build Configurations

The CI test scripts provide examples of different build configurations:

#### CPU-Only Build

From [tests/scripts/task_config_build_cpu.sh 19-60](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_cpu.sh#L19-L60):

#### GPU Build

From [tests/scripts/task_config_build_gpu.sh 19-56](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu.sh#L19-L56):

#### Minimal Build

From [tests/scripts/task_config_build_minimal.sh 19-36](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_minimal.sh#L19-L36):

**Sources:**[tests/scripts/task_config_build_cpu.sh 1-60](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_cpu.sh#L1-L60)[tests/scripts/task_config_build_gpu.sh 1-56](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu.sh#L1-L56)[tests/scripts/task_config_build_minimal.sh 1-36](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_minimal.sh#L1-L36)

### Build Options Reference

Key CMake options defined in [CMakeLists.txt 29-129](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L29-L129):

| Option | Values | Purpose |
| --- | --- | --- |
| `USE_CUDA` | `ON` / `OFF` / `/path/to/cuda` | Enable CUDA support |
| `USE_LLVM` | `ON` / `OFF` / `/path/to/llvm-config` | LLVM backend for CPU codegen |
| `USE_MICRO` | `ON` / `OFF` | MicroTVM for embedded targets |
| `USE_PROFILER` | `ON` / `OFF` | Enable runtime profiling |
| `USE_GRAPH_EXECUTOR` | `ON` / `OFF` | Static graph executor (default ON) |
| `USE_RPC` | `ON` / `OFF` | RPC runtime support (default ON) |
| `HIDE_PRIVATE_SYMBOLS` | `ON` / `OFF` | Symbol visibility for shared library |
| `USE_CCACHE` | `AUTO` / `ON` / `OFF` / `/path` | Compiler cache |

**Sources:**[CMakeLists.txt 29-129](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L29-L129)[cmake/config.cmake 1-441](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L1-L441)

## Development Environment Setup

### Docker-Based Development

The CI Docker images provide pre-configured development environments. The main images are:

To use a CI Docker image for development:

**Sources:**[docker/Dockerfile.ci_cpu 1-152](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cpu#L1-L152)[docker/Dockerfile.ci_gpu 1-172](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_gpu#L1-L172)[docker/Dockerfile.ci_lint 1-66](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_lint#L1-L66)

### Local Development Setup

For local development without Docker:

1.   **Install system dependencies** (Ubuntu/Debian):

 
2.   **Set up Python virtual environment**:

 
3.   **Configure and build**:

 
4.   **Set up Python path**:

 

**Sources:**[docker/install/ubuntu_install_core.sh 26-46](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_core.sh#L26-L46)[docker/install/ubuntu_install_python.sh 84-108](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python.sh#L84-L108)

## Running Tests

### Test Infrastructure

TVM uses pytest for Python tests and GoogleTest for C++ tests. The test execution is managed by [tests/scripts/setup-pytest-env.sh 1-98](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh#L1-L98)

**Sources:**[tests/scripts/setup-pytest-env.sh 1-98](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh#L1-L98)

### Running Python Tests

The `run_pytest` function in [tests/scripts/setup-pytest-env.sh 49-97](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh#L49-L97) provides the standard test execution interface:

The function automatically handles:

*   Test result collection in JUnit XML format
*   Parallel execution with pytest-xdist (`-n` flag)
*   Test reruns on failure (up to 3 times with `--reruns=3`)
*   Test sharding for distributed CI execution

**Sources:**[tests/scripts/setup-pytest-env.sh 49-97](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh#L49-L97)

### Running C++ Tests

C++ tests use GoogleTest and are built when `USE_GTEST` is enabled in [CMakeLists.txt 453-488](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L453-L488):

**Sources:**[CMakeLists.txt 729-767](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L729-L767)

### Linting and Code Quality

Code quality checks are run via [tests/lint/](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/) scripts:

| Tool | Script | Purpose |
| --- | --- | --- |
| flake8 | [tests/lint/flake8.sh 21](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/flake8.sh#L21-L21) | Python syntax errors |
| pylint | CI Docker | Python code quality |
| black | CI Docker | Python formatting |
| clang-format | CI scripts | C++ formatting |
| mypy | CI Docker | Python type checking |

Run linting locally:

**Sources:**[tests/lint/flake8.sh 1-22](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/flake8.sh#L1-L22)[tests/lint/check_cmake_options.py 1-80](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/check_cmake_options.py#L1-L80)[docker/Dockerfile.ci_lint 1-66](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_lint#L1-L66)

## Build System Details

### CMake Module Organization

The CMake build system is organized into modular components:

Modules are included in [CMakeLists.txt 518-561](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L518-L561):

*   Backend modules: `CUDA.cmake`, `LLVM.cmake`, `ROCM.cmake`, `Vulkan.cmake`, `Metal.cmake`
*   Feature modules: `Micro.cmake`, `VTA.cmake`, `StandaloneCrt.cmake`
*   Contrib modules: `contrib/DNNL.cmake`, `contrib/CUTLASS.cmake`, `contrib/TensorRT.cmake`

**Sources:**[CMakeLists.txt 518-561](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L518-L561)[cmake/modules/CUDA.cmake 1-98](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/CUDA.cmake#L1-L98)[cmake/utils/FindCUDA.cmake 1-144](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/utils/FindCUDA.cmake#L1-L144)

### Build Introspection System

TVM includes a runtime introspection system to query build configuration. This is implemented in [src/support/libinfo.cc 1-373](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/support/libinfo.cc#L1-L373) and exposed through [cmake/modules/LibInfo.cmake 1-142](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/LibInfo.cmake#L1-L142)

The `add_lib_info` function in [cmake/modules/LibInfo.cmake 21-141](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/LibInfo.cmake#L21-L141) injects CMake variables as C++ preprocessor definitions into `libinfo.cc`. At runtime, Python code can query these via [python/tvm/support.py 80-103](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/support.py#L80-L103):

**Sources:**[src/support/libinfo.cc 262-368](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/support/libinfo.cc#L262-L368)[cmake/modules/LibInfo.cmake 21-141](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/LibInfo.cmake#L21-L141)[python/tvm/support.py 80-103](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/support.py#L80-L103)

### Compiler Cache Integration

TVM supports ccache and sccache for faster rebuilds. Configuration in [CMakeLists.txt 501-504](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L501-L504):

For CI builds, sccache is installed via [docker/install/ubuntu_install_sccache.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_sccache.sh) and configured in build scripts like [tests/scripts/task_config_build_hexagon.sh 32-39](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_hexagon.sh#L32-L39)

**Sources:**[CMakeLists.txt 501-504](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L501-L504)[docker/install/ubuntu_install_sccache.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_sccache.sh)[tests/scripts/task_config_build_hexagon.sh 32-39](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_hexagon.sh#L32-L39)

## CI/CD Integration

### Jenkins Pipeline Generation

The CI system uses generated Jenkinsfiles from Jinja2 templates. The generation is handled by [ci/jenkins/generate.py 1-202](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py#L1-L202):

To regenerate Jenkinsfiles:

To check if Jenkinsfiles are up to date:

The system detects "images-only" changes (Docker image tag updates) in [ci/jenkins/generate.py 61-102](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py#L61-L102) and preserves timestamps to avoid unnecessary CI rebuilds.

**Sources:**[ci/jenkins/generate.py 1-202](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py#L1-L202)

### Docker Image Management

Docker images are versioned and built in the `ci-docker-staging` branch. Each Dockerfile specifies its dependencies:

Each Docker image includes a Python virtual environment at `/venv/apache-tvm-py3.8` created by [docker/install/ubuntu_install_python.sh 84-113](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python.sh#L84-L113) with packages from [docker/install/ubuntu_install_python_package.sh 24-48](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python_package.sh#L24-L48)

**Sources:**[docker/Dockerfile.ci_cpu 1-152](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cpu#L1-L152)[docker/Dockerfile.ci_gpu 20-172](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_gpu#L20-L172)[docker/install/ubuntu_install_python.sh 84-113](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python.sh#L84-L113)

## Development Workflow Best Practices

### Iterative Development Cycle

### Debug Builds

For development, use debug builds with assertions enabled:

This configuration:

*   Enables `USE_RELAY_DEBUG` for additional checks in [CMakeLists.txt 646-658](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L646-L658)
*   Builds with debug symbols (`-g -O0`)
*   Enables libbacktrace for stack traces [CMakeLists.txt 76](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L76-L76)
*   Installs signal handler for segfaults [CMakeLists.txt 77](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L77-L77)

**Sources:**[CMakeLists.txt 646-658](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L646-L658)[tests/scripts/task_config_build_minimal.sh 26-34](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_minimal.sh#L26-L34)

### Profiling Support

Enable profiling for performance analysis:

PAPI support is installed via [docker/install/ubuntu_install_papi.sh 1-36](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_papi.sh#L1-L36) and enables hardware performance counter access. For more details on using PAPI with TVM, see [docs/how_to/profile/papi.rst 1-135](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docs/how_to/profile/papi.rst#L1-L135)

**Sources:**[CMakeLists.txt 419-429](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L419-L429)[docker/install/ubuntu_install_papi.sh 28-35](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_papi.sh#L28-L35)[docs/how_to/profile/papi.rst 19-30](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docs/how_to/profile/papi.rst#L19-L30)

### Symbol Visibility

For production builds or when creating Python wheels, hide private symbols:

This is configured in [CMakeLists.txt 69](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L69-L69) and sets `-fvisibility=hidden` compiler flag in [CMakeLists.txt 216-220](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L216-L220) A separate `tvm_allvisible` target is created for testing in [CMakeLists.txt 711-725](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L711-L725)

**Sources:**[CMakeLists.txt 216-220](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L216-L220)[CMakeLists.txt 711-725](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L711-L725)

## Common Development Tasks

### Adding Support for New Hardware

For adding a new backend target, see [Adding Backend Support](https://deepwiki.com/tenstorrent/tt-tvm/11.4-adding-backend-support). The typical pattern involves:

1.   Add target detection in `cmake/utils/Find<Target>.cmake`
2.   Create `cmake/modules/<Target>.cmake` for source files and linking
3.   Implement code generator in `src/target/source/<target>/`
4.   Add runtime support in `src/runtime/<target>/`
5.   Register target in `src/target/target_kind.cc`

**Example:** CUDA support is implemented across:

*   Detection: [cmake/utils/FindCUDA.cmake 41-143](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/utils/FindCUDA.cmake#L41-L143)
*   Module: [cmake/modules/CUDA.cmake 19-98](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/CUDA.cmake#L19-L98)
*   Codegen: `src/target/source/codegen_cuda.cc`
*   Runtime: `src/runtime/cuda/*.cc`

### Adding Frontend Support

For adding a new ML framework frontend, see [Adding a Frontend Converter](https://deepwiki.com/tenstorrent/tt-tvm/11.2-adding-a-frontend-converter). The pattern involves:

1.   Create converter in `python/tvm/relay/frontend/<framework>.py`
2.   Implement operator mapping from framework ops to Relay ops
3.   Handle framework-specific features (control flow, dynamic shapes)
4.   Add tests in `tests/python/frontend/<framework>/`
5.   Add CI integration in Docker images

**Example:** ONNX frontend structure:

*   Main converter: `python/tvm/relay/frontend/onnx.py`
*   Common utilities: `python/tvm/relay/frontend/common.py`
*   Tests: `tests/python/frontend/onnx/`
*   CI dependencies: [docker/install/ubuntu_install_onnx.sh 29-41](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_onnx.sh#L29-L41)

### Testing Changes

Before submitting a PR:

1.   **Run relevant unit tests:**

 
2.   **Run integration tests:**

 
3.   **Check code formatting:**

 
4.   **Build and test C++ changes:**

 
5.   **Test with CI Docker image** (recommended):

 

**Sources:**[tests/scripts/setup-pytest-env.sh 49-97](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh#L49-L97)[tests/lint/flake8.sh 21](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/flake8.sh#L21-L21)

## Troubleshooting

### Common Build Issues

| Issue | Solution |
| --- | --- |
| CMake cannot find LLVM | Specify path: `set(USE_LLVM /path/to/llvm-config)` |
| Out of memory during build | Reduce parallelism: `make -j4` instead of `make -j` |
| CUDA not found | Set `CUDA_TOOLKIT_ROOT_DIR` or use `USE_CUDA=/path/to/cuda` |
| Python import errors | Check `PYTHONPATH` includes `${TVM_HOME}/python` |
| Symbol visibility errors | Try `set(HIDE_PRIVATE_SYMBOLS OFF)` for development |

### CMake Cache Issues

If changing options doesn't take effect:

### Docker Build Issues

For Docker build failures:

*   Check disk space: Docker layers can be large (20GB+)
*   Prune unused images: `docker system prune -a`
*   Build with no cache: `docker build --no-cache`

**Sources:**[CMakeLists.txt 17-28](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L17-L28)[cmake/utils/FindCUDA.cmake 41-59](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/utils/FindCUDA.cmake#L41-L59)[cmake/utils/FindLLVM.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/utils/FindLLVM.cmake)

* * *

For detailed guides on specific development tasks:

*   Setting up a complete development environment: [Setting Up Development Environment](https://deepwiki.com/tenstorrent/tt-tvm/11.1-setting-up-development-environment)
*   Adding support for a new ML framework: [Adding a Frontend Converter](https://deepwiki.com/tenstorrent/tt-tvm/11.2-adding-a-frontend-converter)
*   Implementing new operators: [Adding Relay Operators](https://deepwiki.com/tenstorrent/tt-tvm/11.3-adding-relay-operators)
*   Adding support for new hardware: [Adding Backend Support](https://deepwiki.com/tenstorrent/tt-tvm/11.4-adding-backend-support)
*   Writing and running tests: [Writing Tests](https://deepwiki.com/tenstorrent/tt-tvm/11.5-writing-tests)

Dismiss
Refresh this wiki

Enter email to refresh