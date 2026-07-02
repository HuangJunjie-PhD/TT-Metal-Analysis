---
project: tt-flash
github: https://github.com/tenstorrent/tt-flash
deepwiki: https://deepwiki.com/tenstorrent/tt-flash/8-development-guide
---

# Development Guide

Relevant source files
*   [Makefile](https://github.com/tenstorrent/tt-flash/blob/60c06032/Makefile)
*   [pyproject.toml](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml)
*   [setup.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/setup.py)

This guide is for developers who want to contribute to or modify tt-flash itself. It covers setting up a development environment, building from source, understanding the project structure, and following code quality standards.

For information about using tt-flash as an end user, see [Getting Started](https://deepwiki.com/tenstorrent/tt-flash/2-getting-started). For information about the release and packaging process, see [Release and Distribution](https://deepwiki.com/tenstorrent/tt-flash/9-release-and-distribution).

* * *

## Purpose and Scope

This document provides comprehensive instructions for:

*   Setting up a local development environment
*   Building tt-flash from source
*   Understanding the codebase structure and module organization
*   Using code quality tools (formatting and type checking)
*   Development workflow best practices

* * *

## Development Environment Setup

### Prerequisites

tt-flash requires the following development environment:

| Requirement | Specification |
| --- | --- |
| **Python Version** | >= 3.10 (specified in [pyproject.toml 6](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L6-L6)) |
| **Operating System** | Ubuntu 22.04+ or compatible Linux distribution |
| **Build Tools** | setuptools >= 43.0.0, wheel |
| **Optional Tools** | black (code formatter), basedpyright (type checker) |

The project uses Python's native virtual environment (`venv`) for dependency isolation during development.

**Sources:**[pyproject.toml 1-81](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L1-L81)[Makefile 1-24](https://github.com/tenstorrent/tt-flash/blob/60c06032/Makefile#L1-L24)

* * *

## Building from Source

### Development Build

The primary method for building tt-flash in development mode uses the `Makefile`:

This command performs the following operations:

**Detailed steps executed by `make build`:**

1.   **Virtual environment creation**: `python3 -m venv .env` creates an isolated Python environment in `.env/`
2.   **Pip upgrade**: Updates pip to the latest version within the venv
3.   **Editable installation**: `pip install -ve .[dev]` installs tt-flash in editable mode with development dependencies

The `-e` flag (editable mode) allows you to modify source files without reinstalling, as the package links directly to your working directory.

**Sources:**[Makefile 8-12](https://github.com/tenstorrent/tt-flash/blob/60c06032/Makefile#L8-L12)

### Release Build

For testing a production-like installation:

This creates a separate virtual environment (`my-env`) and installs tt-flash as a regular package without editable mode. This simulates how end users will install the package.

**Sources:**[Makefile 14-19](https://github.com/tenstorrent/tt-flash/blob/60c06032/Makefile#L14-L19)

### Cleanup

Remove build artifacts and virtual environments:

This removes the `.env` directory. Note that it does not remove `my-env` created by `make release`.

**Sources:**[Makefile 21-23](https://github.com/tenstorrent/tt-flash/blob/60c06032/Makefile#L21-L23)

* * *

## Project Structure

### Directory Layout

**Sources:**[pyproject.toml 52-65](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L52-L65)

### Package Organization

The `tt_flash` package is organized into functional modules:

| Module | Purpose | Key Classes/Functions |
| --- | --- | --- |
| `main.py` | CLI interface and argument parsing | `main()`, `flash` subcommand, `verify` subcommand |
| `flash.py` | Core flashing logic | `flash_chips()`, version comparison, write/verify cycle |
| `chip.py` | Hardware abstraction layer | `TTChip`, `WhChip`, `BhChip`, `detect_local_chips()` |
| `utility.py` | Helper utilities | `get_board_type()`, `change_to_public_name()`, version parsing |

### Package Data Inclusion

The package includes non-Python resources:

This configuration ensures that:

*   Version information is embedded in the package
*   Firmware definition files (`fw_defines.yaml`) are included in distributions

**Sources:**[pyproject.toml 55-59](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L55-L59)

* * *

## Configuration Files

### pyproject.toml

The authoritative project configuration following PEP 517/518 standards:

**Key sections:**

1.   **Project metadata** ([pyproject.toml 1-22](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L1-L22)):

    *   Package name, version, description
    *   Python version constraint (>= 3.10)
    *   Apache 2.0 license
    *   Author and maintainer information

2.   **Dependencies** ([pyproject.toml 23-35](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L23-L35)):

    *   **Exact pinning** for core libraries: `pyyaml == 6.0.2`, `tabulate == 0.9.0`
    *   **Compatible release** for Tenstorrent libraries: `pyluwen ~= 0.8.0`, `tt_tools_common ~= 1.5`
    *   Version-conditional imports for Python < 3.11 (`tomli`)

3.   **Development dependencies** ([pyproject.toml 37-42](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L37-L42)):

    *   `black` with version-specific constraints for Python 3.7-3.11

4.   **Entry points** ([pyproject.toml 49-50](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L49-L50)):

    *   `tt-flash` command maps to `tt_flash.main:main`

5.   **Type checking configuration** ([pyproject.toml 67-69](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L67-L69)):

    *   `basedpyright` configured for Python 3.7 compatibility
    *   Standard type checking mode

**Sources:**[pyproject.toml 1-81](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L1-L81)

### setup.py

Compatibility layer for older packaging tools on Ubuntu 22.04:

This file exists solely to support Ubuntu 22.04's packaging tools which may not fully support PEP 517. It reads configuration from `pyproject.toml` using `tomli` to maintain a single source of truth.

**Sources:**[setup.py 1-26](https://github.com/tenstorrent/tt-flash/blob/60c06032/setup.py#L1-L26)

* * *

## Code Quality Tools

### Black Code Formatter

tt-flash uses `black` for consistent code formatting. The configuration supports multiple Python versions:

| Python Version | Black Version | Configuration Location |
| --- | --- | --- |
| 3.9+ | 24.10.0 | [pyproject.toml 39](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L39-L39) |
| 3.8 | 24.8.0 | [pyproject.toml 40](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L40-L40) |
| 3.7 | 23.3.0 | [pyproject.toml 41](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L41-L41) |

**Running black:**

Black is installed automatically as a development dependency when running `make build`.

**Sources:**[pyproject.toml 38-42](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L38-L42)

### Basedpyright Type Checker

Static type checking is configured using `basedpyright`:

**Configuration details:**

*   **Python version**: 3.7 for maximum backward compatibility
*   **Type checking mode**: "standard" provides a balance between strictness and usability

**Running type checks:**

Note: `basedpyright` is not listed in the development dependencies, indicating it may be used in CI/CD workflows but not required for local development.

**Sources:**[pyproject.toml 67-69](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L67-L69)

* * *

## Development Workflow

### Recommended Workflow

**Step-by-step process:**

1.   **Initial setup**:

 
2.   **Activate development environment**:

 
3.   **Make code changes**: Edit files in `tt_flash/` directory

4.   **Test immediately**: Changes are live due to editable install

 
5.   **Format and check**:

 
6.   **Production testing**:

 

**Sources:**[Makefile 1-24](https://github.com/tenstorrent/tt-flash/blob/60c06032/Makefile#L1-L24)[pyproject.toml 1-81](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L1-L81)

* * *

## Dependency Management

### Version Pinning Strategy

tt-flash uses different version constraints based on dependency stability:

**Rationale:**

*   **Exact pinning** (`==`) ensures reproducible builds for stable libraries
*   **Compatible release** (`~=`) allows patch updates for actively developed Tenstorrent libraries
*   Example: `pyluwen ~= 0.8.0` allows 0.8.1, 0.8.2, etc., but not 0.9.0

**Sources:**[pyproject.toml 23-35](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L23-L35)

### Conditional Dependencies

Python version-specific dependencies are handled automatically:

These dependencies are only installed when the Python version meets the condition:

*   `tomli`: TOML parser backport (Python 3.11+ has native `tomllib`)
*   `importlib-metadata`: Package metadata access (Python 3.8+ has native version)
*   `importlib-resources`: Resource file access (Python 3.9+ has enhanced version)

**Sources:**[pyproject.toml 28-34](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L28-L34)

* * *

## Build System Architecture

The build system follows modern Python packaging standards:

**Build backend configuration:**

This configuration:

*   Uses `setuptools.build_meta` as the PEP 517 build backend
*   Requires setuptools >= 43.0.0 for full PEP 517 support
*   Includes `wheel` for binary distribution generation

**Sources:**[pyproject.toml 74-81](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L74-L81)

* * *

## Common Development Tasks

### Testing Local Changes

### Installing Additional Dependencies

### Updating Version

Version updates should be coordinated with the release process. The version is defined in [pyproject.toml 3](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L3-L3) and must be manually updated. For the full release workflow, see [Release and Distribution](https://deepwiki.com/tenstorrent/tt-flash/9-release-and-distribution).

**Sources:**[pyproject.toml 1-81](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L1-L81)[Makefile 1-24](https://github.com/tenstorrent/tt-flash/blob/60c06032/Makefile#L1-L24)

* * *

## Integration with CI/CD

The development setup integrates with GitHub Actions workflows:

| Workflow | Purpose | Trigger |
| --- | --- | --- |
| `build-all.yml` | Orchestrates all build types | Push/PR |
| `build-debian.yml` | Debian package builds | Called by build-all |
| `build-pypi.yml` | Python wheel builds | Called by build-all |
| `release.yml` | Version bump and publish | Manual trigger |

Development dependencies and configurations (black, basedpyright) are used in CI to enforce code quality. For detailed CI/CD documentation, see [CI/CD Automation](https://deepwiki.com/tenstorrent/tt-flash/10-cicd-automation).

**Sources:**[pyproject.toml 38-42](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L38-L42)

* * *

## Environment Variables

The Makefile supports customization through environment variables:

| Variable | Default | Purpose |
| --- | --- | --- |
| `PYTHON` | `python3` | Python interpreter to use |
| `LUWEN_DIR` | `${HOME}/work/luwen` | Path to local luwen development directory |

**Usage:**

Note: `LUWEN_DIR` is defined but not currently used in the Makefile targets. It may be reserved for future development workflows involving local luwen modifications.

**Sources:**[Makefile 5-6](https://github.com/tenstorrent/tt-flash/blob/60c06032/Makefile#L5-L6)

Dismiss
Refresh this wiki

Enter email to refresh