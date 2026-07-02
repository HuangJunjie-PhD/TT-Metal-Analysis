---
project: tt-torch
github: https://github.com/tenstorrent/tt-torch
deepwiki: https://deepwiki.com/tenstorrent/tt-torch/6-developer-tools
---

# Developer Tools

Relevant source files
*   [.github/workflows/run-tt-forge-models-tests.yml](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/run-tt-forge-models-tests.yml)
*   [.github/workflows/tt-forge-models-tests.yml](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/workflows/tt-forge-models-tests.yml)
*   [.test_durations](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.test_durations)
*   [README.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/README.md?plain=1)
*   [docs/src/adding_models.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/adding_models.md?plain=1)
*   [docs/src/controlling.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/controlling.md?plain=1)
*   [docs/src/overview.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/overview.md?plain=1)
*   [docs/src/pre_commit.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/pre_commit.md?plain=1)
*   [docs/src/test.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/test.md?plain=1)
*   [tests/runner/conftest.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/runner/conftest.py)
*   [tests/runner/test_config.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/runner/test_config.py)
*   [tests/runner/test_models.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/runner/test_models.py)
*   [tests/runner/test_utils.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/runner/test_utils.py)

> **NOTE:** TT-Torch is deprecated. To work with PyTorch and the various features available in TT-Torch, please see the documentation for [TT-XLA](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/TT-XLA)

This page provides an overview of developer tools, workflows, and best practices for working on tt-torch. It covers the essential utilities for debugging compilation issues, analyzing model performance, adding new model tests, and contributing to the project.

## Overview

tt-torch provides a comprehensive development ecosystem organized into three main categories:

1.   **Results Analysis and Reporting** (see page 6.1) - Tools for analyzing op-by-op compilation results and generating detailed reports
2.   **Adding New Models** (see page 6.2) - Framework and guidelines for implementing and testing new models
3.   **Profiling and Debugging** (see page 6.3) - Performance profiling tools and debugging utilities

This page serves as an entry point to these developer tools, providing context on the development environment and common workflows.

## Development Environment

**Developer Dependencies and Tools**

The development environment requires:

*   Python 3.11 with venv support
*   TTMLIR toolchain installed at `/opt/ttmlir-toolchain/` (or `$TTMLIR_TOOLCHAIN_DIR`)
*   PyTorch 2.7.0 and torch-xla dependencies
*   Developer tools: pytest, black, pre-commit

**Environment Activation:**

`source env/activate`
This script configures:

*   `TT_TORCH_HOME` - Project root directory
*   `LD_LIBRARY_PATH` - Shared library paths
*   `PYTHONPATH` - Python module search paths
*   `TT_METAL_HOME` - tt-metal source location
*   `ARCH_NAME` - Target architecture (default: `wormhole_b0`)

Sources: [env/activate 1-52](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/env/activate#L1-L52)[requirements.txt 1-62](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt#L1-L62)[setup.py 1-234](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L1-L234)

## Results Analysis and Reporting

tt-torch provides tools for analyzing compilation results and generating reports to track model support progress. The primary tool is `parse_op_by_op_results.py`, which processes JSON files from op-by-op testing to create Excel reports showing per-operation compilation status.

**Results Analysis Workflow**

**Compilation Status Stages:**

1.   `NOT_STARTED` → 2. `CREATED_GRAPH` → 3. `CONVERTED_TO_TORCH_IR` → 4. `CONVERTED_TO_TORCH_BACKEND_IR` → 5. `CONVERTED_TO_STABLE_HLO` → 6. `CONVERTED_TO_TTIR` → 7. `CONVERTED_TO_TTNN` → 8. `EXECUTED`

The analysis tools help identify which operations are failing at each compilation stage, enabling targeted debugging efforts.

**See page 6.1 for detailed documentation on results analysis and reporting tools.**

Sources: [docs/src/adding_models.md 242-295](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/adding_models.md?plain=1#L242-L295)

## Adding New Models

The model testing framework uses the `ModelTester` base class to provide a standardized approach for testing PyTorch and ONNX models. Developers implement three key methods: `_load_model()`, `_load_inputs()`, and optionally `_extract_outputs()`.

**Model Test Structure**

**Basic Test Template:**

`from tests.utils import ModelTesterfrom tt_torch.tools.utils import CompilerConfig, CompileDepth class ThisTester(ModelTester):    def _load_model(self):        # Load model from file or instantiate        return model        def _load_inputs(self):        # Generate or load input tensors        return inputs def test_model_name(record_property, mode, op_by_op):    cc = CompilerConfig()    cc.enable_consteval = True        tester = ThisTester("ModelName", mode, compiler_config=cc)    results = tester.test_model()    tester.finalize()`
**Loading Test Files from S3:** Use the `get_file()` function from tt-forge-models to load model files from the S3-backed cache:

`from third_party.tt_forge_models.tools.utils import get_file file = get_file("test_files/pytorch/model/model.pt")`
Files are automatically cached locally and synced from S3 on CI runners.

**See page 6.2 for comprehensive documentation on adding new models, including CI integration strategies.**

Sources: [docs/src/adding_models.md 1-357](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/adding_models.md?plain=1#L1-L357)[tests/models/yolov10/test_yolov10.py 15-32](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/yolov10/test_yolov10.py#L15-L32)

## Profiling and Debugging

tt-torch includes profiling tools based on Tracy for performance analysis and environment variables for controlling compilation and debugging behavior.

**Profiling Workflow**

**Performance Profiling:**

`# Build with profiling enabledpython setup.py install --build_perf # Profile a test using tt_profile commandtt_profile "pytest -svv tests/models/resnet/test_resnet.py::test_resnet[single_device-full-eval]"`
The `tt_profile` command is registered as a console script entry point in `setup.py` and wraps the Tracy capture/export workflow.

**Debugging Environment Variables:**

| Variable | Purpose | Default |
| --- | --- | --- |
| `TT_TORCH_COMPILE_DEPTH` | Compilation depth (TORCH_FX, STABLEHLO, TTIR, TTNN_IR, EXECUTE) | `EXECUTE` |
| `TT_TORCH_VERIFY_OP_BY_OP` | Verify each op against PyTorch golden outputs | `False` |
| `TT_TORCH_VERIFY_INTERMEDIATES` | Verify runtime intermediate tensors | `False` |
| `TT_TORCH_CONSTEVAL` | Enable constant folding in FX graph | `False` |
| `TT_TORCH_CONSTEVAL_PARAMETERS` | Include model parameters in consteval | `False` |
| `TT_TORCH_IR_LOG_LEVEL` | MLIR IR logging (INFO shows all stages, DEBUG shows all passes) | Disabled |

These can also be set programmatically via `CompilerConfig`:

`from tt_torch.tools.utils import CompilerConfig, CompileDepth cc = CompilerConfig()cc.compile_depth = CompileDepth.EXECUTE_OP_BY_OPcc.enable_consteval = Truecc.verify_op_by_op = True`
**See page 6.3 for comprehensive documentation on profiling tools and debugging techniques.**

Sources: [tt_torch/tools/profile_util.py 1-349](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/profile_util.py#L1-L349)[docs/src/controlling.md 1-48](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/controlling.md?plain=1#L1-L48)[setup.py 173-183](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L173-L183)[setup.py 225-233](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L225-L233)

## Build System and Dependencies

**Build System Components**

**Build Configurations:**

The build system supports several configuration options via command-line flags:

| Flag | Purpose | CMake Variable |
| --- | --- | --- |
| `--code_coverage` | Enable code coverage reporting | `CODE_COVERAGE=ON` |
| `--build_perf` | Enable performance profiling | `TT_RUNTIME_ENABLE_PERF_TRACE=ON` |
| `--build_runtime_debug` | Enable runtime debug hooks | `TT_RUNTIME_DEBUG=ON` |
| `--build_op_model` | Enable operation model | `TTMLIR_ENABLE_OPMODEL=ON` |
| `--include-models` | Include tt-forge-models in wheel | N/A |

**Build from Source:**

`source env/activatecmake -G Ninja -B buildcmake --build buildpython setup.py install`
**Install from Wheel:**

`pip install tt-torch`
The wheel installation includes all necessary shared libraries and the `tt_mlir` Python module.

Sources: [setup.py 1-234](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py#L1-L234)[requirements.txt 1-62](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt#L1-L62)[tt_torch/csrc/CMakeLists.txt 1-90](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/CMakeLists.txt#L1-L90)

## Pre-Commit Hooks

tt-torch uses pre-commit hooks to enforce code quality standards. Install hooks after activating the environment:

`source env/activatepre-commit install`
This applies Git hooks that run on every commit to check:

*   Code formatting with `black`
*   License headers (SPDX format)
*   File size limits
*   Trailing whitespace and line endings

Run pre-commit on all files:

`pre-commit run --all-files`
**SPDX License Header:** All new files must include:

`# SPDX-FileCopyrightText: (c) 2025 Tenstorrent AI ULC## SPDX-License-Identifier: Apache-2.0`
Sources: [docs/src/pre_commit.md 1-20](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/pre_commit.md?plain=1#L1-L20)[README.md 46-48](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/README.md?plain=1#L46-L48)

## Testing Infrastructure

**Test Organization**

**Running Tests:**

`# Single test filepytest -svv tests/torch/test_basic.py # Specific test functionpytest -svv tests/models/resnet/test_resnet.py::test_resnet[single_device-full-eval] # All tests in directorypytest tests/models/ # With coveragepytest --cov=tt_torch tests/ # Parallel executionpytest -n auto tests/torch/`
**Test Discovery:**

`# List all tests without runningpytest --collect-only tests/models/resnet/`
Sources: [docs/src/test.md 1-174](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/test.md?plain=1#L1-L174)[requirements.txt 21-29](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt#L21-L29)

## Development FAQ

### How do I debug compilation issues?

Use the op-by-op flow with `CompileDepth.EXECUTE_OP_BY_OP` to identify which operations are failing to compile or execute.

### How do I add support for a new operation?

1.   Check if the operation can be decomposed into existing operations
2.   If so, add a custom decomposition
3.   If not, you may need to implement support in the TTIR/TTNN layer

### How do I profile the performance of my model?

Use the profiling tools described in the Documentation and Tools section to measure operation performance on Tenstorrent hardware.

### How do I test my changes to the compiler?

Run the existing test suite with your changes to ensure compatibility, and add new tests for your specific modifications.

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-torch/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh