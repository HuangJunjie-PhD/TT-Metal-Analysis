---
project: tt-forge
github: https://github.com/tenstorrent/tt-forge
deepwiki: https://deepwiki.com/tenstorrent/tt-forge/6-development-guide
---

# Development Guide

Relevant source files
*   [.github/check-spdx.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/check-spdx.yml)
*   [.github/workflows/pages.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/pages.yml)
*   [.github/workflows/pre-commit.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/pre-commit.yml)
*   [.gitignore](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.gitignore)
*   [.pre-commit-config.yaml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.pre-commit-config.yaml)
*   [CONTRIBUTING.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CONTRIBUTING.md?plain=1)
*   [docs/.gitignore](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/.gitignore)
*   [docs/book.toml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/book.toml)
*   [docs/src/SUMMARY.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/SUMMARY.md?plain=1)
*   [docs/src/model-bring-up-guide.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1)

This guide provides practical instructions for developers working with the TT-Forge codebase. It covers local benchmark execution, test configuration, demo testing, and model bring-up workflows. For information about the CI/CD release system, see [CI/CD and Release System](https://deepwiki.com/tenstorrent/tt-forge/5-cicd-and-release-system). For details about the benchmarking infrastructure architecture, see [Benchmarking System](https://deepwiki.com/tenstorrent/tt-forge/3-benchmarking-system). For the complete testing infrastructure overview, see [Testing Infrastructure](https://deepwiki.com/tenstorrent/tt-forge/4-testing-infrastructure).

* * *

## Model Bring-Up

Bringing up a model on Tenstorrent hardware involves a pipeline from high-level frameworks (PyTorch/JAX) through the TT-MLIR compiler to the TT-Metalium runtime.

### The Compilation Pipeline

**Sources:**[docs/src/model-bring-up-guide.md 26-65](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L26-L65)[CONTRIBUTING.md 3-11](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CONTRIBUTING.md?plain=1#L3-L11)

For a comprehensive guide on porting HuggingFace models, debugging common issues, and optimizing performance, see [Model Bring-Up Guide](https://deepwiki.com/tenstorrent/tt-forge/6.4-model-bring-up-guide).

* * *

## Running Benchmarks Locally

### Benchmark Execution Overview

The TT-Forge repository provides two primary benchmark execution paths: **torch-xla benchmarks** for the PyTorch/JAX frontend, and **forge.compile benchmarks** for the TVM-based frontend.

**Sources:**[.github/workflows/perf-benchmark.yml 1-266](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/perf-benchmark.yml#L1-L266)[docs/src/model-bring-up-guide.md 99-130](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L99-L130)

### Direct Pytest Execution

Benchmark scripts can be executed directly using `pytest`. Each benchmark file contains parameterized test functions.

#### Basic Execution Pattern

`# Run a single benchmark with specific parameterspytest benchmark/tt-xla/resnet.py::test_resnet_torch_xla \  -k "batch_size=8" \  -k "loop_count=4"`
#### Required Environment Variables

| Environment Variable | Purpose | Typical Value |
| --- | --- | --- |
| `PJRT_DEVICE` | PJRT backend selection | `"TT"` |
| `XLA_STABLEHLO_COMPILE` | Enable StableHLO compilation | `"1"` |
| `TT_METAL_HOME` | TTMetal installation path | Path to library |

**Sources:**[docs/src/model-bring-up-guide.md 103-110](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L103-L110)[docs/src/model-bring-up-guide.md 204-210](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L204-L210)

For detailed instructions on local execution, parameter configuration, and interpreting results, see [Running Benchmarks](https://deepwiki.com/tenstorrent/tt-forge/6.1-running-benchmarks).

* * *

## Running Demo Tests

Demo tests execute real-world model demonstrations to validate end-to-end functionality. Unlike benchmarks that measure performance, demos verify that models can be loaded, compiled, and executed correctly.

### Demo Execution Flow

1.   **Checkout**: Source code and submodules.
2.   **Install Dependencies**: Defined in `pyreq` (Python) or `libreq` (System) fields.
3.   **Environment Setup**: Configures `TT_METAL_HOME` and `HF_HOME`.
4.   **Execution**: Runs the demo script from the `demos/` directory.

**Sources:**[.github/workflows/demo-tests.yml 1-238](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/demo-tests.yml#L1-L238)[.github/workflows/demo-tests.yml 136-159](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/demo-tests.yml#L136-L159)

For instructions on running demo tests and understanding the demo test infrastructure, see [Running Demo Tests](https://deepwiki.com/tenstorrent/tt-forge/6.2-running-demo-tests).

* * *

## Testing and Validation

The testing infrastructure consists of three levels:

1.   **Basic Tests**: Quick smoke tests (`basic-tests.yml`) for PR validation.
2.   **Demo Tests**: Comprehensive model-level validation (`demo-tests.yml`).
3.   **Performance Benchmarks**: Long-running performance validation (`perf-benchmark.yml`).

### Test Matrix Configuration

The repository uses `perf-bench-matrix.json` to define test configurations and `filter-test-matrix.py` to select specific subsets based on project or hardware architecture (e.g., `n150` for Wormhole, `p150` for Blackhole).

**Sources:**[.github/workflows/perf-bench-matrix.json 1-192](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/perf-bench-matrix.json#L1-L192)[.github/workflows/perf-benchmark.yml 109-143](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/perf-benchmark.yml#L109-L143)

For a guide on running various test suites and validating changes, see [Testing and Validation](https://deepwiki.com/tenstorrent/tt-forge/6.3-testing-and-validation).

* * *

## Contributing to TT-Forge

### General Workflow

1.   **Fork and Clone**: Start by forking the relevant repository (TT-MLIR, TT-XLA, etc.).
2.   **Environment Setup**: Build the project and set up the environment.
3.   **Coding Guidelines**: Follow the specific guidelines for the sub-project (e.g., `TT-MLIR Coding Guidelines`).
4.   **Error Messages**: Use the `TT_FATAL` macro with descriptive, actionable messages.
5.   **Legal**: Every file must include the appropriate SPDX header (Apache-2.0).

**Sources:**[CONTRIBUTING.md 28-57](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CONTRIBUTING.md?plain=1#L28-L57)[CONTRIBUTING.md 132-149](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CONTRIBUTING.md?plain=1#L132-L149)

### Error Message Principles

When writing C++ code, ensure error messages are:

*   **Specific**: Include actual vs. expected values.
*   **Actionable**: Tell the user how to fix the issue.
*   **Contextual**: Explain why the condition is important.

`// Example of a good error messageTT_FATAL(head_size % TILE_WIDTH == 0,         "Invalid head size: {}. The head size must be a multiple of the tile width ({}).",         head_size, TILE_WIDTH);`
**Sources:**[CONTRIBUTING.md 58-121](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CONTRIBUTING.md?plain=1#L58-L121)

Dismiss
Refresh this wiki

Enter email to refresh
