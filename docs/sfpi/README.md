---
project: sfpi
github: https://github.com/tenstorrent/sfpi
deepwiki: https://deepwiki.com/tenstorrent/sfpi
---

# Overview

Relevant source files
*   [.github/workflows/build-sfpi.yaml](https://github.com/tenstorrent/sfpi/blob/ed989b9d/.github/workflows/build-sfpi.yaml)
*   [README.md](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1)
*   [scripts/build.sh](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh)
*   [scripts/release.sh](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/release.sh)
*   [scripts/riscv32-unknown-elf-run](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/riscv32-unknown-elf-run)
*   [scripts/sfpi-info.sh](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/sfpi-info.sh)

## Purpose and Scope

This document provides a high-level introduction to the SFPI (Special Function Processing Unit Programming Interface) repository, which delivers a C++ programming interface and GCC-based toolchain for developing kernels that execute on Tenstorrent's SFPU hardware. This page covers the repository's purpose, architecture, main components, and how they interact to enable portable SFPU kernel development across Wormhole, Blackhole, and Grayskull chip architectures.

For detailed information about specific components:

*   Repository directory layout and file organization: see [Repository Structure](https://deepwiki.com/tenstorrent/sfpi/1.1-repository-structure)
*   Layer-by-layer component interactions: see [SFPI System Components](https://deepwiki.com/tenstorrent/sfpi/1.2-sfpi-system-components)
*   Programming with SFPI types and operations: see [Core SFPI Programming Interface](https://deepwiki.com/tenstorrent/sfpi/2-core-sfpi-programming-interface)
*   Building the toolchain from source: see [Building the SFPI Toolchain](https://deepwiki.com/tenstorrent/sfpi/5-building-the-sfpi-toolchain)
*   Testing and validation procedures: see [Testing and Validation](https://deepwiki.com/tenstorrent/sfpi/6-testing-and-validation)
*   Using SFPI in tt-metal projects: see [Integration and Usage](https://deepwiki.com/tenstorrent/sfpi/7-integration-and-usage)

## What is SFPI

SFPI is both a C++ programming interface and a complete toolchain (GCC + Binutils + Newlib) that enables developers to write portable high-performance kernels for Tenstorrent's Special Function Processing Unit (SFPU). The SFPU is a vector processing unit with 64-element vector registers that executes mathematical operations on floating-point and integer data.

The repository provides:

| Component | Description | Location |
| --- | --- | --- |
| **C++ API Headers** | Vector types (`vFloat`, `vInt`, `vUInt`) and high-level abstractions | `include/sfpi.h`, `include/sfpi_fp16.h`, `include/lltt.h` |
| **Hardware Layers** | Platform-specific intrinsics and implementations | `include/wormhole/`, `include/blackhole/`, `include/grayskull/` |
| **GCC Toolchain** | RISC-V compiler with SFPU instruction extensions | `gcc/` submodule |
| **Binutils** | Assembler and linker with SFPU support | `binutils/` submodule |
| **Build System** | Automated build, test, and packaging | `scripts/build.sh`, `scripts/release.sh` |
| **Test Infrastructure** | Comprehensive validation suite | `tests/`, `xfails/` |

**Sources:**[README.md 1-41](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L1-L41)[scripts/build.sh 1-148](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh#L1-L148)

## System Architecture

The SFPI system follows a five-layer abstraction model that enables write-once, run-anywhere code across different Tenstorrent chip architectures:

**Diagram: SFPI Abstraction Layers**

The abstraction strategy isolates user code from hardware details while allowing platform-specific optimizations when needed. The preprocessor macros `__riscv_xtttensixwh`, `__riscv_xtttensixbh`, and `__riscv_xtttensixgs` select the appropriate implementation at compile time.

**Sources:**[README.md 1-10](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L1-L10) Diagram 1 and Diagram 2 from system overview

## Repository Components

### Core Programming Interface

The top-level headers in `include/` provide the primary API:

**Diagram: Core API Headers**

*   **`include/sfpi.h`**: Defines vector types, operators, control flow constructs, and register access
*   **`include/sfpi_fp16.h`**: Provides 16-bit floating-point conversion types and functions
*   **`include/lltt.h`**: Implements instruction sequence recording and replay for loop optimization

**Sources:**[README.md 6](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L6-L6)

### Hardware Abstraction Layers

Each supported architecture has three header files that form a complete implementation:

**Diagram: Platform-Specific Implementation Layers**

Each platform's `*_hw.h` file defines hardware intrinsics, `*_imp.h` implements the C++ type system, and `*_lib.h` provides mathematical utility functions.

**Sources:** Diagram 5 from system overview, [README.md 6](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L6-L6)

### Toolchain Components

The repository includes enhanced versions of the standard RISC-V GCC toolchain as Git submodules:

**Diagram: Toolchain Submodules**

The toolchain provides:

*   **GCC**: C/C++ compiler with SFPU intrinsic support via `__builtin_rvtt_sfp*` functions
*   **Binutils**: Assembler (`as`) and linker (`ld`) that understand SFPU instructions
*   **Newlib**: Standard C library for freestanding (no OS) environments
*   **QEMU**: Emulator for testing compiled code (cloned when running tests)

**Sources:**[README.md 7-11](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L7-L11)[scripts/build.sh 154-159](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh#L154-L159)

### Build and Release System

The build system orchestrates compilation of all components and package creation:

**Diagram: Build System Scripts**

*   **`configure`**: Generated from `configure.ac`, detects system capabilities and generates `Makefile`
*   **`scripts/build.sh`**: Main build script that compiles GCC, Binutils, Newlib, and runs tests
*   **`scripts/release.sh`**: Creates distribution packages in multiple formats
*   **`scripts/sfpi-info.sh`**: Computes version strings and platform information

**Sources:**[scripts/build.sh 1-236](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh#L1-L236)[scripts/release.sh 1-160](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/release.sh#L1-L160)[scripts/sfpi-info.sh 1-211](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/sfpi-info.sh#L1-L211)[README.md 55-84](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L55-L84)

### Testing Infrastructure

The testing system validates both SFPI-specific functionality and upstream toolchain compatibility:

**Diagram: Testing System Components**

The test infrastructure includes:

*   **Instruction-level tests**: Validate individual SFPU operations against gold standard assembly
*   **Kernel tests**: Verify complete mathematical functions produce correct results
*   **Upstream test suites**: Run GCC, G++, Binutils, and linker test suites
*   **Failure management**: Track known limitations via `.xfail` files processed by `local-xfails.py`

**Sources:**[scripts/build.sh 151-213](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh#L151-L213)[README.md 86-211](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L86-L211) Diagram 4 from system overview

## CI/CD and Distribution

The GitHub Actions workflow automates building, testing, and releasing:

**Diagram: CI/CD Pipeline Flow**

The workflow defined in `.github/workflows/build-sfpi.yaml` builds for multiple architectures and optionally on different Linux distributions (bare Ubuntu or AlmaLinux container). Artifacts include compiled toolchains, test results, and configuration information.

**Sources:**[.github/workflows/build-sfpi.yaml 1-113](https://github.com/tenstorrent/sfpi/blob/ed989b9d/.github/workflows/build-sfpi.yaml#L1-L113)

## Integration with Downstream Projects

SFPI releases are consumed by the `tt-metal` project via CMake's `FetchContent`:

**Diagram: Integration with tt-metal**

The integration process:

1.   SFPI releases are published to GitHub with version tags
2.   `tt-metal`'s `sfpi-version.sh` specifies version, URL, and SHA256 hash
3.   CMake downloads and extracts the toolchain during configuration
4.   User kernel code is compiled with the downloaded SFPI toolchain
5.   Build artifacts and debug information are cached locally

**Sources:**[README.md 135-161](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L135-L161) Diagram 6 from system overview

## Version Numbering

SFPI uses integral version numbering. The version is determined by:

1.   Git tags matching `[0-9]*.[0-9]*.[0-9]*` (e.g., `1.2.3`)
2.   Branch name appended if not on `main` or not at a tagged commit
3.   Repository name prefix if forked from a different organization

Version information is computed by `scripts/sfpi-info.sh` and stored in `build/version` during the build process. The major version may be incremented when updating to a new upstream GCC version, but does not indicate API-breaking changes.

**Sources:**[scripts/build.sh 48-107](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh#L48-L107)[README.md 18-21](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L18-L21)[scripts/sfpi-info.sh 1-211](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/sfpi-info.sh#L1-L211)

## Key Files Reference

| File/Directory | Purpose |
| --- | --- |
| `include/sfpi.h` | Main C++ API: vector types, operators, control flow |
| `include/sfpi_fp16.h` | FP16 conversion types and functions |
| `include/lltt.h` | Instruction replay interface |
| `include/wormhole/` | Wormhole architecture implementation |
| `include/blackhole/` | Blackhole architecture implementation |
| `include/grayskull/` | Grayskull architecture implementation |
| `gcc/` | GCC compiler submodule |
| `binutils/` | Binutils assembler/linker submodule |
| `newlib/` | C library submodule |
| `tests/` | Test suites for each architecture |
| `xfails/` | Known test failure definitions |
| `scripts/build.sh` | Main build script |
| `scripts/release.sh` | Package creation script |
| `scripts/sfpi-info.sh` | Version and platform information |
| `.github/workflows/build-sfpi.yaml` | CI/CD automation |
| `configure`, `Makefile.in` | Autoconf build system |

**Sources:**[README.md 1-26](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L1-L26)

Dismiss
Refresh this wiki

Enter email to refresh