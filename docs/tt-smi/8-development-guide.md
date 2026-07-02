---
project: tt-smi
github: https://github.com/tenstorrent/tt-smi
deepwiki: https://deepwiki.com/tenstorrent/tt-smi/8-development-guide
---

# Development and Maintenance

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1)
*   [pyproject.toml](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml)
*   [tests/test_reset.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tests/test_reset.py)

This section provides essential information for developers who are working with or contributing to the Tenstorrent System Management Interface (TT-SMI). It covers development environment setup, project structure, dependency management, build processes, and contribution guidelines.

For information about the history of changes and version evolution, see [Changelog and Version History](https://deepwiki.com/tenstorrent/tt-smi/6.1-application-layer-architecture). For details about licensing and legal considerations, see [License and Legal Information](https://deepwiki.com/tenstorrent/tt-smi/6.2-backend-system-(ttsmibackend)).

## Development Environment Setup

Setting up a development environment for TT-SMI requires Python 3.7 or later and involves installing several dependencies.

### Development Setup Process

Sources: [pyproject.toml 6-43](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L6-L43)

### Python Environment

TT-SMI supports Python versions 3.7 to 3.11. It's recommended to use a virtual environment for development:

`python -m venv venvsource venv/bin activate  # On Windows: venv\Scripts\activate`
### Installing Dependencies

The project has both runtime and development dependencies:

`# Install the package in development mode with all dependenciespip install -e . # Install development dependenciespip install -e ".[dev]"`
### Code Quality Tools

TT-SMI uses pre-commit hooks for consistent code quality:

`pre-commit install`
This sets up automatic checks when committing code, including code formatting with Black.

## Project Structure and Architecture

TT-SMI follows a modular architecture separating the user interfaces from the backend functionality and hardware interaction.

### Code Organization

Sources: [pyproject.toml 26-37](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L26-L37)

## Dependency Management

TT-SMI has several key dependencies that are essential for its functionality:

### Core Dependencies

| Dependency | Version | Purpose |
| --- | --- | --- |
| pyluwen | v0.6.4 | Hardware access library for Tenstorrent devices |
| tt_tools_common | v1.4.14 | Common utilities for Tenstorrent tools |
| textual | 0.59.0 | Text User Interface framework |
| rich | 13.7.0 | Terminal formatting and display |
| pydantic | ≥1.2 | Data validation and settings management |
| elasticsearch | 8.11.0 | Optional integration for log storage |
| distro | 1.8.0 | OS distribution information |
| importlib_resources | 6.1.1 | Resource management |
| pre-commit | 3.5.0 | Git hook scripts for code quality |

### Dependency Relationships

Sources: [pyproject.toml 26-37](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L26-L37)

### Development Dependencies

The project uses Black for code formatting with version-specific configurations based on the Python version:

| Python Version | Black Version |
| --- | --- |
| 3.9 and newer | 24.10.0 |
| 3.8 | 24.8.0 |
| 3.7 | 23.3.0 |

Sources: [pyproject.toml 39-43](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L39-L43)

## Build and Packaging

TT-SMI uses setuptools for building and packaging. The build configuration is defined in `pyproject.toml`.

### Build Configuration

The project is configured as follows:

*   **Project name**: tt-smi
*   **Current version**: 3.0.15
*   **Build system**: setuptools
*   **Entry point**: `tt-smi = "tt_smi:main"`
*   **Package data**: Includes CSS files, YAML files, and version information

### Build and Package Process

Sources: [pyproject.toml 1-79](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L1-L79)

### Building the Package

To build the package:

`pip install buildpython -m build`
This creates both wheel and source distribution packages in the `dist/` directory.

## Contributing Guidelines

### Contribution Workflow

Sources: [pyproject.toml 39-43](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L39-L43)[pyproject.toml 47-48](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L47-L48)

### Code Style and Quality

TT-SMI maintains code quality through automated tools:

1.   **Code Formatting**: Uses Black with version-specific configurations
2.   **Pre-commit Hooks**: Runs checks before code is committed
3.   **Python Support**: Maintains compatibility with Python 3.7 to 3.11

### Pull Request Process

1.   Ensure code passes all pre-commit checks
2.   Update documentation as needed
3.   Ensure dependencies are properly documented in `pyproject.toml`
4.   Submit a pull request with a clear description of changes

## Version Management

TT-SMI follows semantic versioning. The current version is 3.0.15 as defined in `pyproject.toml`.

For a detailed history of changes and version evolution, see [Changelog and Version History](https://deepwiki.com/tenstorrent/tt-smi/6.1-application-layer-architecture).

Sources: [pyproject.toml 3](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L3-L3)

## Issue Reporting

Bug reports and feature requests should be submitted to the project's GitHub issue tracker:

*   Bug Reports: [https://github.com/tenstorrent/tt-smi/issues](https://github.com/tenstorrent/tt-smi/issues)

When reporting issues, include:

1.   TT-SMI version
2.   Python version
3.   Operating system details
4.   Steps to reproduce the issue
5.   Expected vs. actual behavior

Sources: [pyproject.toml 47](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L47-L47)

## Maintainers

The primary maintainer of TT-SMI is:

*   Sam Bansal ([sbansal@tenstorrent.com](mailto:sbansal@tenstorrent.com))

Sources: [pyproject.toml 8-13](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L8-L13)

## Legal Considerations

TT-SMI is licensed under the Apache License 2.0. All contributions to the project must comply with this license.

For detailed information about licensing terms and legal considerations, see [License and Legal Information](https://deepwiki.com/tenstorrent/tt-smi/6.2-backend-system-(ttsmibackend)).

Sources: [LICENSE 1-178](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/LICENSE#L1-L178)[pyproject.toml 7](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L7-L7)

Dismiss
Refresh this wiki

Enter email to refresh