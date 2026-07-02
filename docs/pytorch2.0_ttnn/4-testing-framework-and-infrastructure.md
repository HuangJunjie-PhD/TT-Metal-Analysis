---
project: pytorch2.0_ttnn
github: https://github.com/tenstorrent/pytorch2.0_ttnn
deepwiki: https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4-testing-framework-and-infrastructure
---

# Testing Framework and Infrastructure

Relevant source files
*   [tests/conftest.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py)
*   [tests/lowering/eltwise/binary/test_pow_scalar.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/eltwise/binary/test_pow_scalar.py)
*   [tests/lowering/eltwise/unary/test_hardsigmoid.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/eltwise/unary/test_hardsigmoid.py)
*   [tests/lowering/pool/test_max_pool_2d.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/pool/test_max_pool_2d.py)
*   [tests/models/clip/test_clip.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/clip/test_clip.py)
*   [tests/models/dino/test_dino.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/dino/test_dino.py)
*   [tests/models/glpn_kitti/test_glpn_kitti.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/glpn_kitti/test_glpn_kitti.py)
*   [tests/models/hardnet/test_hardnet.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/hardnet/test_hardnet.py)
*   [tests/models/timm/test_timm_image_classification.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/timm/test_timm_image_classification.py)
*   [tests/models/torchvision/test_torchvision_object_detection.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/torchvision/test_torchvision_object_detection.py)
*   [tests/models/unet/test_unet.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/unet/test_unet.py)
*   [tests/models/unet_brain/test_unet_brain.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/unet_brain/test_unet_brain.py)
*   [tests/models/unet_carvana/test_unet_carvana.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/unet_carvana/test_unet_carvana.py)
*   [tests/models/yolov3/test_yolov3.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/yolov3/test_yolov3.py)
*   [tests/tools/test_input_vals_utils.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/tools/test_input_vals_utils.py)
*   [tests/tools/test_nested_output_accuracy.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/tools/test_nested_output_accuracy.py)
*   [tests/utils.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py)
*   [tools/generate_guard_function_from_input_var_metric.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_guard_function_from_input_var_metric.py)
*   [tools/generate_input_variation_test_from_models.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_input_variation_test_from_models.py)
*   [tools/merge_to_tt_guard_autogen.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/merge_to_tt_guard_autogen.py)
*   [tools/to_tt_guard_autogen.tmpl](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/to_tt_guard_autogen.tmpl)

The pytorch2.0_ttnn repository implements a comprehensive pytest-based testing framework for validating PyTorch model compilation and execution on Tenstorrent hardware. The framework provides automated test orchestration, metrics collection, accuracy verification, and support for both single-device and multi-device testing scenarios.

This page covers the overall architecture and organization of the testing system. For specific subsystems, see: [pytest Infrastructure and Fixtures](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4.1-pytest-infrastructure-and-fixtures), [ModelTester Framework](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4.2-modeltester-framework), [Test Utilities and Assertion Helpers](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4.3-test-utilities-and-assertion-helpers), and [Automated Test Generation](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4.4-automated-test-generation). For information about specific model coverage, see sections [5](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/5-model-test-coverage), [5.1](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/5.1-computer-vision-model-tests), [5.2](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/5.2-natural-language-processing-model-tests), and [5.3](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/5.3-multi-modal-and-speech-model-tests). For operation-level test coverage, see section [6](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/6-operation-lowering-test-coverage).

## Test Organization and Structure

The test suite is organized into four primary categories, each serving a distinct validation purpose:

**Sources:**[tests/models/mnist/test_mnist.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py)[tests/models/bert/test_bert.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py)[tests/models/resnet/test_resnet.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/resnet/test_resnet.py)[tests/lowering/eltwise/binary/test_pow_scalar.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/eltwise/binary/test_pow_scalar.py)[tests/lowering/eltwise/unary/test_hardsigmoid.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/eltwise/unary/test_hardsigmoid.py)

### Test Category Characteristics

| Category | Purpose | Test Count | Execution Context |
| --- | --- | --- | --- |
| **models/** | End-to-end model validation | ~50+ models | Full compilation pipeline |
| **lowering/** | Operation lowering verification | ~100+ operations | Isolated operation tests |
| **autogen_op/** | Automatically generated tests | Dynamic (from metrics) | Single operation per test |
| **multi_device/** | Data parallelism validation | Selected models | N300 hardware required |

**Sources:**[tests/conftest.py 196-198](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L196-L198)

## Core Testing Infrastructure Components

The testing framework is built on several interconnected components that work together to orchestrate test execution, collect metrics, and verify correctness:

**Sources:**[tests/conftest.py 62-110](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L62-L110)[tests/conftest.py 130-189](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L130-L189)[tests/conftest.py 191-411](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L191-L411)[tests/utils.py 15-153](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L15-L153)

## Test Execution Flow

The framework uses pytest's fixture system to automatically intercept test execution and perform compilation, metrics collection, and validation:

**Sources:**[tests/conftest.py 191-411](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L191-L411)[tests/utils.py 146-152](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L146-L152)[tests/models/mnist/test_mnist.py 60-68](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py#L60-L68)

## pytest Configuration and Command-Line Options

The framework provides several command-line options for controlling test behavior:

| Option | Purpose | Default | Usage |
| --- | --- | --- | --- |
| `--input_var_only_native` | Test only native PyTorch execution | False | Metrics generation mode |
| `--input_var_check_ttnn` | Verify TTNN operation execution | False | Operation validation |
| `--input_var_check_accu` | Check accuracy on each operation | False | Detailed accuracy debugging |
| `--report_nth_iteration` | Specify iteration count for metrics | 1 | Performance measurement |
| `--export_code` | Export standalone Python code | None | Debugging/profiling |
| `--data_parallel` | Enable multi-device data parallelism | False | N300 hardware tests |
| `--native_integration` | Use native device integration | False | Alternative execution mode |
| `--disable_load_params_once` | Disable parameter caching optimization | False | Testing without optimization |

**Sources:**[tests/conftest.py 34-60](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L34-L60)

### pytest Markers

The framework defines several custom pytest markers for test categorization and conditional execution:

**Sources:**[tests/conftest.py 154-171](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L154-L171)[tests/conftest.py 174-181](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L174-L181)[tests/conftest.py 409-410](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L409-L410)[tests/models/mnist/test_mnist.py 56-59](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py#L56-L59)

## Session-Scoped Device Management

The `device` fixture provides session-scoped access to Tenstorrent hardware, ensuring that device initialization occurs only once per test session:

**Sources:**[tests/conftest.py 77-110](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L77-L110)[tests/conftest.py 112-128](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L112-L128)

## Automatic Metrics Collection and Validation

The `compile_and_run` fixture is the central orchestrator of the testing framework. It automatically intercepts test execution using pytest's `autouse=True` parameter:

**Sources:**[tests/conftest.py 191-411](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L191-L411)

### Key Behaviors of compile_and_run

1.   **Automatic Interception**: Uses `autouse=True` to intercept all test functions without explicit declaration
2.   **Dual Execution**: Runs tests both natively and with compilation for comparison
3.   **Metrics Persistence**: Saves execution metrics to `metrics/{model_name}/` directory as pickle files
4.   **Failure Recovery**: Attempts to collect metrics even when compilation fails using bypass mode
5.   **Warmup Iterations**: Supports multiple iterations with `--report_nth_iteration` for performance measurement
6.   **Native Integration**: Supports alternative execution mode with native device integration

**Sources:**[tests/conftest.py 191-220](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L191-L220)[tests/conftest.py 261-411](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L261-L411)

## ModelTester Pattern and Abstract Interface

The `ModelTester` base class enforces a standardized interface for all model tests:

**Sources:**[tests/utils.py 15-153](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L15-L153)[tests/models/mnist/test_mnist.py 41-53](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py#L41-L53)[tests/models/bert/test_bert.py 15-49](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py#L15-L49)

### Abstract Method Requirements

All concrete test implementations must override two abstract methods:

1.   **`_load_model()`**: Returns the PyTorch model instance

    *   Can download from Hugging Face transformers
    *   Can use torchvision models
    *   Can define custom nn.Module classes
    *   Should convert to `torch.bfloat16` for consistency

2.   **`_load_inputs(batch_size)`**: Returns model inputs

    *   Must support dynamic batch sizing
    *   Can return `torch.Tensor` for simple inputs
    *   Can return `Dict[str, Tensor]` for complex inputs (BERT, etc.)
    *   Should convert to `torch.bfloat16` for consistency

**Sources:**[tests/utils.py 25-29](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L25-L29)[tests/models/mnist/test_mnist.py 42-53](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py#L42-L53)[tests/models/bert/test_bert.py 16-49](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py#L16-L49)

## Accuracy Verification System

The framework provides multiple accuracy comparison methods to handle different precision requirements:

**Sources:**[tests/utils.py 200-252](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L200-L252)[tests/utils.py 307-380](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L307-L380)[tests/utils.py 395-441](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L395-L441)[tests/utils.py 443-472](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L443-L472)[tests/utils.py 474-510](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L474-L510)

### Pearson Correlation Coefficient (PCC)

Used for comparing tensors with correlation-based similarity:

**Sources:**[tests/utils.py 266-272](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L266-L272)[tests/lowering/eltwise/unary/test_hardsigmoid.py 9](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/eltwise/unary/test_hardsigmoid.py#L9-L9)[tests/lowering/eltwise/unary/test_hardsigmoid.py 63](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/eltwise/unary/test_hardsigmoid.py#L63-L63)

### Unit of Least Precision (ULP)

Used for comparing low-precision floating-point tensors:

**Sources:**[tests/utils.py 395-440](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L395-L440)[tests/lowering/eltwise/binary/test_pow_scalar.py 9](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/eltwise/binary/test_pow_scalar.py#L9-L9)[tests/lowering/eltwise/binary/test_pow_scalar.py 44](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/eltwise/binary/test_pow_scalar.py#L44-L44)

## Nested Structure Accuracy Calculation

The `calculate_accuracy` function recursively processes nested outputs (Lists, Dicts, Tuples of Tensors):

**Sources:**[tests/utils.py 474-510](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L474-L510)[tests/tools/test_nested_output_accuracy.py 1-33](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/tools/test_nested_output_accuracy.py#L1-L33)

## Test Input Generation from Metrics

The framework includes utilities for generating test inputs from serialized metrics strings:

**Sources:**[tests/utils.py 513-645](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L513-L645)[tests/utils.py 648-781](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L648-L781)[tests/utils.py 783-785](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L783-L785)

### Metric String Format

The system parses metric strings in the format:

```
<type><shape> name = raw_val
```

Examples:

*   `Tensor<[2, 3, 4]> input = None`
*   `int exponent = 2`
*   `Optional[float] eps = 1e-05`
*   `List[Tensor] indices = [None, <[3, 4]>, <[5]>]`

The renderer converts these strings back into executable Python objects (tensors, scalars, lists) for test execution.

**Sources:**[tests/utils.py 620-635](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L620-L635)

## Test Utilities and Helper Functions

Additional utility functions support common testing patterns:

| Function | Purpose | File Location |
| --- | --- | --- |
| `repeat_inputs(inputs, batch_size)` | Expand batch dimension of inputs | [tests/utils.py 155-169](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L155-L169) |
| `get_absolute_cache_path(relative_path)` | Resolve cache paths (NFS or local) | [tests/utils.py 172-179](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L172-L179) |
| `get_cached_image_or_reload(path, url)` | Download and cache images | [tests/utils.py 182-196](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L182-L196) |
| `run_model(model, inputs)` | Execute model with Dict or Sequence inputs | [tests/conftest.py 413-419](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L413-L419) |
| `manage_dependencies(request)` | Install/uninstall per-module dependencies | [tests/conftest.py 422-429](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L422-L429) |

**Sources:**[tests/utils.py 155-196](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/utils.py#L155-L196)[tests/conftest.py 413-429](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L413-L429)[tests/models/dino/test_dino.py 9](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/dino/test_dino.py#L9-L9)

## Integration with CI/CD Pipeline

The testing framework integrates with the CI/CD system documented in section [9](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/9-cicd-and-release-management):

*   Tests execute in parallel matrices across multiple GitHub runners
*   Results are aggregated into metrics pickle files
*   Metrics feed into the report generation system ([7.3](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/7.3-documentation-generation))
*   Failed tests generate guard blocklists ([7.4](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/7.4-guard-blocklist-management))
*   Autogenerated tests are created from collected metrics ([4.4](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4.4-automated-test-generation))

For details on CI/CD integration, see [Testing Pipeline and Parallelization](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/9.2-testing-pipeline-and-parallelization).

**Sources:**[tests/conftest.py 233-259](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L233-L259)[tests/conftest.py 356-407](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L356-L407)

Dismiss
Refresh this wiki

Enter email to refresh