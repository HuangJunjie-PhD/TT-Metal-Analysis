---
project: tt-burnin
github: https://github.com/tenstorrent/tt-burnin
deepwiki: https://deepwiki.com/tenstorrent/tt-burnin/6-development-environment
---

# Development Environment

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitignore)
*   [Makefile](https://github.com/tenstorrent/tt-burnin/blob/809f293d/Makefile)
*   [debian/rules](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/rules)
*   [setup.py](https://github.com/tenstorrent/tt-burnin/blob/809f293d/setup.py)

This document provides guidance for developers working on the tt-burnin project, covering the development environment setup, build system components, and local development workflows. It explains how to set up a development environment using the provided build tools and manage dependencies for both development and production scenarios.

For information about automated CI/CD workflows and release processes, see [CI/CD Workflows](https://deepwiki.com/tenstorrent/tt-burnin/5.2-cicd-workflows). For details about Debian packaging configuration, see [Debian Packaging](https://deepwiki.com/tenstorrent/tt-burnin/5.1-debian-packaging).

## Development Environment Setup

The tt-burnin project provides multiple approaches for setting up a development environment, primarily through a `Makefile` and compatibility `setup.py` script. The build system is designed to work with Python 3.10+ and uses virtual environments to isolate dependencies.

### Build System Components

Sources: [Makefile 1-24](https://github.com/tenstorrent/tt-burnin/blob/809f293d/Makefile#L1-L24)[setup.py 1-17](https://github.com/tenstorrent/tt-burnin/blob/809f293d/setup.py#L1-L17)[debian/rules 1-12](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/rules#L1-L12)

### Environment Management Workflow

The development environment uses virtual environments to manage dependencies and isolate the development setup from system Python packages.

Sources: [Makefile 8-18](https://github.com/tenstorrent/tt-burnin/blob/809f293d/Makefile#L8-L18)

## Build System Implementation

### Makefile Configuration

The `Makefile` provides three primary targets for environment management:

| Target | Purpose | Virtual Environment | Installation Mode |
| --- | --- | --- | --- |
| `build` | Development setup | `.env/` | Editable install with dev dependencies |
| `release` | Production testing | `my-env/` | Standard install with requirements.txt |
| `clean` | Environment cleanup | Removes `.env/` | N/A |

The `PYTHON` variable defaults to `python3` but can be overridden: [Makefile 5](https://github.com/tenstorrent/tt-burnin/blob/809f293d/Makefile#L5-L5)

`PYTHON=python3.11 make build`
Sources: [Makefile 1-23](https://github.com/tenstorrent/tt-burnin/blob/809f293d/Makefile#L1-L23)

### Setup.py Compatibility Layer

The `setup.py` file provides compatibility with Ubuntu 22.04 packaging tools that may not fully support `pyproject.toml` configuration. It uses `setuptools_scm` for dynamic versioning from git tags:

*   **Dynamic versioning**: `use_scm_version=True` extracts version from git tags [setup.py 12](https://github.com/tenstorrent/tt-burnin/blob/809f293d/setup.py#L12-L12)
*   **Package discovery**: `find_packages()` automatically discovers Python packages [setup.py 13](https://github.com/tenstorrent/tt-burnin/blob/809f293d/setup.py#L13-L13)
*   **Python requirement**: Enforces Python 3.10+ compatibility [setup.py 14](https://github.com/tenstorrent/tt-burnin/blob/809f293d/setup.py#L14-L14)
*   **Build dependency**: Requires `setuptools_scm` for version management [setup.py 15](https://github.com/tenstorrent/tt-burnin/blob/809f293d/setup.py#L15-L15)

Sources: [setup.py 6-16](https://github.com/tenstorrent/tt-burnin/blob/809f293d/setup.py#L6-L16)

### Debian Build Integration

The Debian packaging system integrates with the Python build tools through `debian/rules`:

*   **Build system**: Uses `pybuild` for Python package building [debian/rules 5](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/rules#L5-L5)
*   **Python support**: Configured for `python3` packages [debian/rules 6](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/rules#L6-L6)
*   **Test handling**: Overrides `dh_auto_test` to skip tests during package build [debian/rules 9-10](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/rules#L9-L10)

Sources: [debian/rules 3-11](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/rules#L3-L11)

## Development Workflow

### Initial Setup

1.   Clone the repository
2.   Run `make build` to create development environment
3.   Activate the virtual environment: `. ./.env/bin/activate`
4.   Begin development with editable package installation

### Environment Isolation

The `.gitignore` file ensures development artifacts don't enter version control:

*   Virtual environments: `.env/`, `my-env/`[.gitignore 3-4](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitignore#L3-L4)
*   Build artifacts: `.eggs/`, `*.egg-info`, `build/`[.gitignore 1-8](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitignore#L1-L8)
*   Python cache: `__pycache__/`[.gitignore 6](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitignore#L6-L6)
*   IDE files: `.vscode/`[.gitignore 7](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitignore#L7-L7)

Sources: [.gitignore 1-9](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitignore#L1-L9)

### Development vs Production Testing

Sources: [Makefile 8-18](https://github.com/tenstorrent/tt-burnin/blob/809f293d/Makefile#L8-L18)

The development mode (`make build`) installs the package in editable mode with development dependencies, while production test mode (`make release`) performs a standard installation using `requirements.txt` to simulate production deployment conditions.

Dismiss
Refresh this wiki

Enter email to refresh