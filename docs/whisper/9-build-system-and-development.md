---
project: whisper
github: https://github.com/tenstorrent/whisper
deepwiki: https://deepwiki.com/tenstorrent/whisper/9-build-system-and-development
---

# Build System and Development

Relevant source files
*   [GNUmakefile](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile)
*   [README.md](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1)
*   [Triggers.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.cpp)
*   [Triggers.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.hpp)
*   [whisper.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp)

This page documents the build system, compilation options, dependencies, Python bindings (`pywhisper`), testing infrastructure (RISCOF), and development setup for the Whisper RISC-V simulator. For runtime configuration options, see [System Organization and Configuration](https://deepwiki.com/tenstorrent/whisper/2.1-system-organization-and-configuration). For interactive debugging, see [Interactive Command-Line Interface](https://deepwiki.com/tenstorrent/whisper/6.2-interactive-command-line-interface).

## Overview

Whisper uses a GNU Make-based build system defined in `GNUmakefile`. The build process produces:

*   `whisper` - The main simulator executable
*   `pywhisper` - Python bindings (shared library)
*   `librvcore.a` - Core simulator library
*   Sub-project libraries (softfloat, PCI, virtual memory)

The build system supports multiple compilation modes, optional features, and both static and dynamic linking.

**Sources:**[GNUmakefile 1-240](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L1-L240)[README.md 92-115](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L92-L115)

## Build System Architecture

**Sources:**[GNUmakefile 1-240](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L1-L240)[README.md 92-115](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L92-L115)

## Dependencies

### Required Dependencies

| Dependency | Version | Purpose |
| --- | --- | --- |
| **g++** | 11+ | C++ compiler with C++20 support |
| **Boost** | 1.75+ | `boost_program_options` for command-line parsing |
| **RISC-V Toolchain** | - | Cross-compiler (`riscv64-unknown-elf-gcc`) for target programs |
| **Make** | GNU Make | Build orchestration |

### Optional Dependencies

| Dependency | Build Flag | Purpose |
| --- | --- | --- |
| **lz4** | `LZ4_COMPRESS=1` | LZ4 compression for snapshots/traces |
| **libvncserver** | `REMOTE_FRAME_BUFFER=1` | VNC-based frame buffer for graphics output |
| **SoftFloat** | `SOFT_FLOAT=1` | IEEE-compliant floating-point operations |

### Installing Dependencies

**Ubuntu/Debian:**

**Arch Linux:**

**Boost:** Download from [boost.org](https://www.boost.org/) and compile with C++20:

**Sources:**[README.md 44-91](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L44-L91)[GNUmakefile 6-82](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L6-L82)

## Build Configuration Options

### Key Build Variables

| Variable | Default | Description |
| --- | --- | --- |
| `STATIC_LINK` | `1` | Static (1) vs dynamic (0) linking to Boost |
| `BOOST_DIR` | `/wdc/apps/utilities/boost-1.67` | Path to Boost installation |
| `OFLAGS` | `-O3` | Optimization flags (`-g` for debug) |
| `CXX_STD` | `c++20` | C++ standard version |
| `BUILD_DIR` | `build-$(uname -s)` | Output directory for build artifacts |
| `INSTALL_DIR` | `.` | Installation target directory |

### Preprocessor Flags

| Flag | Enabled By | Effect |
| --- | --- | --- |
| `SOFT_FLOAT` | `SOFT_FLOAT=1` | Use Berkeley SoftFloat library |
| `MEM_CALLBACKS` | `MEM_CALLBACKS=1` | Enable sparse memory with callbacks |
| `FAST_SLOPPY` | `FAST_SLOPPY=1` | Disable some compliance checks for speed |
| `LZ4_COMPRESS` | `LZ4_COMPRESS=1` | Enable LZ4 snapshot compression |
| `REMOTE_FRAME_BUFFER` | `REMOTE_FRAME_BUFFER=1` | Enable VNC frame buffer |
| `GIT_SHA` | Always | Compile git commit SHA into binary |

**Note:**`FAST_SLOPPY` and `MEM_CALLBACKS` are mutually exclusive.

**Sources:**[GNUmakefile 6-82](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L6-L82)[GNUmakefile 121-129](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L121-L129)

## Building the Simulator

### Basic Build

### Build Targets

| Target | Description | Output |
| --- | --- | --- |
| `all` | Build simulator and Python bindings | `whisper` + `pywhisper.so` |
| `$(BUILD_DIR)/$(PROJECT)` | Build main simulator only | `build-Linux/whisper` |
| `$(BUILD_DIR)/$(PY_PROJECT)` | Build Python bindings only | `build-Linux/pywhisper.so` |
| `install` | Install simulator to `INSTALL_DIR` | - |
| `install-py` | Install Python bindings | - |
| `clean` | Remove all build artifacts | - |
| `cscope` | Generate cscope database | `cscope.out` |

### Build Process Flow

### Source File Organization

The core library (`librvcore.a`) is compiled from the following sources:

```
RVCORE_SRCS:
  - IntRegs.cpp, CsRegs.cpp, FpRegs.cpp, VecRegs.cpp
  - Memory.cpp, SparseMem.cpp
  - Hart.cpp, Core.cpp, System.cpp
  - Decoder.cpp, InstEntry.cpp, DecodedInst.cpp
  - Triggers.cpp, PerfRegs.cpp
  - Server.cpp, Interactive.cpp, PerfApi.cpp
  - Mcm.cpp (Memory Consistency Model)
  - vector.cpp, float.cpp, bitmanip.cpp, crypto.cpp, vector-crypto.cpp
  - hypervisor.cpp, Syscall.cpp
  - imsic/Imsic.cpp, aplic/Aplic.cpp, iommu/Iommu.cpp
  - Trace.cpp, printTrace.cpp, snapshot.cpp
  - Args.cpp, Session.cpp, HartConfig.cpp
```

**Sources:**[GNUmakefile 156-177](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L156-L177)[GNUmakefile 189-193](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L189-L193)

### Git SHA Tracking

The build system automatically embeds the current git commit SHA into the binary. If the SHA changes, `Args.cpp` is automatically recompiled:

This allows `whisper --version` to report the exact commit used for the build.

**Sources:**[GNUmakefile 121-125](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L121-L125)

## Python Bindings (pywhisper)

### Build System Integration

Python bindings are built using [pybind11](https://pybind11.readthedocs.io/). The shared library `pywhisper.so` (or `.dylib`/`.pyd` depending on platform) exposes C++ classes to Python.

### Building Python Bindings

The build rule for Python bindings:

**Sources:**[GNUmakefile 145-149](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L145-L149)[GNUmakefile 152-153](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L152-L153)

### Using Python Bindings

Example usage from the README:

The Python bindings expose:

*   **System class**: Multi-hart system management
*   **Hart class**: Individual hart execution
*   **Register access**: Integer (`x0-x31`), floating-point (`f0-f31`), vector, CSRs
*   **Execution control**: `step()`, `run()` methods

**Sources:**[README.md 1043-1056](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L1043-L1056)

## Testing and Verification

### RISCOF Integration

Whisper includes a plugin for [RISCOF](https://riscof.readthedocs.io/) (RISC-V Compliance Test Framework) to run the official [riscv-arch-test](https://github.com/tenstorrent/whisper/blob/733fd921/riscv-arch-test) compliance test suite.

### Running RISCOF Tests

**Setup:**

**Configuration file (`config.ini`):**

**Run tests:**

The plugin compiles each test with the RISC-V toolchain, runs it in both Whisper and the reference model (Sail or Spike), and compares the signature outputs.

**Sources:**[README.md 1079-1099](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L1079-L1099)

### Code Coverage

Whisper supports C++ code coverage using `gcov` and `lcov`.

**Enable coverage:**

**Collect coverage:**

The coverage infrastructure creates:

*   `.gcno` files (at compile time): Notes about instrumentation points
*   `.gcda` files (at runtime): Execution counts for each instrumentation point
*   `coverage.dat`: Aggregated coverage data in lcov format
*   HTML report: Browsable coverage visualization

**Environment variables for working directory control:**

*   `GCOV_PREFIX`: Redirect `.gcda` output to specific directory
*   `GCOV_PREFIX_STRIP`: Strip N leading directories from paths

**Sources:**[README.md 1024-1038](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L1024-L1038)

## Development Setup

### Compiler Requirements

*   **C++ Standard**: C++20 (`-std=c++20`)
*   **Compiler**: g++ 11 or later
*   **Flags**: `-MMD -MP` (dependency generation), `-fPIC` (position-independent code)
*   **Warnings**: `-pedantic -Wall -Wextra -Wformat -Wwrite-strings`

### Project Structure

```
whisper/
├── GNUmakefile              # Main build file
├── whisper.cpp              # Main entry point
├── py-bindings.cpp          # Python interface
├── build-Linux/             # Build output directory
│   ├── *.cpp.o              # Object files
│   ├── *.d                  # Dependency files
│   ├── librvcore.a          # Core library
│   ├── whisper              # Main executable
│   └── pywhisper.so         # Python module
├── third_party/
│   ├── softfloat/           # Berkeley SoftFloat
│   └── nlohmann/            # JSON parser
├── pci/                     # PCI device support
├── trace-reader/            # Trace file reader
├── virtual_memory/          # Virtual memory subsystem
├── imsic/                   # IMSIC interrupt controller
├── aplic/                   # APLIC interrupt controller
├── iommu/                   # IOMMU implementation
├── arch_test_target/        # RISCOF plugin
│   └── whisper/
│       ├── riscof_whisper.py
│       ├── whisper_isa.yaml
│       └── whisper_platform.yaml
└── configuration/           # JSON config schema
```

### Compilation Process

**Dependency tracking:** The build system uses `-MMD -MP` flags to generate `.d` files that track header dependencies. These are automatically included via:

This ensures that when a header changes, all dependent source files are recompiled.

**Sources:**[GNUmakefile 128-134](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L128-L134)[GNUmakefile 183-187](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L183-L187)

### Debugging Build Issues

**Common issues:**

| Problem | Solution |
| --- | --- |
| Boost not found | Set `BOOST_DIR=/path/to/boost` |
| Wrong Boost ABI | Add `CPPFLAGS += -D_GLIBCXX_USE_CXX11_ABI=0` for older Boost |
| Missing lz4 | Install `liblz4-dev` or disable with `LZ4_COMPRESS=0` |
| Missing VNC | Install `libvncserver-dev` or disable with `REMOTE_FRAME_BUFFER=0` |
| Python config error | Ensure `python3-config` is in PATH |
| Linker errors | Check that all sub-libraries built successfully |

**Verbose build:**

**Clean rebuild:**

**Sources:**[GNUmakefile 221-227](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L221-L227)[README.md 44-91](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L44-L91)

### Build Performance

The build system supports parallel compilation:

Sub-projects (softfloat, PCI, etc.) are built independently and can leverage parallelism within their own makefiles.

**Sources:**[GNUmakefile 195-206](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L195-L206)

## Platform-Specific Notes

### Linux

*   Default configuration works out of the box
*   Uses static libstdc++ (`-static-libstdc++`)
*   Links against system pthread, z, dl, rt, util

### macOS (Darwin)

*   Detected via `uname -s`
*   Uses libc++ instead of libstdc++
*   Different library linking: `-lpthread -lm -lz -ldl -lc++`
*   Boost linked as static library directly

### Architecture

*   **x86_64**: Uses `-mfma` for FMA instructions
*   **Other architectures**: No special flags

**Sources:**[GNUmakefile 96-107](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L96-L107)[GNUmakefile 103-107](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L103-L107)

## Summary

The Whisper build system provides:

1.   **Flexible configuration** via Makefile variables and preprocessor flags
2.   **Modular architecture** with core library and optional sub-projects
3.   **Python bindings** for scripting and automation
4.   **Testing integration** via RISCOF for compliance verification
5.   **Coverage support** for code quality analysis
6.   **Platform portability** across Linux and macOS

Key files:

*   [GNUmakefile 1-240](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L1-L240) - Build system definition
*   [whisper.cpp 1-115](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp#L1-L115) - Main entry point
*   [py-bindings.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/py-bindings.cpp) - Python bindings (referenced but not provided)
*   [README.md 92-1099](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L92-L1099) - Build and testing documentation

Dismiss
Refresh this wiki

Enter email to refresh