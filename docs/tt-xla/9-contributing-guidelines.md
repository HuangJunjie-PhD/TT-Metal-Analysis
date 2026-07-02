---
project: tt-xla
github: https://github.com/tenstorrent/tt-xla
deepwiki: https://deepwiki.com/tenstorrent/tt-xla/9-contributing-guidelines
---

# Contributing Guidelines

Relevant source files
*   [.github/workflows/test-matrix-presets/model-test-experimental.json](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.github/workflows/test-matrix-presets/model-test-experimental.json)
*   [.pre-commit-config.yaml](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.pre-commit-config.yaml)
*   [tests/runner/test_config/__init__.py](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/runner/test_config/__init__.py)

## Purpose and Scope

This document defines the contribution standards, testing requirements, and submission process for TT-XLA. It covers code style enforcement, pre-commit workflows, testing expectations, and pull request procedures.

For development environment setup and building from source, see [Developer Guide](https://deepwiki.com/tenstorrent/tt-xla/8-developer-guide). For running and validating tests locally, see [Running and Debugging Tests](https://deepwiki.com/tenstorrent/tt-xla/8.3-running-and-debugging-tests). For understanding the CI/CD pipeline that validates contributions, see [CI/CD Pipeline](https://deepwiki.com/tenstorrent/tt-xla/7-cicd-pipeline).

* * *

## Code Style Standards

TT-XLA enforces consistent code style across Python and C++ codebases through automated tooling. All contributions must pass pre-commit checks before submission.

### Python Style Requirements

Python code follows the **black** code formatter with **isort** for import organization:

*   **Formatter**: `black` (version 25.9.0)
*   **Import sorting**: `isort` with black profile
*   **Line length**: Default black settings (88 characters)

Example import organization:

`# Standard library importsimport osimport sys # Third-party importsimport jaximport torch # Local importsfrom pjrt_plugin_tt import initialize_device`
### C++ Style Requirements

C++ code follows **clang-format** style:

*   **Version**: v21.1.1
*   **Configuration**: Style defined in `.clang-format` at repository root
*   **Applies to**: `.c`, `.cpp`, `.h`, `.hpp` files

### License Headers

All source files must include SPDX license headers:

`# SPDX-FileCopyrightText: (c) 2025 Tenstorrent AI ULC## SPDX-License-Identifier: Apache-2.0`
The copyright checker validates header presence across all source files.

### Pre-Commit Hook Configuration

[.pre-commit-config.yaml 1-30](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.pre-commit-config.yaml#L1-L30)

**Configured Hooks:**

| Hook | Purpose | Configuration |
| --- | --- | --- |
| `black` | Python code formatting | `language_version: python3` |
| `clang-format` | C/C++ code formatting | `-style=file, -i` |
| `check-copyright` | SPDX header validation | Config: `.github/check-spdx.yaml` |
| `trailing-whitespace` | Remove trailing spaces | Default |
| `end-of-file-fixer` | Ensure newline at EOF | Default |
| `check-added-large-files` | Block large file commits | Default |
| `check-yaml` | YAML syntax validation | Default |
| `isort` | Python import sorting | `--profile black` |

**Sources:**[.pre-commit-config.yaml 1-30](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.pre-commit-config.yaml#L1-L30)

* * *

## Testing Requirements

All contributions must include appropriate tests and pass existing test suites before merge.

### Running Tests Locally

Before submitting a pull request, execute relevant test suites:

**PyTorch models:**

`pytest tests/runner/test_models.py::test_all_models_torch -m "n150 and inference and single_device"`
**JAX models:**

`pytest tests/runner/test_models.py::test_all_models_jax -m "n150 and inference and single_device"`
**LLM models:**

`pytest tests/runner/test_models.py::test_llms_torch -m "n150 and inference and single_device"`
**vLLM sampling tests:**

`pytest tests/runner/test_vllm_sample.py`
For detailed test execution guidance, see [Running and Debugging Tests](https://deepwiki.com/tenstorrent/tt-xla/8.3-running-and-debugging-tests).

### Test Configuration Requirements

When adding new models or modifying existing ones, update test configurations:

**Test Status Categories:**

*   `EXPECTED_PASSING`: Model must pass in CI
*   `KNOWN_FAILURE_XFAIL`: Known issue, tracked with TODO/ticket
*   `NOT_SUPPORTED_SKIP`: Model not yet supported, documented reason

Test configurations are defined in YAML files under `tests/runner/test_config/`. For configuration format and status management, see [Test Configuration System](https://deepwiki.com/tenstorrent/tt-xla/6.1-test-configuration-system).

### CI Test Matrix

Pull requests trigger parallel test execution across hardware platforms:

[.github/workflows/test-matrix-presets/model-test-experimental.json 1-8](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.github/workflows/test-matrix-presets/model-test-experimental.json#L1-L8)

The CI system runs tests on multiple device types (`n150`, `p150`, `n300`, `llmbox`) with parallel execution groups. Ensure your changes pass on all relevant platforms.

**Sources:**[.github/workflows/test-matrix-presets/model-test-experimental.json 1-8](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.github/workflows/test-matrix-presets/model-test-experimental.json#L1-L8)

* * *

## Pull Request Process

### Change Detection and CI Triggers

The CI system intelligently detects changes to avoid unnecessary builds:

**Triggers:**

*   Pull requests to `main`
*   Direct pushes to `main`
*   Nightly scheduled runs (00:00 UTC)
*   Manual workflow dispatch

**Change Detection Logic:** The `inspect-changes` workflow analyzes modified files to determine if builds/tests should run. For details, see [Workflow Architecture](https://deepwiki.com/tenstorrent/tt-xla/7.1-workflow-architecture).

### PR Submission Checklist

Before submitting a pull request:

1.   **Code Style**

    *   - [x]  Pre-commit hooks installed: `pre-commit install`
    *   - [x]  All hooks passing: `pre-commit run --all-files`
    *   - [x]  SPDX headers present on new files

2.   **Testing**

    *   - [x]  Tests added for new functionality
    *   - [x]  Existing tests pass locally
    *   - [x]  Test configurations updated (if adding/modifying models)
    *   - [x]  IR validation tests added (if modifying compiler)

3.   **Documentation**

    *   - [x]  Code comments added for complex logic
    *   - [x]  Docstrings updated for public APIs
    *   - [x]  Wiki pages updated if architecture changes

4.   **Build Validation**

    *   - [x]  Clean build succeeds: `./build.sh`
    *   - [x]  Wheel installs correctly: `pip install build/dist/*.whl`

### PR Review Process

Pull requests are reviewed for:

1.   **Correctness**: Tests pass, logic is sound
2.   **Code Quality**: Follows style guidelines, well-documented
3.   **Performance**: No regressions, profiling data if relevant
4.   **Test Coverage**: Adequate test coverage for changes
5.   **CI Status**: All CI checks green

Reviewers may request changes or additional tests based on the contribution scope.

**Sources:**[.pre-commit-config.yaml 1-30](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.pre-commit-config.yaml#L1-L30)

* * *

## Contribution Types and Guidelines

### Framework Integration (PyTorch, JAX, vLLM)

When contributing framework integration code:

**PyTorch Backend:**

*   Modifications to `integrations/torch_plugin_tt/`
*   Graph transformation passes require FileCheck tests
*   Update decomposition registry for new ops
*   See [PyTorch/XLA Backend](https://deepwiki.com/tenstorrent/tt-xla/5.1-pytorchxla-backend)

**JAX Backend:**

*   Modifications to `python_package/jax_plugin_tt/`
*   JAX primitives must map to StableHLO ops
*   See [JAX Backend](https://deepwiki.com/tenstorrent/tt-xla/5.2-jax-backend)

**vLLM Integration:**

*   Modifications to `integrations/vllm_tt/`
*   Sampling tests required for new parameters
*   Pooling tests for embedding model changes
*   See [vLLM Integration](https://deepwiki.com/tenstorrent/tt-xla/5.3-vllm-integration)

### PJRT Plugin Changes

Core PJRT plugin modifications:

*   **Location**: `src/common/` and `src/tt/`
*   **Build impact**: May require CMake changes
*   **Testing**: Low-level PJRT API tests needed
*   **Documentation**: Update PJRT API documentation

See [PJRT Plugin System](https://deepwiki.com/tenstorrent/tt-xla/4.2-pjrt-plugin-system) and [Build System](https://deepwiki.com/tenstorrent/tt-xla/3-build-system).

### Test Configuration Updates

When updating test configurations:

**Model Test Configs (`tests/runner/test_config/`):**

1.   Update YAML with model metadata
2.   Set appropriate status (`EXPECTED_PASSING`, `KNOWN_FAILURE_XFAIL`, `NOT_SUPPORTED_SKIP`)
3.   Document failure reasons if using `KNOWN_FAILURE_XFAIL`
4.   Test locally before updating CI matrix

**CI Test Matrix (`test-matrix-presets/*.json`):**

1.   Update parallel-groups if adding many tests
2.   Adjust test marks for hardware targeting
3.   Validate JSON syntax

See [Test Configuration System](https://deepwiki.com/tenstorrent/tt-xla/6.1-test-configuration-system) and [Test Matrix Generation and Execution](https://deepwiki.com/tenstorrent/tt-xla/7.3-test-matrix-generation-and-execution).

### Documentation Contributions

Documentation changes:

*   **Wiki pages**: Markdown format, technical focus
*   **Code comments**: Explain non-obvious logic
*   **API docstrings**: Python docstring format
*   **Build instructions**: Keep synchronized with actual process

* * *

## Development Workflow Best Practices

### Local Development Setup

1.   **Install pre-commit hooks:**

`pip install pre-commitpre-commit install`
2.   **Build in development mode:**

`./build.sh  # Or follow instructions in Developer Guide`
3.   **Activate environment:**

`source install/venv/bin/activate`

For complete setup instructions, see [Development Environment Setup](https://deepwiki.com/tenstorrent/tt-xla/8.1-development-environment-setup).

### Debugging and Validation

**Enable detailed logging:**

`export TTXLA_LOGGER_LEVEL=2  # 0=ERROR, 1=WARN, 2=INFO, 3=DEBUG`
**Generate IR for inspection:**

`pytest tests/runner/test_models.py::test_model_name --serialize# Generates .ttir.mlir and .ttnn.mlir files`
**Run FileCheck validation:**

`pytest tests/filecheck/test_filecheck.py -k test_name`
See [Debugging Compilation and Performance](https://deepwiki.com/tenstorrent/tt-xla/8.4-debugging-compilation-and-performance) for advanced debugging techniques.

### Common Development Patterns

**Sources:**[.pre-commit-config.yaml 1-30](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.pre-commit-config.yaml#L1-L30)[.github/workflows/test-matrix-presets/model-test-experimental.json 1-8](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.github/workflows/test-matrix-presets/model-test-experimental.json#L1-L8)

* * *

## Special Considerations

### Performance-Sensitive Changes

If your contribution affects performance:

1.   **Run benchmarks locally:**

`pytest tests/runner/test_models.py -k perf --benchmark`
2.   **Document expected performance impact in PR description**

3.   **CI will run performance tests** on nightly builds

See [Performance Benchmarking](https://deepwiki.com/tenstorrent/tt-xla/7.4-performance-benchmarking) for benchmark execution details.

### Breaking Changes

For changes that break backward compatibility:

1.   **Document migration path** in PR description
2.   **Update version compatibility notes**
3.   **Coordinate with maintainers** before submission
4.   **Add deprecation warnings** if phasing out APIs

### Security and Licensing

*   **Do not commit secrets or credentials**
*   **Use SPDX license headers on all new files**
*   **Third-party code requires approval** and licensing review

* * *

## Getting Help

**Issues and Questions:**

*   Open GitHub issues for bugs or feature requests
*   Tag issues with appropriate labels (`bug`, `enhancement`, `documentation`)

**Development Support:**

*   Review [Developer Guide](https://deepwiki.com/tenstorrent/tt-xla/8-developer-guide) for environment and debugging help
*   Check existing issues for similar problems
*   Consult architecture documentation for system understanding

**Code Review Process:**

*   PRs typically reviewed within 1-2 business days
*   Address reviewer feedback promptly
*   Request re-review after addressing comments

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-xla/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
