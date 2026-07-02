---
project: tt-npe
github: https://github.com/tenstorrent/tt-npe
deepwiki: https://deepwiki.com/tenstorrent/tt-npe/8-development-guide
---

# Development Guide

Relevant source files
*   [.github/workflows/build_and_test_ubuntu.yml](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/.github/workflows/build_and_test_ubuntu.yml)
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/CMakeLists.txt)
*   [build-npe.sh](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/build-npe.sh)
*   [cmake/compilers.cmake](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/cmake/compilers.cmake)
*   [cmake/dependencies.cmake](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/cmake/dependencies.cmake)
*   [tt_npe/CMakeLists.txt](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/CMakeLists.txt)

This page provides an overview of the development workflow for contributors to tt-npe. It covers environment setup, build procedures, testing infrastructure, and contribution guidelines. For detailed information on specific topics, see:

*   Build system configuration and CMake details: [Build System](https://deepwiki.com/tenstorrent/tt-npe/8.1-build-system)
*   Testing framework, pytest configuration, and coverage: [Testing Framework](https://deepwiki.com/tenstorrent/tt-npe/8.2-testing-framework)
*   Internal data structure reference: [Core Data Structures](https://deepwiki.com/tenstorrent/tt-npe/8.3-core-data-structures)
*   CI/CD pipelines and contribution process: [CI/CD and Contributing](https://deepwiki.com/tenstorrent/tt-npe/8.4-cicd-and-contributing)

## Development Environment Requirements

The tt-npe project requires the following tools and dependencies:

| Component | Requirement | Notes |
| --- | --- | --- |
| **Compiler** | Clang-17 (default) or GCC-12+ | Clang-17 is tested and recommended |
| **CMake** | 3.20 or higher | Used for build configuration |
| **Python** | 3.x with development headers | Required for pybind11 bindings |
| **C++ Standard** | C++20 | Enforced via `CMAKE_CXX_STANDARD` |
| **Build Generator** | Ninja (recommended) or Make | Ninja provides faster parallel builds |

**Additional Libraries (auto-fetched):**

*   Boost (container, unordered, program_options)
*   pybind11 for Python bindings
*   nlohmann/json and simdjson for JSON parsing
*   fmt for string formatting
*   magic_enum for enum introspection
*   GoogleTest for unit testing
*   zstd for compression

Sources: [CMakeLists.txt 1-218](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/CMakeLists.txt#L1-L218)[cmake/dependencies.cmake 1-101](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/cmake/dependencies.cmake#L1-L101)

## Quick Start: Building from Source

The simplest way to build tt-npe is using the provided build script:

This script:

1.   Creates a `build/` directory for CMake artifacts
2.   Configures CMake with appropriate build type
3.   Builds all targets using Ninja
4.   Installs binaries to `install/` directory

**Manual Build Process:**

Sources: [build-npe.sh 1-34](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/build-npe.sh#L1-L34)

## Development Workflow

**Development Iteration:**

1.   Make changes to source files in `tt_npe/cpp/` or `tt_npe/py/`
2.   Run `./build-npe.sh` to rebuild
3.   Execute unit tests: `./build/tt_npe/tt_npe_ut`
4.   Run integration tests: `pytest` (see [Testing Framework](https://deepwiki.com/tenstorrent/tt-npe/8.2-testing-framework))
5.   Install to `install/` directory for system-wide usage

Sources: [build-npe.sh 1-34](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/build-npe.sh#L1-L34)

## Build System Architecture

The build system is based on CMake with a two-level hierarchy: root configuration and subdirectory targets.

### CMake Build Hierarchy

**Key Build Targets:**

| Target | Type | Purpose | Dependencies |
| --- | --- | --- | --- |
| `tt_npe` | Shared Library | Core simulation engine (C++) | Boost, nlohmann/json, fmt, zstd |
| `tt_npe_pybind` | Python Module | Python bindings via pybind11 | `tt_npe`, pybind11 |
| `tt_npe_ut` | Executable | C++ unit tests | `tt_npe`, GoogleTest |
| `npe_common_libs` | Interface Library | Common link dependencies | (aggregates external deps) |
| `test_common_libs` | Interface Library | Test dependencies | GoogleTest, magic_enum, fmt |

Sources: [CMakeLists.txt 1-218](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/CMakeLists.txt#L1-L218)[tt_npe/CMakeLists.txt 1-102](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/CMakeLists.txt#L1-L102)

### Compiler Configuration

The build system supports multiple compiler configurations with automatic detection:

**Compiler Selection Logic:**

1.   Check for `CMAKE_C_COMPILER` and `CMAKE_CXX_COMPILER` environment variables
2.   If not set, invoke `FIND_AND_SET_CLANG17()` to locate `clang-17` and `clang++-17`
3.   Validate compiler version via `CHECK_COMPILERS()` function
4.   Configure standard library (libc++ for Clang, libstdc++ for GCC)
5.   Select optimal linker (mold > lld > gold) if using Clang

**Build Types:**

| Build Type | Flags | Use Case |
| --- | --- | --- |
| `Release` | `-O3` | Production builds, performance testing |
| `Debug` | `-O0 -g -DDEBUG` | Development with debugger |
| `RelWithDebInfo` | `-O3 -g -DDEBUG` | Performance profiling with symbols |
| `CI` | `-O3 -DDEBUG` | Continuous integration (fast + asserts) |

Sources: [CMakeLists.txt 15-120](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/CMakeLists.txt#L15-L120)[cmake/compilers.cmake 1-44](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/cmake/compilers.cmake#L1-L44)

## Installation Structure

After building, the `install/` directory follows standard Unix conventions:

```
install/
├── bin/
│   ├── tt_npe.py                              # Main CLI
│   ├── npe_analyze_noc_trace_dir.py           # Batch analysis
│   ├── fabric_post_process.py                 # Multi-chip preprocessing
│   └── programmatic_workload_generation.py    # Example script
├── lib/
│   ├── libtt_npe.so                           # Core C++ library
│   └── tt_npe_pybind.cpython-*.so             # Python module
└── include/
    ├── npeConfig.hpp                          # Configuration
    ├── npeDeviceModel.hpp                     # Device models
    ├── npeEngine.hpp                          # Simulation engine
    ├── npeStats.hpp                           # Statistics
    ├── npeWorkload.hpp                        # Workload types
    └── npeWorkloadIngest.hpp                  # JSON parsing
```

**RPATH Configuration:**

The build system sets `RPATH` to `$ORIGIN/../lib:$ORIGIN/` to enable relocatable installations. This allows the Python module and executables to find `libtt_npe.so` without requiring `LD_LIBRARY_PATH` modifications.

Sources: [tt_npe/CMakeLists.txt 4-102](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/CMakeLists.txt#L4-L102)

## Testing Infrastructure Overview

The project includes both C++ unit tests and Python integration tests:

### Test Execution Flow

**Running Tests:**

For detailed test organization and configuration, see [Testing Framework](https://deepwiki.com/tenstorrent/tt-npe/8.2-testing-framework).

Sources: [CMakeLists.txt 200-212](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/CMakeLists.txt#L200-L212)[tt_npe/CMakeLists.txt 68-80](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/CMakeLists.txt#L68-L80)

## Source Code Organization

The codebase follows a clear directory structure separating concerns:

```
tt_npe/
├── cpp/
│   ├── include/           # Public C++ API headers
│   │   ├── npeEngine.hpp
│   │   ├── npeDeviceModel.hpp
│   │   ├── npeWorkload.hpp
│   │   └── ...
│   ├── src/               # C++ implementation
│   │   ├── npeEngine.cpp
│   │   ├── npeDeviceModel.cpp
│   │   └── ...
│   ├── pybind/            # Python bindings
│   │   └── bindings.cpp
│   └── test/              # C++ unit tests
│       └── *.cpp
└── py/
    ├── pycli/             # Command-line interface
    │   └── tt_npe.py
    ├── util/              # Analysis utilities
    │   ├── npe_analyze_noc_trace_dir.py
    │   └── fabric_post_process.py
    └── examples/          # Example scripts
        └── programmatic_workload_generation.py
```

**File Naming Conventions:**

*   C++ headers use `.hpp` extension
*   Implementation files use `.cpp` extension
*   Python modules use lowercase with underscores (snake_case)
*   Test files are prefixed with `test_` (Python) or located in `test/` directory (C++)

**Key Source Files:**

| File | Purpose | API Level |
| --- | --- | --- |
| `npeEngine.{hpp,cpp}` | Core simulation loop | Internal |
| `npeDeviceModel.{hpp,cpp}` | Device model interface and implementations | Public API |
| `npeWorkload.{hpp,cpp}` | Workload data structures | Public API |
| `npeWorkloadIngest.{hpp,cpp}` | JSON parsing and trace conversion | Public API |
| `npeStats.{hpp,cpp}` | Statistics collection and output | Internal |
| `pybind/bindings.cpp` | Python API definitions | Binding layer |

Sources: [tt_npe/CMakeLists.txt 12-21](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/CMakeLists.txt#L12-L21)

## Compilation Flags and Warnings

The project enforces strict compilation standards:

**Common Compiler Flags:**

*   `-Werror`: Treat warnings as errors
*   `-Wreturn-type`: Check return types
*   `-Wswitch`: Check switch completeness
*   `-Wuninitialized`: Check for uninitialized variables
*   `-mavx2`: Enable AVX2 instructions
*   `-fPIC`: Position-independent code (for shared libraries)
*   `-fvisibility-inlines-hidden`: Hide inline functions in shared libraries

**Sanitizer Support:**

| Sanitizer | CMake Option | Purpose |
| --- | --- | --- |
| AddressSanitizer | `ENABLE_ASAN=ON` | Detect memory errors (buffer overflows, use-after-free) |
| MemorySanitizer | `ENABLE_MSAN=ON` | Detect uninitialized memory reads |
| ThreadSanitizer | `ENABLE_TSAN=ON` | Detect data races |
| UBSanitizer | `ENABLE_UBSAN=ON` | Detect undefined behavior |

**Building with Sanitizers:**

**Note:** Only one sanitizer can be enabled at a time (enforced by CMake logic).

Sources: [CMakeLists.txt 155-195](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/CMakeLists.txt#L155-L195)

## Dependency Management

External dependencies are automatically fetched and built using CPM (CMake Package Manager) and FetchContent.

### Dependency Resolution Flow

**CPM Cache:**

Dependencies are cached in `.cpmcache/` directory to avoid repeated downloads:

**Custom Dependency Configuration:**

*   **zstd:** Static library only, no programs/tests
*   **GoogleTest:** Install disabled (`INSTALL_GTEST OFF`)
*   **simdjson:** Static library build enabled
*   **Boost:** Fetched using custom `fetch_boost_library()` macro from `cmake/fetch_boost.cmake`

Sources: [cmake/dependencies.cmake 1-101](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/cmake/dependencies.cmake#L1-L101)

## Continuous Integration

The project uses GitHub Actions for automated testing on every pull request and push to main branch.

### CI Pipeline

**CI Configuration:**

*   **Platform:** Ubuntu (latest)
*   **Build Type:** Release
*   **Python:** System Python 3.x
*   **Test Execution:** Via `./tt_npe/scripts/run_ut.sh`

**Workflow File:**`.github/workflows/build_and_test_ubuntu.yml`

For detailed contribution guidelines and workflow requirements, see [CI/CD and Contributing](https://deepwiki.com/tenstorrent/tt-npe/8.4-cicd-and-contributing).

Sources: [.github/workflows/build_and_test_ubuntu.yml 1-44](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/.github/workflows/build_and_test_ubuntu.yml#L1-L44)

## Key Development Practices

**Code Style:**

*   C++20 standard with no extensions (`CMAKE_CXX_EXTENSIONS OFF`)
*   Public headers in `cpp/include/`, implementation in `cpp/src/`
*   Python follows PEP 8 conventions
*   Use `fmt` for string formatting (not `std::cout` or `printf`)
*   Use `magic_enum` for enum-to-string conversions

**Memory Management:**

*   Prefer RAII and smart pointers over raw pointers
*   Use Boost containers for performance-critical code
*   Leverage compiler sanitizers during development

**Build Hygiene:**

*   Out-of-source builds enforced (prevents polluting source directory)
*   Separate build directories per build type recommended
*   Clean rebuild: `rm -rf build/ && ./build-npe.sh`

**Testing Requirements:**

*   Add C++ unit tests for new core functionality
*   Add Python integration tests for API changes
*   Ensure tests pass in CI before merging

**Debugging Tips:**

Sources: [CMakeLists.txt 1-218](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/CMakeLists.txt#L1-L218)[tt_npe/CMakeLists.txt 1-102](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/CMakeLists.txt#L1-L102)

## Related Documentation

For more specific information about development topics, refer to:

*   **[Build System](https://deepwiki.com/tenstorrent/tt-npe/8.1-build-system):** Detailed CMake configuration, targets, and build options
*   **[Testing Framework](https://deepwiki.com/tenstorrent/tt-npe/8.2-testing-framework):** Test organization, pytest configuration, C++ unit tests, coverage reporting
*   **[Core Data Structures](https://deepwiki.com/tenstorrent/tt-npe/8.3-core-data-structures):** Internal data structure reference (Coord, Grid2D/3D, MulticastCoordSet, etc.)
*   **[CI/CD and Contributing](https://deepwiki.com/tenstorrent/tt-npe/8.4-cicd-and-contributing):** GitHub workflows, code review process, contribution guidelines

For using tt-npe (non-development workflows), see:

*   **[Quick Start Guide](https://deepwiki.com/tenstorrent/tt-npe/1.2-quick-start-guide):** Basic usage examples
*   **[Python API Reference](https://deepwiki.com/tenstorrent/tt-npe/6.2-python-api-reference):** Public API documentation
*   **[Command Line Interface](https://deepwiki.com/tenstorrent/tt-npe/6.1-command-line-interface):** CLI usage and options

Dismiss
Refresh this wiki

Enter email to refresh