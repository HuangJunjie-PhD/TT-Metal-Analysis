---
project: tt-mlir
github: https://github.com/tenstorrent/tt-mlir
deepwiki: https://deepwiki.com/tenstorrent/tt-mlir/6-testing-and-validation
---

# Testing and Validation

Relevant source files
*   [.github/scripts/python/run_tests_from_json.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/scripts/python/run_tests_from_json.py)
*   [.github/test_scripts/builder.sh](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/test_scripts/builder.sh)
*   [.github/test_scripts/emitc.sh](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/test_scripts/emitc.sh)
*   [.github/test_scripts/pykernel.sh](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/test_scripts/pykernel.sh)
*   [.github/workflows/call-build-docs.yml](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/call-build-docs.yml)
*   [.github/workflows/on-pr.yml](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/on-pr.yml)
*   [.github/workflows/on-push.yml](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/on-push.yml)
*   [.github/workflows/schedule-nightly-model-explorer-uplift.yml](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/schedule-nightly-model-explorer-uplift.yml)
*   [.github/workflows/schedule-nightly-uplift.yml](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/schedule-nightly-uplift.yml)
*   [docs/src/builder/testing.md](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/docs/src/builder/testing.md?plain=1)
*   [docs/src/ci.md](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/docs/src/ci.md?plain=1)
*   [test/python/golden/conftest.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/conftest.py)
*   [test/python/golden/mlir_snippets/stablehlo/stablehlo_scatter.mlir](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/mlir_snippets/stablehlo/stablehlo_scatter.mlir)
*   [test/python/golden/pytest.ini](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/pytest.ini)
*   [test/python/golden/test_shardy_ops.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/test_shardy_ops.py)
*   [test/python/golden/test_stablehlo_ops.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/test_stablehlo_ops.py)
*   [test/python/golden/test_ttir_models.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/test_ttir_models.py)
*   [test/python/golden/test_ttir_ops.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/test_ttir_ops.py)
*   [test/python/golden/ttnn_ops/eltwise/test_ttnn_binary.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/ttnn_ops/eltwise/test_ttnn_binary.py)
*   [test/python/golden/ttnn_ops/eltwise/test_ttnn_unary.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/ttnn_ops/eltwise/test_ttnn_unary.py)
*   [tools/builder/README.md](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/README.md?plain=1)
*   [tools/builder/base/builder.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder.py)
*   [tools/builder/base/builder_apis.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_apis.py)
*   [tools/builder/base/builder_runtime.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_runtime.py)
*   [tools/builder/base/builder_utils.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_utils.py)
*   [tools/builder/d2m/d2m_builder.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/d2m/d2m_builder.py)
*   [tools/builder/stablehlo/stablehlo_builder.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/stablehlo/stablehlo_builder.py)
*   [tools/builder/ttir/ttir_builder.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/ttir/ttir_builder.py)
*   [tools/builder/ttnn/ttnn_builder.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/ttnn/ttnn_builder.py)
*   [tools/golden/mapping.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/golden/mapping.py)

This page provides an overview of the testing infrastructure in `tt-mlir`, covering how tests are structured, how correctness is verified, and how the CI/CD system executes them. The four major components are: the Python **builder framework** for programmatic test construction, **golden comparison** for numerical correctness, **lit tests** for MLIR transformation verification, and **GitHub Actions** for automation.

For detailed coverage of each subsystem, see the child pages:

*   [Builder Framework and Golden Testing](https://deepwiki.com/tenstorrent/tt-mlir/6.1-builder-framework-and-golden-testing) — Document the builder API (`TTIRBuilder`, `StableHLOBuilder`, `TTNNBuilder`, `D2MBuilder`) and golden tensor validation system including `GoldenMapTensor`, PCC verification, and multi-device simulation.
*   [OpModel Testing and Validation](https://deepwiki.com/tenstorrent/tt-mlir/6.2-opmodel-testing-and-validation) — Explain OpModel unit tests, mock devices, constraint validation testing, shard solver tests, and scheduler tests.
*   [CI/CD Pipeline and Automation](https://deepwiki.com/tenstorrent/tt-mlir/6.3-cicd-pipeline-and-automation) — Detail the GitHub Actions workflows for PR validation, nightly uplifts, dependency management, downstream testing, wheel builds, and test matrix generation.
*   [Lit Tests and MLIR Verification](https://deepwiki.com/tenstorrent/tt-mlir/6.4-lit-tests-and-mlir-verification) — Describe FileCheck-based lit tests for verifying MLIR transformations and dialect conversions, including Silicon tests, EmitC tests, and the lit configuration.

For the runtime system that executes compiled binaries during test runs, see [Runtime System](https://deepwiki.com/tenstorrent/tt-mlir/4-runtime-system).

* * *

## Testing Infrastructure Overview

The testing infrastructure spans three layers: MLIR-level structural verification (lit tests), Python-driven compile-and-execute tests (builder tests), and hardware CI automation.

**Testing layers and their relationships:**

Sources: [tools/builder/base/builder_apis.py 50-127](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_apis.py#L50-L127)[tools/builder/base/builder_runtime.py 21-34](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_runtime.py#L21-L34)[.github/workflows/on-pr.yml 67-83](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/on-pr.yml#L67-L83)

* * *

## Builder-Based Tests

The builder tests are pytest suites that programmatically construct MLIR modules, compile them through the backend pipeline, execute the result on hardware, and compare outputs against PyTorch reference values.

### Builder Class Hierarchy

**Builder class relationships and key methods:**

Sources: [tools/builder/base/builder.py 38-117](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder.py#L38-L117)[tools/builder/ttir/ttir_builder.py 25-156](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/ttir/ttir_builder.py#L25-L156)[tools/builder/stablehlo/stablehlo_builder.py 26-205](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/stablehlo/stablehlo_builder.py#L26-L205)[tools/builder/ttnn/ttnn_builder.py 22-137](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/ttnn/ttnn_builder.py#L22-L137)

### Compile-and-Execute Flow

Each test defines a `module` function that accepts a builder and calls `@builder.func(...)` to generate an MLIR function. The top-level entry points are:

| API | Source | Use case |
| --- | --- | --- |
| `compile_and_execute_ttir` | `builder_apis.py` | Tests written at TTIR level |
| `compile_and_execute_shlo` | `builder_apis.py` | Tests written at StableHLO level |
| `build_module` | `builder_apis.py` | Build only, no execution |

The internal `_compile_and_execute` function dispatches execution based on the target:

*   `execute_fb(...)` — for `ttnn` and `ttmetal` targets (flatbuffer execution) [tools/builder/base/builder_apis.py 105-123](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_apis.py#L105-L123)
*   `execute_py(...)` — for the `emitpy` target [tools/builder/base/builder_apis.py 125-138](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_apis.py#L125-L138)
*   `execute_cpp(...)` — for the `emitc` target [tools/builder/base/builder_apis.py 140-157](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_apis.py#L140-L157)

**Supported test targets:**

| Target | Backend pipeline | Execution |
| --- | --- | --- |
| `ttnn` | `ttir_to_ttnn_runtime_pipeline` | `_ttmlir_runtime` flatbuffer |
| `ttmetal` | `ttir_to_ttmetal_backend_pipeline` | `_ttmlir_runtime` flatbuffer |
| `emitpy` | `ttir_to_emitpy_pipeline` | subprocess Python execution |
| `emitc` | `translate_to_cpp` | compiled C++ binary |

Sources: [tools/builder/base/builder_apis.py 50-157](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_apis.py#L50-L157)[tools/builder/base/builder_utils.py 20-32](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_utils.py#L20-L32)[tools/builder/base/builder_utils.py 146-182](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_utils.py#L146-L182)

### Test Structure Pattern

Tests in [test/python/golden/test_ttir_ops.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/test_ttir_ops.py) follow a consistent structure:

The `@builder.func` decorator registers the function's inputs and outputs for golden bookkeeping. Each builder method is annotated with a `@tag(Dialect.OpName)` decorator ([tools/builder/base/builder_utils.py 115-120](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_utils.py#L115-L120)) that associates it with its MLIR op class.

Sources: [test/python/golden/test_ttir_ops.py 54-71](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/test_ttir_ops.py#L54-L71)[tools/builder/base/builder_utils.py 115-120](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_utils.py#L115-L120)

* * *

## Golden Comparison

Each builder op method automatically computes a PyTorch reference output alongside the MLIR op. These "golden" values are stored in the `Builder._goldens` dict ([tools/builder/base/builder.py 80](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder.py#L80-L80)), keyed by `Operand` (MLIR value).

### GoldenMapTensor

The `GoldenMapTensor` class ([tools/golden/mapping.py 36-47](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/golden/mapping.py#L36-L47)) manages golden values for single or multi-device tensors. It represents a dictionary of `torch.Tensor` objects, each representing a shard.

Key properties and methods:

| Member | Purpose |
| --- | --- |
| `_shard_map` | `Dict[int, torch.Tensor]` — per-device shard storage [tools/golden/mapping.py 115](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/golden/mapping.py#L115-L115) |
| `golden_map_tensor_as_torch_tensors()` | Export shards as plain `torch.Tensor`, handling dtype normalization and memory contiguity [tools/golden/mapping.py 138-153](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/golden/mapping.py#L138-L153) |
| `__torch_function__` | Implements the protocol to apply torch functions independently to each shard [tools/golden/mapping.py 42-46](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/golden/mapping.py#L42-L46) |
| `_binary_map` | Utility for mapping binary operations across shards [tools/golden/mapping.py 164-173](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/golden/mapping.py#L164-L173) |

Sources: [tools/golden/mapping.py 36-173](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/golden/mapping.py#L36-L173)

### Golden Function Mapping

The `mapping.py` module provides centralized mapping between TTIR/StableHLO operations and their corresponding PyTorch golden reference implementations ([tools/golden/mapping.py 5-21](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/golden/mapping.py#L5-L21)).

Example of manual golden intervention in tests:

*   In `test_div`, the test manually calculates `output_golden = torch.div(dividend_tensor, divisor_tensor)` and calls `builder.set_goldens_from_builder_tensor` ([test/python/golden/test_ttir_ops.py 160-164](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/test_ttir_ops.py#L160-L164)).
*   In `logical_not`, the golden is explicitly set to handle boolean-to-input-dtype conversion ([test/python/golden/test_ttir_ops.py 29-44](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/test_ttir_ops.py#L29-L44)).

Sources: [tools/golden/mapping.py 5-21](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/golden/mapping.py#L5-L21)[test/python/golden/test_ttir_ops.py 160-164](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/test_ttir_ops.py#L160-L164)[test/python/golden/test_ttir_ops.py 29-44](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/python/golden/test_ttir_ops.py#L29-L44)

### Numerical Comparison

After executing a compiled flatbuffer, outputs are compared against goldens. The `_compile_and_execute` function takes `pcc`, `atol`, and `rtol` parameters to define the pass/fail criteria for numerical validation.

| Metric | Purpose |
| --- | --- |
| **PCC** | Pearson Correlation Coefficient — primary measure of numerical similarity [tools/builder/base/builder_runtime.py 200-214](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_runtime.py#L200-L214) |
| **atol** | Absolute tolerance [tools/builder/base/builder_runtime.py 200-224](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_runtime.py#L200-L224) |
| **rtol** | Relative tolerance [tools/builder/base/builder_runtime.py 200-230](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_runtime.py#L200-L230) |

Sources: [tools/builder/base/builder_apis.py 50-157](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_apis.py#L50-L157)[tools/builder/base/builder_runtime.py 188-230](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_runtime.py#L188-L230)

* * *

## CI/CD Pipeline and Automation

The project uses GitHub Actions for continuous integration, managing PR validation and scheduled maintenance.

### PR Validation (`on-pr.yml`)

Triggered on PR open, synchronize, or reopen ([.github/workflows/on-pr.yml 14-16](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/on-pr.yml#L14-L16)).

*   **Linting**: Runs pre-commit hooks and `call-lint.yml` ([.github/workflows/on-pr.yml 29-31](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/on-pr.yml#L29-L31)[.github/workflows/on-pr.yml 60-66](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/on-pr.yml#L60-L66)).
*   **Builds**: Executes Release and Debug builds ([.github/workflows/on-pr.yml 42-59](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/on-pr.yml#L42-L59)).
*   **Testing**: Runs the test matrix via `call-test.yml` and `ttsim-test` ([.github/workflows/on-pr.yml 67-83](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/on-pr.yml#L67-L83)).
*   **Downstream Checks**: When a PR is on an `uplift` branch, it triggers tests in `tt-forge-onnx` and `tt-xla` ([.github/workflows/on-pr.yml 85-125](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/on-pr.yml#L85-L125)).

### Nightly Uplifts

Automated workflows manage the synchronization with upstream dependencies:

*   **Nightly Uplift**: Daily PR creation to update the `tt-metal` submodule to the latest version via `schedule-nightly-uplift.yml`.
*   **Model Explorer Uplift**: Scheduled updates for the `tt-explorer` visualization tool via `schedule-nightly-model-explorer-uplift.yml`.

Sources: [.github/workflows/on-pr.yml 1-146](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/.github/workflows/on-pr.yml#L1-L146)

* * *

## Lit Tests and MLIR Verification

Lit tests provide low-level verification of MLIR transformations using `FileCheck`. These tests verify that specific passes produce the expected IR structure.

Key utilities in `builder_utils.py` support this by providing wrappers for pass managers:

*   `create_custom_ttir_pipeline_fn`: Parses and runs a custom string-based pipeline ([tools/builder/base/builder_utils.py 190-232](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_utils.py#L190-L232)).
*   `run_ttir_pipeline`: Orchestrates the execution of backend pipelines with system descriptors and mesh shapes ([tools/builder/base/builder_utils.py 235-242](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_utils.py#L235-L242)).

Sources: [tools/builder/base/builder_utils.py 190-242](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/builder/base/builder_utils.py#L190-L242)

Dismiss
Refresh this wiki

Enter email to refresh
