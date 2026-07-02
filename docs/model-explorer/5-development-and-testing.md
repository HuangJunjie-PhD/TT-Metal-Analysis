---
project: model-explorer
github: https://github.com/tenstorrent/model-explorer
deepwiki: https://deepwiki.com/tenstorrent/model-explorer/5-development-and-testing
---

# Development and Testing

Relevant source files
*   [.github/workflows/playwright.yml.disabled](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/.github/workflows/playwright.yml.disabled)
*   [ci/bash_helpers.sh](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/bash_helpers.sh)
*   [ci/code_style_check.sh](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/code_style_check.sh)
*   [ci/pigweed.patch](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/pigweed.patch)
*   [ci/pigweed_download.sh](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/pigweed_download.sh)
*   [ci/playwright_test.py](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/playwright_test.py)

This document covers the development workflow, testing infrastructure, and code quality tools used in the Model Explorer repository. It provides an overview of the CI/CD pipeline, automated testing strategies, and development best practices.

For detailed information about specific testing components, see [Testing Infrastructure](https://deepwiki.com/tenstorrent/model-explorer/5.1-testing-infrastructure). For comprehensive coverage of code style enforcement and continuous integration workflows, see [Code Quality and CI/CD](https://deepwiki.com/tenstorrent/model-explorer/5.2-code-quality-and-cicd).

## Development Workflow Overview

The Model Explorer development process follows a structured approach with automated quality gates and comprehensive testing. The workflow integrates multiple language ecosystems (Python, TypeScript, C++) with unified tooling for consistency.

## Code Quality Pipeline

**Code Quality Enforcement Workflow** The quality pipeline uses Pigweed tooling for license verification and Pyink for Python formatting. The system automatically downloads and patches Pigweed to align with Model Explorer's requirements.

_Sources: [ci/code\_style\_check.sh 1-95](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/code\_style\_check.sh#L1-L95)[ci/pigweed\_download.sh 1-43](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/pigweed\_download.sh#L1-L43)[ci/pigweed.patch 1-129](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/pigweed.patch#L1-L129)_

## Testing Strategy Architecture

**End-to-End Testing Architecture** The testing system uses Playwright for browser automation with visual regression testing. Each test function validates specific adapter types against golden screenshots with pixel-level comparison.

_Sources: [ci/playwright\_test.py 1-283](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/playwright\_test.py#L1-L283)[ci/playwright\_test.py 35-64](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/playwright\_test.py#L35-L64)[ci/playwright\_test.py 82-87](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/playwright\_test.py#L82-L87)_

## Test Execution Flow

The testing infrastructure follows a systematic approach to validate both functionality and visual consistency across different model adapters:

| Test Category | Test Functions | Purpose |
| --- | --- | --- |
| Basic UI | `test_homepage()` | Validates main interface loading |
| TFLite Adapters | `test_litert_direct_adapter()`, `test_litert_mlir_adapter()` | Tests flatbuffer and MLIR conversion paths |
| TensorFlow | `test_tf_mlir_adapter()`, `test_tf_direct_adapter()`, `test_tf_graphdef_adapter()` | Validates TF model processing |
| StableHLO | `test_shlo_mlir_adapter()` | Tests MLIR dialect processing |
| PyTorch | `test_pytorch()` | Validates PyTorch model export |
| Server Reuse | `test_reuse_server_*()` | Tests server persistence across model loads |

The visual comparison system uses a threshold-based approach with `matched_images()` function comparing actual screenshots against golden references. Failed comparisons generate debug artifacts in `build/debug/` for analysis.

_Sources: [ci/playwright\_test.py 89-283](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/playwright\_test.py#L89-L283)[ci/playwright\_test.py 38-64](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/playwright\_test.py#L38-L64)_

## Development Environment Setup

The development process requires coordination between multiple build systems and package managers:

**Development Environment Dependencies** The setup process coordinates Pigweed for code quality, Playwright for testing, and standard Python/Node.js toolchains for the main application stack.

_Sources: [ci/pigweed\_download.sh 17-43](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/pigweed\_download.sh#L17-L43)[.github/workflows/playwright.yml.disabled 36-57](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/.github/workflows/playwright.yml.disabled#L36-L57)_

## Code Style Enforcement

The repository uses a two-tier approach for code quality:

1.   **License Verification**: The `pigweed_presubmit.py` script with `copyright_notice` function validates Apache 2.0 license headers across all source files
2.   **Python Formatting**: The `pyink` formatter enforces consistent style with specific parameters: `--pyink-use-majority-quotes --pyink-indentation=2 --line-length 80`

The license checker excludes specific directories and file types defined in [ci/code_style_check.sh 39-62](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/code_style_check.sh#L39-L62) including build artifacts, downloaded dependencies, and binary files.

_Sources: [ci/code\_style\_check.sh 23-95](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/code\_style\_check.sh#L23-L95)[ci/pigweed.patch 45-82](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/ci/pigweed.patch#L45-L82)_

## Continuous Integration Status

The GitHub Actions workflow for Playwright testing is currently disabled (`.yml.disabled` extension) but provides the blueprint for automated testing. The workflow would:

1.   Set up Python 3.11 and Node.js 22.12.0 environments
2.   Build the UI from source using Angular CLI
3.   Start the Model Explorer server on localhost:8080
4.   Execute Playwright tests with artifact collection

The disabled status suggests the project may be transitioning CI/CD providers or restructuring the automation pipeline.

_Sources: [.github/workflows/playwright.yml.disabled 1-67](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/.github/workflows/playwright.yml.disabled#L1-L67)_

Dismiss
Refresh this wiki

Enter email to refresh