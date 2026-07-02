---
project: riscv_arch_tests
github: https://github.com/tenstorrent/riscv_arch_tests
deepwiki: https://deepwiki.com/tenstorrent/riscv_arch_tests/3-test-infrastructure).
---

# Test Infrastructure

Relevant source files
*   [README.md](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1)

This page covers the infrastructure components that support test execution and management in the RISC-V architectural test framework. The infrastructure includes Python-based test runners, containerization systems, CI/CD pipelines, and various utilities for managing and executing tests across different RISC-V instruction set simulators.

For information about the test architecture and organization, see [Test Architecture](https://deepwiki.com/tenstorrent/riscv_arch_tests/2-test-architecture). For details about simulator-specific configurations, see [Simulator Integration](https://deepwiki.com/tenstorrent/riscv_arch_tests/4-simulator-integration).

## Infrastructure Overview

The test infrastructure consists of several interconnected components that work together to provide a comprehensive testing environment for RISC-V architectural tests.

**Infrastructure Architecture Overview**

Sources: [README.md 50-51](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L50-L51)[README.md 92-93](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L92-L93)[README.md 105-113](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L105-L113)

## Core Test Execution System

The primary test execution infrastructure is built around two main Python scripts that handle different aspects of test execution.

### Test Runner Components

**Test Execution Flow Architecture**

The `quals.py` script serves as the primary test execution engine, providing comprehensive test management capabilities including compilation, execution, and result analysis. The `smoke.py` script provides a higher-level interface for running comprehensive test suites.

Sources: [README.md 105-113](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L105-L113)

### Command Line Interface

The infrastructure supports flexible command-line execution with multiple configuration options:

| Parameter | Description | Default |
| --- | --- | --- |
| `--pre-compiled` | Use pre-compiled binaries from releases | Optional flag |
| `--quals_file` | Specify test list file | Mandatory |
| `--iss` | Choose ISS (whisper/spike) | Can be specified in quals file |
| `--vlen` | Vector length for RVV tests (128/256) | 256 |

The test runner creates compiled tests in the `riscv_tests/log/build` directory and outputs execution logs to `riscv_tests/log`.

Sources: [README.md 105-113](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L105-L113)

## Test List Generation

The `gen_testlist.py` utility automates the creation of test manifest files from the directory structure.

**Test List Generation Flow**

The generation process scans the hierarchical test directory structure and creates corresponding `.list` files that specify which tests should be executed for each ISA extension and configuration.

Sources: [README.md 133-186](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L133-L186)

## Container Infrastructure

The repository includes a comprehensive containerization system built around Podman to ensure consistent test environments across different platforms.

### Container Build System

**Container System Architecture**

The container system provides isolated, reproducible environments for building and running RISC-V tests. It includes both whisper and spike simulators pre-built and configured for immediate use.

Sources: [README.md 94-103](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L94-L103)

### Container Configuration

The `Containerfile` defines a complete environment that includes:

*   RISC-V GNU toolchain (release 2023.12.12)
*   Pre-built whisper ISS with custom configurations
*   Pre-built spike ISS with extended ISA support (`RV64IMAFDCV_ZBA_ZBB_ZBC_ZBS`)
*   Python infrastructure and dependencies
*   Required system libraries and utilities

The container build process integrates the git submodules for both simulators and compiles them with the specific configurations required for the test suite.

Sources: [README.md 8](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L8-L8)[README.md 97-103](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L97-L103)

## CI/CD Integration

The GitLab CI pipeline provides automated testing workflows integrated with the container infrastructure.

### Pipeline Configuration

**CI/CD Pipeline Architecture**

The pipeline automatically builds container images, runs comprehensive smoke tests, and deploys validated images to the container registry. This ensures that all test infrastructure changes are validated before deployment.

Sources: [README.md 111-112](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L111-L112)

## Archive and Utility Management

The `unpack.py` utility provides archive management capabilities for handling pre-compiled test binaries and managing test distribution.

**Archive Management System**

The archive system enables efficient distribution and management of pre-compiled test binaries, supporting both local compilation workflows and distribution of release packages.

Sources: [README.md 16](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L16-L16)[README.md 107](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L107-L107)

## Configuration Management

The infrastructure includes sophisticated configuration management for different test scenarios and simulator configurations.

### ISS Configuration Files

| Configuration File | Purpose | Vector Length |
| --- | --- | --- |
| `whisper_config.json` | Base whisper configuration | Default |
| `whisper_config_vlen_128.json` | Vector length 128 configuration | 128 |
| `whisper_config_vlen_256.json` | Vector length 256 configuration | 256 |

These configuration files provide different vector processing configurations for RVV (RISC-V Vector) extension testing, allowing comprehensive validation across different vector implementation parameters.

The configuration system supports flexible ISS selection, where the command-line `--iss` parameter takes precedence over settings specified in test list files, providing both default configurations and runtime flexibility.

Sources: [README.md 109-111](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L109-L111)

Dismiss
Refresh this wiki

Enter email to refresh