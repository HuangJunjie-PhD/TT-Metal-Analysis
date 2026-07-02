---
project: tt-torch
github: https://github.com/tenstorrent/tt-torch
deepwiki: https://deepwiki.com/tenstorrent/tt-torch/4-building-and-testing
---

# Building and Testing

Relevant source files
*   [.github/Dockerfile.base](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/Dockerfile.base)
*   [.github/Dockerfile.ci](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/Dockerfile.ci)
*   [.github/build-docker-images.sh](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/build-docker-images.sh)
*   [.github/get-docker-tag.sh](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/get-docker-tag.sh)
*   [.github/workflows/build-image.yml](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/build-image.yml)
*   [.github/workflows/on-pr.yml](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/on-pr.yml)
*   [.github/workflows/on-push.yml](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/on-push.yml)
*   [.github/workflows/perf-benchmark.yml](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/perf-benchmark.yml)
*   [.github/workflows/run-build.yml](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-build.yml)
*   [.github/workflows/run-tests.yml](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tests.yml)
*   [.github/workflows/spdx.yml](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/spdx.yml)
*   [env/activate](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/env/activate)
*   [requirements.txt](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt)
*   [setup.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py)
*   [tt_torch/__init__.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/__init__.py)
*   [tt_torch/csrc/CMakeLists.txt](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/CMakeLists.txt)
*   [tt_torch/tools/profile_util.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/profile_util.py)

This document provides an overview of tt-torch's build system, testing infrastructure, and CI/CD pipelines. The build system uses scikit-build to integrate Python packaging with CMake for C++ components and external dependencies. The testing framework validates functionality across multiple hardware architectures (wormhole_b0, blackhole, n300) through unit tests, model tests, and performance benchmarks. The CI/CD pipeline orchestrates Docker image builds, artifact management, and test execution through GitHub Actions workflows.

For detailed information on specific testing components, see:

*   [Build System](https://deepwiki.com/tenstorrent/tt-torch/4.1-build-system) - CMake configuration, dependency management, and wheel building
*   [Testing Framework](https://deepwiki.com/tenstorrent/tt-torch/4.2-testing-framework) - ModelTester classes and pytest integration
*   [Model Testing Framework](https://deepwiki.com/tenstorrent/tt-torch/4.2.1-model-testing-framework) - Test parameterization and verification metrics
*   [Test Configuration and Management](https://deepwiki.com/tenstorrent/tt-torch/4.2.2-test-configuration-and-management) - test_config.py structure and ModelStatus
*   [Hardware Matrix Testing](https://deepwiki.com/tenstorrent/tt-torch/4.2.3-hardware-matrix-testing) - Multi-architecture test execution
*   [Performance Benchmarking](https://deepwiki.com/tenstorrent/tt-torch/4.2.4-performance-benchmarking) - Profiling tools and Tracy integration
*   [CI/CD Pipeline](https://deepwiki.com/tenstorrent/tt-torch/4.3-cicd-pipeline) - GitHub Actions workflows and Docker infrastructure

## Build System Architecture

The tt-torch build system integrates Python packaging with CMake through scikit-build. The [setup.py 1-234](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L1-L234) entry point orchestrates the build process, compiling C++ bindings, external dependencies (tt-xla, tt-mlir, torch-mlir), and packaging the final wheel distribution.

### Build System Overview

The build process starts with environment setup through [env/activate 1-52](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/env/activate#L1-L52) which configures Python 3.11, virtual environment, and required library paths. The [setup.py 4-234](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L4-L234) script uses scikit-build to integrate Python packaging with CMake, compiling C++ components and bundling dependencies into a wheel distribution.

**Sources:**`setup.py`, `env/activate`, `tt_torch/csrc/CMakeLists.txt`, `third_party/CMakeLists.txt`

### Key Build Components

| Component | Purpose | Key Files |
| --- | --- | --- |
| **setup.py** | Main build orchestration using scikit-build | [setup.py 4-234](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L4-L234) |
| **install_metal_libs** | Custom installer for tt-metal tools, profiling binaries, and libraries | [setup.py 62-147](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L62-L147) |
| **build_py_with_torch_mlir** | Custom build command to include torch-mlir packages in wheel | [setup.py 17-60](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L17-L60) |
| **tt_torch/csrc/CMakeLists.txt** | C++ compilation for MLIR interface and pybind11 module | [tt_torch/csrc/CMakeLists.txt 1-90](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/CMakeLists.txt#L1-L90) |
| **env/activate** | Environment setup and dependency configuration | [env/activate 1-52](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/env/activate#L1-L52) |

### Build Configuration Arguments

The build system supports multiple compilation modes through command-line arguments passed to `setup.py`:

| Argument | CMake Flag | Purpose |
| --- | --- | --- |
| `--code_coverage` | `-DCODE_COVERAGE=ON` | Enables code coverage instrumentation [setup.py 167-171](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L167-L171) |
| `--build_perf` | `-DTT_RUNTIME_ENABLE_PERF_TRACE=ON` | Enables Tracy profiling and performance tracing [setup.py 173-182](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L173-L182) |
| `--build_runtime_debug` | `-DTT_RUNTIME_DEBUG=ON` | Enables runtime debugging hooks [setup.py 184-188](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L184-L188) |
| `--build_op_model` | `-DTTMLIR_ENABLE_OPMODEL=ON` | Enables op model functionality [setup.py 190-193](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L190-L193) |
| `--include-models` | N/A | Includes tt-forge-models in wheel for CI builds [setup.py 196-199](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L196-L199) |

The CI/CD pipeline uses different build configurations for different purposes:

*   PR builds: `--code_coverage --include-models --build_perf`[.github/workflows/on-pr.yml 107](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/on-pr.yml#L107-L107)
*   Main branch builds: Same as PR builds plus a release build with `--build_perf` only [.github/workflows/on-push.yml 22-29](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/on-push.yml#L22-L29)
*   Performance benchmarks: `--include-models --build_perf`[.github/workflows/perf-benchmark.yml 46](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/perf-benchmark.yml#L46-L46)

**Sources:**`setup.py`, `tt_torch/csrc/CMakeLists.txt`, `env/activate`, `.github/workflows/on-pr.yml`, `.github/workflows/on-push.yml`

## Testing Infrastructure

The testing infrastructure validates tt-torch across multiple layers: unit tests for core functionality, model tests for end-to-end validation, and performance benchmarks for hardware optimization. The framework uses pytest with custom fixtures and configuration management through `test_config.py`.

### Test Organization and Execution

The testing infrastructure is organized into several categories, each with specific purposes and execution patterns. The [tests/runner/test_models.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/runner/test_models.py) file implements the main model testing framework using the `ModelTester` base class, while [test_config.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/test_config.py) centralizes model configuration including expected status, tolerances, and hardware-specific overrides.

**Sources:**`.github/workflows/run-tests.yml`, `requirements.txt`

### Test Execution Matrix

The testing framework executes multiple test categories with specific configurations:

| Test Type | Command | Purpose | Configuration |
| --- | --- | --- | --- |
| **PyTorch Unit Tests** | `pytest -v tests/torch --junit-xml=report_torch_*.xml` | Core backend and executor tests | [.github/workflows/run-tests.yml 116-123](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tests.yml#L116-L123) |
| **ONNX Unit Tests** | `pytest -v tests/onnx --junit-xml=report_onnx_*.xml` | ONNX compilation pipeline tests | [.github/workflows/run-tests.yml 132-138](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tests.yml#L132-L138) |
| **Config Validation** | `pytest -vv tests/runner/test_models.py --validate-test-config` | Validate test_config.py structure | [.github/workflows/run-tests.yml 94-98](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tests.yml#L94-L98) |
| **Model Tests** | `pytest -vv tests/runner/test_models.py -m expected_passing -k "pattern"` | Execute specific model with filters | [.github/workflows/run-tests.yml 100-108](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tests.yml#L100-L108) |
| **Op-by-Op Torch** | `TT_TORCH_CHECK_ALL_OPS_EXECUTE=1 pytest --op_by_op_torch` | Per-operation validation against PyTorch | [.github/workflows/run-tests.yml 147-151](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tests.yml#L147-L151) |
| **Op-by-Op StableHLO** | `pytest --op_by_op_stablehlo` | Per-operation validation at StableHLO level | [.github/workflows/run-tests.yml 153-157](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tests.yml#L153-L157) |
| **CI Matrix Verification** | `python tt_torch/tools/ci_verification.py --scan-workflows` | Verify workflow consistency | [.github/workflows/run-tests.yml 110-114](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tests.yml#L110-L114) |
| **Demo Scripts** | `python demos/resnet/resnet50_demo.py` | End-to-end demo execution | [.github/workflows/run-tests.yml 159-163](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tests.yml#L159-L163) |

### Test Splitting and Parallelization

The CI system uses `pytest-split` to distribute tests across multiple runners:

*   Model tests are split based on test duration using `--splits` and `--group` arguments
*   Unit tests run on a single runner with `pytest-xdist` for parallel execution within the runner
*   Hardware-specific tests are routed to appropriate runners based on `runs-on` labels

**Sources:**`.github/workflows/run-tests.yml`, `requirements.txt`

## CI/CD Pipeline

The CI/CD system orchestrates Docker image builds, artifact management, and test execution through GitHub Actions workflows. The pipeline supports multiple trigger mechanisms (PR, push, workflow_dispatch, schedule) and routes builds to appropriate hardware runners.

### Primary CI/CD Workflows

The workflow architecture uses job dependencies to ensure proper ordering: `pre-commit` and `spdx` validation run first [.github/workflows/on-pr.yml 23-30](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/on-pr.yml#L23-L30) followed by Docker image building [.github/workflows/on-pr.yml 31-36](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/on-pr.yml#L31-L36) then main build [.github/workflows/on-pr.yml 99-107](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/on-pr.yml#L99-L107) and finally parallel test execution [.github/workflows/on-pr.yml 108-128](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/on-pr.yml#L108-L128)

**Sources:**`.github/workflows/on-pr.yml`, `.github/workflows/on-push.yml`, `.github/workflows/run-build.yml`, `.github/workflows/run-tests.yml`

### Workflow Orchestration Details

| Workflow | Trigger | Purpose | Key Jobs |
| --- | --- | --- | --- |
| **on-pr.yml** | pull_request (opened, synchronize) | PR validation | pre-commit, spdx, docker-build, build, test, full-model-test [.github/workflows/on-pr.yml 1-146](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/on-pr.yml#L1-L146) |
| **on-push.yml** | push to main | Main branch validation | docker-build, build, build-release, test, full-model-test [.github/workflows/on-push.yml 1-51](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/on-push.yml#L1-L51) |
| **run-build.yml** | workflow_call | Reusable build workflow | build job with ccache, setup.py bdist_wheel [.github/workflows/run-build.yml 1-109](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-build.yml#L1-L109) |
| **run-tests.yml** | workflow_call, workflow_run | Reusable test workflow | tests job with artifact download and pytest execution [.github/workflows/run-tests.yml 1-195](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tests.yml#L1-L195) |
| **build-image.yml** | workflow_call | Docker image building | check-if-docker-exist, build-image [.github/workflows/build-image.yml 1-121](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/build-image.yml#L1-L121) |

The build system uses artifact passing between jobs: `build` produces `install-artifacts` containing wheels [.github/workflows/run-build.yml 104-108](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-build.yml#L104-L108) which are then downloaded by `test` jobs [.github/workflows/run-tests.yml 72-76](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tests.yml#L72-L76) and installed via pip [.github/workflows/run-tests.yml 78-84](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tests.yml#L78-L84)

### Docker Infrastructure

The Docker build system creates layered images using a multi-stage approach for efficient caching and reproducible builds:

The Docker infrastructure implements content-addressable tagging: [.github/get-docker-tag.sh 1-86](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/get-docker-tag.sh#L1-L86) computes a tag based on Dockerfile contents and the tt-mlir version (extracted from `third_party/CMakeLists.txt`). This ensures that identical build environments produce identical Docker tags. The [.github/build-docker-images.sh 1-50](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/build-docker-images.sh#L1-L50) script checks if an image with the computed tag already exists using `docker manifest inspect`[.github/build-docker-images.sh 26](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/build-docker-images.sh#L26-L26) avoiding unnecessary rebuilds.

The [.github/Dockerfile.ci 1-42](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/Dockerfile.ci#L1-L42) uses a multi-stage build pattern:

1.   **ci-build stage**: Clones tt-torch and builds the TTMLIR toolchain [.github/Dockerfile.ci 4-26](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/Dockerfile.ci#L4-L26)
2.   **ci final stage**: Copies only the toolchain from the build stage [.github/Dockerfile.ci 29-42](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/Dockerfile.ci#L29-L42) resulting in a smaller final image

**Sources:**`.github/Dockerfile.base`, `.github/Dockerfile.ci`, `.github/get-docker-tag.sh`, `.github/build-docker-images.sh`

### Performance and Profiling Support

The build system includes optional performance profiling support through the `--build_perf` flag:

**Profiling Components:**

*   **Tracy Integration**: When built with `--build_perf`, the system includes Tracy capture and export tools [setup.py 176](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L176-L176)
*   **tt_profile Tool**: Console script entry point for profiling workflows [setup.py 230](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L230-L230)
*   **Metal Profiler**: Copies profiling tools from tt-metal build [setup.py 83-106](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L83-L106)
*   **Device Op Tracing**: Environment variable `TT_RUNTIME_ENABLE_PERF_TRACE` enables device-level tracing

The [tt_torch/tools/profile_util.py 1-349](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/profile_util.py#L1-L349) module provides the `Profiler` class which:

*   Sets up Tracy capture server [tt_torch/tools/profile_util.py 137-179](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/profile_util.py#L137-L179)
*   Processes Tracy dumps into CSV format [tt_torch/tools/profile_util.py 199-217](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/profile_util.py#L199-L217)
*   Enriches profiling data with MLIR location information [tt_torch/tools/profile_util.py 239-344](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/profile_util.py#L239-L344)
*   Generates performance reports at `results/perf/device_ops_perf_trace.csv`[tt_torch/tools/profile_util.py 47](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/profile_util.py#L47-L47)

**Sources:**`setup.py`, `tt_torch/tools/profile_util.py`, `.github/workflows/perf-benchmark.yml`

## Environment and Dependencies

### Environment Setup

The [env/activate 1-52](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/env/activate#L1-L52) script provides a comprehensive environment setup that configures:

*   **Python Environment**: Creates Python 3.11 virtual environment and installs dependencies from [requirements.txt 1-62](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt#L1-L62)
*   **Library Paths**: Configures `LD_LIBRARY_PATH` for torch, TTMLIR toolchain, and tt-torch libraries [env/activate 23](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/env/activate#L23-L23)
*   **Runtime Paths**: Sets up `PYTHONPATH` and `PATH` for proper module discovery [env/activate 41-49](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/env/activate#L41-L49)
*   **Hardware Configuration**: Defaults to `wormhole_b0` architecture [env/activate 50](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/env/activate#L50-L50)

### Dependency Management

The project uses a layered dependency approach:

| Category | Location | Purpose |
| --- | --- | --- |
| **Core Dependencies** | [requirements.txt 1-16](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt#L1-L16) | PyTorch, torch-xla, build tools |
| **Developer Dependencies** | [requirements.txt 17-30](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt#L17-L30) | Testing, formatting, analysis tools |
| **Model Dependencies** | [requirements.txt 31-62](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt#L31-L62) | Various ML model libraries |
| **Build Dependencies** | [setup.py 152-161](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L152-L161) | Runtime package requirements |
| **Performance Dependencies** | [setup.py 179-181](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L179-L181) | Profiling and analysis tools (conditional) |

The build system automatically manages dependency installation through the virtual environment, with specific version pinning for compatibility (e.g., `setuptools==59.6.0` for torchvision compatibility [requirements.txt 8](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt#L8-L8)).

**Sources:**`env/activate`, `requirements.txt`, `setup.py`

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-torch/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh