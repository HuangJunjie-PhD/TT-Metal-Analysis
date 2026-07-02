---
project: tt-umd
github: https://github.com/tenstorrent/tt-umd
deepwiki: https://deepwiki.com/tenstorrent/tt-umd/7-build-system-and-development
---

# Build System & Development

Relevant source files
*   [.github/manylinux-aarch64.Dockerfile](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.github/manylinux-aarch64.Dockerfile)
*   [.github/ubuntu-24.04-arm.Dockerfile](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.github/ubuntu-24.04-arm.Dockerfile)
*   [.github/workflows/build-device.yml](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.github/workflows/build-device.yml)
*   [.github/workflows/docker-image.yml](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.github/workflows/docker-image.yml)
*   [.github/workflows/release.yml](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.github/workflows/release.yml)
*   [CHANGELOG](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CHANGELOG)
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt)
*   [VERSION](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/VERSION)
*   [cmake/example_client.cmake](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/cmake/example_client.cmake)
*   [device/CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt)
*   [tests/CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/CMakeLists.txt)
*   [third_party/CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/third_party/CMakeLists.txt)

This document provides an overview of the tt-umd build system, development workflows, and testing infrastructure. It covers the CMake-based build configuration, third-party dependency management, CI/CD pipeline, and test organization patterns used throughout the repository.

For detailed information about:

*   Build configuration options and CMake settings, see [Build Configuration](https://deepwiki.com/tenstorrent/tt-umd/7.1-build-configuration)
*   CI/CD workflows and automation, see [CI/CD Pipeline](https://deepwiki.com/tenstorrent/tt-umd/7.2-cicd-pipeline)
*   Test organization and utilities, see [Testing Infrastructure](https://deepwiki.com/tenstorrent/tt-umd/7.3-testing-infrastructure)

## Build System Architecture

The tt-umd build system is based on CMake 3.25+ with CPM (CMake Package Manager) for third-party dependency management [CMakeLists.txt 1-11](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L1-L11) The build system supports both C++ library builds and Python wheel generation through separate but integrated build paths.

### Build System Overview

**Build System Components**

The build system consists of several key components:

| Component | File Path | Purpose |
| --- | --- | --- |
| Root Configuration | `CMakeLists.txt` | Project setup, compiler configuration, build options [CMakeLists.txt 1-178](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L1-L178) |
| Device Library | `device/CMakeLists.txt` | Core UMD library compilation and target properties [device/CMakeLists.txt 1-138](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L1-L138) |
| Test Suite | `tests/CMakeLists.txt` | Test target organization and `test_common` setup [tests/CMakeLists.txt 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/CMakeLists.txt#L1-L50) |
| Dependency Manager | `third_party/CMakeLists.txt` | Third-party dependency fetching via CPM [third_party/CMakeLists.txt 1-162](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/third_party/CMakeLists.txt#L1-L162) |
| Python Packaging | `pyproject.toml` | Wheel build configuration using scikit-build-core |

Sources: [CMakeLists.txt 1-178](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L1-L178)[device/CMakeLists.txt 1-138](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L1-L138)[tests/CMakeLists.txt 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/CMakeLists.txt#L1-L50)[third_party/CMakeLists.txt 1-162](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/third_party/CMakeLists.txt#L1-L162)

### Build Options

The build system provides several CMake options to control the compilation scope:

| Option | Default | Description |
| --- | --- | --- |
| `TT_UMD_BUILD_TESTS` | OFF | Build test executables [CMakeLists.txt 55](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L55-L55) |
| `TT_UMD_BUILD_SIMULATION` | OFF | Build simulation harnessing (RTL sim) [CMakeLists.txt 56](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L56-L56) |
| `TT_UMD_BUILD_PYTHON` | OFF | Build Python bindings via nanobind [CMakeLists.txt 57](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L57-L57) |
| `TT_UMD_BUILD_TOOLS` | ON | Build utility tools (tt-smi, etc.) [CMakeLists.txt 58](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L58-L58) |
| `TT_UMD_BUILD_EXAMPLES` | OFF | Build example programs [CMakeLists.txt 59](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L59-L59) |
| `TT_UMD_BUILD_ALL` | OFF | Enable all above options [CMakeLists.txt 60](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L60-L60) |
| `TT_UMD_BUILD_STATIC` | OFF | Build static library instead of shared [CMakeLists.txt 61](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L61-L61) |
| `TT_UMD_BUILD_EMULE` | OFF | Enable software emulation chip (`SWEmuleChip`) [CMakeLists.txt 63](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L63-L63) |

Sources: [CMakeLists.txt 55-63](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L55-L63)

### Compiler Configuration

**Compiler Requirements:**

*   C++17 standard required [CMakeLists.txt 6-7](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L6-L7)
*   Position-independent code enabled globally [CMakeLists.txt 8](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L8-L8)
*   x86-64-v3 microarchitecture level for x86_64 builds [CMakeLists.txt 149](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L149-L149)
*   Warnings treated as errors by default [CMakeLists.txt 145](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L145-L145)

Sources: [CMakeLists.txt 6-152](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L6-L152)

## Build Target Organization

### Device Library (`tt-umd`)

The core `tt-umd` library target contains all hardware abstraction functionality. It can be built as either `SHARED` or `STATIC`[device/CMakeLists.txt 31-37](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L31-L37)

**Source Organization:**

*   Architecture implementations for Wormhole, Blackhole, and Grendel [device/CMakeLists.txt 85-87](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L85-L87)
*   Chip abstractions (Local, Remote, Mock, Simulation) [device/CMakeLists.txt 68-72](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L68-L72)
*   Memory management (TLB, Sysmem) [device/CMakeLists.txt 73-81](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L73-L81)
*   Communication protocols (PCIe, JTAG, Remote) [device/CMakeLists.txt 100-102](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L100-L102)
*   Topology discovery and Cluster management [device/CMakeLists.txt 82-130](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L82-L130)

Sources: [device/CMakeLists.txt 64-138](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L64-L138)

### Test Targets

Tests are organized using an interface library `test_common` that aggregates necessary dependencies like `gtest`, `spdlog`, and the `tt-umd` library itself [tests/CMakeLists.txt 1-14](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/CMakeLists.txt#L1-L14)

**Test Categories:**

*   `api_tests`: Public API validation [tests/CMakeLists.txt 27](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/CMakeLists.txt#L27-L27)
*   `baremetal_tests`: Tests that do not require hardware [tests/CMakeLists.txt 28](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/CMakeLists.txt#L28-L28)
*   `unit_tests_blackhole` / `unit_tests_wormhole`: Architecture-specific tests [tests/CMakeLists.txt 29-33](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/CMakeLists.txt#L29-L33)
*   `unit_tests_glx`: Galaxy-specific multi-chip tests [tests/CMakeLists.txt 34](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/CMakeLists.txt#L34-L34)

Sources: [tests/CMakeLists.txt 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/CMakeLists.txt#L1-L50)

## CI/CD Pipeline

The CI/CD pipeline uses GitHub Actions to build and verify tt-umd across a matrix of OS distributions and compilers.

### Build Matrix Configuration

The `Build Device` workflow [.github/workflows/build-device.yml 3](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/%20.github/workflows/build-device.yml#L3-L3) executes on every PR and push to main. It tests:

*   **Distributions:** Ubuntu 22.04, Ubuntu 24.04 (x86 and ARM64), Fedora 39 [.github/workflows/build-device.yml 95-114](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/%20.github/workflows/build-device.yml#L95-L114)
*   **Compilers:** Clang (13, 17, 20) and GCC (11, 13) [.github/workflows/build-device.yml 96-108](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/%20.github/workflows/build-device.yml#L96-L108)
*   **Release Flags:** Mirroring production release flags to catch linting/compilation issues early [.github/workflows/build-device.yml 112-113](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/%20.github/workflows/build-device.yml#L112-L113)

Sources: [.github/workflows/build-device.yml 3-114](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.github/workflows/build-device.yml#L3-L114)

### Release Automation

When the `VERSION` file is updated on the `main` branch, an automated release workflow is triggered [.github/workflows/release.yml 1-9](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/%20.github/workflows/release.yml#L1-L9) This workflow:

1.   Extracts the version and specific changelog entries [.github/workflows/release.yml 34-68](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/%20.github/workflows/release.yml#L34-L68)
2.   Generates a summary of included Pull Requests [.github/workflows/release.yml 134-157](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/%20.github/workflows/release.yml#L134-L157)
3.   Builds production-ready artifacts including Python wheels and system packages (.deb, .rpm).

Sources: [.github/workflows/release.yml 1-157](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.github/workflows/release.yml#L1-L157)[VERSION 1](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/VERSION#L1-L1)

## Development Features

### Unity Builds

To speed up compilation, the system supports unity builds which concatenate source files. Specific files like `wormhole_implementation.cpp` and `blackhole_implementation.cpp` are excluded from this to avoid ODR (One Definition Rule) violations due to conflicting header definitions [device/CMakeLists.txt 50-62](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L50-L62)

### Clang-Tidy Integration

Static analysis is integrated into the build process. It uses a project-specific `.clang-tidy` configuration and includes flags to ignore unknown warning options, ensuring compatibility across different clang-tidy versions [CMakeLists.txt 120-141](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L120-L141)

### FlatBuffers Generation

For simulation builds (`TT_UMD_BUILD_SIMULATION`), the system automatically generates C++ headers from the `simulation_device.fbs` schema using `flatc`[device/CMakeLists.txt 6-29](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L6-L29)

Sources: [device/CMakeLists.txt 6-62](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/CMakeLists.txt#L6-L62)[CMakeLists.txt 120-141](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/CMakeLists.txt#L120-L141)

Dismiss
Refresh this wiki

Enter email to refresh