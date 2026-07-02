---
project: polaris
github: https://github.com/tenstorrent/polaris
deepwiki: https://deepwiki.com/tenstorrent/polaris/3-performance-analysis-and-benchmarking))
---

# Performance Analysis and Benchmarking

Relevant source files
*   [tests/test_tools/test_parse_metal_tensix_results.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_tools/test_parse_metal_tensix_results.py)
*   [tools/parse_metal_tensix_results.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/parse_metal_tensix_results.py)
*   [tools/run_metal_tensix_correlation.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/run_metal_tensix_correlation.py)

The Performance Analysis and Benchmarking system provides tools for extracting performance metrics from external sources, running correlation analysis against simulation results, and generating performance reports. This system enables validation of simulation accuracy by comparing Polaris simulation outputs with reference benchmarks from hardware implementations.

For information about the core simulation execution, see [Core Simulation System](https://deepwiki.com/tenstorrent/polaris/2-core-simulation-system). For details about visualization and reporting outputs, see [Visualization and Reports](https://deepwiki.com/tenstorrent/polaris/3.4-visualization-and-reports).

## System Overview

The performance analysis system consists of three main components: metrics extraction from HTML reports, correlation analysis between reference and simulation data, and report generation. The system focuses primarily on neural network workloads like BERT and ResNet, extracting performance data from external sources and validating simulation accuracy.

**Sources:**[tools/parse_metal_tensix_results.py 1-314](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/parse_metal_tensix_results.py#L1-L314)[tools/run_metal_tensix_correlation.py 1-211](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/run_metal_tensix_correlation.py#L1-L211)

## Data Flow Architecture

The system processes performance data through a structured pipeline that transforms raw HTML tables into structured metrics, executes simulations, and generates correlation reports.

**Sources:**[tools/parse_metal_tensix_results.py 96-214](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/parse_metal_tensix_results.py#L96-L214)[tools/run_metal_tensix_correlation.py 24-64](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/run_metal_tensix_correlation.py#L24-L64)

## Performance Metrics Data Model

The system uses a structured data model to represent performance metrics consistently across different workloads and systems.

| Field | Type | Description | Example |
| --- | --- | --- | --- |
| `benchmark` | `str` | Benchmark identifier | `"Benchmark.BERT"` |
| `wlname` | `str` | Workload name | `"bert"` |
| `gpu` | `str` | GPU type | `"Tensix"` |
| `gpu_batch_size` | `int` | Batch size | `32` |
| `metric` | `str` | Performance metric type | `"Samples/s"` |
| `perf` | `float` | Performance value | `1250.0` |
| `target_perf` | `float` | Target performance | `1300.0` |
| `system` | `str` | Hardware system | `"n150"` |
| `precision` | `str` | Data precision | `"bf8"` |

The `TensixNwPerfMetricModel` class [tools/parse_metal_tensix_results.py 23-45](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/parse_metal_tensix_results.py#L23-L45) provides Pydantic validation for this data structure, ensuring consistency and type safety across the analysis pipeline.

**Sources:**[tools/parse_metal_tensix_results.py 23-45](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/parse_metal_tensix_results.py#L23-L45)

## Column Mapping and Data Processing

The system handles various input formats through a comprehensive column mapping system that normalizes different naming conventions into a consistent internal format.

The mapping system includes specific transformations for different workload types:

*   BERT workloads: Set `benchmark` to `"Benchmark.BERT"` and `metric` to `"Samples/s"`
*   ResNet workloads: Set `benchmark` to `"Benchmark.ResNet50"` and `metric` to `"Samples/s"`

**Sources:**[tools/parse_metal_tensix_results.py 64-153](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/parse_metal_tensix_results.py#L64-L153)

## Correlation Analysis Workflow

The correlation analysis compares reference performance data against simulation results to validate accuracy and identify performance gaps.

**Sources:**[tools/run_metal_tensix_correlation.py 24-64](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/run_metal_tensix_correlation.py#L24-L64)[tools/run_metal_tensix_correlation.py 65-206](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/run_metal_tensix_correlation.py#L65-L206)

## Workload Configuration Generation

The system dynamically generates workload specifications for simulation based on extracted performance metrics.

For BERT workloads, the system creates configurations with:

*   Module: `BasicLLM@BasicLLM.py`
*   Parameters: `nL=24`, `nH=16`, `dE=1024`, `nW=384`, `vocab_sz=30522`

For ResNet workloads, the system creates configurations with:

*   Module: `ResNet@basicresnet.py`
*   Parameters: `layers=[3,4,6,3]`, `num_classes=1000`, `num_channels=3`

**Sources:**[tools/run_metal_tensix_correlation.py 132-151](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/run_metal_tensix_correlation.py#L132-L151)

## System Integration Points

The performance analysis system integrates with several other components of the Polaris framework:

*   **Configuration System**: Uses `parse_yaml()` and `print_yaml()` from `ttsim.utils.common` for YAML processing
*   **Web Utilities**: Leverages `read_from_url()` from `ttsim.utils.readfromurl` for fetching HTML content
*   **Project Orchestration**: Executes `polproj.py` subprocess calls for simulation runs
*   **Logging**: Uses `loguru` for structured logging throughout the analysis pipeline

The system maintains data consistency through type hints and Pydantic models, ensuring reliable data flow between components.

**Sources:**[tools/parse_metal_tensix_results.py 15-18](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/parse_metal_tensix_results.py#L15-L18)[tools/run_metal_tensix_correlation.py 15-16](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/run_metal_tensix_correlation.py#L15-L16)

Dismiss
Refresh this wiki

Enter email to refresh