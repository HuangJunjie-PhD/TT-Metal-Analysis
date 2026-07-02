---
project: tt-burnin
github: https://github.com/tenstorrent/tt-burnin
deepwiki: https://deepwiki.com/tenstorrent/tt-burnin/3-project-configuration
---

# Project Configuration

Relevant source files
*   [pyproject.toml](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml)
*   [tt_burnin/__init__.py](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/__init__.py)

This document covers the central project configuration system for tt-burnin, focusing on the project structure, metadata, and configuration management defined in the Python packaging standards. This includes the `pyproject.toml` configuration file and the dynamic version resolution system that supports both development and distribution environments.

For detailed information about dependencies and installation procedures, see [Dependencies and Setup](https://deepwiki.com/tenstorrent/tt-burnin/3.1-dependencies-and-setup). For package structure and versioning implementation details, see [Package Structure and Versioning](https://deepwiki.com/tenstorrent/tt-burnin/3.2-package-structure-and-versioning). For build and distribution processes, see [Build and Release](https://deepwiki.com/tenstorrent/tt-burnin/5-build-and-release).

## Project Metadata Configuration

The project configuration is centralized in `pyproject.toml`, which serves as the single source of truth for package metadata, dependencies, and build system configuration. This file follows the PEP 518 and PEP 621 standards for Python project configuration.

### Project Structure Configuration

**Project Metadata Structure**

The core project metadata is defined in the `[project]` section:

| Field | Value | Purpose |
| --- | --- | --- |
| `name` | "tt-burnin" | Package name for PyPI distribution |
| `version` | "0.2.4" | Semantic version number |
| `description` | "Run high power workload on all connected chips" | Package summary |
| `requires-python` | ">=3.7" | Minimum Python version requirement |
| `license` | `{file = "LICENSE"}` | Apache 2.0 license reference |

Sources: [pyproject.toml 1-25](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L1-L25)

### Dependencies and Entry Points

The project configuration defines both runtime dependencies and development tools through structured dependency management:

**Dependency Version Strategy**

The project uses a mixed versioning strategy for dependencies:

*   **Minimum versions** for core libraries (`tt_tools_common>=1.4.19`, `pyluwen>=0.7.11`)
*   **Pinned versions** for serialization libraries (`jsons==1.6.3`, `tomli==2.0.1`)
*   **Conditional dependencies** based on Python version for compatibility

Sources: [pyproject.toml 26-50](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L26-L50)

## Build System Configuration

The build system configuration follows the PEP 518 standard for declaring build requirements and backend:

**Package Data Inclusion**

The configuration ensures that TTX workload files are included in the distribution through the `[tool.setuptools.package-data]` section. The pattern `"ttx/*.ttx"` captures all workload files for different chip architectures.

Sources: [pyproject.toml 51-69](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L51-L69)

## Dynamic Version Resolution System

The project implements a sophisticated version resolution system that works in both development and production environments through the `__init__.py` module:

### Version Resolution Flow

**Version Resolution Strategy**

The system uses a fallback mechanism:

1.   **Cache check**: Return previously resolved version if available
2.   **Distribution metadata**: Use `importlib_metadata` for installed packages
3.   **Development fallback**: Parse `pyproject.toml` directly with development suffix
4.   **Graceful failure**: Return "unknown" if all methods fail

**Python Version Compatibility**

The version resolution handles Python version differences:

*   **Python ≥ 3.8**: Uses `importlib.metadata` from standard library
*   **Python < 3.8**: Falls back to `importlib_metadata` package
*   **Python ≥ 3.11**: Uses `tomllib` from standard library for TOML parsing
*   **Python < 3.11**: Uses `tomli` package for TOML parsing

Sources: [tt_burnin/__init__.py 6-60](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/__init__.py#L6-L60)

### Attribute Access Interface

The module implements a dynamic attribute access system through the `__getattr__` function:

This allows the package version to be accessed as `tt_burnin.__version__` while maintaining lazy evaluation and proper error handling for other attributes.

Sources: [tt_burnin/__init__.py 54-59](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/__init__.py#L54-L59)

## Project URLs and Classification

The project configuration includes metadata for package discovery and classification:

| URL Type | Value | Purpose |
| --- | --- | --- |
| Homepage | [http://tenstorrent.com](http://tenstorrent.com/) | Corporate website |
| Bug Reports | [https://github.com/tenstorrent/tt-burnin/issues](https://github.com/tenstorrent/tt-burnin/issues) | Issue tracking |
| Source | [https://github.com/tenstorrent/tt-burnin](https://github.com/tenstorrent/tt-burnin) | Source repository |

**PyPI Classifiers**

The project includes comprehensive PyPI classifiers for package discovery:

*   **Development Status**: Beta (4)
*   **Environment**: Console/Curses interface
*   **License**: Apache Software License
*   **Python Versions**: 3.7 through 3.11 support
*   **Programming Language**: Python 3 only

Sources: [pyproject.toml 14-46](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L14-L46)

Dismiss
Refresh this wiki

Enter email to refresh