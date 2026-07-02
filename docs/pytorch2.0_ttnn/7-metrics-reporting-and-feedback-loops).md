---
project: pytorch2.0_ttnn
github: https://github.com/tenstorrent/pytorch2.0_ttnn
deepwiki: https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/7-metrics-reporting-and-feedback-loops)
---

# Metrics, Reporting, and Feedback Loops

Relevant source files
*   [README.md](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/README.md?plain=1)
*   [docs/AddNewOperationLowering.md](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/AddNewOperationLowering.md?plain=1)
*   [docs/README.md.in](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/README.md.in)
*   [tests/lowering/pool/test_max_pool_2d.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/pool/test_max_pool_2d.py)
*   [tests/models/clip/test_clip.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/clip/test_clip.py)
*   [tests/tools/test_input_vals_utils.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/tools/test_input_vals_utils.py)
*   [tools/collect_metrics.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py)
*   [tools/generate_guard_function_from_input_var_metric.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_guard_function_from_input_var_metric.py)
*   [tools/generate_input_variation_test_from_models.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_input_variation_test_from_models.py)
*   [tools/merge_to_tt_guard_autogen.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/merge_to_tt_guard_autogen.py)
*   [tools/to_tt_guard_autogen.tmpl](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/to_tt_guard_autogen.tmpl)
*   [torch_ttnn/metrics.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/metrics.py)

This document explains the metrics collection, aggregation, reporting, and feedback systems that enable continuous improvement of the pytorch2.0_ttnn compiler. The system collects runtime and schema metrics during test execution, aggregates them to track operation conversion status, generates comprehensive documentation, and creates guard blocklists to prevent known problematic operations from compilation.

For information about the testing infrastructure that generates these metrics, see [Testing Framework and Infrastructure](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4-testing-framework-and-infrastructure). For details on how guards are used during compilation, see [Guard System and Operation Blocklists](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/2.4-guard-system-and-operation-blocklists).

## Overview

The metrics and reporting system forms a closed feedback loop that drives compiler development:

**Feedback Loop Overview**

Sources: [tools/collect_metrics.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py)[torch_ttnn/metrics.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/metrics.py)[tools/generate_input_variation_test_from_models.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_input_variation_test_from_models.py)[tools/generate_guard_function_from_input_var_metric.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_guard_function_from_input_var_metric.py)

## Metrics Collection Architecture

The system collects two types of metrics during test execution:

| Metric Type | File Format | Content | Purpose |
| --- | --- | --- | --- |
| Runtime Metrics | `{original|compiled}-run_time_metrics.pickle` | Execution time, batch size, success status, accuracy | Performance comparison |
| Schema Lists | `{original|compiled}-schema_list.pickle` | Input variations per operation | Conversion tracking |

### Runtime Metrics Structure

Runtime metrics are collected in [tests/conftest.py 108-137](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L108-L137) by the `compile_and_run` fixture and saved via [torch_ttnn/metrics.py 13-18](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/metrics.py#L13-L18):

**Runtime Metrics Collection Flow**

Runtime metrics dictionary contains:

*   `run_time`: Execution time in milliseconds
*   `avg_run_time`: Average over multiple runs
*   `run_time_first_iter`: First run including compilation
*   `batch_size`: Input batch size
*   `success`: Boolean execution status
*   `accuracy`: PCC (Pearson Correlation Coefficient) value

Sources: [tests/conftest.py 108-137](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/conftest.py#L108-L137)[torch_ttnn/metrics.py 13-18](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/metrics.py#L13-L18)

### Schema Tracking with InputVariation

The `InputVariation` class captures the signature and arguments of each operation:

**Schema Tracking Class Hierarchy**

The `InputVariation` class is populated during graph transformation by [torch_ttnn/metrics.py 119-168](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/metrics.py#L119-L168) Each node in the FX graph has its schema extracted:

**Input Variation Collection from FX Node**

When an operation is converted (e.g., `aten.add` → `ttnn.add`), a `ConvertedInput` wrapper preserves the original schema via [torch_ttnn/metrics.py 107-116](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/metrics.py#L107-L116)

Sources: [torch_ttnn/metrics.py 44-105](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/metrics.py#L44-L105)[torch_ttnn/metrics.py 119-168](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/metrics.py#L119-L168)[torch_ttnn/metrics.py 170-191](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/metrics.py#L170-L191)

## Aggregation with InputVarPerOp

The `InputVarPerOp` class is the core aggregation data structure defined in [tools/collect_metrics.py 161-449](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L161-L449):

**InputVarPerOp Data Structure**

The nested structure maps: `opname → input_variation → ConversionStatus`

Sources: [tools/collect_metrics.py 161-449](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L161-L449)

### Conversion Status Classification

The `ConversionStatus` enum [tools/collect_metrics.py 152-158](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L152-L158) classifies each input variation:

| Status | Meaning | Criteria |
| --- | --- | --- |
| `DONE` | Successfully converted to TTNN | Operation converted and different from original |
| `FALLBACK` | Partial conversion | Converted to another aten operation |
| `NONE` | Not converted | Same operation remains after compilation |
| `REMOVED` | Eliminated from graph | Operation not present in compiled graph (e.g., optimized away) |
| `UNKNOWN` | Status undetermined | Operation not processed yet |
| `BUG` | Known issue | Marked as problematic |

The classification logic in [tools/collect_metrics.py 238-283](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L238-L283) compares original and compiled schemas:

**Conversion Status Decision Tree**

Sources: [tools/collect_metrics.py 152-158](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L152-L158)[tools/collect_metrics.py 238-283](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L238-L283)

### Metrics Aggregation Process

The main aggregation script [tools/collect_metrics.py 451-634](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L451-L634) processes all model metrics:

**Metrics Aggregation Pipeline**

Key statistics calculated in [tools/collect_metrics.py 495-563](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L495-L563):

*   **Throughput**: `batch_size / run_time * 1000` (inferences per second)
*   **Operation counts**: Total and unique aten ops before/after
*   **Conversion ratio**: Remaining aten ops / original aten ops
*   **Accuracy**: PCC percentage

Sources: [tools/collect_metrics.py 451-634](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L451-L634)

## Documentation Generation

### README Generation

The README is generated from a template [docs/README.md.in 1-116](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/README.md.in#L1-L116) with substituted metrics via [tools/collect_metrics.py 114-148](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L114-L148):

**README Generation Flow**

The header mappings [tools/collect_metrics.py 18-60](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L18-L60) provide descriptions for each column in the model summary table:

The generated table includes:

*   Model name with link to detailed report
*   Status emoji (✅ fully converted, 🚧 partial, ❌ failed)
*   Performance metrics (throughput, first run time)
*   Accuracy percentage
*   Operation counts before/after conversion

Sources: [tools/collect_metrics.py 18-60](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L18-L60)[tools/collect_metrics.py 114-148](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L114-L148)[docs/README.md.in 1-116](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/docs/README.md.in#L1-L116)

### Operations Report

The operations report provides per-operation conversion status via [tools/collect_metrics.py 417-433](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L417-L433):

**Operations Report Generation**

Each operation page [tools/collect_metrics.py 423-433](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L423-L433) includes:

| Column | Description |
| --- | --- |
| ATen Input Variations | String representation of input schema |
| Status | ConversionStatus (Done/Fallback/None/Removed) |
| Isolated | Single-op test result (Done/Failed/Fallback) |
| PCC | Accuracy from single-op test |
| Host | Number of host fallbacks in single-op test |

The **Isolated** column comes from `metrics-autogen-op/` directory containing results from single-operation tests generated by [tools/generate_input_variation_test_from_models.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_input_variation_test_from_models.py)

Sources: [tools/collect_metrics.py 370-387](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L370-L387)[tools/collect_metrics.py 417-433](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L417-L433)

### Generality Score

The generality score [tools/collect_metrics.py 202-210](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L202-L210) measures operation robustness:

```
Generality Score = (DONE + REMOVED) / Total Input Variations
```

A score of 1.0 means all observed input variations are handled successfully. Lower scores indicate the operation works only for specific input patterns.

Sources: [tools/collect_metrics.py 202-210](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L202-L210)

## Automated Test Generation

The system generates single-operation tests from observed input variations via [tools/generate_input_variation_test_from_models.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_input_variation_test_from_models.py):

**Automated Test Generation Pipeline**

### AtenOpTestExporter

The `AtenOpTestExporter` class [tools/generate_input_variation_test_from_models.py 14-57](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_input_variation_test_from_models.py#L14-L57) extends `InputVarPerOp` to generate test files:

Templates used:

*   `tools/templates/aten_test.tmpl` - Main test structure
*   `tools/templates/aten_test_generic_module.tmpl` - Standard module
*   `tools/templates/aten_test_check_only_first_output_module.tmpl` - Special cases (e.g., batch_norm)

Sources: [tools/generate_input_variation_test_from_models.py 14-57](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_input_variation_test_from_models.py#L14-L57)[tools/generate_input_variation_test_from_models.py 62-108](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_input_variation_test_from_models.py#L62-L108)

### Test Structure

Each generated test [tools/generate_input_variation_test_from_models.py 46-55](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_input_variation_test_from_models.py#L46-L55):

1.   Creates a `torch.nn.Module` wrapping the single operation
2.   Parametrizes over all observed input variations
3.   Compiles with `torch.compile(backend=torch_ttnn.backend)`
4.   Verifies the operation is converted (not present in compiled graph)
5.   Checks accuracy with `assert_with_pcc`
6.   Saves results to `metrics-autogen-op/`

Sources: [tools/generate_input_variation_test_from_models.py 46-55](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_input_variation_test_from_models.py#L46-L55)[tests/lowering/pool/test_max_pool_2d.py 56-71](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/pool/test_max_pool_2d.py#L56-L71)

## Guard Blocklist Generation

Failed or low-accuracy operations trigger guard blocklist generation via [tools/generate_guard_function_from_input_var_metric.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_guard_function_from_input_var_metric.py):

**Guard Blocklist Generation Flow**

### GuardFuncExporter

The `GuardFuncExporter` class [tools/generate_guard_function_from_input_var_metric.py 11-86](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_guard_function_from_input_var_metric.py#L11-L86) processes autogen test results:

The threshold by default checks [tools/generate_guard_function_from_input_var_metric.py 96](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_guard_function_from_input_var_metric.py#L96-L96):

*   `run == False` - Operation failed to execute
*   `accuracy < 0.99` - Low accuracy result

Sources: [tools/generate_guard_function_from_input_var_metric.py 11-86](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_guard_function_from_input_var_metric.py#L11-L86)

### Guard Template

The generated file uses template [tools/to_tt_guard_autogen.tmpl 1-32](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/to_tt_guard_autogen.tmpl#L1-L32):

The guard is checked during compilation in `can_lowering_to_ttnn`[torch_ttnn/passes/lowering/to_tt_pass.py 77-89](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/to_tt_pass.py#L77-L89)

Sources: [tools/to_tt_guard_autogen.tmpl 1-32](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/to_tt_guard_autogen.tmpl#L1-L32)[tools/generate_guard_function_from_input_var_metric.py 59-85](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_guard_function_from_input_var_metric.py#L59-L85)

### Merging Guards

Multiple guard files can be merged via [tools/merge_to_tt_guard_autogen.py 1-88](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/merge_to_tt_guard_autogen.py#L1-L88):

**Guard Merge Process**

The merge combines blocklists by union: if an input variation appears in either guard, it's blocked in the merged output.

Sources: [tools/merge_to_tt_guard_autogen.py 1-88](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/merge_to_tt_guard_autogen.py#L1-L88)

## Pydantic Schema Export

The system exports metrics to JSON using Pydantic models defined in [tools/data_collection/pydantic_models.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/data_collection/pydantic_models.py):

**Pydantic Schema for JSON Export**

Each model generates a `metrics/{model}/model_run.json` file [tools/collect_metrics.py 622-627](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L622-L627) containing:

*   Performance metrics
*   Links to visualized graphs (SVG)
*   Complete operation lists with schemas for original and compiled graphs

This structured format enables programmatic analysis and integration with external tools.

Sources: [tools/collect_metrics.py 442-448](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L442-L448)[tools/collect_metrics.py 583-627](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py#L583-L627)

## Complete Feedback Loop

The complete feedback mechanism:

**Complete Feedback Loop Architecture**

This closed-loop system ensures:

1.   **Visibility**: Documentation shows current operation support status
2.   **Testing**: Autogenerated tests expand coverage systematically
3.   **Safety**: Guards prevent known failures from blocking development
4.   **Progress tracking**: Generality scores quantify improvement over time

Sources: [tools/collect_metrics.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/collect_metrics.py)[tools/generate_input_variation_test_from_models.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_input_variation_test_from_models.py)[tools/generate_guard_function_from_input_var_metric.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tools/generate_guard_function_from_input_var_metric.py)[torch_ttnn/metrics.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/metrics.py)

Dismiss
Refresh this wiki

Enter email to refresh