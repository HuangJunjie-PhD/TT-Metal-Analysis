---
project: tt-xla
github: https://github.com/tenstorrent/tt-xla
deepwiki: https://deepwiki.com/tenstorrent/tt-xla/6-testing-infrastructure
---

# Testing Infrastructure

Relevant source files
*   [.github/workflows/manual-test-single.yml](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.github/workflows/manual-test-single.yml)
*   [.github/workflows/test-matrix-presets/model-test-passing.json](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.github/workflows/test-matrix-presets/model-test-passing.json)
*   [.test_durations](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.test_durations)
*   [pytest.ini](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pytest.ini)
*   [tests/infra/testers/single_chip/model/model_tester.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/testers/single_chip/model/model_tester.py)
*   [tests/infra/testers/single_chip/model/torch_model_tester.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/testers/single_chip/model/torch_model_tester.py)
*   [tests/infra/utilities/failing_reasons/__init__.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/utilities/failing_reasons/__init__.py)
*   [tests/infra/utilities/failing_reasons/checks_xla.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/utilities/failing_reasons/checks_xla.py)
*   [tests/infra/utilities/failing_reasons/finder.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/utilities/failing_reasons/finder.py)
*   [tests/infra/utilities/failing_reasons/utils.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/utilities/failing_reasons/utils.py)
*   [tests/runner/test_config/jax/test_config_inference_data_parallel.yaml](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/jax/test_config_inference_data_parallel.yaml)
*   [tests/runner/test_config/jax/test_config_inference_single_device.yaml](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/jax/test_config_inference_single_device.yaml)
*   [tests/runner/test_config/jax/test_config_inference_tensor_parallel.yaml](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/jax/test_config_inference_tensor_parallel.yaml)
*   [tests/runner/test_config/jax/test_config_training_single_device.yaml](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/jax/test_config_training_single_device.yaml)
*   [tests/runner/test_config/torch/test_config_inference_data_parallel.yaml](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/torch/test_config_inference_data_parallel.yaml)
*   [tests/runner/test_config/torch/test_config_inference_single_device.yaml](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/torch/test_config_inference_single_device.yaml)
*   [tests/runner/test_config/torch/test_config_inference_tensor_parallel.yaml](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/torch/test_config_inference_tensor_parallel.yaml)
*   [tests/runner/test_config/torch/test_config_training_single_device.yaml](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/torch/test_config_training_single_device.yaml)
*   [tests/runner/test_config/torch_llm/test_config_inference_single_device.yaml](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/torch_llm/test_config_inference_single_device.yaml)
*   [tests/runner/test_config/torch_llm/test_config_inference_tensor_parallel.yaml](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/torch_llm/test_config_inference_tensor_parallel.yaml)
*   [tests/runner/test_models.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py)
*   [tests/runner/test_utils.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py)
*   [tests/runner/testers/torch/dynamic_torch_model_tester.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/testers/torch/dynamic_torch_model_tester.py)
*   [tests/runner/utils/dynamic_loader.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/utils/dynamic_loader.py)

## Purpose and Scope

The Testing Infrastructure provides a comprehensive system for validating model execution on Tenstorrent hardware across multiple frameworks (PyTorch and JAX), execution modes (inference and training), and parallelism configurations (single device, data parallel, tensor parallel). This document covers test configuration, discovery, execution, validation, and failure analysis.

For information about the CI/CD pipeline that orchestrates test execution, see [CI/CD Pipeline](https://deepwiki.com/tenstorrent/tt-xla/7-cicd-pipeline). For details on how models are compiled and executed, see [Compilation Pipeline](https://deepwiki.com/tenstorrent/tt-xla/4.1-compilation-pipeline) and [Framework Integration](https://deepwiki.com/tenstorrent/tt-xla/5-framework-integration).

* * *

## Test Configuration System

The test configuration system uses YAML files to define expected behavior, validation thresholds, and architecture-specific overrides for each model test. Configuration files are organized by framework, execution mode, and parallelism type.

### Configuration File Structure

Configuration files follow a hierarchical structure with test-specific settings:

**Sources:**[tests/runner/test_config/torch/test_config_inference_single_device.yaml 1-1000](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/torch/test_config_inference_single_device.yaml#L1-L1000)[tests/runner/test_config/jax/test_config_inference_single_device.yaml 1-100](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/jax/test_config_inference_single_device.yaml#L1-L100)

### ModelTestConfig Class

The `ModelTestConfig` class parses YAML configuration and provides test metadata with architecture-specific resolution:

| Field | Type | Purpose |
| --- | --- | --- |
| `status` | `ModelTestStatus` | Test expectation (EXPECTED_PASSING, KNOWN_FAILURE_XFAIL, NOT_SUPPORTED_SKIP) |
| `required_pcc` | `float` | Minimum Pearson Correlation Coefficient threshold |
| `assert_pcc` | `bool` | Whether to enforce PCC validation |
| `supported_archs` | `list[str]` | Architectures where test should run |
| `arch_overrides` | `dict` | Per-architecture configuration overrides |
| `markers` | `list[str]` | Pytest markers (push, nightly, extended) |
| `filechecks` | `list[str]` | FileCheck pattern files for IR validation |

**Sources:**[tests/runner/test_utils.py 80-138](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L80-L138)

### Status Management

Tests are classified using `ModelTestStatus` enum:

**Sources:**[tests/runner/test_utils.py 67-78](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L67-L78)

### Architecture-Specific Overrides

Configuration supports per-architecture overrides for validation thresholds and test status:

The `_resolve()` method in `ModelTestConfig` handles architecture-specific resolution at [tests/runner/test_utils.py 133-137](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L133-L137)

**Sources:**[tests/runner/test_config/torch/test_config_inference_single_device.yaml 51-56](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/torch/test_config_inference_single_device.yaml#L51-L56)

* * *

## Test Framework Architecture

### Test Discovery and Entry Points

**Sources:**[tests/runner/test_models.py 52-58](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py#L52-L58)[tests/runner/test_models.py 247-302](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py#L247-L302)[tests/runner/test_models.py 305-358](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py#L305-L358)

### ModelTester Hierarchy

**Sources:**[tests/infra/testers/single_chip/model/model_tester.py 30-44](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/testers/single_chip/model/model_tester.py#L30-L44)[tests/infra/testers/single_chip/model/torch_model_tester.py 57-98](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/testers/single_chip/model/torch_model_tester.py#L57-L98)[tests/runner/testers/torch/dynamic_torch_model_tester.py 22-28](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/testers/torch/dynamic_torch_model_tester.py#L22-L28)

### Test Execution Lifecycle

The test execution follows a structured lifecycle managed by `ModelTester` and its subclasses:

**Sources:**[tests/runner/test_models.py 61-245](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py#L61-L245)[tests/infra/testers/single_chip/model/torch_model_tester.py 247-310](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/testers/single_chip/model/torch_model_tester.py#L247-L310)[tests/runner/test_utils.py 483-629](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L483-L629)

* * *

## Test Discovery and Parametrization

### Dynamic Test Discovery

The `DynamicLoader` system discovers tests from the `tt-forge-models` repository:

**Sources:**[tests/runner/utils/dynamic_loader.py 129-212](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/utils/dynamic_loader.py#L129-L212)[tests/runner/test_models.py 52-58](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py#L52-L58)

### Test ID Generation

Test IDs follow a structured format for clarity and filtering:

```
model_family/task/framework-variant-parallelism-mode
```

Example: `llama/causal_lm/pytorch-3.2_1B-single_device-inference`

The ID generation logic at [tests/runner/utils/dynamic_loader.py 268-294](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/utils/dynamic_loader.py#L268-L294) creates these identifiers by combining model information with test parameters.

### Parametrization Strategy

Tests are parametrized across three dimensions:

| Dimension | Values | Pytest Marker |
| --- | --- | --- |
| `run_mode` | INFERENCE, TRAINING | `@pytest.mark.inference`, `@pytest.mark.training` |
| `parallelism` | SINGLE_DEVICE, DATA_PARALLEL, TENSOR_PARALLEL | `@pytest.mark.single_device`, etc. |
| `test_entry` | All discovered model variants | Dynamic from discovery |

**Sources:**[tests/runner/test_models.py 247-275](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py#L247-L275)

### LLM-Specific Test Parametrization

LLM tests add additional dimensions for sequence length, batch size, and execution phase:

The `RunPhase` enum distinguishes between prefill and decode:

**Sources:**[tests/runner/test_models.py 363-424](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py#L363-L424)[tests/runner/test_utils.py 38-44](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L38-L44)

* * *

## Comparison and Validation

### ComparisonConfig and Metrics

The `ComparisonConfig` class configures validation thresholds for three comparison methods:

**Sources:**[tests/runner/test_utils.py 139-178](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L139-L178)

### PCC (Pearson Correlation Coefficient)

PCC measures the linear correlation between CPU and TT device outputs:

Default threshold: `required_pcc = 0.99`

Configuration options:

*   `assert_pcc: false` - Disable PCC enforcement
*   `required_pcc: 0.98` - Lower threshold for numerical instability

**Sources:**[tests/runner/test_config/torch/test_config_inference_single_device.yaml 14-16](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/torch/test_config_inference_single_device.yaml#L14-L16)

### ATOL (Absolute Tolerance)

ATOL measures maximum absolute difference:

Enabled explicitly in configuration:

**Sources:**[tests/runner/test_utils.py 151-154](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L151-L154)

### ALLCLOSE (NumPy allclose)

Uses `np.allclose()` with relative and absolute tolerances:

**Sources:**[tests/runner/test_utils.py 157-176](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L157-L176)

### ComparisonResult Processing

Multiple comparison results are collected and processed to determine test outcome:

**Sources:**[tests/runner/test_utils.py 357-424](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L357-L424)[tests/runner/test_utils.py 427-480](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L427-L480)

* * *

## Failure Analysis and Classification

### BringupStatus Tracking

The `BringupStatus` enum classifies test failures by pipeline stage:

**Sources:**[tests/utils.py 1-20](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/utils.py#L1-L20) (enum definition in imported file)

### Bringup Stage Detection

Failed tests are classified by detecting the last compilation/execution stage reached before failure:

The stage detection logic at [tests/runner/test_utils.py 191-218](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L191-L218) reads the marker file and maps stages to status codes.

**Sources:**[tests/runner/test_utils.py 191-218](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L191-L218)

### FailingReasonsFinder

The `FailingReasonsFinder` automatically classifies failures by analyzing exception messages, tracebacks, and output logs:

**Sources:**[tests/infra/utilities/failing_reasons/finder.py 15-58](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/utilities/failing_reasons/finder.py#L15-L58)[tests/infra/utilities/failing_reasons/checks_xla.py 1-50](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/utilities/failing_reasons/checks_xla.py#L1-L50)

### Component Classification

Failures are attributed to specific components based on stack trace patterns:

| Component | Detection Pattern |
| --- | --- |
| METAL | Contains `lib/libtt_metal.so` in traceback |
| TTNN | Contains `lib/_ttnncpp.so` but not Metal |
| TTMLIR | Contains `lib/libTTMLIRCompiler.so` |
| XLA | Contains `tt_torch/backend/backend.py` without other libs |
| TORCH | Contains `torch/utils/_pytree.py` without TT libs |
| FRONTEND | Framework-specific compilation errors |

**Sources:**[tests/infra/utilities/failing_reasons/checks_xla.py 13-112](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/utilities/failing_reasons/checks_xla.py#L13-L112)

### Exception Analysis Workflow

**Sources:**[tests/runner/test_models.py 171-182](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py#L171-L182)[tests/runner/test_utils.py 221-263](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L221-L263)

* * *

## Test Property Recording

### Metadata Collection

The `record_model_test_properties()` function collects comprehensive test metadata for CI/CD reporting:

**Sources:**[tests/runner/test_utils.py 483-629](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L483-L629)

### Recorded Properties

Core properties recorded for each test execution:

**Sources:**[tests/runner/test_utils.py 569-629](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L569-L629)

### Guidance Tag Generation

Guidance tags suggest configuration improvements based on test results:

| Tag | Condition | Action |
| --- | --- | --- |
| `RM_XFAIL` | Test marked KNOWN_FAILURE_XFAIL but passing | Remove xfail marker |
| `ADD_CONFIG` | Test marked UNSPECIFIED but passing | Add proper configuration |
| `ENABLE_PCC` | PCC disabled but value safely above threshold | Enable PCC validation |
| `ENABLE_PCC_099` | Same as above, threshold already ≥0.99 | Enable strict PCC |
| `RAISE_PCC` | Threshold <0.99 and value exceeds next level | Raise to next 0.01 step |
| `RAISE_PCC_099` | Threshold <0.99 and value exceeds 0.99 | Raise to 0.99 |

**Sources:**[tests/runner/test_utils.py 304-334](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L304-L334)[tests/runner/test_utils.py 427-480](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_utils.py#L427-L480)

* * *

## Multi-Chip and Parallelism Testing

### Mesh Configuration

Multi-chip tests use the `Mesh` abstraction for device topology:

**Sources:**[tests/infra/utilities/torch_multichip_utils.py 1-50](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/utilities/torch_multichip_utils.py#L1-L50) (referenced but not in files)

### Parallelism Configuration

**Sources:**[tests/runner/test_models.py 114-156](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py#L114-L156)[tests/infra/testers/single_chip/model/torch_model_tester.py 132-156](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/testers/single_chip/model/torch_model_tester.py#L132-L156)

### Shard Spec Functions

For tensor parallel tests, models provide shard spec functions defining parameter distribution:

The tester applies sharding to both parameters and gradients during training at [tests/infra/testers/single_chip/model/torch_model_tester.py 216-246](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/testers/single_chip/model/torch_model_tester.py#L216-L246)

**Sources:**[tests/infra/testers/single_chip/model/torch_model_tester.py 216-246](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/testers/single_chip/model/torch_model_tester.py#L216-L246)

### Supported Architecture Filtering

Multi-chip tests specify supported architectures in configuration:

Architecture filtering occurs during pytest collection using the `supported_arch_marker` fixture at [conftest.py 200-250](https://github.com/tenstorrent/tt-xla/blob/c77995f6/conftest.py#L200-L250) (not in provided files but referenced).

**Sources:**[tests/runner/test_config/torch/test_config_inference_tensor_parallel.yaml 156-163](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/torch/test_config_inference_tensor_parallel.yaml#L156-L163)

* * *

## Specialized Test Types

### LLM Tests (Prefill/Decode)

LLM tests validate both prefill (initial token processing) and decode (autoregressive generation) phases with varying sequence lengths and batch sizes:

**Sources:**[tests/runner/test_models.py 363-477](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py#L363-L477)[tests/runner/testers/torch/dynamic_torch_model_tester.py 43-100](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/testers/torch/dynamic_torch_model_tester.py#L43-L100)

### Test Configuration for LLM Tests

LLM tests use separate configuration files with phase-specific settings:

**Sources:**[tests/runner/test_config/torch_llm/test_config_inference_single_device.yaml 11-183](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/torch_llm/test_config_inference_single_device.yaml#L11-L183)

### FileCheck Tests

FileCheck tests validate generated MLIR IR by pattern matching:

The test dynamically adds FileCheck markers if patterns are specified at [tests/runner/test_models.py 157-160](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py#L157-L160)

Pattern files contain FileCheck directives:

**Sources:**[tests/runner/test_config/torch/test_config_inference_single_device.yaml 509-514](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/torch/test_config_inference_single_device.yaml#L509-L514)[tests/infra/utilities/filecheck_utils.py 1-100](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/infra/utilities/filecheck_utils.py#L1-L100) (referenced)

### vLLM Integration Tests

vLLM tests validate sampling correctness and embedding generation:

| Test Type | Location | Purpose |
| --- | --- | --- |
| Sampling Parameters | `tests/integrations/vllm_plugin/sampling/test_sampling_params.py` | Validate temperature, top_p, top_k, penalties |
| Pooling/Embedding | `tests/integrations/vllm_plugin/pooling/test_single_device.py` | Validate embedding model correctness |
| Generation | `tests/integrations/vllm_plugin/generative/test_llama3_3b_generation.py` | End-to-end generation tests |

**Sources:**[.test_durations 45-56](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.test_durations#L45-L56)

* * *

## CI/CD Integration

### Test Matrix Presets

Test execution in CI is driven by JSON matrix configuration files:

**Sources:**[.github/workflows/test-matrix-presets/model-test-passing.json 1-17](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.github/workflows/test-matrix-presets/model-test-passing.json#L1-L17)

### Test Duration Tracking

Test durations are tracked in `.test_durations` for intelligent test splitting:

This data enables load-balanced parallel execution by distributing tests based on historical runtime.

**Sources:**[.test_durations 1-100](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.test_durations#L1-L100)

### Parallel Test Execution

The `call-test.yml` workflow orchestrates parallel test execution:

**Sources:**[.github/workflows/call-test.yml 1-100](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.github/workflows/call-test.yml#L1-L100) (referenced in context), [pytest.ini 1-34](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pytest.ini#L1-L34)

### Performance Benchmarking

Performance measurements are collected and reported for inference tests:

Measurements include compilation time, execution time, and throughput metrics.

**Sources:**[tests/runner/test_models.py 205-244](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_models.py#L205-L244)

* * *

## Configuration Reference

### Pytest Markers

| Marker | Purpose |
| --- | --- |
| `push` | Run in PR pipeline |
| `nightly` | Run in nightly pipeline |
| `extended` | Extended test suite |
| `large` | Large models requiring more resources |
| `notimeout` | Use default 240min timeout |
| `expected_passing` | Test expected to pass |
| `known_failure_xfail` | Known failure, marked xfail |
| `not_supported_skip` | Not supported, skipped |
| `inference` | Inference mode |
| `training` | Training mode |
| `single_device` | Single device execution |
| `tensor_parallel` | Tensor parallel execution |
| `data_parallel` | Data parallel execution |

**Sources:**[pytest.ini 6-33](https://github.com/tenstorrent/tt-xla/blob/c77995f6/pytest.ini#L6-L33)

### Architecture Tags

| Tag | Description |
| --- | --- |
| `n150` | Wormhole B0 single chip |
| `p150` | Blackhole single chip |
| `n300` | Wormhole B0 dual chip |
| `n300-llmbox` | 8-chip LLMBox configuration |
| `galaxy-wh-6u` | Galaxy multi-rack |

**Sources:**[.github/workflows/manual-test-single.yml 22-33](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.github/workflows/manual-test-single.yml#L22-L33)

### Test Execution Commands

Execute specific test configurations:

**Sources:**[.github/workflows/manual-test-single.yml 1-56](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.github/workflows/manual-test-single.yml#L1-L56)

Dismiss
Refresh this wiki

Enter email to refresh
