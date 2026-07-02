---
project: polaris
github: https://github.com/tenstorrent/polaris
deepwiki: https://deepwiki.com/tenstorrent/polaris/5-development-and-testing)).
---

# Development and Testing

Relevant source files
*   [.github/workflows/checkin_tests.yml](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/.github/workflows/checkin_tests.yml)
*   [.pre-commit-config.yaml](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/.pre-commit-config.yaml)
*   [checkin_tests.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/checkin_tests.py)
*   [envdev.yaml](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/envdev.yaml)
*   [tools/run_onnx_shape_inference.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/run_onnx_shape_inference.py)
*   [tools/spdxchecker.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/spdxchecker.py)

This document covers the development infrastructure and testing framework for the Polaris simulation system. It details the development environment setup, automated testing procedures, code quality enforcement, and continuous integration workflows that ensure code reliability and compliance.

For information about the core simulation system, see [Core Simulation System](https://deepwiki.com/tenstorrent/polaris/2-core-simulation-system). For details on performance analysis tools, see [Performance Analysis and Benchmarking](https://deepwiki.com/tenstorrent/polaris/3-performance-analysis-and-benchmarking).

## Development Environment Setup

The Polaris development environment is managed through conda using a standardized environment specification. The development setup includes all necessary dependencies for simulation, testing, and code quality assurance.

### Conda Environment Configuration

The development environment is defined in `envdev.yaml` and includes:

| Component | Purpose | Key Dependencies |
| --- | --- | --- |
| Core Python | Base runtime environment | `python=3.13.2` |
| Simulation Libraries | Scientific computing and ML | `numpy=2.2.3`, `onnx=1.17.0`, `networkx=3.4.2` |
| Configuration Management | YAML processing and validation | `pyyaml=6.0.2`, `pydantic=2.10.6`, `hydra-core=1.3.2` |
| Testing Framework | Unit testing and coverage | `pytest==8.3.4`, `coverage=7.6.12`, `pytest-mock` |
| Static Analysis | Type checking and linting | `mypy=1.15.0`, `types-PYYAML==6.0.12.20241230` |
| Documentation | Sphinx documentation generation | `sphinx=8.2.1`, `sphinx-rtd-theme=3.0.1` |
| Development Tools | Pre-commit hooks and utilities | `pre-commit`, `loguru`, `deepdiff` |

**Development Environment Architecture**

Sources: [envdev.yaml 1-34](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/envdev.yaml#L1-L34)

## Test Automation Framework

The test automation system is orchestrated by the `checkin_tests.py` script, which provides a comprehensive testing framework supporting multiple test categories and execution modes.

### Test Categories and Handlers

The testing framework defines several test categories, each with dedicated command handlers:

**Test Automation Framework Architecture**

Sources: [checkin_tests.py 1-193](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/checkin_tests.py#L1-L193)

### Test Handler Implementation

Each test category has a dedicated handler function that generates the appropriate command sequences:

| Handler Function | Purpose | Key Commands |
| --- | --- | --- |
| `prepare_commands_coverage()` | Unit test coverage analysis | `coverage run -m pytest`, `coverage report`, `coverage html` |
| `prepare_commands_static()` | Static type checking | `mypy ./` |
| `prepare_commands_workload_tests()` | Integration testing | `polaris.py` with various configurations |

The workload testing system performs comprehensive validation using multiple parameter combinations:

**Workload Test Parameter Generation**

Sources: [checkin_tests.py 39-87](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/checkin_tests.py#L39-L87)

## Code Quality and Compliance

The codebase enforces strict code quality standards through automated tools and compliance checking, particularly focusing on SPDX license header validation and static analysis.

### SPDX License Compliance System

The SPDX compliance system is implemented in `tools/spdxchecker.py` and provides comprehensive license and copyright header validation:

**SPDX Compliance Architecture**

The system validates headers against predefined patterns:

| Pattern | Purpose | Regex |
| --- | --- | --- |
| `SPDX_LICENSE` | License identifier validation | `SPDX-License-Identifier:\s+(?P<license_text>.*)` |
| `SPDX_COPYRIGHT` | Copyright holder validation | `SPDX-FileCopyrightText:\s+(?P<copyright_text>.*)` |
| `COPYRIGHT_REGEX` | Copyright format validation | `(?P<cprt_string>©|[(][cC][)])\s+(?P<cprt_years>\d{4}(-\d{4})?)\s+(?P<cprt_holder>.*)` |

Sources: [tools/spdxchecker.py 1-361](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/spdxchecker.py#L1-L361)

### Pre-commit Hook Configuration

The pre-commit system enforces quality gates before code commits:

**Pre-commit Hook Architecture**

Sources: [.pre-commit-config.yaml 1-31](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/.pre-commit-config.yaml#L1-L31)

## CI/CD Pipeline

The continuous integration system is implemented through GitHub Actions, providing automated testing and validation for all code changes.

### GitHub Actions Workflow

The CI/CD pipeline is defined in `.github/workflows/checkin_tests.yml` and orchestrates the complete testing workflow:

**CI/CD Pipeline Architecture**

The workflow includes comprehensive testing phases:

| Phase | Commands | Output |
| --- | --- | --- |
| Unit Testing | `coverage run -m pytest -m "not tools_secondary"` | JUnit XML, Coverage XML |
| Static Analysis | `python checkin_tests.py static` | MyPy reports |
| Workload Testing | `python checkin_tests.py workloads` | Simulation validation |
| Artifact Collection | `upload-artifact@v4` | Test results, coverage reports |

Sources: [.github/workflows/checkin_tests.yml 1-69](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/.github/workflows/checkin_tests.yml#L1-L69)

## Utilities and Tools

The development infrastructure includes specialized utilities for model processing and analysis.

### ONNX Shape Inference Tool

The `tools/run_onnx_shape_inference.py` utility provides ONNX model shape inference capabilities:

**ONNX Shape Inference Tool Architecture**

Sources: [tools/run_onnx_shape_inference.py 1-28](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tools/run_onnx_shape_inference.py#L1-L28)

### Command Line Interface

The testing system provides a comprehensive command-line interface for development workflows:

| Option | Purpose | Usage |
| --- | --- | --- |
| `--environment` | Specify conda environment | `checkin_tests.py --environment polarisdev` |
| `--stop` | Stop on first failure | `checkin_tests.py --stop` |
| `--filter` | Filter specific commands | `checkin_tests.py --filter "coverage"` |
| `--dryrun` | Preview commands without execution | `checkin_tests.py --dryrun` |

The system supports multiple test categories that can be run individually or in combination:

*   `coverage` - Unit test coverage analysis
*   `static` - Static type checking with mypy
*   `workloads` - Integration testing with polaris.py
*   `all` - Complete test suite execution

Sources: [checkin_tests.py 98-192](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/checkin_tests.py#L98-L192)

Dismiss
Refresh this wiki

Enter email to refresh