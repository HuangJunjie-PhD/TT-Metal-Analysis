---
project: tt-tvm
github: https://github.com/tenstorrent/tt-tvm
deepwiki: https://deepwiki.com/tenstorrent/tt-tvm/10-cicd-infrastructure
---

# CI/CD Infrastructure

Relevant source files
*   [apps/microtvm/reference-vm/base-box/base_box_provision.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/apps/microtvm/reference-vm/base-box/base_box_provision.sh)
*   [apps/microtvm/reference-vm/rebuild_tvm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/apps/microtvm/reference-vm/rebuild_tvm.sh)
*   [ci/jenkins/generate.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py)
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
*   [tests/lint/flake8.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/flake8.sh)
*   [tests/scripts/setup-pytest-env.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh)
*   [tests/scripts/task_config_build_minimal.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_minimal.sh)

## Purpose and Scope

This document describes TVM's continuous integration and deployment infrastructure, including Docker-based CI environments, Jenkins pipeline generation, Python environment management, and the testing framework. This infrastructure ensures code quality across diverse hardware targets (CPU, GPU, ARM, RISC-V, Hexagon, Cortex-M, WASM) and supports automated testing, building, and deployment workflows.

For information about the CMake build system configuration, see [Build System](https://deepwiki.com/tenstorrent/tt-tvm/9-build-system). For details about specific hardware target configuration, see [Target-Specific Configuration](https://deepwiki.com/tenstorrent/tt-tvm/9.3-target-specific-configuration).

* * *

## High-Level CI/CD Architecture

The CI/CD infrastructure is organized around specialized Docker images, a template-based Jenkins pipeline system, and a pytest-based testing framework with sharding support.

**Sources:**

*   [docker/Dockerfile.ci_cpu 1-152](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cpu#L1-L152)
*   [docker/Dockerfile.ci_gpu 1-172](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_gpu#L1-L172)
*   [docker/Dockerfile.ci_lint 1-66](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_lint#L1-L66)
*   [ci/jenkins/generate.py 1-202](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py#L1-L202)
*   [tests/scripts/setup-pytest-env.sh 1-98](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh#L1-L98)

* * *

## Docker CI Environment Matrix

TVM maintains multiple specialized Docker images for different testing scenarios. Each image is based on Ubuntu 22.04 (or Ubuntu 20.04 for i386) and includes specific toolchains and dependencies.

### Environment Comparison Table

| Image | Base | Primary Purpose | Key Dependencies |
| --- | --- | --- | --- |
| `ci_cpu` | ubuntu:22.04 | CPU testing, x86/ARM compilation | LLVM, DNNL, TensorFlow, PyTorch, ONNX, MXNet |
| `ci_gpu` | nvidia/cuda:11.8.0-cudnn8 | GPU testing, CUDA/ROCm | CUDA 11.8, cuDNN, ROCm, Vulkan, TensorRT libraries |
| `ci_lint` | ubuntu:22.04 | Code linting and formatting | pylint, mypy, black, clang-format, shellcheck |
| `ci_arm` | ubuntu:22.04 | ARM/AArch64 testing | ARM Compute Library, AArch64 toolchains |
| `ci_hexagon` | tvmcihexagon/ci-hexagon-base | Hexagon DSP testing | Hexagon SDK 4.5.0.3, Android NDK |
| `ci_i386` | i386/ubuntu:20.04 | 32-bit x86 testing | LLVM, 32-bit toolchains |
| `ci_cortexm` | ubuntu:22.04 | Microcontroller testing | Zephyr, FreeRTOS, Arduino, CMSIS, Ethos-U NPU |
| `ci_wasm` | ubuntu:22.04 | WebAssembly testing | Emscripten 3.1.30, wasmtime |
| `ci_riscv` | ubuntu:22.04 | RISC-V testing | RISC-V toolchains, QEMU, Spike simulator |
| `ci_minimal` | ubuntu:22.04 | Minimal cross-compilation | AArch64 cross-compile, minimal LLVM |

**Sources:**

*   [docker/Dockerfile.ci_cpu 18-19](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cpu#L18-L19)
*   [docker/Dockerfile.ci_gpu 18-20](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_gpu#L18-L20)
*   [docker/Dockerfile.ci_lint 18-21](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_lint#L18-L21)
*   [docker/Dockerfile.ci_arm 18-19](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_arm#L18-L19)
*   [docker/Dockerfile.ci_hexagon 18-20](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_hexagon#L18-L20)
*   [docker/Dockerfile.ci_i386 18-21](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_i386#L18-L21)
*   [docker/Dockerfile.ci_cortexm 18-19](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cortexm#L18-L19)
*   [docker/Dockerfile.ci_wasm 17](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_wasm#L17-L17)
*   [docker/Dockerfile.ci_riscv 18-19](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_riscv#L18-L19)
*   [docker/Dockerfile.ci_minimal 18-19](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_minimal#L18-L19)

### Common Docker Build Pattern

All Docker images follow a consistent build pattern:

**Sources:**

*   [docker/Dockerfile.ci_cpu 19-152](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cpu#L19-L152)
*   [docker/install/ubuntu_setup_tz.sh 1-26](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_setup_tz.sh#L1-L26)
*   [docker/install/ubuntu_install_core.sh 1-47](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_core.sh#L1-L47)
*   [docker/install/ubuntu_install_cmake_source.sh 1-40](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_cmake_source.sh#L1-L40)

* * *

## Python Virtual Environment Management

### Environment Structure

TVM uses a standardized Python virtual environment across all CI images:

*   **Environment variable:**`TVM_VENV=/venv/apache-tvm-py3.8`
*   **Python version:** 3.8 (specified in Dockerfile)
*   **PATH priority:** Virtual environment bin directory is prepended to PATH
*   **User site packages:** Disabled via `PYTHONNOUSERSITE=1` to prevent contamination

**Sources:**

*   [docker/Dockerfile.ci_cpu 37-42](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cpu#L37-L42)
*   [docker/install/ubuntu_install_python.sh 24-124](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python.sh#L24-L124)
*   [docker/install/ubuntu_install_python_package.sh 1-49](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python_package.sh#L1-L49)

### Dependency Pinning with SHA256 Hashes

The Python environment uses strict dependency pinning with SHA256 hash verification for reproducibility and security:

**Constraint file location:**`python/bootstrap/lockfiles/constraints-3.8.txt`

**Installation process:**

1.   Base pip upgrade with hash verification: [docker/install/ubuntu_install_python.sh 98-100](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python.sh#L98-L100)
2.   Bootstrap packages installed with `--require-hashes` flag: [docker/install/ubuntu_install_python.sh 106-108](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python.sh#L106-L108)
3.   Project dependencies installed without hashes (development packages): [docker/install/ubuntu_install_python_package.sh 24-48](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python_package.sh#L24-L48)

**Key Python packages installed:**

| Category | Packages |
| --- | --- |
| Core Scientific | numpy==1.21.*, scipy, Pillow==9.1.0, ml_dtypes |
| Testing | pytest, pytest-xdist, pytest-rerunfailures==10.2, pytest-profiling, pytest-lazy-fixture |
| Code Quality | mypy, pylint, black==22.12.0, flake8==3.9.2 |
| ML Frameworks | tensorflow==2.9.1, keras==2.9, torch==2.0.0, onnx==1.12.0 |
| Build Tools | cython, Jinja2, cloudpickle, decorator |

**Sources:**

*   [docker/install/ubuntu_install_python_package.sh 24-48](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python_package.sh#L24-L48)
*   [docker/install/ubuntu_install_tensorflow.sh 23-25](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_tensorflow.sh#L23-L25)
*   [docker/install/ubuntu_install_onnx.sh 29-41](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_onnx.sh#L29-L41)

### Permission Management

The virtual environment uses ACLs (Access Control Lists) to allow multiple users to modify packages:

```
addgroup tvm-venv
chgrp -R tvm-venv "${TVM_VENV}"
setfacl -R -d -m group:tvm-venv:rwx "${TVM_VENV}"
setfacl -R -m group:tvm-venv:rwx "${TVM_VENV}"
```

**Sources:**[docker/install/ubuntu_install_python.sh 110-113](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_python.sh#L110-L113)

* * *

## Jenkins Pipeline Generation System

### Template-Based Pipeline Architecture

TVM uses a Jinja2 template system to generate Jenkinsfiles from templates, enabling maintainable CI configuration across multiple pipelines.

**Sources:**

*   [ci/jenkins/generate.py 1-202](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py#L1-L202)
*   [ci/jenkins/generate.py 34-38](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py#L34-L38)

### Generator Implementation Details

**Key classes and functions:**

*   `Change` enumeration: `IMAGES_ONLY`, `NONE`, `FULL` - [ci/jenkins/generate.py 41-44](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py#L41-L44)
*   `ChangeData` dataclass: Stores diff, content, destination, source paths - [ci/jenkins/generate.py 47-52](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py#L47-L52)
*   `change_type()`: Analyzes diffs to detect if only Docker image tags changed - [ci/jenkins/generate.py 61-102](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py#L61-L102)
*   `update_jenkinsfile()`: Renders template and generates change data - [ci/jenkins/generate.py 105-153](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py#L105-L153)

**Timestamp preservation logic:**

When only Docker image names change (e.g., `ci_cpu = 'v0.60'` → `ci_cpu = 'v0.61'`), the generator preserves the original timestamp to avoid spurious changes:

**Sources:**[ci/jenkins/generate.py 146-149](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py#L146-L149)

### Command-Line Interface

**Generate pipelines:**

**Verify pipelines are up-to-date (CI check):**

**Force timestamp update:**

**Sources:**[ci/jenkins/generate.py 156-201](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/ci/jenkins/generate.py#L156-L201)

* * *

## Testing Framework

### Pytest Environment Setup

The testing framework is configured via `setup-pytest-env.sh`, which establishes environment variables and helper functions for running tests.

**Sources:**

*   [tests/scripts/setup-pytest-env.sh 1-98](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh#L1-L98)
*   [tests/scripts/setup-pytest-env.sh 22-26](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh#L22-L26)
*   [tests/scripts/setup-pytest-env.sh 49-97](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh#L49-L97)

### run_pytest Function

The `run_pytest` function is the core test execution wrapper with the following features:

**Function signature:**

**Parameters:**

*   `FFI_TYPE`: Either "cython" or "ctypes" - determines which TVM FFI binding to use
*   `TEST_SUITE_NAME`: Name of the test suite for reporting
*   Additional arguments passed directly to pytest

**Key features:**

1.   **Sharding support:** Honors `TVM_SHARD_INDEX` environment variable for distributed testing
2.   **Flaky test handling:** Automatically adds `--reruns=3` if pytest-rerunfailures is installed
3.   **Parallel execution:** Sets `-n=$DEFAULT_PARALLELISM` if not specified
4.   **JUnit reporting:** Generates XML reports in `${TVM_PYTEST_RESULT_DIR}/${suite_name}.xml`
5.   **Error aggregation:** Collects all failures and reports them at the end via cleanup trap

**Example invocation:**

**Suite name format:**`${TEST_SUITE_NAME}-${current_shard}-${FFI_TYPE}`

*   Example: `unittest-shard-0-cython`
*   No shard: `unittest-no-shard-cython`

**Sources:**

*   [tests/scripts/setup-pytest-env.sh 49-97](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh#L49-L97)
*   [tests/scripts/setup-pytest-env.sh 64-76](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh#L64-L76)

### Error Reporting and Cleanup

Test errors are accumulated and reported at exit via a trap:

**Sources:**[tests/scripts/setup-pytest-env.sh 34-47](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/setup-pytest-env.sh#L34-L47)

* * *

## Hardware-Specific CI Configurations

### GPU Environment (ci_gpu)

The GPU environment supports CUDA, ROCm, and Vulkan:

**Base image:**`nvidia/cuda:11.8.0-cudnn8-devel-ubuntu22.04`

**Key dependencies:**

*   CUDA 11.8 with cuDNN 8
*   ROCm for AMD GPUs: [docker/Dockerfile.ci_gpu 76-77](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_gpu#L76-L77)
*   Vulkan SDK 1.2.135: [docker/Dockerfile.ci_gpu 111-112](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_gpu#L111-L112)
*   OpenCL: [docker/Dockerfile.ci_gpu 58-59](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_gpu#L58-L59)

**PAPI profiling support:**

This enables hardware performance counters for CUDA and ROCm devices.

**Environment variables:**

**Sources:**

*   [docker/Dockerfile.ci_gpu 20](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_gpu#L20-L20)
*   [docker/Dockerfile.ci_gpu 110-172](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_gpu#L110-L172)
*   [docker/install/ubuntu_install_papi.sh 32-35](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_papi.sh#L32-L35)

### Embedded Targets: Cortex-M Environment (ci_cortexm)

The Cortex-M environment supports microcontroller development with multiple RTOSes:

**RTOS and framework support:**

*   **Zephyr RTOS:** Version 3.2-branch - [docker/install/ubuntu_init_zephyr_project.sh 54](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_init_zephyr_project.sh#L54-L54)
*   **FreeRTOS:**[docker/Dockerfile.ci_cortexm 94-96](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cortexm#L94-L96)
*   **Arduino:** CLI version 0.21.1 with multiple cores - [docker/Dockerfile.ci_cortexm 98-105](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cortexm#L98-L105)

**NPU and library support:**

*   **CMSIS-NN:** ARM's neural network library - [docker/Dockerfile.ci_cortexm 111-114](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cortexm#L111-L114)
*   **Arm Ethos-U NPU:** Driver stack for NPU acceleration - [docker/Dockerfile.ci_cortexm 116-118](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cortexm#L116-L118)
*   **Vela compiler:** Neural network compiler for Ethos-U - [docker/Dockerfile.ci_cortexm 120-122](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cortexm#L120-L122)

**Environment variables:**

**Sources:**

*   [docker/Dockerfile.ci_cortexm 1-126](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cortexm#L1-L126)
*   [docker/install/ubuntu_install_zephyr.sh 1-84](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_zephyr.sh#L1-L84)
*   [docker/install/ubuntu_install_arduino.sh 1-37](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_arduino.sh#L1-L37)

### Embedded Targets: Hexagon DSP Environment (ci_hexagon)

The Hexagon environment provides support for Qualcomm Hexagon DSP:

**Base image:**`tvmcihexagon/ci-hexagon-base:v0.02_SDK4.5.0.3`

**Key components:**

*   Hexagon SDK 4.5.0.3: [docker/Dockerfile.ci_hexagon 70](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_hexagon#L70-L70)
*   Hexagon Tools 8.5.08: [docker/Dockerfile.ci_hexagon 74](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_hexagon#L74-L74)
*   Android SDK and NDK: [docker/Dockerfile.ci_hexagon 61-65](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_hexagon#L61-L65)

**Environment variables:**

**Sources:**

*   [docker/Dockerfile.ci_hexagon 1-96](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_hexagon#L1-L96)
*   [docker/install/ubuntu_install_androidsdk.sh 1-115](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_androidsdk.sh#L1-L115)

### RISC-V Environment (ci_riscv)

The RISC-V environment includes multiple toolchains and simulators:

**Toolchain support:**

*   **Linux toolchain:** RISC-V 64-bit for Linux targets - [docker/Dockerfile.ci_riscv 94-96](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_riscv#L94-L96)
*   **Bare-metal toolchain:** RISC-V 64-bit ELF for embedded - [docker/Dockerfile.ci_riscv 98-100](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_riscv#L98-L100)

**Simulators:**

*   **QEMU:** Xuantie RISC-V QEMU - [docker/Dockerfile.ci_riscv 102-104](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_riscv#L102-L104)
*   **Spike:** RISC-V ISA simulator with proxy kernel - [docker/Dockerfile.ci_riscv 110-112](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_riscv#L110-L112)

**Compute library:**

*   **CSI-NN2:** C-SKY neural network library - [docker/Dockerfile.ci_riscv 106-108](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_riscv#L106-L108)

**Environment PATH additions:**

**Sources:**

*   [docker/Dockerfile.ci_riscv 1-119](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_riscv#L1-L119)

### WebAssembly Environment (ci_wasm)

The WASM environment uses Emscripten for WebAssembly compilation:

**Emscripten version:** 3.1.30

**Installation:**

**Environment variables:**

**Sources:**

*   [docker/Dockerfile.ci_wasm 1-74](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_wasm#L1-L74)
*   [docker/install/ubuntu_install_emscripten.sh 23-27](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/install/ubuntu_install_emscripten.sh#L23-L27)

* * *

## Linting and Code Quality (ci_lint)

The lint environment provides comprehensive code quality checks:

**Python linting:**

*   `pylint` for comprehensive Python style checking
*   `mypy` for static type checking
*   `black` for code formatting
*   `flake8` for basic syntax and style
*   `blocklint` for blocklist checking

**C++ linting:**

*   `clang-format` for C++ formatting
*   `cpplint` for C++ style checking
*   `doxygen` for documentation generation

**Shell linting:**

*   `shellcheck` for shell script analysis

**License checking:**

*   Apache RAT (Release Audit Tool) for license compliance

**Example flake8 invocation:**

**Locale settings:** The environment sets `LC_ALL=C.UTF-8` and `LANG=C.UTF-8` to prevent black command line errors.

**Sources:**

*   [docker/Dockerfile.ci_lint 1-66](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_lint#L1-L66)
*   [docker/Dockerfile.ci_lint 41](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_lint#L41-L41)
*   [docker/Dockerfile.ci_lint 63-65](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_lint#L63-L65)
*   [tests/lint/flake8.sh 21](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/flake8.sh#L21-L21)

* * *

## Build Acceleration: sccache

Most CI environments include `sccache` (Shared Compilation Cache) for faster builds:

**Installation:**

sccache provides distributed compilation caching, significantly reducing build times in CI by caching compilation artifacts.

**Sources:**

*   [docker/Dockerfile.ci_cpu 140-143](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_cpu#L140-L143)
*   [docker/Dockerfile.ci_gpu 148-151](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/docker/Dockerfile.ci_gpu#L148-L151)

* * *

## Integration with Build System

The CI infrastructure integrates with TVM's CMake build system through configuration scripts. Example minimal build configuration:

**Sources:**[tests/scripts/task_config_build_minimal.sh 19-36](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_minimal.sh#L19-L36)

* * *

## Summary

The CI/CD infrastructure provides:

1.   **10+ specialized Docker environments** covering CPU, GPU, ARM, RISC-V, Hexagon, Cortex-M, WASM, and minimal configurations
2.   **Reproducible Python environment** with SHA256-hashed dependencies in virtual environments
3.   **Template-based Jenkins pipeline generation** with smart change detection
4.   **Comprehensive testing framework** with pytest, sharding, flaky test handling, and JUnit reporting
5.   **Code quality tools** for Python, C++, shell scripts, and Rust
6.   **Build acceleration** via sccache
7.   **Hardware-specific configurations** for diverse deployment targets from cloud GPUs to embedded microcontrollers

This infrastructure enables TVM to maintain high code quality across its entire compilation pipeline and diverse hardware ecosystem.

Dismiss
Refresh this wiki

Enter email to refresh