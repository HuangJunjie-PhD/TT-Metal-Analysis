---
project: tt-tools-common
github: https://github.com/tenstorrent/tt-tools-common
deepwiki: https://deepwiki.com/tenstorrent/tt-tools-common/6-development-guide
---

# Development Guide

Relevant source files
*   [.github/workflows/test.yml](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/test.yml)
*   [pyproject.toml](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml)
*   [tt_tools_common/tests/conftest.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/tests/conftest.py)

## Purpose and Scope

This guide covers the development workflow, testing practices, code quality standards, and continuous integration/deployment processes for contributing to tt-tools-common. It provides an overview of the development toolchain and common tasks developers encounter when modifying or extending the library.

For detailed information on specific topics:

*   Test suite structure and execution: see [Testing Guide](https://deepwiki.com/tenstorrent/tt-tools-common/6.1-testing-guide)
*   Code formatting and linting setup: see [Code Style and Quality Tools](https://deepwiki.com/tenstorrent/tt-tools-common/6.2-code-style-and-quality-tools)
*   GitHub Actions workflow details: see [CI/CD Pipeline Overview](https://deepwiki.com/tenstorrent/tt-tools-common/6.3-cicd-pipeline-overview)
*   Local environment configuration: see [Development Environment Setup](https://deepwiki.com/tenstorrent/tt-tools-common/6.4-development-environment-setup)

For build and release processes: see [Build and Distribution](https://deepwiki.com/tenstorrent/tt-tools-common/5-build-and-distribution)

* * *

## Development Toolchain Overview

The tt-tools-common development environment uses modern Python tooling with strict quality controls. The toolchain separates runtime dependencies from development-time tools to minimize production package size while providing comprehensive development support.

### Development Dependencies

The project defines development dependencies in the `[project.optional-dependencies]` section of `pyproject.toml`:

| Dependency | Version | Purpose |
| --- | --- | --- |
| `black` | >=23.11.0 | Code formatting |
| `pre-commit` | >=3.5.0 | Git hook automation |
| `pyluwen` | ~=0.8.0 | Hardware abstraction for testing |
| `pytest` | >=7.0.0 | Test framework |
| `pytest-asyncio` | >=0.21.0 | Async test support |

**Sources:**[pyproject.toml 36-43](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L36-L43)

### Python Version Requirements

The project supports Python 3.10, 3.11, and 3.12, with 3.10 as the minimum required version [pyproject.toml 3](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L3-L3) All CI workflows test against this matrix to ensure compatibility.

**Diagram:** Python version compatibility matrix and CI enforcement

**Sources:**[pyproject.toml 3](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L3-L3)[pyproject.toml 19-21](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L19-L21)[.github/workflows/test.yml 20](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/test.yml#L20-L20)

* * *

## Development Workflow

The standard development workflow follows a feature-branch pattern with automated testing and quality checks at each stage.

**Diagram:** Standard development workflow from setup to merge

**Sources:**[pyproject.toml 36-43](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L36-L43)[.github/workflows/test.yml 4-11](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/test.yml#L4-L11)

### Key Workflow Steps

1.   **Environment Setup**: Install the package in editable mode with dev dependencies using `pip install -e ".[dev]"`[.github/workflows/test.yml 34](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/test.yml#L34-L34)
2.   **Pre-commit Hooks**: Automated formatting checks run before each commit (configured via `pre-commit` package)
3.   **Local Testing**: Run tests without hardware requirements using `pytest -m "not requires_hardware"`[.github/workflows/test.yml 38](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/test.yml#L38-L38)
4.   **CI Validation**: All pull requests trigger automated testing across Python 3.10, 3.11, and 3.12
5.   **Hardware Testing**: Optional but recommended for reset-related changes, runs on specialized runners

* * *

## Testing Architecture

The test suite uses pytest with custom markers to differentiate between tests that require physical Tenstorrent hardware and those that can run in any environment.

### Test Organization

**Diagram:** Test suite organization and execution paths

**Sources:**[pyproject.toml 50-53](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L50-L53)[tt_tools_common/tests/conftest.py 8-11](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/tests/conftest.py#L8-L11)[.github/workflows/test.yml 36-66](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/test.yml#L36-L66)

### pytest Markers

The project defines a custom marker in `pyproject.toml`:

`[tool.pytest.ini_options]markers = [  "requires_hardware: marks tests as requiring Tenstorrent hardware"]`
This marker enables selective test execution:

*   **Without hardware**: `pytest -m "not requires_hardware"` runs in standard CI
*   **With hardware**: `pytest -m requires_hardware` runs on specialized runners with Tenstorrent chips

**Sources:**[pyproject.toml 50-53](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L50-L53)

### Test Fixtures

The `conftest.py` file defines shared fixtures available to all tests:

| Fixture | Type | Purpose |
| --- | --- | --- |
| `devices` | function | Returns list of detected Tenstorrent chips via `pyluwen.detect_chips()` |

**Sources:**[tt_tools_common/tests/conftest.py 8-11](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/tests/conftest.py#L8-L11)

* * *

## Continuous Integration Matrix

The CI pipeline runs two types of test jobs with different runner configurations.

### Test Matrix Configuration

**Diagram:** CI test matrix showing parallel execution strategies

**Sources:**[.github/workflows/test.yml 14-66](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/test.yml#L14-L66)

### Job Definitions

**Software Tests**[.github/workflows/test.yml 14-38](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/test.yml#L14-L38):

*   Runs on `ubuntu-latest`
*   Tests Python 3.10, 3.11, 3.12 in parallel
*   Uses `fail-fast: false` to complete all version tests even if one fails
*   Excludes hardware-dependent tests

**Hardware Tests**[.github/workflows/test.yml 40-66](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/test.yml#L40-L66):

*   Runs on specialized self-hosted runners with Tenstorrent hardware
*   Fixed to Python 3.10
*   Two runner types for different chip architectures (N150, P150B)
*   Includes only tests marked with `requires_hardware`

* * *

## Common Development Commands

### Installation and Setup

| Command | Purpose |
| --- | --- |
| `pip install -e .` | Install package in editable mode (runtime only) |
| `pip install -e ".[dev]"` | Install with development dependencies |
| `pre-commit install` | Set up git commit hooks |

### Testing

| Command | Purpose |
| --- | --- |
| `pytest tt_tools_common/tests/` | Run all tests |
| `pytest -v` | Run with verbose output |
| `pytest -m "not requires_hardware"` | Run software tests only |
| `pytest -m requires_hardware` | Run hardware tests only |
| `pytest -k test_name` | Run tests matching name pattern |

### Code Quality

| Command | Purpose |
| --- | --- |
| `black .` | Format all Python files |
| `black --check .` | Check formatting without modifying |
| `pre-commit run --all-files` | Run all pre-commit hooks |

**Sources:**[pyproject.toml 36-43](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L36-L43)[.github/workflows/test.yml 32-38](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/test.yml#L32-L38)

* * *

## Package Installation Modes

The project supports multiple installation modes depending on the development context.

### Installation Mode Comparison

| Mode | Command | Use Case | Dependencies Installed |
| --- | --- | --- | --- |
| **Production** | `pip install tt-tools-common` | End users, consuming tools | Runtime only |
| **Editable** | `pip install -e .` | Development without testing | Runtime only |
| **Development** | `pip install -e ".[dev]"` | Full development workflow | Runtime + dev tools |

**Diagram:** Package installation modes and dependency relationships

**Sources:**[pyproject.toml 23-43](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L23-L43)

### Editable Installation Benefits

Editable mode (`-e` flag) creates a symlink to the source directory, allowing:

*   Code changes to take effect immediately without reinstallation
*   Direct file editing in the repository
*   Simplified debugging with IDE integration

This is the recommended mode for all development work.

* * *

## Development Environment Variables

While not strictly required, certain environment variables can enhance the development experience:

| Variable | Purpose | Example |
| --- | --- | --- |
| `PYTHONPATH` | Add uninstalled modules to path | `export PYTHONPATH=/path/to/tt-tools-common` |
| `PYTEST_ADDOPTS` | Default pytest options | `export PYTEST_ADDOPTS="-v --tb=short"` |

* * *

## Quality Assurance Workflow

The development workflow includes multiple quality gates before code reaches production.

**Diagram:** Multi-stage quality assurance workflow

**Sources:**[.github/workflows/test.yml 1-67](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/test.yml#L1-L67)

* * *

## Next Steps

This overview covers the essential concepts for contributing to tt-tools-common. For detailed instructions on specific development tasks:

*   **Setting up your environment**: [Development Environment Setup](https://deepwiki.com/tenstorrent/tt-tools-common/6.4-development-environment-setup)
*   **Writing and running tests**: [Testing Guide](https://deepwiki.com/tenstorrent/tt-tools-common/6.1-testing-guide)
*   **Configuring code quality tools**: [Code Style and Quality Tools](https://deepwiki.com/tenstorrent/tt-tools-common/6.2-code-style-and-quality-tools)
*   **Understanding CI workflows**: [CI/CD Pipeline Overview](https://deepwiki.com/tenstorrent/tt-tools-common/6.3-cicd-pipeline-overview)

For release management and packaging: [Build and Distribution](https://deepwiki.com/tenstorrent/tt-tools-common/5-build-and-distribution)

Dismiss
Refresh this wiki

Enter email to refresh