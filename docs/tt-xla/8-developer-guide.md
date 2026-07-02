---
project: tt-xla
github: https://github.com/tenstorrent/tt-xla
deepwiki: https://deepwiki.com/tenstorrent/tt-xla/8-developer-guide
---

# Developer Guide

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.gitignore)
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt)
*   [README.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1)
*   [docs/src/getting_started.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1)
*   [docs/src/getting_started_build_from_source.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1)
*   [docs/src/getting_started_docker.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_docker.md?plain=1)
*   [docs/src/imgs/test_infra.png](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/imgs/test_infra.png)
*   [docs/src/imgs/tt_smi.png](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/imgs/tt_smi.png)
*   [docs/src/imgs/tt_xla_logo.png](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/imgs/tt_xla_logo.png)
*   [docs/src/test_infra.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/test_infra.md?plain=1)
*   [tests/filecheck/add.ttnn.mlir](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/filecheck/add.ttnn.mlir)
*   [tests/filecheck/rms_norm.ttir.mlir](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/filecheck/rms_norm.ttir.mlir)
*   [third_party/CMakeLists.txt](https://github.com/tenstorrent/tt-xla/blob/c77995f6/third_party/CMakeLists.txt)

This guide provides practical information for developers working on the TT-XLA codebase. It covers environment setup, building from source, running tests, and debugging compilation and performance issues.

## Overview

TT-XLA development follows a standard workflow centered around the virtual environment activation script, CMake-based build system, and pytest-based testing infrastructure. The development cycle typically involves:

1.   **Environment Setup** ([Section 8.1](https://deepwiki.com/tenstorrent/tt-xla/8.1-development-environment-setup)): Activate the virtual environment and verify hardware configuration
2.   **Building from Source** ([Section 8.2](https://deepwiki.com/tenstorrent/tt-xla/8.2-building-from-source)): Configure and build native binaries with CMake
3.   **Running Tests** ([Section 8.3](https://deepwiki.com/tenstorrent/tt-xla/8.3-running-and-debugging-tests)): Execute test suites and validate changes
4.   **Debugging Issues** ([Section 8.4](https://deepwiki.com/tenstorrent/tt-xla/8.4-debugging-compilation-and-performance)): Use IR dumps, Tracy profiling, and logging to diagnose problems

**Development Cycle Diagram**:

**Sources**: [README.md 1-62](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L1-L62)[docs/src/getting_started.md 1-99](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1#L1-L99)

* * *

## System Requirements

TT-XLA development requires specific system dependencies and hardware configuration.

**Host System Requirements**:

| Component | Requirement |
| --- | --- |
| Operating System | Ubuntu 22.04 |
| Python | 3.11 or 3.12 |
| Compiler | Clang 17 |
| GCC | GCC 12 (for libstdc++) |
| Build System | CMake 4.0.3, Ninja |
| MPI | OpenMPI with ULFM support |

**Hardware Requirements**:

*   Tenstorrent device (n150, p150, n300, or llmbox)
*   Hugepages configured (1GB pages)
*   Device driver installed via tt-installer

**Verification Command**:

`# Check device visibilitytt-smi`
**Sources**: [docs/src/getting_started_build_from_source.md 21-112](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L21-L112)[docs/src/getting_started.md 25-49](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1#L25-L49)

* * *

## Quick Reference: Common Tasks

This section provides quick commands for common development tasks. See child sections for detailed explanations.

### Environment Setup

`# Activate development environment (required for all operations)source venv/activate # Verify environment is activeecho $TTXLA_ENV_ACTIVATED  # Should print "1"`
### Building

`# Configure build (Release mode)cmake -G Ninja -B build -DCMAKE_BUILD_TYPE=Release # Build native binariescmake --build build # Build Python wheelcd python_packagepython setup.py bdist_wheel --build-type release`
### Running Tests

`# Run single-chip testspytest -v tests/jax/single_chip # Run specific test with verbose outputpytest -svv tests/jax/single_chip/test_operations.py::test_add # Run with IR serializationpytest tests/jax/single_chip/test_model.py::test_resnet --serialize`
### Debugging

`# Enable debug loggingexport TTXLA_LOGGER_LEVEL=DEBUGexport TT_METAL_LOGGER_LEVEL=DEBUG # Export compilation IRsexport TTXLA_EXPORT_PATH=./debug_irpython my_model.py`
**Sources**: [docs/src/getting_started_build_from_source.md 114-173](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L114-L173)[docs/src/test_infra.md 117-155](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/test_infra.md?plain=1#L117-L155)

* * *

## Key File Locations

Understanding the repository structure is essential for navigating the codebase.

**Core Directories**:

| Directory | Purpose |
| --- | --- |
| `pjrt_implementation/src/` | PJRT plugin implementation (C++) |
| `pjrt_implementation/inc/` | Public headers and interfaces |
| `python_package/` | Python package structure and setup.py |
| `tests/` | Test suites (pytest-based) |
| `third_party/` | External dependencies (tt-mlir, loguru, pjrt_c_api) |
| `integrations/vllm_plugin/` | vLLM integration for LLM serving |
| `.github/workflows/` | CI/CD workflow definitions |

**Build Artifacts**:

| Path | Contents |
| --- | --- |
| `build/` | CMake build directory (native binaries) |
| `install/` | CMake install prefix (libraries, headers) |
| `python_package/dist/` | Generated Python wheels |
| `third_party/tt-mlir/src/tt-mlir/` | tt-mlir source (external project) |
| `third_party/tt-mlir/install/` | tt-mlir installation (libraries) |

**Sources**: [CMakeLists.txt 1-111](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L1-L111)[.gitignore 1-46](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.gitignore#L1-L46)

* * *

## Development Tools

TT-XLA provides several tools for debugging and analysis:

**Compilation Debugging**:

*   **IR Export**: Dump intermediate representations at each compilation stage (VHLO, StableHLO, TTIR, TTNN)
*   **FileCheck**: Validate IR transformations with pattern matching
*   **--serialize flag**: Save compilation artifacts from pytest runs

**Performance Analysis**:

*   **Tracy Profiler**: Host-side performance tracing with zone annotations
*   **TTNN Metrics**: Device-side operation statistics (execution time, bandwidth)
*   **tt-metal Logging**: Runtime execution traces and memory allocation logs

**Testing Tools**:

*   **OpTester**: Test individual operations with random inputs
*   **GraphTester**: Test multi-operation subgraphs
*   **ModelTester**: Test complete models with validation
*   **CompilerConfig**: Programmatic control of compiler options in tests

Detailed usage of these tools is covered in [Section 8.3](https://deepwiki.com/tenstorrent/tt-xla/8.3-running-and-debugging-tests) and [Section 8.4](https://deepwiki.com/tenstorrent/tt-xla/8.4-debugging-compilation-and-performance).

**Sources**: [docs/src/test_infra.md 1-155](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/test_infra.md?plain=1#L1-L155)[CMakeLists.txt 63](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L63-L63)

* * *

## Pre-commit Hooks

The repository uses pre-commit hooks to enforce code quality standards. Install them after cloning:

`source venv/activatepre-commit install # Run on all files (optional)pre-commit run --all-files`
The hooks perform:

*   Code formatting (C++, Python)
*   Linting checks
*   SPDX license header validation
*   Trailing whitespace removal

**Sources**: [docs/src/getting_started_build_from_source.md 196-210](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L196-L210)

* * *

## Next Steps

For detailed information on specific development tasks, see the following sections:

*   **[Section 8.1: Development Environment Setup](https://deepwiki.com/tenstorrent/tt-xla/8.1-development-environment-setup)**: Virtual environment configuration, toolchain setup, environment variables
*   **[Section 8.2: Building from Source](https://deepwiki.com/tenstorrent/tt-xla/8.2-building-from-source)**: CMake configuration, dependency management, wheel packaging
*   **[Section 8.3: Running and Debugging Tests](https://deepwiki.com/tenstorrent/tt-xla/8.3-running-and-debugging-tests)**: Test execution, --serialize flag, FileCheck validation
*   **[Section 8.4: Debugging Compilation and Performance](https://deepwiki.com/tenstorrent/tt-xla/8.4-debugging-compilation-and-performance)**: IR export, Tracy profiling, TTNN metrics, logging configuration

For information on testing infrastructure architecture, see [Testing Infrastructure](https://deepwiki.com/tenstorrent/tt-xla/6-testing-infrastructure). For build system details, see [Build System](https://deepwiki.com/tenstorrent/tt-xla/3-build-system).

**Sources**: [docs/src/getting_started_build_from_source.md 1-211](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L1-L211)[docs/src/test_infra.md 1-155](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/test_infra.md?plain=1#L1-L155)

* * *

## Compiler Options Reference

The `CompileOptions` struct controls all aspects of model compilation. These options can be set from JAX, PyTorch, or directly through the PJRT API.

### Core Compilation Options

**PyTorch Example**:

`import torch_xlatorch_xla.set_custom_compile_options({    "optimization_level": "2",    "enable_bfp8_conversion": "true",    "math_fidelity": "hifi4",    "export_path": "./debug_ir",    "export_model_name": "my_model_v1",})`
**JAX Example**:

`import jax@jax.jit(compiler_options={    "optimization_level": "1",    "enable_trace": "true",    "export_path": "/tmp/ir_export",})def my_function(x):    return x @ x.T`
**Sources**: [pjrt_implementation/inc/api/compile_options.h 15-157](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/inc/api/compile_options.h#L15-L157)[tests/infra/testers/compiler_config.py 9-143](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/testers/compiler_config.py#L9-L143)

* * *

## IR Export and Debugging Workflow

One of the most powerful debugging tools is the ability to export intermediate representations (IRs) at each stage of compilation. This allows inspection of transformations applied to your model.

### IR Export Stages

**Filename Convention**: `<stage>_<model_name>_g<graph_number>_<timestamp>.mlir`

*   **stage**: `vhlo`, `shlo`, `shlo_frontend`, `shlo_compiler`, `ttir`, `ttnn`
*   **model_name**: User-provided identifier from `export_model_name`
*   **graph_number**: Auto-incremented counter (g0, g1, g2...), resets when model_name changes
*   **timestamp**: Milliseconds since epoch for uniqueness

**Graph Counter Behavior**:

*   Each compilation increments the counter: g0, g1, g2...
*   When `export_model_name` changes, counter resets to 0
*   Useful for distinguishing forward (g0) vs backward (g1) passes

**Example: PyTorch Training**:

`torch_xla.set_custom_compile_options({    "export_path": "./training_debug",    "export_model_name": "resnet_bs32",}) # Forward pass creates g0 filesoutput = model(input)torch_xla.sync() # Backward pass creates g1 filesloss.backward()torch_xla.sync()`
This will generate files like:

```
training_debug/irs/
  vhlo_resnet_bs32_g0_1704123456789.mlir      # Forward VHLO
  ttnn_resnet_bs32_g0_1704123456790.mlir      # Forward TTNN
  vhlo_resnet_bs32_g1_1704123456791.mlir      # Backward VHLO
  ttnn_resnet_bs32_g1_1704123456792.mlir      # Backward TTNN
```

**Sources**: [pjrt_implementation/src/api/module_builder/module_builder.cc 228-238](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/src/api/module_builder/module_builder.cc#L228-L238)[examples/pytorch/export_ir_example.py 1-68](https://github.com/tenstorrent/tt-xla/blob/c77995f6/examples/pytorch/export_ir_example.py#L1-L68)[tests/torch/test_export_ir_naming.py 1-68](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/torch/test_export_ir_naming.py#L1-L68)

* * *

## IR Stage Descriptions

Each compilation stage produces a different IR dialect, capturing progressive lowering from high-level framework operations to hardware-specific instructions.

| Stage | Dialect | Description | Key Transformations |
| --- | --- | --- | --- |
| `vhlo` | VHLO (Versioned StableHLO) | Serialized format from JAX/PyTorch | None (deserialized input) |
| `shlo` | StableHLO | Framework-independent ML ops | VHLO deserialization |
| `shlo_frontend` | StableHLO | Frontend-specific annotations | Input role propagation, argument attributes |
| `shlo_compiler` | StableHLO | Optimized StableHLO | Shardy/GSPMD sharding, StableHLO pipeline passes |
| `ttir` | TTIR (Tenstorrent IR) | Device-agnostic TT ops | StableHLO → TTIR lowering, mesh configuration |
| `ttnn` | TTNN | Device-specific neural network ops | TTIR → TTNN lowering, optimization passes, backend selection |

**Compiler Pipeline Code Locations**:

*   VHLO → SHLO: [pjrt_implementation/src/api/module_builder/module_builder.cc 401-419](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/src/api/module_builder/module_builder.cc#L401-L419)
*   Frontend passes: [pjrt_implementation/src/api/module_builder/module_builder.cc 421-432](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/src/api/module_builder/module_builder.cc#L421-L432)
*   Compiler StableHLO pipeline: [pjrt_implementation/src/api/module_builder/module_builder.cc 752-778](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/src/api/module_builder/module_builder.cc#L752-L778)
*   SHLO → TTIR: [pjrt_implementation/src/api/module_builder/module_builder.cc 780-806](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/src/api/module_builder/module_builder.cc#L780-L806)
*   TTIR → TTNN: [pjrt_implementation/src/api/module_builder/module_builder.cc 898-1025](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/src/api/module_builder/module_builder.cc#L898-L1025)

**Sources**: [pjrt_implementation/src/api/module_builder/module_builder.cc 217-380](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/src/api/module_builder/module_builder.cc#L217-L380)

* * *

## Performance Profiling Options

Performance analysis in tt-xla involves multiple layers: host-side tracing with Tracy, device-side metrics from TTNN, and memory logging from tt-metal runtime.

### Tracy Profiler Integration

Enable Tracy zones to measure host-side overhead in the PJRT plugin:

`# Build with Tracy supportcmake -G Ninja -B build -DTTXLA_TRACY_ZONES=ON # Run Tracy server (separate terminal)tracy # Run your workloadpython my_model.py`
Tracy zones are inserted at key PJRT API boundaries to measure:

*   Compilation time in `ModuleBuilder::buildModule`
*   Buffer creation and data transfer
*   Execution dispatch overhead
*   Device synchronization

**Sources**: [CMakeLists.txt 61](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L61-L61)

### TTNN Performance Metrics

TTNN performance metrics capture device-level operation statistics including execution time, memory bandwidth, and kernel utilization:

`torch_xla.set_custom_compile_options({    "ttnn_perf_metrics_enabled": "true",    "ttnn_perf_metrics_output_file": "./perf_metrics.json",})`
**Graph Auto-Numbering**: When `ttnn_perf_metrics_enabled` is true and an output file is specified, the system automatically appends a graph index to the filename:

*   `perf_metrics.json` → `perf_metrics_0.json`, `perf_metrics_1.json`, etc.
*   Counter increments for each compilation
*   Prevents overwriting metrics from multiple graphs

**Metrics JSON Structure**:

`{  "operations": [    {      "name": "ttnn.matmul",      "execution_time_us": 1234,      "memory_bandwidth_gb_s": 45.2,      "device_utilization": 0.87    }  ]}`
**Sources**: [pjrt_implementation/src/api/module_builder/module_builder.cc 942-971](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/src/api/module_builder/module_builder.cc#L942-L971)

### Memory and Logging Configuration

Control tt-metal runtime logging verbosity through environment variables:

`# Error-level logging (default)export TT_METAL_LOGGER_LEVEL=ERROR # Info-level logging (shows device allocation)export TT_METAL_LOGGER_LEVEL=INFO # Debug-level logging (detailed operation traces)export TT_METAL_LOGGER_LEVEL=DEBUG`
Additional debugging environment variables:

*   `LOGURU_DEBUG_LOGGING=1`: Enable verbose PJRT plugin logging (set automatically in Debug builds)
*   `TT_RUNTIME_DEBUG=ON`: Enable tt-mlir runtime debugging (CMake option)

**Sources**: [venv/activate 38](https://github.com/tenstorrent/tt-xla/blob/c77995f6/venv/activate#L38-L38)[CMakeLists.txt 41-44](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L41-L44)[CMakeLists.txt 84-92](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L84-L92)

* * *

## Common Development Workflows

### Building from Source

`# 1. Activate environmentsource venv/activate # 2. Configure with CMakecmake -G Ninja -B build \  -DCMAKE_BUILD_TYPE=Release \  -DTTXLA_ENABLE_PJRT_TESTS=ON # 3. Buildcmake --build build # 4. Install to local directorycmake --install build --prefix ./install # 5. Build Python wheelcd python_packagepython setup.py bdist_wheel --build-type release`
The wheel will be generated in `python_package/dist/` with embedded native binaries, tt-metal runtime, and all shared libraries.

**Sources**: [CMakeLists.txt 27-102](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L27-L102)[python_package/setup.py 262-333](https://github.com/tenstorrent/tt-xla/blob/c77995f6/python_package/setup.py#L262-L333)

### Debugging a Compilation Issue

**Workflow Example**:

1.   **Enable IR Export**:

`torch_xla.set_custom_compile_options({    "export_path": "./debug",    "export_model_name": "problematic_model",})`
1.   **Trigger Compilation**:

`output = model(input)torch_xla.sync()`
1.   **Examine Generated IRs**:

`ls debug/irs/# vhlo_problematic_model_g0_*.mlir# shlo_problematic_model_g0_*.mlir# ttir_problematic_model_g0_*.mlir# ttnn_problematic_model_g0_*.mlir`
1.   **Analyze Each Stage**:

    *   Open `vhlo_*.mlir` - Check input from framework is correct
    *   Open `shlo_*.mlir` - Check StableHLO ops are as expected
    *   Open `ttir_*.mlir` - Check device-agnostic TT ops
    *   Open `ttnn_*.mlir` - Check final device-specific ops

2.   **Correlate with Logs**: Enable verbose logging to see which pass fails:

`export LOGURU_DEBUG_LOGGING=1export TT_METAL_LOGGER_LEVEL=DEBUGpython my_model.py 2>&1 | tee compilation.log`
**Sources**: [pjrt_implementation/src/api/module_builder/module_builder.cc 217-380](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/src/api/module_builder/module_builder.cc#L217-L380)

### Testing with Custom Compiler Options

The test infrastructure provides `CompilerConfig` for programmatic control of compilation:

`from tests.infra.testers.compiler_config import CompilerConfig config = CompilerConfig(    optimization_level=2,    enable_bfp8_conversion=True,    math_fidelity="hifi4",    export_path="./test_ir",    export_model_name="test_model") # Convert to framework-specific formatjax_options = config.to_jax_compiler_options()torch_options = config.to_torch_compile_options() # Use in tests@jax.jit(compiler_options=jax_options)def test_fn(x):    return x @ x.T`
**Sources**: [tests/infra/testers/compiler_config.py 9-143](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/testers/compiler_config.py#L9-L143)

* * *

## Advanced: XLA Ingestion and Optimized Program

When using `PJRT_Executable_OptimizedProgram` to retrieve the compiled IR for inspection, the module undergoes sanitization to ensure XLA compatibility. This process strips TT-specific dialect attributes that XLA cannot parse.

### Sanitization Process

**Key Transformations**:

1.   **Attribute Stripping**: Removes `ttcore.*` and `ttir.*` attributes from function signatures
2.   **Location Removal**: Strips all `loc` attributes to reduce size and avoid XLA parser issues
3.   **Shardy Output Extraction** (when using Shardy): 
    *   Extracts `sdy.manual_computation` output shardings
    *   Converts `TensorShardingAttr` to HloShardingV2 string format
    *   Injects as `mhlo.spmd_output_sharding` module attribute
    *   Simplifies main function body (removes illegal `sdy.manual_computation`)

**HloShardingV2 Format**: `{devices=[<tile_dims>]<=<reshape_dims>]T(<transpose_perm>) last_tile_dim_replicate}`

**Sources**: [pjrt_implementation/src/api/module_builder/frontend_passes/shlo_clean_for_xla_ingestion.cc 1-431](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/src/api/module_builder/frontend_passes/shlo_clean_for_xla_ingestion.cc#L1-L431)[pjrt_implementation/inc/api/module_builder/frontend_passes/shlo_clean_for_xla_ingestion.h 1-43](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/inc/api/module_builder/frontend_passes/shlo_clean_for_xla_ingestion.h#L1-L43)

* * *

## Debugging Tools Summary

| Tool | Purpose | Configuration |
| --- | --- | --- |
| **IR Export** | Inspect compilation stages | `export_path` + `export_model_name` compiler options |
| **Tracy Profiler** | Host-side performance tracing | `TTXLA_TRACY_ZONES=ON` CMake option |
| **TTNN Metrics** | Device-side operation metrics | `ttnn_perf_metrics_enabled` + `ttnn_perf_metrics_output_file` |
| **tt-metal Logging** | Runtime execution traces | `TT_METAL_LOGGER_LEVEL` environment variable |
| **PJRT Logging** | Plugin-level debugging | `LOGURU_DEBUG_LOGGING=1` (auto-enabled in Debug builds) |
| **Code Coverage** | Measure test coverage | `CODE_COVERAGE=ON` CMake option |
| **Explorer** | Interactive IR exploration | `TTXLA_ENABLE_EXPLORER=ON` CMake option |

**Sources**: [CMakeLists.txt 46-92](https://github.com/tenstorrent/tt-xla/blob/c77995f6/CMakeLists.txt#L46-L92)[pjrt_implementation/inc/api/compile_options.h 15-157](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pjrt_implementation/inc/api/compile_options.h#L15-L157)[venv/activate 38](https://github.com/tenstorrent/tt-xla/blob/c77995f6/venv/activate#L38-L38)

Dismiss
Refresh this wiki

Enter email to refresh
