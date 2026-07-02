---
project: tt-forge-onnx
github: https://github.com/tenstorrent/tt-forge-onnx
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-onnx/6-testing-infrastructure)
---

# Testing Infrastructure

Relevant source files
*   [.git-blame-ignore-revs](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.git-blame-ignore-revs)
*   [.github/Dockerfile.ird](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/Dockerfile.ird)
*   [.github/build-docker-images.sh](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/build-docker-images.sh)
*   [.github/get-docker-tag.sh](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/get-docker-tag.sh)
*   [.github/workflows/build-image.yml](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/build-image.yml)
*   [.github/workflows/build.yml](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/build.yml)
*   [.github/workflows/compile_and_run.sh](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/compile_and_run.sh)
*   [.github/workflows/on-nightly.yml](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/on-nightly.yml)
*   [.github/workflows/on-pr.yml](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/on-pr.yml)
*   [.github/workflows/produce_data.yml](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/produce_data.yml)
*   [.github/workflows/test-sub.yml](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml)
*   [.github/workflows/test.yml](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test.yml)
*   [forge/test/models/pytorch/timeseries/nbeats/model_utils/http_utils.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/timeseries/nbeats/model_utils/http_utils.py)
*   [forge/test/operators/pytorch/conftest.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/conftest.py)
*   [forge/test/operators/pytorch/eltwise_nary/test_concatenate.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/eltwise_nary/test_concatenate.py)
*   [forge/test/operators/pytorch/matmul/__init__.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/matmul/__init__.py)
*   [forge/test/operators/pytorch/test_all.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/test_all.py)
*   [forge/test/operators/utils/__init__.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/__init__.py)
*   [forge/test/operators/utils/compat.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/compat.py)
*   [forge/test/operators/utils/failing_reasons_validation.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/failing_reasons_validation.py)
*   [forge/test/operators/utils/features.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/features.py)
*   [forge/test/operators/utils/logger_utils.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/logger_utils.py)
*   [forge/test/operators/utils/plan.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py)
*   [forge/test/operators/utils/pytest.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/pytest.py)
*   [forge/test/operators/utils/test_data.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/test_data.py)
*   [forge/test/operators/utils/utils.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/utils.py)

## Overview

The tt-forge-onnx testing infrastructure validates the compiler pipeline through three test categories:

1.   **Operator Testing**: Parameterized test cases covering 80+ operations with systematic testing of input sources, shapes, data formats, and math fidelities
2.   **Model Testing**: 100+ pre-trained models from HuggingFace, TorchVision, and TIMM across vision, NLP, and multimodal domains
3.   **CI/CD Automation**: GitHub Actions workflows with parallel execution, intelligent test splitting, and automated failure classification

Key architectural components:

*   **Test Planning**: `TestPlan`, `TestCollection`, and `TestVector` classes generate test parameters via cartesian product expansion
*   **Failure Management**: `FailingReasons` enum with 150+ categories and `ComponentChecker` for attribution (Metal, TTNN, MLIR, Forge, TVM)
*   **Execution Model**: Pytest-based execution with matrix parallelization across hardware targets (n150, n300, p150)
*   **Result Tracking**: JUnit XML reports, memory profiling, and historical timing data in `.test_durations`

This page provides an overview of the testing system architecture. For detailed information, see:

*   [Test Planning Framework](https://deepwiki.com/tenstorrent/tt-forge-onnx/6.1-test-planning-framework) - `TestPlan`, `TestCollection`, `TestVector` class structure
*   [Operator Testing](https://deepwiki.com/tenstorrent/tt-forge-onnx/6.2-operator-testing) - Operator test organization and `TestSuite` management
*   [Model Testing](https://deepwiki.com/tenstorrent/tt-forge-onnx/6.3-model-testing) - Model loader patterns and verification workflows
*   [CI/CD Pipeline](https://deepwiki.com/tenstorrent/tt-forge-onnx/6.4-cicd-pipeline) - GitHub Actions workflows and matrix execution
*   [Failure Classification](https://deepwiki.com/tenstorrent/tt-forge-onnx/6.5-failure-classification-system) - `FailingReasons`, `ComponentChecker`, and validation logic

## System Architecture

**Diagram: Testing Infrastructure Components**

**Sources:**

*   [.github/workflows/on-pr.yml 1-149](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/on-pr.yml#L1-L149)
*   [.github/workflows/on-nightly.yml 1-39](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/on-nightly.yml#L1-L39)
*   [.github/workflows/test-sub.yml 1-327](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L1-L327)
*   [forge/test/operators/utils/plan.py 1-1107](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L1-L1107)
*   [forge/test/operators/utils/test_data.py 1-337](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/test_data.py#L1-L337)
*   [.test_durations (referenced in test-sub.yml)](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.test_durations%20(referenced%20in%20test-sub.yml))

## Operator Testing Overview

Operator tests validate 80+ operations with systematic parameter coverage. Tests are organized by operator category:

| Category | Location | Key Operations |
| --- | --- | --- |
| Eltwise Unary | `forge/test/operators/pytorch/eltwise_unary/` | relu, sqrt, sigmoid, exp, log, abs |
| Eltwise Binary | `forge/test/operators/pytorch/eltwise_binary/` | add, mul, div, sub, maximum, minimum |
| Eltwise Nary | `forge/test/operators/pytorch/eltwise_nary/` | concatenate, where, stack |
| Tensor Manipulation | `forge/test/operators/pytorch/tm/` | reshape, transpose, squeeze, unsqueeze |
| Convolution | `forge/test/operators/pytorch/conv/` | conv2d, conv_transpose_2d |
| Neural Network | `forge/test/operators/pytorch/nn/` | layer_norm, batch_norm, pooling |
| Matmul | `forge/test/operators/pytorch/matmul/` | matmul, bmm |
| Reduction | `forge/test/operators/pytorch/reduce/` | sum, mean, max, min |

The `TestPlan` framework generates test cases through cartesian product expansion:

Each test vector becomes a pytest parameter set with automatic xfail marking based on `failing_rules` in the `TestPlan`. See [Operator Testing](https://deepwiki.com/tenstorrent/tt-forge-onnx/6.2-operator-testing) for detailed test organization.

**Sources:**

*   [forge/test/operators/utils/plan.py 82-130](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L82-L130)
*   [forge/test/operators/utils/test_data.py 17-256](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/test_data.py#L17-L256)
*   [forge/test/operators/pytorch/eltwise_nary/test_concatenate.py 204-268](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/eltwise_nary/test_concatenate.py#L204-L268)

## Model Testing Overview

Model tests validate 100+ pre-trained models across three domains:

**Vision Models**:

*   Image Classification: ResNet, VGG, MobileNet, EfficientNet, ViT, DeiT, Swin Transformer
*   Object Detection: YOLO variants, DETR
*   Segmentation: U-Net, SegFormer, DeepLabV3
*   Specialized: PixelShuffle, style transfer models

**Text/LLM Models**:

*   Decoder-only: Llama 2/3, Mistral, Phi 2/3/4, Gemma, Qwen, Falcon
*   Encoder: BERT variants (base, large)
*   Testing modes: Prefill, decode, KV cache configurations

**Multimodal Models**:

*   Vision-Language: ViLT, LLaVA, CLIP, Phi3.5 Vision
*   Generative: Stable Diffusion, VAE, SDXL

Model tests are organized in test modules under `forge/test/models/`:

*   `forge/test/models/pytorch/vision/` - Vision models
*   `forge/test/models/pytorch/text/` - LLMs and text models
*   `forge/test/models/pytorch/multimodal/` - Multimodal models
*   `forge/test/models/onnx/` - ONNX model tests

Each test module instantiates models from various sources (HuggingFace `transformers`, TorchVision, TIMM) and executes:

See [Model Testing](https://deepwiki.com/tenstorrent/tt-forge-onnx/6.3-model-testing) for detailed model loader patterns and verification configurations.

**Sources:**

*   Model test directories: `forge/test/models/pytorch/`, `forge/test/models/onnx/`
*   Model loading typically uses `transformers`, `torchvision.models`, `timm`

## Test Execution Flow

**Diagram: End-to-End Test Execution**

**Sources:**

*   [.github/workflows/test-sub.yml 106-288](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L106-L288)
*   [forge/test/operators/pytorch/conftest.py 22-86](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/conftest.py#L22-L86)
*   [forge/test/operators/utils/compat.py 289-348](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/compat.py#L289-L348)

### Matrix Parallelization

The CI/CD pipeline uses pytest-split with historical timing data to balance test execution across parallel jobs:

**Diagram: Test Splitting Strategy**

Matrix strategy in `test-sub.yml`:

*   `runs-on: [n150, n300, p150]` - 3 hardware targets
*   `test_group_ids: [1,2,3,4,5,6]` - 6 test groups
*   Result: 18 parallel jobs (3 × 6)

**Sources:**

*   [.github/workflows/test-sub.yml 79-104](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L79-L104)
*   [.test_durations 1-50](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.test_durations#L1-L50)

## Key Components

### TestPlan Framework

The core abstraction for test generation and execution defined in [forge/test/operators/utils/plan.py 336-454](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L336-L454):

Usage pattern from [forge/test/operators/pytorch/eltwise_nary/test_concatenate.py 204-268](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/eltwise_nary/test_concatenate.py#L204-L268):

The `TestPlan.generate()` method produces `TestVector` instances via cartesian product. See [Test Planning Framework](https://deepwiki.com/tenstorrent/tt-forge-onnx/6.1-test-planning-framework) for detailed mechanics.

**Sources:**

*   [forge/test/operators/utils/plan.py 336-454](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L336-L454)
*   [forge/test/operators/pytorch/eltwise_nary/test_concatenate.py 204-268](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/eltwise_nary/test_concatenate.py#L204-L268)

### FailingReasons Classification

The `FailingReasons` enum in [forge/test/operators/utils/failing_reasons.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/failing_reasons.py) classifies failures into 150+ categories with component attribution via `ComponentChecker`:

**Component Detection Logic:**

| Component | Detection Pattern | Example Error |
| --- | --- | --- |
| `ComponentChecker.METAL` | `lib/libtt_metal.so` in traceback | OOM, device errors |
| `ComponentChecker.TTNN` | `lib/_ttnncpp.so` without metal | Unsupported operations |
| `ComponentChecker.MLIR` | `lib/libTTMLIRRuntime.so` only | TTIR/TTNN lowering failures |
| `ComponentChecker.FORGE` | `forge/_C.so` or forge Python paths | Data mismatch, shape errors |
| `ComponentChecker.TVM` | `tvm/relay/` in traceback | Relay IR conversion errors |

**Common FailingReason Examples:**

See [Failure Classification](https://deepwiki.com/tenstorrent/tt-forge-onnx/6.5-failure-classification-system) for complete taxonomy and validation logic.

**Sources:**

*   [forge/test/operators/utils/failing_reasons.py 1-1164](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/failing_reasons.py#L1-L1164)
*   Component checker logic and exception pattern matching

### Artifact Management

Each test job uploads structured artifacts defined in [.github/workflows/test-sub.yml 246-327](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L246-L327):

| Artifact | File | Purpose |
| --- | --- | --- |
| `collected-test-list` | `collected_test_list` | List of tests selected by pytest-split |
| `test-log` | `pytest.log` or `test_runner_verbose.log` | Standard or verbose (-svv) pytest output |
| `test-crash-log` | `crashed_pytest.log` | Tests that caused Python crashes |
| `memory-usage` | `pytest-memory-usage.csv` | Per-test memory consumption |
| `test-reports` | `reports/report_{job_id}.xml` | JUnit XML for CI/CD integration |

**Artifact Naming Convention:**

```
{artifact-type}-{runs-on}-{test_group_id}-{test_mark}-{job_id}

Examples:
- test-log-n150-1-nightly-1234567890
- test-reports-n300-2-push-9876543210
- memory-usage-p150-3-pr_models_regression-1122334455
```

**Upload Steps from test-sub.yml:**

See [CI/CD Pipeline](https://deepwiki.com/tenstorrent/tt-forge-onnx/6.4-cicd-pipeline) for workflow details.

**Sources:**

*   [.github/workflows/test-sub.yml 246-327](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L246-L327)
*   JUnit XML report generation by pytest with custom properties

* * *

## CI/CD Pipeline Architecture

### Workflow Hierarchy

**Sources:**

*   [.github/workflows/on-pr.yml 1-149](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/on-pr.yml#L1-L149)
*   [.github/workflows/on-nightly.yml 1-39](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/on-nightly.yml#L1-L39)
*   [.github/workflows/test.yml 1-167](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test.yml#L1-L167)
*   [.github/workflows/test-sub.yml 1-327](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L1-L327)
*   [.github/workflows/produce_data.yml 1-32](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/produce_data.yml#L1-L32)

### Test-Sub Workflow Parameters

The `test-sub.yml` reusable workflow accepts multiple input parameters:

| Parameter | Type | Purpose | Example |
| --- | --- | --- | --- |
| `test_mark` | string | pytest marker to select tests | `"push"`, `"nightly"`, `"pr_models_regression"` |
| `test_group_cnt` | string | Number of parallel groups | `"2"`, `"6"` |
| `test_group_ids` | string | JSON array of group IDs | `"[1,2,3,4,5,6]"` |
| `docker-image` | string | Docker image to use | From build-image output |
| `runs-on` | string | JSON array of runner configs | `'[{"runs-on": "n150"}]'` |
| `enable-verbose-output` | boolean | Enable pytest -svv flag | `true` / `false` |

**Sources:**

*   [.github/workflows/test-sub.yml 3-77](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L3-L77)

### Matrix Parallelization Strategy

For example, `on-pr.yml` uses:

*   `runs-on: '[{"runs-on": "n150"}, {"runs-on": "n300"}]'`
*   `test_group_ids: '[1,2]'`
*   Result: 2 hardware types × 2 groups = 4 parallel jobs

**Sources:**

*   [.github/workflows/test-sub.yml 79-104](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L79-L104)
*   [.github/workflows/on-pr.yml 100-130](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/on-pr.yml#L100-L130)

### Test Collection and Execution Flow

**Sources:**

*   [.github/workflows/test-sub.yml 198-288](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L198-L288)

### Test Splitting with Historical Timing

The `.test_durations` file stores execution times for intelligent test distribution via pytest-split:

**File Format (JSON):**

**Test Collection Command from [.github/workflows/test-sub.yml 198-244](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L198-L244):**

**Algorithm:** The `least_duration` algorithm distributes tests across groups to balance total execution time per group. Tests with longer durations are assigned first to avoid imbalanced groups.

**Example Distribution (6 groups):**

*   Group 1: 5 tests, total 180s
*   Group 2: 8 tests, total 185s
*   Group 3: 6 tests, total 178s
*   Group 4: 7 tests, total 182s
*   Group 5: 9 tests, total 179s
*   Group 6: 5 tests, total 181s

**Sources:**

*   [.github/workflows/test-sub.yml 198-244](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L198-L244)
*   `.test_durations` file (auto-updated by pytest-split)

* * *

## Artifact Management

### Artifact Collection Strategy

Each test job generates multiple artifacts that are collected for post-processing:

Artifact naming example:

```
test-log-n150-1-nightly-1234567890
test-reports-n300-2-push-1234567891
memory-usage-p150-3-pr_models_regression-1234567892
```

**Sources:**

*   [.github/workflows/test-sub.yml 246-327](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L246-L327)
*   [scripts/consolidate_nightly_result.py 1-400](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/scripts/consolidate_nightly_result.py#L1-L400)

### Result Consolidation

The `consolidate_nightly_result.py` script processes nightly results:

The Excel output includes:

*   Color-coded cells (green=PASS, red=FAIL, yellow=SKIP, pink=XFAIL)
*   Dropdown validation for manual corrections
*   Multi-line failure reason details
*   10-day trend analysis

**Sources:**

*   [scripts/consolidate_nightly_result.py 37-275](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/scripts/consolidate_nightly_result.py#L37-L275)

* * *

## Historical Timing Data

The `.test_durations` file contains execution times for all tests, updated after each run. This enables intelligent test splitting:

### Duration Data Structure

**Usage in Test Splitting:**

1.   **least_duration algorithm**: Distributes tests across groups to balance total execution time
2.   **Group assignment**: Longer tests are distributed first to avoid imbalanced groups
3.   **Dynamic updates**: File is updated after each test run to improve future splits

Example distribution for 6 groups:

*   Group 1: 5 tests, total 180s
*   Group 2: 8 tests, total 185s
*   Group 3: 6 tests, total 178s
*   Group 4: 7 tests, total 182s
*   Group 5: 9 tests, total 179s
*   Group 6: 5 tests, total 181s

**Sources:**

*   [.test_durations 1-50](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.test_durations#L1-L50)

* * *

## Pytest Configuration

The testing infrastructure uses pytest markers and configuration for test selection:

### Test Markers

| Marker | Purpose | Trigger |
| --- | --- | --- |
| `push` | Quick validation tests | PR workflows |
| `nightly` | Comprehensive regression | Nightly schedule |
| `pr_models_regression` | Model validation | PR workflows (optional) |
| `run_in_pp` | Pipeline-parallel tests | All workflows |
| `slow` | Long-running tests | Nightly only |
| `xfail` | Expected failures | Marked dynamically |

### Dynamic xfail Marking

Tests are marked as xfail/skip during parameterization based on `TestPlan.failing_rules`:

**Test Vector Generation from [forge/test/operators/utils/plan.py 392-444](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L392-L444):**

**Xfail Assignment from [forge/test/operators/utils/plan.py 360-390](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L360-L390):**

**Pytest Parameterization from [forge/test/operators/utils/plan.py 128-130](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L128-L130):**

**Sources:**

*   [forge/test/operators/utils/plan.py 82-130](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L82-L130) - TestVector class
*   [forge/test/operators/utils/plan.py 360-444](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L360-L444) - TestPlan.check_test_failing and generate
*   [forge/test/operators/pytorch/eltwise_nary/test_concatenate.py 240-268](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/eltwise_nary/test_concatenate.py#L240-L268) - Example failing_rules

* * *

## Integration with Verification System

The testing infrastructure integrates tightly with the verification system:

The test infrastructure provides:

*   Test parameterization (shapes, formats, operators)
*   Expected failure classification
*   Test execution orchestration

The verification system provides:

*   Model compilation
*   Output comparison
*   Numerical accuracy validation

**Sources:**

*   [forge/test/operators/pytorch/eltwise_unary/test_unary.py 84-131](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/eltwise_unary/test_unary.py#L84-L131)
*   [forge/test/operators/pytorch/eltwise_binary/test_binary.py 99-188](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/eltwise_binary/test_binary.py#L99-L188)

Dismiss
Refresh this wiki

Enter email to refresh