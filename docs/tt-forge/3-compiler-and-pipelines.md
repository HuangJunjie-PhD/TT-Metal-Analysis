---
project: tt-forge
github: https://github.com/tenstorrent/tt-forge
deepwiki: https://deepwiki.com/tenstorrent/tt-forge/3-compiler-and-pipelines
---

# Benchmarking System

Relevant source files
*   [.github/workflows/basic-tests.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/basic-tests.yml)
*   [.github/workflows/demo-tests.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/demo-tests.yml)
*   [.github/workflows/filter-test-matrix.py](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/filter-test-matrix.py)
*   [.github/workflows/models-matrix.json](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/models-matrix.json)
*   [demos/tt-forge-onnx/README.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/demos/tt-forge-onnx/README.md?plain=1)

The benchmarking system provides comprehensive performance validation for the TT-Forge compiler stack across multiple hardware architectures (Wormhole n150, Blackhole p150) and model types. It validates two primary compilation paths: **torch-xla** benchmarks (page 3.3) test the JAX/PyTorch → XLA → TT-MLIR → TTNN path, while **forge.compile** benchmarks (page 3.4) test the TVM → TT-MLIR → TTNN path.

The system consists of:

*   **Benchmark Infrastructure and Workflows** ([Benchmark Infrastructure and Workflows](https://deepwiki.com/tenstorrent/tt-forge/3.1-benchmark-infrastructure-and-workflows)): `perf-benchmark.yml` orchestration, GitHub runners, and Docker containers.
*   **Test Matrix Configuration** ([Test Matrix Configuration](https://deepwiki.com/tenstorrent/tt-forge/3.2-test-matrix-configuration)): `perf-bench-matrix.json` structure and `filter-test-matrix.py` filtering logic.
*   **torch-xla Backend Benchmarks** ([torch-xla Backend Benchmarks](https://deepwiki.com/tenstorrent/tt-forge/3.3-torch-xla-backend-benchmarks)): ResNet, UNet, ViT, and LLMs using the torch-xla device.
*   **forge.compile Backend Benchmarks** ([forge.compile Backend Benchmarks](https://deepwiki.com/tenstorrent/tt-forge/3.4-forge.compile-backend-benchmarks)): MobileNetV2, Segformer, and UNet using the `forge.compile` API.
*   **Performance Metrics and Reporting** ([Performance Metrics and Reporting](https://deepwiki.com/tenstorrent/tt-forge/3.5-performance-metrics-and-reporting)): FPS measurement, PCC validation, and JSON result structures.

## System Architecture

The benchmarking system is built on a three-tier architecture: workflow orchestration, matrix-based configuration, and dynamic execution.

### Overall Architecture

Sources: [.github/workflows/demo-tests.yml 54-104](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/demo-tests.yml#L54-L104)[.github/workflows/basic-tests.yml 58-100](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/basic-tests.yml#L58-L100)[.github/workflows/filter-test-matrix.py 10-24](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/filter-test-matrix.py#L10-L24)

## Workflow Orchestration

The system uses specialized workflows to handle different validation levels, from quick smoke tests to deep performance benchmarks.

### Workflow Types

| Workflow | Purpose | Key File |
| --- | --- | --- |
| **Basic Tests** | Rapid validation of frontend functionality (smoke tests). | [.github/workflows/basic-tests.yml 1-57](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/basic-tests.yml#L1-L57) |
| **Demo Tests** | Validates end-to-end model demos across different projects. | [.github/workflows/demo-tests.yml 1-47](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/demo-tests.yml#L1-L47) |
| **Perf Benchmarks** | Detailed performance measurement (FPS, Latency, PCC). | [.github/workflows/perf-benchmark.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/perf-benchmark.yml) |

For details, see [Benchmark Infrastructure and Workflows](https://deepwiki.com/tenstorrent/tt-forge/3.1-benchmark-infrastructure-and-workflows).

## Matrix Configuration System

The benchmarking system uses JSON-based matrix files to define test configurations. The `filter-test-matrix.py` script parses these matrices and generates filtered test configurations based on project, test name, and hardware requirements.

### Matrix Filtering Logic

The script `filter-test-matrix.py` performs the following operations:

1.   **Flattening**: Expands tests with multiple `runs-on` targets into individual matrix entries [[.github/workflows/filter-test-matrix.py 10-24](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[.github/workflows/filter-test-matrix.py#L10-L24)].
2.   **Filtering**: Matches entries against `project-filter` and `test-filter` inputs [[.github/workflows/filter-test-matrix.py 27-48](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[.github/workflows/filter-test-matrix.py#L27-L48)].
3.   **Runner Mapping**: Maps logical runner names (e.g., `n150`) to specific CI labels (e.g., `n150-perf`) [[.github/workflows/filter-test-matrix.py 51-60](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[.github/workflows/filter-test-matrix.py#L51-L60)].

For details, see [Test Matrix Configuration](https://deepwiki.com/tenstorrent/tt-forge/3.2-test-matrix-configuration).

## Backend Benchmarks

The system validates performance across different frontend entry points.

### torch-xla Backend

This backend targets the JAX and PyTorch ecosystem. Benchmarks include:

*   **NLP**: ALBERT, GPT2, OPT, BGE-M3 [[.github/workflows/models-matrix.json 24-32](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[.github/workflows/models-matrix.json#L24-L32)].
*   **CNN**: ResNet50, ResNet50-HF [[.github/workflows/models-matrix.json 29-30](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[.github/workflows/models-matrix.json#L29-L30)].

For details, see [torch-xla Backend Benchmarks](https://deepwiki.com/tenstorrent/tt-forge/3.3-torch-xla-backend-benchmarks).

### forge.compile Backend

This backend targets models compiled via the TVM-based frontend. It covers a wide range of computer vision models including AlexNet, DenseNet, and EfficientNet [[demos/tt-forge-onnx/README.md 25-32](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[demos/tt-forge-onnx/README.md?plain=1#L25-L32)].

For details, see [forge.compile Backend Benchmarks](https://deepwiki.com/tenstorrent/tt-forge/3.4-forge.compile-backend-benchmarks).

## Performance Metrics and Reporting

The system tracks several critical metrics to ensure hardware efficiency and numerical correctness:

*   **FPS (Frames Per Second)**: Throughput measurement.
*   **PCC (Pearson Correlation Coefficient)**: Measures numerical accuracy against a CPU baseline.
*   **CPU FPS**: Baseline performance for comparison.

### Failure Notification

Failures on the `main` branch trigger Slack notifications to the `C088QN7E0R3` channel [[.github/workflows/demo-tests.yml 173-190](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[.github/workflows/demo-tests.yml#L173-L190)].

For details, see [Performance Metrics and Reporting](https://deepwiki.com/tenstorrent/tt-forge/3.5-performance-metrics-and-reporting).

Sources: [.github/workflows/demo-tests.yml 173-190](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/demo-tests.yml#L173-L190)[.github/workflows/models-matrix.json 1-45](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/models-matrix.json#L1-L45)[.github/workflows/filter-test-matrix.py 1-94](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/filter-test-matrix.py#L1-L94)

Dismiss
Refresh this wiki

Enter email to refresh