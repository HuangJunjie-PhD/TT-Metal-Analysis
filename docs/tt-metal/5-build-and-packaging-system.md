---
project: tt-metal
github: https://github.com/tenstorrent/tt-metal
deepwiki: https://deepwiki.com/tenstorrent/tt-metal/5-build-and-packaging-system
---

# Build and Packaging System

Relevant source files
*   [.github/actions/find-changed-files/action.yml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/actions/find-changed-files/action.yml)
*   [.github/actions/manual-docker-bake/action.yml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/actions/manual-docker-bake/action.yml)
*   [.github/actions/report-failure/action.yml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/actions/report-failure/action.yml)
*   [.github/scripts/compute-platform-data.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/scripts/compute-platform-data.sh)
*   [.github/scripts/utils/find-changed-files.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/scripts/utils/find-changed-files.sh)
*   [.github/scripts/utils/model-charts-sync.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/scripts/utils/model-charts-sync.py)
*   [.github/workflows/basic.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/basic.yaml)
*   [.github/workflows/build-artifact.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml)
*   [.github/workflows/build-docker-artifact.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-docker-artifact.yaml)
*   [.github/workflows/build-docker-tools.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-docker-tools.yaml)
*   [.github/workflows/build-evaluation-image.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-evaluation-image.yaml)
*   [.github/workflows/build-wrapper.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-wrapper.yaml)
*   [.github/workflows/clang-tidy-reusable.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/clang-tidy-reusable.yaml)
*   [.github/workflows/code-analysis.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/code-analysis.yaml)
*   [.github/workflows/merge-gate.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml)
*   [.github/workflows/pr-gate.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pr-gate.yaml)
*   [.github/workflows/sdk-examples.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/sdk-examples.yaml)
*   [.github/workflows/smoke.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/smoke.yaml)
*   [.github/workflows/ttsim.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml)
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt)
*   [INSTALLING.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/INSTALLING.md?plain=1)
*   [MANIFEST.in](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/MANIFEST.in)
*   [build_metal.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/build_metal.sh)
*   [cmake/linking.cmake](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/cmake/linking.cmake)
*   [cmake/project_options.cmake](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/cmake/project_options.cmake)
*   [create_venv.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/create_venv.sh)
*   [dockerfile/Dockerfile](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile)
*   [dockerfile/Dockerfile.basic-dev](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile.basic-dev)
*   [dockerfile/Dockerfile.evaluation](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile.evaluation)
*   [dockerfile/Dockerfile.tools](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile.tools)
*   [dockerfile/scripts/install-ccache.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/scripts/install-ccache.sh)
*   [docs/source/tt-metalium/tools/triage.rst](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/docs/source/tt-metalium/tools/triage.rst)
*   [install_dependencies.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/install_dependencies.sh)
*   [pyproject.toml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/pyproject.toml)
*   [scripts/install-uv.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/scripts/install-uv.sh)
*   [scripts/install_debugger.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/scripts/install_debugger.sh)
*   [setup.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/setup.py)
*   [tests/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/CMakeLists.txt)
*   [tests/pipeline_reorg/ttnn-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/pipeline_reorg/ttnn-tests.yaml)
*   [tests/pipeline_reorg/ttsim-skip-list.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/pipeline_reorg/ttsim-skip-list.yaml)
*   [tests/tt_metal/distributed/benchmark_distributed_host_buffer.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/distributed/benchmark_distributed_host_buffer.cpp)
*   [tests/tt_metal/distributed/benchmark_thread_pool.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/distributed/benchmark_thread_pool.cpp)
*   [tests/tt_metal/distributed/test_thread_pool.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/distributed/test_thread_pool.cpp)
*   [tests/tt_metal/tt_fabric/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/tt_metal/tt_fabric/CMakeLists.txt)
*   [tests/ttnn/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/CMakeLists.txt)
*   [tests/ttnn/benchmark/cpp/benchmark_host_alloc_on_tensor_readback.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/benchmark/cpp/benchmark_host_alloc_on_tensor_readback.cpp)
*   [tests/ttnn/benchmark/cpp/benchmark_host_dtype_conversion.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/benchmark/cpp/benchmark_host_dtype_conversion.cpp)
*   [tests/ttnn/benchmark/cpp/host_tilizer_untilizer/tilizer_untilizer.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/benchmark/cpp/host_tilizer_untilizer/tilizer_untilizer.cpp)
*   [tests/ttnn/benchmark/cpp/matmul/test_matmul_benchmark.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/benchmark/cpp/matmul/test_matmul_benchmark.cpp)
*   [tests/ttnn/benchmark/cpp/operations/ternary/benchmark_where.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/benchmark/cpp/operations/ternary/benchmark_where.cpp)
*   [tests/ttnn/benchmark/cpp/padding/pad_rm.cpp](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/benchmark/cpp/padding/pad_rm.cpp)
*   [tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_deepseek_moe_post_combine_reduce.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_deepseek_moe_post_combine_reduce.py)
*   [tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_deepseek_prefill_extract.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_deepseek_prefill_extract.py)
*   [tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_deepseek_prefill_insert.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_deepseek_prefill_insert.py)
*   [tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_moe_grouped_topk.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_moe_grouped_topk.py)
*   [tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_single_routed_expert.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_single_routed_expert.py)
*   [tt_metal/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/CMakeLists.txt)
*   [tt_metal/common/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/common/CMakeLists.txt)
*   [tt_metal/common/sources.cmake](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/common/sources.cmake)
*   [tt_metal/fabric/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/fabric/CMakeLists.txt)
*   [tt_metal/hw/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/CMakeLists.txt)
*   [tt_metal/hw/toolchain/main.ld](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/toolchain/main.ld)
*   [tt_metal/impl/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/CMakeLists.txt)
*   [tt_metal/jit_build/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/CMakeLists.txt)
*   [tt_metal/llrt/CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/CMakeLists.txt)
*   [tt_metal/python_env/requirements-dev.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/python_env/requirements-dev.txt)
*   [ttnn/ttnn/download_sfpi.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/download_sfpi.py)

This document describes the CMake-based build system, packaging infrastructure, and artifact generation pipeline for `tt-metal`. It covers build configuration, dependency management, Docker environments, and CI/CD artifact generation. For runtime behavior and execution, see [Core Runtime System (TT-Metalium)](https://deepwiki.com/tenstorrent/tt-metal/2-core-runtime-system-(tt-metalium)). For testing infrastructure, see [CI/CD and Testing Infrastructure](https://deepwiki.com/tenstorrent/tt-metal/6-cicd-and-testing-infrastructure).

* * *

## Overview

The build system is structured around CMake as the primary build generator, with bash scripts providing convenience wrappers like `build_metal.sh`[build_metal.sh 1-200](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/build_metal.sh#L1-L200) The system produces multiple artifact types: compiled libraries, Debian packages, Python wheels, and Docker images. Build configuration is highly flexible, supporting multiple platforms (Ubuntu 22.04, 24.04), compilers (GCC, Clang), and hardware targets (Wormhole, Blackhole).

### Build System Architecture

**Sources:**[CMakeLists.txt 1-180](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L1-L180)[tt_metal/CMakeLists.txt 5-217](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/CMakeLists.txt#L5-L217)[build_metal.sh 1-200](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/build_metal.sh#L1-L200)[tt_metal/hw/CMakeLists.txt 123](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/CMakeLists.txt#L123-L123)[dockerfile/Dockerfile 136](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L136-L136)[CMakeLists.txt 115](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L115-L115)

* * *

## CMake Project Structure

The project uses CMake 3.24+ [CMakeLists.txt 1](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L1-L1) with Ninja as the recommended generator. The root `CMakeLists.txt` establishes the project configuration, manages third-party submodules (like `umd`), and delegates to subdirectories for component-specific builds. For deep technical details on CMake logic, see [CMake Configuration and Compilation](https://deepwiki.com/tenstorrent/tt-metal/5.1-cmake-configuration-and-compilation).

### Project Configuration

| CMake Variable | Purpose | Default |
| --- | --- | --- |
| `CMAKE_BUILD_TYPE` | Build configuration (Release, Debug, RelWithDebInfo) | RelWithDebInfo [CMakeLists.txt 22](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L22-L22) |
| `BUILD_SHARED_LIBS` | Build shared vs static libraries | ON [CMakeLists.txt 136](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L136-L136) |
| `WITH_PYTHON_BINDINGS` | Include Python bindings (nanobind) | ON [CMakeLists.txt 137](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L137-L137) |
| `TT_METAL_BUILD_TESTS` | Build Metalium test suite | OFF [CMakeLists.txt 139](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L139-L139) |
| `TT_UNITY_BUILDS` | Enable Unity builds for faster compilation | ON [CMakeLists.txt 141](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L141-L141) |
| `ENABLE_CCACHE` | Enable ccache integration | ON [CMakeLists.txt 112](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L112-L112) |
| `ENABLE_DISTRIBUTED` | Enable multi-host distributed compute (OpenMPI) | ON [CMakeLists.txt 145](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L145-L145) |

**Sources:**[CMakeLists.txt 22](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L22-L22)[CMakeLists.txt 112-114](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L112-L114)[CMakeLists.txt 135-145](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L135-L145)

### Build Type Configurations

The project defines custom build types beyond CMake defaults to support performance profiling and memory safety:

| Build Type | Compiler Flags | Use Case |
| --- | --- | --- |
| **Release** | `-O3` | Production deployment [CMakeLists.txt 61](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L61-L61) |
| **RelWithDebInfo** | `-O3 -g3 -DDEBUG -fno-omit-frame-pointer` | Performance profiling with symbols [CMakeLists.txt 48-62](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L48-L62) |
| **Debug** | `-O0 -g3 -DDEBUG -fno-omit-frame-pointer` | Full interactive debugging [CMakeLists.txt 48-62](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L48-L62) |
| **ASan / TSan** | Address/Thread Sanitizer instrumentation | Memory and concurrency safety [CMakeLists.txt 66](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L66-L66) |

**Sources:**[CMakeLists.txt 48-66](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L48-L66)

* * *

## Docker Build Environments

The CI/CD pipeline uses multi-stage Docker builds to provide consistent environments for building, testing, and development. Images are managed through GitHub Actions and distributed via a container registry (GHCR or Harbor). For details on container workflows, see [Docker Build Environments](https://deepwiki.com/tenstorrent/tt-metal/5.2-docker-build-environments).

### Image Variants

*   **ci-build:** Contains the full toolchain (Clang, Ninja, CMake, mold, ccache) for compiling the repository [dockerfile/Dockerfile 136-157](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L136-L157)
*   **ci-test:** Minimal runtime environment for executing tests on hardware runners [dockerfile/Dockerfile 159-180](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L159-L180)
*   **dev:** Includes development tools and headers for interactive work [dockerfile/Dockerfile 182-200](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L182-L200)
*   **manylinux:** Specialized image for building portable Python wheels [.github/workflows/build-artifact.yaml 127-131](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml#L127-L131)

**Sources:**[dockerfile/Dockerfile 136-200](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L136-L200)[.github/workflows/build-artifact.yaml 127-131](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml#L127-L131)

* * *

## Artifact Generation and Packaging

The system generates multiple artifact types for distribution, ranging from developer-focused binaries to production-ready Debian packages. For details on the `build-artifact.yaml` workflow, see [Artifact Generation and Packaging](https://deepwiki.com/tenstorrent/tt-metal/5.3-artifact-generation-and-packaging).

### Artifact Types

| Type | Generation Logic | Use Case |
| --- | --- | --- |
| **Debian Packages** | `cpack` integration via `packaging.cmake` | System installation (`metalium-runtime`, `metalium-dev`) [tt_metal/CMakeLists.txt 178-217](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/CMakeLists.txt#L178-L217) |
| **Python Wheels** | `setup.py` and `uv build` | PyPI distribution for `ttnn`[.github/workflows/build-artifact.yaml 27-31](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml#L27-L31) |
| **Docker Images** | `dockerfile/Dockerfile` | Reproducible CI and deployment environments [.github/workflows/build-artifact.yaml 102-132](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml#L102-L132) |

**Sources:**[tt_metal/CMakeLists.txt 178-217](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/CMakeLists.txt#L178-L217)[.github/workflows/build-artifact.yaml 27-31](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml#L27-L31)[.github/workflows/build-artifact.yaml 102-132](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml#L102-L132)

* * *

## Dependency Management

The project manages dependencies through CMake's `add_subdirectory` and `CPM`. Core hardware drivers (UMD) are integrated as submodules. For documentation on integration with UMD, FlatBuffers, and Tracy, see [Dependency Management](https://deepwiki.com/tenstorrent/tt-metal/5.4-dependency-management).

### Key Dependencies

*   **UMD:** Universal Memory Driver for hardware communication [CMakeLists.txt 4-6](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L4-L6)
*   **FlatBuffers:** Used for mesh coordinate and descriptor serialization [tt_metal/CMakeLists.txt 16-21](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/CMakeLists.txt#L16-L21)
*   **Tracy:** Integrated for high-resolution profiling [tt_metal/CMakeLists.txt 97](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/CMakeLists.txt#L97-L97)
*   **SFPI:** Specialized RISC-V toolchain for Tensix core kernels [tt_metal/hw/CMakeLists.txt 42-107](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/CMakeLists.txt#L42-L107)
*   **OpenMPI:** Required for multi-host distributed execution [dockerfile/Dockerfile 90-95](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L90-L95)

**Sources:**[CMakeLists.txt 4-6](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L4-L6)[tt_metal/CMakeLists.txt 16-21](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/CMakeLists.txt#L16-L21)[tt_metal/CMakeLists.txt 97](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/CMakeLists.txt#L97-L97)[tt_metal/hw/CMakeLists.txt 42-107](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/CMakeLists.txt#L42-L107)[dockerfile/Dockerfile 90-95](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L90-L95)

* * *

## Build Caching and Optimization

To maintain fast developer and CI turnaround times, the system employs aggressive caching strategies:

*   **ccache:** Integration for caching C++ object files [CMakeLists.txt 112-114](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L112-L114)
*   **Unity Builds:**`TT_UNITY_BUILDS` reduces compiler overhead [CMakeLists.txt 141](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L141-L141)
*   **Precompiled Headers (PCH):**`tt_reuse_precompile_headers` reduces header parsing time [tt_metal/CMakeLists.txt 119](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/CMakeLists.txt#L119-L119)
*   **mold:** Used as a faster linker in CI environments [dockerfile/Dockerfile 154](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L154-L154)

For performance tuning details, see [Build Caching and Optimization](https://deepwiki.com/tenstorrent/tt-metal/5.5-build-caching-and-optimization).

**Sources:**[CMakeLists.txt 112-114](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L112-L114)[CMakeLists.txt 141](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt#L141-L141)[tt_metal/CMakeLists.txt 119](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/CMakeLists.txt#L119-L119)[dockerfile/Dockerfile 154](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L154-L154)

* * *

## Hardware Toolchain and SFPI

Firmware for Tenstorrent Tensix cores is compiled using a specialized RISC-V SFPI toolchain. The build system manages the download or local build of this toolchain via `sfpi-info.sh`[tt_metal/hw/CMakeLists.txt 46-51](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/CMakeLists.txt#L46-L51) For details, see [Hardware Toolchain and SFPI](https://deepwiki.com/tenstorrent/tt-metal/5.6-hardware-toolchain-and-sfpi).

### Kernel Compilation Flow

**Sources:**[tt_metal/hw/CMakeLists.txt 109](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/CMakeLists.txt#L109-L109)[tt_metal/hw/CMakeLists.txt 194-207](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/hw/CMakeLists.txt#L194-L207)

* * *

## Integration with CI/CD

The build system is the foundation for the CI/CD pipeline. The `build-artifact.yaml` workflow acts as the central hub, orchestrating the compilation across different platforms and publishing results to subsequent test jobs [.github/workflows/build-artifact.yaml 1-137](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml#L1-L137) It handles complex logic such as building with Tracy enabled or disabled and managing multi-host OpenMPI dependencies [.github/workflows/build-artifact.yaml 17-26](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml#L17-L26) Static analysis is integrated via `code-analysis.yaml`, which triggers `clang-tidy` scans [.github/workflows/code-analysis.yaml 155-195](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/code-analysis.yaml#L155-L195) For simulator-based validation, `ttsim.yaml` orchestrates integration tests using the `libttsim.so` backend [.github/workflows/ttsim.yaml 69-113](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L69-L113)

**Sources:**[.github/workflows/build-artifact.yaml 1-137](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml#L1-L137)[.github/workflows/code-analysis.yaml 155-195](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/code-analysis.yaml#L155-L195)[.github/workflows/ttsim.yaml 69-113](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L69-L113)

Dismiss
Refresh this wiki

Enter email to refresh