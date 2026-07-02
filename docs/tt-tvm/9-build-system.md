---
project: tt-tvm
github: https://github.com/tenstorrent/tt-tvm
deepwiki: https://deepwiki.com/tenstorrent/tt-tvm/9-build-system
---

# Build System

Relevant source files
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt)
*   [cmake/config.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake)
*   [cmake/modules/CUDA.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/CUDA.cmake)
*   [cmake/modules/Git.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/Git.cmake)
*   [cmake/modules/LibInfo.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/LibInfo.cmake)
*   [cmake/utils/FindCUDA.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/utils/FindCUDA.cmake)
*   [python/tvm/support.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/support.py)
*   [src/support/libinfo.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/support/libinfo.cc)
*   [tests/lint/check_cmake_options.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/check_cmake_options.py)
*   [tests/scripts/task_config_build_arm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_arm.sh)
*   [tests/scripts/task_config_build_cortexm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_cortexm.sh)
*   [tests/scripts/task_config_build_cpu.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_cpu.sh)
*   [tests/scripts/task_config_build_gpu.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu.sh)
*   [tests/scripts/task_config_build_gpu_other.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu_other.sh)
*   [tests/scripts/task_config_build_gpu_vulkan.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu_vulkan.sh)
*   [tests/scripts/task_config_build_hexagon.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_hexagon.sh)
*   [tests/scripts/task_config_build_i386.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_i386.sh)
*   [tests/scripts/task_config_build_wasm.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_wasm.sh)

## Purpose and Scope

The Build System provides the CMake-based infrastructure for configuring and compiling TVM's compiler and runtime libraries. It handles platform detection, dependency resolution, optional feature selection, and generation of build artifacts including `libtvm.so` (full compiler) and `libtvm_runtime.so` (runtime-only library).

This page covers build configuration, library outputs, and runtime introspection. For testing infrastructure and continuous integration, see [CI/CD Infrastructure](https://deepwiki.com/tenstorrent/tt-tvm/10-cicd-infrastructure). For development environment setup, see [Setting Up Development Environment](https://deepwiki.com/tenstorrent/tt-tvm/11.1-setting-up-development-environment).

Sources: [CMakeLists.txt 1-898](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L1-L898)[cmake/config.cmake 1-441](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L1-L441)

## Build System Architecture

The build system is organized around a central CMakeLists.txt with modular components for different features and targets:

**Build System Components**

Sources: [CMakeLists.txt 1-24](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L1-L24)[CMakeLists.txt 517-560](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L517-L560)

## Configuration Flow

The build system searches for configuration in this order:

**Typical workflow:**

1.   Create build directory: `mkdir build && cd build`
2.   Copy template: `cp ../cmake/config.cmake .`
3.   Edit `config.cmake` to enable/disable features
4.   Run cmake: `cmake ..`
5.   Build: `make -j8`

Sources: [CMakeLists.txt 17-23](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L17-L23)[cmake/config.cmake 18-37](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L18-L37)

## Build Options System

The `tvm_option()` macro defines configurable build options. Each option has a default value that can be overridden in `config.cmake` or via CMake CLI.

### tvm_option Macro

The macro is defined in [cmake/utils/Utils.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/utils/Utils.cmake) and provides a consistent interface for boolean and path-based options.

**Usage patterns:**

Sources: [CMakeLists.txt 29-128](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L29-L128)

### Build Option Categories

| Category | Description | Example Options |
| --- | --- | --- |
| **Backend Runtimes** | Target hardware support | `USE_CUDA`, `USE_ROCM`, `USE_OPENCL`, `USE_VULKAN`, `USE_METAL` |
| **Executors** | Execution engines | `USE_GRAPH_EXECUTOR`, `USE_AOT_EXECUTOR`, `USE_PROFILER` |
| **Compiler Infrastructure** | Core compiler features | `USE_LLVM`, `USE_MLIR`, `USE_RPC`, `USE_MICRO` |
| **BLAS Libraries** | Linear algebra backends | `USE_BLAS`, `USE_MKL`, `USE_DNNL`, `USE_CUBLAS` |
| **External Codegen** | BYOC integrations | `USE_TENSORRT_CODEGEN`, `USE_ARM_COMPUTE_LIB`, `USE_CUTLASS` |
| **Development Tools** | Build and debug support | `USE_GTEST`, `USE_LIBBACKTRACE`, `USE_CCACHE`, `SUMMARIZE` |
| **3rdparty Paths** | External dependency locations | `DLPACK_PATH`, `DMLC_PATH`, `COMPILER_RT_PATH` |

Sources: [CMakeLists.txt 29-128](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L29-L128)[cmake/config.cmake 39-441](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L39-L441)

### Key Configuration Options

**LLVM Support:**

**CUDA Support:**

**Runtime Options:**

Sources: [cmake/config.cmake 43-255](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L43-L255)[tests/scripts/task_config_build_cpu.sh 26-59](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_cpu.sh#L26-L59)

## Source File Organization

The build system organizes source files into logical groups:

**Object library targets** ([CMakeLists.txt 581-583](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L581-L583)):

*   `tvm_objs`: Compiler-only sources (IR, passes, codegen)
*   `tvm_runtime_objs`: Runtime sources (executors, device backends)
*   `tvm_libinfo_objs`: Build configuration metadata

Sources: [CMakeLists.txt 288-349](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L288-L349)[CMakeLists.txt 370-494](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L370-L494)

## Library Outputs

The build system produces three primary library configurations:

### Library Target Definitions

**libtvm.so** ([CMakeLists.txt 586-597](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L586-L597)):

Full compiler and runtime. Use for model compilation and execution.

**libtvm_runtime.so** ([CMakeLists.txt 597-612](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L597-L612)):

Runtime-only library. Use for deployment when pre-compiled models are loaded.

**Size comparison:**

*   `libtvm.so`: ~100-200 MB (includes compiler)
*   `libtvm_runtime.so`: ~10-20 MB (runtime only)

### Static Runtime

When `BUILD_STATIC_RUNTIME=ON`:

**Important:** Static runtime requires special linker flags to ensure global constructors are executed (needed for TVM's registry mechanism):

Sources: [CMakeLists.txt 586-612](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L586-L612)[cmake/config.cmake 366-376](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L366-L376)

## Runtime Introspection System

The build system embeds configuration metadata into the compiled libraries, accessible at runtime via `tvm.support.libinfo()`.

### Implementation Details

**CMake Module** ([cmake/modules/LibInfo.cmake 21-141](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/LibInfo.cmake#L21-L141)):

The `add_lib_info()` function sets compile definitions on `libinfo.cc`:

**C++ Implementation** ([src/support/libinfo.cc 25-372](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/support/libinfo.cc#L25-L372)):

Macros convert compile definitions to runtime strings:

**Python API** ([python/tvm/support.py 33-48](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/support.py#L33-L48)):

### Usage Example

**Output:**

```
Python Environment
  TVM version    = 0.12.0
  Python version = 3.8.10 (64 bit)
  os.uname()     = Linux 5.15.0
CMake Options:
  {
    "USE_CUDA": "ON",
    "USE_LLVM": "/usr/bin/llvm-config-15",
    "GIT_COMMIT_HASH": "abc123...",
    ...
  }
```

Sources: [cmake/modules/LibInfo.cmake 21-141](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/LibInfo.cmake#L21-L141)[src/support/libinfo.cc 267-372](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/support/libinfo.cc#L267-L372)[python/tvm/support.py 33-69](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/support.py#L33-L69)

## Platform-Specific Build Configurations

The build system includes pre-configured scripts for different platforms in `tests/scripts/`:

| Script | Target | Key Features |
| --- | --- | --- |
| `task_config_build_cpu.sh` | x86-64 CPU | LLVM, DNNL, NNPACK, TFLite, Arm Compute Lib |
| `task_config_build_gpu.sh` | NVIDIA GPU | CUDA, cuDNN, cuBLAS, Vulkan, OpenCL, TensorRT |
| `task_config_build_arm.sh` | ARM CPU | LLVM, Arm Compute Library, VTA |
| `task_config_build_hexagon.sh` | Hexagon DSP | Hexagon SDK, QHL library |
| `task_config_build_cortexm.sh` | Cortex-M | MicroTVM, CMSIS-NN, Ethos-U |
| `task_config_build_wasm.sh` | WebAssembly | Emscripten LLVM, minimal runtime |
| `task_config_build_i386.sh` | 32-bit x86 | LLVM 10, minimal features |

### CPU Build Configuration

**Example** ([tests/scripts/task_config_build_cpu.sh 26-59](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_cpu.sh#L26-L59)):

Sources: [tests/scripts/task_config_build_cpu.sh 19-60](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_cpu.sh#L19-L60)

### GPU Build Configuration

**Example** ([tests/scripts/task_config_build_gpu.sh 26-56](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu.sh#L26-L56)):

Sources: [tests/scripts/task_config_build_gpu.sh 19-56](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_gpu.sh#L19-L56)

### Hexagon DSP Configuration

**Example** ([tests/scripts/task_config_build_hexagon.sh 26-48](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_hexagon.sh#L26-L48)):

Hexagon requires special compiler configuration to avoid standard library dependencies ([CMakeLists.txt 351-359](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L351-L359)):

Sources: [tests/scripts/task_config_build_hexagon.sh 19-48](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_hexagon.sh#L19-L48)[CMakeLists.txt 351-359](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L351-L359)

## Module System

The build system uses a modular structure where each feature has a dedicated CMake module:

### Core Modules

### Module Inclusion Pattern

Modules are included from the main CMakeLists.txt ([CMakeLists.txt 517-560](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L517-L560)):

### CUDA Module Example

The CUDA module ([cmake/modules/CUDA.cmake 18-98](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/CUDA.cmake#L18-L98)) demonstrates the standard pattern:

1.   **Find dependency:**

1.   **Check if found:**

1.   **Add sources:**

1.   **Link libraries:**

1.   **Handle optional sub-features:**

Sources: [cmake/modules/CUDA.cmake 1-98](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/CUDA.cmake#L1-L98)[CMakeLists.txt 517-560](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L517-L560)

## Build Validation and Testing

The build system includes validation to ensure consistency:

### Option Synchronization Check

The linter ([tests/lint/check_cmake_options.py 32-83](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/check_cmake_options.py#L32-L83)) verifies that all `tvm_option()` declarations are mirrored in both `libinfo.cc` and `LibInfo.cmake`:

This ensures runtime introspection stays synchronized with build options.

Sources: [tests/lint/check_cmake_options.py 32-83](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/lint/check_cmake_options.py#L32-L83)

### Git Information

The Git module ([cmake/modules/Git.cmake 1-57](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/Git.cmake#L1-L57)) captures repository metadata:

This information is embedded in the library and accessible via `libinfo()["GIT_COMMIT_HASH"]`.

Sources: [cmake/modules/Git.cmake 23-56](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/modules/Git.cmake#L23-L56)

## Compiler Flags and Build Types

The build system configures compiler flags based on platform and build type:

### Debug vs Release

**Debug Mode** ([CMakeLists.txt 207-221](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L207-L221)):

### Platform-Specific Flags

**MSVC** ([CMakeLists.txt 154-204](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L154-L204)):

*   `/EHsc`: Enable C++ exception handling
*   `/MP`: Multi-processor compilation
*   `/bigobj`: Large object files
*   `/wd4244`, `/wd4267`: Suppress specific warnings

**GCC/Clang** ([CMakeLists.txt 206-235](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L206-L235)):

*   `-Wall`: Enable all warnings
*   `-fPIC`: Position-independent code
*   `-std=c++17`: C++17 standard
*   `-faligned-new`: Aligned allocation support

### Symbol Visibility

When `HIDE_PRIVATE_SYMBOLS=ON` ([CMakeLists.txt 217-220](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L217-L220)):

This reduces library size and prevents symbol conflicts when linking multiple TVM-dependent libraries.

Sources: [CMakeLists.txt 154-235](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L154-L235)[CMakeLists.txt 711-725](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L711-L725)

## Advanced Build Features

### CCache Support

Accelerate rebuilds using compiler caching ([cmake/utils/CCache.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/utils/CCache.cmake)):

Sources: [CMakeLists.txt 501-504](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L501-L504)[cmake/config.cmake 378-387](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/config.cmake#L378-L387)

### Alternative Linkers

Speed up linking with mold or lld ([cmake/utils/Linker.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/utils/Linker.cmake)):

Sources: [CMakeLists.txt 875](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L875-L875)

### Cross-Compilation

For embedded targets, specify toolchain file:

The Hexagon configuration demonstrates conditional compiler setup ([tests/scripts/task_config_build_hexagon.sh 32-40](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_hexagon.sh#L32-L40)):

Sources: [tests/scripts/task_config_build_hexagon.sh 32-40](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_hexagon.sh#L32-L40)

## Summary Options

When `SUMMARIZE=ON`, the build system prints a comprehensive summary of all configured options after CMake configuration:

This invokes [cmake/utils/Summary.cmake](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/cmake/utils/Summary.cmake) to display:

*   Enabled/disabled features
*   Detected dependency versions
*   Compiler flags
*   Library paths
*   Git commit information

Useful for debugging configuration issues and documenting build configurations.

Sources: [CMakeLists.txt 877-879](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/CMakeLists.txt#L877-L879)[tests/scripts/task_config_build_cpu.sh 59](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/scripts/task_config_build_cpu.sh#L59-L59)

Dismiss
Refresh this wiki

Enter email to refresh